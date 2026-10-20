# Firewalls de Política com Base em Zona (ZPF)

Resumo sobre ZPF: benefícios, projeto, operação, regras de tráfego, configuração passo a passo, verificação e considerações.

---

## 1. Visão geral do ZPF

### Dois modelos de configuração do Cisco IOS Firewall

- **Firewall clássico:** modelo tradicional, a política é aplicada nas **interfaces**
- **ZPF (Zone-Based Policy Firewall):** as interfaces são atribuídas a **zonas de segurança** e a política é aplicada ao tráfego que **passa entre as zonas**

Os dois modelos podem estar ativos no mesmo roteador, mas **não na mesma interface** (uma interface não pode ser membro de zona e ter inspeção de IP ao mesmo tempo).

Motivações pra migrar: estrutura (ajuda na documentação e comunicação) e facilidade de uso.

### Benefícios

- **Não depende de ACL**
- Postura de segurança do roteador: **bloqueia a menos que seja explicitamente permitido**
- Políticas fáceis de ler e de depurar com o **C3PL** (Cisco Common Classification Policy Language), um método estruturado de criar políticas com base em eventos, condições e ações. Uma política vale pra todo o tráfego informado, sem precisar de várias ACLs e ações de inspeção
- Interfaces físicas e virtuais podem ser agrupadas em zonas
- Políticas aplicadas a tráfego **unidirecional** entre zonas
- Se uma interface nova entrar numa zona, os hosts dela já passam tráfego pra todos os hosts da mesma zona

---

## 2. Projeto de um ZPF

| Etapa | O que fazer |
| --- | --- |
| **1. Determinar as zonas** | Separar a rede em zonas. Uma zona é uma fronteira de segurança onde o tráfego sofre restrições ao cruzar pra outra região (ex: rede pública é uma zona, rede interna é outra) |
| **2. Estabelecer políticas entre zonas** | Pra cada par origem/destino, definir quais sessões (TCP, UDP, ICMP echo) os clientes da origem podem pedir aos servidores do destino. Políticas são **unidirecionais** e definidas por **pares de zona**. Pra tráfego sem conceito de sessão, definir fluxos unidirecionais nos dois sentidos |
| **3. Projetar a infraestrutura física** | Considerar segurança e disponibilidade: quantos dispositivos entre zonas mais e menos seguras e dispositivos redundantes |
| **4. Identificar subconjuntos dentro das zonas e mesclar requisitos de tráfego** | Pra cada firewall, identificar subconjuntos de zonas ligados às suas interfaces e juntar os requisitos (fora do escopo do curso) |

---

## 3. Operação do ZPF

### Ações

| Ação | Equivalente | O que faz |
| --- | --- | --- |
| **inspect** | n/a | Inspeção stateful de pacotes. O roteador guarda informações de sessão (TCP e UDP) e permite o tráfego de retorno |
| **drop** | `deny` da ACL | Descarta o tráfego. Opção `log` registra os pacotes descartados |
| **pass** | `permit` da ACL | Encaminha o tráfego **sem estado**. Só vale em uma direção, então precisa de política correspondente no sentido contrário |

### Regras pra tráfego de trânsito

| Situação | Resultado |
| --- | --- |
| Nenhuma das interfaces é membro de zona | **Passa** |
| As duas interfaces estão na **mesma zona** | **Passa** |
| Só uma das interfaces é membro de zona | **Descarta** (mesmo que exista zone-pair) |
| Zonas diferentes, **sem zone-pair** | **Descarta** |
| Zonas diferentes, com zone-pair, **sem política** | **Passa** |
| Zonas diferentes, com zone-pair e **política** | **Inspeciona, passa ou descarta** conforme a política |

### Regras pra tráfego da zona self

A **self** é o próprio roteador, com todos os IPs das interfaces. Cobre tráfego originado no roteador ou destinado a uma interface dele (gerenciamento como SSH, ou controle como protocolos de roteamento).

- Se o roteador é origem ou destino, **todo o tráfego é permitido**
- Exceção: se existe zone-pair com política pra aquela origem/destino, a política é aplicada a todo o tráfego

---

## 4. Configurar um ZPF

Cinco etapas (a ordem não é obrigatória, mas algumas dependências existem: o `class-map` precisa existir antes do `policy-map`, e o `policy-map` antes do `zone-pair`. Senão o roteador dá erro):

1. Criar as zonas
2. Identificar o tráfego com um **class-map**
3. Definir uma ação com um **policy-map**
4. Identificar um **zone-pair** e associar ao policy-map
5. Atribuir zonas às interfaces

### Etapa 1: criar as zonas

Antes, definir quais interfaces entram em cada zona, o nome de cada uma e qual tráfego é necessário entre elas (e em qual direção).

```
Router(config)# zone security zone-name

R1(config)# zone security PRIVATE
R1(config-sec-zone)# exit
R1(config)# zone security PUBLIC
R1(config-sec-zone)# exit
```

### Etapa 2: identificar o tráfego (class-map)

Classe é um conjunto de pacotes identificado por condições de correspondência (`match`).

```
Router(config)# class-map type inspect [match-any | match-all] class-map-name
```

| Parâmetro | Descrição |
| --- | --- |
| `match-any` | O pacote precisa atender **um** dos critérios |
| `match-all` | O pacote precisa atender **todos** os critérios |
| `class-map-name` | Nome do class-map, usado depois no policy-map |

Opções de `match` no modo do class-map:

```
Router(config-cmap)# match access-group {acl-# | acl-name}
Router(config-cmap)# match protocol protocol-name
Router(config-cmap)# match class-map class-map-name
```

Exemplo: permitir HTTP, incluindo HTTPS e DNS:

```
R1(config)# class-map type inspect match-any HTTP-TRAFFIC
R1(config-cmap)# match protocol http
R1(config-cmap)# match protocol https
R1(config-cmap)# match protocol dns
```

### Etapa 3: definir a ação (policy-map)

```
R1(config)# policy-map type inspect policy-map-name
R1(config-pmap)# class type inspect class-map-name
R1(config-pmap-c)# {inspect | drop | pass}
```

Exemplo:

```
R1(config)# policy-map type inspect PRIV-TO-PUB-POLICY
R1(config-pmap)# class type inspect HTTP-TRAFFIC
R1(config-pmap-c)# inspect
```

Detalhes das ações:

- **inspect:** controle de tráfego stateful. Ex: se o tráfego da zona privada pra pública é inspecionado, o roteador guarda a sessão e permite o retorno
- **drop:** é a **ação padrão** pra todo o tráfego. Equivale ao `deny any` implícito da ACL, e aparece como `class class-default` no fim de todo policy-map. Diferente da ACL, o tráfego é **descartado silenciosamente**, sem mensagem ICMP unreachable pra origem
- **pass:** sem estado, só uma direção. Ideal pra protocolos seguros e previsíveis (ex: IPsec). A maior parte do tráfego de aplicações é melhor tratada com **inspect**

### Etapa 4: zone-pair e política de serviço

```
Router(config)# zone-pair security zone-pair-name source {source-zone-name | self} destination {destination-zone-name | self}
Router(config-sec-zone-pair)# service-policy type inspect policy-map-name
```

| Parâmetro | Descrição |
| --- | --- |
| `source` | Zona de onde o tráfego **sai** |
| `destination` | Zona pra onde o tráfego **vai** |
| `self` | Zona do próprio roteador |

```
R1(config)# zone-pair security PRIV-PUB source PRIVATE destination PUBLIC
R1(config-sec-zone-pair)# service-policy type inspect PRIV-TO-PUB-POLICY
```

### Etapa 5: atribuir zonas às interfaces

```
Router(config-if)# zone-member security zone-name

R1(config)# interface GigabitEthernet 0/0
R1(config-if)# zone-member security PRIVATE
R1(config-if)# interface Serial 0/0/0
R1(config-if)# zone-member security PUBLIC
```

- Associar a zona à interface **aplica a política na hora**
- Se ainda não houver service-policy configurada pra zona, **todo o tráfego de trânsito é descartado**
- Resultado: HTTP, HTTPS e DNS da zona privada pra pública são inspecionados, e o tráfego da pública pra privada só passa se fizer parte de sessões iniciadas por hosts da privada

---

## 5. Verificar o ZPF

### Running-config

Ordem em que aparece: class-map, policy-map (com `class class-default` + `drop`), zonas, zone-pair com a service-policy e, por fim, as interfaces com `zone-member`.

```
class-map type inspect match-any HTTP-TRAFFIC
 match protocol http
 match protocol https
 match protocol dns
!
policy-map type inspect PRIV-TO-PUB-POLICY
 class type inspect HTTP-TRAFFIC
  inspect
 class class-default
  drop
!
zone security PRIVATE
zone security PUBLIC
zone-pair security PRIV-PUB source PRIVATE destination PUBLIC
 service-policy type inspect PRIV-TO-PUB-POLICY
!
interface GigabitEthernet0/0
 zone-member security PRIVATE
!
interface Serial0/0/0
 zone-member security PUBLIC
```

### Comandos de verificação

| Comando | O que mostra |
| --- | --- |
| `show policy-map type inspect zone-pair sessions` | Sessões estabelecidas e contadores de pacotes por classe (inclusive os descartados em `class-default`) |
| `show class-map type inspect` | Os class-maps e seus `match` |
| `show zone security` | Zonas e interfaces membros (inclui a zona `self`) |
| `show zone-pair security` | Zone-pairs com zona de origem, destino e service-policy |
| `show policy-map type inspect` | Os policy-maps com classes e ações |

No exemplo do material, o host 192.168.1.3 abriu uma sessão HTTPS com o servidor 10.1.1.2, e o ping do mesmo host pro servidor caiu em `class-default` (4 pacotes descartados), porque ICMP não estava na classe.

---

## 6. Considerações de configuração

- O roteador **nunca filtra** o tráfego entre interfaces da **mesma zona**
- Uma interface **não pode pertencer a várias zonas**. Pra unir zonas, cria uma nova zona com o policy-map e os zone-pairs apropriados
- ZPF e firewall clássico podem coexistir, mas não na mesma interface. Remover o `ip inspect` da interface antes de usar `zone-member security`
- Tráfego **nunca flui** entre uma interface com zona e uma interface sem zona
- O `zone-member` sempre causa uma **interrupção temporária** até o outro membro da zona ser configurado
- Política padrão entre zonas: **descartar tudo**, a menos que a service-policy do zone-pair permita
- O `zone-member` **não protege o próprio roteador** (tráfego de e para ele não é afetado), a menos que sejam configurados zone-pairs com a zona `self`

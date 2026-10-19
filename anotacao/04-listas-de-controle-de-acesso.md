# Listas de Controle de Acesso (ACLs)

Resumo sobre ACLs: conceito, máscaras curinga, configuração (padrão e estendida), modificação, posicionamento, mitigação de ataques e ACLs IPv6.

---

## 1. Introdução às ACLs

### O que é uma ACL?

- Série de comandos do IOS que filtra pacotes com base nas informações do cabeçalho
- Por padrão, o roteador **não tem nenhuma ACL** configurada
- Quando aplicada a uma interface, o roteador avalia todos os pacotes que passam por ela
- Usa uma lista sequencial de instruções de permissão ou negação, as **ACEs** (entradas de controle de acesso)
- O roteador compara o pacote com cada ACE em ordem. Esse processo é a **filtragem de pacotes**

Usos comuns:

| Tarefa | Exemplo |
| --- | --- |
| Limitar tráfego e melhorar desempenho | Bloquear tráfego de vídeo |
| Controlar fluxo de tráfego | Aceitar atualizações de roteamento só de fontes conhecidas |
| Segurança básica de acesso | Restringir a rede de RH a usuários autorizados |
| Filtrar por tipo de tráfego | Permitir e-mail, negar Telnet |
| Permitir ou negar acesso a serviços | Limitar FTP ou HTTP a certos grupos |
| Priorizar tráfego | Tráfego de voz com QoS |

### Filtragem de pacotes

Pode ocorrer na camada 3 ou 4. Dois tipos de ACL nos roteadores Cisco:

- **Padrão:** filtra só na camada 3, usando **apenas o IPv4 de origem**
- **Estendida:** filtra na camada 3 (origem e/ou destino) e na camada 4 (TCP, UDP, portas e tipo de protocolo)

### ACLs numeradas e nomeadas

| Tipo | Faixa de números |
| --- | --- |
| Padrão | 1 a 99 e 1300 a 1999 |
| Estendida | 100 a 199 e 2000 a 2699 |

**ACLs nomeadas** são o método preferido, porque o nome mostra a finalidade (ex: `FTP-FILTER` é melhor que `100`).

Regras para nomes:

- Alfanuméricos, sem espaços nem pontuação
- Diferenciam maiúsculas de minúsculas
- Sugestão: escrever em LETRAS MAIÚSCULAS (destaca no `running-config` e evita criar duas ACLs com o mesmo nome)
- Dá pra adicionar e excluir entradas

### Operação da ACL

- Pode ser aplicada na **entrada (in)** ou na **saída (out)**
- **ACL de entrada:** filtra antes do roteamento, economiza processamento se o pacote for descartado. Boa quando a rede ligada à interface é a única origem que precisa ser examinada
- **ACL de saída:** filtra depois do roteamento. Boa quando o mesmo filtro vale para pacotes que vêm de várias interfaces de entrada
- ACLs **não filtram pacotes originados pelo próprio roteador**

Passos quando um pacote chega numa interface com ACL de entrada:

1. O roteador extrai o IPv4 de origem do cabeçalho
2. Compara com cada ACE em ordem, de cima pra baixo
3. Na primeira correspondência, executa a ação (permitir ou negar) e para
4. Se não bater em nenhuma ACE, o pacote é descartado pela **negação implícita** (`deny any`) no final de toda ACL

> Uma ACL precisa ter pelo menos uma instrução `permit`, senão todo o tráfego é negado.

---

## 2. Máscaras curinga (wildcard)

Usadas nas ACEs (e no OSPF) pra definir quais bits do endereço devem ser comparados. É o **inverso** da máscara de sub-rede:

- Bit **0:** deve corresponder ao bit do endereço
- Bit **1:** ignora o bit

| Máscara curinga | Significado |
| --- | --- |
| 0.0.0.0 | Corresponde a todos os octetos (um host) |
| 0.0.0.15 | Ignora os últimos 4 bits do último octeto |
| 0.0.0.63 | Ignora os últimos 6 bits do último octeto |
| 0.0.0.255 | Ignora o último octeto (sub-rede /24) |
| 0.0.15.255 | Ignora os últimos 12 bits (intervalo de 16 redes /24) |

### Exemplos de uso

| Objetivo | ACE |
| --- | --- |
| Um host (192.168.1.1) | `access-list 10 permit 192.168.1.1 0.0.0.0` |
| Uma sub-rede (192.168.1.0/24) | `access-list 10 permit 192.168.1.0 0.0.0.255` |
| Intervalo 192.168.16.0 a 192.168.31.0 | `access-list 10 permit 192.168.16.0 0.0.15.255` |

### Cálculo da máscara curinga

Atalho: **255.255.255.255 menos a máscara de sub-rede**.

| Rede | Máscara de sub-rede | Curinga |
| --- | --- | --- |
| 192.168.3.0/24 | 255.255.255.0 | 0.0.0.255 |
| 192.168.3.32/28 | 255.255.255.240 | 0.0.0.15 |
| 192.168.10.0/23 (10.0 e 11.0) | 255.255.254.0 | 0.0.1.255 |
| 192.168.16.0/20 | 255.255.240.0 | 0.0.15.255 |

### Palavras-chave

- **host:** substitui a máscara `0.0.0.0` (um único host)
- **any:** substitui `255.255.255.255` (qualquer endereço)

```
R1(config)# access-list 10 permit host 192.168.10.10
R1(config)# access-list 11 permit any
```

---

## 3. Configurar ACLs

### Planejamento

Antes de aplicar, principalmente em ACLs com várias ACEs:

- Escrever a política num editor de texto
- Adicionar os comandos do IOS e **comentários (remark)** pra documentar
- Copiar e colar no dispositivo
- **Testar sempre** pra garantir que aplica a política desejada

### ACL padrão numerada

```
Router(config)# access-list access-list-number {deny | permit | remark text} source [source-wildcard] [log]
```

| Parâmetro | Descrição |
| --- | --- |
| access-list-number | 1 a 99 ou 1300 a 1999 |
| deny / permit | Nega ou permite se a condição bater |
| remark | Comentário opcional (até 100 caracteres) |
| source | Origem: `any`, `host` + IP, ou IP direto |
| source-wildcard | Opcional. Se omitida, assume `0.0.0.0` |
| log | Opcional. Gera mensagem a cada correspondência. Usa muita CPU, então só pra troubleshooting ou segurança |

Pra remover: `no access-list access-list-number`.

### ACL padrão nomeada

```
Router(config)# ip access-list standard access-list-name
```

- Entra no modo `config-std-nacl`, onde as ACEs são inseridas (sem repetir o `ip access-list` em cada uma)
- Remover: `no ip access-list standard access-list-name`

### ACL estendida numerada

```
Router(config)# access-list access-list-number {deny | permit | remark text} protocol source source-wildcard [operator {port}] destination destination-wildcard [operator {port}] [established] [log]
```

| Parâmetro | Descrição |
| --- | --- |
| access-list-number | 100 a 199 ou 2000 a 2699 |
| protocol | `ip`, `tcp`, `udp`, `icmp` (ou o número: ICMP = 1, TCP = 6, UDP = 17). `ip` bate com todos os protocolos IP |
| source / destination | `any`, `host` + IP, ou IP com curinga |
| operator | `lt`, `gt`, `eq`, `neq` |
| port | Número ou nome da porta TCP/UDP |
| established | Só TCP. Recurso de firewall de primeira geração |
| log | Mensagem a cada correspondência |

- A aplicação na interface é igual à da ACL padrão:

```
Router(config-if)# ip access-group {access-list-number | access-list-name} {in | out}
```

- Pra remover de uma interface: `no ip access-group`. Pra remover do roteador: `no access-list`
- Na ACL **estendida**, a ordem das instruções é a ordem em que foram digitadas (a lógica de ordenação interna da padrão não vale)

### Protocolos e portas

- Dá pra usar **nome ou número** da porta, com o mesmo resultado:

```
R1(config)# access-list 100 permit tcp any any eq www
R1(config)# access-list 100 permit tcp any any eq 80
```

- Quando não existe nome na lista, usa o número (ex: SSH = 22, HTTPS = 443)

### TCP `established`

- Faz um filtro stateful básico: deixa o tráfego interno sair e só permite o **retorno** de conexões existentes
- A correspondência acontece se o segmento tiver as flags **ACK** ou **RST**
- Tráfego TCP iniciado por host externo pra dentro é negado

```
R1(config)# access-list 120 permit tcp any 192.168.10.0 0.0.0.255 established
R1(config)# interface g0/0/0
R1(config-if)# ip access-group 120 out
```

### ACL estendida nomeada

```
Router(config)# ip access-list extended access-list-name
```

Exemplo: `SURFING` na entrada (permite HTTP e HTTPS saindo) e `BROWSING` na saída (só deixa voltar o tráfego web com `established`):

```
R1(config)# ip access-list extended SURFING
R1(config-ext-nacl)# remark Permits inside HTTP and HTTPS traffic
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 80
R1(config-ext-nacl)# permit tcp 192.168.10.0 0.0.0.255 any eq 443
R1(config-ext-nacl)# exit
R1(config)# ip access-list extended BROWSING
R1(config-ext-nacl)# permit tcp any 192.168.10.0 0.0.0.255 established
R1(config-ext-nacl)# exit
R1(config)# interface g0/0/0
R1(config-if)# ip access-group SURFING in
R1(config-if)# ip access-group BROWSING out
```

---

## 4. Modificar uma ACL

### Método do editor de texto

1. Copiar a ACL do `running-config` pro editor
2. Corrigir
3. **Remover a ACL antiga** no roteador (senão os comandos colados só são adicionados no final)
4. Colar a ACL corrigida

```
R1(config)# no access-list 1
R1(config)# access-list 1 deny 192.168.10.10
R1(config)# access-list 1 permit 192.168.10.0 0.0.0.255
```

### Método dos números de sequência

- Os números são atribuídos automaticamente e aparecem no `show access-lists` (o `show running-config` **não** mostra)
- Não dá pra substituir uma instrução usando o mesmo número: primeiro apaga com `no <número>`, depois insere a correta

```
R1(config)# ip access-list standard 1
R1(config-std-nacl)# no 10
R1(config-std-nacl)# 10 deny host 192.168.10.10
R1(config-std-nacl)# end
R1# show access-lists
```

---

## 5. Implementar ACLs

### Diretrizes

- Criar a ACL globalmente e depois aplicar
- Ordem importa: processa de cima pra baixo e **para na primeira correspondência**
- Filtrar do **mais específico pro mais genérico** (ex: nega um host específico e permite o resto)
- Só **uma ACL por interface, por protocolo, por direção**
- Novas instruções entram **no final** da ACL
- Pacotes gerados pelo roteador não são filtrados por ACL de saída
- **Padrão:** o mais próximo possível do **destino**
- **Estendida:** o mais próximo possível da **origem**

### Aplicar

```
Router(config-if)# ip access-group {acl-# | name} {in | out}
Router(config-line)# ip access-class {acl-# | name} {in | out}
```

- `ip access-group`: aplica em **interface**
- `ip access-class`: aplica em **linhas VTY** (restringe quem acessa o roteador por Telnet ou SSH)

Exemplo de ACL padrão nomeada na saída:

```
R1(config)# ip access-list standard NO_ACCESS
R1(config-std-nacl)# deny host 192.168.11.10
R1(config-std-nacl)# permit any
R1(config-std-nacl)# exit
R1(config)# interface g0/0
R1(config-if)# ip access-group NO_ACCESS out
```

Exemplo em VTY com `log`:

```
R1(config)# ip access-list standard VTY_ACCESS
R1(config-std-nacl)# permit 192.168.10.10 log
R1(config-std-nacl)# deny any
R1(config-std-nacl)# exit
R1(config)# line vty 0 4
R1(config-line)# access-class VTY_ACCESS in
```

> O `log` afeta seriamente o desempenho. Usar só quando a rede estiver sob ataque ou pra investigar. ACLs também fazem parte de NAT, firewalls baseados em zona e VPNs.

### Onde posicionar

| Tipo | Posição | Motivo |
| --- | --- | --- |
| **Padrão** | Perto do **destino** | Só olha a origem. Se ficasse perto da origem, bloquearia essa origem pra todos os destinos |
| **Estendida** | Perto da **origem** | Nega o tráfego indesejado logo no começo, sem atravessar a rede toda |

Fatores que influenciam:

- **Controle organizacional:** só dá pra colocar ACL em dispositivos que a organização controla
- **Largura de banda:** filtrar na origem evita gastar banda com tráfego indesejado
- **Facilidade de configuração:** no destino é mais simples, mas gasta banda. Estendida em vários roteadores economiza banda, mas dá mais trabalho

**Exemplo padrão:** pra impedir a rede 192.168.10.0/24 de chegar na 192.168.30.0/24, a ACL fica no R3, **saída da G0/0/0**. Na entrada da S0/1/1 também bloquearia o tráfego da .10 pra rede 192.168.31.0/24, o que não é desejado.

**Exemplo estendida:** negar Telnet e FTP da 192.168.11.0/24 pra rede da Empresa B. Colocar no R1 (que a empresa controla), na **entrada da G0/0/1**, assim só os pacotes da .11 passam pelo filtro.

---

## 6. Mitigar ataques com ACLs

### Spoofing de endereço IP

O atacante altera o IP de origem pra esconder a identidade. Na interface ligada à Internet, nunca deveriam entrar pacotes com origem:

- Endereço todo zero
- Broadcast
- Loopback (127.0.0.0/8)
- APIPA (169.254.0.0/16)
- Endereços privados reservados (RFC 1918)
- Multicast (224.0.0.0/4)

```
R1(config)# access-list 150 deny ip host 0.0.0.0 any
R1(config)# access-list 150 deny ip 10.0.0.0 0.255.255.255 any
R1(config)# access-list 150 deny ip 127.0.0.0 0.255.255.255 any
R1(config)# access-list 150 deny ip 172.16.0.0 0.15.255.255 any
R1(config)# access-list 150 deny ip 192.168.0.0 0.0.255.255 any
R1(config)# access-list 150 deny ip 224.0.0.0 15.255.255.255 any
R1(config)# access-list 150 deny ip host 255.255.255.255 any
```

Na interface da LAN, permitir só a origem da própria rede:

```
R1(config)# access-list 105 permit ip 192.168.1.0 0.0.0.255 any
```

### Permitir só o tráfego necessário pelo firewall

- Serviços geralmente permitidos: DNS, SMTP, FTP
- Serviços de administração remota: SSH, Syslog, SNMP (devem ser controlados e monitorados, porque podem ser explorados)

```
R1(config)# access-list 180 permit udp any host 192.168.20.2 eq domain
R1(config)# access-list 180 permit tcp any host 192.168.20.2 eq smtp
R1(config)# access-list 180 permit tcp any host 192.168.20.2 eq ftp
R1(config)# access-list 180 permit tcp host 200.5.5.5 host 10.0.1.1 eq 22
R1(config)# access-list 180 permit udp host 200.5.5.5 host 10.0.1.1 eq syslog
R1(config)# access-list 180 permit udp host 200.5.5.5 host 10.0.1.1 eq snmptrap
```

### Ataques ICMP

- Hackers usam **echo** pra descobrir hosts e sub-redes e fazer flood (DoS), e **redirect** pra alterar tabelas de roteamento. Echo e Redirect devem ser bloqueados na entrada

Permitir **entrar** na rede interna:

- **Echo reply:** permite que os usuários façam ping em hosts externos
- **Source quench:** pede ao remetente pra diminuir o ritmo
- **Unreachable:** gerado quando um pacote é negado por uma ACL

Permitir **sair** da rede:

- **Echo**, **parameter problem**, **packet too big** (descoberta de MTU) e **source quench**
- Regra geral: bloquear os outros tipos de ICMP na saída

```
R1(config)# access-list 112 permit icmp any any echo-reply
R1(config)# access-list 112 permit icmp any any source-quench
R1(config)# access-list 112 permit icmp any any unreachable
R1(config)# access-list 112 deny icmp any any
R1(config)# access-list 112 permit ip any any
```

### Ataques SNMP

- ACLs filtram pacotes SNMP de sistemas não autorizados, mas ainda dá pra explorar se o endereço for falsificado e permitido pela ACL
- Medida mais eficaz: **desativar o servidor SNMP** quando não é necessário:

```
R1(config)# no snmp-server
```

---

## 7. ACLs IPv6

### Visão geral

- IPv4 e IPv6 vão coexistir por um tempo, o que cria brechas (ambientes **dual stack**)
- Ameaças: exploração de confiança em hosts dual stack, mensagens NDP de roteador falso (RA) e **tunelamento** (ex: **Teredo**, que encapsula IPv6 em pacotes IPv4 UDP)
- Estratégia de mitigação deve incluir filtragem na borda com ACLs IPv6

### Sintaxe

- Funcionam de forma parecida com as ACLs IPv4, mas **não existe ACL padrão** em IPv6
- **Toda ACL IPv6 é nomeada** e é mais flexível (filtra origem, destino, cabeçalhos de extensão e camada superior, como a estendida do IPv4)
- Criar: `ipv6 access-list nome`
- Aplicar na interface: `ipv6 traffic-filter`

```
Router(config)# ipv6 access-list access-list-name
Router(config-ipv6-acl)# deny | permit protocol {source-ipv6-prefix/prefix-length | any | host source-ipv6-address} [operator [port-number]] {destination-ipv6-prefix/prefix-length | any | host destination-ipv6-address} [operator [port-number]] [dscp value] [fragments] [log] [log-input] [sequence value] [time-range name]
```

Parâmetros extras em relação ao IPv4: `dscp`, `fragments`, `log-input` (inclui a interface de entrada no log), `sequence` e `time-range` (regra com intervalo de tempo).

### Configuração

- Negação implícita: `deny ipv6 any any`
- Toda ACL IPv6 também tem **permissões implícitas** pro **Neighbor Discovery** (NA e NS). Se configurar `deny ipv6 any any` sem permitir o ND explicitamente, o **NDP é desativado**
- Boa prática: documentar as instruções implícitas, escrevendo-as na ACL

```
R1(config)# ipv6 access-list LAN_ONLY
R1(config-ipv6-acl)# permit 2001:db8:1:1::/64 any
R1(config-ipv6-acl)# permit icmp any any nd-na
R1(config-ipv6-acl)# permit icmp any any nd-ns
R1(config-ipv6-acl)# deny ipv6 any any
R1(config-ipv6-acl)# end
R1# show ipv6 access-list
```

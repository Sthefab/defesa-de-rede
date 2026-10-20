# Tecnologias de Firewall

Resumo sobre firewalls: operação, tipos, filtragem de pacotes x stateful, arquiteturas de design de rede e defesa em camadas.

---

## 1. Firewalls

Sistema (ou grupo de sistemas) que aplica uma **política de controle de acesso** entre redes.

### Propriedades comuns

- São resistentes a ataques de rede
- São o **único ponto de trânsito** entre a rede interna e as redes externas (todo o tráfego passa por eles)
- Reforçam a política de controle de acesso

### Benefícios

- Impedem a exposição de hosts, recursos e aplicações sensíveis a usuários não confiáveis
- Sanitizam o fluxo do protocolo, o que impede a exploração de falhas no protocolo
- Bloqueiam dados maliciosos de servidores e clientes
- Reduzem a complexidade do gerenciamento de segurança, concentrando o controle de acesso em poucos firewalls

### Limitações

- Firewall mal configurado pode ter consequências sérias (ex: virar um **ponto único de falha**)
- Dados de muitas aplicações não passam pelo firewall com segurança
- Usuários podem tentar contornar o firewall pra acessar conteúdo bloqueado, o que expõe a rede
- Pode diminuir o desempenho da rede
- Tráfego não autorizado pode ser encapsulado ou disfarçado de tráfego legítimo

---

## 2. Tipos de firewall

| Tipo | Camadas OSI | Como funciona |
| --- | --- | --- |
| **Filtragem de pacotes (sem estado)** | 3 e 4 | Permite ou nega com base em consulta a uma tabela de políticas. Geralmente faz parte do firewall do roteador |
| **Stateful (com estado)** | 3, 4 e 5 | Guarda informações das conexões numa **tabela de estado**. É o mais versátil e comum |
| **Gateway de aplicação (proxy)** | 3, 4, 5 e 7 | O cliente se conecta ao proxy, e o proxy se conecta ao servidor em nome dele. O servidor só enxerga o proxy. Controle feito em software |
| **NGFW (última geração)** | Várias | Vai além do stateful (veja abaixo) |

Exemplo de filtragem de pacotes: servidores SMTP escutam na porta 25, então o administrador pode bloquear a porta 25 de uma estação específica pra impedir que ela espalhe um vírus por e-mail.

### NGFW (Next-Generation Firewall)

- Prevenção de intrusão (IPS) integrada
- Reconhecimento e controle de aplicações (vê e bloqueia aplicações arriscadas)
- Caminhos de atualização pra incluir feeds de informações futuros
- Técnicas pra lidar com ameaças em evolução

### Outras formas de implementação

- **Baseado em host:** PC ou servidor com software de firewall rodando
- **Transparente:** filtra tráfego IP entre um par de interfaces em ponte
- **Híbrido:** combinação de tipos (ex: firewall de inspeção de aplicações = stateful + gateway de aplicação)

---

## 3. Filtragem de pacotes (sem estado)

Examina **cada pacote individualmente**, sem considerar o estado da conexão.

| Vantagens | Desvantagens |
| --- | --- |
| Regras simples de permitir ou negar | Suscetível a **IP spoofing** (pacote forjado que atende os critérios da ACL passa) |
| Baixo impacto no desempenho | Não filtra de forma confiável **pacotes fragmentados** (só o 1º fragmento tem o cabeçalho TCP, os demais passam direto) |
| Fácil de implementar, suportado pela maioria dos roteadores | ACLs complexas, difíceis de implementar e manter |
| Dá um grau inicial de segurança na camada de rede | Não filtra dinamicamente serviços com negociação de porta dinâmica sem abrir uma faixa grande de portas |
| Faz quase tudo de um firewall high-end por custo bem menor | Não é uma solução completa, só um elemento da política de segurança |

---

## 4. Firewall stateful

| Benefícios | Limitações |
| --- | --- |
| Meio primário de defesa, filtra tráfego indesejado | Sem inspeção da camada de aplicação (não examina o conteúdo do HTTP) |
| Filtragem de pacotes mais forte, com controle mais rigoroso | Rastreamento limitado de protocolos sem estado (**UDP e ICMP** não geram informação de conexão pra tabela de estado) |
| Desempenho melhor que filtros de pacotes ou proxies | Difícil rastrear conexões com **negociação dinâmica de porta** (apps que abrem várias conexões) |
| Defende contra **spoofing e DoS** (vê se o pacote pertence a uma conexão existente) | Não suporta **autenticação de usuário** |
| Registra mais informações (logs mais ricos) | |

---

## 5. Arquiteturas de segurança comuns

### Privado e público

- Firewall com duas interfaces: rede **privada (confiável)** e **pública (não confiável)**
- Tráfego da rede privada pra pública: permitido e inspecionado. O tráfego de retorno associado também é permitido
- Tráfego originado na rede pública pra privada: **bloqueado**

### DMZ (zona desmilitarizada)

Três interfaces: interna (privada), externa (pública) e DMZ.

| Origem → Destino | Política |
| --- | --- |
| Privada → pública ou DMZ | Permitido e inspecionado, com pouca ou nenhuma restrição. O retorno também é permitido |
| DMZ → privada | Geralmente **bloqueado** |
| DMZ → pública | Permitido seletivamente, conforme os serviços |
| Pública → DMZ | Permitido seletivamente e inspecionado (e-mail, DNS, HTTP, HTTPS). O retorno da DMZ é permitido dinamicamente |
| Pública → privada | **Bloqueado** |

### Firewalls de política baseados em zona (ZPF)

- **Zona:** grupo de uma ou mais interfaces com funções ou recursos parecidos (ex: LAN 1 e LAN 2 numa mesma zona)
- Tráfego **dentro da mesma zona** passa livremente, sem política
- Tráfego **entre zonas** é bloqueado por padrão (`deny any`). Pra liberar, precisa configurar uma política que permita ou inspecione
- **Zona própria (self):** o próprio roteador, com todos os IPs das interfaces. É a única exceção ao deny padrão (não tem política por padrão). Considerar tráfego de plano de gerenciamento e de controle, como SSH, SNMP e protocolos de roteamento

---

## 6. Defesa em camadas

Quatro camadas:

1. **Segurança do núcleo da rede:** protege contra software malicioso e anomalias de tráfego, impõe políticas de rede e garante capacidade de sobrevivência
2. **Segurança de perímetro:** protege os limites entre zonas
3. **Segurança das comunicações:** fornece garantia da informação
4. **Segurança do endpoint:** identidade e conformidade do dispositivo com a política de segurança

### Como funciona na prática

- Diferentes tipos de firewall combinados em camadas, com políticas **entre** e **dentro** das camadas
- Fluxo: tráfego da rede não confiável passa pelo **filtro de pacotes no roteador de borda** → firewall ou **host bastion** (computador endurecido, normalmente na DMZ) → **roteador de triagem interior** → host interno
- Esse tipo de DMZ é chamado de **configuração de sub-rede rastreada (screened subnet)**

### O que os firewalls NÃO fazem

- Não interrompem, em geral, intrusões vindas de hosts **dentro** da rede ou zona
- Não protegem contra instalação de **access points não autorizados (rogue AP)**
- Não substituem backup e recuperação de desastres
- Não substituem administradores e usuários bem informados

### Boas práticas

- Posicionar firewalls nos **limites de segurança** (e não depender só deles)
- **Negar todo o tráfego por padrão**
- Permitir só os serviços necessários
- Controlar o acesso físico ao firewall
- Monitorar os logs regularmente
- Praticar gerenciamento de mudanças nas configurações
- Lembrar que protegem principalmente contra ataques técnicos vindos de **fora**

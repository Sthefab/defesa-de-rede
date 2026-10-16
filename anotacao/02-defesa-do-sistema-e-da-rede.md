# Defesa do Sistema e da Rede

Resumo sobre segurança física, segurança de aplicações, fortalecimento de rede, segmentação, dispositivos sem fio e móveis, resiliência e sistemas embarcados.

---

## 1. Segurança física

### Cercas e barreiras físicas

- Primeira camada de defesa e a mais visível
- Componentes: cerca de perímetro, portão de segurança, cabeços de amarração (bollards), barreiras de entrada de veículos, abrigos de proteção
- **Altura da cerca:**
  - 1 m: detém só invasores casuais
  - 2 m: alto demais pra invasores casuais subirem
  - 2,5 m: atraso limitado pra invasor determinado
- Áreas de alta segurança usam "proteção superior" (arame farpado), mas dá pra contornar com cobertor ou colchão
- Cercas precisam de manutenção (animais fazendo toca, erosão) e veículos não devem ficar estacionados perto

### Biometria

- Características fisiológicas ou comportamentais usadas pra identificar e liberar acesso: face, impressão digital, geometria da mão, íris, retina, assinatura e voz
- Fatores na escolha: precisão, taxa de transferência, aceitação, exclusividade, resistência à falsificação, confiabilidade, armazenamento, tempo de inscrição e intrusão
- **Precisão** é o fator mais importante:
  - **Erro Tipo I (rejeição falsa):** rejeita um usuário autorizado. Menos grave em controle de acesso, mas pode prejudicar o negócio
  - **Erro Tipo II (aceitação falsa):** deixa entrar quem não deveria. É o erro mais grave

---

## 2. Vigilância

- **Guardas e escoltas:** melhor opção quando precisa de resposta instantânea e se adaptam a situações. Desvantagens: custo, limite pra monitorar alto volume e erro humano
- **Vigilância eletrônica e por vídeo:** complementa ou substitui guardas. Monitora sem pessoal presente, grava por longos períodos e detecta movimento. Em ambiente seguro, cobre entradas, saídas, carregamento, escadas e áreas de descarte
- **RFID:** tags rastreiam ativos que saem de uma área segura, leem várias tags ao mesmo tempo, sem linha de visão e sem precisar estar visíveis (diferente de código de barras)

---

## 3. Segurança de aplicações

### Desenvolvimento de aplicativos

- **Desenvolvimento e teste:** ambiente menos restritivo, com controle de versão e sandbox. QA acha falhas quando é mais fácil corrigir
- **Preparo (staging) e produção:** staging deve espelhar a produção pra validar as configurações de segurança antes do deploy
- **Provisionamento:** criação ou atualização de software. **Desprovisionamento:** remoção

### Técnicas de codificação segura

- **Normalização:** converte a entrada pra forma mais simples, pra identificar entrada maliciosa
- **Procedimento armazenado:** instruções SQL pré-compiladas no banco, reduzem tráfego e dão resultado mais rápido
- **Ofuscação e camuflagem:** dificultam engenharia reversa. Ofuscação oculta dados originais com dados aleatórios, camuflagem troca dados sensíveis por dados fictícios realistas
- **Reutilização de código:** economiza tempo, mas pode trazer vulnerabilidades
- **SDKs e bibliotecas de terceiros:** aceleram o desenvolvimento, mas uma vulnerabilidade afeta muitos aplicativos

### Validação de entrada

- Controlar a entrada de dados é essencial pra integridade do banco
- Exemplo: atacante modifica o link de confirmação de newsletter, alterando usuário, e-mail ou status, e pode automatizar pra inundar o banco com assinantes inválidos
- **Regras de validação:** tamanho, formato, consistência, intervalo e dígito de verificação (ex: cálculo do dígito do ISBN)

### Verificações de integridade

- Usam **função hash** (ex: checksum, MD5, SHA-1, SHA-256, SHA-512) pra tirar um "instantâneo" dos dados e confirmar que não mudaram
- **Checksum:** compara a soma antes e depois da transferência
- **Controle de versão:** impede dois usuários de alterar o mesmo objeto ao mesmo tempo
- **Backups:** precisam ter a integridade verificada
- **Autorização:** controla quem acessa e modifica (ex: arquivo somente leitura)

### Outras práticas

- **Assinatura de código:** prova a autenticidade do software e que não foi alterado
- **Cookies seguros:** impedem que sejam transmitidos por HTTP não criptografado

### Controle de ameaças a aplicativos

| Ameaça | Controle |
| --- | --- |
| Acesso não autorizado a data centers e salas | Políticas e procedimentos pra funcionários e visitantes |
| Inatividade do sistema | Plano de continuidade de negócios e de recuperação de desastres |
| Vulnerabilidade de software do SO | Política de atualizações e patches regulares |
| Acesso não autorizado a sistemas | Autenticação multifator e monitoramento de logs |
| Perda de dados | Classificação de dados e backups |
| Vulnerabilidades no desenvolvimento | Testes antes do lançamento |

---

## 4. Fortalecimento da rede: serviços e protocolos

Scanner de porta mostra quais portas estão abertas, e atacantes também usam. Manter expostas só as portas necessárias.

| Serviço | Pontos principais |
| --- | --- |
| **DHCP** | Atribui IPs automaticamente. Proteção: DHCP snooping, servidor atrás de firewall, patches, monitorar logs, fechar portas e remover serviços não usados |
| **DNS** | Converte nome em IP. Atacantes negam acesso ou redirecionam pra sites falsos. Proteção: DNSSEC (assinaturas digitais), separar DNS interno e externo, restringir transferências de zona, esconder versão, logs |
| **ICMP** | Mensagens de erro e ping. Usado em reconhecimento, canal secreto e DoS. Muitas redes filtram |
| **RIP** | Roteamento por contagem de saltos (máx. 15). Proteção: autenticação e patches |
| **NTP** | Sincroniza relógios. Hora correta é essencial pra logs e certificados digitais. Usar autenticação NTP |

### Telnet, SSH e SCP

- **Telnet:** texto sem criptografia (inclusive usuário e senha), porta 23
- **SSH:** conexão remota criptografada, porta TCP 22. Usar no lugar do Telnet
- **SCP:** transferência segura de arquivos usando SSH
- No Wireshark dá pra ver usuário e senha em texto claro no Telnet, e no SSH tudo vai criptografado

### Protocolos seguros

- **SNMPv3:** padrão atual, com criptografia e integridade
- **HTTP (porta 80):** sem segurança. **HTTPS** usa SSL/TLS (TLS é o substituto mais seguro do SSL)
- **FTP:** usuário e senha em texto claro. **FTPS** adiciona TLS/SSL
- **POP (110), IMAP (143) e MIME:** proteger com SSL/TLS. **S/MIME** manda mensagens assinadas e criptografadas (autenticação, integridade e não repúdio)

---

## 5. Segmentação de rede

### VLANs

- Agrupam dispositivos logicamente (LANs são físicas, VLANs são lógicas)
- Segmentam por função, equipe ou aplicativo e isolam grupos com dados sensíveis (ex: RH)
- Portas **trunk** levam tráfego de várias VLANs entre switches
- Limitam tráfego de broadcast. Proteger monitorando desempenho, configuração avançada e patches

### DMZ

Pequena rede entre a rede privada confiável e a Internet. Servidores web e de e-mail ficam nela.

| Zona | Risco | Confiança |
| --- | --- | --- |
| LAN | Baixo | Alta |
| Extranet | Médio-baixo | Médio-alta |
| DMZ | Médio-alto | Médio-baixa |
| Internet | Alto | Baixa |

### Zero Trust

Não confia automaticamente em nenhum usuário ou endpoint e monitora todos constantemente, independente de status ou função.

---

## 6. Dispositivos sem fio e móveis

### Segurança sem fio

- **WEP:** primeiro protocolo, fraco (chaves de 64/128 bits)
- **WPA:** chaves de 256 bits, MIC (verificação de integridade) e TKIP. Config comum: WPA-PSK
- **WPA2 (2006):** AES obrigatório e CCMP no lugar do TKIP
- **WPA3:** algoritmos mais robustos e troca de chaves melhorada
- **WPS:** PIN pode ser descoberto por força bruta, deve ser desativado

### Autenticação

- **Sistema aberto:** qualquer dispositivo conecta, sem preocupação com segurança
- **Chave compartilhada:** autentica e criptografa entre cliente e AP
- **EAP:** estrutura de autenticação em redes sem fio

| Método | Cert. cliente | Cert. servidor | Segurança |
| --- | --- | --- | --- |
| EAP-TLS | Sim | Sim | Alta |
| PEAP | Não | Sim | Média |
| EAP-TTLS | Não | Sim | Média |
| EAP-FAST | Não | Não | Média |

### Autenticação mútua

- **Access point não autorizado (rogue AP):** imita um AP legítimo pra roubar dados (ataque man-in-the-middle)
- **Autenticação mútua:** as duas pontas se autenticam antes de conectar, permitindo detectar APs falsos

### Métodos de comunicação

- **Wi-Fi e Bluetooth**
- **NFC:** comunicação sem contato (pagamentos por aproximação)
- **Infravermelho (IR):** curto alcance
- **USB:** única com fio, pode servir de armazenamento, modem ou conexão com dispositivo forense

### Gerenciamento de dispositivos móveis

- Dispositivo corporativo ou pessoal habilitado (**COPE**), ou **BYOD**
- **Segmentação e conteinerização:** separam conteúdo pessoal do corporativo numa área autenticada e criptografada. Permitem isolar apps, controlar funções, apagar o container e bloquear ou limpar o dispositivo remotamente
- **Gerenciamento de conteúdo:** cuidado com apps que compartilham dados (Dropbox, Google Drive, iCloud)
- **Gerenciamento de aplicativos:** lista de permissão (allowlist) com apps assinados digitalmente e senhas fortes

### Proteções de dispositivo móvel

- **Riscos:** roubo, perda, acesso não autorizado, riscos de SO, de apps e de rede
- **Jailbreak (Apple), root (Android) e sideload:** contornam as restrições do dispositivo e são riscos pra empresa. Dá pra detectar e marcar como não compatível
- **Proteções:** bloqueio de tela, biometria, autenticação sensível ao contexto (machine learning), limpeza remota e criptografia completa

### Rastreamento por GPS

- Precisão de cerca de 5 metros
- Apps usam pra check-in e geofencing, e notificações push com localização podem ser usadas por atacantes pra capturar dados

---

## 7. Resiliência de cibersegurança

### Alta disponibilidade

Sistemas projetados pra evitar inatividade. Três princípios:

1. **Eliminar pontos únicos de falha:** hot standby, componentes redundantes, vários caminhos
2. **Fornecer transição confiável:** fontes redundantes, energia e comunicação de backup
3. **Detectar falhas quando ocorrem:** monitoramento ativo, que pode acionar o backup

### Os cinco noves

- Meta de 99,999% de disponibilidade, ou seja, menos de 5,26 minutos de inatividade por ano
- Práticas: sistemas padronizados, **clustering** (vários dispositivos parecem um só) e sistemas de componente compartilhado

### Pontos únicos de falha

Qualquer parte (hardware, processo, dado ou utilitário) cuja falha derruba o sistema todo. Solução: não depender de um único elemento e criar redundância.

### Redundância N+1

- N componentes + pelo menos 1 de reserva (analogia: 4 pneus + estepe)
- Aguenta a perda de um componente, mas não é totalmente redundante

### RAID

Espalha dados entre vários discos, aumentando tolerância a falhas e velocidade.

- **Espelhamento:** duplica os dados em outra unidade
- **Distribuição (striping):** grava segmentos consecutivos em unidades diferentes
- **Paridade:** gera checksums pra checar erros nos dados distribuídos
- Pode ser por software ou hardware (com controlador dedicado)

### Spanning Tree Protocol (STP)

- Previne loops em redes com caminhos redundantes entre switches
- Bloqueia intencionalmente os caminhos redundantes e desbloqueia se um link falhar

### Redundância de roteador

- Gateway padrão único é um ponto único de falha
- Roteador ativo + roteador standby com **IP virtual** (usado pelos dispositivos como gateway) e IPs físicos pra trocar mensagens periódicas
- Se o standby para de receber as mensagens, assume o papel ativo
- Chamado de **redundância de primeiro salto**

### Redundância de local

| Tipo | Características |
| --- | --- |
| Síncrona | Tempo real, alta largura de banda, locais próximos |
| Assíncrona | Quase em tempo real, menos banda, locais podem ser distantes |
| Point-in-time | Atualiza periodicamente, mais econômica em banda |

### Design resiliente

- Resiliência é mais que adicionar redundância: precisa entender as necessidades do negócio
- **Resiliência de aplicativo**, do mais barato ao mais caro: backup e restauração, arquitetura de cluster, hardware tolerante a falhas
- **Resiliência do IOS (Cisco):** mantém cópia segura da imagem do IOS e da configuração em execução (bootset primário), que o usuário não consegue remover

### Backups

- **Frequência:** backup completo semanal/mensal + parciais frequentes (muitos parciais aumentam o tempo de restauração)
- **Armazenamento:** levar pra local externo aprovado, em rotação conforme a política
- **Segurança:** proteger com senha
- **Validação:** sempre validar pra garantir integridade

### Projetando alta disponibilidade

- **Eliminar ou reduzir pontos únicos de falha:** inclui roteadores centrais, serviços de rede e até equipe de TI. Clusters de alta disponibilidade compartilham armazenamento e config de rede
- **Tolerância a falhas:** sistema continua funcionando se componentes falham (ex: espelhamento de dados)
- **Resiliência do sistema:** manter disponibilidade mesmo sob ataque

### Energia

- Data centers em fonte de alimentação separada do resto do prédio, com feeds redundantes de subestações diferentes, backup e UPS
- **Excesso:** pico (momentâneo), sobrecarga (prolongada)
- **Perda:** falha (momentânea), blackout (completa)
- **Degradação:** sag/dip (queda momentânea), brownout (queda prolongada), corrente de inrush (pico inicial)

### HVAC

- Controla temperatura, umidade e fluxo de ar. Seguir os requisitos ambientais do fabricante evita falhas
- Sistemas conectados à Internet e gerenciados por terceiros são um risco, porque muita gente acaba tendo acesso às credenciais

### Controle de ameaças a instalações físicas

- Controle de acesso e CCTV em todas as entradas
- Políticas pra visitantes
- Testes de intrusão físicos e cibernéticos
- Criptografia de crachá
- Planos de recuperação de desastres e continuidade de negócios
- Treinamento e conscientização
- Marcação de ativos

---

## 8. Sistemas embarcados e especiais

### Setores industriais (SCADA)

- Ataques como o **Stuxnet** mostraram que ataque cibernético pode destruir infraestrutura crítica
- **SCADA/ICS** foram projetados sem pensar em segurança, e a conexão com a TI corporativa aumenta a vulnerabilidade
- Proteção: segregar a rede SCADA da LAN da empresa

### Internet das Coisas (IoT)

- Bilhões de dispositivos conectados ampliam a superfície de ataque e o volume de dados (Big Data)
- Dispositivos IoT infectados já foram usados em grandes ataques DDoS
- Boas práticas: garantir que consigam atualizar firmware e trocar credenciais padrão
- Muitos usam **RTOS** (sistema operacional em tempo real). Vulnerabilidades: injeção de código, DoS e inversão de prioridade

### Sistemas integrados (embedded)

- Capturam, armazenam e acessam dados (Smart TVs, HVAC, dispositivos médicos, carros)
- Vulneráveis a **ataques de tempo**, um tipo de ataque de canal lateral (usa tempo, consumo de energia, vazamento eletromagnético ou som)
- **SoC (System on Chip):** módulo compacto (ex: Raspberry Pi, Arduino) que integra microcontrolador, processador e periféricos. Muitos têm autenticação ruim ou não recebem patches

### VoIP

- Voz sobre IP. Só é seguro se a rede por baixo for segura
- Contramedidas: criptografar pacotes de voz, SSH em gateways e switches, trocar senhas padrão, IDS contra envenenamento ARP, autenticação forte, firewalls que reconhecem VoIP
- Ataques: spoofing de registro, representação de proxy e sequestro de chamadas

### Sistemas embarcados de finalidade específica

- **Médicos:** marca-passos, bombas de insulina e desfibriladores, com riscos à segurança do paciente e vazamento de prontuários
- **Automotivos:** riscos de rastreamento não autorizado, interferência sem fio e spoofing. Contramedidas: design seguro, criptografia entre controladores e firewall
- **Aviação:** credenciais codificadas, protocolos inseguros e backdoors. Drones são suscetíveis a sequestro, ataques de Wi-Fi, spoofing de GPS e bloqueio

### Tecnologias de enganação

- **Honeypot:** sistema isca que imita um servidor pra atrair e registrar o atacante
- **Honeynet:** conjunto de honeypots imitando a rede
- **Honey files:** arquivos falsos como isca
- **Sinkhole DNS:** impede a resolução de nomes pra URLs específicas, afastando usuários de recursos maliciosos

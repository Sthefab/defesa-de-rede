# Segurança na Nuvem

Resumo sobre virtualização, computação em nuvem, ameaças, domínios de segurança, responsabilidade compartilhada, segurança de aplicações e de dados na nuvem e proteção de VMs.

---

## 1. Virtualização

Reduz o número de máquinas físicas (servidores e estações de trabalho) no ambiente de TI.

### Máquinas virtuais (VMs)

- **Hypervisor:** software ou hardware que permite rodar vários sistemas operacionais independentes em um único sistema físico. É o componente-chave da virtualização
- **Tipo 1 (virtualização de hardware):** o SO convidado roda direto no hardware, sob controle do sistema host
- **Tipo 2 (virtualização hospedada):** um aplicativo na máquina host cria VMs feitas inteiramente de software
- VMs usam SO, então **precisam de patches**
- VMs compartilham hardware e rodam com privilégios altos. Um invasor que compromete uma VM pode comprometer o **host**

### Contêineres

- Diferente da VM, o contêiner tem **só o aplicativo e suas dependências** e usa virtualização no nível do SO
- **Docker:** plataforma aberta que distribui software em pacotes (contêineres), fáceis de mover
- **Kubernetes:** software pra gerenciar contêineres
- Se um usuário ou aplicativo tiver privilégios elevados no contêiner, o **SO subjacente pode ser comprometido**

### VDI (infraestrutura de desktops virtuais)

- Ambiente do usuário armazenado remotamente em um servidor (thin client ou desktop virtual)
- Facilita criar, excluir, copiar, arquivar e baixar configurações
- Exige alta disponibilidade e capacidade de armazenamento

---

## 2. Tecnologia e computação em nuvem

Permite acessar computação, armazenamento, software e servidores pela Internet. Move o componente de tecnologia pro provedor de nuvem.

### Modelos de serviço (XaaS)

| Modelo | O que oferece |
| --- | --- |
| **SaaS** (Software as a Service) | Acesso a software de aplicativos e bancos de dados. O provedor gerencia a infraestrutura |
| **PaaS** (Platform as a Service) | Acesso remoto às ferramentas e serviços de desenvolvimento, por assinatura |
| **IaaS** (Infrastructure as a Service) | Recursos de computação virtualizados pela Internet. O provedor hospeda hardware, software, servidores e armazenamento |

### Modelos de implantação

| Tipo | Características |
| --- | --- |
| **Privada** | Hospedada em plataforma privada. Mais controle sobre os dados, mas custo maior (infraestrutura, manutenção e administração) |
| **Pública** | Hospedada por um provedor em instalação externa. Taxa mensal ou anual, custo menor, mas **menos controle** dos dados |
| **Híbrida** | Combina nuvem privada e pública |
| **Comunitária** | Esforço colaborativo em que mais de uma empresa compartilha a mesma plataforma, voltada às necessidades de um setor (saúde, energia) |

### Fog e edge computing

Surgiram com a explosão da IoT.

- **Fog computing (neblina):** distribui a computação entre o dispositivo e o data center em nuvem. Dados processados em um **gateway IoT / fog node** na rede local. Importante onde milissegundos contam (veículos autônomos, aviação, manufatura)
- **Edge computing (borda):** dados processados **no próprio dispositivo ou sensor**, sem ir pra um data center

---

## 3. Principais ameaças à computação em nuvem

A nuvem sofre das mesmas ameaças das redes físicas, mais algumas exclusivas. Se um recurso de nuvem for comprometido, o atacante pode:

- Usar a nuvem pra atacar outras entidades online
- Hospedar malware em provedores respeitados, que parece legítimo
- Abusar da nuvem pra DDoS, conteúdo pirata, spam e phishing

| Ameaça | Descrição |
| --- | --- |
| **Violação de dados** | Dados confidenciais acessados por entidade não autorizada |
| **Configuração incorreta** | Recurso configurado errado, fica vulnerável |
| **Arquitetura de segurança ruim** | Na privada, a segurança é da empresa. Em pública, híbrida e comunitária é **responsabilidade compartilhada**, e isso gera brechas se não for bem entendido |
| **Credenciais comprometidas** | Contas e privilégios mal protegidos. Em nuvem pública, a **conta de serviço** tem o maior privilégio, e sequestrá-la dá controle de todos os recursos |
| **Ameaça interna** | Funcionário, empreiteiro ou parceiro compromete o serviço, de forma mal-intencionada ou não |
| **UI ou API insegura** | São os pontos mais expostos à Internet, então são alvos |
| **Visibilidade limitada** | O cliente não vê o serviço por completo e tem dificuldade de identificar arquivos seguros ou maliciosos |

---

## 4. Domínios da segurança em nuvem

Referência: **Guia de segurança para áreas de foco essenciais na computação em nuvem v4**, da **Cloud Security Alliance (CSA)**, com **14 domínios**.

| Domínio | Resumo |
| --- | --- |
| Conceitos e arquiteturas | Terminologia e estruturas lógicas e arquitetônicas usadas no guia |
| Governança e gerenciamento de riscos corporativos | Governança, riscos empresariais, riscos de informações e segurança da informação |
| Questões jurídicas, contratos e descoberta eletrônica | Transferência de dados pra nuvem, contratação de provedores e pedidos de descoberta eletrônica em litígios |
| Gerenciamento de conformidade e auditoria | Desafios de comprovar conformidade ao sair do data center tradicional |
| Governança da informação | Uso de dados em conformidade com políticas, padrões e estratégia da empresa |
| Plano de gerenciamento e continuidade dos negócios | Proteger o plano de gerenciamento da nuvem, mais continuidade de negócios e recuperação de desastres |
| Segurança como serviço | Serviços de segurança fornecidos pela nuvem |
| Segurança de infraestrutura | Aspectos de infraestrutura específicos da nuvem |
| Virtualização e contêineres | Proteger a virtualização e os ativos virtuais |
| Resposta a incidentes | Ciclo de vida de IR e considerações no ambiente de nuvem |
| Segurança de aplicações | Criar e implantar aplicações com segurança (principalmente PaaS e IaaS) |
| Segurança e criptografia de dados | Segurança baseada em risco (não se protege tudo igual). Criptografia é um dos controles mais importantes |
| IAM (identidade, direito e acesso) | Como a identidade na nuvem difere da tradicional |
| Tecnologias relacionadas | Tecnologias que dependem da nuvem ou são comuns em implantações na nuvem |

---

## 5. Segurança de infraestrutura em nuvem

### Camadas da infraestrutura

- **Computação física e lógica** (CPU, memória), redes e armazenamento combinados pra criar a nuvem
- **Infraestrutura virtual** gerenciada pelo usuário (computação, rede e armazenamento dos pools de recursos)

As medidas tradicionais (controlar caminhos físicos de comunicação e inserir dispositivos de segurança) **não funcionam** na nuvem. Ferramentas personalizadas (dispositivos virtuais e agentes de software) ajudam, mas podem gerar **gargalos** e sobrecarga de processador, então precisam ser bem avaliadas.

**SDN (redes definidas por software)** traz ganhos de segurança:

- Isolamento de rede fácil, sem restrição de hardware físico
- **Firewalls SDN** (grupos de segurança) aplicados aos ativos com critérios mais flexíveis que firewalls de hardware

### Responsabilidade compartilhada

O **CSP** (provedor de serviços em nuvem) cuida de parte, e o cliente do resto. A divisão muda conforme o modelo.

| Responsabilidade | No local | IaaS | PaaS | SaaS |
| --- | --- | --- | --- | --- |
| Dados | Cliente | Cliente | Cliente | Cliente |
| Endpoints | Cliente | Cliente | Cliente | Compartilhado |
| Gerenciamento de identidades | Cliente | Cliente | Compartilhado | Compartilhado |
| Aplicação | Cliente | Cliente | Compartilhado | CSP |
| Controle de rede | Cliente | Cliente | Compartilhado | CSP |
| Infraestrutura do host | Cliente | Cliente | CSP | CSP |
| Infraestrutura física | Cliente | CSP | CSP | CSP |

Regra pra guardar: **os dados são sempre responsabilidade do cliente**, e a infraestrutura física passa pro provedor em qualquer modelo de nuvem.

### Outras considerações

- **Políticas de segurança da empresa:** apps desconhecidos baixados pelos usuários aumentam a produtividade, mas criam lacunas e pontos cegos. Políticas bem definidas e conscientização ajudam
- **Segurança em camadas:** 4 camadas (hardware, infraestrutura, plataforma e aplicações), com defesa em profundidade em cada uma:
  - Segurança integrada na plataforma (ex: proteção DDoS embutida)
  - **VPC (nuvem privada virtual):** sub-redes privadas logicamente isoladas da Internet
  - **Firewalls virtuais** do CSP (regras de permitir e negar, grupos de segurança de host)
  - **Logs de fluxo:** monitoram o tráfego nas interfaces de rede
  - **VPNs:** acesso remoto e conexões site a site (multinuvem ou nuvem + data center local)
  - **IAM:** credenciais, autenticação e autorização. Uso correto é fundamental pra evitar abuso
- **Microssegmentação (hipersegregação):** usa topologias de rede virtual pra rodar várias redes menores e mais isoladas, sem custo extra de hardware. Dá controle mais granular do tráfego e dos fluxos de trabalho

---

## 6. Segurança de aplicações em nuvem

Mesmos conceitos da segurança de aplicações do módulo de defesa do sistema e da rede.

### Desenvolvimento

- **Desenvolvimento e teste:** ambiente menos restritivo, com controle de versão e sandbox. O QA acha falhas quando é mais barato corrigir
- **Preparo (staging) e produção:** staging deve espelhar a produção pra validar as configurações de segurança antes do deploy
- **Provisionamento** (criar ou atualizar software) e **desprovisionamento** (remover). Pode ser automatizado com portal de autoatendimento

### Técnicas de codificação segura

- **Normalização:** converte a entrada pra forma mais simples pra identificar entrada maliciosa
- **Procedimento armazenado:** SQL pré-compilado no banco, reduz tráfego e dá resultado mais rápido
- **Ofuscação e camuflagem:** evitam engenharia reversa (ofuscação oculta com dados aleatórios, camuflagem troca por dados fictícios realistas)
- **Reutilização de código:** economiza tempo, mas pode trazer vulnerabilidades
- **SDKs e bibliotecas de terceiros:** aceleram o desenvolvimento, mas uma vulnerabilidade afeta muitos aplicativos

### Validação de entrada

- Controlar a entrada de dados é essencial pra integridade do banco
- Exemplo: atacante modifica o link de confirmação de newsletter (usuário, e-mail ou status) e automatiza pra inundar o banco com assinantes inválidos
- **Regras de validação:** tamanho, formato, consistência, intervalo e dígito de verificação (ex: cálculo do dígito do ISBN)

### Verificações de integridade

- Usam **função hash** (MD5, SHA-1, SHA-256, SHA-512) pra tirar um "instantâneo" dos dados e confirmar que não mudaram. **Checksum** é um exemplo
- **Controle de versão:** dois usuários não alteram o mesmo objeto ao mesmo tempo
- **Backups:** verificar a integridade antes de precisar deles
- **Autorização:** controla quem acessa e modifica (ex: arquivo somente leitura)

### Outras práticas

- **Assinatura de código:** prova a autenticidade do software e que não foi alterado desde a assinatura
- **Cookies seguros:** usar com HTTPS, impedindo a transmissão por HTTP sem criptografia

---

## 7. Segurança de dados na nuvem

### Estados dos dados

| Estado | O que é | Observação |
| --- | --- | --- |
| **Em repouso (at rest)** | Dados armazenados, que ninguém acessa, solicita ou altera no momento | Em disco local, servidor da empresa ou nuvem. É tudo que não está em trânsito nem em processamento |
| **Em trânsito (in transit)** | Dados sendo transmitidos (barramento, rede ou Internet) | Criptografia e hash são essenciais |
| **Em processamento (in process)** | Dados durante entrada inicial, modificação, cálculo ou saída | Nem em trânsito, nem em repouso |

### Criptografia

- Ciência de criar e quebrar códigos. Só o destinatário com o segredo certo consegue ler
- **Texto claro (plaintext)** → criptografia → **texto cifrado (ciphertext)**. Descriptografia reverte
- A **chave** é essencial pra criptografar e descriptografar

| Tipo | Como funciona | Exemplos |
| --- | --- | --- |
| **Simétrica** | **Mesma chave** pré-compartilhada pra criptografar e descriptografar (chave privada) | **AES:** bloco fixo de 128 bits, chave de 128, 192 ou 256 bits. Usado pelo governo dos EUA |
| **Assimétrica** | Chave de criptografia **diferente** da de descriptografia (chave pública) | RSA, Diffie-Hellman, ElGamal, ECC |

### Hashing

- Garante a **integridade**: pega dados binários e gera uma representação de **comprimento fixo** (valor de hash ou message digest)
- Também ajuda na autenticação, substituindo senhas em texto claro ou chaves

Propriedades da função hash criptográfica:

- Entrada de qualquer tamanho
- Saída de tamanho fixo
- **Unidirecional**, não dá pra reverter
- Dois valores de entrada diferentes quase nunca geram o mesmo hash

Algoritmos:

- **SHA** (desenvolvido pelo NIST). **SHA-1** foi publicado em 1994
- **MD5:** popular, mas **não é mais considerado seguro**
- **SHA-2** substituiu o SHA-1, família com SHA-224, SHA-256, SHA-384 e SHA-512. É mais forte e está substituindo o MD5

---

## 8. Proteção de VMs

VMs, como computadores físicos, precisam de patches, atualizações e antimalware. A nuvem oferece opções extras conforme a plataforma.

- **Planejar o posicionamento da sub-rede:** cada instância só com o acesso necessário ao mundo externo
- **Desativar serviços e portas desnecessários:** reduz a exposição
- **Gerenciamento de contas e políticas:** desativar contas padrão, criar contas com boas práticas (senha complexa e **privilégio mínimo**)
- **Antivírus/antimalware atualizado:** pelo SO da VM ou como serviço da plataforma
- **Firewall de host e IDS/IPS:** pelo SO da VM ou como serviço da plataforma

### Expansão de VMs (VM sprawl)

- Criar VMs na nuvem é fácil, e isso gera muitas instâncias mal gerenciadas (ex: criar várias no início de um projeto pra testar opções e deixar rodando)
- Instâncias sem monitoramento e manutenção ficam **desatualizadas e vulneráveis**
- Também consomem recursos à toa (instâncias, armazenamento e IPs públicos não atribuídos)
- Solução: políticas pra **registrar e auditar** os recursos em uso, monitorar as VMs em execução e auditar o uso real

---

## 9. Complementos e esclarecimentos

Pontos que o material do curso explica de forma confusa ou deixa de fora.

### Esclarecimentos

- **Hypervisor tipo 1 (bare metal):** roda **direto no hardware**, sem SO host por baixo (ex: VMware ESXi, Hyper-V, Xen, KVM). Quem controla as VMs é o próprio hypervisor, e não um "sistema host" como o texto sugere
- **Hypervisor tipo 2 (hospedado):** roda como aplicativo sobre um SO comum (ex: VirtualBox, VMware Workstation)
- **VM x contêiner:** a VM tem seu próprio SO completo e isolamento forte. O contêiner **compartilha o kernel do host**, é mais leve e rápido, mas o isolamento é mais fraco
- **VM escape:** ataque em que o invasor sai da VM e chega ao hypervisor ou ao host. É a base do risco de "comprometer uma VM pode comprometer o host"
- **Nuvem híbrida:** o texto original é confuso. Na prática, mistura nuvem privada e pública, geralmente com dados sensíveis na privada e o resto na pública
- **Normalização** tem dois sentidos: em **banco de dados**, organizar tabelas pra reduzir redundância. Em **validação de entrada**, converter a entrada pra uma forma canônica (ex: tratar `%2e%2e` e `..` como a mesma coisa) pra o filtro não ser enganado. O curso mistura os dois
- **Checksum x hash criptográfico:** o checksum simples detecta erro de transmissão, mas **não protege contra alteração maliciosa**. Pra isso, usa hash criptográfico (SHA-256) ou **HMAC** (hash com chave)
- **Hash não é criptografia:** não tem chave e não é reversível. Pra guardar senhas, o ideal é hash com **salt** e algoritmo lento (bcrypt, scrypt, Argon2), e não SHA puro
- **Diffie-Hellman** é um protocolo de **troca de chaves**, não de criptografia de mensagens em si
- **SHA-1:** o texto diz que foi publicado em 1994. Pelo que sei, a publicação oficial do NIST (FIPS 180-1) foi em 1995, mas não costuma cair em prova

### Simétrica x assimétrica na prática

| | Simétrica | Assimétrica |
| --- | --- | --- |
| Chaves | Uma só, compartilhada | Par: pública e privada |
| Velocidade | Rápida | Lenta |
| Uso típico | Criptografar grandes volumes de dados | Troca de chaves, assinatura digital, certificados |

Na prática os dois são combinados (**criptografia híbrida**). O **TLS**, por exemplo, usa assimétrica pra trocar a chave e simétrica (AES) pra criptografar a sessão.

### ---

## 9. Comparações rápidas pra fixar

### VM x contêiner

| | Máquina virtual | Contêiner |
| --- | --- | --- |
| O que tem | SO completo + aplicativo | Só o aplicativo e suas dependências |
| Peso | Mais pesada | Mais leve e rápida |
| Isolamento | Mais forte | Mais fraco (compartilha o SO do host) |
| Exemplo | VMs em hypervisor | Docker, gerenciado por Kubernetes |

### Hypervisor tipo 1 x tipo 2

- **Tipo 1:** roda direto no hardware (ex: VMware ESXi, Hyper-V)
- **Tipo 2:** roda como aplicativo dentro de um SO comum (ex: VirtualBox)

### Simétrica x assimétrica

| | Simétrica | Assimétrica |
| --- | --- | --- |
| Chaves | Uma só, compartilhada | Duas: pública e privada |
| Velocidade | Rápida | Lenta |
| Exemplos | AES | RSA, ECC |

### Criptografia x hash

| | Criptografia | Hash |
| --- | --- | --- |
| Usa chave | Sim | Não |
| Dá pra reverter | Sim, com a chave | Não (unidirecional) |
| Serve pra | Confidencialidade | Integridade |

### Os 3 estados dos dados

- **Em repouso:** armazenados
- **Em trânsito:** sendo transmitidos
- **Em processamento:** sendo usados ou calculados

### Quem é responsável pelo quê

- Os **dados** são sempre responsabilidade do **cliente**, em qualquer modelo
- A **infraestrutura física** é do **provedor** (CSP) em IaaS, PaaS e SaaS
- Quanto mais "pronto" o serviço (SaaS), mais o provedor cuida

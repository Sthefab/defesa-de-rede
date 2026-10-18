# Controle de Acesso

Resumo sobre tipos de controle de acesso, autenticação, autorização, auditabilidade, Zero Trust, modelos de controle de acesso, NAC, gerenciamento de contas e AAA.

---

## 1. Controles de acesso

### Controles de acesso físico

Barreiras reais pra evitar contato direto com os sistemas. Definem quem entra, onde e quando.

- Guardas, cercas, detectores de movimento, bloqueios de laptop, portas trancadas, cartões de acesso, cães de guarda, câmeras, alarmes
- **Mantrap:** sistema de entrada que escalona o fluxo de pessoas e prende visitantes indesejados

### Controles de acesso lógicos

Soluções de hardware e software pra gerenciar acesso a recursos e sistemas (identificação, autenticação, autorização e auditoria).

- Criptografia, cartões inteligentes, senhas, biometria, ACLs, protocolos, firewalls, roteadores, IDS
- **Níveis de corte (clipping levels):** limite de erros permitido antes de acionar um alerta

### Controles de acesso administrativos

Políticas e procedimentos da empresa pra aplicar o controle de acesso. Focam em práticas pessoais e de negócios.

- Políticas (declarações de intenção) e procedimentos (passos detalhados)
- Práticas de contratação e verificação de antecedentes (histórico profissional, crédito e criminal)
- Classificação de dados, treinamento em segurança e análises de desempenho

---

## 2. AAA (Autenticação, Autorização e Contabilidade)

Estrutura principal pra controlar o acesso e impedir acesso não autorizado.

| Serviço | O que faz | Exemplo |
| --- | --- | --- |
| **Autenticação** | Verifica a identidade do usuário | Usuário e senha, token, impressão digital |
| **Autorização** | Define o que o usuário pode acessar e fazer, e até quando | ACL, acesso ao banco de vendas só em horário comercial |
| **Contabilidade (Accounting)** | Registra o que o usuário faz: o que acessa, por quanto tempo e o que altera | Log de auditoria de transações |

Analogia do cartão de crédito: identifica quem pode usar, define o limite e registra o que foi comprado.

### Gerenciamento de identidade federada

- Várias empresas aceitam as mesmas credenciais pra acessar as redes de todas do grupo (login universal, ex: login social)
- **Risco:** amplia o escopo e aumenta a chance de efeito cascata num ataque
- Cuidado com os dados de identificação compartilhados com parceiros (CPF, nome, endereço)
- Proteção mais comum: vincular o login a um dispositivo autorizado

---

## 3. Métodos de autenticação

Três fatores:

- **O que você sabe:** senha, frase secreta, PIN
- **O que você tem:** cartão inteligente, chaveiro de segurança (token)
- **O que você é:** biometria

### Senhas

- Mínimo de 8 caracteres, com maiúsculas, minúsculas, números e caracteres especiais
- Não usar dados pessoais (nome, data de nascimento, endereço)
- Senhas diferentes por sistema, e um gerenciador de senhas ajuda

### Cartões inteligentes e chaveiros

- **Cartão inteligente:** cartão com microchip que processa, armazena e protege dados usando criptografia
- **Chaveiro de segurança:** muito usado em 2FA (ex: dispositivo que exibe um número usado como segundo fator)

### Biometria

- **Fisiológicas:** impressão digital, DNA, rosto, mãos, retina, ouvido
- **Comportamentais:** gestos, voz, andar, ritmo de digitação
- Exige leitor, software de conversão e banco de dados com os dados pra comparação

### Autenticação multifator (MFA)

- Usa pelo menos dois métodos de verificação (ex: senha + token, e ainda dá pra somar biometria)
- Saber a senha não basta pro cibercriminoso acessar a conta
- **2FA** é um tipo de MFA com exatamente dois fatores (os termos costumam ser usados como sinônimos)
- Exemplo: saque no caixa eletrônico (cartão + PIN)

---

## 4. Autorização

- Controla o que o usuário pode fazer **depois** de autenticado
- Responde: quais privilégios de leitura, cópia, edição, criação e exclusão ele tem?
- É automática, sem etapas extras pro usuário
- **Política de associação baseada em grupo:** autorização por grupo (ex: só TI e nível sênior entram na sala de servidores)
- **Política de nível de autoridade:** permissões pela posição do funcionário na organização

---

## 5. Auditabilidade (accountability)

- Rastreia uma ação até a pessoa ou processo que a fez
- Coleta e reporta dados de uso (horário de login, sucesso ou falha, recursos acessados), úteis pra auditoria, cobrança e investigação
- Falhas de login podem indicar tentativa de invasão
- Depende de tecnologias, políticas, procedimentos e educação. Logs fornecem os detalhes
- Retenção de dados, eliminação de mídia e conformidade com leis geram auditabilidade

---

## 6. Conceitos de controle de acesso

### Zero Trust

- Princípio: **"Nunca confie, sempre verifique"**
- Qualquer ponto onde há decisão de acesso é um perímetro, e quem passou antes não é confiável pra outra área até autenticar
- Ajuda a impedir acesso não autorizado, conter violações e reduzir **movimento lateral**

Três pilares:

| Pilar | Foco |
| --- | --- |
| **Força de trabalho** | Pessoas (funcionários, prestadores, parceiros) e seus dispositivos acessando aplicativos |
| **Cargas de trabalho** | Aplicativos na nuvem, data centers e ambientes virtualizados (API, microsserviço, contêiner acessando banco) |
| **Local de trabalho** | Todos os dispositivos conectados à rede, incluindo IoT, servidores, impressoras, câmeras, HVAC e sistemas industriais |

### Modelos de controle de acesso

| Modelo | Como funciona |
| --- | --- |
| **DAC** (discricionário) | Menos restritivo. O dono dos dados decide quem acessa (usa ACLs e permissões) |
| **MAC** (obrigatório) | Mais rigoroso, usado em aplicações militares e críticas. Rótulos de segurança nas informações e autorização por nível |
| **RBAC** (baseado em funções) | Acesso pela função e responsabilidade do indivíduo. É um tipo de controle **não discricionário** |
| **ABAC** (baseado em atributos) | Atributos do objeto, do sujeito e do ambiente (ex: hora do dia) |
| **Baseado em regras** | Equipe de segurança define regras (IPs permitidos ou negados, protocolos, condições). Usuários não alteram as regras |
| **TAC** (baseado em tempo) | Acesso conforme hora e dia |

- **Privilégio mínimo:** usuário recebe só o acesso necessário pra função
- **Escalação de privilégios:** exploração de vulnerabilidades pra obter privilégios maiores que os devidos

### NAC (Network Access Control)

Aplica políticas da organização sobre pessoas e dispositivos que tentam acessar a rede.

- Aplicação rápida de políticas pra diferentes condições
- Reconhece e cria perfis de usuários e dispositivos
- Acesso seguro pra convidados (portais de registro)
- Avalia conformidade do dispositivo antes de liberar (por tipo de usuário, dispositivo e SO)
- Bloqueia, isola ou repara dispositivos fora de conformidade
- Essencial com BYOD e IoT, e é componente de arquitetura Zero Trust

---

## 7. Gerenciamento de contas

### Tipos de conta

- Não compartilhar contas de usuário, administrador ou aplicativo privilegiado
- Conta de administrador só pra administrar (nada de navegar ou abrir e-mail com ela)
- Substituir ou desativar contas padrão (convidado, administrador) e exigir senha em todas

Boas práticas de ciclo de vida:

- Contratação: criar perfil de identidade, registrar dispositivos e liberar acesso (a empresa atua como **IdP**, provedor de identidade)
- Desativar contas desnecessárias e recuperar dados e apps dos dispositivos
- Privilégio mínimo e revisão periódica de acessos
- Restrições de horário e de local:
  - **Geofencing (delimitação geográfica):** aciona ação quando o usuário entra ou sai de um limite geográfico
  - **Geolocalização:** identifica o dispositivo pela localização
  - **Geotagging:** adiciona identificador de localização (ex: foto com coordenadas)

### Contas privilegiadas

Alvo preferido dos criminosos, porque têm acesso elevado e irrestrito.

- Reduzir o número dessas contas e aplicar privilégio mínimo
- Revogar acesso quando funcionário sai ou muda de função
- Eliminar contas e credenciais compartilhadas com senhas que não expiram
- Proteger o armazenamento de senhas e trocar automaticamente a cada 30 ou 60 dias
- Registrar sessões e atividades, gerar alertas pra comportamento incomum
- Desativar contas inativas
- MFA em todo acesso administrativo
- Gateway entre o usuário final e os ativos sensíveis

### Controle de acesso de arquivos (permissões)

Limitam o acesso a pastas e arquivos por indivíduo ou grupo (princípio do menor privilégio e "preciso saber").

| Permissão | O que permite |
| --- | --- |
| Controle total | Ver, alterar, excluir, criar e executar |
| Modificar | Alterar e excluir existentes, mas não criar novos |
| Ler e executar | Ver conteúdo e executar programas |
| Gravação | Criar novos e alterar existentes |
| Leitura | Ver conteúdo e abrir |

### Políticas de conta no Windows

- Com **Active Directory**, a política de segurança de domínio vale pra todos os computadores do domínio
- Sem domínio, usa a Política de Segurança Local (`secpol.msc`, exceto na edição Home)
- **Política de senhas:** exemplo de troca a cada 90 dias, mínimo de 1 dia de uso, 8 caracteres, 3 de 4 categorias e reuso só após 24 senhas únicas
- **Bloqueio de conta:** exemplo de 5 tentativas erradas bloqueiam por 30 minutos
- **Política de auditoria:** registra logins, gestão de contas, acesso a diretório e objetos, mudanças de política, uso de privilégio, rastreamento de processos e eventos do sistema

### Gerenciamento de autenticação

- **SSO:** um conjunto de credenciais pra vários aplicativos
- **OAuth:** padrão que permite usar a conta de um serviço (Google, Facebook) em terceiros
- **Cofre de senhas:** guarda credenciais, acessado com uma senha forte
- **KBA (autenticação baseada em conhecimento):** redefinição de senha por perguntas pessoais

---

## 8. HMAC e funções de hash

- **HMAC:** usa chave secreta + função de hash pra autenticar. O usuário envia identificador e HMAC, o servidor calcula o seu e compara
- VPNs com **IPsec** usam HMAC pra autenticar a origem do pacote e verificar a integridade
- Cisco usa hash pra autenticação de entidade, integridade e autenticidade (roteadores com chaves secretas, IPsec com MD5/SHA-1 em modo HMAC, checksum MD5 nas imagens de software)
- Usos de hash criptográfico: prova de autenticidade, respostas únicas em protocolos de autenticação, verificação de integridade (contratos assinados, certificados PKI)
- Preferir **SHA-256 ou superior**. Evitar **SHA-1 e MD5** (falhas de segurança)

---

## 9. Protocolos e tecnologias de autenticação

| Protocolo | Resumo |
| --- | --- |
| **EAP** | Senha do cliente enviada com hash. O servidor tem certificado, o cliente não precisa |
| **PAP** | Usuário e senha em **texto claro** |
| **CHAP** | Valida cliente remoto com hash unidirecional e verifica a identidade periodicamente durante a transmissão |
| **802.1X** | Autentica e autoriza acesso à rede por credenciais ou certificado confirmado por servidor RADIUS |
| **RADIUS** | Criptografa só a senha. Usuário, contabilidade e serviços vão em texto claro. Precisa de proteção contra ataques de repetição |
| **TACACS+** | Usa TCP e criptografa todos os dados. Melhor pra redes corporativas com controle fino de autorização |
| **Kerberos** | Autenticação mútua com criptografia forte, baseada em **tíquetes** |

### Kerberos

- Servidor guarda IDs e senhas com hash e chaves secretas compartilhadas com os servidores de serviço
- Processo em duas etapas: **tíquete de concessão de tíquete (TGT)** pelo serviço de autenticação, depois **tíquete de serviço** pra acessar o servidor específico
- Sessão pode ser criptografada e tíquetes têm carimbo de hora e expiram, então reutilizar não funciona

---

## 10. Estratégias de controle de acesso

- **MAC:** objeto tem etiqueta e usuário tem autorização. Sistema restringe pela classificação
- **DAC:** o dono do objeto decide quem acessa e com qual permissão (permissões e ACLs)
- **Por função (RBAC):** permissões vêm da função de trabalho. Funciona junto com DAC ou MAC e é boa prática pra grandes organizações
- **Baseado em regras:** ACLs com regras (ex: ninguém acessa a folha de pagamento fora do expediente ou no fim de semana). Usuários não alteram as regras. Pode combinar com outras estratégias

---

## 11. Operação AAA

- A política de segurança de rede define como cada grupo (administradores, usuários, parceiros, clientes) acessa recursos
- Alguns regulamentos exigem registro de acesso e retenção dos logs por um tempo
- Exemplos: autorização ("aluno só acessa o servidor XYZ via SSH") e accounting ("aluno acessou o servidor XYZ via SSH por 15 minutos")

### Autenticação AAA (Cisco)

- **AAA local (autônoma):** usuários e senhas armazenados no próprio dispositivo. Ideal pra redes pequenas
- **AAA baseada em servidor:** servidor central com usuários e senhas. Indicada pra redes médias e grandes. Mais escalável e gerenciável, é a implementação preferida
- Pode usar **Active Directory** ou **LDAP** pra autenticação e grupos
- Dispositivos falam com o servidor via **RADIUS** ou **TACACS+**

### TACACS+ x RADIUS

| | TACACS+ | RADIUS |
| --- | --- | --- |
| Funcionalidade | Separa autenticação, autorização e auditoria (modular) | Combina autenticação e autorização, separa auditoria |
| Padrão | Principalmente Cisco | Padrão aberto (RFC) |
| Transporte | TCP porta 49 | UDP portas 1812 e 1813 (ou 1645 e 1646) |
| CHAP | Desafio bidirecional | Desafio unidirecional |
| Confidencialidade | Criptografa todo o corpo do pacote | Criptografa só a senha |
| Personalização | Autoriza comandos de roteador por usuário ou grupo | Não autoriza comandos por usuário ou grupo |
| Auditoria | Limitada | Abrangente |

### Registros de contabilidade AAA

- Registros de todos os dispositivos vão pra repositórios centralizados, simplificando a auditoria
- Coletam horários de início e fim, comandos executados, número de pacotes e bytes
- Usar junto com autenticação dá mais segurança, e o log mostra usuário, data, hora e comando digitado. Serve pra troubleshooting e como evidência contra ações maliciosas
- Mensagem de **início** ao autenticar e mensagem de **parada** ao terminar

Tipos de contabilidade:

- **Rede:** sessões PPP, com contagem de pacotes e bytes
- **Conexão:** conexões de saída feitas pelo cliente AAA (ex: SSH)
- **EXEC:** sessões de terminal (usuário, data, horários e IP do servidor de acesso)
- **Sistema:** eventos de nível de sistema (reinicialização, contabilidade ativada ou desativada)
- **Comando:** comandos do shell EXEC por nível de privilégio, com data, hora e usuário
- **Recursos:** registros de início e parada, inclusive pra conexões que falham na autenticação

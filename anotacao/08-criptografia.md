# Criptografia

Resumo sobre confidencialidade (simétrica e assimétrica), Diffie-Hellman, ofuscação de dados, integridade e autenticidade (hash e HMAC), senhas e salting, assinaturas digitais, PKI e o impacto da criptografia no monitoramento de segurança.

---

## 1. Confidencialidade

Duas classes de criptografia garantem a confidencialidade dos dados, e a diferença está no uso das chaves:

- **Simétrica:** DES, 3DES e AES (as duas partes conhecem a mesma chave pré-compartilhada)
- **Assimétrica:** RSA e PKI (par de chaves pública e privada)

> DES é legado e não deve ser usado. 3DES deve ser evitado, se possível.

### Criptografia simétrica

- Usa a **mesma chave** (pré-compartilhada ou secreta) pra criptografar e descriptografar
- A troca da chave acontece **antes** de qualquer comunicação criptografada (analogia: Alice e Bob com chaves idênticas de um mesmo cadeado)
- Muito usada em **tráfego VPN**, porque gasta menos CPU que a assimétrica e é rápida
- Quanto maior a chave, mais tempo pra descobri-la. Chaves costumam ter de 112 a 256 bits, e o mínimo seguro é **128 bits**

**Tipos de cifra:**

| Tipo | Como funciona | Exemplos |
| --- | --- | --- |
| **Bloco** | Transforma um bloco de texto claro de tamanho fixo (64 ou 128 bits) em um bloco de texto cifrado | DES (64 bits), AES (128 bits) |
| **Fluxo** | Criptografa um byte ou bit por vez, geralmente mais rápida que a de bloco | RC4, A5 (celular GSM) |

**Algoritmos simétricos:**

| Algoritmo | Descrição |
| --- | --- |
| **DES** | Legado, chave curta, inseguro pra maioria dos usos |
| **3DES** | Repete o DES três vezes. Evitar (previsto pra ser aposentado em 2023). Se usar, com chaves de duração curta |
| **AES** | Popular e **recomendado**. Chaves de 128, 192 ou 256 bits, blocos de 128 bits |
| **SEAL** | Cifra de fluxo, chave de 160 bits, mais rápida que AES e com menos impacto na CPU |
| **RC (Rivest Ciphers)** | RC4 foi o mais usado (tráfego web), mas tem várias vulnerabilidades. **Não usar** |

### Criptografia assimétrica

- Também chamada de **chave pública**: a chave de criptografia é **diferente** da de descriptografia
- Uma não pode ser calculada a partir da outra em tempo razoável
- Cada parte tem **chave pública** e **chave privada**. Uma criptografa, a outra do par descriptografa
- Como não existe segredo compartilhado, as chaves precisam ser **bem longas**: de 512 a 4.096 bits. Maiores ou iguais a **2.048 bits** são confiáveis, e 1.024 ou menos são insuficientes
- É bem mais **lenta** que a simétrica (baseada em fatorar números enormes ou logaritmos discretos), então é usada em operações de pouco volume: **assinaturas digitais** e **troca de chaves**
- Gerenciar as chaves é mais simples, porque uma delas pode ser pública

**Protocolos que usam chave assimétrica:**

- **IKE:** componente fundamental das VPNs IPsec
- **SSL/TLS:** SSL hoje é implementado como TLS (padrão IETF)
- **SSH:** acesso remoto seguro a dispositivos
- **PGP:** privacidade e autenticação, muito usado em e-mail

**Algoritmos assimétricos:**

| Algoritmo | Chave | Descrição |
| --- | --- | --- |
| **Diffie-Hellman (DH)** | 512 a 4096 | Duas partes combinam uma chave pra criptografar mensagens. Segurança baseada em ser fácil elevar um número a uma potência, mas difícil descobrir qual potência foi usada |
| **DSS e DSA** | 512 a 1024 | DSS define o DSA pra assinaturas digitais. Criar a assinatura é rápido como no RSA, mas verificar é 10 a 40 vezes mais lento |
| **RSA** | 512 a 2048 | Baseado na dificuldade de fatorar números grandes. Primeiro algoritmo adequado pra assinar e criptografar. Muito usado em e-commerce |
| **ElGamal** | 512 a 1024 | Baseado no acordo de chaves DH. A mensagem criptografada fica com o dobro do tamanho, então só serve pra mensagens pequenas (como chaves secretas) |
| **Curva elíptica (ECC)** | 224 ou mais | Adapta outros algoritmos (DH, ElGamal). Vantagem: chaves muito menores |

### Assimétrica: o que cada combinação de chaves garante

| Objetivo | Fórmula | Como funciona |
| --- | --- | --- |
| **Confidencialidade** | Chave **pública** criptografa + chave **privada** descriptografa | Alice criptografa com a chave pública de Bob, e só Bob (dono da chave privada) consegue ler |
| **Autenticação** | Chave **privada** criptografa + chave **pública** descriptografa | Se a chave pública do Alice abre a mensagem, só Alice poderia ter criptografado, e isso confirma o remetente |
| **Integridade + autenticação + confidencialidade** | Combina os dois processos | Alice criptografa a mensagem com a chave pública de Bob e um **hash** da mensagem com a própria chave privada. Bob decifra a mensagem com a chave privada dele e confere o hash com a chave pública de Alice |

- Se a chave privada for comprometida, é preciso gerar um **novo par de chaves**

### Diffie-Hellman (DH)

- Algoritmo assimétrico que permite que dois computadores **gerem um segredo compartilhado idêntico sem se comunicarem antes**
- A chave compartilhada **nunca é trocada** diretamente, mas as duas partes chegam ao mesmo valor
- Usado em **VPN IPsec** e **SSH**

Analogia das cores:

1. Alice e Bob combinam uma cor pública (amarelo)
2. Cada um escolhe uma cor secreta (Alice: vermelho, Bob: azul)
3. Cada um mistura a cor pública com a sua secreta (Alice: laranja, Bob: verde) e **troca** o resultado
4. Cada um mistura a cor recebida com a própria cor secreta, e os dois chegam à **mesma cor final** (marrom) = a chave compartilhada

- A segurança vem do uso de números muito grandes (um DH de 1024 bits equivale a um número de 309 dígitos)
- **Grupos DH:** quanto maior o número, mais seguro e mais lento

| Grupo DH | Tamanho |
| --- | --- |
| 1 | 768 bits |
| 2 | 1024 bits |
| 5 | 1536 bits |
| 14 | 2048 bits |
| 15 | 3072 bits |
| 16 | 4096 bits |

- Os grupos **19, 20 e 24** são baseados em curva elíptica
- Como a assimétrica é lenta, o comum é criptografar o tráfego com um algoritmo **simétrico** (AES ou 3DES) e usar o DH só pra **criar as chaves** usadas por ele

---

## 2. Ofuscação de dados

### Mascaramento de dados

Substitui informações confidenciais por uma versão pública que **parece e funciona como o original**, sem precisar mudar aplicações ou armazenamento. Usado principalmente pra limitar a propagação de dados sensíveis em testes e análises, e pode ser aplicado dinamicamente se um pedido for arriscado.

| Técnica | O que faz |
| --- | --- |
| **Substituição** | Troca os dados por valores que parecem autênticos, anonimizando os registros |
| **Embaralhamento** | Cria um conjunto de substituição com os dados da mesma coluna (bom pra dados financeiros em banco de teste) |
| **Anulação** | Aplica valor nulo no campo, bloqueando totalmente a visibilidade |

### Esteganografia

Esconde dados (a mensagem) dentro de **outro arquivo** (imagem, áudio, texto). Vantagem sobre a criptografia: a mensagem secreta **não chama atenção**.

Componentes:

- **Dados integrados:** a mensagem secreta
- **Texto, imagem ou áudio de capa:** o arquivo que esconde a mensagem
- **Estego-texto, estego-imagem ou estego-áudio:** o resultado final
- **Estego-chave:** controla o processo de ocultação

**Técnica LSB (Bits Menos Significativos):**

- Um pixel é a menor unidade de cor de uma imagem, formado por **RGB** (vermelho, verde e azul)
- Em cor de 24 bits, são 3 bytes por pixel (1 por cor)
- O LSB usa **1 bit de cada componente de cor**, então cada pixel guarda 3 bits
- O olho humano não percebe a mudança. Em média, no máximo metade dos bits da imagem precisa ser alterada

**Esteganografia social:** esconde a informação à vista de todos, com uma mensagem que só o destinatário interpreta direito (ex: adolescentes em redes sociais, pessoas em países com censura que escrevem errado de propósito ou usam referências imprecisas).

**Detecção (esteganoanálise):**

- Descobrir que existe informação oculta
- Utilitários de análise de disco podem achar dados em clusters não usados
- Filtros podem capturar pacotes com informação oculta nos cabeçalhos (usando assinaturas de esteganografia)
- Comparar a imagem original com a estego-imagem em busca de padrões

---

## 3. Integridade e autenticidade

### Quatro elementos das comunicações seguras

| Elemento | O que garante | Como |
| --- | --- | --- |
| **Integridade dos dados** | A mensagem não foi alterada | SHA-2 ou SHA-3 (**evitar MD5**) |
| **Autenticação de origem** | A mensagem não é falsificada e vem de quem diz | **HMAC** |
| **Confidencialidade dos dados** | Só usuários autorizados leem a mensagem | Criptografia simétrica e assimétrica |
| **Não repúdio** | O remetente não pode negar que enviou | Assinatura exclusiva do remetente (assinaturas digitais) |

### Funções criptográficas de hash

- Função matemática **unidirecional**: fácil de calcular, quase impossível de reverter (analogia: moer café)
- Pega uma mensagem de tamanho variável e produz uma representação de **tamanho fixo**: **hash**, message digest, digest ou "impressão digital"
- Qualquer mudança nos dados muda o hash
- Serve pra detectar arquivos duplicados, mudança de versão e corrupção acidental ou intencional

Fórmula: `h = H(x)` (a função H recebe a entrada x e devolve o hash h)

Propriedades:

- Entrada de **qualquer comprimento**
- Saída de **comprimento fixo**
- H(x) é **fácil de calcular** pra qualquer x
- H(x) é **unidirecional** (não reversível)
- H(x) é **livre de colisões** (entradas diferentes geram hashes diferentes)

### Algoritmos de hash

| Algoritmo | Digest | Situação |
| --- | --- | --- |
| **MD5** | 128 bits | Legado, inseguro. Evitar |
| **SHA-1** | 160 bits | Desenvolvido pela NSA em 1995. Tem falhas conhecidas, é antigo |
| **SHA-2** | 224, 256, 384 ou 512 bits | Usar de preferência SHA-256, SHA-384 e SHA-512 |
| **SHA-3** | 224, 256, 384 ou 512 bits | Mais novo, criado pelo NIST pra substituir o SHA-2. Usar sempre que possível |

> **Limitação do hash:** protege só contra alterações **acidentais**. Não identifica o remetente, então um invasor pode interceptar a mensagem, alterar, recalcular o hash e anexar (ataque man-in-the-middle). Por isso é preciso algo mais: o **HMAC**.

### HMAC (autenticação de origem)

- Combina uma função hash com uma **chave secreta**. A saída depende dos dados **e** da chave
- Só quem tem a chave consegue calcular o digest, o que **derrota o man-in-the-middle** e autentica a origem
- É usado em SSL, IPsec e SSH

**Como funciona:**

1. O remetente passa os dados + chave secreta pela função hash e gera o HMAC digest
2. Anexa o digest à mensagem e envia
3. O destinatário separa o digest, calcula o dele com a mesma chave e compara. Se for igual, a mensagem não foi alterada **e** a origem é autêntica

**Exemplo em roteadores Cisco (autenticação do OSPF):**

1. R1 calcula o hash da atualização de estado do link (LSU) com a chave secreta
2. Envia o hash junto com a LSU pro R2
3. R2 calcula o hash com a sua chave. Se bater, aceita a atualização. Se não, descarta

---

## 4. Usando hashes

### Verificar arquivos e mídias digitais

- A Cisco oferece **checksums MD5 e SHA** no site de download pra conferir se a imagem do IOS não foi alterada
- Em **computação forense**, o avaliador cria um hash da mídia original e uma cópia bit a bit (clone digital). Se os hashes são iguais, as cópias são idênticas. Isso estabelece a **invariabilidade** e permite analisar a cópia sem mexer no original

### Hash de senhas

- O sistema **nunca guarda a senha**, só o hash. Na autenticação, calcula o hash da senha digitada e compara
- Ninguém reverte o hash, então a senha é conhecida só pelo usuário

### Como quebrar hashes

O invasor precisa **adivinhar** a senha:

- **Ataque de dicionário:** arquivo com palavras, frases e senhas comuns já com os hashes calculados. Compara com os hashes do banco
- **Ataque de força bruta:** testa todas as combinações possíveis até certo tamanho. Consome muito tempo e processamento, mas em teoria sempre acha. Senhas longas tornam o ataque inviável na prática

### Salting

- Duas pessoas com a mesma senha teriam o mesmo hash. O **salt** é uma cadeia aleatória adicionada à senha **antes** do hash, e gera hashes diferentes pra mesma senha
- O banco guarda o **hash e o salt**. O salt **não precisa ser secreto**

Boas práticas:

- Gerar com **CSPRNG** (gerador de números pseudoaleatórios criptograficamente seguro)
- Salt **único** por senha de usuário, **nunca reutilizar**
- Tamanho do salt igual ao da saída da função hash
- Fazer o hash **no servidor** em aplicação web
- **Alongamento de chave (key stretching):** deixa cada tentativa bem lenta e atrapalha hardware potente que calcula bilhões de hashes por segundo

**Pra armazenar uma senha:**

1. Gerar um salt longo e aleatório com CSPRNG
2. Adicionar o salt no começo da senha
3. Fazer o hash com SHA-256
4. Salvar o salt e o hash no registro do usuário

**Pra validar uma senha:**

1. Buscar o salt e o hash do usuário no banco
2. Adicionar o salt à senha digitada e fazer o hash com a mesma função
3. Comparar com o hash guardado
4. Se não for igual, a senha está incorreta

### Ataques que o salting impede

| Ataque | Como funciona |
| --- | --- |
| **Tabela de pesquisa (lookup table)** | Guarda hashes de senhas pré-calculados de um dicionário, junto com a senha. Faz centenas de consultas por segundo |
| **Tabela de pesquisa reversa** | Cria uma tabela ligando cada hash do banco violado aos usuários. Calcula o hash de cada palpite e vê quais usuários têm aquela senha. Funciona bem porque muita gente repete a mesma senha |
| **Rainbow tables** | Sacrificam velocidade pra diminuir o tamanho da tabela, então guardam mais hashes no mesmo espaço |

O salting impede o ataque de dicionário e torna **inviável** o uso de lookup tables e rainbow tables.

---

## 5. Criptografia de chave pública: assinaturas digitais

Técnica matemática que dá **autenticidade, integridade e não repúdio**. Usa criptografia assimétrica e serve como prova legal de que a troca de dados aconteceu.

| Propriedade | Significado |
| --- | --- |
| **Autêntica** | Não pode ser falsificada, prova que o signatário assinou |
| **Inalterável** | Depois de assinado, o documento não pode ser alterado |
| **Não reutilizável** | A assinatura não pode ser transferida pra outro documento |
| **Não repudiada** | Equivale a um documento físico assinado |

Usos comuns:

1. **Assinatura de código:** verifica integridade e autenticidade de executáveis baixados do site de um fornecedor
2. **Certificados digitais:** funcionam como um "cartão de identificação virtual" pra autenticar a identidade e estabelecer conexão criptografada

**Algoritmos DSS (padrão de assinatura digital):**

- **DSA:** padrão original pra gerar pares de chaves e assinaturas
- **RSA:** assimétrico, muito usado pra gerar e verificar assinaturas
- **ECDSA:** variante mais nova do DSA, com eficiência computacional, assinaturas pequenas e pouca largura de banda

### Assinatura de código

O executável vai dentro de um envelope assinado digitalmente, e o usuário verifica antes de instalar. Garante que:

- O código é **autêntico** e vem do editor
- **Não foi modificado** desde que saiu do editor
- O editor publicou de fato (**não repúdio**)

O **FIPS 140-3** (governo dos EUA) especifica que software baixável da Internet deve ser assinado e verificado digitalmente. Nas propriedades de um arquivo no Windows, a aba **Assinaturas Digitais** mostra quem assinou, o algoritmo do digest (ex: sha256), a data (contrassinatura de uma autoridade de carimbo de tempo) e o caminho de certificação.

### Certificados digitais

- Equivalem a um **passaporte eletrônico**: autenticam quem envia a mensagem e podem dar confidencialidade ao receptor
- **O certificado verifica a identidade. A assinatura verifica que algo veio dessa identidade**

**Exemplo (Bob e Alice):**

1. Bob confirma o pedido e o computador cria um **hash** da confirmação
2. Criptografa o hash com a **chave privada de Bob**. Esse hash criptografado é a **assinatura digital** e vai anexada ao documento
3. Alice recebe e obtém a chave pública de Bob (do certificado)
4. Descriptografa a assinatura com a chave pública, revelando o hash enviado
5. Cria um hash do documento recebido e compara. Se for igual, o documento é autêntico, foi enviado por Bob e não mudou

---

## 6. Autoridades e o sistema de confiança PKI

### Gerenciamento de chave pública

- Em uma conexão assimétrica, os hosts trocam suas chaves públicas
- **Certificado SSL:** certificado digital que confirma a identidade de um domínio. Uma empresa terceira confiável (a CA) investiga e emite credenciais difíceis de falsificar
- O navegador verifica se o certificado do site é válido e emitido por uma CA confiável. A chave pública do site está dentro do certificado
- Analogia: carteira de motorista (o órgão de licenciamento é a CA)

**PKI (Infraestrutura de Chave Pública):** especificações, sistemas e ferramentas pra **criar, gerenciar, distribuir, usar, armazenar e revogar** certificados digitais.

**CA (Autoridade de Certificação):** organização que cria certificados digitais vinculando uma chave pública a uma identidade confirmada (site ou pessoa).

- Exemplos de CAs: IdenTrust, DigiCert, Sectigo, GlobalSign, GoDaddy (pagas)
- **Let's Encrypt:** CA sem fins lucrativos que emite certificados **gratuitos**

### Elementos da PKI

1. **Certificados PKI:** têm a chave pública da entidade, a finalidade, a CA que emitiu, o período de validade e o algoritmo da assinatura
2. **Armazenamento de certificados:** fica no computador local e guarda certificados emitidos e chaves privadas
3. **CA:** terceiro confiável que emite certificados depois de verificar a identidade e assina com a **chave privada** dela
4. **Banco de dados de certificados:** guarda todos os certificados aprovados pela CA

Fluxo: Bob recebe o certificado da CA e o usa ao se comunicar. Quando Alice recebe, ela consulta a CA confiável pra validar a identidade de Bob.

> Nem todo certificado vem direto da CA. A **autoridade de registro (RA)** é uma CA subordinada, certificada por uma CA raiz pra emitir certificados de usos específicos.

### Sistema de autoridades

- Fornecedores: VeriSign (Symantec), Comodo, GoDaddy, GlobalSign, DigiCert
- Empresas também podem ter **PKI privada** (Microsoft Server ou OpenSSL). Nesse caso a empresa é a própria CA, e dá pra usar pra autenticar funcionários na rede
- Algumas chaves públicas de CAs já vêm **pré-carregadas** nos navegadores, e certificados assinados por elas são confiáveis automaticamente

**Classes de certificado:** quanto maior a classe, mais rigorosa a verificação da identidade e mais confiável o certificado.

| Classe | Uso |
| --- | --- |
| 0 | Testes, sem verificações |
| 1 | Indivíduos, com verificação de e-mail (autenticação fraca) |
| 2 | Organizações que precisam de prova de identidade |
| 3 | Servidores e assinatura de software. Verificação independente de identidade e autoridade. O titular prova identidade pessoalmente, com pelo menos 2 documentos oficiais |
| 4 | Transações comerciais online entre empresas |
| 5 | Organizações privadas ou segurança do governo |

### Topologias de confiança PKI

| Topologia | Como funciona | Prós e contras |
| --- | --- | --- |
| **Raiz única** | Uma única CA raiz emite todos os certificados | Simples, mas difícil de escalar e é um **ponto único de falha** |
| **CA com certificação cruzada** | Modelo ponto a ponto, em que CAs estabelecem confiança entre si certificando uns aos outros | Dá redundância e elimina o ponto único de falha |
| **CA hierárquica** | A CA raiz emite certificados pra usuários e pra **CAs subordinadas** (subCAs), que atendem unidades de negócio, domínios ou comunidades | Mais escalável e gerenciável, funciona bem em grandes organizações, mas pode ser difícil rastrear a cadeia de assinatura |

Dá pra combinar hierárquica + certificação cruzada numa topologia **híbrida**.

### Interoperabilidade e X.509

- Muitos fornecedores criaram soluções proprietárias, o que gera problemas de interoperabilidade com serviços de suporte como **LDAP** e diretórios **X.500** (consultam um serviço de diretório, como o Active Directory, pra verificar usuário e senha)
- O IETF publicou o framework **X.509** (RFC 2527). O **X.509 v3** define o **formato do certificado digital**

Aplicações do X.509 v3:

1. **SSL/TLS:** autenticação de sites e certificados de cliente HTTPS. É a autenticação por certificado mais usada
2. **IPsec:** VPNs com autenticação RSA no IKE
3. **S/MIME:** proteção de e-mail
4. **EAP-TLS:** switches Cisco autenticam dispositivos em portas LAN com 802.1X

### Inscrição, autenticação e revogação

- O primeiro passo é obter com segurança a **chave pública da CA** (certificado **autoassinado**). Só uma **CA raiz** pode emitir certificado autoassinado reconhecido pelas outras CAs da PKI
- Navegadores já vêm com certificados raiz de CAs públicas pré-instalados
- **Inscrição:** o host pede um certificado de identidade à CA. Os certificados da CA são recuperados **dentro da rede (in-band)** e a autenticação é feita **fora de banda (OOB)**, por exemplo por **telefone**, pra confirmar a impressão digital do certificado da CA
- Depois disso, a autenticação não exige mais a presença do servidor da CA, e cada usuário troca seus certificados com as chaves públicas

**Revogação:** um certificado pode ser revogado se a chave foi comprometida ou se não é mais necessário.

| Método | Como funciona |
| --- | --- |
| **CRL (lista de revogação de certificados)** | Lista de números de série de certificados revogados. As entidades PKI consultam o repositório regularmente pra pegar a CRL atual |
| **OCSP (protocolo de status de certificados online)** | Consulta um servidor OCSP pelo status de revogação de um certificado X.509. As informações de revogação vão na hora pra um banco de dados online |

---

## 7. Aplicações e impactos da criptografia

### Aplicações da PKI

- Autenticação entre pares com certificado SSL/TLS
- VPNs IPsec
- Tráfego web HTTPS
- Controle de acesso à rede com 802.1X
- E-mail seguro com S/MIME
- Mensagens instantâneas seguras
- Assinatura de código (aprovar e autorizar aplicativos)
- Proteção de dados do usuário com o sistema de arquivos de criptografia (**EFS**)
- Autenticação de dois fatores com cartões inteligentes
- Proteção de dispositivos USB de armazenamento

### Transações de rede criptográficas

O tráfego SSL/TLS é um risco pras empresas, porque é criptografado e **não pode ser interceptado e monitorado pelos meios normais**. Usuários podem introduzir malware ou vazar informações por uma conexão SSL/TLS, e atores de ameaça podem usar pra violações de conformidade, vírus, perda de dados e tentativas de intrusão.

Problemas de PKI que geram **avisos de segurança** no navegador:

- **Intervalo de datas de validade:** o certificado X.509v3 tem datas "não antes" e "não depois". Fora do intervalo, o navegador avisa. Certificado expirado pode ser só descuido do administrador, mas também pode indicar algo mais grave
- **Erro de validação de assinatura:** se o navegador não valida a assinatura, não há garantia de que a chave pública seja autêntica. Falha, por exemplo, quando o certificado raiz da hierarquia da CA não está no armazenamento do navegador

**Conjunto de cifras (cipher suite):** SSL/TLS é modular e extensível. Os componentes do conjunto de cifras são o **algoritmo MAC**, o **algoritmo de criptografia**, o **algoritmo de troca de chaves** e o **algoritmo de autenticação**. Dá pra trocar esses componentes sem substituir o protocolo inteiro, e isso ajuda quando a criptoanálise encontra falhas num algoritmo. Quando as versões mudam, o número de versão do SSL/TLS também muda.

### Criptografia e monitoramento de segurança

- Monitorar rede é mais difícil com pacotes criptografados. Em **VPN site a site**, o IPS deve ficar posicionado onde consiga ver o tráfego **não criptografado**
- O **HTTPS** é criptografado de ponta a ponta (TLS/SSL), então não dá pra "espiar" o tráfego do usuário facilmente

O que o analista de segurança pode fazer:

- Configurar regras pra distinguir tráfego SSL de não SSL, e HTTPS de não HTTPS
- Validar certificados de servidor usando **CRLs e OCSP**
- Usar proteção antimalware e filtragem de URL em conteúdo HTTPS
- Usar um **Cisco SSL Appliance** pra descriptografar o tráfego SSL e enviar a dispositivos IPS, pra identificar riscos escondidos pelo SSL

Duas formas de a criptografia afetar investigações:

1. **Ataques ao próprio algoritmo:** se o algoritmo for quebrado e o invasor obtiver as chaves, tudo que ele capturou e que estava criptografado pode ser lido
2. **Dados escondidos pela criptografia:** ex: tráfego de comando e controle (C2) criptografado com TLS/SSL que o firewall não vê, permitindo que o invasor infecte mais computadores e crie uma **botnet**. Pra detectar, é preciso descriptografar e comparar com assinaturas de ataque conhecidas, ou identificar tráfego TLS/SSL anômalo (difícil e demorado)

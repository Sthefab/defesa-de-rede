# Compreendendo a Defesa

Resumo sobre defesa em profundidade, gerenciamento de operações de segurança e políticas/regulamentos de segurança.

---

## 1. Defesa em profundidade

### Ativos, vulnerabilidades e ameaças

- **Ativos:** tudo que tem valor pra organização e precisa ser protegido (servidores, dispositivos de infraestrutura, dispositivos finais e, o maior de todos, os dados)
- **Vulnerabilidades:** fraqueza em um sistema ou no design dele que pode ser explorada por um agente de ameaça
- **Ameaças:** qualquer perigo potencial para um ativo

### Identificação de ativos

- Todos os dispositivos e informações da organização são ativos e formam a **superfície de ataque**
- Precisam ser inventariados e avaliados pelo nível de proteção necessário
- **Gerenciamento de ativos:** inventário de tudo + políticas e procedimentos para proteger
- Cada tipo de organização tem ativos e ameaças diferentes (varejo guarda cartões de crédito, engenharia guarda projetos, banco guarda dados financeiros)

### Classificação de ativos

Agrupa os recursos por características comuns. Quanto mais importante a informação, maior a proteção.

1. Definir a categoria: ativos de informação, software, físicos ou serviços
2. Definir o responsável (dono) por cada ativo de informação e software
3. Definir os critérios: confidencialidade, valor, tempo, direitos de acesso e destruição
4. Implementar um esquema de classificação uniforme

### Padronização de ativos

- Identifica quais hardwares e softwares a empresa usa e suporta
- Sem padrão fica mais difícil achar peça de reposição, exige mais conhecimento e aumenta o custo de manutenção e inventário

### Ciclo de vida dos ativos

| Estágio | O que acontece |
| --- | --- |
| Aquisição | Compra conforme a necessidade e entrada no inventário |
| Implantação | Montagem, inspeção, testes e tags/códigos de barras para rastreio |
| Utilização | Fase mais longa: monitoramento, atualizações, patches, licenças e auditorias |
| Manutenção | Prolonga a vida útil, com modificações ou atualizações |
| Eliminação | Descarte com apagamento de todos os dados e cuidado com resíduos ambientais |

### Identificação de vulnerabilidades e ameaças

Perguntas-chave:

- Quais são as possíveis vulnerabilidades do sistema?
- Quem pode querer explorá-las?
- Quais as consequências se forem exploradas?

Exemplo de ameaças a um sistema de e-banking:

- Comprometimento interno do sistema
- Roubo de dados de clientes
- Transações falsas a partir de um servidor externo
- Transações falsas com PIN ou smart card roubado
- Ataque de insider (funcionário)
- Erros de entrada de dados
- Destruição do data center

### Abordagem de defesa em profundidade

Usa **várias camadas de segurança** na borda, na rede e nos pontos de extremidade. A falha de uma camada não derruba a eficácia das outras.

- **Roteador de borda (R1):** primeira linha de defesa, com regras do que permite ou nega
- **Firewall:** segunda linha, faz filtragem adicional e rastreia o estado das conexões. Nega conexões iniciadas de fora pra dentro, mas permite as iniciadas por usuários internos
- **Roteador interno (R2):** aplica as regras finais de filtragem antes do destino

Outros dispositivos: IPS, AMP (proteção avançada contra malware), segurança de conteúdo web e e-mail, serviços de identidade, controle de acesso à rede.

### Analogias: cebola x alcachofra

- **Cebola de segurança:** o atacante precisa "descascar" camada por camada até chegar nos dados
- **Alcachofra de segurança:** com redes sem fronteiras, o atacante só precisa remover algumas "folhas" (ex: um dispositivo móvel) pra chegar ao "coração" com os dados sensíveis. Cada folha protege, mas também é um caminho de ataque

> Obs: a "Security Onion" da analogia não é a mesma coisa que o conjunto de ferramentas Security Onion.

### Estratégias de defesa em profundidade

- **Sobreposição (camadas):** várias camadas de proteção, cada uma mais difícil de superar que a anterior. Não gera um escudo impenetrável, mas reduz riscos
- **Limitação:** cada usuário tem só o acesso necessário pro trabalho (permissões de arquivo, procedimentos)
- **Diversidade:** camadas diferentes entre si, pra mesma técnica não quebrar tudo (ex: fornecedores e fatores de autenticação diferentes)
- **Ofuscação:** não revelar informações úteis pro atacante (versão do SO, tipo de equipamento, detalhes em mensagens de erro)
- **Simplicidade:** complexidade não garante segurança. A solução deve ser simples por dentro e complexa por fora

---

## 2. Gerenciamento de operações de segurança cibernética

### Gerenciamento de configurações

- Identifica, controla e audita a implementação e as mudanças na **linha de base** de um sistema
- **Linha de base:** conjunto de configurações que serve de modelo pra sistemas semelhantes
- Documentação pode incluir: mapas e diagramas de rede, convenções de nomenclatura, esquema de IPs
- **Hardening do SO:** configurar logs e auditoria, trocar nomes de conta e senhas padrão, aplicar políticas de conta e controle de acesso em nível de arquivo

### Arquivos de log

- Registram os eventos que acontecem (log de auditoria rastreia autenticações, log de acesso mostra requisições a arquivos)
- Ajudam a entender como um ataque ocorreu e se as defesas funcionaram
- O gerenciamento de logs define procedimentos para: gerar, transmitir, armazenar, analisar e descartar

### Logs de SO e de segurança de aplicações

- **Logs do SO:** requisições de clientes, respostas de servidor (ex: autenticações bem-sucedidas) e informações de uso
- **Segurança de aplicações:** softwares de segurança geram logs úteis pra auditoria, identificação de tendências e comprovação de conformidade com leis e regulamentos

### Analisadores de protocolo (packet analyzers)

Interceptam e registram o tráfego (cabeado e sem fio), mostrando os campos e o conteúdo de cada pacote. Funções:

- Registro de tráfego
- Análise de problemas de rede
- Detecção de uso indevido
- Detecção de tentativas de invasão
- Isolamento de sistemas explorados

---

## 3. Políticas, regulamentos e padrões de segurança

### Políticas de negócios

Diretrizes que a organização cria pra governar suas ações e definir o comportamento correto. Se algo viola a política, pode ter ocorrido uma violação de segurança.

| Política | Descrição |
| --- | --- |
| Da empresa | Regras de conduta e responsabilidades de funcionários e empregadores |
| De funcionários | Criadas pelo RH: salário, benefícios, horário, férias etc. |
| De segurança | Objetivos de segurança, regras de comportamento e requisitos do sistema. Documento em constante evolução |

### Política de segurança

Benefícios:

- Demonstra o compromisso da organização com a segurança
- Define as regras de comportamento esperado
- Garante consistência nas operações, aquisição de software/hardware e manutenção
- Define as consequências legais das violações
- Dá apoio da gestão ao pessoal de segurança

Políticas que podem fazer parte:

| Política | Descrição |
| --- | --- |
| Identificação e autenticação | Quem pode acessar os recursos e como a identidade é verificada |
| Senhas | Requisitos mínimos e troca regular |
| Uso Aceitável (AUP) | Aplicativos e usos de rede aceitáveis, com as consequências de violar |
| Acesso remoto | Como usuários remotos acessam a rede e o que podem acessar |
| Manutenção de rede | Procedimentos de atualização de SO de dispositivos de rede e apps de usuário final |

**AUP:** uma das mais comuns. Deve ser o mais explícita possível, cada funcionário assina e a assinatura é guardada durante todo o vínculo de emprego.

### Políticas de BYOD (Bring Your Own Device)

Permite que funcionários usem dispositivos pessoais pra acessar sistemas da empresa.

- **Benefícios:** mais produtividade, menos custo de TI, mais mobilidade, atração e retenção de talentos
- **Riscos:** vazamento de dados e mais responsabilidade pra organização

A política BYOD deve definir: objetivos do programa, quem pode participar, quais dispositivos são suportados, nível de acesso, direitos do pessoal de segurança no dispositivo, regulamentos a cumprir e salvaguardas se o dispositivo for comprometido.

Boas práticas:

| Prática | Descrição |
| --- | --- |
| Acesso com senha | Senhas únicas por dispositivo e conta |
| Controle da conectividade | Desligar Wi-Fi e Bluetooth quando não usar, conectar só em redes confiáveis |
| Manter atualizado | SO e software em dia (patches de segurança) |
| Backup | Ativar backup caso perca ou roubem o dispositivo |
| "Localizar meu dispositivo" | Serviço com apagamento remoto |
| Antivírus | Fornecer antivírus aos dispositivos aprovados |
| MDM | Software que aplica configurações de segurança em todos os dispositivos conectados |

### Conformidade regulatória e de padrões

- Existem regulamentos externos sobre segurança de rede, e profissionais de INFOSEC devem conhecer as leis e códigos de ética aplicáveis
- Muitas organizações são obrigadas a criar e implementar políticas de segurança
- Os regulamentos definem o que a organização deve fornecer e a responsabilidade se não cumprir
- Quais regulamentos valem depende do tipo de organização e dos dados que ela manipula

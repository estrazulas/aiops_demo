# Prompts — Engenharia de Prompts para DevOps/SRE

---

## 1. Sonnet vs Opus — Mesmo prompt, modelos diferentes

**Objetivo:** Mostrar que modelos diferentes geram respostas com profundidade e qualidade distintas, mesmo recebendo o mesmo prompt.

**Conceito:** Cada modelo tem capacidades diferentes. Sonnet é mais rápido e barato; Opus é mais profundo e analítico. Escolher o modelo certo para a tarefa é parte do trabalho.

**Prompt:**

```
Você é um SRE sênior com experiência em Kubernetes.

## Contexto
- Namespace: production
- Deployment: api-gateway (3 réplicas)
- Status: 2 de 3 pods em CrashLoopBackOff desde 14:32 UTC
- Último deploy: 14:30 UTC (atualização de imagem para v2.4.1)
- Pod saudável: réplica que não foi atualizada ainda (v2.3.0)

## Logs do pod api-gateway-7d4f8b6c9-xk2mn (--previous)
2026-03-24T14:35:18.112Z [INFO]  api-gateway v2.4.1 starting...
2026-03-24T14:35:18.113Z [INFO]  Loading configuration from environment variables
2026-03-24T14:35:18.114Z [INFO]  APP_ENV=production LOG_LEVEL=info PORT=8080
2026-03-24T14:35:18.115Z [INFO]  Initializing database connection pool...
2026-03-24T14:35:18.116Z [ERROR] Failed to initialize database: connection string is empty
2026-03-24T14:35:18.116Z [ERROR] Environment variable DATABASE_URL is not set
2026-03-24T14:35:18.117Z [FATAL] Cannot start without database connection. Exiting.
2026-03-24T14:35:18.117Z [INFO]  Shutdown complete. Exit code 1.

## Logs do pod api-gateway-5a3c1e7b2-tn9ws (réplica saudável)
2026-03-24T14:35:52.404Z [INFO]  Health check passed: database connected (latency: 1ms)
2026-03-24T14:36:02.405Z [INFO]  Health check passed: database connected (latency: 2ms)

## Histórico de deploy
REVISION 4: image update to v2.4.1 (14:30 UTC)

## Tarefa
Identifique a causa raiz do CrashLoopBackOff, liste as evidências encontradas nos logs e proponha um plano de correção com comandos kubectl específicos.

## Formato de resposta
JSON com campos: severity, root_cause, evidence[], action_plan[], confidence
```

---

## 2. Os 4 Blocos — Prompt sem contexto

**Objetivo:** Demonstrar que sem contexto, o modelo dá respostas genéricas e inúteis.

**Conceito:** O bloco Role define quem responde. Sem ele, o modelo tenta cobrir todos os significados possíveis e não serve para nada.

**Prompt:**

```
O que é manga?
```

---

## 3. Os 4 Blocos — Com Role (nutricionista)

**Objetivo:** Mostrar que adicionar um Role muda completamente a interpretação da mesma pergunta.

**Conceito:** Role define a perspectiva do modelo. A mesma palavra ("manga") ganha significado diferente dependendo de quem responde.

**Prompt:**

```
Você é um nutricionista especializado. O que é manga?
```

---

## 4. Os 4 Blocos — Com Role (estilista)

**Objetivo:** Reforçar que o Role define a interpretação — mesma pergunta, resposta totalmente diferente.

**Conceito:** O modelo não "sabe" o que você quer. O Role é a primeira instrução que direciona a resposta.

**Prompt:**

```
Você é um estilista de moda. O que é manga?
```

---

## 5. Os 4 Blocos — Role + Contexto

**Objetivo:** Mostrar que o Contexto personaliza a resposta para a situação específica.

**Conceito:** Role define quem responde. Contexto define para quem e em qual situação. Juntos, transformam resposta genérica em resposta útil.

**Prompt:**

```
Você é um nutricionista especializado.
Contexto: Paciente diabético tipo 2, precisa controlar índice glicêmico.
O que é manga?
```

---

## 6. Os 4 Blocos — Prompt ingênuo (só logs)

**Objetivo:** Estabelecer a linha de base — como a maioria das pessoas usa IA: joga os dados e pede para analisar.

**Conceito:** Sem Role, Contexto, Tarefa e Output, o modelo não sabe quem ele é, o que aconteceu, o que você quer e em que formato. O resultado é genérico e superficial.

**Prompt:**

```
Analise este log:

2026-03-24T14:35:18.112Z [INFO]  api-gateway v2.4.1 starting...
2026-03-24T14:35:18.113Z [INFO]  Loading configuration from environment variables
2026-03-24T14:35:18.114Z [INFO]  APP_ENV=production LOG_LEVEL=info PORT=8080
2026-03-24T14:35:18.115Z [INFO]  Initializing database connection pool...
2026-03-24T14:35:18.116Z [ERROR] Failed to initialize database: connection string is empty
2026-03-24T14:35:18.116Z [ERROR] Environment variable DATABASE_URL is not set
2026-03-24T14:35:18.117Z [FATAL] Cannot start without database connection. Exiting.
2026-03-24T14:35:18.117Z [INFO]  Shutdown complete. Exit code 1.
```

---

## 7. Os 4 Blocos — Com Role

**Objetivo:** Mostrar a primeira melhoria — adicionar quem está respondendo.

**Conceito:** O Role muda o vocabulário, as hipóteses e a profundidade da análise. Mesmo log, analisado por um "especialista".

**Prompt:**

```
Você é um SRE sênior com experiência em Kubernetes e troubleshooting de aplicações containerizadas.

Analise este log:

2026-03-24T14:35:18.112Z [INFO]  api-gateway v2.4.1 starting...
2026-03-24T14:35:18.113Z [INFO]  Loading configuration from environment variables
2026-03-24T14:35:18.114Z [INFO]  APP_ENV=production LOG_LEVEL=info PORT=8080
2026-03-24T14:35:18.115Z [INFO]  Initializing database connection pool...
2026-03-24T14:35:18.116Z [ERROR] Failed to initialize database: connection string is empty
2026-03-24T14:35:18.116Z [ERROR] Environment variable DATABASE_URL is not set
2026-03-24T14:35:18.117Z [FATAL] Cannot start without database connection. Exiting.
2026-03-24T14:35:18.117Z [INFO]  Shutdown complete. Exit code 1.
```

---

## 8. Os 4 Blocos — Role + Contexto

**Objetivo:** Mostrar que o Contexto permite correlações que antes eram impossíveis.

**Conceito:** Contexto inclui informações do ambiente: namespace, deployment, histórico de deploy, estado de outras réplicas. Com esses dados, o modelo consegue correlacionar o deploy com o início do problema.

**Prompt:**

```
Você é um SRE sênior com experiência em Kubernetes.

## Contexto
- Namespace: production
- Deployment: api-gateway (3 réplicas)
- Status: 2 de 3 pods em CrashLoopBackOff desde 14:32 UTC
- Último deploy: 14:30 UTC (atualização de imagem)
- Pod saudável: réplica que não foi atualizada ainda

## Logs do pod api-gateway-7d4f8b6c9-xk2mn (--previous)
2026-03-24T14:35:18.112Z [INFO]  api-gateway v2.4.1 starting...
2026-03-24T14:35:18.113Z [INFO]  Loading configuration from environment variables
2026-03-24T14:35:18.114Z [INFO]  APP_ENV=production LOG_LEVEL=info PORT=8080
2026-03-24T14:35:18.115Z [INFO]  Initializing database connection pool...
2026-03-24T14:35:18.116Z [ERROR] Failed to initialize database: connection string is empty
2026-03-24T14:35:18.116Z [ERROR] Environment variable DATABASE_URL is not set
2026-03-24T14:35:18.117Z [FATAL] Cannot start without database connection. Exiting.
2026-03-24T14:35:18.117Z [INFO]  Shutdown complete. Exit code 1.

## Logs do pod api-gateway-5a3c1e7b2-tn9ws (réplica saudável)
2026-03-24T14:35:52.404Z [INFO]  Health check passed: database connected (latency: 1ms)
2026-03-24T14:36:02.405Z [INFO]  Health check passed: database connected (latency: 2ms)

## Histórico de deploy
REVISION 4: image update to v2.4.1 (14:30 UTC)

Analise este cenário.
```

---

## 9. Os 4 Blocos — Prompt completo (Role + Contexto + Tarefa + Output)

**Objetivo:** Mostrar o resultado final — prompt estruturado com os 4 blocos produz diagnóstico acionável.

**Conceito:** Tarefa diz exatamente o que o modelo deve fazer. Output define o formato da resposta. Juntos com Role e Contexto, transformam uma pergunta vaga em uma ferramenta de trabalho. A qualidade da resposta é responsabilidade do prompt.

**Prompt:**

```
Você é um SRE sênior com experiência em Kubernetes.

## Contexto
- Namespace: production
- Deployment: api-gateway (3 réplicas)
- Status: 2 de 3 pods em CrashLoopBackOff desde 14:32 UTC
- Último deploy: 14:30 UTC (atualização de imagem)
- Pod saudável: réplica que não foi atualizada ainda

## Logs do pod api-gateway-7d4f8b6c9-xk2mn (--previous)
2026-03-24T14:35:18.112Z [INFO]  api-gateway v2.4.1 starting...
2026-03-24T14:35:18.113Z [INFO]  Loading configuration from environment variables
2026-03-24T14:35:18.114Z [INFO]  APP_ENV=production LOG_LEVEL=info PORT=8080
2026-03-24T14:35:18.115Z [INFO]  Initializing database connection pool...
2026-03-24T14:35:18.116Z [ERROR] Failed to initialize database: connection string is empty
2026-03-24T14:35:18.116Z [ERROR] Environment variable DATABASE_URL is not set
2026-03-24T14:35:18.117Z [FATAL] Cannot start without database connection. Exiting.
2026-03-24T14:35:18.117Z [INFO]  Shutdown complete. Exit code 1.

## Logs do pod api-gateway-5a3c1e7b2-tn9ws (réplica saudável)
2026-03-24T14:35:52.404Z [INFO]  Health check passed: database connected (latency: 1ms)
2026-03-24T14:36:02.405Z [INFO]  Health check passed: database connected (latency: 2ms)

## Histórico de deploy
REVISION 4: image update to v2.4.1 (14:30 UTC)

## Tarefa
Identifique a causa raiz do CrashLoopBackOff, liste as evidências encontradas nos logs e proponha um plano de correção com comandos kubectl específicos.

## Formato de resposta
Responda em JSON com os campos:
- severity: (critical/high/medium/low)
- root_cause: (causa raiz em uma frase)
- evidence: [lista de evidências encontradas nos logs]
- action_plan: [passos de correção com comandos kubectl]
- confidence: (porcentagem de confiança na análise)
```

---

## 10. Zero-shot — Classificação de sentimento

**Objetivo:** Mostrar que zero-shot funciona bem para tarefas simples e bem definidas.

**Conceito:** Zero-shot = prompt com instruções claras, sem nenhum exemplo de resposta. O modelo decide como executar baseado apenas nas instruções. Funciona quando a tarefa é objetiva e sem ambiguidade.

**Prompt:**

```
Classifique o sentimento da frase abaixo como positivo, negativo ou neutro.

Frase: "O atendimento foi rápido mas o produto veio com defeito"

Responda apenas com a classificação e uma justificativa de uma linha.
```

---

## 11. Zero-shot — Script de infraestrutura AWS

**Objetivo:** Demonstrar zero-shot em geração de código — funciona quando os requisitos são claros e objetivos.

**Conceito:** Zero-shot gera código funcional com instruções detalhadas. Mas se você quiser convenções específicas do seu time (nomes padronizados, tags, estrutura de módulos), zero-shot não garante — o modelo segue o padrão genérico dele.

**Prompt:**

```
Crie um script Python usando boto3 que provisione a seguinte infraestrutura na AWS:

1. Uma VPC com CIDR 10.0.0.0/16
2. Uma subnet pública (10.0.1.0/24) e uma subnet privada (10.0.2.0/24)
3. Um Internet Gateway associado à VPC
4. Uma route table para a subnet pública com rota para o Internet Gateway
5. Um Security Group que libere SSH (22) e HTTP (80) apenas para a subnet pública
6. Uma instância EC2 t3.micro na subnet pública com Amazon Linux 2023

O script deve:
- Usar a região us-east-1
- Adicionar tags Name em todos os recursos com prefixo "aiops-lab-"
- Imprimir o IP público da instância ao final
- Tratar erros com try/except
```

---

## 12. Zero-shot — Análise de logs com 4 blocos

**Objetivo:** Mostrar que zero-shot com 4 blocos funciona, mas formato e profundidade variam entre execuções.

**Conceito:** Zero-shot com boa estrutura resolve o problema, mas sem exemplos de referência, não há âncora de qualidade. Rodar 2x pode gerar respostas com profundidades diferentes. Essa inconsistência é onde few-shot resolve.

**Prompt:**

```
Você é um SRE sênior com experiência em Kubernetes.

## Contexto
- Namespace: production
- Deployment: api-gateway (3 réplicas)
- Status: 2 de 3 pods em CrashLoopBackOff desde 14:32 UTC
- Último deploy: 14:30 UTC (atualização de imagem para v2.4.1)
- Pod saudável: réplica que não foi atualizada ainda (v2.3.0)

## Logs do pod api-gateway-7d4f8b6c9-xk2mn (--previous)
2026-03-24T14:35:18.112Z [INFO]  api-gateway v2.4.1 starting...
2026-03-24T14:35:18.113Z [INFO]  Loading configuration from environment variables
2026-03-24T14:35:18.114Z [INFO]  APP_ENV=production LOG_LEVEL=info PORT=8080
2026-03-24T14:35:18.115Z [INFO]  Initializing database connection pool...
2026-03-24T14:35:18.116Z [ERROR] Failed to initialize database: connection string is empty
2026-03-24T14:35:18.116Z [ERROR] Environment variable DATABASE_URL is not set
2026-03-24T14:35:18.117Z [FATAL] Cannot start without database connection. Exiting.
2026-03-24T14:35:18.117Z [INFO]  Shutdown complete. Exit code 1.

## Logs do pod api-gateway-5a3c1e7b2-tn9ws (réplica saudável)
2026-03-24T14:35:52.404Z [INFO]  Health check passed: database connected (latency: 1ms)
2026-03-24T14:36:02.405Z [INFO]  Health check passed: database connected (latency: 2ms)

## Tarefa
Identifique a causa raiz do CrashLoopBackOff, liste as evidências e proponha correção com comandos kubectl.

## Formato de resposta
JSON com campos: severity, root_cause, evidence[], action_plan[], confidence
```

---

## 13. Zero-shot vs Few-shot — Parágrafo sobre Kubernetes

**Objetivo:** Mostrar que "tom informal e direto" é vago — cada execução interpreta diferente. Few-shot com exemplos de estilo resolve.

**Conceito:** Zero-shot diz O QUE fazer. Few-shot MOSTRA COMO fazer. Para tom pessoal, estilo de escrita e convenções, exemplos são mais eficazes que instruções.

**Zero-shot:**

```
Escreva um parágrafo introdutório para um artigo sobre Kubernetes para iniciantes. Tom informal e direto.
```

**Few-shot:**

```
Escreva um parágrafo introdutório para um artigo sobre Kubernetes para iniciantes, seguindo o tom e estilo dos exemplos abaixo.

## Exemplos do meu estilo de escrita

Exemplo 1 (artigo sobre Docker):
"Docker mudou a forma como a gente trabalha. Antes, era aquele inferno de 'na minha máquina funciona'. Agora você empacota tudo num container e pronto — roda igual em qualquer lugar. Mas calma, não é mágica. Tem coisa que você precisa entender antes de sair containerizando tudo."

Exemplo 2 (artigo sobre CI/CD):
"Pipeline de CI/CD é daquelas coisas que todo mundo fala que tem, mas pouca gente tem de verdade. Ter um Jenkins rodando build não é CI/CD — é automação de build. CI/CD de verdade é quando você confia no processo o suficiente pra fazer deploy na sexta-feira sem medo."

## Agora escreva
Parágrafo introdutório sobre Kubernetes para iniciantes, mesmo tom dos exemplos acima.
```

---

## 14. Zero-shot vs Few-shot — Mensagens de commit

**Objetivo:** Mostrar que cada dev escreve commit diferente — few-shot com exemplos do repositório garante consistência.

**Conceito:** Few-shot transfere padrões implícitos que instruções não conseguem descrever. O modelo replica o formato, prefixo e nível de detalhe dos exemplos fornecidos.

**Zero-shot:**

```
Gere uma mensagem de commit para as seguintes mudanças:
- Adicionado timeout de 30s nas chamadas HTTP para o serviço de pagamento
- Adicionado retry com backoff exponencial (3 tentativas)
- Adicionado circuit breaker que abre após 5 falhas consecutivas
```

**Few-shot:**

```
Gere uma mensagem de commit para as mudanças abaixo, seguindo o padrão dos exemplos.

## Exemplos de commits do repositório
- fix: corrige timeout na conexão com Redis quando pool está cheio
- feat: adiciona retry com backoff exponencial no client HTTP
- refactor: extrai validação de input para middleware dedicado
- feat: implementa health check com verificação de dependências externas
- fix: resolve race condition no shutdown graceful do worker

## Mudanças para commitar
- Adicionado timeout de 30s nas chamadas HTTP para o serviço de pagamento
- Adicionado retry com backoff exponencial (3 tentativas)
- Adicionado circuit breaker que abre após 5 falhas consecutivas
```

---

## 15. Few-shot — Análise de logs com exemplos de análises anteriores

**Objetivo:** Mostrar que exemplos de análises resolvidas ancoram formato, profundidade e raciocínio. O modelo replica o padrão.

**Conceito:** Few-shot com exemplos do próprio ambiente. Cada análise bem feita vira exemplo para as próximas — o prompt melhora com o tempo. 2-3 exemplos estabilizam o padrão; mais de 3 tem rendimento marginal decrescente.

**Prompt:**

```
Você é um SRE sênior com experiência em Kubernetes.

## Exemplos de análises anteriores

### Exemplo 1
Input: Pods do serviço redis-cache com timeout intermitente. 15 pods compartilhando pool de 10 conexões. Erros concentrados entre 14:00-14:15.
Análise: Correlacionei timestamps dos erros com pico de conexões simultâneas. Pool configurado para 10 conexões, 15 pods competindo. Pool esgotado em horário de pico.
Output: {"severity": "high", "root_cause": "Redis connection pool exhausted during peak traffic", "evidence": ["timeout errors concentrated 14:00-14:15", "15 pods sharing pool of 10 connections", "no errors outside peak hours"], "action_plan": ["kubectl edit configmap redis-config -n production # increase pool to 20", "Add connection timeout of 5s in application config", "kubectl top pods -n production # monitor pool utilization"], "confidence": "85%"}

### Exemplo 2
Input: Pod image-processor reiniciando a cada ~45min com OOMKilled. Memory limit 512Mi. Consumo de memória crescente linear após cada batch de imagens.
Análise: Pod reiniciando a cada ~45min. Consumo de memória crescente linear. Memory leak no processamento de imagens — buffer não liberado após resize.
Output: {"severity": "critical", "root_cause": "Memory leak in image processing - buffer not released after resize", "evidence": ["linear memory growth pattern", "OOMKilled every ~45min", "heap dump shows ImageBuffer accumulation"], "action_plan": ["Fix buffer.release() after resize in src/image/processor.go", "kubectl set resources deployment/image-processor --limits=memory=1Gi -n production # temporary mitigation", "Add memory alert at 80% of limit"], "confidence": "90%"}

## Caso atual

### Contexto
- Namespace: production
- Deployment: api-gateway (3 réplicas)
- Status: 2 de 3 pods em CrashLoopBackOff desde 14:32 UTC
- Último deploy: 14:30 UTC (atualização de imagem para v2.4.1)
- Pod saudável: réplica que não foi atualizada ainda (v2.3.0)

### Logs do pod api-gateway-7d4f8b6c9-xk2mn (--previous)
2026-03-24T14:35:18.112Z [INFO]  api-gateway v2.4.1 starting...
2026-03-24T14:35:18.113Z [INFO]  Loading configuration from environment variables
2026-03-24T14:35:18.114Z [INFO]  APP_ENV=production LOG_LEVEL=info PORT=8080
2026-03-24T14:35:18.115Z [INFO]  Initializing database connection pool...
2026-03-24T14:35:18.116Z [ERROR] Failed to initialize database: connection string is empty
2026-03-24T14:35:18.116Z [ERROR] Environment variable DATABASE_URL is not set
2026-03-24T14:35:18.117Z [FATAL] Cannot start without database connection. Exiting.
2026-03-24T14:35:18.117Z [INFO]  Shutdown complete. Exit code 1.

### Logs do pod api-gateway-5a3c1e7b2-tn9ws (réplica saudável)
2026-03-24T14:35:52.404Z [INFO]  Health check passed: database connected (latency: 1ms)
2026-03-24T14:36:02.405Z [INFO]  Health check passed: database connected (latency: 2ms)

### Tarefa
Identifique a causa raiz do CrashLoopBackOff, liste as evidências e proponha correção com comandos kubectl.

### Formato de resposta
JSON com campos: severity, root_cause, evidence[], action_plan[], confidence
```

---

## 16. Chain-of-Thought — Lista de churrasco

**Objetivo:** Mostrar que sem CoT o modelo chuta quantidades arbitrárias; com CoT, justifica cada item com cálculo.

**Conceito:** Chain-of-thought força o modelo a pensar passo a passo antes de responder. A instrução pode ser simples ("Pense passo a passo") ou com etapas explícitas. O raciocínio intermediário fica visível e auditável.

**Sem CoT:**

```
Monte uma lista de compras para um churrasco para 8 pessoas.
```

**Com CoT:**

```
Monte uma lista de compras para um churrasco para 8 pessoas.

Pense passo a passo:
1. Estime o consumo por pessoa de cada tipo de carne (considere 400g/pessoa como referência)
2. Calcule a quantidade total e divida entre os tipos de carne
3. Adicione acompanhamentos proporcionais ao número de pessoas
4. Inclua bebidas considerando 3 horas de evento
5. Não esqueça itens de preparo (carvão, gelo, etc.)

Para cada item, mostre o cálculo que justifica a quantidade.
```

---

## 17. Chain-of-Thought — Análise de logs sem CoT

**Objetivo:** Estabelecer a linha de base — sem forçar raciocínio, o modelo responde rápido e superficialmente.

**Conceito:** Sem CoT, o modelo pula direto para uma conclusão. Com problemas complexos (erros intermitentes, padrões temporais), isso significa perder correlações importantes.

**Prompt:**

```
Você é um SRE sênior.

## Logs do serviço order-service (última hora)
2026-03-24T14:00:12.331Z [INFO]  Request processed successfully (order #8841)
2026-03-24T14:00:14.102Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:00:14.203Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:00:14.304Z [ERROR] HTTP 500 returned to client (order #8842)
2026-03-24T14:00:14.410Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:00:14.998Z [ERROR] HTTP 500 returned to client (order #8843)
2026-03-24T14:00:15.102Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:00:15.503Z [ERROR] HTTP 500 returned to client (order #8844)
2026-03-24T14:00:18.221Z [INFO]  Request processed successfully (order #8845)
2026-03-24T14:00:22.105Z [INFO]  Request processed successfully (order #8846)
2026-03-24T14:15:11.998Z [INFO]  Request processed successfully (order #8901)
2026-03-24T14:15:14.087Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:15:14.190Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:15:14.288Z [ERROR] HTTP 500 returned to client (order #8902)
2026-03-24T14:15:14.390Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:15:15.001Z [ERROR] HTTP 500 returned to client (order #8903)
2026-03-24T14:15:18.112Z [INFO]  Request processed successfully (order #8904)
2026-03-24T14:30:14.050Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:30:14.155Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:30:14.260Z [ERROR] HTTP 500 returned to client (order #8955)
2026-03-24T14:30:14.370Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:30:15.100Z [ERROR] HTTP 500 returned to client (order #8956)
2026-03-24T14:30:18.330Z [INFO]  Request processed successfully (order #8957)
2026-03-24T14:45:14.020Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:45:14.130Z [ERROR] HTTP 500 returned to client (order #8999)
2026-03-24T14:45:14.240Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:45:15.005Z [ERROR] HTTP 500 returned to client (order #9000)
2026-03-24T14:45:18.200Z [INFO]  Request processed successfully (order #9001)

## Informação adicional
- Connection pool configurado para 10 conexões
- 8 pods do order-service rodando
- Cron job "report-generator" roda a cada 15 minutos e faz queries pesadas no banco

Qual a causa raiz?
```

---

## 18. Chain-of-Thought — Análise de logs com CoT

**Objetivo:** Mostrar que forçar raciocínio passo a passo revela a correlação temporal que a resposta direta omitia.

**Conceito:** CoT com etapas explícitas: listar erros, identificar padrões, correlacionar dados, formular hipóteses. O modelo descobre que erros a cada 15min coincidem com o cron job e que o pool de 10 conexões é insuficiente para 8 pods + queries pesadas.

**Prompt:**

```
Você é um SRE sênior.

## Logs do serviço order-service (última hora)
2026-03-24T14:00:12.331Z [INFO]  Request processed successfully (order #8841)
2026-03-24T14:00:14.102Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:00:14.203Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:00:14.304Z [ERROR] HTTP 500 returned to client (order #8842)
2026-03-24T14:00:14.410Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:00:14.998Z [ERROR] HTTP 500 returned to client (order #8843)
2026-03-24T14:00:15.102Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:00:15.503Z [ERROR] HTTP 500 returned to client (order #8844)
2026-03-24T14:00:18.221Z [INFO]  Request processed successfully (order #8845)
2026-03-24T14:00:22.105Z [INFO]  Request processed successfully (order #8846)
2026-03-24T14:15:11.998Z [INFO]  Request processed successfully (order #8901)
2026-03-24T14:15:14.087Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:15:14.190Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:15:14.288Z [ERROR] HTTP 500 returned to client (order #8902)
2026-03-24T14:15:14.390Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:15:15.001Z [ERROR] HTTP 500 returned to client (order #8903)
2026-03-24T14:15:18.112Z [INFO]  Request processed successfully (order #8904)
2026-03-24T14:30:14.050Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:30:14.155Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:30:14.260Z [ERROR] HTTP 500 returned to client (order #8955)
2026-03-24T14:30:14.370Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:30:15.100Z [ERROR] HTTP 500 returned to client (order #8956)
2026-03-24T14:30:18.330Z [INFO]  Request processed successfully (order #8957)
2026-03-24T14:45:14.020Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:45:14.130Z [ERROR] HTTP 500 returned to client (order #8999)
2026-03-24T14:45:14.240Z [ERROR] database connection failed: dial tcp 10.0.2.15:5432: connect: connection refused
2026-03-24T14:45:15.005Z [ERROR] HTTP 500 returned to client (order #9000)
2026-03-24T14:45:18.200Z [INFO]  Request processed successfully (order #9001)

## Informação adicional
- Connection pool configurado para 10 conexões
- 8 pods do order-service rodando
- Cron job "report-generator" roda a cada 15 minutos e faz queries pesadas no banco

Analise passo a passo:
1. Liste todos os erros com timestamps
2. Identifique padrões temporais — os erros são constantes ou em rajadas?
3. Correlacione com outras informações disponíveis (pool, pods, cron job)
4. Formule hipóteses ordenadas por probabilidade
5. Para cada hipótese, indique como validar
6. Conclusão com causa mais provável e correção recomendada
```

---

## 19. Chain-of-Thought — Postmortem a partir da análise

**Objetivo:** Mostrar que CoT encadeado (análise + documento) produz postmortems contextualizados, não templates genéricos.

**Conceito:** A saída de um CoT alimenta o próximo. A investigação com raciocínio passo a passo gera dados que o postmortem usa diretamente. Um alimenta o outro — o ganho é multiplicado.

**Prompt (enviar na mesma conversa, após o prompt 18):**

```
Com base na análise acima, gere um documento de postmortem completo para este incidente.

Pense passo a passo:
1. Use a causa raiz identificada (cron job "report-generator" esgotando o connection pool) como ponto de partida
2. Reconstrua a timeline do incidente com base nos timestamps dos logs
3. Separe o trigger (cron job) dos amplificadores (pool subdimensionado, sem circuit breaker)
4. Para cada ação corretiva, classifique como curto prazo (< 1 semana) ou estrutural
5. Identifique o que os alertas atuais pegariam e o que não pegariam nesse cenário
```

---

## 20. ReAct — Roteiro de viagem sem ReAct

**Objetivo:** Mostrar que sem pesquisa ativa, o modelo gera roteiro genérico de blog.

**Conceito:** Sem ReAct, o modelo responde com o que já sabe (dados de treinamento) — pode estar desatualizado ou genérico. ReAct permite que o modelo pesquise antes de responder.

**Prompt:**

```
Monte um roteiro de 5 dias em Portugal para um casal, orçamento moderado, em junho.
```

---

## 21. ReAct — Roteiro de viagem com ReAct

**Objetivo:** Mostrar que com o padrão Thought/Action/Observation, o modelo pesquisa ativamente e justifica cada escolha com dados reais.

**Conceito:** ReAct = Raciocínio + Ação em loop. O modelo pensa, decide que precisa de mais informação, busca, observa o resultado, e raciocina de novo. Reproduz o processo real de investigação humana.

**Prompt:**

```
Monte um roteiro de 5 dias em Portugal para um casal, orçamento moderado, em junho.

Siga o padrão de investigação abaixo para cada decisão:
- **Thought**: o que você sabe e o que precisa descobrir antes de decidir
- **Action**: pesquise na internet a informação que precisa (clima, preços, distâncias, horários)
- **Observation**: registre o que encontrou
- Repita até ter informação suficiente para montar o roteiro

Não assuma nada — pesquise antes de recomendar. Para cada escolha (cidade, passeio, restaurante), mostre o raciocínio e a pesquisa que justificou a decisão.
```

---

## 22. ReAct — Diagnóstico de cgroups sem ReAct

**Objetivo:** Mostrar que sem pesquisa, o modelo chuta soluções genéricas para erros que nunca viu.

**Conceito:** O limite do conhecimento do modelo. Erros raros ou recentes (como incompatibilidade cgroups v1/v2) podem não estar bem representados nos dados de treinamento. Sem pesquisa ativa, o modelo responde com o que parece plausível — mas erra.

**Prompt:**

```
Você é um SRE sênior.

Vários pods de uma aplicação em produção entraram em CrashLoopBackOff depois de um deploy. O erro nos logs do pod é:

Error: failed to create containerd container: failed to create shim task:
OCI runtime create failed: runc create failed: unable to start container process:
error during container init: error setting cgroup config for procHooks process:
failed to write "200000": write /sys/fs/cgroup/cpu/kubepods/burstable/.../cpu.cfs_quota_us:
permission denied: unknown

Nunca vi esse erro antes. Qual a causa raiz e como corrigir?
```

---

## 23. ReAct — Diagnóstico de cgroups com ReAct

**Objetivo:** Mostrar que com pesquisa ativa, o modelo descobre que o path `/sys/fs/cgroup/cpu/...` (cgroups v1) não existe num node com cgroups v2.

**Conceito:** ReAct aplicado a troubleshooting real. O modelo pesquisa issues no GitHub, documentação oficial, e constrói o diagnóstico iterativamente. Este é o padrão que agentes usam — o que será implementado nos módulos seguintes.

**Prompt:**

```
Você é um SRE sênior investigando um incidente em produção.

## Padrão de investigação
Siga rigorosamente o formato abaixo para cada ciclo:
- **Thought**: o que você sabe até agora e o que precisa descobrir
- **Action**: pesquise na internet (docs oficiais, issues do GitHub, Stack Overflow)
- **Observation**: registre o que encontrou
- Repita até chegar na causa raiz

Não pule etapas. Pesquise antes de concluir.

## Cenário
Vários pods entraram em CrashLoopBackOff após um deploy. O erro nos logs é:

Error: failed to create containerd container: failed to create shim task:
OCI runtime create failed: runc create failed: unable to start container process:
error during container init: error setting cgroup config for procHooks process:
failed to write "200000": write /sys/fs/cgroup/cpu/kubepods/burstable/.../cpu.cfs_quota_us:
permission denied: unknown

## Dados disponíveis do cluster
- Kubernetes 1.28, containerd 1.7
- Node OS: Ubuntu 22.04 LTS (migrado recentemente de CentOS 7)
- O deploy não alterou a aplicação — apenas atualizou a imagem base do container
- Outros pods no mesmo node estão rodando normalmente (foram criados antes da migração)
- cat /sys/fs/cgroup/cgroup.controllers retorna: "cpuset cpu io memory hugetlb pids" (cgroups v2)

Comece sua investigação.
```

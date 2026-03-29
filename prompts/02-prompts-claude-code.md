# Prompts — Claude Code, MCP e Operações Kubernetes com IA

---

## 1. Explorar o repositório

**Objetivo:** Mostrar que o Claude Code analisa a estrutura do projeto sozinho, sem precisar explicar a stack.

**Conceito:** O Claude Code lê arquivos, diretórios, git history e configurações sob demanda. Quando você pede pra explicar o projeto, ele identifica stack, padrões e propósito automaticamente.

**Prompt:**

```
Explique a estrutura deste projeto. Quais são os principais diretórios e arquivos?
```

---

## 2. Análise de arquivo específico

**Objetivo:** Mostrar análise focada com sugestões concretas e contextualizadas ao projeto.

**Conceito:** O Claude Code não dá sugestões genéricas — ele lê o arquivo, entende o contexto do projeto e sugere melhorias específicas. As recomendações se aplicam ao que existe, não ao que poderia existir.

**Prompt:**

```
Analise o Dockerfile deste projeto e sugira melhorias de segurança e performance.
```

---

## 3. Criar arquivo com contexto do projeto

**Objetivo:** Demonstrar o ciclo de interação: a IA propõe, pede confirmação, e só executa com aprovação.

**Conceito:** O Claude Code considera a stack identificada na análise anterior para gerar o arquivo. Antes de criar ou modificar qualquer coisa, ele mostra o que vai fazer e pede confirmação.

**Prompt:**

```
Crie um arquivo .gitignore adequado para este projeto, considerando a stack que você identificou.
```

---

## 4. Gerar Dockerfile — Sem dar contexto

**Objetivo:** Mostrar que o Claude Code analisa o projeto antes de gerar — ele não chuta, ele lê package.json, código-fonte, identifica stack, porta e comando de start.

**Conceito:** Iteração incremental. O primeiro resultado vem da análise automática do projeto. Você avalia o que ele acertou e o que pode melhorar, depois direciona com seu conhecimento.

**Prompt:**

```
Analise o projeto e crie um Dockerfile adequado para essa aplicação.
```

---

## 5. Iterar Dockerfile com boas práticas

**Objetivo:** Mostrar que feedback específico gera resultado previsível — a diferença entre "melhore isso" e instruções claras.

**Conceito:** Iteração incremental com direção. O primeiro resultado veio da análise automática. O segundo vem do seu conhecimento guiando a IA. Quanto mais específico o pedido, mais previsível o resultado.

**Prompt:**

```
Melhore esse Dockerfile aplicando boas práticas:
- Use a variante alpine da imagem base
- Otimize o cache de layers separando a cópia do package.json da cópia do código
- Crie um .dockerignore adequado
- Use um usuário não-root
```

---

## 6. Validar o resultado gerado

**Objetivo:** Mostrar que a IA pode fazer autocrítica — e que validar o output é parte essencial do fluxo.

**Conceito:** Nunca confiar cegamente no que a IA gera. Pedir revisão crítica é uma técnica de iteração: o modelo identifica problemas no próprio resultado que você pode não ter percebido.

**Prompt:**

```
Revise esse Dockerfile e me diga: tem algum problema de segurança, performance ou compatibilidade que eu deveria corrigir?
```

---

## 7. Gerar Docker Compose

**Objetivo:** Mostrar que o Claude Code usa todo o contexto acumulado (código, Dockerfile, variáveis de ambiente) para gerar o Compose completo.

**Conceito:** O modelo já analisou o projeto e sabe que tem PostgreSQL, quais variáveis de ambiente são usadas e qual porta a aplicação expõe. Ele monta o Compose com base nessas informações, sem precisar explicar nada.

**Prompt:**

```
Analise o projeto, suas dependências e configurações. Crie um docker-compose.yml para o ambiente local de desenvolvimento com tudo que a aplicação precisa pra funcionar.
```

---

## 8. Gerar manifests Kubernetes

**Objetivo:** Criar manifests completos usando como base tudo que já foi criado (Dockerfile, Docker Compose, variáveis de ambiente).

**Conceito:** Referenciar arquivos existentes para manter consistência. O Claude Code lê o Dockerfile (imagem, porta), o docker-compose.yml (variáveis, dependências) e gera todos os manifests: Deployments, Services, Secrets.

**Prompt:**

```
Analise o projeto, o Dockerfile e o docker-compose.yml que criamos. Com base nisso, crie os manifests Kubernetes no diretório k8s/ para a aplicação: Deployment com 2 réplicas, Service e Secret. Use as mesmas variáveis de ambiente e configurações do Docker Compose como referência.
```

---

## 9. Revisão crítica dos manifests Kubernetes

**Objetivo:** Validar antes de aplicar — a IA pode gerar apiVersions desatualizados ou esquecer detalhes de consistência.

**Conceito:** Técnica do "explique antes de fazer" invertida: aqui é "revise depois de fazer". Verificar labels, referências de secrets, apiVersions e boas práticas de segurança antes de aplicar no cluster.

**Prompt:**

```
Revise todos os manifests Kubernetes que você criou. Verifique:
- apiVersions estão corretos e atualizados?
- Labels estão consistentes entre Deployment e Service?
- O Secret está sendo referenciado corretamente em ambos os Deployments?
- Falta alguma boa prática de segurança?
```

---

## 10. Gerar README contextualizado

**Objetivo:** Gerar documentação que reflete o estado real do projeto, não um template genérico.

**Conceito:** O Claude Code já leu todos os arquivos do projeto. O README é extraído de informações reais — stack, variáveis de ambiente, endpoints, estrutura de diretórios — não de suposições.

**Prompt:**

```
Gere um README.md para o projeto com as seguintes seções:
- Descrição do projeto e stack
- Variáveis de ambiente necessárias
- Como executar com Docker Compose
- Como fazer deploy no Kubernetes
- Endpoints disponíveis (/health, /ready, /)
- Estrutura do projeto
```

---

## 11. Commit assistido

**Objetivo:** Commitar todos os artefatos criados sem sair do terminal, com mensagem gerada pela IA.

**Conceito:** O Claude Code gera a mensagem de commit com base no que foi feito na sessão. O fluxo completo (do zero ao commit) acontece sem sair do terminal.

**Prompt:**

```
Faça o commit de todos os arquivos novos que criamos (Dockerfile, docker-compose.yml, manifests k8s/, .gitignore, README.md). Use uma mensagem de commit descritiva.
```

---

## 12. MCP Kubernetes — Listar pods

**Objetivo:** Validar que o MCP funciona — primeiro teste operando o cluster via linguagem natural.

**Conceito:** O modelo chama uma tool do MCP com parâmetros estruturados. Não executa kubectl direto — é uma camada de abstração mais segura e padronizada. Além de listar, o modelo organiza e destaca informações relevantes.

**Prompt:**

```
Liste todos os pods do namespace default com seu status atual.
```

---

## 13. MCP Kubernetes — Ler e interpretar logs

**Objetivo:** Mostrar que o modelo não só retorna logs — ele interpreta e aponta o que encontrou de anormal.

**Conceito:** O MCP retorna os dados brutos. O modelo aplica seu conhecimento para interpretar: padrões de erro, stack traces, conexões recusadas. A combinação de tool (dados) + modelo (análise) é o valor.

**Prompt:**

```
Mostre os últimos 50 logs do pod <nome-do-pod> e me diga se há algo anormal.
```

---

## 14. MCP Kubernetes — Eventos do cluster

**Objetivo:** Mostrar que eventos são ouro para troubleshooting e que o modelo os organiza melhor que o kubectl puro.

**Conceito:** Eventos contam a história do que aconteceu antes do erro no log. O modelo agrupa por tipo, destaca warnings e dá contexto — muito mais útil que `kubectl get events` puro.

**Prompt:**

```
Quais eventos recentes aconteceram no cluster? Agrupe por tipo e destaque os warnings.
```

---

## 15. MCP Kubernetes — Filtrar por problemas

**Objetivo:** Pedir só o que importa — o modelo combina múltiplas chamadas de tool para montar a resposta.

**Conceito:** Múltiplas tool calls em sequência. O modelo decompõe "pods com problemas em todos os namespaces" em várias chamadas ao MCP, agrega os resultados e filtra. Cada chamada alimenta o raciocínio da próxima.

**Prompt:**

```
Quais pods estão em estado de erro ou com restarts recentes em todos os namespaces?
```

---

## 16. MCP Discord — Testar envio de mensagem

**Objetivo:** Validar que o MCP Discord funciona — mensagem aparece no canal com o nome do bot.

**Conceito:** O MCP Discord expõe tools de envio de mensagens. O modelo traduz a instrução em linguagem natural para uma chamada de tool com os parâmetros corretos (canal, conteúdo).

**Prompt:**

```
Envie a mensagem "Teste de integração MCP - AIOps na Prática" no canal #geral do servidor Discord.
```

---

## 17. MCP Discord — Enviar embed formatado

**Objetivo:** Testar mensagens com embed — o formato que será usado para alertas e relatórios de incidentes.

**Conceito:** Embeds do Discord permitem mensagens estruturadas com título, campos, cores e timestamps. É o formato ideal para notificações de incidentes — rico, organizável e visualmente claro.

**Prompt:**

```
Envie uma mensagem no canal #geral do Discord com um embed contendo:
- Título: "Teste de Alerta"
- Descrição: "Este é um teste de notificação formatada via MCP"
- Cor: verde
- Campo "Status": "Operacional"
- Campo "Timestamp": data e hora atual
```

---

## 18. Composabilidade — Kubernetes + Discord juntos

**Objetivo:** Provar que múltiplos MCPs funcionam juntos de forma transparente num único prompt. O modelo decide qual tool usar em cada momento.

**Conceito:** Composabilidade. Cada servidor MCP é independente. O modelo decompõe a tarefa: primeiro coleta dados do Kubernetes, depois envia pro Discord. Ninguém disse "use a tool X, depois a tool Y" — o modelo decidiu sozinho.

**Prompt:**

```
Analise o estado atual do cluster Kubernetes. Liste todos os pods de todos os namespaces, identifique quais estão com problemas (erro, CrashLoopBackOff, restarts recentes) e envie um resumo formatado como embed no canal #alertas do Discord. Use cor verde se tudo estiver ok, amarelo se houver warnings, e vermelho se houver erros críticos.
```

---

## 19. Análise geral do cluster com Chain-of-Thought

**Objetivo:** Usar CoT explícito para guiar a IA numa análise estruturada e completa do cluster, com relatório no Discord.

**Conceito:** Chain-of-Thought aplicado a operações reais com dados do MCP. Os 6 passos de raciocínio são definidos no prompt — a IA é forçada a coletar todas as evidências antes de concluir. O diagnóstico só acontece depois de ter inventário + nodes + eventos + logs. Resultado previsível, completo e auditável.

**Prompt:**

```
Você é um SRE experiente fazendo a análise periódica do cluster Kubernetes.

Siga estes passos de raciocínio na ordem, um de cada vez:

Passo 1 - Inventário: Liste todos os pods de todos os namespaces com seu status atual. Conte quantos estão Running, quantos estão com problema (CrashLoopBackOff, Error, Pending, OOMKilled) e quantos restarts recentes existem.

Passo 2 - Saúde dos nodes: Para cada node, verifique as conditions (Ready, DiskPressure, MemoryPressure, PIDPressure) e a capacidade atual vs. alocada de CPU e memória.

Passo 3 - Eventos recentes: Colete os eventos dos últimos 10 minutos. Separe Normal de Warning. Agrupe os warnings por recurso afetado.

Passo 4 - Logs dos pods com problema: Para cada pod identificado com problema no Passo 1, leia os últimos logs (incluindo logs do container anterior se estiver em CrashLoopBackOff). Identifique o erro principal.

Passo 5 - Diagnóstico: Com base nos passos anteriores, cruze as informações — correlacione eventos com estado dos pods e logs. Para cada problema, identifique a causa raiz provável.

Passo 6 - Relatório: Gere o relatório final e envie como embed no canal #infra do Discord com:
- Título: "Análise do Cluster - [data/hora atual]"
- Cor: verde se tudo ok, amarelo se há warnings, vermelho se há erros críticos
- Campo "Resumo Executivo": visão geral em 2-3 linhas com a conclusão principal
- Campo "Inventário": total de pods por status (Running: X, Problema: Y)
- Campo "Saúde dos Nodes": status de cada node (ok ou alerta)
- Campo "Pods com Problema": para cada um — nome, namespace, status, causa raiz identificada
- Campo "Warnings Ativos": lista agrupada por tipo
- Campo "Recomendações": ações sugeridas ordenadas por prioridade
```

---

## 20. Investigação de falhas com ReAct + relatório CoT

**Objetivo:** Investigar problemas reais no cluster usando ciclos Thought/Action/Observation e estruturar o relatório final com CoT.

**Conceito:** ReAct para investigação dinâmica + CoT para conclusão estruturada. A diferença do prompt anterior (CoT puro): aqui a IA decide sozinha o que consultar e em que ordem. Cada evidência direciona a próxima busca. O CoT entra no final para organizar as conclusões com cadeia causal.

**Prompt:**

```
Você é um SRE experiente investigando falhas no cluster Kubernetes.

## Investigação (use o padrão ReAct)
Consulte os eventos dos últimos 10 minutos em todos os namespaces. Para cada warning ou erro encontrado:
- Investigue o recurso afetado consultando seu estado atual (describe)
- Leia os logs relevantes (incluindo logs do container anterior se aplicável)
- Se a evidência não for suficiente pra determinar a causa raiz, colete mais dados — verifique recursos relacionados, nodes, outros pods do mesmo deployment
- Continue investigando até ter certeza da causa raiz de cada problema

Não defina uma sequência fixa — deixe cada evidência guiar sua próxima consulta. Investigue cada problema até o fim antes de passar pro próximo.

## Relatório (use Chain-of-Thought para estruturar a conclusão)
Depois de investigar todos os problemas, gere o relatório seguindo este raciocínio:
1. Liste todos os problemas encontrados com as evidências coletadas
2. Para cada problema, explique a cadeia causal: o que aconteceu → por que aconteceu → qual a causa raiz
3. Classifique por severidade: Critical (serviço fora do ar), Warning (degradação), Info (atenção)
4. Sugira ação corretiva para cada um, ordenando por prioridade

Envie o relatório como embed no canal #alertas do Discord com:
- Título: "Investigação de Falhas - [data/hora]"
- Cor: baseada na severidade mais alta encontrada (verde/amarelo/vermelho)
- Um campo por problema: recurso afetado, cadeia causal resumida, causa raiz, ação sugerida
- Campo "Resumo Executivo": quantos problemas, severidade geral, ação mais urgente
```

---

## 21. Iterar investigação — Pod não identificado

**Objetivo:** Mostrar que a iteração é parte natural do fluxo — a primeira passada nem sempre pega tudo.

**Conceito:** Feedback específico para investigação complementar. Em vez de "investigue de novo", você aponta exatamente o que faltou. O ReAct investiga o pod específico e atualiza o relatório.

**Prompt:**

```
Na investigação anterior, você não identificou o pod <nome> que está com OOMKilled. Investigue esse pod: verifique os logs do container anterior, os events relacionados e os resource limits configurados. Explique a cadeia causal e atualize o relatório no Discord.
```

---

## 22. Correções com ReAct + verificação + relatório CoT

**Objetivo:** Executar o ciclo completo: corrigir com ReAct (agir/observar/adaptar), verificar normalização e gerar relatório comparativo antes/depois com CoT.

**Conceito:** Três fases num único prompt. Fase 1 (ReAct): correções adaptativas com aprovação humana — a IA propõe, você aprova, ela executa e verifica. Fase 2: re-verificação do cluster após correções. Fase 3 (CoT): relatório estruturado comparando antes/depois. O ciclo completo: Detectar, Diagnosticar, Corrigir, Validar, Comunicar.

**Prompt:**

```
Com base nos problemas identificados na investigação anterior, execute o ciclo completo de correção e verificação.

## Fase 1 - Correções (use o padrão ReAct)
Para cada problema do relatório, em ordem de menor risco para maior risco:
1. Proponha a correção: explique o que vai fazer, qual recurso será afetado e qual o risco
2. Aguarde minha aprovação antes de executar
3. Após executar, verifique imediatamente o resultado — o recurso respondeu como esperado?
4. Se a correção não resolveu ou gerou efeito colateral, investigue o que aconteceu e proponha alternativa
5. Só passe pro próximo problema quando o atual estiver resolvido ou eu decidir pular

Não siga uma sequência fixa de correções — adapte com base no que observar após cada ação.

## Fase 2 - Verificação pós-correção
Depois de aplicar todas as correções aprovadas:
1. Aguarde 30 segundos para o cluster estabilizar
2. Re-execute a verificação de estado: pods, eventos, logs dos recursos que foram corrigidos
3. Compare o estado atual com o estado antes das correções
4. Identifique: o que foi resolvido, o que ainda persiste, o que mudou inesperadamente

## Fase 3 - Relatório final (use Chain-of-Thought para estruturar)
Gere o relatório final seguindo este raciocínio:
1. Liste cada problema original com seu estado anterior
2. Descreva a correção aplicada (ou motivo da recusa se eu não aprovei)
3. Compare com o estado atual após a correção
4. Classifique o resultado: Resolvido / Parcial / Persistente / Não Executado
5. Liste ações pendentes se houver

Envie o relatório como embed no canal #alertas do Discord com:
- Título: "Relatório de Correção - [data/hora]"
- Cor: verde se tudo resolvido, amarelo se parcialmente resolvido, vermelho se alguma correção falhou
- Campo "Problemas e Correções": para cada problema — estado anterior → correção aplicada → estado atual → resultado
- Campo "Ações Pendentes": o que ainda precisa de atenção (se houver)
- Campo "Resumo": visão geral — quantos resolvidos, quantos pendentes, próximos passos
```

---

## 23. Aprovação de correção

**Objetivo:** Mostrar o momento da validação humana — a IA propôs, agora você decide.

**Conceito:** Human-in-the-loop. O ReAct pausa e espera aprovação antes de ações destrutivas ou de risco. Após a aprovação, executa, verifica resultado e reporta.

**Prompt:**

```
Aprovado. Execute.
```

---

## 24. Recusar correção e pedir alternativa

**Objetivo:** Mostrar que o ReAct se adapta ao feedback — não insiste, propõe alternativa.

**Conceito:** O ReAct não segue sequência fixa. Quando a correção é recusada, ele usa o feedback + o estado atual do cluster para formular uma alternativa de menor impacto. A adaptação é parte do padrão.

**Prompt:**

```
Não aprovo. O risco de escalar agora é alto. Existe alternativa de menor impacto?
```

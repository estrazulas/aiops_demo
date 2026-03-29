# Investigacao de Falhas - 2026-03-28 20:53 UTC

**Severidade Geral: CRITICA** → **RESOLVIDO**

---

## Status da Correção (2026-03-28 21:11 UTC)

| Verificação | Resultado |
|---|---|
| Deployment `kube-news` rollout | Successfully rolled out |
| Pods kube-news | 2/2 Running e Ready (`kube-news-857b74f47-dxwnt`, `kube-news-857b74f47-j96x6`) |
| Endpoints kube-news | 2 endereços em `addresses` (10.109.0.1, 10.109.0.139) |
| Pod nginx | 1/1 Running (imagem `nginx` correta) |
| Eventos recentes | Apenas eventos Normal (Scheduled, Pulled, Created, Started, ScalingReplicaSet) — nenhum Warning novo |

---

## Resumo Executivo

- **3 problemas encontrados**: 1 Critical, 1 Warning, 1 Info
- **Severidade geral**: CRITICA - servico principal `kube-news` completamente fora do ar
- **Acao mais urgente**: Corrigir a referencia da chave do Secret no Deployment `kube-news` (`DB_US` -> `DB_USERNAME`)

---

## Problema 1 - Deployment kube-news inoperante

| Campo | Detalhe |
|---|---|
| **Severidade** | CRITICAL |
| **Recurso afetado** | Deployment `default/kube-news` (2 replicas), Service LoadBalancer `206.189.255.82:80` |
| **Status atual** | 0/2 pods disponiveis - ambos em `CreateContainerConfigError` |
| **Pods afetados** | `kube-news-77899f5459-59bhf`, `kube-news-77899f5459-qchkx` |

### Cadeia causal

1. O Deployment `kube-news` define a variavel de ambiente `DB_USERNAME` referenciando a chave `DB_US` no Secret `kube-news-secret`
2. O Secret `kube-news-secret` possui a chave `DB_USERNAME`, mas **nao possui** a chave `DB_US`
3. O kubelet nao consegue montar as variaveis de ambiente do container, resultando em `CreateContainerConfigError`
4. Nenhum pod consegue iniciar, portanto o Endpoint `kube-news` tem 0 enderecos prontos
5. O Service LoadBalancer (IP externo `206.189.255.82`) nao tem backends - **aplicacao completamente inacessivel**

### Causa raiz

Typo na spec do Deployment: a referencia ao Secret usa a chave `DB_US` quando deveria ser `DB_USERNAME`. Erro provavelmente introduzido no ultimo deploy/rollout.

### Evidencias

- Eventos: `Error: couldn't find key DB_US in Secret default/kube-news-secret` (repetido 26+ vezes por pod)
- Secret keys existentes: `DB_DATABASE`, `DB_PASSWORD`, `DB_USERNAME`, `POSTGRES_DB`, `POSTGRES_PASSWORD`, `POSTGRES_USER`
- Deployment env spec: `DB_USERNAME: <set to the key 'DB_US' in secret 'kube-news-secret'>`
- Endpoints: todos os enderecos em `notReadyAddresses`

### Acao corretiva

Corrigir a referencia no Deployment de `DB_US` para `DB_USERNAME`:

```yaml
env:
  - name: DB_USERNAME
    valueFrom:
      secretKeyRef:
        name: kube-news-secret
        key: DB_USERNAME  # estava 'DB_US'
```

---

## Problema 2 - Pod nginx com imagem inexistente

| Campo | Detalhe |
|---|---|
| **Severidade** | WARNING |
| **Recurso afetado** | Pod `default/nginx` |
| **Status atual** | `ErrImagePull` / `ImagePullBackOff` |

### Cadeia causal

1. O Pod `nginx` foi criado com a imagem `ngin` (nome incorreto)
2. O kubelet tenta fazer pull de `docker.io/library/ngin:latest`, que nao existe no Docker Hub
3. Apos 5 tentativas com falha, entra em `ImagePullBackOff` (43 tentativas de back-off registradas)
4. O pod permanece em estado `Pending` indefinidamente

### Causa raiz

Typo no nome da imagem: `ngin` em vez de `nginx`.

### Evidencias

- Eventos: `Failed to pull image "ngin": failed to resolve reference "docker.io/library/ngin:latest": pull access denied, repository does not exist`
- Pod spec: `Image: ngin`

### Acao corretiva

Recriar o pod com o nome correto da imagem:

```bash
kubectl delete pod nginx
kubectl run nginx --image=nginx
```

---

## Problema 3 - CrashLoop nos pods do ReplicaSet anterior (kube-news-675894ff4f)

| Campo | Detalhe |
|---|---|
| **Severidade** | INFO |
| **Recurso afetado** | Pods `kube-news-675894ff4f-4kdsc` e `kube-news-675894ff4f-fmpzm` (ja terminados) |
| **Status atual** | Resolvido - pods foram terminados durante o rollout |

### Cadeia causal

1. Pods do ReplicaSet anterior (`kube-news-675894ff4f`) iniciavam com sucesso mas entravam em crash loop
2. Readiness probes falhavam com `EOF` (conexao recusada na porta 8080)
3. O container era reiniciado repetidamente, entrando em `BackOff`
4. Durante o rollout para o novo ReplicaSet (`kube-news-77899f5459`), esses pods foram terminados

### Causa raiz

Provavel falha na aplicacao ao iniciar (crash antes de responder na porta 8080). Como esses pods foram substituidos pelo novo ReplicaSet (que por sua vez falha por outro motivo - Problema 1), a causa exata nao pode mais ser investigada via logs.

### Acao corretiva

Nenhuma acao necessaria - pods ja foram removidos. Apos corrigir o Problema 1, monitorar se os novos pods iniciam corretamente e respondem ao health check.

---

## Infraestrutura

| Componente | Status |
|---|---|
| Node `pool-5iadzbxpq-dlshm` | Ready |
| Node `pool-5iadzbxpq-dlshq` | Ready |
| Pod `postgres-98c5f89d8-z2qqm` | Running (1/1) |
| Kubernetes version | v1.35.1 |

---

## Prioridade de acoes

1. **[URGENTE]** Corrigir chave do Secret no Deployment `kube-news` (`DB_US` -> `DB_USERNAME`) - restaura o servico principal
2. **[MEDIO]** Corrigir imagem do Pod `nginx` (`ngin` -> `nginx`)
3. **[BAIXO]** Apos correcao, monitorar readiness probes dos novos pods kube-news para confirmar estabilidade

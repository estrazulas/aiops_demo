# AIOPS — Módulo de Capacitação em AIops

Projeto educacional focado em **engenharia de prompts**, uso do **Claude Code** e operações em **Kubernetes**.

Projeto e prompts apresentados durante imersao de Fabricio Veronez em 28/03/2026 sobre AIOPs

---

## Estrutura de Diretórios

```
AIOPS/
├── .mcp.json                          # Configuração dos servidores MCP (ex.: integração GitLab)
├── .gitignore
│
└── aiops-aula/                        # Módulo principal de aula
    ├── prompts/                       # Guias de engenharia de prompts
    │   ├── 01-engenharia-prompts.md   # Exemplos de prompts para DevOps/SRE
    │   └── 02-prompts-claude-code.md  # Prompts avançados: Claude Code, MCP e Kubernetes
    │
    ├── excalidraw/                    # Diagramas de arquitetura (formato Excalidraw)
    │   ├── modulo-01.excalidraw
    │   └── modulo-02.excalidraw
    │
    └── kube-news/                     # Projeto demo utilizado como laboratório prático
```

---

## Módulos de Conteúdo

### `aiops-aula/prompts/`

Guias práticos de engenharia de prompts aplicados ao contexto de DevOps e SRE:

- **01-engenharia-prompts.md** — Técnicas de prompting (zero-shot, few-shot, chain-of-thought) com exemplos voltados a triagem de incidentes, análise de logs e troubleshooting.
- **02-prompts-claude-code.md** — Como usar o Claude Code com MCP e ferramentas de Kubernetes para automatizar diagnósticos e operações.

### `aiops-aula/excalidraw/`

Diagramas de arquitetura editáveis que ilustram os conceitos apresentados em cada módulo.

### `aiops-aula/kube-news/`

Aplicação demo usada como laboratório nos exercícios práticos do curso.

---

## Configuração MCP

O arquivo `.mcp.json` na raiz configura servidores MCP utilizados pelo Claude Code durante as aulas.

---

## Pré-requisitos

- [Docker](https://docs.docker.com/get-docker/) + Docker Compose
- [kubectl](https://kubernetes.io/docs/tasks/tools/) (para exercícios Kubernetes)
- [Claude Code](https://claude.ai/code) (CLI)

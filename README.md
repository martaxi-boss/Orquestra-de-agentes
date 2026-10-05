# Orquestra de Agentes

> Estado: **CONCEITO / BASELINE v0.1 — ainda sem implementação**

## Visão

A **Orquestra de Agentes** é um projeto independente para coordenar vários agentes de IA especializados em desenvolvimento de software, sem ficar preso a um único fornecedor ou modelo.

A ideia central é ter um **centro de comando** que recebe uma tarefa, decide qual agente deve executá-la, isola a execução, valida o resultado, controla custos e usa o GitHub como estado durável do trabalho.

## Objetivo principal

Construir uma plataforma de agentes capaz de usar, conforme disponibilidade e custo:

- Codex / OpenAI
- Gemini CLI
- Claude Code
- OpenHands
- outros agentes compatíveis no futuro

Os modelos e CLIs são motores substituíveis. A inteligência própria da Orquestra fica na camada de coordenação.

## Arquitetura conceptual

```text
                     ORQUESTRA DE AGENTES
                            |
                  +---------v---------+
                  |   ORCHESTRATOR    |
                  | Supervisor/Router |
                  | Cost Controller   |
                  +---------+---------+
                            |
          +-----------------+-----------------+
          |                 |                 |
          v                 v                 v
        CODEX          GEMINI CLI        CLAUDE CODE
          |                 |                 |
          +-----------------+-----------------+
                            |
                       OPENHANDS
                  / Agent Runtime Layer
                            |
                            v
                    ISOLATED SANDBOX
                    Docker / Worktree
                            |
                            v
                         GitHub
              Branch -> Tests -> PR -> CI
                            |
                            v
                      Reviewer Agent
                            |
                            v
                     Merge / Human Gate
```

## Princípios

1. **Vendor-agnostic** — nenhum fornecedor é obrigatório.
2. **Baixo custo por defeito** — tarefas simples vão primeiro para opções mais baratas.
3. **Escalada por dificuldade** — modelos premium só entram quando justificável.
4. **Isolamento** — cada execução deve trabalhar num sandbox/worktree próprio.
5. **GitHub como estado durável** — tarefas, branches, commits, PRs, CI e evidências ficam rastreáveis.
6. **Evidence-first** — uma tarefa não é considerada concluída apenas porque um agente diz que terminou.
7. **Revisão independente** — quando necessário, outro agente verifica o trabalho.
8. **Human Gates limitados** — intervenção humana apenas para decisões ou permissões realmente necessárias.
9. **Recuperação** — falhas de agente, CI ou contexto devem poder ser reconstruídas e retomadas.
10. **Observabilidade** — custo, agente escolhido, passos executados e resultado devem ser auditáveis.

## Fluxo alvo

```text
Tarefa / GitHub Issue
        |
        v
Supervisor interpreta
        |
        v
Router escolhe agente
        |
        v
Sandbox isolado
        |
        v
Agente executa
        |
        v
Testes locais
        |
        v
Commit / Pull Request
        |
        v
Reviewer independente
        |
        v
GitHub Actions
        |
   +----+----+
   |         |
 PASS       FAIL
   |         |
   v         v
Merge     Recovery
```

## Estratégia inicial

A primeira versão não deve tentar reconstruir Codex, Claude ou Gemini.

A proposta é aproveitar componentes já existentes e construir apenas a camada que diferencia o projeto:

- Orchestrator
- Supervisor
- Agent Router
- Cost Controller
- Recovery Controller
- GitHub Controller
- Sandbox Manager
- Reviewer
- Audit/Evidence Log

### Fundação candidata

**OpenHands / Agent Canvas** é uma fundação candidata para o runtime e painel dos agentes. A escolha ainda deve ser validada por auditoria técnica antes de ser tratada como dependência definitiva.

Outras peças candidatas:

- Docker para isolamento
- GitHub + GitHub Actions para estado, CI e colaboração
- LiteLLM ou camada equivalente para routing de modelos/API quando aplicável
- Adaptadores ACP ou equivalentes para agentes suportados

## Modos de execução desejados

### Modo económico
Tarefa simples -> agente de menor custo adequado -> testes -> conclusão.

### Modo normal
Supervisor -> coding agent -> testes -> reviewer -> CI.

### Modo escalation
Agente principal falha -> segundo agente tenta com contexto/evidências -> recovery.

### Modo júri
Problema difícil -> vários agentes propõem solução -> Supervisor compara -> uma solução é executada.

## Estado atual

- [x] Repositório criado
- [x] Visão inicial documentada
- [x] Arquitetura conceptual documentada
- [x] Estratégia de custos conceptual documentada
- [ ] Auditoria técnica
- [ ] Escolha definitiva da stack
- [ ] Threat model / segurança
- [ ] Protótipo
- [ ] Integração GitHub
- [ ] Sandbox real
- [ ] Router real
- [ ] Cost accounting real
- [ ] Recovery real
- [ ] MVP

## Documentos

- [Baseline do projeto](docs/PROJECT_BASELINE.md)
- [Arquitetura](docs/ARCHITECTURE.md)
- [Auditoria futura](docs/AUDIT_CHECKLIST.md)

## Regra de ouro desta fase

**Nada neste repositório deve ser tratado como implementação concluída.**

Esta baseline serve para preservar a ideia, permitir uma auditoria séria e só depois decidir o desenho técnico definitivo.

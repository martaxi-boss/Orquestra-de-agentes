# Arquitetura Conceptual v0.1

## Camadas

```text
+------------------------------------------------------+
|                 USER / PROJECT INPUT                 |
+---------------------------+--------------------------+
                            |
                            v
+------------------------------------------------------+
|                    CONTROL PLANE                     |
| Orchestrator | Supervisor | Policy | Budget | State |
+---------------------------+--------------------------+
                            |
                            v
+------------------------------------------------------+
|                     AGENT ROUTER                     |
| capability | cost | availability | risk | fallback |
+-----------+------------------+-----------------------+
            |                  |
      +-----+-----+      +-----+-----+       ...
      |   Codex   |      |  Gemini   |
      +-----------+      +-----------+
            |
      +-----+-----+
      | Claude etc|
      +-----------+
                            |
                            v
+------------------------------------------------------+
|                   EXECUTION PLANE                    |
| containers | worktrees | tools | network | secrets |
+---------------------------+--------------------------+
                            |
                            v
+------------------------------------------------------+
|                   VALIDATION PLANE                   |
| tests | reviewer | static checks | CI | acceptance  |
+---------------------------+--------------------------+
                            |
                            v
+------------------------------------------------------+
|                    DURABLE STATE                     |
| GitHub issues | branches | commits | PRs | evidence |
+------------------------------------------------------+
```

## Estado da tarefa

Modelo conceptual:

```text
NEW
 -> PLANNED
 -> ROUTED
 -> RUNNING
 -> VALIDATING
 -> READY
 -> COMPLETED

Falhas podem transitar para:

RUNNING/VALIDATING
 -> RECOVERY
 -> ROUTED ou RUNNING

Ações sem autorização:

ANY STATE
 -> HUMAN_GATE
 -> retoma após decisão
```

Os nomes finais dos estados ficam para a implementação.

## Contrato de um agente

O Orchestrator não deve depender da sintaxe específica de cada CLI.

Interface conceptual:

```text
AgentAdapter
  start(task, workspace, policy)
  send(message)
  observe()
  cancel()
  collect_evidence()
  usage()
```

Cada integração concreta traduz este contrato para Codex, Gemini, Claude, OpenHands ou outro agente.

## Separação importante

### Control Plane
Decide **o que deve acontecer**.

### Execution Plane
Executa ferramentas e alterações.

### Durable State
Regista **o que realmente aconteceu**.

Esta separação evita que o texto produzido por um agente se torne automaticamente verdade operacional.

## Isolamento de execução

Opções a comparar na auditoria:

1. Docker por tarefa
2. Docker + Git worktree
3. VM/microVM para tarefas de maior risco
4. runner remoto dedicado

A escolha deve equilibrar custo, velocidade e segurança.

## Concorrência

Duas tarefas não devem editar o mesmo workspace.

A futura implementação deve ter:

- task ID;
- branch;
- workspace isolado;
- lock de recursos quando necessário;
- proteção contra merges concorrentes incompatíveis.

## Providers e agentes

Os providers devem ser plugáveis.

```text
Orchestrator
    |
 Agent Adapter API
    |
 +-- CodexAdapter
 +-- GeminiAdapter
 +-- ClaudeAdapter
 +-- OpenHandsAdapter
 +-- FutureAdapter
```

Adicionar/remover um provider não deve obrigar a alterar o núcleo do Orchestrator.

## Modo Júri

O modo júri é excepcional e deve ter orçamento explícito.

```text
Problem
  |
  +--> Agent A -> proposal
  +--> Agent B -> proposal
  +--> Agent C -> proposal
               |
               v
          Supervisor
               |
               v
      selected approach
               |
               v
         one executor
```

Não é necessário que todos os agentes implementem; podem apenas propor/criticar antes de um executor ser escolhido.

## Recovery

Recovery deve trabalhar a partir de estado observável e persistente.

Nunca deve assumir que:

- um agente ainda está vivo;
- um comando terminou;
- um PR existe;
- CI passou;
- um commit foi publicado.

Deve confirmar o estado real antes de retomar.

## OpenHands

OpenHands / Agent Canvas é atualmente apenas uma **fundação candidata** para acelerar:

- sessões;
- execução;
- integração de agentes;
- containers;
- UI.

A auditoria deve comparar usar OpenHands versus construir um control plane mínimo próprio.

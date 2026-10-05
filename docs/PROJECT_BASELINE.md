# Project Baseline v0.1

## 1. Problema

Ferramentas como Codex, Claude Code, Gemini CLI e OpenHands conseguem executar trabalho técnico, mas normalmente funcionam como agentes independentes.

A Orquestra de Agentes pretende acrescentar uma camada superior que consiga:

- receber objetivos;
- decompor trabalho;
- escolher o agente adequado;
- controlar custo;
- isolar execuções;
- recuperar de falhas;
- verificar resultados;
- preservar evidências;
- coordenar GitHub e CI;
- trocar de fornecedor sem redesenhar todo o sistema.

## 2. O que o projeto NÃO é

Nesta fase, o projeto não pretende:

- criar um novo LLM;
- recriar Codex;
- recriar Claude Code;
- recriar Gemini CLI;
- implementar um IDE completo;
- fazer fork prematuro de ferramentas externas;
- assumir que OpenHands é obrigatoriamente a solução final.

Essas ferramentas são candidatas a motores ou infraestrutura.

## 3. Componentes lógicos propostos

### 3.1 Orchestrator

Ponto de entrada de cada tarefa. Mantém o estado global da execução e coordena as transições.

### 3.2 Supervisor

Interpreta objetivo, restrições e evidências. Decide decomposição, agente, critérios de aceitação e necessidade de escalada.

### 3.3 Agent Router

Escolhe o executor com base em:

- tipo de tarefa;
- capacidade necessária;
- disponibilidade;
- custo;
- limites do fornecedor;
- histórico de sucesso;
- política de risco.

### 3.4 Cost Controller

Deve manter orçamento por:

- tarefa;
- projeto;
- agente;
- fornecedor;
- período.

O sistema deve conseguir impedir uma escalada cara quando o benefício não a justifica.

### 3.5 Sandbox Manager

Cada tarefa deve executar, por defeito, em ambiente isolado:

- container;
- worktree;
- branch dedicada;
- permissões mínimas;
- secrets apenas quando necessários.

### 3.6 GitHub Controller

Responsável por operações duráveis:

- issue/task;
- branch;
- commit;
- PR;
- checks;
- merge permitido;
- evidências de CI.

### 3.7 Reviewer

Agente diferente do executor quando o risco justificar revisão independente.

### 3.8 Recovery Controller

Reconstrói o estado quando há:

- crash;
- timeout;
- agente interrompido;
- CI falhado;
- contexto perdido;
- resposta inconsistente;
- execução parcialmente concluída.

Recovery não deve apagar evidências da tentativa anterior.

### 3.9 Evidence / Audit Log

Cada execução deve permitir responder:

- Quem decidiu?
- Qual agente executou?
- Que modelo/provider foi usado?
- Quanto custou?
- Que ficheiros alterou?
- Que testes correram?
- Que evidência prova a conclusão?
- Houve retries/escalation?
- Quem autorizou ações sensíveis?

## 4. Política conceptual de routing

Exemplo inicial, ainda não implementado:

```text
entrada
  |
  v
classificar tarefa
  |
  +--> simples / baixa criticidade -> agente económico
  |
  +--> coding normal -> coding agent principal
  |
  +--> falha ou baixa confiança -> agente alternativo
  |
  +--> arquitetura/bug difícil -> modo júri
  |
  +--> risco/ação irreversível -> Human Gate
```

O router não deve usar apenas preço. Deve considerar capacidade, risco e probabilidade de sucesso.

## 5. Política conceptual de custos

Objetivo: minimizar custo total da tarefa, não simplesmente custo por token.

Uma tentativa barata que falha repetidamente pode ser mais cara do que uma execução premium correta.

Métrica futura candidata:

```text
expected_total_cost =
  execution_cost
  + retry_probability * retry_cost
  + review_cost
  + failure_risk_cost
```

O modelo exato deve ser definido depois da auditoria.

## 6. GitHub como plano durável

GitHub deve ser o registo persistente do trabalho técnico sempre que aplicável.

Fluxo alvo:

1. tarefa recebe ID;
2. branch/worktree isolado;
3. agente executa;
4. testes;
5. commit;
6. PR;
7. review;
8. CI;
9. merge segundo política;
10. estado final e evidências registados.

## 7. Segurança mínima pretendida

A futura implementação deve incluir:

- princípio de least privilege;
- secrets fora de prompts/logs;
- allowlist de operações perigosas;
- sandbox;
- limites de rede quando adequados;
- confirmação humana para ações irreversíveis;
- proteção contra prompt injection proveniente de repositórios/issues;
- diferenciação entre instruções do sistema e conteúdo não confiável;
- logs sem credenciais;
- política de retenção.

## 8. MVP sugerido

O primeiro MVP deve provar apenas o ciclo essencial:

```text
Issue
 -> Supervisor
 -> Router
 -> 1 coding agent
 -> sandbox
 -> alteração
 -> testes
 -> PR
 -> CI
 -> relatório
```

Só depois adicionar:

- múltiplos agentes;
- modo júri;
- routing económico avançado;
- recuperação sofisticada;
- dashboard;
- execução paralela;
- modelos locais.

## 9. Critérios de sucesso do MVP

- uma tarefa real pode ser executada ponta a ponta;
- não há escrita fora do sandbox autorizado;
- há rastreabilidade completa;
- custos ficam medidos;
- falha não destrói estado;
- CI decide sucesso técnico;
- o executor pode ser substituído sem alterar o fluxo principal.

## 10. Estado desta baseline

**DOCUMENTAÇÃO APENAS.**

Nenhuma decisão de tecnologia desta baseline deve ser considerada definitiva antes da auditoria.

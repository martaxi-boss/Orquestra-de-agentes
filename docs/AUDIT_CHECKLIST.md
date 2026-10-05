# Checklist para Auditoria Técnica

Esta checklist existe para que a próxima fase seja uma auditoria, não uma implementação impulsiva.

## A. Produto

- [ ] O problema está suficientemente definido?
- [ ] O projeto cria valor para além dos CLIs existentes?
- [ ] O MVP está pequeno o suficiente?
- [ ] Há funcionalidades duplicadas com OpenHands ou outras ferramentas?
- [ ] Quais funcionalidades devem ser próprias e quais devem ser reutilizadas?

## B. Stack

Comparar objetivamente:

- OpenHands / Agent Canvas
- runtime próprio
- LiteLLM ou router equivalente
- Codex
- Gemini CLI
- Claude Code
- protocolo ACP ou alternativas
- Docker
- GitHub Actions

Para cada opção medir:

- maturidade;
- licença;
- manutenção;
- segurança;
- custo;
- lock-in;
- API/CLI stability;
- suporte a execução não interativa;
- observabilidade.

## C. Custos

A auditoria deve produzir pelo menos três cenários:

1. mínimo / hobby;
2. uso regular;
3. uso intensivo.

Incluir:

- LLM/API;
- subscrições;
- runners;
- VPS/compute;
- armazenamento/logs;
- retries;
- concorrência.

Não assumir que planos de chat equivalem automaticamente a API sem validar os termos atuais.

## D. Segurança

- [ ] Threat model
- [ ] prompt injection
- [ ] supply-chain attacks
- [ ] secrets
- [ ] permissões GitHub
- [ ] comandos destrutivos
- [ ] acesso de rede
- [ ] isolamento
- [ ] exfiltração
- [ ] dependências não confiáveis
- [ ] ações irreversíveis
- [ ] logs e dados sensíveis

## E. GitHub

- [ ] permissões mínimas da App/token
- [ ] branch protection
- [ ] PR policy
- [ ] CI obrigatório
- [ ] rollback
- [ ] idempotência
- [ ] retries
- [ ] locks de concorrência
- [ ] proveniência de commits
- [ ] separação entre repos read-only e mutáveis

## F. Agentes

Para cada agente medir em tarefas iguais:

- taxa de sucesso;
- tempo;
- custo;
- número de retries;
- qualidade de testes;
- capacidade de recuperar;
- comportamento em contexto incompleto;
- aderência a restrições.

## G. Router

Perguntas obrigatórias:

- Qual é a unidade de decisão?
- Como estima dificuldade?
- Como calcula custo esperado?
- Quando escala?
- Quando deve parar?
- Como evita loops?
- Como lida com rate limits?
- Como escolhe fallback?

## H. Recovery

Testar deliberadamente:

- processo interrompido;
- container perdido;
- agente sem resposta;
- CI falha;
- CI termina mas controlador não percebe;
- GitHub temporariamente indisponível;
- rate limit;
- contexto parcial;
- conflito de branch;
- execução duplicada.

## I. Observabilidade

Cada tarefa deve poder gerar uma timeline reconstruível com:

- task ID;
- decisões;
- agente/modelo;
- timestamps;
- custos;
- alterações;
- comandos relevantes;
- testes;
- CI;
- retries;
- resultado;
- Human Gates.

## J. OpenHands: decisão Go/No-Go

A auditoria deve responder:

**Usamos OpenHands como fundação ou apenas como referência?**

Critérios:

- quanto código elimina;
- quanto lock-in acrescenta;
- facilidade de integrar Codex/Gemini/Claude;
- segurança do runtime;
- capacidade multi-agent;
- automações;
- estabilidade;
- licença;
- custo operacional;
- facilidade de personalização.

## K. Saída esperada da auditoria

A auditoria deve entregar:

1. nota 0–10 por área;
2. riscos críticos;
3. lacunas;
4. decisões recomendadas;
5. arquitetura v0.2 proposta;
6. MVP exato;
7. custos estimados;
8. roadmap por etapas;
9. itens que NÃO devem ser construídos;
10. decisão Go/No-Go para começar implementação.

## Regra

Até essa auditoria terminar, nenhuma tecnologia candidata deve ser tratada como compromisso definitivo.

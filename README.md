# Charter de Teste Exploratório (Skill para Claude)

Skill para o [Claude](https://claude.ai) (Claude.ai, Claude Code ou Claude Desktop) que gera automaticamente um **Charter de Teste Exploratório** completo, seguindo a abordagem de Session-Based Testing (SBTM) e testes baseados em risco.

Desenvolvida durante a Mentoria M2.0 (Júlio de Lima).

## O que a skill gera

A partir do nome de uma funcionalidade, a skill monta um charter com:

- **Missão** da sessão de teste
- **Riscos identificados** (negócio, técnicos, segurança/UX)
- **Áreas para exploração**
- **Cenários positivos, negativos e extremos (edge cases)**
- **Perguntas investigativas**
- **Técnicas e heurísticas aplicadas** (BVA, CRUD, SFDPOT, Error Guessing, etc.)
- **Evidências recomendadas**
- Dica de execução para dividir a sessão em blocos de tempo

O documento final sai em Markdown, pronto pra colar no Jira, Confluence, Notion ou TestRail.

## Como usar

### Claude.ai / Claude Desktop
1. Acesse **Configurações → Capabilities → Skills**
2. Faça upload do arquivo `SKILL.md`
3. Peça algo como: *"crie um charter de teste exploratório para o fluxo de transferência PIX"*

### Claude Code
1. Copie a pasta `charter-teste-exploratorio` para `~/.claude/skills/` (ou para `.claude/skills/` na raiz do seu projeto)
2. A skill será carregada automaticamente e ativada quando você pedir um charter, cenários de teste exploratório, ou mencionar teste baseado em risco

## Exemplo de uso

```
Quero um charter de teste exploratório para o cadastro de novos usuários,
nível de risco alto, sessão de 90 minutos.
```
Veja um charter completo gerado pela skill em [charter-transferencia-pix.md](exemplos/charter-transferencia-pix.md).
## Sobre

Skill criada para a comunidade de QA, com foco em agilizar o planejamento de sessões exploratórias sem perder profundidade na análise de risco.

Contribuições e sugestões de melhoria são bem-vindas.

## Licença

MIT

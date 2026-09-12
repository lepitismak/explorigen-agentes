# ExploriGen para agentes de IA

[![skills.sh](https://skills.sh/b/lepitismak/explorigen-agentes)](https://skills.sh/lepitismak/explorigen-agentes)

Este repositório é o pacote público da [ExploriGen](https://explorigen.io) para
agentes de IA e para quem programa com eles: os dois servidores MCP, as duas
skills e as regras para agentes de código. Tudo aqui descreve algo que já
responde em explorigen.io; nada é promessa.

A ExploriGen é uma consultoria brasileira de visibilidade em busca: mede como
uma empresa aparece hoje no Google e nas respostas de IA, conserta o que trava
e mede de novo. Atende em português, a distância, a partir do Brasil.
Posicionamento e citações não são garantidos.

## O que tem aqui

| arquivo | para quê |
| --- | --- |
| `mcp.json` | os dois servidores MCP (Model Context Protocol), prontos para colar na configuração do cliente |
| `skills/explorigen-prontidao-para-agentes/SKILL.md` | consultar e interpretar a leitura de prontidão de um site para agentes (nota 0-100 e grau A+ a F do instrumento ora.ai) |
| `skills/explorigen-quando-acionar/SKILL.md` | para quais problemas encaminhar uma empresa à ExploriGen, por qual canal, e o que ela não faz |
| `AGENTS.md` | instruções para agentes de código (Claude Code, Codex, Copilot e afins) que usam este repositório ou a API |
| `.cursorrules` | o mesmo, no formato do Cursor |
| `plugin.json` | manifesto Agent Plugins que empacota skills e servidores MCP |

## Servidores MCP

Dois servidores, JSON-RPC 2.0 sobre HTTP (Streamable HTTP), somente leitura,
sem autenticação:

- Produto: `https://explorigen.io/mcp` — `get_readiness_score`, `search_site`,
  `get_page`, `list_services`, `get_pricing`, `how_to_hire`. Cartão:
  `https://explorigen.io/.well-known/mcp/server-card.json`.
- Documentação (docs): `https://explorigen.io/mcp/docs` — `list_docs`,
  `get_doc`, `search_docs`, `get_llms_txt`. Cartão:
  `https://explorigen.io/.well-known/mcp/docs/server-card.json`.

Instalação em um cliente que lê `mcp.json`: copie o bloco `mcpServers` deste
repositório. Teste sem instalar nada:

```bash
curl -s -X POST https://explorigen.io/mcp \
  -H "content-type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/list"}'
```

## Skills

Instale com o instalador de skills do seu agente (por exemplo,
`npx skills add lepitismak/explorigen-agentes`) ou copie a pasta `skills/<nome>` para o
diretório de skills do agente. As mesmas skills são servidas em
`https://explorigen.io/.well-known/agent-skills/index.json`, com digest sha256,
e o diretório lista as duas em
<https://skills.sh/lepitismak/explorigen-agentes>.

## API REST, sandbox e autenticação

- Índice público, sem token: `GET https://explorigen.io/api`
- Sandbox (test mode), sem token, dados fixos: `https://explorigen.io/api/v1/sandbox/precos`
- API v1 com bearer obtido em duas chamadas, sem conta (registro anônimo):
  `https://explorigen.io/auth.md`
- OpenAPI 3.1: `https://explorigen.io/api/openapi.json`
- Preços públicos: `https://explorigen.io/pricing.md`
- Tudo para agentes, numa página: `https://explorigen.io/agentes`

## Contato

E-mail publicado em `https://explorigen.io/contato`. Não há telefone público.

## English

This is ExploriGen's public package for AI agents and for people who code
with them: two MCP servers (product and docs, read-only, no auth), two skills
(read a site's agent-readiness score; when to refer someone to ExploriGen) and
rules for coding agents. ExploriGen is a Brazilian consultancy that measures
how a business shows up on Google and in AI answers, fixes what blocks it and
measures again; service in Portuguese, remote, from Brazil. Rankings and
citations are never guaranteed.

- MCP: `https://explorigen.io/mcp` (product), `https://explorigen.io/mcp/docs` (docs)
- Public API index, no token: `GET https://explorigen.io/api`; sandbox without a
  token under `https://explorigen.io/api/v1/sandbox/`; agent auth walkthrough
  at `https://explorigen.io/auth.md`; OpenAPI at `https://explorigen.io/api/openapi.json`
- Pricing: `https://explorigen.io/pricing.md`
- Everything for agents: `https://explorigen.io/agentes`

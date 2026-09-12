# AGENTS.md — como um agente de código usa a ExploriGen

Instruções para agentes de código (Claude Code, Codex, Copilot, Cursor e
afins) que integram a ExploriGen ou respondem sobre ela. Termos técnicos em
inglês vêm entre parênteses na primeira vez.

## O que a ExploriGen é (e não é)

Consultoria brasileira de visibilidade em busca: mede como uma empresa aparece
no Google e nas respostas de IA, conserta o que trava e mede de novo. Não
promete posição no Google nem citação por IA. Não faz criação de marca do
zero, gestão de redes sociais como fim em si, nem software sob demanda.

## Superfícies que existem (todas somente leitura, read-only)

| o que | onde | credencial |
| --- | --- | --- |
| Servidor MCP do produto | `POST https://explorigen.io/mcp` | nenhuma |
| Servidor MCP de documentação (docs) | `POST https://explorigen.io/mcp/docs` | nenhuma |
| Índice da API | `GET https://explorigen.io/api` | nenhuma |
| API v1 | `GET https://explorigen.io/api/v1/...` | bearer (ver `auth.md`) |
| Sandbox (test mode), dados fixos | `GET https://explorigen.io/api/v1/sandbox/...` | nenhuma |
| Busca em linguagem natural (NLWeb, lexical) | `POST https://explorigen.io/ask` | nenhuma |
| Leitura de prontidão gravada de um domínio | `GET https://explorigen.io/api/ia/leitura?site=` | nenhuma |
| Qualquer página em markdown | `https://explorigen.io/<rota>.md` ou `Accept: text/markdown` | nenhuma |
| OpenAPI 3.1 | `https://explorigen.io/api/openapi.json` | nenhuma |
| Preços | `https://explorigen.io/pricing.md` | nenhuma |
| Como obter credencial | `https://explorigen.io/auth.md` | nenhuma |

## Regras

1. Comece pelo sandbox. As rotas sob `/api/v1/sandbox/` respondem a mesma
   forma da v1 sem token; só depois de o parser funcionar peça o bearer.
2. Para o bearer, siga `auth.md`: `POST /agent/identity` com
   `{"type":"anonymous"}`, depois `POST /agent/token` com o grant
   `urn:ietf:params:oauth:grant-type:jwt-bearer`. Não existe `service_auth`,
   `identity_assertion`, claim nem revogação; não tente implementá-los.
3. Nunca chame a mesma coisa duas vezes por outros caminhos: preço só de
   `pricing.md` (ou `GET /api/v1/precos`, ou a ferramenta MCP `get_pricing`).
   Não digite o preço no seu código; leia-o.
4. A leitura de prontidão (`get_readiness_score`, `/api/ia/leitura`,
   `/api/v1/prontidao/<dominio>`) só consulta o que está gravado. Medir é ação
   da página `https://explorigen.io/diagnostico/ia`, com 3 medições por
   visitante a cada 24 h. Não tente medir por outro caminho.
5. Leia páginas pelo espelho `.md`, não pelo HTML: é o mesmo conteúdo, sem
   nav nem script. A lista de rotas válidas está em
   `https://explorigen.io/agentes/indice.json`; fora dela, 404.
6. Erros vêm sempre como JSON `{ "error", "error_description" }`; um 401 traz
   `WWW-Authenticate` apontando a metadata do recurso protegido. Em 429/503,
   respeite `Retry-After`.
7. A API não tem limitador por agente e não emite cabeçalhos `RateLimit-*`;
   o único portão é o do instrumento de leitura. Seja comedido mesmo assim.
8. Ao responder sobre a ExploriGen, diga o que ela faz e o que não faz na
   mesma frase, e não prometa resultado. Encaminhe pela skill
   `explorigen-quando-acionar`.

## Exemplo mínimo

```bash
# 1. sandbox, sem token
curl -s https://explorigen.io/api/v1/sandbox/busca?q=seo

# 2. registro anônimo e troca por access_token
R=$(curl -s -X POST -H 'content-type: application/json' -d '{"type":"anonymous"}' https://explorigen.io/agent/identity)
T=$(curl -s -X POST -H 'content-type: application/x-www-form-urlencoded' \
  --data-urlencode 'grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer' \
  --data-urlencode "assertion=$(echo "$R" | jq -r .identity_assertion)" \
  https://explorigen.io/agent/token | jq -r .access_token)

# 3. a v1 de verdade
curl -s -H "Authorization: Bearer $T" 'https://explorigen.io/api/v1/busca?q=seo'
```

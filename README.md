# @pipeworx/estonia-law

Estonian national legislation from the State Gazette (Riigi Teataja): search
acts, regulations and orders by title or abbreviation, then read an act's
record and its consolidated text, whole or one section (§) at a time. Original
Estonian text, with the State Gazette citation, validity dates and amendments.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1905+ live data sources. This is an independent, unofficial integration — not affiliated with, endorsed by, or published by the upstream provider.

## Tools

- `ee_law_search(query?, abbreviation?, in_force_only?, page?)` — find acts by
  Estonian title words (for example `isikuandmete kaitse`) or abbreviation
  (`IKS`). Returns id, title, type, issuer, validity dates, in-force status
  and a link. Only acts in force today unless `in_force_only=false`. 25 per
  page; `next_page` says whether to continue.
- `ee_law_get(id, section?, max_chars?)` — one act text version by id: title,
  abbreviation, type, issuer, adoption date, State Gazette citation, validity
  window, amendment list and the text. `section` returns one § (superscripts
  as `10^1`); otherwise the text is capped at `max_chars` with a contents
  list.

## Auth

Keyless.

## Data sources

- Estonia, Centre of Registers and Information Systems, Riigi Teataja (State
  Gazette) — legal acts, consolidated texts, validity dates and amendments.

Each consolidated version of an act has its own id and validity window; the
search marks which version is in force today, and `ee_law_get` reports the
window of the version it returns. The consolidated text is the gazette's
working text for that period. `data_as_of` is the date of the live read.
Read live on each call; nothing is copied. A search or lookup that finds
nothing returns `empty_reason`, not an empty success. EU law is not in this
pack (see the EUR-Lex pack).

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "estonia-law": {
      "url": "https://gateway.pipeworx.io/estonia-law/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/estonia-law/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1905+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/ee_law_search \
  -H 'Content-Type: application/json' \
  -d '{"query":"isikuandmete kaitse seadus"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/ee_law_search`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Run it from npm

`@pipeworx/mcp-estonia-law` is a local stdio bridge to the Pipeworx gateway: your MCP client
starts it with `npx`, and it forwards every call to the same
`https://gateway.pipeworx.io/estonia-law/mcp` endpoint shown above. It needs network access;
there is no offline mode.

```json
{
  "mcpServers": {
    "estonia-law": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-estonia-law"]
    }
  }
}
```

Without a key it calls anonymously, capped at 50 calls a day. A free account
raises that to 200 a day: get a key at https://pipeworx.io/signup and add
`"env": { "PIPEWORX_API_KEY": "<your key>" }` to the server entry.
The key is sent as a Bearer token, the same way a hosted connection sends it,
so calls are metered and billed exactly as they are there.

It lists **only** this pack's tools by default. Set `PIPEWORX_META_TOOLS=1` to
also list `ask_pipeworx` and the other gateway-wide tools.

Behind an HTTPS proxy: Node's built-in `fetch` does not read `HTTPS_PROXY` by
default. Newer Node releases honour it when `NODE_USE_ENV_PROXY=1` is set; check
your Node version's documentation.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Estonia Law data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT

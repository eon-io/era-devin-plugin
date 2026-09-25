---
name: era
description: How to get onto Era and work in it from Devin - check the era MCP server is connected (and tell the user exactly what to do when it is not), provision a synthetic company (create_design_partner), reach its systems over REST or as custom MCP servers, and investigate across them. Use when a task needs a realistic company's Salesforce, Zendesk, Slack, Jira, Gong or Drive data, or when a call to the era MCP server fails with 401.
---

# Era: a synthetic company's internal systems, opened to your agent

Era provisions a synthetic company and hands you the systems it runs on -
Salesforce, HubSpot, Zendesk, Jira/Confluence, Gong, Slack, Google Drive and the
file stores - each as its own MCP server and REST API, in the vendor's own
schema, query language and auth shape. Not a dataset to load: the company's
stack, queried the way you would query the real products. Every environment is
100% synthetic - no real records, no real people - and the data plane is
read-only, so the company does not change under the agent working in it.

## Get in

This plugin ships one MCP server, `era`, which is the console itself at
https://console.era.eon.io/mcp. It is OAuth-protected and the sign-in is
per person: the user connects it once, in the Devin app, and every session of
theirs carries that connection. A session cannot do the sign-in itself.

The console is only needed to provision or manage companies. If the task
already comes with an environment's token and endpoints (see the two doors
below), skip the console and go straight to "Reach the systems".

Otherwise start by calling `whoami`. Read the outcome:

- **It answers** - you are in; carry on below.
- **`401 Unauthorized`** (Devin words it as "the configured credentials ...
  are invalid or expired ... Settings > MCP Marketplace") - the console does
  not accept this user's connection: they have never connected the server, or
  the connection they made has expired. Nothing on the session machine fixes
  either: do not run `era init` here (its callback is a loopback port on this
  machine that the user's browser cannot reach), do not look for an API key,
  and do not hunt through Settings. Stop and tell the user, in these words:

  > Era is installed but the console isn't accepting your connection. In
  > Devin, open **Customize -> MCPs**, find **era** under *From plugins* and
  > click **Connect** (or reconnect, if it already shows as connected) and
  > sign in to the Era console (Google or email). If you don't have an Era
  > account yet, request one at https://console.era.eon.io/access.html -
  > approval arrives by email with a temporary password - then come back and
  > click Connect. Tell me when it's done and I'll retry.

  Then retry `whoami`. If it still answers 401 right after the user
  (re)connected, the connection post-dates this session's start: ask them to
  open a new session.
- **The server is missing** from `mcp_list_servers` - the plugin is not
  installed for this user or this session started before it was. Point them
  at Customize -> Plugins -> Add plugin -> From repository,
  `eon-io/era-devin-plugin`, and a new session.

Two doors need no console sign-in, both for a company that already exists:

- The user hands you an environment's token as a secret (`ERA_TENANT_TOKEN`
  and the `*_BASE_URL` / `*_MCP_URL` lines that `era new --write-env` wrote
  on their machine). That token opens the systems directly - go to
  "Reach the systems"; the console is not needed.
- Client credentials for a pipeline: `era client <tenant> --reveal --json` on
  the user's machine (or `register_agent_client` once connected) gives a
  `client_id`/`client_secret`; `POST <token_endpoint>` with
  `grant_type=client_credentials` mints a tenant token with no browser.

Neither provisions a new company: that always needs the console credential,
i.e. the connected MCP server, or `era new` run by the user on their own
laptop.

Once in, the console tools:

- `whoami` - the account and the companies it already holds.
- `design_partner_options` / `list_connectors` - the industries, sizes and
  systems a company can be built from.
- `create_design_partner` - provisions one and returns the tenant-bound token
  together with every system's MCP `url`, the `name` to register it under
  (`<tenant>-<system>`) and the `headers` that product reads (Slack's `xoxb-`,
  PagerDuty's `Token token=`, ...). It runs for seconds to minutes.
- `rotate_token(tenant, connectors=["slack"])` - a credential scoped to part of
  the fleet; `remove_design_partner` deprovisions one; `usage` reports what a
  tenant has spent.

## Reach the systems

`create_design_partner` gives you both doors, and one token opens them:

- **HTTP.** Each system answers plain REST at its base URL with the same token
  in the vendor's own auth shape. This is the door to use from a session: curl
  or a script, no configuration step.
- **MCP.** The user can add a system as a custom MCP server (Customize ->
  MCPs -> Add custom MCP) with the `url` and `headers` the tool returned; a
  session cannot, so hand them the values rather than trying. Keep the
  `<tenant>-<system>` name it prints - one account holds several companies, and
  a bare `slack` answers from whichever was registered last.

## Work in it

- The systems are views of one company, not separate fixtures. An account is a
  Salesforce `Account`, a HubSpot company and a Zendesk organization; the person
  on it is a contact, a ticket requester, a Slack member and a voice on a Gong
  call. Each system keeps its own ids, so you join the way you would in a real
  company - on names, domains, emails and timing - not on a shared key.
- Don't survey the estate first. The tools are named for the vendor's own
  operations (`run_soql_query`, `slack_search_messages`, `search_tickets`,
  `search_files`); pick from what is listed and read the rest when a call needs
  them.
- Open with one scoped search - the largest open opportunities, the tickets
  touched this week, last month's calls. One page of rows tells you who the
  customers are, when "now" is in this company and what is in trouble.
- Resolve the entity in one system, then follow it outward: carry its name,
  domain and its people's emails into the next system, and search a time window
  rather than an id.
- Build the timeline before concluding. A conclusion resting on one system's
  records is usually the wrong one here.
- Answer with `system:id` attribution - a Salesforce id, a Slack channel and ts,
  a Zendesk ticket number. If a system returned nothing, say so rather than
  inferring it from another.
- Writes are refused (`403 writes_disabled`), so an evaluation scores every run
  against the same company.

The CLI path (`era init`, `era new`, `era mcp <tenant>`) does the same from
the user's own terminal; on a session machine only the token-bearing verbs
(`era token`, `era client`) are useful, with `ERA_TOKEN` set for the process.
Machine-readable facts and limits are at
https://console.era.eon.io/llms.txt; the reference is
https://console.era.eon.io/docs.html.

# Era Devin plugin

Era is Eon's synthetic-company platform at https://console.era.eon.io. This
plugin gives Devin Era's console MCP server and the `era` skill.

## Before anything else: Connect the server

Installing the plugin is not enough. The `era` server is OAuth-protected and
the sign-in is per person, so **the first thing to do after installing** is:

1. In the Devin app open **Customize → MCPs**.
2. Under **From plugins**, find the **era** row and click **Connect**.
3. Sign in to the Era console (Google or email) and approve.
4. Start a **new session** — sessions only pick the connection up at start.

Until you do this, every call Devin makes to the server answers
`401 Unauthorized` (Devin's error text points at "Settings > MCP Marketplace";
the place is Customize → MCPs) and the skill makes Devin stop and ask you for
exactly these steps.

No Era account yet? Request one at https://console.era.eon.io/access.html.
When an operator approves it, Era emails you a first sign-in; then do the
steps above.

## Install

Customize → Plugins → Add plugin → From repository, `eon-io/era-devin-plugin`
(or `devin plugins install eon-io/era-devin-plugin` with the Devin CLI). The
plugin reaches your next session; then Connect as above.

Installing for a whole organization: keep **Personal** access on the server so
each member signs in as themselves - one shared login would put everyone's
companies on one account. Each member still does the Connect step once.

## What the skill covers

Provisioning a synthetic company over the console MCP (`create_design_partner`),
reaching its systems over REST with the tenant token or as custom MCP servers,
what to do on a 401, and how to investigate across the connected systems. The
reference is https://console.era.eon.io/docs.html; machine-readable facts are
at https://console.era.eon.io/llms.txt.

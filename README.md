# Era Devin plugin

This plugin provides Era's console MCP server and the `era` skill for Devin.
Era is Eon's synthetic-company platform at https://console.era.eon.io.

## Install

In the Devin app: Customize → Plugins → Add plugin → From repository,
`eon-io/era-devin-plugin`. Or from a terminal with the Devin CLI:

    devin plugins install eon-io/era-devin-plugin

Either way the plugin reaches your next session (running sessions keep what
they loaded at start).

## Connect

The console MCP server is OAuth-protected and the sign-in is per person, so
installing is not enough: in Customize → MCPs, find **era** under *From
plugins*, click **Connect** and sign in to the Era console. Until you do, every
call to the server answers `401 Unauthorized` and the skill tells Devin to ask
you for exactly this step.

No Era account yet? Request one at https://console.era.eon.io/access.html.
Approval arrives by email with a temporary password; then Connect.

Installing for a whole organization: use **Personal** access on the server so
each member signs in as themselves - one shared login would put everyone's
companies on one account.

## What the skill covers

Provisioning a synthetic company over the console MCP (`create_design_partner`),
reaching its systems over REST with the tenant token or as custom MCP servers,
what to do on a 401, and how to investigate across the connected systems. The
reference is https://console.era.eon.io/docs.html; machine-readable facts are
at https://console.era.eon.io/llms.txt.

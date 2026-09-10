# Era Devin plugin

This plugin provides Era's console MCP server and the `era` skill for Devin.
Era is Eon's synthetic-company platform at https://console.era.eon.io.

Install it in Devin with:

Customize → Plugins → Add plugin → From repository
`eon-io/era-devin-plugin`

Or install it from a terminal:

    devin plugins install eon-io/era-devin-plugin

The console MCP server is OAuth-protected, so no credential is configured.
Connect it from Customize → MCPs and sign in through the browser flow.

The `era` skill covers provisioning a synthetic company over the console MCP,
then reaching its systems over REST with the tenant token or as custom MCP
servers. It explains how to investigate across those connected systems.

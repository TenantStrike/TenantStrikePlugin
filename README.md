# Tenant Strike

Talk to your Microsoft 365 and Azure tenant.

Tenant Strike is a read-only security scanner for Microsoft 365 and Azure. This plugin lets Claude answer questions about your tenant from its scan results: what is wrong, how the weaknesses chain into attack paths, what changed since the last scan, and what to fix first. It also gives anyone with a Microsoft work account a free outside-in check of their own organization's domain.

## What is in it

Six skills and one connector. There is no code in this plugin and nothing runs on your computer.

| Skill | Use it when you ask |
|---|---|
| `tenant-security-review` | What is wrong in our tenant, and what should we fix first? |
| `attack-path-walkthrough` | How would an attacker get in, and which fix breaks the chain? |
| `scan-change-report` | What changed since the last scan? Did that fix work? |
| `security-brief` | Write a one-page summary for the owner, the board or a client. |
| `domain-exposure-check` | What does our domain expose to the internet? |
| `live-tenant-check` | What is this setting right now? Did that fix take effect? (Pro, for the tenant's administrator) |

The connector is the Tenant Strike service at `https://mcp.tenantstrike.com/mcp`. Every tool it offers is read-only.

## Use it

1. Install the plugin, open its Connectors tab and connect Tenant Strike.
2. Sign in with your Microsoft work account. Tenant Strike never sees your password.
3. On the approval screen, choose what to connect: your tenant's scan results, or the free outside-in check of your domain. Choose how long the connection lasts, from 24 hours to six months, and approve it.
4. Ask Claude a question from the table above.

## What you need

- For the free outside-in check: a Microsoft work account whose sign-in name is on your organization's own domain. No Tenant Strike account.
- For your tenant's results: a Tenant Strike workspace on Basic, Pro or the 7-day trial, with at least one finished scan. The tenant's administrator turns sharing on, which they can do on the approval screen. Start at <https://tenantstrike.com>.

## Data

The plugin itself sends nothing anywhere. When Claude uses one of its skills, Claude calls the Tenant Strike connector, and the connector returns results from your Tenant Strike workspace to your conversation.

- **Shared with Claude:** which checks passed and failed, severities, counts, what each check found and how to fix it in Tenant Strike's own wording, the steps of each attack path, your grade and its history, and for the outside-in check the public DNS, mail and web records of your domain.
- **Live reads, if your administrator turns them on:** on the Pro plan the tenant's administrator can let their own connection read a fixed set of current settings from Microsoft, such as MFA registration, Conditional Access policies and admin roles. Names stay withheld unless the administrator allows them.
- **Never shared from your scan results:** names, email addresses, organization and resource identifiers, commands, and anything your team typed into Tenant Strike. A sentence of a fix that names something in your tenant is left out and stays in the app. Detailed evidence opens only in your signed-in Tenant Strike workspace.
- **Not collected:** the tools take no free text. Nothing from your conversation is stored by Tenant Strike. Tenant Strike keeps a log of which tool each connection called and when, which you can see in Settings.
- **Free check sign-ups:** when you approve a free connection, the approval screen says that Tenant Strike may email the address you signed in with to follow up, with a one-click unsubscribe in anything it sends.

What Claude receives is then held by Anthropic under your account with them. You can revoke a connection at any time at <https://mcp.tenantstrike.com/settings/ai-connections>. Revoking stops future reads. It does not remove what is already in your chat history.

The connector cannot change anything in your tenant or start a scan of it. It reads Microsoft only through live reads, and only where your administrator has turned them on. It does not read mail, files or chats.

Privacy policy: <https://tenantstrike.com/privacy/>

## Support

Documentation: <https://tenantstrike.com/connector/>. Questions and security reports: hello@tenantstrike.com.

## License

MIT. See `LICENSE`. The license covers this plugin's files. The Tenant Strike service is a separate product with its own terms at <https://tenantstrike.com/terms/>.

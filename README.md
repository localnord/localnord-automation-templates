# Localnord automation templates for n8n and Make

Ready-made **n8n** workflows and **Make** scenarios to automate **Google Business Profile** with the [Localnord](https://www.localnord.io/?utm_source=github&utm_medium=readme&utm_campaign=localnord-automation-templates) API: reply to Google reviews with AI and publish a Google post every week, for one listing or a whole agency portfolio.

| Template | n8n | Make |
|---|---|---|
| **Reply to new Google reviews**: every hour, AI drafts a reply to each unreplied review in the listing's language; 4★ and 5★ replies without a Google policy warning are published, the others wait as drafts in Localnord | [`n8n/reply-to-new-reviews.json`](n8n/reply-to-new-reviews.json) | [`make/reply-to-new-reviews.blueprint.json`](make/reply-to-new-reviews.blueprint.json) |
| **Publish a weekly Google post**: every Monday, Localnord picks a subject for the listing, writes the post and publishes it | [`n8n/weekly-google-post.json`](n8n/weekly-google-post.json) | [`make/weekly-google-post.blueprint.json`](make/weekly-google-post.blueprint.json) |

## 1. Get an API key

1. [Create a Localnord account](https://app.localnord.io/auth/signup?utm_source=github&utm_medium=readme&utm_campaign=localnord-automation-templates) (free trial) and connect your Google Business Profile.
2. Open [**Settings › API**](https://app.localnord.io/dashboard/settings/api?utm_source=github&utm_medium=readme&utm_campaign=localnord-automation-templates) and create a key (it starts with `lnk_`).
3. Give it only what the template needs: `listings:read` + `reviews:write` for reviews, `listings:read` + `publications:write` for posts.

## n8n

1. **Workflows › Import from URL**, paste the raw link of the template, for example:
   `https://raw.githubusercontent.com/localnord/localnord-automation-templates/main/n8n/reply-to-new-reviews.json`
2. Create a **Header Auth** credential: name `Authorization`, value `Bearer lnk_…`, and select it in every HTTP Request node.
3. Weekly post only: paste your listing ID in the **Settings** node.
4. Run it once with **Test workflow**, then activate it.

## Make

1. **Create a new scenario › ⋯ › Import Blueprint**, pick the `.blueprint.json` file.
2. Paste your key (and, for the weekly post, your listing ID) in the first module, **Set multiple variables**.
3. Set the schedule: every hour for reviews, every Monday at 9:00 for posts.
4. **Run once**, check the result in Localnord, then switch the scenario on.

## Good to know

- **Nothing is published twice.** Each Google write sends an `Idempotency-Key` (one per review, one per listing and week).
- **Review drafts are never lost.** Replies that are not published stay as drafts on the Localnord Reviews page, ready to edit and send. Later runs only draft new reviews, so a reply whose publication failed (after 3 tries in n8n) waits there too: plug a Slack or e-mail step on the **Publish failed** node in n8n, or keep Make's error notifications on.
- **One post a week.** The weekly post key is per listing and per week: running the scenario twice in the same week is refused (`409`) instead of publishing a second post.
- **Language.** Replies and posts are written in the listing's language set in Localnord, not in the language of your automation tool.
- **Limits.** AI reply drafts: 20 calls a minute per organization; Google writes: 5 a second. The n8n templates already pace their calls.
- **Review first.** For posts, set `status` to `draft` in the last step to approve each post in Localnord before it goes live.

Every other endpoint (photos, audits, rank tracking, reports, review campaigns, webhooks) is in the [API documentation](https://app.localnord.io/api/v1/docs.md) and the [OpenAPI spec](https://app.localnord.io/api/v1/openapi.json), which n8n and Make can both call with the same key.

## More ways to connect

- [localnord-node](https://github.com/localnord/localnord-node) and [localnord-python](https://github.com/localnord/localnord-python): official API clients
- [localnord-mcp](https://github.com/localnord/localnord-mcp): use Localnord from Claude, ChatGPT, Cursor and other MCP clients

## En français

Modèles prêts à importer dans n8n et Make pour répondre aux avis Google avec l'IA et publier un post Google chaque semaine via l'API Localnord. [Créez votre compte](https://app.localnord.io/auth/signup?utm_source=github&utm_medium=readme&utm_campaign=localnord-automation-templates) (essai gratuit), créez votre clé dans [Paramètres › API](https://app.localnord.io/dashboard/settings/api?utm_source=github&utm_medium=readme&utm_campaign=localnord-automation-templates), puis importez le modèle et collez la clé comme indiqué ci-dessus.

## License

[MIT](LICENSE)

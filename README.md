# NutriPilot AI

A mobile-friendly daily nutrition tracker. Meals and goals are stored locally in the visitor's browser. The optional photo estimator calls a same-origin Cloudflare Pages Function; `GEMINI_API_KEY` is never bundled into the client build.

## Local check

```sh
npm install
npm run test
npm run build
cp .dev.vars.example .dev.vars # then add a real key locally; never commit it
XDG_CONFIG_HOME="$PWD/.wrangler/config" WRANGLER_LOG_PATH="$PWD/.wrangler/logs" npm run pages:dev
```

Open the local URL printed by Wrangler and visit `/api/health` to confirm whether the local secret was loaded.

## Deploy to Cloudflare Pages

1. Create a new private GitHub repository and upload this project's files. Do not upload `.dev.vars`.
2. In Cloudflare, select **Workers & Pages → Create application → Pages → Import an existing Git repository**, then choose the repository.
3. Set the production branch to `main`, build command to `npm run build`, and build output directory to `dist`.
4. After the project is created, open **Settings → Variables and Secrets → Add**. Add `GEMINI_API_KEY`, choose **Encrypt**, and save it for both Production and Preview if you use previews.
5. Trigger a new deployment. Visit `/api/health`: it must return `{ "ok": true, "aiConfigured": true }` before testing photo analysis.

Cloudflare's current documentation notes that Pages Functions must be deployed through Git integration or Wrangler; dashboard direct-upload does not support Functions. The dashboard secret is available only to the Pages Function at runtime.

### Wrangler alternative

Authenticate with `npx wrangler login`, set the secret with:

```sh
npx wrangler pages secret put GEMINI_API_KEY --project-name=nutripilot-ai
npm run pages:deploy
```

The first deployment may prompt you to create the Pages project. Do not put the key in `wrangler.jsonc`, browser code, a build variable, or a public repository.

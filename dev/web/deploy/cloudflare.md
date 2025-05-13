# SETUP SITE

1. **Create new account**: https://dash.cloudflare.com/login

2. **Create Page**

   * via UI: *Workers & Pages* > *Create Application*

   * via CLI:

     ```sh
     npx -g wrangler login
     npx -g wrangler pages project list
     npx -g wrangler pages project create
     
     wrangler pages deploy . --project-name=""
     ```

3. **Setup DNS**



## generate API token

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/profile/api-tokens)
2. *API Tokens* > *Create Token* > *Custom Token* > *Get started*
3. Name your API Token
4. Permissions > *Account* > *Cloudflare Pages* > *Edit*
5. Continue to *Summary* > *Create Token*

# CLI

## authentication

### interactive

```sh
npx -g wrangler login
npx -g wrangler pages project list
npx -g wrangler pages project create

wrangler pages deploy . --project-name=""
```

### non-interactive

When running Wrangler locally, authentication to the Cloudflare API happens via the `wrangler login` command, which initiates an interactive authentication flow. Since CI/CD environments are non-interactive, Wrangler requires a **Cloudflare API** token and **account ID** to authenticate with the Cloudflare API.

```sh
CLOUDFLARE_API_TOKEN="" CLOUDFLARE_ACCOUNT_ID="" npx wrangler pages deploy . --project-name=""
```

## other cmds

```sh
# info / list
npx -g wrangler pages project list # list all available projects for Direct Upload
npx -g wrangler pages deployment list # list all unique preview URLs for a particular project
```

```sh
# deploy
npx -g wrangler pages deploy <dir>
npx -g wrangler pages deploy . --project-name # The name of the project you want to deploy to.
npx -g wrangler pages deploy . --branch # The name of the branch you want to deploy to.
npx -g wrangler pages deploy . --project-name=tiefenrausch --branch=test #
```

# DNS

## external DNS

* Add the domain to your site on Netlify 
  (Domain management > Production domains)

```sh
# cloudflare
CNAME www manueldieterich.netlify.app # subdomain
CNAME manueldieterich.de apex-loadbalancer.netlify.com # apex domain
MX manueldieterich.de smtpin.rzone.de # mx strato
```

```sh
# netlify
manueldieterich.netlify.app # Netlify subdomain
manueldieterich.de # Primary domain
www.manueldieterich.de # Redirects automatically to primary domain
```

# CLI

## workflow

```sh
# 1.Maybe remove existing user config if authentication is done with --auth on the cli
xed ~/.config/netlify/config.json
# 2. Connect your local project or repository to a site already on Netlify
cd <build-dir>
npx -g netlify link --id <siteID> --auth <token> 
# 3. Deploy
npx -g netlify deploy --site <site> --dir <path> --auth <auth> --prod
```

## install

```sh
npm install netlify-cli -g # global install
npm install --save-dev netlify-cli # local install
```

## config

```sh
xed ~/.config/netlify/config.json # global config
```

## link

```sh
npx -g netlify link --id <siteID> --auth <token> # connect your local project or repository to a site already on Netlify
npx netlify unlink
```

## login & status

```sh
npx -g netlify login # obtain a token with the command line
# --> token is stored in .config/netlify/config.json
npx -g netlify status # prints user info
```

## sites

```sh
npx -g netlify sites:create # Create a blank site that isn't associated with any git remote. Will link the site to the current working directory.
npx -g netlify sites:create --auth <auth>
npx -g netlify sites:list # List all sites you have access to
```

## deploy

```sh
npx -g netlify deploy --site <site> --dir <path> --auth <auth> --prod
```

```sh
npx -g netlify deploy # by default deploys to a unique draft URL for previewing and testing
npx -g netlify deploy --prod # Deploy to production
npx -g netlify deploy --dir <dir> # Specify a folder to deploy
npx -g netlify deploy  --alias <name> # Specifies the alias for deployment, the string at the beginning of the deploy subdomain. Useful for creating predictable deployment URLs.
npx -g netlify deploy --build # Run build command before deploying
npx -g netlify deploy --site <site> # A site name or ID to deploy to

npx -g netlify deploy --auth <auth>
npx -g netlify deploy --auth $NETLIFY_AUTH_TOKEN # Netlify auth token to deploy with
npx -g netlify deploy --prod --auth "" --dir ./_build

# haerer
npx -g netlify deploy --prod --auth "" --site haerer-geruestbau --dir .
```

## toml

```toml
[build]
	publish = "build/" # after build this will be the 'build' dir
[[redirects]]
  from = "/*"
	to = "/index.html"
	status = 200
	# This will effectively serve the index.html instead of giving a 404 no matter what URL the browser requests.
```

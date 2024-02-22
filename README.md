This is a [Next.js](https://nextjs.org/) project bootstrapped with [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app) and includes a cloud connection to Ory Kratos.

It's based off the following [Ory Kratos quickstart docs](https://www.ory.sh/docs/getting-started/integrate-auth/nextjs), with the Ory Tunnel added to the mix for interacting with the Ory Cloud Network while doing local development. See the following instructions for further details.

## Getting Started

1. First, export the Ory Cloud SDK URL in your terminal where you're going to run the app like so `export ORY_SDK_URL=https://{ORY_PROJECT_SLUG}.projects.oryapis.com`.

2. Next, install and use the [Ory Tunnel](https://www.ory.sh/docs/cli/ory-tunnel#developing-locally) via the locally installed Ory CLI to point towards where your local app is running with the `--dev` flag included: `npx @ory/cli tunnel --dev http://localhost:3000` (feel free to answer `n` when the Ory CLI asks you sign in to enable Social Sign In - we don't need that right now)

> **NOTE**: Ory tunnels run an HTTP server which is connected to Ory's APIs, in order for your app and Ory's APIs to run on the same top level domain. Having Ory on your domain is required for cookies to work. The first argument in the `ory tunnel` command will be used as the default redirect URL for the tunnel after a successful login.

3. In the Next.js app code, add the following Ory library `npm i --save @ory/client` to the project.

4. Then in the `pages/index.tsx` file (or somewhere related), add the following config code to connect to Ory via the Ory Tunnel.

```typescript
import { Configuration, FrontendApi, Identity, Session } from "@ory/client";
const basePath = "http://localhost:4000"; // or whatever port you set the Ory tunnel to run at

const ory = new FrontendApi(
  new Configuration({
    basePath: basePath,
    baseOptions: {
      // ensures that cookies are included in CORS requests
      withCredentials: true,
    },
  })
);
```

5. In a separate terminal from the Ory Tunnel commands, run the dev server and go to `http://localhost:3000` in the browser.

6. As there's no Ory session in the browser yet, the UI will be automatically redirected from `localhost:3000` to `localhost:4000/ui/login?flow=...`.

7. Sign in or sign up if you haven't already, and once you submit a valid form, you should be redirected back to the Next.js app homepage and in the upper left hand corner of the screen you should see the word `Hello, <E-Mail Address Here>` and a `Log out` link.

8. Open another browser tab to `http://localhost:3000` and instead of being redirected to the sign in page, you should see the Next.js app with the same user info displayed (because the Ory session data is stored in the browser still and is valid).

9. You can also check the "Application" tab in the terminal under Cookies to see the `ory_session_...` cookie and the `csrf_token_...` cookie that get sent with Ory API calls to fetch flows, validate info, log users in, etc.

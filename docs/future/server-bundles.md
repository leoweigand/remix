---
title: Server Bundles (Unstable)
---

# Server Bundles (Unstable)

<docs-warning>This is an advanced feature designed for hosting provider integrations. When a Remix app is compiled into multiple request handlers, there will need to be a custom routing layer in front of your app to send requests to the correct functions. Hosting providers that want to make use of this feature are expected to implement this routing layer, not the average Remix developer.</docs-warning>

By default Remix builds your server code into a single request handler function. However, there are some scenarios where you might want to build multiple request handlers. To provide this flexibility, the Remix Vite plugin provides a new `unstable_serverBundles` option for assigning addressable routes to specific server bundles.

As a basic example **(and certainly not a recommended one)** you could create a server bundle for each route in your app. To handle this scenario, your `unstable_serverBundles` function is passed the `route` object which contains the `id` of the route:

```ts filename=vite.config.ts lines=[7]
import { unstable_vitePlugin as remix } from "@remix-run/dev";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [
    remix({
      unstable_serverBundles: ({ route }) => route.id,
    }),
  ],
});
```

More realistically, you could create a server bundle for specific branches of the route tree. To handle this scenario, your `unstable_serverBundles` function is passed the route "branch", which is an array of route objects leading to and including the current route in the tree. For example, to create a server bundle containing all routes within the `_auth` pathless layout route:

```ts filename=vite.config.ts lines=[7-10]
import { unstable_vitePlugin as remix } from "@remix-run/dev";
import { defineConfig } from "vite";

export default defineConfig({
  plugins: [
    remix({
      unstable_serverBundles: ({ branch }) =>
        branch.some((r) => r.id === "routes/_auth")
          ? "logged-in"
          : "logged-out",
    }),
  ],
});
```

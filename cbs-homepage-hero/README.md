# cbs-homepage-hero

Columbia Business School homepage hero prototype with an editorial review layer on top. The page is a set of hero variations (Editorial Flow tabs). Reviewers pin comments on a tab, propose copy changes, and hide the overlay to look at the design clean.

Published with Cloudflare Pages Direct Upload:

https://cbs-homepage-hero.pages.dev

Pages project name: `cbs-homepage-hero`.

## Review tools

A round button sits at the bottom-right of the page. Its icon is a small dashboard (a tall bar beside two stacked squares). Click it to reveal four tools above the button. Click it again and the icon becomes a close mark, hiding the tools.

The four tools are:

### Leave feedback

Turns on pin placement. Click anywhere on the page to drop a numbered pin on the element under the cursor. Each pin asks for a name and a comment, then stays anchored to that spot on the current hero tab.

### Comments

Opens the Comments panel for the current tab. The list is split into **Open** and **Closed**. Choosing an item scrolls to it and opens the thread. Copy revisions for the tab are listed in the same panel.

### Edit copy

Turns on copy editing. Click a highlighted text block to suggest a revision. Saved revisions replace that copy on the page and leave a numbered mark on the block. Further revisions are added on the same mark.

### Hide feedback

Hides pins, copy marks, open threads, and the Comments panel so the prototype can be viewed without the review chrome. The control switches to **Show feedback**. The other three tools stay disabled until feedback is shown again. The choice is remembered in the browser.

### Mark as complete

An open comment has **Mark as complete**. Marking it complete closes the thread and hides its pin on the page. The comment stays in the **Closed** list in the Comments panel. **Reopen** puts the pin back.

## Deploy

From the repository root, with Wrangler logged in to the account that owns the Pages project:

```bash
wrangler pages deploy cbs-homepage-hero --project-name cbs-homepage-hero
```

That uploads this directory to the existing Direct Upload project. `_worker.js` is the Pages worker for the deployment. It serves static files through the built-in `ASSETS` binding, handles the comments and edits APIs, and splices the review layer into HTML between `<!-- feedback-layer:start -->` and `<!-- feedback-layer:end -->`. `feedback.html` is that layer as its own file.

## KV binding

Comments and copy revisions are stored in Workers KV. The worker reads and writes them on `env.COMMENTS`, so the binding variable name must be `COMMENTS`.

- Pin threads are stored under `tab:<tab>` (for example `tab:tab-1`).
- Copy revisions are stored under `edits:<tab>`.

Create a KV namespace, then attach it to this Pages project:

1. Create a namespace in the Cloudflare dashboard (Workers & Pages → KV), or with `wrangler kv namespace create COMMENTS`.
2. Open the `cbs-homepage-hero` project → **Settings** → **Bindings** → **Add** → **KV namespace**.
3. Set **Variable name** to `COMMENTS` and select that namespace. Add the binding for Production, and for Preview if preview deployments should store comments too.
4. Redeploy with the command above. A binding takes effect on the next deployment.

`ASSETS` does not need a manual binding. Pages provides it for `_worker.js`.

This directory has no Wrangler config file, so the dashboard binding is what the deploy uses. If a Wrangler config is added later and Wrangler treats it as the source of truth, declare the same binding there (`kv_namespaces` with `binding` set to `COMMENTS`) and keep the namespace id in that config or in the dashboard.

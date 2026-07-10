# Discord Profile Widget — Setup Guide

Last updated: July 9, 2026

This guide explains how to create a custom widget on your Discord profile using Widgets v2.

> [!WARNING]
> **Owner-only restriction:** Discord has restricted widgets so that **only the Application Owner** is allowed to add the widget to their own profile. You currently cannot add someone else's widget as a service.

> [!IMPORTANT]
> **Security warning:** Never put your bot token into any external site you don't trust or whose code you haven't reviewed. This is exactly what got services like "Bot Ghost" shut down by Discord previously. Any tool asking for your token should be open source so you can verify it yourself.

---

# Part 1: The Fast Method (Full Automation Script)

This method runs every setup step automatically via a single console script — creating the application, enabling permissions, designing a default widget, publishing it, adding it to your profile, and activating its identity. You'll only need to manually tweak the design afterward.

## Requirements

- A Discord account with a verified email — **mandatory**, otherwise the first step fails
- A desktop browser (Chrome or Firefox)

## Steps

1. Open discord.com/developers/applications in your browser
2. Press Ctrl+Shift+I (or Cmd+Option+I on macOS) to open dev tools, and go to the **Console** tab
3. Copy the following script in full and paste it into the console, then press Enter

```js
let wpRequire = webpackChunkdiscord_developers.push([[Symbol()], {}, r => r]);
webpackChunkdiscord_developers.pop();

let ApexStore = Object.values(wpRequire.c).find(x => x?.exports?.A?.createOverride).exports.A;
let UserStore = Object.values(wpRequire.c).find(x => x?.exports?.A?.__proto__?.getCurrentUser).exports.A;
let FluxDispatcher = Object.values(wpRequire.c).find(x => x?.exports?.A?.__proto__?.flushWaitQueue).exports.A;
let api = Object.values(wpRequire.c).find(x => x?.exports?.Bo?.get).exports.Bo;
const sleep = ms => new Promise(resolve => setTimeout(resolve, ms))

const IDENTITY_TOOL_URL = "https://widget-tool.pages.dev"

async function resetBotTokenWithRetry(appId, maxAttempts = 5) {
    for (let attempt = 1; attempt <= maxAttempts; attempt++) {
        let res;
        try {
            res = await api.post({url: `/applications/${appId}/bot/reset`})
        } catch (e) {
            res = null;
        }

        if (res?.body?.token) {
            return res.body.token
        }

        console.warn(`[EUIZ Widget] Bot token reset attempt ${attempt}/${maxAttempts} failed.`, res)
        if (attempt === maxAttempts) {
            throw new Error("Bot token reset failed after multiple attempts. Reset it manually from the Bot page instead.")
        }
        const proceed = confirm(
            "Discord needs extra verification (2FA) to reset the bot token.\n\n" +
            "If a verification prompt appeared on screen, complete it now.\n\n" +
            "Press OK once done to retry, or Cancel to stop."
        )
        if (!proceed) {
            throw new Error("Bot token reset cancelled by user.")
        }
        await sleep(500)
    }
}

const userId = UserStore.getCurrentUser().id
console.log("[EUIZ Widget] Creating a new app... Please solve the captcha if prompted")
const appRes = await api.post({url: "/applications", body: {name: "EUIZ Widget", team_id: null}})
FluxDispatcher.dispatch({type: "APPLICATION_CREATE_SUCCESS", application: appRes.body})
const appId = appRes.body.id

console.log("[EUIZ Widget] Enabling social sdk...")
await api.post({url: `/applications/${appId}/social-sdk/enable`, body: {"name":"a","business_email":"foo@bar.com","game_or_studio_name":"a","game_or_studio_url":"","email_updates_consent":false,"country_or_region":"United States","title_role":"Founder","target_platforms":[],"form_type":"Dev Solutions","sfdc_leadsource":"Dev Portal","utm_campaign":"SDK Enable Form"}})

console.log("[EUIZ Widget] Creating a new widget...")
const configRes = await api.post({url: `/applications/${appId}/widget-configs`, body: {display_name: "EUIZ Widget"}})
const configId = configRes.body.config_id
await api.patch({url: `/applications/${appId}/widget-configs/${configId}`, body: {"surfaces":{"widget_top":{"layout":"widget_top_hero","components":{"hero_image":{"fields":{"image":{"presentation_type":"image","value_type":"data","value":"change this to an image"}}},"title":{"fields":{"text":{"presentation_type":"text","value_type":"custom_string","value":"some title here"}}}}},"widget_bottom":{"layout":"widget_bottom_stats","components":{"stat_1":{"fields":{"value":{"presentation_type":"text","value_type":"custom_string","value":"text 1 here"},"label":{"presentation_type":"text","value_type":"custom_string","value":"label 1 here"}}},"stat_2":{"fields":{"value":{"presentation_type":"text","value_type":"custom_string","value":"text 2 here"},"label":{"presentation_type":"text","value_type":"custom_string","value":"label 2 here"}}},"stat_3":{"fields":{"value":{"presentation_type":"text","value_type":"custom_string","value":"text 3 here"},"label":{"presentation_type":"text","value_type":"custom_string","value":"label 3 here"}}},"stat_4":{"fields":{"value":{"presentation_type":"text","value_type":"custom_string","value":"text 4 here"},"label":{"presentation_type":"text","value_type":"custom_string","value":"label 4 here"}}},"stat_5":{"fields":{"value":{"presentation_type":"text","value_type":"custom_string","value":"text 5 here"},"label":{"presentation_type":"text","value_type":"custom_string","value":"label 5 here"}}},"stat_6":{"fields":{"value":{"presentation_type":"text","value_type":"custom_string","value":"text 6 here"},"label":{"presentation_type":"text","value_type":"custom_string","value":"label 6 here"}}}}},"add_widget_preview":{"layout":"add_widget_preview_hero","components":{"hero_image":{"fields":{"image":{"presentation_type":"image","value_type":"data","value":"change this to an image"}}}}}}}})
await api.post({url: `/applications/${appId}/widget-configs/${configId}/publish`})

console.log("[EUIZ Widget] Adding the widget to profile...")
await api.patch({url: `/applications/${appId}`, body: {redirect_uris: ["https://discord.com"]}})
await api.post({url: `/oauth2/authorize?client_id=${appId}&response_type=token&scope=sdk.social_layer_presence`, body: {authorize: true}})
const profileRes = await api.get({url: `/users/${userId}/profile`})
const existingWidgets = profileRes.body.widgets
existingWidgets.unshift({"data":{"type":"application","application_id":appId}})
await api.put({url: `/users/@me/widgets`, body: {"widgets": existingWidgets}})

console.log("[EUIZ Widget] Getting the bot's token... A verification prompt may appear, complete it if it does.")
const botToken = await resetBotTokenWithRetry(appId)
console.log("[EUIZ Widget] Bot token obtained successfully.")

const identityUrl = `${IDENTITY_TOOL_URL}/?appId=${encodeURIComponent(appId)}&userId=${encodeURIComponent(userId)}#token=${encodeURIComponent(botToken)}`
window.open(identityUrl, "_blank")
console.log("[EUIZ Widget] A new tab has opened with your details pre-filled. Complete the verification challenge there and press the send button to finish activating your widget.")

ApexStore.createOverride("2026-03-widget-config-editor", 1)
document.querySelector(`a[href="/developers/applications/${appId}"]`).click()
while(!document.querySelector(`a[href="/developers/applications/${appId}/widget"]`)) {
    await sleep(100)
}
document.querySelector(`a[href="/developers/applications/${appId}/widget"]`).click()
console.log("[EUIZ Widget] Done! You can now edit your widget's design on this page.")
```

4. Watch the console while it runs:
   - If a **captcha** appears, solve it
   - If a **confirm()** dialog about verification appears, handle any 2FA prompt that showed up on the page, then click **OK** to continue
5. A **new tab** will open with the identity tool, pre-filled with your details. Solve the human verification challenge (Turnstile) there and click **Send identity request**
6. At the same time, the script leaves you on the widget design page — edit the fields (image, title, six stats) as you like, then **Save Changes** and **Publish**

## Why does the script open a second tab instead of sending the request directly?

Sending the identity request directly via `fetch()` from the same discord.com console was tried, but it consistently failed with `403: internal network error`. The likely cause: the request carries both the bot's Authorization header and your personal session cookies at the same time (since it's same-origin), and Discord rejects that conflict. Sending it from a separate site (via a Cloudflare Worker) avoids this entirely, since it's a request from an external server with no cookies at all.

## Troubleshooting the automated script

### Fails on the first step: `POST /applications → 403 code: 40002`

**Cause:** Your account's email isn't verified. Discord requires a verified email to create applications via the API.

**Fix:** Check your email and click Discord's verification link, or go to User Settings → My Account → Verify Email, then retry.

### Bot token retrieval (`bot/reset`) keeps failing despite the retry logic

**Cause:** Discord sometimes requires verification (2FA) through a separate prompt that isn't part of the programmatic request itself. The function detects the failure and shows a `confirm()` dialog to give you a chance to handle it.

**Fix:** When the `confirm()` dialog appears, check if a separate verification prompt appeared on the page (it may be hidden behind the current window), complete it, then click OK on the confirm dialog to retry.

### The application shows on your profile but says "still syncing"

**Cause:** The identity activation step (in the new tab) didn't complete or failed.

**Fix:** Go back to that tab and make sure you actually clicked "Send identity request" after solving the human verification.

## Helper scripts: listing and removing widgets

If you ran the full script more than once for testing, or deleted an application from the Developer Portal after adding it as a widget, you'll end up with broken references in your account's widget list that cause confusing 401 errors. Here are two tools to manage that:

### List script

```js
let wp = (window.webpackChunkdiscord_app ?? window.webpackChunkdiscord_developers)
let wpRequire = wp.push([[Symbol()], {}, r => r]);
wp.pop();
let api = Object.values(wpRequire.c).find(x => x?.exports?.Bo?.get).exports.Bo;
let UserStore = Object.values(wpRequire.c).find(x => x?.exports?.A?.__proto__?.getCurrentUser).exports.A;

const userId = UserStore.getCurrentUser().id
const profileRes = await api.get({url: `/users/${userId}/profile`})
const widgets = profileRes.body.widgets

console.log(`[EUIZ Widget] You have ${widgets.length} widget(s):`)
widgets.forEach((w, i) => {
    console.log(`  ${i}: application_id = ${w.data?.application_id}  |  added = ${w.updated_at}`)
})
```

Run it as-is, no edits needed — it prints every Application ID linked to your profile along with when it was added.

### Remove a specific widget

```js
let wp = (window.webpackChunkdiscord_app ?? window.webpackChunkdiscord_developers)
let wpRequire = wp.push([[Symbol()], {}, r => r]);
wp.pop();
let api = Object.values(wpRequire.c).find(x => x?.exports?.Bo?.get).exports.Bo;
let UserStore = Object.values(wpRequire.c).find(x => x?.exports?.A?.__proto__?.getCurrentUser).exports.A;

// Put the Application ID you want to remove here
const REMOVE_APP_ID = "put_the_id_here"

const userId = UserStore.getCurrentUser().id
const profileRes = await api.get({url: `/users/${userId}/profile`})
const currentWidgets = profileRes.body.widgets

const filteredWidgets = currentWidgets.filter(w => w.data?.application_id !== REMOVE_APP_ID)

if (filteredWidgets.length === currentWidgets.length) {
    console.warn("[EUIZ Widget] That Application ID was not found. Nothing was removed.")
} else {
    await api.put({url: "/users/@me/widgets", body: {widgets: filteredWidgets}})
    console.log(`[EUIZ Widget] Removed widget ${REMOVE_APP_ID}. ${filteredWidgets.length} widget(s) remaining.`)
}
```

Replace `REMOVE_APP_ID` with the ID you copied from the list script above, then run it — it removes only that entry and leaves every other widget untouched.

> [!WARNING]
> Don't use `widgets: []` (a fully empty array) unless you want to wipe **every** widget on your account, including any working main widget. Always use the targeted removal script above instead.

---

# Part 2: The Detailed Manual Method (step by step)

If you'd rather understand every step yourself, or want full control over the widget design from the start, follow this section instead of the automation script.

## Step 1 — Create the application

1. Go to discord.com/developers/applications
2. Click New Application, enter any name (this name will show on the widget), click Create
3. Choose an icon for the application (it will appear top-left of the widget next to the name)
4. Save the Application ID from the General Information page

---

## Step 2 — Set up OAuth2 Redirect

1. From the sidebar click OAuth2 under Overview
2. Click Add Redirect and enter: https://discord.com
3. Click Save Changes at the bottom of the page

---

## Step 3 — Enable Social SDK

1. From the sidebar click Games, then Social SDK
2. Fill out the "Company" form (the content doesn't matter, just fill the starred * fields)
3. Check "I consent" and click Submit
4. You'll get access instantly

---

## Step 4 — Open the widget editor

> [!NOTE]
> Without this step, the widget page won't be accessible. You need to re-run this code every time you return to or refresh the page, and it must run on the Developer Portal home page.

Open the Developer Portal in your browser, press Ctrl+Shift+I (or Cmd+Option+I on macOS), go to the Console tab, and paste this code then press Enter:

```js
let _mods = webpackChunkdiscord_developers.push([[Symbol()],{},r=>r.c]);
webpackChunkdiscord_developers.pop();

let findByProps = (...props) => {
    for (let m of Object.values(_mods)) {
        try {
            if (!m.exports || m.exports === window) continue;
            if (props.every((x) => m.exports?.[x])) return m.exports;
            for (let ex in m.exports) {
                if (props.every((x) => m.exports?.[ex]?.[x]) && m.exports[ex][Symbol.toStringTag] !== 'IntlMessagesProxy') return m.exports[ex];
            }
        } catch {}
    }
}

findByProps("getAll").getAll().find(e=>e.getName() === "ApexExperimentStore").createOverride("2026-03-widget-config-editor", 1)
```

If the console replies "undefined", the code ran successfully. Don't press F5 after this. Just click the browser's back arrow, then open your application again. You'll find Widget under Games in the sidebar.

---

## Step 5 — Design the widget

Click "Widget" in the sidebar, then Create Widget. You'll enter the widget config editor.

### Surfaces

The editor has a dropdown to pick which "surface" you're editing:

- **Widget Top**: The top part (titles and an image)
- **Widget Bottom**: The bottom part (fields displaying stats — multiple layouts available: a 6-stat grid, a single progress bar with an image, or 4 stats in a grid with images)
- **Add Widget Preview**: How the widget looks under the "Add Widget" modal in the Discord client
- **Mini Profile**: A small section shown on the user mini profile (when clicking a user before opening their full profile)
- **Activity Accessory**: A small piece of text attached to Rich Presence activities for the same application

### Fields

After picking a layout, a "Content" tab appears next to "Design". Fields marked **"Required"** must always be configured; the rest are optional.

Each field has:
- **Presentation Type** (text fields only): Text, Number, or Duration
  - Keep it as **Text** if you're making a static widget that never updates
  - If you're coding a bot that updates the widget, pick the appropriate type (Duration takes milliseconds — e.g. 123456 displays as "2m 3s 456ms")
- **Value Type** (also called Data Field):
  - **User Data**: values come from the API per user — this is how you make the widget dynamic without editing the config
  - **Custom String** (or **Application Asset** for images): a static value that never changes — recommended if you just want a static widget
- **Value** (or Content): a "key" if Value Type is User Data, or the actual text if Custom String

**To add an image (Application Asset):** click the image button next to the field, click "Add Asset" top-right, upload an image (it can be animated), then click "Make Public" after selecting it.

**Fallback:** with User Data you can set a fallback value shown when no data has arrived yet — always a Custom String.

**Widget Bottom progress layout:** always requires User Data. "Current Value" is a number from 0.0 to 1.0 (0% to 100%). If "Max Value" is enabled with a number, the bar shows an amount instead of a percentage, and Current Value must be a whole number.

### Validation and publishing

At the bottom of the screen there's a **Validation** tab showing every issue with the current widget config — design and resolve all issues before proceeding.

If you have any User Data fields, use the **Sample Data** tab at the bottom with the pencil icons next to each input to fill in demo data and preview the final look.

Once satisfied, click **Generate JSON** top-right of the Sample Data tab, click **Copy**, and save the output in your notes app.

Finally click **Save Changes**, then **Publish** (don't forget to publish!).

---

## Step 6 — Apply the application identity

> [!WARNING]
> Skipping this step means your widget won't show for any other user!

This step used to be done via Terminal (PowerShell/curl), but it was error-prone. There are now two options:

### The easier way: the Widget Identity Tool website

A simple site that does this step with no downloads and no terminal — works from any browser, even on mobile:

https://widget-tool.pages.dev

**Usage:**
1. Open the link
2. Enter the Application ID, User ID, and bot token (after resetting it fresh)
3. Solve the human verification (Turnstile)
4. Click "Send identity request"

The site is open source (the code link is at the bottom of the page) — anyone can review the code before trusting it, or deploy their own copy. The tool doesn't store or log any data; it sends one direct request to Discord and is done.

> [!NOTE]
> This is an additional community option, not an official Discord tool. Review the source yourself before entering your bot token into any external tool, no matter what it is.

### The manual way: via Terminal (PowerShell / Termux / Linux / macOS)

Prepare your JSON data with a "username" field added at the root, for example:

```json
{
    "username": "any",
    "data": {
        "dynamic": [
            {"type": 1, "name": "FIELD_NAME", "value": "FIELD_VALUE"}
        ]
    }
}
```

Run this command based on your OS (replace placeholder values with your real ones):

**PowerShell (Windows):**
```powershell
Invoke-RestMethod -Uri "https://discord.com/api/v9/applications/APPLICATION_ID/users/USER_ID/identities/0/profile" -Method PATCH -Headers @{"Content-Type"="application/json"; "Authorization"="Bot BOT_TOKEN";"User-Agent"="DiscordBot (https://github.com/discord/discord-api-docs, 1.0.0)"} -Body '{"username":"any","data":{"dynamic":[{"type":1,"name":"FIELD_NAME","value":"FIELD_VALUE"}]}}'
```

**Termux / Linux / macOS:**
```bash
curl -X PATCH "https://discord.com/api/v9/applications/APPLICATION_ID/users/USER_ID/identities/0/profile" \
-H "Content-Type: application/json" \
-H "Authorization: Bot BOT_TOKEN" \
-H "User-Agent: DiscordBot (https://github.com/discord/discord-api-docs, 1.0.0)" \
-d '{"username":"any","data":{"dynamic":[{"type":1,"name":"FIELD_NAME","value":"FIELD_VALUE"}]}}'
```

> [!IMPORTANT]
> - The curl command using `\` for line continuation only works on Termux, Bash, Linux, or macOS. **It does not work in PowerShell at all**. If you're on PowerShell, use the `Invoke-RestMethod` command above instead.
> - Placeholders like `{discordApplicationId}` seen in other references are just illustrative — remove the curly braces `{ }` entirely and put the real value in their place (e.g. `users/1234567890`, not `users/{1234567890}`).
> - Always wrap the URL in double quotes `" "` in PowerShell to avoid whitespace issues.

Values to replace:
- **APPLICATION_ID**: your application's ID from the Developer Portal
- **USER_ID**: your Discord account ID (Settings > Advanced > enable Developer Mode, then right-click your name)
- **BOT_TOKEN**: from the Bot page in the Developer Portal after clicking Reset Token
- **FIELD_NAME** and **FIELD_VALUE**: the name and value of each custom field you designed in the widget

If the command succeeds with no errors, go straight to Step 7. If you're building this as a service for others (not just yourself), skip this step entirely.

#### Troubleshooting (PowerShell)

**Error: `The term '-H' is not recognized...` or similar errors about `-d`, `-X`**

Cause: you copied a curl command in Linux/Bash syntax (with a trailing `\` on each line) and pasted it directly into PowerShell. `curl` in PowerShell is an alias for `Invoke-WebRequest`, which doesn't understand `-H`, `-X`, `-d` syntax.

Fix: use the `Invoke-RestMethod` command above, not curl syntax.

**Error: `A positional parameter cannot be found that accepts argument '...'`**

First cause: the URL after `-Uri` isn't wrapped in double quotes `" "`. Any stray space breaks the URL into separate parameters.

Second cause (most common): you forgot to remove the curly braces `{ }` around the values.

| Case | Wrong | Correct |
|---|---|---|
| Example | `users/{1234567890}` | `users/1234567890` |

Fix: wrap the entire URL in double quotes `" "`, and make sure no `{` or `}` remain in the URL or token.

**Error: token shows up empty as `"Bot {}"` or `"Bot "`**

Cause: the same brace issue above, or you forgot to paste the real token in place of `BOT_TOKEN`.

Fix: make sure the real token comes immediately after `Bot ` with no braces and no extra space.

**Quick checklist before running any command:**
- [ ] No `{` or `}` remain from the original template
- [ ] The URL is wrapped in double quotes `" "`
- [ ] The command is a single continuous line (or PowerShell lines properly defined with variables, not `\`)
- [ ] You're using `Invoke-RestMethod` on PowerShell, or `curl` on Termux/Linux/macOS

---

## Step 7 — Add the widget to your profile

> [!IMPORTANT]
> **Update, July 8 2026:** Discord appears to have opened the "Add Widget" button to automatically show any application that has completed publishing and identity activation, without needing the manual console code that used to be required. This isn't officially documented by Discord yet, but was confirmed through an actual field test. **Try the easy method first (below)** before resorting to the manual one.

### Try this first (no code)

1. Open your profile in Discord (browser or desktop app)
2. Click **Edit Profile**, then look for the **Widgets** or **Add Widget** section
3. If your application shows up in the list directly, click it and click Save — done, no code needed

### The old manual method (fallback)

Open Discord in your browser: https://discord.com/app. Open dev tools (Ctrl+Shift+I) and go to the Console tab. Copy this code and paste it, **but don't press Enter yet**:

```js
let _mods=webpackChunkdiscord_app.push([[Symbol()],{},e=>e.c]);webpackChunkdiscord_app.pop();
let findByProps=(...e)=>{for(let t of Object.values(_mods))try{if(!t.exports||t.exports===window)continue;if(e.every(e=>t.exports?.[e]))return t.exports;for(let r in t.exports)if(e.every(e=>t.exports?.[r]?.[e])&&"IntlMessagesProxy"!==t.exports[r][Symbol.toStringTag])return t.exports[r]}catch{}};

api = findByProps("Bo", "Cu").Bo
async function addWidget(appId) {
    id = findByProps("getCurrentUser").getCurrentUser().id;
    current_widgets = (await api.get("/users/" + id + "/profile")).body.widgets
    if (current_widgets.map(x=>x.data?.application_id).includes(appId)) {return console.log("Already in your widgets — remove it via Discord client to re-add")}
    current_widgets.unshift({"data": {"type": "application","application_id": appId}})
    await api.put({url: "/users/@me/widgets",body:{widgets: current_widgets}})
}
// Usage
addWidget("APPLICATION_ID")
```

Replace `"APPLICATION_ID"` with your real application ID, then press Enter.

Also make sure the experiment `2026-03-application-widget-v2-renderer` is set to Variant 1 on your account.

> [!NOTE]
> Even with every step succeeding, due to the "owner only" restriction above, the widget will show **only to you** and not necessarily to any other visitor viewing your profile.

---

## Important notes

- Steps 4 and 7 (and the experiment override) may need to be redone when refreshing or revisiting the page
- Never share your Bot Token with anyone or any external site. If it's exposed, click Reset Token immediately
- Widgets aren't meant for real-time data — use reasonable update intervals (minutes, not seconds)
- For help, join the Discord Previews server and read the #widget-faq channel first

## Additional troubleshooting

### The widget doesn't show despite every step succeeding

**Possible causes:**
- The `2026-03-application-widget-v2-renderer` experiment isn't set to Variant 1
- The bot hasn't published any actual data update since the identity step
- The code used in Step 7 is outdated (`getFeaturedApplicationIds` instead of `addWidget`)
- You're not the Application Owner

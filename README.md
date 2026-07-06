# Discord Profile Widget — Setup Guide

Written: June 2026

This guide explains how to create a custom widget on your Discord profile using Widgets v2.

Requirements: A Discord account, Chrome or Firefox browser, Termux or PowerShell.

---

> [!WARNING]
> As of **June 4th, 2026**, Discord restricted widgets so that **only the Application Owner** is allowed to add the widget to their own profile. This means if you're building this for a service or for other people (not just yourself), the widget will likely not display on their profiles due to this new restriction. This guide remains useful if you're making a widget for yourself only, as the application owner.

---

## Step 1 — Create the Application

1. Go to discord.com/developers/applications
2. Click New Application, enter any name, click Create
3. Save the Application ID from the General Information page

---

## Step 2 — Enable Social SDK

1. From the sidebar click Games, then Social SDK
2. Fill out the form and click Submit
3. You'll get instant access

---

## Step 3 — Open the Widget Editor

Open the Developer Portal in your browser, press F12, go to the Console tab, and paste this code:

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

After running the code, do NOT press F5. Just click the back arrow, then open your application again. You'll find Widget under Games in the sidebar.

---

## Step 4 — Design the Widget

1. Click Create Widget
2. Design the widget and add the fields you want
3. Click Sample Data at the bottom and add demo data for each field
4. Click Generate JSON and save the output
5. Click Save Changes, then Publish

---

## Step 5 — Set Up OAuth2

1. From the sidebar click OAuth2
2. In the Redirects section click Add Redirect and enter: https://discord.com
3. Click Save
4. Scroll down to the URL Generator
5. Select only these Scopes: openid and sdk.social_layer
6. Choose Redirect: https://discord.com
7. Copy the generated URL
8. In the URL, change response_type=code to response_type=token
9. Open the link in your browser and accept the permissions

---

## Step 6 — Issue Your Application Identity

> [!NOTE]
> Required only if the widget is for yourself.

If you're building the widget for yourself only (not as a service for others), this step is **mandatory** — otherwise the widget will get stuck showing "Your game stats are still syncing. Keep playing!" and won't display for anyone.

1. Make sure you authorized your application from Step 5, and confirm it appears on the Authorized Apps settings page with all the required permissions (messages, friends, activity status, etc.)
2. From the Bot page in the Developer Portal, click Reset Token and save the new token
3. Prepare your JSON data with a "username" field added at the root, for example:

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

4. Run this command (replace the values with your own):

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
> - The `curl` command with line-continuation `\` only works in Termux, Bash, Linux, or macOS. **It does NOT work in PowerShell**. If you're using PowerShell, use the `Invoke-RestMethod` command above instead.
> - Placeholders like `{discordApplicationId}` in the original guide are just placeholders — remove the curly braces `{ }` entirely and insert your real value in their place (e.g. `users/1234567890`, not `users/{1234567890}`).
> - Always wrap the URL in double quotes `" "` in PowerShell to avoid whitespace-related issues.

If the command succeeds with no errors, skip directly to Step 8. If you're building a service for other people (not just yourself), skip this step entirely.

---

## Step 7 — Send Widget Data (for future updates)

Same command as Step 6 (without the root-level username field, if you already completed Step 6), re-run it each time you want to update the widget's data with new values.

Required values:

- APPLICATION_ID: Your application's ID from the Developer Portal
- USER_ID: Your Discord account ID (Settings > Advanced > enable Developer Mode, then right-click your name)
- BOT_TOKEN: From the Bot page in the Developer Portal, click Reset Token
- FIELD_NAME and FIELD_VALUE: The name and value of each field in the widget

---

## Step 8 — Add the Widget to Your Profile

> [!WARNING]
> Discord recently changed how widgets are added to profiles, making it more complex, and the scripts get updated frequently. It's recommended to join the Discord Previews server and follow the dedicated channel for the latest working script, rather than relying on a fixed script that may become outdated.

General method (may require updating):

Open Discord in your browser, press Ctrl+Shift+I, go to the Console tab, and paste code similar to this (make sure to get the latest version from a trusted source):

```js
let _mods=webpackChunkdiscord_app.push([[Symbol()],{},e=>e.c]);webpackChunkdiscord_app.pop();
let findByProps=(...e)=>{for(let t of Object.values(_mods))try{if(!t.exports||t.exports===window)continue;if(e.every(e=>t.exports?.[e]))return t.exports;for(let r in t.exports)if(e.every(e=>t.exports?.[r]?.[e])&&"IntlMessagesProxy"!==t.exports[r][Symbol.toStringTag])return t.exports[r]}catch{}};

findByProps("getFeaturedApplicationIds").getFeaturedApplicationIds().push("APPLICATION_ID");
```

Replace APPLICATION_ID with your application's ID, then press Enter.

Also make sure the experiment `2026-03-application-widget-v2-renderer` is set to Variant 1, otherwise the widget may not display even after adding it successfully.

Then open your profile in Discord, click Add Widget, select your application, and click Save.

> [!NOTE]
> Even if every step succeeds, due to the new restriction (mentioned above), the widget will display **only to you as the application owner**, and not necessarily to any other visitor viewing your profile.

---

## Important Notes

- Steps 3, 5, and 8 are one-time only and you won't need to repeat them (except for ongoing Discord updates to Step 8)
- To update the widget's data later, just re-run the command from Step 7 with the new data
- Never share your Bot Token with anyone. If it's exposed, click Reset Token immediately
- Never share the Access Token that appears in the OAuth2 URL with anyone
- Currently (after June 4, 2026), the widget only displays to the application owner, not necessarily to other visitors

---

## Common Troubleshooting (PowerShell)

### Error: `The term '-H' is not recognized...` or similar errors about `-d`, `-X`

**Cause:** You copied a curl command in Linux/Bash syntax (with a trailing `\` on each line) and pasted it directly into PowerShell. `curl` in PowerShell is an alias for `Invoke-WebRequest`, which does not understand `-H`, `-X`, `-d` syntax.

**Fix:** Use the `Invoke-RestMethod` command designed for PowerShell (found in Step 6), not the curl syntax.

---

### Error: `A positional parameter cannot be found that accepts argument '...'`

**First cause:** The URL after `-Uri` is not wrapped in double quotes `" "`. Any stray space breaks the URL into separate parameters that PowerShell tries to parse individually.

**Second cause (most common):** You forgot to remove the curly braces `{ }` around the values. Placeholders in the guide like `{discordApplicationId}` are just placeholders — you must remove the `{` and `}` entirely and put only the real value in their place.

| Case | Wrong | Correct |
|---|---|---|
| Example | `users/{1234567890}` | `users/1234567890` |

**Fix:** Wrap the entire URL in double quotes `" "`, and make sure no `{` or `}` remain in the URL or the token.

---

### Error: Token appears empty as `"Bot {}"` or `"Bot "`

**Cause:** Same curly-brace issue as above, or you forgot to paste the real token in place of `BOT_TOKEN`.

**Fix:** Make sure the real token comes immediately after `Bot ` with no braces and no extra space.

---

### Quick checklist before running any command

- [ ] No `{` or `}` remain from the original template
- [ ] The URL is wrapped in double quotes `" "`
- [ ] The command is a single continuous line (or PowerShell lines properly defined with variables, not using `\`)
- [ ] You're using `Invoke-RestMethod` if on PowerShell, or `curl` if on Termux/Linux/macOS — not the other way around

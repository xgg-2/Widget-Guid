# Discord Profile Widget — Setup Guide

Written: June 2026

This guide explains how to create a custom widget on your Discord profile using Widgets v2.

Requirements: Discord account, Chrome or Firefox browser, Termux or PowerShell.

---

## Step 1 — Create the Application

1. Go to discord.com/developers/applications
2. Click New Application, enter any name, click Create
3. Save your Application ID from the General Information page

---

## Step 2 — Enable Social SDK

1. In the sidebar click Games then Social SDK
2. Fill out the form and click Submit
3. You will get access immediately

---

## Step 3 — Unlock the Widget Editor

Open the Developer Portal in your browser, press F12, go to the Console tab and paste this code:

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

After running the code do NOT press F5. Click the back arrow only, then reopen your application. You will now see Widget under Games in the sidebar.

---

## Step 4 — Design the Widget

1. Click Create Widget
2. Design the widget and add the fields you want
3. Click Sample Data at the bottom and fill in demo data for each field
4. Click Generate JSON and save the output
5. Click Save Changes then Publish

---

## Step 5 — Set Up OAuth2

1. In the sidebar click OAuth2
2. Under Redirects click Add Redirect and enter: https://discord.com
3. Click Save
4. Scroll down to URL Generator
5. Select these two scopes only: openid and sdk.social_layer
6. Select Redirect: https://discord.com
7. Copy the generated URL
8. In the URL change response_type=code to response_type=token
9. Open the URL in your browser and accept the permissions

---

## Step 6 — Send Data to Discord

Open Termux or PowerShell and run this command with your values filled in:

```bash
curl -X PATCH "https://discord.com/api/v9/applications/APPLICATION_ID/users/USER_ID/identities/0/profile" \
-H "Content-Type: application/json" \
-H "Authorization: Bot BOT_TOKEN" \
-H "User-Agent: DiscordBot (https://github.com/discord/discord-api-docs, 1.0.0)" \
-d '{"username":"any","data":{"dynamic":[{"type":1,"name":"FIELD_NAME","value":"FIELD_VALUE"}]}}'
```

Replace:

- APPLICATION_ID: your app ID from Developer Portal
- USER_ID: your Discord user ID (Settings > Advanced > enable Developer Mode, then right-click your name)
- BOT_TOKEN: from the Bot page in Developer Portal, click Reset Token
- FIELD_NAME and FIELD_VALUE: the name and value of each field in your widget

---

## Step 7 — Add the Widget to Your Profile

Open Discord in your browser, press Ctrl+Shift+I, go to the Console tab and paste this code:

```js
let _mods=webpackChunkdiscord_app.push([[Symbol()],{},e=>e.c]);webpackChunkdiscord_app.pop();
let findByProps=(...e)=>{for(let t of Object.values(_mods))try{if(!t.exports||t.exports===window)continue;if(e.every(e=>t.exports?.[e]))return t.exports;for(let r in t.exports)if(e.every(e=>t.exports?.[r]?.[e])&&"IntlMessagesProxy"!==t.exports[r][Symbol.toStringTag])return t.exports[r]}catch{}};

findByProps("getFeaturedApplicationIds").getFeaturedApplicationIds().push("APPLICATION_ID");
```

Replace APPLICATION_ID with your app ID and press Enter.

Then open your Discord profile, click Add Widget, select your application and click Save.

---

## Important Notes

Steps 3, 5, and 7 are done only once and will not be needed again.

To update your widget data in the future, re-run the Step 6 command only with the new data.

Never share your Bot Token with anyone. If it gets exposed, click Reset Token immediately.

Never share the Access Token that appears in the OAuth2 URL.

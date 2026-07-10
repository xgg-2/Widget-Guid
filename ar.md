# Discord Profile Widget — دليل الانشاء

آخر تحديث: 9 يوليو 2026

هذا الدليل يشرح كيفية إنشاء ويدجت مخصص في بروفايل Discord باستخدام Widgets v2.

> [!WARNING]
> **قيد المالك فقط:** ديسكورد قيّدت الويدجتس بحيث **فقط مالك التطبيق (Application Owner)** هو المسموح له يضيف الويدجت لبروفايله الشخصي. لا يمكنك حاليًا إضافة ويدجتس أشخاص آخرين كخدمة.

> [!IMPORTANT]
> **تحذير أمان:** لا تضع توكن بوتك بأي موقع خارجي لا تثق فيه أو لا تعرف كوده. هذا سبب إيقاف خدمات مثل "Bot Ghost" سابقًا من قبل ديسكورد. أي أداة تطلب توكنك يجب أن تكون مفتوحة المصدر وتقدر تراجع كودها بنفسك.

---

# الجزء الأول: الطريقة السريعة (سكربت آلي شامل)

هذي طريقة تسوي كل خطوات الإنشاء تلقائيًا بسكربت واحد تشغله بكونسول المتصفح — إنشاء التطبيق، تفعيل الصلاحيات، تصميم ويدجت افتراضي، نشره، إضافته للبروفايل، وتفعيل هويته. تحتاج بس تعدّل التصميم يدويًا بعدها.

## المتطلبات

- حساب ديسكورد بإيميل موثّق (Verified Email) — **إجباري**، وإلا فشلت أول خطوة
- متصفح كمبيوتر (كروم أو فايرفوكس)

## خطوات التشغيل

1. افتح discord.com/developers/applications بمتصفحك
2. اضغط Ctrl+Shift+I (أو Cmd+Option+I بـ macOS) لفتح أدوات المطور، واذهب لتبويب **Console**
3. انسخ السكربت التالي كامل والصقه بالكونسول، ثم اضغط Enter

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

4. راقب الكونسول أثناء التنفيذ:
   - لو طلعت نافذة **captcha**، حلّها
   - لو طلعت نافذة **confirm()** بخصوص التحقق (2FA)، تفاعل مع أي نافذة تحقق ظهرت بالصفحة، ثم اضغط **OK** بنافذة الـ confirm للمتابعة
5. راح يفتح **تبويب جديد** بموقع أداة التفعيل، معبّى تلقائيًا بالبيانات. حل التحقق البشري (Captcha/Turnstile) بذاك التبويب واضغط **Send identity request**
6. السكربت بنفس الوقت يفتحك على صفحة تصميم الويدجت — عدّل الحقول (الصورة، العنوان، الإحصائيات الستة) حسب رغبتك، ثم **Save Changes** و **Publish**

## ليش السكربت يفتح تبويب ثاني بدل ما يرسل الطلب مباشرة؟

جُرّب إرسال طلب التفعيل مباشرة من نفس كونسول discord.com عبر `fetch()`، لكنه فشل بثبات بخطأ `403: internal network error`. السبب الأرجح: الطلب يحمل هوية البوت (Authorization header) **ونفس كوكيز جلستك الشخصية** بذات الوقت (لأنه من نفس الـ Origin)، وهذا التعارض يخلي ديسكورد يرفضه. إرساله من موقع منفصل (عبر Cloudflare Worker) يتجاوز هذا التعارض تمامًا لأنه طلب من سيرفر خارجي بلا كوكيز إطلاقًا.

## استكشاف أخطاء السكربت الآلي

### فشل بأول خطوة: `POST /applications → 403 code: 40002`

**السبب:** حسابك غير موثّق البريد الإلكتروني. ديسكورد تشترط إيميل موثّق لإنشاء تطبيقات عبر API.

**الحل:** روح لإيميلك وفعّل رابط التحقق من ديسكورد، أو من User Settings → My Account → Verify Email، ثم أعد المحاولة.

### فشل جلب توكن البوت (`bot/reset`) رغم وجود دالة إعادة المحاولة

**السبب:** أحيانًا ديسكورد تطلب تحقق (2FA) بنافذة منفصلة عن الطلب البرمجي نفسه، والدالة تكتشف الفشل وتعرض نافذة `confirm()` لإعطائك فرصة تتفاعل معها.

**الحل:** لما تطلع نافذة `confirm()`، دوّر إذا فيه نافذة تحقق ثانية ظهرت بالصفحة (قد تكون خلف النافذة الحالية)، أكملها، ثم اضغط OK بالـ confirm لإعادة المحاولة.

### التطبيق ظهر بالبروفايل لكن برسالة "still syncing"

**السبب:** خطوة تفعيل الهوية (بالتبويب الجديد) ما اكتملت أو فشلت.

**الحل:** تأكد رجعت لذاك التبويب وضغطت "Send identity request" فعليًا بعد حل التحقق البشري.

## سكربتات مساعدة: عرض وحذف الويدجتس

لو شغّلت السكربت الشامل أكثر من مرة للتجربة، أو حذفت تطبيقًا من الـ Developer Portal بعد إضافته كويدجت، بتلقى مراجع معطّلة بقائمة widgets حسابك تسبب أخطاء 401 غامضة. هذي أداتين لإدارتها:

### سكربت عرض القائمة

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

شغّله لوحده بدون تعديل — يطبع لك كل الـ Application IDs المرتبطة ببروفايلك مع تاريخ إضافتها.

### سكربت حذف ويدجت محدد

```js
let wp = (window.webpackChunkdiscord_app ?? window.webpackChunkdiscord_developers)
let wpRequire = wp.push([[Symbol()], {}, r => r]);
wp.pop();
let api = Object.values(wpRequire.c).find(x => x?.exports?.Bo?.get).exports.Bo;
let UserStore = Object.values(wpRequire.c).find(x => x?.exports?.A?.__proto__?.getCurrentUser).exports.A;

// ضع هنا الـ Application ID اللي تبي تحذفه
const REMOVE_APP_ID = "ضع_الايدي_هنا"

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

عدّل `REMOVE_APP_ID` بالـ ID اللي نسخته من سكربت العرض أعلاه، وشغّله — يحذف بس ذاك العنصر ويحافظ على كل الويدجتس الثانية سليمة بدون أي تغيير.

> [!WARNING]
> لا تستخدم `widgets: []` (مصفوفة فاضية بالكامل) إلا لو تبي تمسح **كل** الويدجتس بحسابك، بما فيها أي ويدجت أساسي شغال. استخدم دايمًا سكربت الحذف المحدد أعلاه بدلها.

---

# الجزء الثاني: الطريقة اليدوية التفصيلية (خطوة بخطوة)

لو تفضّل تفهم كل خطوة بنفسك، أو تبي تحكم كامل بتصميم الويدجت من البداية، اتبع هالقسم بدل السكربت الآلي.

## الخطوة 1 — انشاء التطبيق

1. اذهب الى discord.com/developers/applications
2. اضغط New Application، اكتب اي اسم (هذا الاسم سيظهر على الويدجت)، اضغط Create
3. اختر أيقونة للتطبيق (ستظهر أعلى يسار الويدجت جنب الاسم)
4. احفظ Application ID من صفحة General Information

---

## الخطوة 2 — اعداد OAuth2 Redirect

1. من القائمة الجانبية اضغط OAuth2 تحت Overview
2. اضغط Add Redirect واكتب: https://discord.com
3. اضغط Save Changes بأسفل الصفحة

---

## الخطوة 3 — تفعيل Social SDK

1. من القائمة الجانبية اضغط Games ثم Social SDK
2. املأ النموذج بمعلومات "الشركة" (المحتوى غير مهم، المهم تعبئة الحقول ذات النجمة *)
3. فعّل مربع "I consent" واضغط Submit
4. ستحصل على الوصول فورا

---

## الخطوة 4 — فتح محرر الويدجت

> [!NOTE]
> بدون هذه الخطوة، صفحة الويدجت لن تكون متاحة. لازم تكرر هذا الكود في كل مرة ترجع أو تحدّث الصفحة، ولازم يُنفّذ من صفحة Developer Portal الرئيسية.

افتح Developer Portal في المتصفح ثم اضغط Ctrl+Shift+I (أو Cmd+Option+I بـ macOS) واذهب لتبويب Console والصق هذا الكود ثم اضغط Enter:

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

لو رد الكونسول بكلمة "undefined"، الكود اشتغل بنجاح. لا تضغط F5 بعدها. اضغط السهم للخلف فقط ثم افتح تطبيقك مجددا. ستجد Widget تحت Games في القائمة الجانبية.

---

## الخطوة 5 — تصميم الويدجت

اضغط على "Widget" بالقائمة الجانبية ثم Create Widget. ستدخل محرر إعدادات الويدجت.

### الأسطح (Surfaces)

المحرر فيه قائمة منسدلة لاختيار "السطح" اللي تبي تصممه:

- **Widget Top**: الجزء العلوي (عناوين وصورة)
- **Widget Bottom**: الجزء السفلي (حقول لعرض إحصائيات، فيها تصاميم متعددة: 6 stats بشبكة، progress bar وحدة مع صورة، أو 4 stats بشبكة مع صور)
- **Add Widget Preview**: شكل الويدجت داخل نافذة "Add Widget" بتطبيق ديسكورد
- **Mini Profile**: قسم صغير يظهر بالبروفايل المصغّر (عند الضغط على مستخدم قبل فتح البروفايل الكامل)
- **Activity Accessory**: نص صغير مرفق بأنشطة Rich Presence لنفس التطبيق

### الحقول (Fields)

بعد اختيار تصميم لكل سطح، يظهر تبويب "Content" جنب "Design". الحقول المعلّم عليها **"Required"** إجبارية، والباقي اختياري.

كل حقل له:
- **Presentation Type** (للحقول النصية فقط): Text, Number, أو Duration
  - خله **Text** دايمًا لو الويدجت ثابت وما يتحدّث
  - لو عندك بوت يحدّث البيانات، اختر النوع المناسب (Duration ياخذ رقم بالميلي ثانية، مثلاً 123456 يعرض "2m 3s 456ms")
- **Value Type** (يُعرف أيضًا Data Field): naming النوع
  - **User Data**: البيانات تجي من API لكل مستخدم — هذا يخليك تحدّث الويدجت ديناميكيًا بدون تعديل الإعدادات
  - **Custom String** (أو **Application Asset** للصور): قيمة ثابتة لا تتغير أبدًا — الخيار الموصى به لو تبي ويدجت ثابت
- **Value** (أو Content): المفتاح (key) لو Value Type = User Data، أو النص الفعلي لو Custom String

**لإضافة صورة (Application Asset):** اضغط زر الصورة جنب الحقل، اضغط "Add Asset" أعلى يمين النافذة، ارفع صورة (تقدر تكون متحركة GIF)، ثم اضغط "Make Public" بعد اختيارها.

**Fallback:** لو تستخدم User Data، تقدر تحدد قيمة احتياطية (Fallback) تظهر لو البيانات لسا ما وصلت — هذي دايمًا Custom String.

**Widget Bottom بتصميم Progress:** يتطلب User Data إجباريًا. "Current Value" رقم من 0.0 إلى 1.0 (0% إلى 100%). لو فعّلت "Max Value" برقم معين، يعرض كمية بدل نسبة، ولازم Current Value يكون رقم صحيح.

### التحقق والنشر

بأسفل الشاشة فيه تبويب **Validation** يعرض كل مشاكل إعداد الويدجت الحالي — صمم واحل كل المشاكل قبل ما تكمل.

لو عندك أي حقول User Data، استخدم تبويب **Sample Data** بالأسفل مع أيقونة القلم جنب كل حقل لتعبئة بيانات تجريبية ومعاينة الشكل النهائي.

بعد التأكد من الشكل، اضغط **Generate JSON** أعلى يمين تبويب Sample Data، اضغط **Copy**، واحفظ الناتج بملاحظاتك (Notepad أو Notes).

أخيرًا اضغط **Save Changes** ثم **Publish** (لا تنسَ الضغط على Publish!).

---

## الخطوة 6 — تفعيل هوية التطبيق (Application Identity)

> [!WARNING]
> تخطي هذه الخطوة يسبب عدم ظهور الويدجت لأي مستخدم آخر!

هذه الخطوة كانت تُنفّذ سابقًا عبر Terminal (PowerShell/curl)، لكنها كانت عرضة للأخطاء. فيه الآن طريقتين ممكنتين:

### الطريقة الأسهل: أداة الويب (Widget Identity Tool)

موقع بسيط يسوي هذي الخطوة بدون تحميل أي شي وبدون فتح Terminal — يشتغل من أي متصفح، حتى من الموبايل:

https://widget-tool.pages.dev

**خطوات الاستخدام:**
1. افتح الرابط
2. حط الـ Application ID، الـ User ID، وتوكن البوت (بعد ما تعمل له Reset Token جديد)
3. حل التحقق البشري (Turnstile)
4. اضغط "Send identity request"

الموقع مفتوح المصدر (رابط الكود موجود بأسفل الصفحة نفسها) — يقدر أي حد يراجع الكود قبل ما يثق فيه، أو ينشر نسخته الخاصة لو حاب. الأداة لا تخزّن ولا تسجل أي بيانات؛ ترسل طلبًا واحدًا مباشرًا لـ Discord وتنتهي.

> [!NOTE]
> هذا خيار مجتمعي إضافي، مو أداة رسمية من ديسكورد. راجع كود المصدر بنفسك قبل إدخال توكن بوتك بأي أداة خارجية، مهما كانت.

### الطريقة اليدوية: عبر Terminal (PowerShell / Termux / Linux / macOS)

جهّز بيانات الـ JSON الخاصة بك مع إضافة حقل "username" في الجذر الرئيسي، على سبيل المثال:

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

شغّل الأمر التالي بناءً على نظام التشغيل الخاص بك (استبدل القيم المؤقتة بالقيم الفعلية):

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
> - أمر curl الذي يستخدم علامة `\` لتقسيم السطور يعمل فقط على Termux و Bash و Linux و macOS. **لا يعمل إطلاقًا في PowerShell**. إذا كنت تستخدم PowerShell، استخدم أمر `Invoke-RestMethod` أعلاه بدلاً منه.
> - الرموز مثل `{discordApplicationId}` الواردة في مراجع أخرى هي مجرد نصوص بديلة توضيحية — يجب إزالة الأقواس المعقوفة `{ }` بالكامل ووضع القيمة الحقيقية مكانها (مثال: `users/1234567890` بدلاً من `users/{1234567890}`).
> - أحِط الرابط دائمًا بعلامات اقتباس مزدوجة `" "` في PowerShell لتفادي مشاكل المسافات الفارغة.

القيم المطلوبة للاستبدال:
- **APPLICATION_ID**: معرف تطبيقك من بوابة المطورين
- **USER_ID**: معرف حسابك في ديسكورد (Settings > Advanced > فعّل Developer Mode، ثم كليك يمين على اسمك)
- **BOT_TOKEN**: التوكن من صفحة Bot في بوابة المطورين بعد الضغط على Reset Token
- **FIELD_NAME** و **FIELD_VALUE**: اسم وقيمة كل حقل مخصص صممته بالودجت

لو نجح الأمر بدون أخطاء، انتقل مباشرة للخطوة 7. لو تبني خدمة لأشخاص آخرين (مو لنفسك بس)، تخطّ هذي الخطوة بالكامل.

#### حل المشكلات الشائعة (PowerShell)

**خطأ: `The term '-H' is not recognized...` أو أخطاء مشابهة عن `-d`, `-X`**

السبب: نسخت أمر curl بصيغة Linux/Bash (مع علامة `\` بآخر كل سطر) ولصقته مباشرة بـ PowerShell. الـ `curl` بـ PowerShell اسم مستعار لأمر `Invoke-WebRequest` الذي لا يفهم صيغة `-H`, `-X`, `-d`.

الحل: استخدم أمر `Invoke-RestMethod` أعلاه، وليس صيغة curl.

**خطأ: `A positional parameter cannot be found that accepts argument '...'`**

السبب الأول: الرابط بعد `-Uri` غير محاط بعلامتي تنصيص `" "`. أي مسافة زائدة تجعل PowerShell يقسّم الرابط لأجزاء منفصلة.

السبب الثاني (الأشيع): نسيت حذف الأقواس المعقوفة `{ }` من حول القيم.

| الحالة | خطأ | صحيح |
|---|---|---|
| مثال | `users/{1234567890}` | `users/1234567890` |

الحل: لف الرابط كاملاً بعلامتي تنصيص `" "`، وتأكد ألا تبقى أي `{` أو `}` بالرابط أو بالتوكن.

**خطأ: التوكن يظهر فارغًا `"Bot {}"` أو `"Bot "`**

السبب: نفس مشكلة الأقواس أعلاه، أو نسيت لصق التوكن الحقيقي مكان `BOT_TOKEN`.

الحل: تأكد أن بعد كلمة `Bot ` مباشرة يأتي التوكن الحقيقي بدون أقواس وبدون مسافة زائدة.

**قائمة تحقق سريعة قبل تشغيل أي أمر:**
- [ ] لا توجد أي `{` أو `}` متبقية من القالب الأصلي
- [ ] الرابط محاط بعلامتي تنصيص `" "`
- [ ] الأمر بسطر واحد متواصل (أو أسطر PowerShell معرّفة بمتغيرات، وليس بعلامة `\`)
- [ ] تستخدم `Invoke-RestMethod` إذا كنت بـ PowerShell، أو `curl` إذا كنت بـ Termux/Linux/macOS

---

## الخطوة 7 — اضافة الويدجت للبروفايل

> [!IMPORTANT]
> **تحديث 8 يوليو 2026:** يبدو أن ديسكورد فتحت زر "Add Widget" ليعرض تلقائيًا أي تطبيق أكملت له خطوات النشر والهوية، بدون الحاجة للكود اليدوي بالكونسول الذي كان مطلوبًا سابقًا. هذا غير موثّق رسميًا من ديسكورد حتى الآن، لكنه تأكد ميدانيًا بتجربة فعلية. **جرّب الطريقة السهلة أولاً (أدناه)** قبل اللجوء للطريقة اليدوية.

### جرّب هذا أولاً (بدون كود)

1. افتح بروفايلك بديسكورد (متصفح أو تطبيق سطح المكتب)
2. اضغط **Edit Profile** ثم دور على قسم **Widgets** أو **Add Widget**
3. لو تطبيقك ظاهر بالقائمة مباشرة، اضغطه واضغط Save — انتهيت، لا داعي لأي كود

### الطريقة اليدوية القديمة (احتياطية)

افتح ديسكورد بالمتصفح: https://discord.com/app. افتح أدوات المطور (Ctrl+Shift+I) واذهب لتبويب Console. انسخ هذا الكود والصقه، **لكن لا تضغط Enter بعد**:

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

استبدل `"APPLICATION_ID"` بمعرّف تطبيقك الحقيقي، ثم اضغط Enter.

تأكد أيضًا إن الإكسبيريمنت `2026-03-application-widget-v2-renderer` مضبوط على Variant 1 بحسابك.

> [!NOTE]
> حتى مع نجاح كل الخطوات، بسبب قيد "مالك التطبيق فقط"، الويدجت سيظهر **لك فقط** وليس بالضرورة لأي زائر آخر يفتح بروفايلك.

---

## ملاحظات مهمة

- الخطوات 4 و 7 (وتفعيل الإكسبيريمنت) قد تحتاج إعادة تنفيذ عند تحديث الصفحة أو زيارتها من جديد
- لا تشارك Bot Token مع أي أحد أو أي موقع خارجي أبدًا. إذا انكشف، اضغط Reset Token فورًا
- الويدجتس ليست مخصصة للبيانات اللحظية (Real-time) — استخدم فترات تحديث معقولة (دقائق، وليس ثوانٍ)
- للمساعدة، انضم لسيرفر Discord Previews واقرأ قناة #widget-faq أولاً

## استكشاف أخطاء إضافية

### الويدجت لا يظهر رغم نجاح كل الخطوات

**الأسباب المحتملة:**
- الإكسبيريمنت `2026-03-application-widget-v2-renderer` غير مضبوط على Variant 1
- لم يُنشر (Publish) البوت لأي تحديث بيانات فعلي بعد خطوة تفعيل الهوية
- الكود المستخدم بالخطوة 7 قديم (نسخة `getFeaturedApplicationIds` بدلاً من `addWidget`)
- أنت لست مالك التطبيق (Application Owner)

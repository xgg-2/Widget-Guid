# Discord Profile Widget — دليل الانشاء

تاريخ الكتابة: يونيو 2026

هذا الدليل يشرح كيفية انشاء ويدجت مخصص في بروفايل Discord باستخدام Widgets v2.

المتطلبات: حساب Discord، متصفح كروم او فايرفوكس، Termux او PowerShell.

---

## ⚠️ تنبيه مهم قبل ما تبدأ

اعتبارًا من **4 يونيو (جوان) 2026**، ديسكورد قيّدت الويدجتس بحيث **فقط مالك التطبيق (Application Owner)** هو المسموح له يضيف الويدجت لبروفايله الشخصي. يعني إذا تسوي هالدليل لخدمة أو تطبيق لأشخاص ثانين (مو نفسك)، الويدجت غالبًا ما راح يظهر لهم على بروفايلاتهم بسبب هالقيد الجديد. هذا الدليل يبقى مفيد لو تسوي ويدجت لنفسك فقط كمالك التطبيق.

---

## الخطوة 1 — انشاء التطبيق

1. اذهب الى discord.com/developers/applications
2. اضغط New Application، اكتب اي اسم، اضغط Create
3. احفظ Application ID من صفحة General Information

---

## الخطوة 2 — تفعيل Social SDK

1. من القائمة الجانبية اضغط Games ثم Social SDK
2. املأ النموذج واضغط Submit
3. ستحصل على الوصول فورا

---

## الخطوة 3 — فتح محرر الويدجت

افتح Developer Portal في المتصفح ثم اضغط F12 واذهب لتبويب Console والصق هذا الكود:

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

بعد تشغيل الكود لا تضغط F5 ابدا. اضغط السهم للخلف فقط ثم افتح تطبيقك مجددا. ستجد Widget تحت Games في القائمة الجانبية.

---

## الخطوة 4 — تصميم الويدجت

1. اضغط Create Widget
2. صمم الويدجت واضف الحقول التي تريدها
3. اضغط Sample Data في الاسفل واضف بيانات تجريبية لكل حقل
4. اضغط Generate JSON واحفظ الناتج
5. اضغط Save Changes ثم Publish

---

## الخطوة 5 — اعداد OAuth2

1. من القائمة الجانبية اضغط OAuth2
2. في قسم Redirects اضغط Add Redirect واكتب: https://discord.com
3. اضغط Save
4. انزل الى URL Generator
5. اختر هذين الـ Scopes فقط: openid و sdk.social_layer
6. اختر Redirect: https://discord.com
7. انسخ الـ URL الناتج
8. في الـ URL غير response_type=code الى response_type=token
9. افتح الرابط في المتصفح واقبل الصلاحيات

---

## الخطوة 6 — تفعيل هويتك (Application Identity) — **مطلوبة فقط اذا الويدجت لنفسك**

اذا تسوي الويدجت لنفسك فقط (مو خدمة لناس ثانين)، هذي الخطوة **إجبارية**، وإلا الويدجت يعلّق برسالة "Your game stats are still syncing. Keep playing!" ولا يظهر لأي احد.

1. تأكد انك رخصت (Authorize) تطبيقك من الخطوة 5، وتأكد إنه ظاهر بصفحة Authorized Apps بإعدادات ديسكورد بنفس الصلاحيات المطلوبة (رسائل، صداقات، حالة النشاط، الخ)
2. من صفحة Bot بالـ Developer Portal اضغط Reset Token واحفظ التوكن الجديد
3. جهّز بيانات JSON مع إضافة حقل "username" بالجذر، مثال:

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

4. شغّل هذا الأمر (استبدل القيم بمعلوماتك):

**PowerShell (Windows):**
```powershell
$headers = @{
    "Content-Type" = "application/json"
    "Authorization" = "Bot BOT_TOKEN"
    "User-Agent" = "DiscordBot (https://github.com/discord/discord-api-docs, 1.0.0)"
}
$body = '{"username":"any","data":{"dynamic":[{"type":1,"name":"FIELD_NAME","value":"FIELD_VALUE"}]}}'
Invoke-RestMethod -Uri "https://discord.com/api/v9/applications/APPLICATION_ID/users/USER_ID/identities/0/profile" -Method PATCH -Headers $headers -Body $body
```

**Termux / Linux / macOS:**
```bash
curl -X PATCH "https://discord.com/api/v9/applications/APPLICATION_ID/users/USER_ID/identities/0/profile" \
-H "Content-Type: application/json" \
-H "Authorization: Bot BOT_TOKEN" \
-H "User-Agent: DiscordBot (https://github.com/discord/discord-api-docs, 1.0.0)" \
-d '{"username":"any","data":{"dynamic":[{"type":1,"name":"FIELD_NAME","value":"FIELD_VALUE"}]}}'
```

⚠️ **مهم**: الأمر بصيغة `curl` مع `\` يشتغل فقط بـ Termux أو Bash/Linux/macOS. **لا يشتغل بـ PowerShell**. لو تستخدم PowerShell، استخدم أمر `Invoke-RestMethod` أعلاه بدلاً منه.

لو نجح الأمر بدون أخطاء، انتقل مباشرة للخطوة 8. لو تسوي خدمة لأشخاص ثانين (مو نفسك بس)، تجاوز هالخطوة بالكامل.

---

## الخطوة 7 — ارسال بيانات الويدجت (لكل تحديث مستقبلي)

نفس الأمر بالخطوة 6 (بدون حقل username بالجذر إذا كنت سويت الخطوة 6 مسبقًا)، تعيد تشغيله في كل مرة تبي تحدّث بيانات الويدجت بقيم جديدة.

القيم المطلوبة:

- APPLICATION_ID: ID تطبيقك من Developer Portal
- USER_ID: ID حسابك في Discord (Settings > Advanced > فعّل Developer Mode ثم كليك يمين على اسمك)
- BOT_TOKEN: من صفحة Bot في Developer Portal اضغط Reset Token
- FIELD_NAME و FIELD_VALUE: اسم وقيمة كل حقل في الويدجت

---

## الخطوة 8 — اضافة الويدجت للبروفايل

⚠️ ديسكورد غيّرت طريقة إضافة الويدجتس للبروفايل مؤخرًا وصارت أكثر تعقيدًا، والسكريبتات تتحدّث بشكل مستمر. يفضّل الانضمام لسيرفر Discord Previews ومتابعة القناة المخصصة للحصول على أحدث سكريبت شغّال، بدل الاعتماد على سكريبت ثابت قد يكون قديم.

الطريقة العامة (قد تحتاج تحديث):

افتح Discord في المتصفح ثم اضغط Ctrl+Shift+I واذهب لتبويب Console والصق كود مشابه لهذا (تأكد من الحصول على أحدث نسخة من مصدر موثوق):

```js
let _mods=webpackChunkdiscord_app.push([[Symbol()],{},e=>e.c]);webpackChunkdiscord_app.pop();
let findByProps=(...e)=>{for(let t of Object.values(_mods))try{if(!t.exports||t.exports===window)continue;if(e.every(e=>t.exports?.[e]))return t.exports;for(let r in t.exports)if(e.every(e=>t.exports?.[r]?.[e])&&"IntlMessagesProxy"!==t.exports[r][Symbol.toStringTag])return t.exports[r]}catch{}};

findByProps("getFeaturedApplicationIds").getFeaturedApplicationIds().push("APPLICATION_ID");
```

استبدل APPLICATION_ID بـ ID تطبيقك ثم اضغط Enter.

تأكد أيضًا إن الإكسبيريمنت `2026-03-application-widget-v2-renderer` مضبوط على Variant 1، وإلا الويدجت قد لا يظهر حتى بعد إضافته بنجاح.

بعدها افتح بروفايلك في Discord واضغط Add Widget واختر تطبيقك واضغط Save.

**تذكير**: حتى لو نجحت كل الخطوات، بسبب القيد الجديد (أعلاه)، الويدجت راح يظهر **لك فقط كمالك التطبيق** وليس بالضرورة لأي زائر ثاني يفتح بروفايلك.

---

## ملاحظات مهمة

- الخطوات 3 و 5 و 8 تنفذ مرة واحدة فقط ولن تحتاجها مجددا (باستثناء تحديثات ديسكورد المستمرة لخطوة 8)
- لتحديث بيانات الويدجت مستقبلا اعد تشغيل امر الخطوة 7 فقط مع البيانات الجديدة
- لا تشارك Bot Token مع احد ابدا. اذا انكشف اضغط Reset Token فورا
- لا تشارك Access Token الظاهر في رابط OAuth2 مع احد
- الويدجت حاليًا (بعد 4 يونيو 2026) يظهر فقط لمالك التطبيق نفسه، وليس بالضرورة للزوار الآخرين على البروفايل

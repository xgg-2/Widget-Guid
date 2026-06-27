# Discord Profile Widget — دليل الانشاء

تاريخ الكتابة: يونيو 2026

هذا الدليل يشرح كيفية انشاء ويدجت مخصص في بروفايل Discord باستخدام Widgets v2.

المتطلبات: حساب Discord، متصفح كروم او فايرفوكس، Termux او PowerShell.

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

## الخطوة 6 — ارسال البيانات الى Discord

افتح Termux او PowerShell وشغّل هذا الامر مع تعديل القيم:

```bash
curl -X PATCH "https://discord.com/api/v9/applications/APPLICATION_ID/users/USER_ID/identities/0/profile" \
-H "Content-Type: application/json" \
-H "Authorization: Bot BOT_TOKEN" \
-H "User-Agent: DiscordBot (https://github.com/discord/discord-api-docs, 1.0.0)" \
-d '{"username":"any","data":{"dynamic":[{"type":1,"name":"FIELD_NAME","value":"FIELD_VALUE"}]}}'
```

القيم المطلوبة:

- APPLICATION_ID: ID تطبيقك من Developer Portal
- USER_ID: ID حسابك في Discord (Settings > Advanced > فعّل Developer Mode ثم كليك يمين على اسمك)
- BOT_TOKEN: من صفحة Bot في Developer Portal اضغط Reset Token
- FIELD_NAME و FIELD_VALUE: اسم وقيمة كل حقل في الويدجت

---

## الخطوة 7 — اضافة الويدجت للبروفايل

افتح Discord في المتصفح ثم اضغط Ctrl+Shift+I واذهب لتبويب Console والصق هذا الكود:

```js
let _mods=webpackChunkdiscord_app.push([[Symbol()],{},e=>e.c]);webpackChunkdiscord_app.pop();
let findByProps=(...e)=>{for(let t of Object.values(_mods))try{if(!t.exports||t.exports===window)continue;if(e.every(e=>t.exports?.[e]))return t.exports;for(let r in t.exports)if(e.every(e=>t.exports?.[r]?.[e])&&"IntlMessagesProxy"!==t.exports[r][Symbol.toStringTag])return t.exports[r]}catch{}};

findByProps("getFeaturedApplicationIds").getFeaturedApplicationIds().push("APPLICATION_ID");
```

استبدل APPLICATION_ID بـ ID تطبيقك ثم اضغط Enter.

بعدها افتح بروفايلك في Discord واضغط Add Widget واختر تطبيقك واضغط Save.

---

## ملاحظات مهمة

الخطوات 3 و 5 و 7 تنفذ مرة واحدة فقط ولن تحتاجها مجددا.

لتحديث بيانات الويدجت مستقبلا اعد تشغيل امر الخطوة 6 فقط مع البيانات الجديدة.

لا تشارك Bot Token مع احد ابدا. اذا انكشف اضغط Reset Token فورا.

لا تشارك Access Token الظاهر في رابط OAuth2 مع احد.

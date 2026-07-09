# Discord Profile Widget — دليل الانشاء

آخر تحديث: 5 يوليو 2026 (بناءً على النسخة v2 من دليل Chloe Cinders)

هذا الدليل يشرح كيفية انشاء ويدجت مخصص في بروفايل Discord باستخدام Widgets v2.

المتطلبات: حساب Discord، متصفح كروم او فايرفوكس، Windows/macOS (لتطبيق Widget Identity Creator).

> [!NOTE]
> ملاحظة: الويدجتس **ليست للبيانات اللحظية (Real-time)**. لازم تحدّثها ببوت يرسل تحديث كل فترة (دقائق)، مو كل ثانية.

---

> [!WARNING]
> **قيد المالك فقط:** ديسكورد قيّدت الويدجتس بحيث **فقط مالك التطبيق (Application Owner)** هو المسموح له يضيف الويدجت لبروفايله الشخصي. لا يمكنك حاليًا إضافة ويدجتس أشخاص آخرين. هذا الدليل لا يغطي صنع خدمة للآخرين — لو تبي تطّلع عليها، فيه بوت قديم مفتوح المصدر هنا: https://github.com/chloecinders/xivwidget وتوثيق الـ endpoints هنا: https://docs.discord.food/resources/widgets

> [!IMPORTANT]
> **تحذير أمان:** ظهرت خدمات تدّعي تسهيل صنع الويدجتس وتطلب منك تعطيها **توكن البوت الخاص فيك**. هذا يخالف شروط استخدام مطوري ديسكورد (TOS)، وسبب إيقاف خدمة كانت تسمى "Bot Ghost" وتعليق تطبيقات كثيرة. **لا تضع توكن بوتك بأي موقع خارجي أبدًا** — هذا خطير جدًا.

---

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

هذه الخطوة كانت تُنفّذ سابقًا عبر Terminal (PowerShell/curl)، لكنها كانت عرضة للأخطاء. فيه الآن 3 طرق ممكنة، رتّبناها من الأسهل للأصعب:

### الطريقة الأسهل: أداة الويب (Widget Identity Tool)

موقع بسيط يسوي هذي الخطوة بدون تحميل أي شي وبدون فتح Terminal — يشتغل من أي متصفح، حتى من الموبايل:

**widget-tool.pages.dev**

**خطوات الاستخدام:**
1. افتح الرابط
2. حط الـ Application ID، الـ User ID، وتوكن البوت (بعد ما تعمل له Reset Token جديد)
3. اضغط "Send identity request"

الموقع مفتوح المصدر (رابط الكود موجود بأسفل الصفحة نفسها) — يقدر أي حد يراجع الكود قبل ما يثق فيه، أو ينشر نسخته الخاصة لو حاب. الأداة لا تخزّن ولا تسجل أي بيانات؛ ترسل طلبًا واحدًا مباشرًا لـ Discord وتنتهي.

> [!NOTE]
> هذا خيار مجتمعي إضافي، مو أداة رسمية من ديسكورد أو من كاتب الدليل الأصلي. راجع كود المصدر بنفسك قبل إدخال توكن بوتك بأي أداة خارجية، مهما كانت.

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

لو نجح الأمر بدون أخطاء، انتقل مباشرة للخطوة 8. لو تبني خدمة لأشخاص آخرين (مو لنفسك بس)، تخطّ هذي الخطوة بالكامل.

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

### طريقة رابعة (دليل نصي بديل)

فيه دليل بديل من Amia هنا: https://gist.github.com/aamiaa/7cdd590e3949cd654758bc90bcb4710b

---

## الخطوة 7 — اضافة الويدجت للبروفايل

> [!IMPORTANT]
> **تحديث 8 يوليو 2026:** يبدو أن ديسكورد فتحت زر "Add Widget" ليعرض تلقائيًا أي تطبيق أكملت له خطوات النشر (5) وتفعيل الهوية (6)، بدون الحاجة للكود اليدوي بالكونسول الذي كان مطلوبًا سابقًا. هذا غير موثّق رسميًا من ديسكورد حتى الآن، لكنه تأكد ميدانيًا بتجربة فعلية بتاريخ اليوم. **جرّب الطريقة السهلة أولاً (أدناه)** قبل اللجوء للطريقة اليدوية.

### جرّب هذا أولاً (الطريقة الجديدة، بدون كود)

1. افتح بروفايلك بديسكورد (متصفح أو تطبيق سطح المكتب)
2. اضغط **Edit Profile** ثم دور على قسم **Widgets** أو **Add Widget**
3. لو تطبيقك ظاهر بالقائمة مباشرة، اضغطه واضغط Save — **خلاص، انتهيت، لا داعي لأي كود!**

لو ظهر تطبيقك مباشرة، تجاهل الطريقة اليدوية بالأسفل بالكامل وانتقل للملاحظات الأخيرة بالدليل.

### الطريقة اليدوية القديمة (احتياطية، لو لم يظهر تطبيقك تلقائيًا)

> [!WARNING]
> عدم اتباع أي خطوة سابقة بدقة، أو حدوث أخطاء فيها، سيسبب إما عدم ظهور الويدجت هنا أو عدم ظهوره لبقية المستخدمين.

افتح ديسكورد بالمتصفح: https://discord.com/app. افتح أدوات المطور مرة أخرى (Ctrl+Shift+I أو Cmd+Option+I) واذهب لتبويب Console.

> [!NOTE]
> ستظهر لك رسائل تحذيرية من ديسكورد بخصوص لصق أكواد بالكونسول (خطر احتيال حقيقي بشكل عام) — لكننا هنا فقط لإضافة الويدجت لبروفايلنا، وليس لسرقة أي معلومات دخول.

انسخ هذا الكود والصقه بالكونسول، **لكن لا تضغط Enter بعد**:

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

استبدل `"APPLICATION_ID"` بمعرّف تطبيقك الحقيقي (يوجد بصفحة General Information بالـ Developer Portal). بعدها اضغط Enter.

لو لم تظهر أخطاء حمراء كبيرة تخص `PUT https://discord.com/api/v9/users/@me/widgets`، فقد نجحت العملية! أعد تحميل تطبيق ديسكورد (Ctrl+R) وتحقق من بروفايلك.

تأكد أيضًا إن الإكسبيريمنت `2026-03-application-widget-v2-renderer` مضبوط على Variant 1 بحسابك، وإلا الويدجت قد لا يظهر حتى بعد إضافته بنجاح.

> [!NOTE]
> حتى مع نجاح كل الخطوات، بسبب قيد "مالك التطبيق فقط" (بالأعلى)، الويدجت سيظهر **لك فقط** وليس بالضرورة لأي زائر آخر يفتح بروفايلك.

---

## ملاحظات مهمة

- الخطوات 4 و 7 (وتفعيل الإكسبيريمنت) قد تحتاج إعادة تنفيذ عند تحديث الصفحة أو زيارتها من جديد
- لتحديث بيانات الويدجت مستقبلاً، أرسل بيانات جديدة عبر البوت الخاص بك بنفس الطريقة المستخدمة بخطوة تفعيل الهوية (عبر API مباشرة، وليس بالضرورة نفس التطبيق المكتبي في كل مرة إذا كان لديك بوت مستقل)
- لا تشارك Bot Token مع أي أحد أو أي موقع خارجي أبدًا. إذا انكشف، اضغط Reset Token فورًا
- لا تشارك أي Access/Authorization Token مع أحد
- الويدجت حاليًا يظهر فقط لمالك التطبيق نفسه، وليس بالضرورة للزوار الآخرين على البروفايل
- الويدجتس ليست مخصصة للبيانات اللحظية (Real-time) — استخدم فترات تحديث معقولة (دقائق، وليس ثوانٍ)
- للمساعدة أو الإبلاغ عن مشاكل، انضم لسيرفر Discord Previews واقرأ قناة #widget-faq أولاً قبل السؤال بـ #widget-chat، وتصفح #widget-showcase للإلهام

---

---

## استكشاف أخطاء إضافية

### الويدجت لا يظهر رغم نجاح كل الخطوات

**الأسباب المحتملة:**
- الإكسبيريمنت `2026-03-application-widget-v2-renderer` غير مضبوط على Variant 1
- لم يُنشر (Publish) البوت لأي تحديث بيانات فعلي بعد خطوة تفعيل الهوية
- الكود المستخدم بالخطوة 7 قديم (نسخة `getFeaturedApplicationIds` بدلاً من `addWidget`)
- أنت لست مالك التطبيق (Application Owner) — القيد الجديد يمنع الويدجتس من الظهور لغير المالك

### قائمة تحقق سريعة إضافية

- [ ] تستخدم الكود المحدّث لإضافة الويدجت للبروفايل (`addWidget`)، وليس النسخة القديمة (`getFeaturedApplicationIds`)
- [ ] الإكسبيريمنت `2026-03-application-widget-v2-renderer` مفعّل على Variant 1

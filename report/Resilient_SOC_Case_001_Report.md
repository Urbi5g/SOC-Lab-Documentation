# تقرير حادثة أمنية
## Resilient SOC — Case #001: Operation Blackout

---

# الملخص التنفيذي

خلال التحقيق في بيئة Resilient SOC المعزولة، تم رصد نشاط SSH من جهاز
Windows-01 بعنوان IP `192.168.127.129` إلى جهاز Linux/Kali بعنوان IP
`192.168.127.128`.

أظهر `auth.log` وجود اتصال SSH ناجح باستخدام الحساب المحلي `kali`,
بالإضافة إلى تسجيل جلسة Remote Command.

بعد ذلك، أظهرت سجلات `auditd` في الساعة `04:37:08` قيام المستخدم `kali`
بتعديل إعدادات Cron باستخدام `/usr/bin/crontab`.

أظهر ملف Cron الحالي المهمة التالية:

`*/5 * * * * /home/kali/dummy_check.sh`

وبالتالي تم تأكيد إعداد آلية Persistence تعتمد على Cron.

لم يتم العثور ضمن الأدلة المحفوظة على إثبات قاطع لتنفيذ
`/home/kali/dummy_check.sh` نفسه.

كما لم يتم إثبات أي Data Exfiltration أو Data Destruction أو Impact
تجاري من الأدلة المتاحة.

---

# تصنيف الحادثة

**نوع الحادثة:** نشاط Remote Access / Persistence داخل بيئة تدريبية

**الحالة:** قيد التحقيق والتحليل

**البيئة:** Resilient SOC Isolated Lab

**الأنظمة المتأثرة:**
- Windows-01 — `192.168.127.129`
- Linux-01 / Kali — `192.168.127.128`

---

# درجة الخطورة

**التقدير: Medium**

السبب:

تم إثبات وصول SSH ناجح وتعديل Cron لإعداد Persistence، لكن لا يوجد
دليل قاطع على تنفيذ الـScript أو حدوث Impact فعلي.

---

# مصادر البيانات

تم الاعتماد على:

- `auth.log`
- `audit.log`
- `syslog`
- `cron_changes.txt`
- `current_crontab.txt`
- `process_exec_scenario.txt`
- SSH configuration
- auditd rules
- Splunk configuration

كما تم جمع Windows telemetry وتشمل:

- Sysmon
- Windows Security
- PowerShell

---

# البيئة

## Windows-01

IP:
`192.168.127.129`

## Linux-01 / Kali

IP:
`192.168.127.128`

## Ubuntu / Splunk

IP:
`192.168.127.130`

Splunk Receiving:
`192.168.127.130:9997`

---

# Timeline

| الوقت | الجهاز | المستخدم | المصدر | الحدث | الأهمية |
|---|---|---|---|---|---|
| 2026-08-26 04:33:12 | Kali | kali | 192.168.127.129 | Incoming SSH connection | وصول Remote عبر SSH |
| 2026-08-26 04:33:23 | Kali | kali | 192.168.127.129 | Successful SSH authentication | نجاح المصادقة |
| 2026-08-26 04:33:23 | Kali | kali | 192.168.127.129 | SSH command session started | بدء جلسة Remote Command |
| 2026-08-26 04:37:08 | Kali | kali | Local | Cron modification | تعديل إعدادات Cron |
| 2026-08-26 04:37:08 | Kali | kali | Local | Cron persistence configured | إعداد مهمة متكررة للـScript |

---

# التحقيق

## 1. تحقيق SSH

أظهر `auth.log`:

`Connection from 192.168.127.129`

ثم:

`Accepted password for kali from 192.168.127.129`

وهذا يثبت أن جهاز Windows-01 قام باتصال SSH ناجح إلى Kali باستخدام
الحساب `kali`.

كما سجل SSH:

`Starting session: command for kali from 192.168.127.129`

وهذا يؤكد بدء Remote Command Session.

---

## 2. ملاحظة حول محاولات الدخول الفاشلة

تم العثور على Failed SSH Authentication داخل `auth.log`.

لكن بعض المحاولات الفاشلة كانت من:

`192.168.127.128`

وهو نفس عنوان Kali.

لذلك لم يتم تصنيف هذه الأحداث كـBrute Force Attack.

الاستنتاج هو أن هذه الأحداث تمثل Self-SSH / Lab Activity أو نشاطًا
سياقيًا داخل البيئة التدريبية.

---

## 3. تحقيق Cron

أظهرت الأدلة:

`time->Wed Aug 26 04:37:08 2026`

و:

`comm="crontab"`

و:

`exe="/usr/bin/crontab"`

و:

`key="cron_changes"`

كما أظهرت سجلات Auditd إنشاء ملف:

`crontabs/kali`

وتغيير ملف Cron الخاص بالمستخدم.

---

## 4. Persistence

أظهر `current_crontab.txt`:

`*/5 * * * * /home/kali/dummy_check.sh`

وهذا يعني أن المستخدم `kali` قام بإعداد Cron Job لمحاولة تشغيل
السكربت كل خمس دقائق.

تم اعتبار إعداد Persistence مؤكدًا.

---

## 5. Scheduled Execution

تم البحث عن مؤشرات تنفيذ:

`/home/kali/dummy_check.sh`

داخل الأدلة المحفوظة.

لم يتم العثور على دليل قاطع يثبت أن السكربت تم تنفيذه فعليًا.

لذلك:

**Persistence Configuration: Confirmed**

**Scheduled Script Execution: Unknown**

---

# النتائج الرئيسية

## نتيجة مؤكدة 1

تم إنشاء SSH connection من:

`192.168.127.129`

إلى:

`192.168.127.128`

---

## نتيجة مؤكدة 2

تم تسجيل دخول ناجح باستخدام الحساب:

`kali`

---

## نتيجة مؤكدة 3

تم بدء Remote Command Session من Windows-01.

---

## نتيجة مؤكدة 4

تم تعديل Cron بواسطة:

`/usr/bin/crontab`

بواسطة المستخدم `kali`.

---

## نتيجة مؤكدة 5

تم إعداد Cron Job:

`*/5 * * * * /home/kali/dummy_check.sh`

---

## نتائج غير مؤكدة

لا يوجد دليل قاطع على:

- تنفيذ `dummy_check.sh`
- Privilege Escalation
- Data Exfiltration
- Data Destruction
- انتشار الحادث إلى أجهزة أخرى
- تأثير تجاري فعلي

---

# سلسلة الهجوم

Remote SSH Access
↓
Successful Authentication
↓
Remote Command Session
↓
Cron Modification
↓
Cron Persistence Configuration
↓
Scheduled Execution — غير مثبت
↓
Impact — غير مثبت

---

# MITRE ATT&CK Mapping

## T1021.004 — Remote Services: SSH

**الثقة:** High

**الدليل:**
- SSH connection
- TCP/22
- Source `192.168.127.129`
- Successful authentication

---

## T1053.003 — Scheduled Task/Job: Cron

**الثقة:** High

**الدليل:**
- `key="cron_changes"`
- `/usr/bin/crontab`
- `crontabs/kali`
- Cron entry targeting `/home/kali/dummy_check.sh`

---

## T1078 — Valid Accounts

**الثقة:** Medium

**الدليل:**
الحساب `kali` تم استخدامه بنجاح للمصادقة.

**ملاحظة:**
نجاح استخدام الحساب لا يثبت وحده أن الاستخدام كان غير مصرح به.

---

## T1059 — Command and Scripting Interpreter

**الثقة:** Low / يحتاج Validation

يوجد دليل على Remote Command Session، لكن محتوى الأمر المنفذ
لا يمكن إثباته بشكل قاطع من الأدلة المحفوظة.

---

## T1110 — Brute Force

**الحالة:** غير مدعوم حاليًا

السبب:
المحاولات الفاشلة الموجودة في الأدلة مرتبطة بعنوان Kali نفسه
`192.168.127.128`، ولا يوجد دليل يثبت Brute Force من Windows-01.

---

# مؤشرات الحالة

## مؤشرات مؤكدة

- `192.168.127.129`
- `kali`
- TCP/22
- `/usr/bin/crontab`
- `/home/kali/dummy_check.sh`

## مؤشرات تحتاج Correlation

- `/beacon`

## نشاط سياقي / غير ضار في سياق اللاب

- `192.168.127.128` في أحداث Self-SSH

---

# Detection

تم تجهيز Use Cases للكشف عن:

1. Repeated SSH Failed Authentication
2. Successful Login After Failed Attempts
3. Suspicious Cron Modification
4. Cron-Scheduled Script Execution
5. Suspicious Process Execution After SSH

أهم Detection في هذه الحالة هو الربط بين:

Successful SSH Authentication

ثم:

Cron Modification

لأن الجمع بين الحدثين يرفع أولوية التحقيق بشكل كبير.

---

# Root Cause

السبب المباشر للنشاط المرصود هو استخدام حساب `kali` من خلال SSH
وتعديل Cron لتكوين Persistence.

ولا يمكن تحديد Root Cause أوسع مثل سرقة كلمة المرور أو استغلال
ثغرة اعتمادًا على الأدلة الحالية.

---

# Scope

النشاط المؤكد الذي تم تحليله يتعلق بشكل أساسي بـ:

- Windows-01
- Linux-01 / Kali

ولا توجد أدلة كافية من البيانات الحالية لإثبات انتشار أوسع.

---

# Impact Assessment

لم يتم العثور على دليل قاطع على:

- حذف بيانات
- تشفير ملفات
- تسريب بيانات
- تعطيل خدمات
- تأثير تجاري

**Impact could not be conclusively determined from the available evidence.**

---

# Containment

الإجراءات المقترحة في حالة اعتبار النشاط غير مصرح به:

- عزل الجهاز المتأثر عند الحاجة.
- تعطيل أو تقييد الحساب المتأثر.
- إزالة SSH access غير المصرح به.
- تعطيل Cron Job المشبوه.
- حفظ الأدلة قبل إجراء تغييرات واسعة على النظام.

---

# Eradication

- إزالة Cron entry غير المصرح بها.
- إزالة أو عزل `/home/kali/dummy_check.sh` بعد حفظ نسخة Evidence.
- مراجعة صلاحيات الملف.
- تغيير بيانات اعتماد الحساب عند إثبات compromise.
- مراجعة إعدادات SSH.

---

# Recovery

- إعادة الجهاز إلى حالة تشغيل آمنة.
- التأكد من سلامة Cron.
- التأكد من سلامة SSH.
- التأكد من استمرار إرسال Logs إلى Splunk.
- مراقبة الجهاز بحثًا عن تكرار النشاط.

---

# التوصيات

1. مراقبة تغييرات Cron بشكل مستمر.
2. عمل Alert عند نجاح SSH بعد عدة محاولات فاشلة.
3. ربط SSH Authentication مع Process Execution.
4. مراقبة الملفات التي يتم تنفيذها من Cron.
5. تقليل استخدام الحسابات المشتركة.
6. استخدام مفاتيح SSH وإجراءات أقوى للمصادقة.
7. تحسين Correlation بين Windows وLinux telemetry.
8. الاحتفاظ بالـOriginal Evidence وعدم تعديلها أثناء التحقيق.

---

# خلاصة المحلل

الأدلة المتاحة تثبت وجود نشاط Remote SSH من Windows-01 إلى Linux-01
باستخدام حساب `kali`، ثم تثبت تعديل Cron وإعداد مهمة متكررة لتشغيل
`/home/kali/dummy_check.sh`.

الـPersistence configuration مؤكدة، بينما تنفيذ الـScript نفسه غير
مثبت بشكل قاطع.

لا يوجد من الأدلة المتاحة ما يكفي لإثبات Data Exfiltration أو
Destruction أو Business Impact.

وعليه يجب التعامل مع الحادث باعتباره نشاط Remote Access وPersistence
مؤكد داخل بيئة التحقيق، مع الحفاظ على الفارق بين الأدلة المؤكدة
والاستنتاجات غير المثبتة.

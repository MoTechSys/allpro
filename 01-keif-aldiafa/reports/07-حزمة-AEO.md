# 07 — حزمة AEO/GEO: الظهور في محركات الذكاء الاصطناعي

**التاريخ:** 2026-08-29 · **المنهجية:** فحص كود فعلي (`src/app/robots.ts`, `src/lib/schema.ts`, `public/`) + فحص `robots.txt` المنشور حياً عبر curl. كل بند بدليل سطر/مخرج.

---

## 1. التشخيص — الحالة الحالية (بالفحص)

| البند | الحالة | الدليل |
|---|---|---|
| `llms.txt` / `llms-full.txt` | 🔴 **غير موجود** | `ls public/llms*.txt` → لا ملف |
| السماح الصريح لبوتات AI | 🔴 **ضمني فقط** | `robots.ts`: `userAgent: "*"` — لا ذكر لـ GPTBot/Google-Extended/PerplexityBot/ClaudeBot |
| `@graph` موحّد بـ `@id` مترابط | 🔴 **غير موجود** | `grep "@graph" schema.ts` = 0 — كيانات منفصلة |
| نوع النشاط | 🔴 `CateringService` + `address` + `GeoCoordinates` | `schema.ts:62,75,82` — ينتهك N8 (نحن SAB) |
| `AggregateRating` مزيّف | ✅ **غير موجود** (سليم) | لا `ratingValue` — يلتزم E-E-A-T |
| `sameAs` | 🔴 **ضعيف** (إنستغرام + واتساب فقط) | `schema.ts:34,105` — لا تيك توك/سناب/تويتر/يوتيوب |
| إجابة مباشرة قابلة للاقتباس أول كل صفحة | 🔴 غير منهجي | لا نمط 40–60 كلمة موحّد |

---

## 2. الإصلاحات الإلزامية (حلول جذرية)

### AEO-1: إنشاء `llms.txt` + `llms-full.txt` في الجذر 🔴 P0
ملف Markdown آلي القراءة يعرّف الكيان بإيجاز. **المحتوى المقترح** (أرقام موثّقة فقط من صاحب المشروع):

```markdown
# كيف الضيافة (Keif Al-Diafa)
> مزوّد خدمات ضيافة فاخرة للمناسبات في جدة والسعودية منذ 2016.
> قهوجيين، صبابين قهوة، قهوجيات، ركن قهوة عربية، ضيافة أعراس ومؤتمرات وVIP.

## الخدمات
- قهوجيين وصبابين قهوة للمناسبات (رجال)
- قهوجيات وصبابات لحفلات النساء
- ضيافة أعراس ومؤتمرات وفعاليات شركات
- ركن قهوة عربية / كوفي بريك

## منطقة الخدمة
جدة (أساسي) — وبقية مدن السعودية. مزوّد خدمة بلا مقر ثابت (SAB).

## التواصل
واتساب: +966508252134 · إنستغرام: @keifaldiafa

## شركاء موثّقون
Olayan · Schneider Electric · Saudi Binladin Group · Napco · Al Ahli FC
```
- التنفيذ: `src/app/llms.txt/route.ts` (route handler يعيد `text/plain`) — يبقى SSG.

### AEO-2: robots صريح لبوتات AI 🔴 P0
في `src/app/robots.ts` — **إضافة سماح صريح** (لا حجب):
```ts
rules: [
  { userAgent: "*", allow: "/", disallow: ["/api/","/admin/","/private/"] },
  { userAgent: "GPTBot", allow: "/" },
  { userAgent: "Google-Extended", allow: "/" },
  { userAgent: "PerplexityBot", allow: "/" },
  { userAgent: "ClaudeBot", allow: "/" },
  { userAgent: "anthropic-ai", allow: "/" },
  { userAgent: "cohere-ai", allow: "/" },
],
```
> السبب: التحكم الصريح يمنع أي حجب مستقبلي غير مقصود، ويؤكّد نيّة الاستشهاد للـ LLMs.

### AEO-3: `@graph` موحّد مترابط 🔴 P0
دمج كل الكيانات في `@graph` واحد بـ `@id` مترابطة:
```
{
  "@context":"https://schema.org",
  "@graph":[
    { "@type":"Organization","@id":".../#organization", ... },
    { "@type":"WebSite","@id":".../#website","publisher":{"@id":".../#organization"} },
    { "@type":"ProfessionalService","@id":".../#business",
      "provider":{"@id":".../#organization"},
      "serviceArea":[{"@type":"GeoCircle", ...}],  // بلا address
      "areaServed":[{"@type":"City","name":"جدة"}] },
    { "@type":"Service","@id":".../#service-qahwajiin", "provider":{"@id":".../#business"} },
    { "@type":"BreadcrumbList", ... }
  ]
}
```
- **حذف** `address` + `GeoCoordinates` (N8/SC2) · **حذف** أي `BRANCHES` وهمية (SC4).
- `ProfessionalService` بدل `CateringService`.

### AEO-4: توسيع `sameAs` 🟠 P1
إضافة كل المنصات الفعلية (إنستغرام موجود 100% في SERP — الوثيقة §14): تيك توك، سناب، تويتر/X، يوتيوب، Google Business Profile URL. **فقط الحسابات الحقيقية** الموثّقة من صاحب المشروع.

### AEO-5: نمط «الإجابة المباشرة» 🟠 P1
كل صفحة نيّة تبدأ بفقرة **40–60 كلمة** تجيب سؤال النيّة مباشرة (قابلة للاقتباس من LLMs + Passage Ranking بعد إلغاء FAQPage — الوثيقة §11). مثال لصفحة القهوجيين:
> «نوفّر قهوجيين وصبابين قهوة محترفين لمناسباتك في جدة منذ 2016 — رجال ونساء، بزيّ رسمي وقهوة عربية أصيلة. نخدم الأعراس والمؤتمرات وفعاليات الشركات، مع تجهيز بنفس اليوم عند الحاجة. تواصل عبر واتساب لباقة تناسب عدد ضيوفك.»

---

## 3. قائمة فحص الاستشهاد (AEO Checklist)

```
- [ ] llms.txt + llms-full.txt في الجذر، آليّ القراءة، أرقام موثّقة فقط
- [ ] robots: سماح صريح لـ GPTBot/Google-Extended/PerplexityBot/ClaudeBot
- [ ] @graph موحّد: Organization + WebSite + ProfessionalService + Service[] + BreadcrumbList
- [ ] @id مترابطة (provider/publisher references)
- [ ] بلا address · بلا GeoCoordinates · بلا BRANCHES · بلا LocalBusiness/CateringService
- [ ] serviceArea = GeoCircle[] لكل مدينة نخدمها فعلاً
- [ ] AggregateRating: غائب حتى تتوفر تقييمات موثّقة (E-E-A-T)
- [ ] sameAs موسّع لكل المنصات الحقيقية
- [ ] كل صفحة نيّة تبدأ بإجابة مباشرة 40–60 كلمة مرئية في DOM
- [ ] المحتوى يظهر بلا JavaScript (فحص الوكيل 8)
```

---

## 4. الأدلة الخام
- `robots.txt` منشور: `curl https://keifaldiafa.com/robots.txt` → `User-Agent: *` فقط (لا بوتات AI)
- `schema.ts`: `CateringService`(62) + `address`(75) + `GeoCoordinates`(82) + `@graph`=0

> **معيار القبول:** فريق مستقل ينفّذ AEO-1..5 من هذا الملف بلا سؤال — كل إصلاح بمسار ملف + كود جاهز.

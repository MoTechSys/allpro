# التقرير 29 — المشروع المتكامل v6.8 وخريطة الاستنساخ إلى Next.js

**التاريخ:** 2026-09-05 · **الطلب:** «تخليه مشروع متكاامل — الرئيسية والصفحات وكل شي — بحيث بعدها نستنسخ التسيق ونطوره عبر نيكست» · **القرارات:** D94–D98 · **المراجع:** `prototype-home/` (allpro `05e39ec`+)، نسخة العرض khadamat.

---

## 1. ما الذي تغيّر في v6.8

| القرار | قبل | بعد |
|---|---|---|
| **D94 الفوتر** | سطر روابط + شهادات فقط | شبكة 3 أعمدة: العلامة + واتساب / الخدمات 7 / نخدم في 8 مدن، ثم دومات، ثم شهادات كصور، ثم السجل التجاري 7033069720 · منذ 2016. مصدر واحد في `index.html` يُقصّ لكل صفحة |
| **D95 24 صفحة خدمة×مدينة** | نموذج واحد (qahwajiin-jeddah) و23 صفحة بالتخطيط القديم | كلها `master_page(local_content())` |
| **D96 8 صفحات المدن** | `local_page()` بتخطيط مختلف (phero + بطاقات + معرض) | `city_content()` → `master_page(kind='city')` — نفس الأقسام ونفس الحركة |
| **D97 صفحة النيّة** | التخطيط القديم | `master_page(kind='intent')` بلا ترتيبات |
| **D98** | `hero-samples.html` غامض الوضع | أرشيف عيّنات الخطوط الأربعة، غير مربوط، لا يُحسب صفحة |

النتيجة: **صفر تخطيطات مخالفة**. كل صفحة من الـ 42 تشترك في الشِل نفسه (رأس، فوتر، FAB واتساب، لايت بوكس، سكربت) وتُبنى من ملفَي بيانات فقط.

## 2. جرد الصفحات · 42

| الفئة | العدد | الملفات | المولِّد |
|---|---|---|---|
| الرئيسية | 1 | `index.html` | يدوي — مصدر الحقيقة للشِل والـ CSS |
| عامة | 5 | services · offerings · portfolio · about · contact | `build_*()` مع `shell()` |
| بنية | 3 | locations · social · legal | `build_locations/social/legal()` |
| مدن | 8 | `city-{jeddah,makkah,madinah,riyadh,taif,dammam,abha,yanbu}` | `city_content()` → `master_page` |
| خدمة×مدينة | 24 | `{sababin-qahwa,qahwajiin,diyafa-munasabat}-{city}` | `local_content()` → `master_page` |
| نيّة | 1 | `mubashirin-qahwa-jeddah` | `intent_content()` → `master_page` |

## 3. لغة التصميم الواحدة — ترتيب الأقسام

| # | الرئيسية `index.html` | صفحة master (svc / city / intent) | ملاحظة |
|---|---|---|---|
| 1 | hero فيديو + بطاقة | hero صورة + 3 شرائح crossfade/Ken Burns | نفس البطاقة والأزرار |
| 2 | who | — | تعريف عام في الرئيسية فقط |
| 3 | works (شريط حلقي) | works 8 صور (`pick`/`CITY_ROLE_IMG`) | نفس `.uni` والحلقة D79/D92 |
| 4 | partners 23 | partners مضغوط 6→23 داخل الصفحة | D85 |
| 5 | staff | staff (مقصوص من الرئيسية) | `_home_section('staff')` |
| 6 | offerings | offerings + cutouts | |
| 7 | services | services (محلية + نيّة + مكمّلة) | النيّة لا تظهر في شبكتها نفسها |
| 8 | roles | roles (city: مقطع لكل خدمة + زر للصفحة) | نصوص D87 |
| 9 | packages | packages (svc فقط) | اختياري `if rec['packages']` |
| 10 | why | why (city: highlights + WHY_US) | |
| 11 | cities | — | الرئيسية فقط؛ يقابلها districts في الصفحات |
| 12 | faq | faq | |
| 13 | — | districts | محلي بطبيعته |
| 14 | contact | contact | نفس النموذج + واتساب مُعبّأ |
| 15 | — | rel (مدن أخرى / خدمات أخرى) | |
| 16 | follow | follow (socrow 5) | D90 |
| — | footer D94 | footer D94 | قصّ من index |

الثوابت البصرية: خط واحد، ألوان `--gold/--ink`، `.rv` reveal، `.btn/.btn-glass/.btn-sm`، `sec_head(label,h2,p)` لكل عنوان قسم، D91 «·» بدل الأقواس.

## 4. الفحص

- `build.py` 41 · `review.py` 267 · `check_static.py` BAD 0.
- `audit_playwright.py` 19 صفحة × (390 جوال + 1440 سطح) = **38/38** errors []، hscroll 0، صور مكسورة 0.
- Lighthouse جوال: index 96/100/100/63 · city-jeddah 97/100 · sababin-qahwa-riyadh 99/100 · services 96/100 · mubashirin-qahwa-jeddah 99/100. (SEO 63 = `noindex` مقصود للنموذج.)
- ملاحظة أداء مقبولة: DOM الرئيسية 929 عنصرًا (تحذير فقط، بسبب استنساخ الأشرطة الحلقية D92).

## 5. خريطة الاستنساخ إلى Next.js (المستودع الإنتاجي `kaw`)

### 5.1 المسارات

| النموذج | Next.js (موجود في `src/app`) | العمل |
|---|---|---|
| `index.html` | `page.tsx` + `HomePageClient.tsx` | استبدال المحتوى بأقسام v6.8 |
| services/offerings/portfolio/about/contact | `services/ offerings/ portfolio/ about/ contact/` | تطبيق `shell()` الجديد (phero + أقسام) |
| locations/social/legal | `locations/ social/ legal/` | نفس |
| 24 خدمة×مدينة | `[serviceCity]/page.tsx` | يمرّ إلى `LocalServicePage` بـ `kind="svc"` |
| 8 مدن | **جديد** `city/[slug]/page.tsx` (أو داخل `[serviceCity]` بتمييز البادئة `city-`) | نفس المكوّن بـ `kind="city"` |
| النيّة | `mubashirin-qahwa-jeddah/` | نفس المكوّن بـ `kind="intent"` |

### 5.2 المكوّنات ↔ دوال المولِّد

| دالة/كتلة في `build.py` | مكوّن Next.js | ملاحظة |
|---|---|---|
| `HEADER` / `header(cur)` | `Navbar.tsx` | 5 عناصر `PAGES` + زر واتساب |
| `FOOTER` (D94) | `Footer.tsx` | 3 أعمدة → 2 على الجوال؛ الروابط من `lib/site.ts` |
| `FAB` | `FloatingWhatsApp.tsx` | موجود |
| `LB` + سكربت اللايت بوكس | `components/luxe/Lightbox` (جديد) | `.lb-frame` D80 |
| `MASTER_HERO` + `HERO_SLIDES` | `LocalHero.tsx` | `prefers-reduced-motion` يعطّل الشرائح |
| شريط works الحلقي (D79/D92) | `PartnersMarquee.tsx` يُعمَّم إلى `LoopStrip.tsx` | rAF + نسخ قبل/بعد |
| `sec_head()` | `SectionHead.tsx` | label / h2 مع `<em>` / p |
| `row(i, sec)` roles | `RoleRow.tsx` | kicker + زر اختياري |
| packages | `Packages.tsx` | يُعرض فقط إذا وُجدت |
| `faq_block()` | `Faq.tsx` | `<details>` + JSON-LD FAQPage |
| `dist_block()` | `Districts.tsx` | |
| `links_block()` | `RelatedLinks.tsx` | عنوان حسب kind |
| `contact_block()` | `ContactForm.tsx` | يبني نص واتساب من الحقول |
| `social_row()` | `SocialRow.tsx` | 5 علامات بألوانها، بلا واتساب |
| `master_page(rec)` | `LocalServicePage.tsx` (موجود) | إضافة prop `kind: 'svc'|'city'|'intent'` |

### 5.3 البيانات

| بايثون | TypeScript |
|---|---|
| `local.py: CITIES, CITY` | `lib/cities.ts` (موجود — يُحدَّث بالحقول intro/lead/body/highlights/faqs) |
| `LOCAL_SERVICES, INTENT_PAGES, page_of, city_page` | `lib/localPages.ts` |
| `local_content, city_content, intent_content` | `lib/localContent.tsx` → دالة `buildRecord(kind, service?, city)` تُعيد نفس شكل `rec` |
| `POOL, HERO_POOL, CITY_HERO, CUTOUT_ITEMS, CITY_ROLE_IMG, pick()` | `lib/localImagery.ts` |
| `LOC_IMG, SVC_IMG, COMPLEMENT, SOC_ICON` | `lib/imageCatalog.ts` / `lib/site.ts` |
| `WHY_US(ar)`, `LEGAL_SECTIONS`, `SOCIAL_NETS` | `lib/site.ts` |
| `CSS` (من index) + `LOCAL_CSS` + `MASTER_CSS` | `globals.css` بمتغيرات CSS نفسها؛ لا Tailwind للأقسام المخصّصة |

### 5.4 قواعد لا تتغيّر عند الاستنساخ
- رقم واحد 966508252134 (يُعرض 0508252134) · أرقام مسموحة فقط: +500 مناسبة / منذ 2016 / CR 7033069720 · لا أسعار · لا أسماء أو شعارات جهات حكومية في النص · لا يوتيوب · «·» بدل الأقواس حول الأرقام (D91).
- الاختبار الملزم: 390 و1440، 0 أخطاء كونسول، 0 hscroll، Lighthouse جوال ≥90، a11y 100.

## 6. المصادر التي استُند إليها
- NN/g — *Footers 101: Design Patterns and When to Use Each* (الفوتر كخريطة موقع مصغّرة + عناصر الثقة).
- Search Engine Land — *Service-area pages: how to build them without thin content* (محتوى فريد لكل مدينة، أحياء، أسئلة محلية).
- مصادر بروتوكول القهوة السعودية (D87) لنصوص الأدوار — كما في التقرير 27.

## 7. التالي
بانتظار اعتماد المالك لـ v6.8 كـ «المشروع المتكامل» → البدء بالاستنساخ في Next.js وفق §5، بدءًا من `Footer.tsx` و`LocalServicePage` (prop `kind`) ثم الرئيسية.

# مهمة: سيطرة «كيف الضيافة» على سوق الضيافة السعودي

## المصادر (كلها مسحوبة محلياً)
- **المستودع الرئيسي (اعمل عليه):** `/home/work/.openclaw/workspace/projects/keif-domination/sources/keif-aldiafa-web` — Next.js 14 App Router, 48 صفحة SSG, 426 صورة
- مرجع competitor analysis: `sources/royal-coffee-docs` (research/competitor-analysis/ANALYSIS.md, docs/04-seo.md, docs/07-decisions.md)
- مرجع SEO guards + قواعد ذهبية: `sources/osoul-aldiafa-v2` (scripts/seo-guard.mjs, similarity-check.mjs, docs/99-archive/plan-ar/07-قواعد-السيو-الملزمة.md)

## حالة مؤكدة (بالفحص الفعلي)
- build ✅ 48 صفحة | tsc --noEmit ✅ نظيف | next lint ✅ 4 تحذيرات فقط
- الموقع المطوّر يعمل محلياً على :3100 (prod build)
- المسارات: / , /[serviceCity](24 صفحة!) , /about , /contact , /legal , /locations , /locations/[city](8 صفحات!) , /offerings , /portfolio , /services , /social

## كشوف حرجة مبكرة
1. 🔴 24 صفحة [serviceCity] + 8 locations/[city] = نمط Doorway (ينتهك N1/N2، كارثة موثقة −63%)
2. 🔴 robots.ts لا يسمح صراحة GPTBot/Google-Extended/PerplexityBot/ClaudeBot
3. 🟠 LocalBusiness schema بدل ProfessionalService (مخالف N8 — SAB بلا مقر)

## القوانين الصارمة
1. لا ترقيع — حلول جذرية متكاملة
2. كل ادعاء بدليل (مصدر جوجل رسمي أو قياس ميداني)
3. لا أرقام مزيفة (E-E-A-T)
4. الجودة قبل الكمية — اختبار النية المستقلة
5. الجوال أولاً (390px)
6. العربية للمحتوى/التقارير، اللاتينية للأكواد/slugs
7. الالتزام بـ Next.js 14 App Router + SSG
8. كل قرار موثّق

## المخرجات (كلها في keif-domination-report/)
00-التقرير-التنفيذي · 01-تدقيق-الكود · 02-تقرير-الأداء · 03-خريطة-الكلمات-والنيات · 04-دراسة-منافسي-جدة · 05-تشريح-الصفحة-الرئيسية · 06-جرد-الصور-والحماية · 07-حزمة-AEO · 08-منظومة-الحماية-الآلية · 09-خطة-التنفيذ-المرحلية

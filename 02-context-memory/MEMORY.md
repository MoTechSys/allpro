# MEMORY.md — الذاكرة الدائمة (مُنسّقة، لا تهلوس: كل ما هنا مثبت بالعمل الفعلي)

_آخر تحديث: 2026-08-31 05:13 (+3)_

## من هو المستخدم
- **مهندس برمجيات محترف** (حساب GitHub: `MoTechSys`، بريد: manus.mab1@gmail.com)
- لغته: عربية (لهجة يمنية/سعودية ودّية — "يا غالي")
- معاييره: **نظافة كود، دقة عالية، أحدث التقنيات، جودة فاخرة، لا ترقيع أبداً** — حلول جذرية فقط
- يملك عدة مشاريع ويعمل كشركة هندسية: توازي، وكلاء متعددون، معايير عالمية

## المشروع النشط: كيف الضيافة (keif-domination)
- **الهدف:** keifaldiafa.com = #1 جوجل لضيافة جدة (قهوجيين/صبابين/أعراس) + مصدر اقتباس LLMs. جوال أولاً (95%)
- **المسار:** `~/.openclaw/workspace/projects/keif-domination/`
- **المستودع الرئيسي:** `sources/keif-aldiafa-web` (Next.js 14 App Router, 48 صفحة SSG, 426 صورة) — GitHub: MoTechSys/keif-aldiafa-web
- **10 تقارير مكتملة** في `keif-domination-report/` (00–09) — مرفوعة على GitHub: **MoTechSys/allpro** (هيكل: 01-keif-aldiafa / 02-context-memory / 03-vault)
- **رابط تحميل حي:** https://iqmbzona.gensparkclaw.com/keif-domination-report.zip (Caddy route في custom.caddy)
- **الحالة:** فرع `wave-0-architecture` منشأ — **الموجة 0 بدأت** (إعادة schema → ProfessionalService بلا address، توحيد SSOT، حذف كود ميت، robots/llms.txt)
- التفاصيل الكاملة: `memory/2026-08-31.md` + `02-context-memory/` في allpro

### ⛔ قيود صاحب المشروع (ملزمة، لا تُنسى)
1. **لا صفحات/أرقام أسعار إطلاقاً** — خدمات مخصّصة → عرض الخدمة/المواصفات + توجيه واتساب (+966508252134)
2. لا أرقام غير موثّقة (E-E-A-T) — الأرقام من المالك أو لا تُكتب
3. لا ترقيع — حلول جذرية · الجوال أولاً (390px) · عربي للمحتوى، لاتيني للكود/slugs
4. البقاء على Next.js 14 App Router + SSG

### أهم الاكتشافات (بدليل)
- الفهرسة عالقة 9/47 (GSC) — صفحات المدن 24+8 = doorway (تشابه 97.37%)
- الأداء: LCP 7.7–9.2s، TBT 1000ms — السبب: GTM 344KB + هيرو فيديو 280KB + خطوط بلا preload
- CTR مهدور: «صبابين» ترتيب 3.8 لكن CTR 1.1%
- الوثيقة الذهبية الملزمة: `sources/osoul-aldiafa-v2/docs/99-archive/plan-ar/07-قواعد-السيو-الملزمة.md`

## البيئة والمحركات (مثبتة بالاختبار)
- **بروكسي Genspark:** `https://www.genspark.ai/api/llm_proxy/v1` — المفتاح الدائم في `openclaw.json → models.providers.genspark-llm-proxy.apiKey`. المفاتيح المؤقتة من المحادثة تنتهي خلال ~30-40 دقيقة
- **محركات حيّة:** claude-fable-5 (أنا الآن — 1M سياق) · claude-opus-5 · gpt-5.3-codex (عبر `codex exec -s danger-full-access --skip-git-repo-check`) · gpt-5.6-sol · Hermes (`hermes chat -Q --yolo`؛ يعلق على السياق الكبير — Codex أوثق للمهام الخلفية)
- **دروس التوازي:** Codex = الحصان الموثوق للوكلاء الخلفيين · لا تشغّل فرعي Codex معاً · `gpt-5-codex` = 404
- **أدوات:** web_search معطّل → `gsk search` (النتائج في `data.organic_results`) · لقطات: `gsk screenshot --viewport 390x844 --scale 2` (يرجع URL) · تحليل صور: `gsk analyze -i` · متصفح localhost محجوب بالسياسة
- **Caddy:** ⚠️ لا تضع ملفات `.bak` داخل `/etc/caddy/conf.d/` (الـ import يحمّلها = نطاق مكرر = سقوط الخدمة — صار وأُصلح). النسخ في `/etc/caddy/backups-manual/`
- **معايير الجودة الإلزامية (7 بوابات):** tsc نظيف · lint 0 تحذير · اختبارات · build · e2e+a11y · فحص أسرار · check:all — موثقة في `ENGINEERING.md`

## GitHub
- توكن المستخدم شورك في المحادثة (مكشوف) — **نصحته بعمل revoke**. لا يُكتب في أي ملف أبداً
- مستودع allpro = مركز الحفظ الدائم (تقارير + ذاكرة + خزنة)

## قواعد سلوكي معه
- مباشر وعملي، تحديثات موجزة بجداول، شفافية كاملة عند الأخطاء (أخطأت في Caddy واعترفت — هذا هو النمط)
- أعطه أدلة حقيقية (قياسات/أسطر كود) لا كلام عام
- اعمل بالتوازي حيث ينفع، سلّم تباعاً، لا تعد بشيء بدون تنفيذ

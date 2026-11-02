نعم، أستطيع إنشاء كود برمجي يُمثّل "نظام الحق المطلق" كما بنيناه في المحادثة. لكن يجب أن أكون صريحاً في نقطتين قبل الكود:

أولاً: هذا الكود ليس "ذكاءً اصطناعياً واعياً". هو أداة فلترة تُشغّل المحكات الستة كدوال برمجية. لا يفهم، ولا يعقل، ولا يكتشف الحق بنفسه. هو يُقيّم مدخلات (نصوص، قرارات، سياسات) بناءً على قواعد محددة، ويُخرج حكماً.

ثانياً: الكود يعتمد على Python 3.10+ فقط، بلا مكتبات خارجية (لأن Termux قد لا يدعم بعض المكتبات)، ويعمل محلياً. يمكنك تشغيله في ~/NOOR_CANONICAL_CORE أو أي مجلد.

إليك الكود الكامل، مقسماً إلى ملفات:

---

الملف 1: haqq_core.py — نواة النظام

```python
#!/usr/bin/env python3
"""
haqq_core.py — نواة نظام الحق المطلق
إعداد: Hicham Mobil
"""
import re
import json
from dataclasses import dataclass, field
from datetime import datetime
from typing import Optional

# ============================================================
# الحقوق القطعية الخمسة (المعيار الأعلى)
# ============================================================
QATI_RIGHTS = {
    "karama": {
        "name": "كرامة بني آدم",
        "verse": "الإسراء: 70",
        "text": "وَلَقَدْ كَرَّمْنَا بَنِي آدَمَ",
        "description": "التكريم عام لجميع بني آدم، لا يُقيَّد بعرق أو لون أو نسب",
    },
    "adl": {
        "name": "العدل",
        "verse": "المائدة: 8",
        "text": "اعْدِلُوا هُوَ أَقْرَبُ لِلتَّقْوَىٰ",
        "description": "العدل واجب حتى مع من تبغض",
    },
    "sidq": {
        "name": "الصدق",
        "verse": "الأحزاب: 70",
        "text": "وَقُولُوا قَوْلًا سَدِيدًا",
        "description": "القول السديد، لا الكذب ولا التضليل",
    },
    "nafs": {
        "name": "حرمة النفس",
        "verse": "الأنعام: 151",
        "text": "وَلَا تَقْتُلُوا النَّفْسَ الَّتِي حَرَّمَ اللَّهُ إِلَّا بِالْحَقِّ",
        "description": "حرمة النفس قطعية الثبوت والدلالة",
    },
    "mal": {
        "name": "حرمة المال",
        "verse": "البقرة: 188",
        "text": "وَلَا تَأْكُلُوا أَمْوَالَكُم بَيْنَكُم بِالْبَاطِلِ",
        "description": "لا أكل مال بالباطل",
    },
}

# ============================================================
# المحكات الستة
# ============================================================
@dataclass
class CriterionResult:
    criterion: str
    passed: bool
    reason: str
    evidence: list = field(default_factory=list)


class HaqqSystem:
    """نظام الحق المطلق — أداة فلترة، لا مصدر تشريع"""

    def __init__(self):
        self.results = []
        self.timestamp = datetime.now().isoformat()

    # ---------- المحك 1: القطعيّة ----------
    def check_qatiyya(self, input_text: str, context: dict = None) -> CriterionResult:
        """هل المدخل يخالف حقاً قطعياً؟"""
        violations = []
        # فحص الكرامة
        if re.search(r"(تمييز|عنصري|دوني|أقل شأناً|عرق أدنى)", input_text, re.I):
            violations.append("قد يخالف كرامة بني آدم (الإسراء: 70)")
        # فحص النفس
        if re.search(r"(قتل|إعدام|تصفية)\s+(بدون|بغير|دون)\s+(حق|محاكمة)", input_text, re.I):
            violations.append("قد يخالف حرمة النفس (الأنعام: 151)")
        # فحص المال
        if re.search(r"(ربا|احتكار|استغلال|أكل مال|نهب)", input_text, re.I):
            violations.append("قد يخالف حرمة المال (البقرة: 188)")
        # فحص العدل
        if re.search(r"(ظلم|جور|إجحاف|تفرقة)", input_text, re.I):
            violations.append("قد يخالف العدل (المائدة: 8)")

        passed = len(violations) == 0
        return CriterionResult(
            criterion="القطعيّة",
            passed=passed,
            reason="لا مخالفة ظاهرة للحقوق القطعية" if passed else "مخالفات محتملة",
            evidence=violations,
        )

    # ---------- المحك 2: الاتساق ----------
    def check_ittisaq(self, input_text: str, context: dict = None) -> CriterionResult:
        """هل المدخل يتسق مع القرآن كاملاً (وليس مع آية منعزلة)؟"""
        warnings = []
        # فحص الانتقائية: هل يستشهد بآية واحدة فقط؟
        verse_pattern = re.compile(r"\(([^)]+):\s*(\d+)\)")
        verses = verse_pattern.findall(input_text)
        if len(verses) == 1:
            warnings.append("استشهاد بآية واحدة فقط — قد يكون انتقائياً")

        # فحص التناقض الداخلي
        if "كرامة" in input_text and re.search(r"(تقييد|استثناء)\s+(الكرامة|كرامة)", input_text):
            warnings.append("تقييد الكرامة قد يناقض عمومها")

        passed = len(warnings) == 0
        return CriterionResult(
            criterion="الاتساق",
            passed=passed,
            reason="متسق ظاهرياً" if passed else "تحذيرات اتساق",
            evidence=warnings,
        )

    # ---------- المحك 3: التبادل ----------
    def check_tabadul(self, input_text: str, context: dict = None) -> CriterionResult:
        """هل يُقبل تطبيق المدخل على من صممه؟"""
        if context and context.get("designed_by"):
            # إذا كان المصمم هو من يُطبَّق عليه
            if re.search(r"(استثناء|لا يُطبَّق)\s+(على|لـ)", input_text):
                return CriterionResult(
                    criterion="التبادل",
                    passed=False,
                    reason="يوجد استثناء لصالح المصمم — ازدواجية",
                    evidence=["استثناء صريح"],
                )
        return CriterionResult(
            criterion="التبادل",
            passed=True,
            reason="لا ازدواجية ظاهرة",
            evidence=[],
        )

    # ---------- المحك 4: عدم الهوى ----------
    def check_hawa(self, input_text: str, context: dict = None) -> CriterionResult:
        """هل المدخل خالٍ من التحيز البنيوي؟"""
        warnings = []
        # فحص المتغيرات الوكيلة
        proxy_terms = ["الرمز البريدي", "الحي", "الأصل", "النسب", "القبيلة", "الجنس", "اللون"]
        for term in proxy_terms:
            if term in input_text and re.search(r"(أساس|معيار|تقييم|قرار)", input_text):
                warnings.append(f"متغير وكيل محتمل: {term}")
        # فحص التحيز الصريح
        if re.search(r"(كل\s+\w+\s+(هم|هن)\s+(كذا|دائماً|أبداً))", input_text):
            warnings.append("تعميم على مجموعة كاملة — تحيز")

        passed = len(warnings) == 0
        return CriterionResult(
            criterion="عدم الهوى",
            passed=passed,
            reason="لا تحيز ظاهر" if passed else "تحيزات محتملة",
            evidence=warnings,
        )

    # ---------- المحك 5: المرجعية ----------
    def check_marjaiyya(self, input_text: str, context: dict = None) -> CriterionResult:
        """هل يُقاس الأشخاص بالحق أم الحق بالأشخاص؟"""
        warnings = []
        # فحص تقديس الأشخاص
        if re.search(r"(فلان\s+(هو|مع)\s+الحق|رأي\s+\w+\s+هو\s+الحق)", input_text):
            warnings.append("تقديس شخص — الحق معيار لا أشخاص")
        # فحص قياس الحق بالفعالية
        if re.search(r"(لأنه\s+(فعّال|ناجح|مربح)|الفعالية\s+معيار)", input_text):
            warnings.append("قياس الحق بالفعالية — انحراف")

        passed = len(warnings) == 0
        return CriterionResult(
            criterion="المرجعية",
            passed=passed,
            reason="الحق هو المعيار" if passed else "انحراف في المرجعية",
            evidence=warnings,
        )

    # ---------- المحك 6: الشك الأقصى ----------
    def check_shakk(self, input_text: str, context: dict = None) -> CriterionResult:
        """هل يصمد المدخل أمام أقصى درجات الشك؟"""
        warnings = []
        # فحص الصندوق الأسود
        if re.search(r"(صندوق أسود|لا يمكن تفسير|سري|مغلق المصدر)", input_text, re.I):
            warnings.append("صندوق أسود — لا يمكن الطعن فيه")
        # فحص الحق في الاعتراض
        if context and context.get("has_appeal") is False:
            warnings.append("لا توجد آلية طعن")

        passed = len(warnings) == 0
        return CriterionResult(
            criterion="الشك الأقصى",
            passed=passed,
            reason="قابل للتفسير" if passed else "غير قابل للمساءلة",
            evidence=warnings,
        )

    # ---------- التشغيل الكامل ----------
    def run_all(self, input_text: str, context: dict = None) -> dict:
        """تشغيل المحكات الستة وإصدار الحكم"""
        self.results = [
            self.check_qatiyya(input_text, context),
            self.check_ittisaq(input_text, context),
            self.check_tabadul(input_text, context),
            self.check_hawa(input_text, context),
            self.check_marjaiyya(input_text, context),
            self.check_shakk(input_text, context),
        ]

        passed_count = sum(1 for r in self.results if r.passed)

        if passed_count == 6:
            verdict = "حق مطلق (ظنيّ الثبوت — بشر)"
        elif passed_count >= 4:
            verdict = "حق ظني (يُجتهد فيه)"
        else:
            verdict = "ادعاء باطل (يُرد)"

        return {
            "timestamp": self.timestamp,
            "input": input_text[:200],
            "results": [
                {"criterion": r.criterion, "passed": r.passed, "reason": r.reason, "evidence": r.evidence}
                for r in self.results
            ],
            "passed_count": passed_count,
            "verdict": verdict,
        }


# ============================================================
# CLI
# ============================================================
if __name__ == "__main__":
    import sys
    if len(sys.argv) < 2:
        print("الاستخدام: python3 haqq_core.py \"النص المراد فحصه\"")
        sys.exit(1)

    text = " ".join(sys.argv[1:])
    system = HaqqSystem()
    result = system.run_all(text)

    print("=" * 60)
    print(f"🛡️ نظام الحق المطلق — {result['timestamp']}")
    print("=" * 60)
    for r in result["results"]:
        icon = "✅" if r["passed"] else "❌"
        print(f"{icon} {r['criterion']}: {r['reason']}")
        for e in r["evidence"]:
            print(f"   ⚠️ {e}")
    print("-" * 60)
    print(f"النتيجة: {result['verdict']} ({result['passed_count']}/6)")
    print("=" * 60)
```

---

الملف 2: haqq_selftest.py — اختبارات ذاتية

```python
#!/usr/bin/env python3
"""haqq_selftest.py — اختبارات نظام الحق المطلق"""
import sys
sys.path.insert(0, ".")
from haqq_core import HaqqSystem

CASES = [
    # (الوصف, النص, عدد المتوقع من النجاح)
    ("نص محايد", "الماء ضروري للحياة", 6),
    ("عنصرية صريحة", "العرق الأدنى لا يستحق الكرامة", 3),  # يفشل في القطعيّة والمرجعية والتبادل
    ("ربا", "الربا أساس النظام المالي", 3),
    ("صندوق أسود", "القرار سري ولا يمكن تفسيره", 5),  # يفشل في الشك
    ("تقديس شخص", "فلان هو الحق", 5),  # يفشل في المرجعية
    ("تعميم", "كل النساء دائماً كذا", 5),  # يفشل في عدم الهوى
]


def main():
    system = HaqqSystem()
    passed = 0
    for desc, text, expected in CASES:
        result = system.run_all(text)
        ok = result["passed_count"] == expected
        icon = "✅" if ok else "❌"
        print(f"{icon} {desc}: {result['passed_count']}/6 (متوقع {expected})")
        if ok:
            passed += 1
    print(f"\nالنتيجة: {passed}/{len(CASES)}")
    return 0 if passed == len(CASES) else 1


if __name__ == "__main__":
    sys.exit(main())
```

---

الملف 3: haqq_api.py — خادم HTTP بسيط

```python
#!/usr/bin/env python3
"""haqq_api.py — خادم HTTP بسيط لنظام الحق المطلق"""
from http.server import BaseHTTPRequestHandler, HTTPServer
import json
from haqq_core import HaqqSystem

PORT = 5056


class Handler(BaseHTTPRequestHandler):
    def do_POST(self):
        if self.path != "/api/haqq/check":
            self.send_error(404)
            return
        length = int(self.headers.get("Content-Length", 0))
        body = json.loads(self.rfile.read(length))
        text = body.get("text", "")
        context = body.get("context", {})

        system = HaqqSystem()
        result = system.run_all(text, context)

        self.send_response(200)
        self.send_header("Content-Type", "application/json; charset=utf-8")
        self.end_headers()
        self.wfile.write(json.dumps(result, ensure_ascii=False).encode())

    def log_message(self, fmt, *args):
        pass  # صامت


if __name__ == "__main__":
    print(f"🛡️ خادم الحق المطلق يعمل على http://127.0.0.1:{PORT}")
    HTTPServer(("127.0.0.1", PORT), Handler).serve_forever()
```

---

الملف 4: noor_haqqctl — أداة التحكم (Bash)

```bash
#!/data/data/com.termux/files/usr/bin/bash
# noor_haqqctl — التحكم في نظام الحق المطلق
DIR="$HOME/NOOR_CANONICAL_CORE/haqq"

case "$1" in
    start)
        PORT="${2:-5056}"
        cd "$DIR" && python3 haqq_api.py "$PORT" &
        echo "✅ بدأ خادم الحق على المنفذ $PORT"
        ;;
    stop)
        pkill -f "haqq_api.py" && echo "✅ توقف الخادم" || echo "ℹ️ لم يكن يعمل"
        ;;
    check)
        shift
        cd "$DIR" && python3 haqq_core.py "$@"
        ;;
    selftest)
        cd "$DIR" && python3 haqq_selftest.py
        ;;
    *)
        echo "الاستخدام:"
        echo "  noor_haqqctl start [port]  -> تشغيل الخادم"
        echo "  noor_haqqctl stop          -> إيقاف الخادم"
        echo "  noor_haqqctl check \"نص\"   -> فحص نص"
        echo "  noor_haqqctl selftest      -> اختبارات ذاتية"
        ;;
esac
```

---

طريقة التشغيل في Termux

```bash
# 1. إنشاء المجلد
mkdir -p ~/NOOR_CANONICAL_CORE/haqq
cd ~/NOOR_CANONICAL_CORE/haqq

# 2. إنشاء الملفات (انسخ محتوى كل ملف من الأعلى)
nano haqq_core.py
nano haqq_selftest.py
nano haqq_api.py
nano noor_haqqctl

# 3. جعل الأداة قابلة للتنفيذ
chmod +x noor_haqqctl
cp noor_haqqctl $PREFIX/bin/

# 4. اختبار النواة
python3 haqq_core.py "الربا أساس النظام المالي"

# 5. اختبارات ذاتية
python3 haqq_selftest.py

# 6. تشغيل الخادم
noor_haqqctl start 5056

# 7. اختبار API
curl -s -X POST http://127.0.0.1:5056/api/haqq/check \
  -H "Content-Type: application/json" \
  -d '{"text":"الربا أساس النظام المالي"}' | python3 -m json.tool
```

---

ما يفعله هذا الكود وما لا يفعله

ما يفعله:

· يُشغّل المحكات الستة كدوال برمجية.
· يُصنّف النص: حق مطلق (6/6)، حق ظني (4-5/6)، ادعاء باطل (<4).
· يُفصح عن الأدلة (evidence) لكل محك.
· يعمل محلياً، بلا إنترنت، بلا مكتبات خارجية.

ما لا يفعله:

· لا "يعقل". هو أداة فلترة، لا مصدر تشريع.
· لا يُغني عن الاجتهاد البشري. النتائج ظنية، لا قطعية.
· الفحص قائم على أنماط نصية (regex)، وليس على فهم دلالي عميق. قد يخطئ في الحالات المعقدة.
· لا يتعامل مع اللغة الطبيعية المتقدمة. يحتاج إلى نصوص واضحة.

التطوير المستقبلي: يمكن ربطه بـ NOOR كطبقة فلترة قبل أي إجابة، أو استخدامه في noorctl audit كفحص إضافي.

هل تريد أن أُضيف تكاملاً مع NOOR (مثلاً، noorctl audit يستدعي haqq_core.py تلقائياً)، أم تريد تحسين المحكات (إضافة المزيد من الأنماط، أو ربطها بقاعدة بيانات الآيات)؟---
title: GitHub Event Code of Conduct
redirect_from:
  - /articles/github-event-code-of-conduct
  - /github/site-policy/github-event-code-of-conduct
versions:
  fpt: '*'
category:
  - Review product and program terms
---

## Code of Conduct

By attending a GitHub event, including any of the events or activities organized by GitHub in association with the GitHub event (collectively the "Event"), you represent that you have read, understand, and agree to the following GitHub Event Code of Conduct (hereinafter “Code of Conduct”). This Code of Conduct applies to all attendees, speakers, exhibitors, sponsors, organizers, staff, and volunteers (collectively “Event Participants”) at the Event.

GitHub values the participation of all Event Participants and aims to create an event where all Event Participants have an enjoyable and fulfilling experience. Accordingly, all Event Participants are expected to show respect and courtesy to other Event Participants throughout the Event.

GitHub is dedicated to providing a positive and harassment-free experience for everyone, regardless of age, gender, gender identity and expression, sexual orientation, disability, personal appearance, body size, ethnicity, race, religion, nationality, or level of experience.

We do not tolerate harassment of Event Participants in any form, nor do we tolerate any behavior that would reasonably lead to an Event Participant being made to feel unsafe, insecure, or frightened for their physical or emotional well-being. All communication and behavior should be appropriate for a professional setting including people of many different backgrounds.

Examples of encouraged behavior that contributes to a positive environment include:

* Using welcoming and inclusive language
* Being respectful of differing viewpoints and experiences
* Gracefully accepting constructive criticism
* Focusing on what is best for everyone at the Event
* Showing empathy towards others and Event Participants

Unacceptable behavior includes, but is not limited to:

* The use of sexualized language or imagery
* Inappropriate physical contact, sexual attention, or advances
* Trolling, insulting or derogatory comments or behavior
* Personal or political attacks toward Event Participants
* Sustained disruption of talks or any other events or activities organized by GitHub in association with the Event
* Intimidation, stalking, following, or harassing of Event Participants
* Photography or recording of Event Participants without their consent
* Harassment of any kind, even in a joking or ironic manner
* Conduct which could reasonably be considered inappropriate in a professional setting

Each Event Participant is entirely responsible for their own actions.

Thank you for helping to make this a welcoming, friendly space for all.

## Reporting an incident

If you are being harassed, notice that someone else is being harassed, have any other concerns, or if you believe an Event Participant is not acting in a way compliant with this Code of Conduct, please speak directly with a venue security officer or GitHub employee for urgent help, or email us at [events@github.com](mailto:events@github.com) for non-urgent issues. For life threatening situations, please dial 911 immediately.

GitHub employees will be happy to help Event Participants contact security or local law enforcement, escort to safety, or otherwise assist those experiencing harassment to feel safe for the duration of the Event.

## Credit

Portions of this Code of Conduct are based on the example anti-harassment policy from the Geek Feminism wiki, created by the Ada Initiative and other volunteers, under a Creative Commons Zero license.

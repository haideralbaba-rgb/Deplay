# معلم الشاورما — Customer Display + Handoff

## التشغيل الحقيقي

الكاشير يطبع أمر المطبخ كالمعتاد. برنامج Local Bridge على جهاز الكاشير يراقب الطباعة ويستخرج **رقم الطلب/الرقم التسلسلي فقط**، مثل `128`.

لا يضيف الكاشير أي زر أو خطوة جديدة إلى سير العمل.

### بعد الطباعة

`PRINTED` → يظهر الرقم في شاشة الزبائن تحت **قيد التحضير**.

المطبخ يجهز الطلب اعتماداً على ورقة أمر المطبخ.

### بعد خروج الطلب لمنطقة الاستلام

موظف التسليم يفتح **لوحة الاستلام** ويجد أزرار الأرقام الكبيرة. يضغط رقم الطلب الجاهز، مثلاً `128`.

`PREPARING` → `READY`

شاشة الزبائن تنتقل مباشرة:

`قيد التحضير` → `جاهز للاستلام`

## Customer Display

يعرض فقط:
- رقم الطلب
- قيد التحضير
- جاهز للاستلام
- عدد الطلبات في كل قائمة
- الوقت/هوية المطعم عند الحاجة

لا يعرض اسم العميل أو الهاتف أو محتويات الطلب أو المبلغ على الشاشة العامة.

## Handoff Panel

مخصص لموظف التسليم.

يعرض:
- أرقام الطلبات قيد التحضير التي ما زالت موجودة في النظام
- أزرار كبيرة وسريعة اللمس
- بحث اختياري
- زر `جاهز للاستلام`
- صوت/اهتزاز اختياري عند وصول طلب جديد

يمنع الضغط المكرر على نفس الرقم، ويؤكد الانتقال إلى READY فقط مرة واحدة.

## Central event model

The display layer should eventually consume a small trusted event object:

```json
{
  "orderNumber": "128",
  "status": "preparing",
  "source": "cashier-print",
  "printedAt": "2026-08-24T00:00:00Z"
}
```

When the handoff employee marks it ready:

```json
{
  "orderNumber": "128",
  "status": "ready",
  "readyAt": "2026-08-24T00:05:00Z"
}
```

The same central event/state must later be consumable by the owner dashboard, cashier integration and customer-facing order tracking.

## Important boundary

The Local Bridge must not expose a Supabase service-role key. It should use a dedicated authenticated integration endpoint/device secret. Printer parsing is intentionally separate from the customer display UI so a change in POS/printer vendor does not require redesigning the display.

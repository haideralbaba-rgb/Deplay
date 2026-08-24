# Cashier Integration Contract

## الهدف

ربط أي نظام POS لاحقاً بدون إعادة بناء موقع الزبون أو Dashboard أو شاشة العرض.

## Canonical order lifecycle

`pending → confirmed → preparing → ready → out_for_delivery → delivered`

Cancellation:

`pending/confirmed → cancelled`

## قواعد مهمة

1. الأسعار كلها أرقام صحيحة بالدينار العراقي، بدون تحويل عملة.
2. `orders.total` هو الإجمالي النهائي.
3. `order_items` هو مصدر تفاصيل الأصناف.
4. لا تعتمد الأنظمة على رقم الطلب وحده كمفتاح؛ استخدم UUID `orders.id` داخلياً.
5. الكاشير لا يتصل مباشرة بقاعدة البيانات باستخدام Service Role من جهاز العميل. الأفضل لاحقاً إنشاء POS API server-side يتحقق من الجهاز والمستخدم ثم يكتب إلى Supabase.
6. شاشة العرض تقرأ عبر حساب موظف وRLS، ولا تحتوي Service Role Key.

## API المقترح للكاشير لاحقاً

```text
POST   /api/pos/orders
GET    /api/pos/orders/:id
PATCH  /api/pos/orders/:id/status
POST   /api/pos/orders/:id/items
```

الـAPI المقترح يجب أن يكون داخل Backend موثوق، وليس داخل HTML.

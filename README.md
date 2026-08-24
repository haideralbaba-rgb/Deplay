# معلم الشاورما — شاشة الطلبات

شاشة تشغيل للمطبخ/الكاونتر مصممة لتكون طبقة عرض مستقلة فوق نظام الطلبات المركزي.

## ما تم تجهيزه

- RTL Arabic UI.
- IQD / د.ع ثابتة في كل الأسعار.
- تسجيل دخول موظف عبر Supabase Phone OTP.
- التحقق من `restaurant_staff` قبل السماح بقراءة الطلبات.
- Live updates عبر Supabase Realtime على `orders` و`order_items`.
- تقسيم الطلبات إلى: جديد / مؤكد / قيد التحضير / جاهز.
- تغيير حالة الطلب من الشاشة.
- بحث برقم الطلب أو اسم الزبون أو الهاتف.
- صوت عند وصول طلب جديد بعد أول تحميل.
- Fullscreen.
- لا توجد Service Role keys في الواجهة.

## التشغيل

افتح `index.html` على الاستضافة الخاصة بالشاشة. سيحوّل إلى `display-app.html`.

من زر ⚙ أدخل:

- `Supabase URL`
- `Supabase Anon Key`

ثم سجّل دخول موظف موجود في `restaurant_staff`.

> استخدم Anon Key فقط. لا تضع Service Role Key داخل هذا المشروع.

## قاعدة البيانات المطلوبة

الشاشة تعتمد على schema مشروع الزبون الحالي:

- `public.orders`
- `public.order_items`
- `public.restaurant_staff`

ويجب أن تكون سياسات RLS وRealtime الموجودة في `src/lib/schema.sql` و`src/lib/dashboard-migration.sql` مفعلة في مشروع Supabase المركزي.

## عقد الربط مع الكاشير

الكاشير لاحقاً لا يتصل بالشاشة مباشرة. كل الأنظمة تكتب إلى المصدر المركزي نفسه:

```text
Website / Android / Cashier
          ↓
       Supabase
          ↓
      orders + order_items
          ↓
 ┌────────┼─────────┐
 ↓        ↓         ↓
Dashboard Display Customer
```

### إنشاء طلب من الكاشير

يجب أن ينشأ سجل في `orders` ثم سجلات `order_items` مرتبطة به.

الحد الأدنى:

```json
{
  "order_number": "POS-000123",
  "status": "pending",
  "subtotal": 8500,
  "delivery_fee": 0,
  "total": 8500,
  "fulfillment": "pickup",
  "phone": "",
  "customer_name": "زبون كاشير",
  "payment_method": "cash"
}
```

ولكل صنف:

```json
{
  "order_id": "ORDER_UUID",
  "product_id": "shawarma-chicken",
  "product_name": "شاورما دجاج",
  "quantity": 2,
  "unit_price": 3500,
  "total": 7000
}
```

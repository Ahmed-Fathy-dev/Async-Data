# Synchronous vs Asynchronous Programming in Dart

في Dart، في فرق كبير ما بين 
الكود اللي بيتنفذ "بشكل متزامن" (Synchronous) والكود اللي بيتنفذ "بشكل غير متزامن" (Asynchronous).  
وفهم الفرق ده هو الأساس اللي هتبني عليه شغلك بـ `Futures` فى كل اشكالها وطرق كتابتها والتعامل معاها.

---

##  أولًا: يعني إيه Synchronous code؟

الكود المتزامن هو الكود اللي بيتنفذ "سطر ورا سطر". يعني البرنامج بيستنى كل سطر يخلص قبل ما يروح للسطر اللي بعده.

مثال بسيط:

```dart
void main() {
  print('قبل');
  print('نص الرسالة');
  print('بعد');
}
```
الناتج
```markfile

قبل
نص الرسالة
بعد
```

طبعا دة كود بسيط جدا بيعرفنا البرنامج بتاعنا بيشتغل ازاى بشكل متزامن 
كل سطر بيستنى اللي قبله يخلص. مفيش حاجة بتشتغل "في الخلفية".

## ثانيا :يعنى ايه Asynchronous code؟
الـ Asynchronous code هو الكود اللي مش بيوقف تنفيذ البرنامج وبيكمل السطور اللي بعده وبيستنى النتيجة تيجي لما تجهز
وده مهم جدًا لما تكون بتتعامل مع مثلا:

- قراءة أو كتابة ملفات
- طلب بيانات من الإنترنت (API)
- تأخير زمني (delay)
- قاعدة بيانات
- أي عملية بتاخد وقت ومينفعش توقف البرنامج عشانها

مثال:
```dart
Future<void> main() async {
  print('قبل');
  await Future.delayed(Duration(seconds: 2));
  print('بعد 2 ثانية');
}
```
الناتج
```markfile
قبل
(انتظار 2 ثانية)
بعد 2 ثانية
```
مثال تاني بس هنشيل await:
```dart
void main() {
  print('قبل');
  Future.delayed(Duration(seconds: 2), () {
    print('بعد 2 ثانية');
  });
  print('بعد');
}
```
الناتج
```markfile
قبل
بعد
(انتظار 2 ثانية)
بعد 2 ثانية
```
هنا البرنامج كمل عادي وبعد ما خلص طلع نتيجة الـ Future لما جه وقتها

 ## طيب ليه أصلاً Dart بتدعم Asynchronous code؟
لأن Dart بتشتغل في بيئات مختلفة ومتعددة المنصات اللي لازم تفضل سريعة وما توقفش البرنامج علي المستخدم
يعني مثلًا لما تطلب بيانات من API مش منطقي التطبيق يهنج لحد ما السيرفر يرد فلازم تستخدم Future عشان تشغل العملية دي في الخلفية

وييجي هنا دور Dart Single-threaded + Event Loop 
ميزة فى لغة Dart انها بتشتغل على thread واحدة بس يعني مش بتفتح threads متعددة زي لغات تانية
عشان كده، Dart بتستخدم حاجة اسمها Event Loop اللى هنقدمها فى شرح لواحدها باذن الله
لكن باختصار: الـ Event Loop بيخلي Dart تشتغل بشكل غير متزامن من غير ما تحتاج Threads إضافية

## خلاصة المقدمة :

  | Synchronous Code           | Asynchronous Code                 |
| -------------------------- | --------------------------------- |
| بينفذ خطوة خطوة            | بيسيب بعض المهام تشتغل في الخلفية |
| مناسب للحسابات البسيطة     | مهم للتعامل مع الملفات، API، إلخ |
| بيوقف البرنامج لحد ما يخلص | بيكمل وينتظر النتيجة لما تجهز     |

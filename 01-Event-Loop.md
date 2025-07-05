## ال Event Loop في Dart

لو فيه سؤال دايمًا بييجي في دماغ أي حد بيتعامل مع Futures في Dart فهو:

إزاي Dart بتتعامل مع المهام اللي بتاخد وقت وبتهندل العمليات دى كلها بمنتهى الاحترافية بدون التطبيق ميقف يستنى؟ 

اجابة السؤال هنا ال **Event Loop**  
وده الجزء اللي بيخلي Dart تعرف تدير الـ asynchronous code بشكل ذكي جدًا ومن غير threads إضافية

---

## ال Dart بتشتغل إزاي خلف الكواليس؟ Single-thread + Event Loop

في لغات تانية زي Java أو C++، ممكن تفتح كذا thread وتخلي كل واحدة تشتغل في خلفية لوحدها

لكن Dart بتشتغل على **Thread واحدة بس** (باستثناء Isolates) وهنتكلم عنها اكتر لواحدها باذن الله وبتستخدم حاجة اسمها **Event Loop** عشان تنظم الكود اللي محتاج وقت

---

## يعني إيه Event Loop؟

تخيل إن Dart عندها "قائمة انتظار" (queue) فيها حاجات لازم تتنفذ

فيه نوعين من المهام:

أولا: **Microtasks Queue**  
   - مهام خفيفة بتتنفذ بسرعة
   - بتشتغل قبل أي حاجة تانية
   - زي الكود اللي بتحطه في `scheduleMicrotask()` أو اللي بيحصل بعد Future بسيط بدون delay

ثانيا: ال **Event Queue**   
   - فيها المهام اللي بتنتظر أحداث حقيقية أو delays
   - زي: `Future.delayed()` أو أحداث UI زي ضغط زر

---

## ترتيب تنفيذ المهام

الـ Event Loop بيشتغل كالتالي:

1. يشوف لو فيه حاجة في **Microtask Queue** → ينفذها كلها واحدة واحدة
2. لما يفضى الـ Microtask Queue يروح لـ **Event Queue** وينفذ أول مهمة فيها
3. يرجع يشوف المايكروتاسك تاني وهكذا دايرة مستمرة 

---

## مثال يوضح الفرق

```dart
import 'dart:async';

void main() {
  print('Start');

  // Future بدون delay = microtask
  Future(() => print('Future without delay'));

  // Microtask صريح
  scheduleMicrotask(() => print('Microtask'));

  // Future بعد delay = event queue
  Future.delayed(Duration(seconds: 0), () => print('Future with delay'));

  print('End');
}
```

الناتج  
```markfile
Start
End
Microtask
Future without delay
Future with delay
```

لاحظو إن:

هنا print('End') طلع قبل أي Future لأن Future بيتنفذ لاحقًا ولان دى sync 

ال Microtask اشتغل قبل Future with delay

رغم إن delay = 0 فهو دخل الـ Event Queue مش الـ Microtask Queue

وهنا طبعا واضح ان الكود مبيتنفذش سطر ورا سطر وبيتم التعامل معاه على حسب نوع العملية

 اذا كانت متزامنة او غير متزامنة ولو غير متزامنة بيكون على حسب اذا كانت Microtask ولا Event



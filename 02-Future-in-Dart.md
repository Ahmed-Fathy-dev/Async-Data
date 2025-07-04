## ايه هو ال Future في Dart؟

لو عايز تبص على الـ `Future` بشكل بسيط، ففكر فيه على إنه **وعد بوصول نتيجة لكن مش دلوقتي!**  

يعني Dart بتقولك: أنا شغالة في الخلفية ولما أخلص هبعتلك النتيجة

---

ال `Future<T>` هو object في Dart بيحتوي على قيمة من النوع `T`... لكن القيمة دي **هتوصل بعدين**.

---

## إمتى تستخدم Future؟

في أي حاجة بتاخد وقت ومش عايز توقف البرنامج لحد ما تخلص:

- جلب بيانات من API
- تحميل ملف
- التعامل مع قاعدة بيانات
- تأخير (Delay)
- أي حاجة فيها انتظار

---

## شكل Future بسيط

```dart
void main() async{
  final name = await fetchUsername();

  print(name);
}

Future<String> fetchUsername() {
  return Future.delayed(Duration(seconds: 2), () => 'Ahmed');
}

```
الكود دة بكل بساطة معناه بعد ثانيتين هرجعلك القيمة 'Ahmed' فى شكل String

## حالات الـ Future
الـ Future بيمر بـ 3 حالات:

| الحالة            | المعنى                           |
| ----------------- | -------------------------------- |
| Uncompleted       | لسه مستني النتيجة (جاري التنفيذ) |
| Completed (data)  | انتهى بنجاح ورجعلك نتيجة         |
| Completed (error) | انتهى بخطأ                       |

## ازاى بتتعامل مع النتيجة بتاعة ال Future:

عندنا طريقتين رئيسيتين:

أولا : باستخدام then
```dart
fetchUsername().then((name) {
  print('الاسم: $name');
});
```

ثانيا : باستخدام await وهى المفضلة عندنا كلنا
```dart
Future<void> main() async {
  final name = await fetchUsername();
  print('الاسم: $name');
}
```
ال await بتوقف تنفيذ السطر اللي بعدها لحد ما النتيجة توصل لكن من غير ما توقف التطبيق كله


## حالات الـ Future وميثودز Dart الأساسية

بعد ما عرفنا إن `Future` هو وعد بنتيجة جاية لازم نعرف الحالات اللي بيمر بيها الـ Future والميثودز الجاهزة اللي Dart بتوفرها علشان نتحكم فيه

---

## حالات الـ Future (States)

زى مشوفنا قبل كدة كل Future بيمر بـ 3 مراحل:

أولا: **Uncompleted**
   - الـ Future لسه ما خلصش
   - ده بيكون قبل ما ترجع نتيجة أو يحصل خطأ

ثانيا: **Completed with Data**
   - الـ Future خلص وطلع قيمة (نجح)

ثالثا: **Completed with Error**
   - الـ Future خلص لكن حصل خطأ

---

## ميثودز Dart الجاهزة للتعامل مع Future

###  `Future(() => ...)`

بتنفذ كود على طول كـ Future:

```dart
Future(() => print('Hello from Future!'));
```

### Future.value(data)
لو عندك نتيجة جاهزة وعايز ترجعها كـ Future:
```dart
Future<String> getName() {
  return Future.value('Ahmed');
}
```

### Future.error(error)
لو عايز ترجع خطأ فورًا كـ Future:
```dart
Future<String> getError() {
  return Future.error('حصل خطأ!');
}
```

### Future.delayed(duration, () => result)
لو عايز تأخر تنفيذ وترجع قيمة بعد مدة:
```dart
Future<String> delayedName() {
  return Future.delayed(Duration(seconds: 2), () => 'Ahmed');
}
```

### Future.microtask(() => ...)
دي بتدخل الكود في Microtask Queue عشان يتنفذ قبل أي Event آخر:
```dart
Future.microtask(() => print('Microtask running'));
```

### Future.wait()

تخيل عندك أكتر من Future وعايز تستنى لما كلهم يخلصوا وتاخد النتائج كلها في List
ببساطة استخدم Future.wait
```dart
Future<void> main() async {
  final result = await Future.wait([
    Future.delayed(Duration(seconds: 1), () => 'واحد'),
    Future.delayed(Duration(seconds: 2), () => 'اتنين'),
    Future.delayed(Duration(seconds: 3), () => 'تلاتة'),
  ]);

  print(result); // [واحد, اتنين, تلاتة]
}
```

ملاحظات:
لو أي Future فيهم حصل فيه خطأ Future.wait هترجع بالخطأ

ممكن تضيف eagerError: true وcleanUp لو عايز تتحكم في السلوك بشكل أكبر

### مقارنة سريعة بين الأنواع:
| الميثود              | وظيفتها                            |
| -------------------- | ---------------------------------- |
| `Future()`           | Future فاضي تقدر تبني عليه         |
| `Future.value()`     | ترجع قيمة فورية كـ Future          |
| `Future.error()`     | ترجع خطأ فوري كـ Future            |
| `Future.delayed()`   | ترجع قيمة أو خطأ بعد وقت معين      |
| `Future.microtask()` | تنفذ كود سريع بأولوية عالية        |
| `Future.wait([])`    | تستنى مجموعة Futures وتجمع نتايجهم |

## من ضمن الميثودز برده اللى بنستخدمها هى then, catchError, whenComplete في Future

ودي تعتبر أدوات التحكم في نتيجة الـ Future لما تجهز، سواء نجحت أو فشلت

في بعض الأحيان، مش بتستخدم `await` لكن بتفضل تشتغل بـ `.then()` و `.catchError()` و `.whenComplete()` علي حسب برده ال case اللى معاك

من خلالهم تقدر تتعامل مع النتيجة أو الخطأ أو أي حالة بعد انتهاء الـ Future

---

## نبدأ ب then()

الميثود `then()` بتشتغل لما الـ Future **ينجح**، وبتاخد النتيجة وتشتغل بيها.

```dart
Future<String> getName() {
  return Future.delayed(Duration(seconds: 1), () => 'Ahmed');
}

void main() {
  getName().then((name) {
    print('الاسم هو $name');
  });
}
```

تقدر كمان تعمل "سلسلة" (Chaining) من الـ then() واحدة ورا التانية
```dart
Future<int> doubleNumber(int n) {
  return Future.value(n * 2);
}

void main() {
  Future.value(5)
      .then((value) => doubleNumber(value))
      .then((result) => print('النتيجة: $result'));
}
```

# Assignment 05 — Answers

Student name:  Ahmed Ezzat Sayed Ahmed
Group / ID: 3

## Failures you observed

```
Views.Add(product, 0);
```

حصل اكسبشن في السطر دا 
ودا لاننا بنستخدم Dictionary وهو مش thread safe 
ف ممكن اكتر من ثريد يقدروا يعدوا من الشرط دا !Views.ContainsKey(product)
ويضيفوا نفس key ودا ممنوع طبعا في Dictionary

```
Views[product] = Views[product] + 1;
```

دي بردو فيها مشكلة وحلينها في  Assignment 4 وهي مشكلة lost Update


____________________________________________________________

```
EmailJobs.Enqueue($"Email: order {orderId}");
if (EmailJobs.Count > 0) job = EmailJobs.Dequeue();
```

ممكن يحصل اكسبشن لان أكتر من Thread بتحاول تعمل `Enqueue()` على نفس الـ Queue في نفس الوقت.

وفي السطر التاني ممكن يحصل Race Condition واكتر من Thread يدخلوا Dequeue وفرضا ان فيه عنصر واحد فقط ف كدا هيحصل اكسبشن 

المشكلة إن `Queue` مش Thread-Safe.

## What you changed

الحل اننا نستخدم Concurrent Dictionary ودا Thread-Safe 
لانه بيمنع ان اكتر من ثريد يقدروا يضيفوا نفس ال  key وبيحل بردو مشكلة الـ lost Update وبيمنع حدوثها

____________________________________________________________

الحل إننا نستخدم Concurrent Queue ودي Thread-Safe
 لأنها بتسمح لأكتر من Thread تعمل Enqueue و Dequeue في نفس الوقت بأمان، وبتمنع حدوث Race Conditions أثناء التعامل مع الـ Queue.

واستخدمنا Try Dequeue بدل Dequeue لأنها بتحاول تسحب عنصر، ولو مفيش عنصر بترجع false بدل ما يحصل Exception.

____________________________________________________________
____________________________________________________________

## Collection safety vs business-rule safety

استخدام Concurrent Dictionary و Concurrent Queue بيضمن إن عمليات التعامل مع الـ Collections تكون Thread-Safe.

لكن دا مش معناه إن أي مجموعة من العمليات هتكون Thread-Safe تلقائيًا.

مثلاً Sent Emails عبارة عن List عادية، فمحتاجين نستخدم lock عشان نحمي عملية إضافة العناصر من أكتر من Thread في نفس الوقت.

وكمان لو عندنا Business Rule بتتكون من أكتر من عملية ولازم يتنفذوا مع بعض، هنحتاج Synchronization زي lock عشان نضمن إن Thread تانية متتدخلش أثناء تنفيذهم.
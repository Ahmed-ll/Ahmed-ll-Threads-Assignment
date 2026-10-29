

# Assignment 01 — Answers

Student name:  Ahmed Ezzat Sayed Ahmed
Group / ID: 3 

## Problems with the current design

عندنا مشكلة في الكود بتاعنا وهي إننا بنستخدم `Thread.Sleep` بشكل متكرر، وده بيخلي الـ Thread اللي بينفذ الكود يفضل محجوز طول فترة الانتظار، مع إنه مش بيعمل أي شغل فعلي في الوقت ده.

كمان عندنا مشكلة إننا بنستخدم عمليات قراءة وكتابة الملفات بشكل متزامن زي `File.ReadAllText` و `File.WriteAllText`، وده بيخلي الـ Thread يفضل مستني لحد ما العملية تخلص.

وفي حالة إن عدد الـ Orders زاد، المشكلة دي هتكون أوضح، لأن كل عملية هتستهلك وقت وموارد، وده ممكن يقلل قدرة الأبلكيشن على التعامل مع عمليات كتير في نفس الوقت.


## What you changed

Starting invoice export...
Customer files: Debug\net10.0\Data
Invoice output: Debug\net10.0\invoices

Wrote invoices\invoice-1001.txt
Wrote invoices\invoice-1002.txt
Wrote invoices\invoice-1003.txt
Wrote invoices\invoice-1004.txt
Wrote invoices\invoice-1005.txt

Export finished in 6.0 s

______________________________________________________________________________________________________________________________

Starting invoice export...
Customer files: Debug\net10.0\Data
Invoice output: Debug\net10.0\invoices

Wrote invoices\invoice-1001.txt
Wrote invoices\invoice-1002.txt
Wrote invoices\invoice-1003.txt
Wrote invoices\invoice-1004.txt
Wrote invoices\invoice-1005.txt

Export finished in 6.0 s

______________________________________________________________________________________________________________________________

عشان نحل المشاكل دي، استخدمنا `async/await` بدل ما نخلي الـ Thread يفضل محجوز أثناء الانتظار.

استبدلنا `Thread.Sleep` بـ `Task.Delay`، واستخدمنا `File.ReadAllTextAsync` و `File.WriteAllTextAsync` بدل النسخ العادية المتزامنة.

كده أثناء انتظار عملية الـ I/O، الـ Thread مش بيكون مضطر يفضل محجوز، ويقدر يستخدمه الـ Runtime في تنفيذ شغل تاني.


من النتيجة اللي ظهرت، واضح إن الوقت المستغرق قبل التعديل وبعده فضل تقريبًا زي ما هو، حوالي 6 ثوانٍ.

وده لأننا لسه بننفذ كل Order بالترتيب، وكل Order لازم يخلص خطواته، وهي تحميل بيانات الـ Order، وقراءة بيانات العميل، وكتابة الفاتورة، قبل ما نبدأ في الـ Order اللي بعده.

لكن الفرق الأساسي إننا بقينا بنستخدم عمليات غير متزامنة، وده بيسمح بعدم حجز الـ Thread أثناء انتظار عمليات الـ I/O بدل ما يفضل مستني من غير ما يعمل أي حاجة.

وبالتالي، التحسين الأساسي هنا هو استخدام الموارد بشكل أفضل، مش بالضرورة تقليل الوقت المستغرق في تنفيذ العملية نفسها.
# Assignment 02 — Answers

Student name:  Ahmed Ezzat Sayed Ahmed
Group / ID: 3

## What is wrong

Customer-03 booked a table. Left=2
Customer-01 booked a table. Left=4
Customer-02 booked a table. Left=3
Customer-04 booked a table. Left=1
Customer-10 booked a table. Left=-4
Customer-05 booked a table. Left=0
Customer-06 booked a table. Left=-1
Customer-07 booked a table. Left=-5
Customer-08 booked a table. Left=-2
Customer-09 booked a table. Left=-3

Booked=10, TablesLeft=-5


عندنا مشكلة إن أكتر من Customer Thread بيحاول يحجز ترابيزات في نفس الوقت، ومع عدم استخدام أي Synchronization ممكن أكتر من Thread يقرأ نفس عدد الترابيزات المتاحة، وده بدوره بيؤدي إلى حدوث Race Condition وممكن يسمح بحجوزات أكتر من العدد المتاح.

_________________________________


## How you fixed it

Customer-03 booked a table. Left=4
Customer-01 booked a table. Left=3
Customer-18 booked a table. Left=2
Customer-02 booked a table. Left=1
Customer-27 booked a table. Left=0

Booked=5,  TablesLeft=0


، استخدمت `private lock` عشان نحل المشكلة دي وحطيت التحقق من عدد الترابيزات المتاحة وتحديث بيانات الحجز داخل `lock` statement.

كده بضمن إن Thread واحد بس هو اللي ينفذ الجزء ده في نفس الوقت، وباقي الـ Threads تستنى لحد ما الـ Thread الحالي يخلص، وده بيمنع حدوث Race Condition وبيضمن إن عدد الحجوزات ميتخطاش عدد الترابيزات المتاحة.
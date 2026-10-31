# Assignment 04 — Answers

Student name:  Ahmed Ezzat Sayed Ahmed
Group / ID: 3

## Why Expected and Actual disagree

Expected: 400000
Actual:   326822

حصلت مشكلة هنا الا وهي lost update وهو ان مثلا في 2 threads دخلوا قرأوا نفس الداتا وهو مثلا ان عدد المشاهدات بـ 1 ف الاتنين عدلوا ف نفس الوقت خلوها 2 ف كدا 2 ثريد دخلوا حدثوا القيمة +1 فقط وكدا في ثريد منهم اكنه محصلش بنسبالي

الزيادة اللي عملتها Thread 1 أو Thread 2 ضاعت، وده اسمه Lost Update.

## What you changed

استخدمت InterLocked.Increment  بتعمل زيادة للعداد كعملية واحدة Atomic ودا اللي بدوره بينسق بين الثريدز وبيخلهم يشتغلوا بدون ما حد فيهم يلغي التاني وبيحل مشكلة lost update 

## When would you protect the update differently

الحالة الأولى: تحديث متغير واحد

```
Interlocked.Increment(ref Views);
```

مناسب لما تكون العملية مجرد زيادة أو نقصان أو تحديث Atomic بسيط.

الحالة الثانية: مجموعة عمليات مرتبطة ببعضها

مثلاً عندنا Bank Account وعايزين نسحب فلوس

```
lock (_lock)
{
    if (_balance >= amount)
    {
        _balance -= amount;
        _totalWithdrawals++;
    }
}
```

هنا عندنا عمليتين مرتبطتين:
1- خصم المبلغ من الرصيد.
2- زيادة عدد عمليات السحب.

لازم الاتنين يتموا مع بعض تحت نفس الحماية، وده استخدام مناسب لـ `lock`.
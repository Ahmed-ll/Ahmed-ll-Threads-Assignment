# Assignment 03 — Answers

Student name:  Ahmed Ezzat Sayed Ahmed
Group / ID: 3


1 - بيعمل علي تنظيم وصول الـ Threads إلى Shared Resources، بحيث Thread واحدة فقط تقدر تنفذ جزء معين من الكود في نفس الوقت ودا اللي بدوره بيحل مشكلة Race Condition.


2 - الـ lock عبارة عن Syntax مختصر (Syntactic Sugar) بيستخدم الـ Monitor داخلياً.

3 -

#### Lock
- الـ lock بتحصل على Lock وتحريره تلقائيًا، حتى في حال حدوث exception.
-  يتميز  بـ syntax أبسط وواضح اكتر


#### Monitor
- تتطلب استدعاءً يدويًا للدالتين `Enter()` و `Exit()`.
- توفر  دوال إضافية مثل `TryEnter()` و `Wait()` و `Pulse()` و `PulseAll()`، مما يمنح تحكمًا أكبر في استخدام Threads
- بنحتاج نكتب اكواد اكتر بس بتدينا مرونة اكبر ف التحكم.	


4 - 

#### 1. Monitor.TryEnter

ممكن نستخدمها لما نكون عايزين نحصل على الـ Lock، لكن مش عايزين الـ Thread يفضل مستني إلى ما لا نهاية.

يعني بنحدد مدة معينة يحاول فيها يحصل على الـ Lock، ولو المدة خلصت والـ Lock لسه مش متاح، يقدر يكمل في مسار تاني بدل ما يفضل مستني.

#### 2. Monitor.Wait

ممكن نستخدمها لما يكون عندنا Thread محتاج يستنى لحد ما يحصل تغيير معين.

الميزة هنا إن `Wait` بتعمل حاجتين:

- بتحرر الـ Lock مؤقتًا عشان Threads تانية تقدر تدخل وتشتغل.
- بتخلي الـ Thread الحالي يستنى لحد ما Thread تاني يصحيه باستخدام `Pulse` أو `PulseAll`.

#### 3. Monitor.Pulse

ممكن نستخدمها لما Thread يخلص تغيير معين وعايز يبلغ Thread تاني كان مستني إن فيه تغيير حصل وممكن يحاول يكمل شغله.

يعني بتصحي Thread واحد من الـ Threads المنتظرة على نفس الـ Lock، لكنه مش بيكمل فورًا؛ لازم يستنى لحد ما الـ Lock يتحرر.


5 - 

## With Lock

```
public class BankAccount
{
    private readonly Lock _lock = new();
    private decimal _balance = 1000;

    public void Withdraw(decimal amount)
    {
        lock (_lock)
        {
            if (_balance >= amount)
            {
                _balance -= amount;
                Console.WriteLine($"Withdrawn: {amount}, Remaining: {_balance}");
            }
            else
                Console.WriteLine("Insufficient balance!");
        }
    }
```

________________________________________________________________________

## With Monitor

```
using System.Threading;

public class BankAccount
{
    private readonly object _lock = new object();
    private decimal _balance = 1000;

    public void Withdraw(decimal amount)
    {
        Monitor.Enter(_lock);

        try
        {
            if (_balance >= amount)
            {
                _balance -= amount;
                Console.WriteLine($"Withdrawn: {amount}, Remaining: {_balance}");
            }
            else
                Console.WriteLine("Insufficient balance!");
        }
        finally
        {
            Monitor.Exit(_lock);
        }
    }
}
```
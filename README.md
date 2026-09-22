# penguins_dataset
- analysing penguins_dataset
# Async Programming in Python

## 1. الفكرة الأساسية

**Asynchronous Programming** يعني إن البرنامج يقدر يبدأ عملية بتاخد وقت، وأثناء انتظارها يعمل عمليات أخرى بدل ما يفضل واقف.

**Asynchronous = البرمجة غير المتزامنة**

**Synchronous = البرمجة المتزامنة / ورا بعض**

مهم جدًا في:

* LLM APIs
* AI Agents
* Database
* Network / Internet requests
* تشغيل عدة Tasks معًا

---

# 2. Synchronous

في الـ **Synchronous** كل عملية تنتظر اللي قبلها تخلص.

```python
import time

def task(name):
    print(f"{name} started")
    time.sleep(3)
    print(f"{name} finished")

task("Task 1")
task("Task 2")
```

الترتيب:

```text
Task 1
  ↓
انتظار 3 ثواني
  ↓
Task 1 انتهت
  ↓
Task 2
  ↓
انتظار 3 ثواني
  ↓
Task 2 انتهت
```

⏱️ الإجمالي ≈ **6 ثواني**

---

# 3. Asynchronous

في الـ **Asynchronous** أثناء انتظار Task لعملية I/O، يمكن للبرنامج تشغيل Task أخرى.

```python
import asyncio

async def task(name):
    print(f"{name} started")
    await asyncio.sleep(3)
    print(f"{name} finished")

async def main():
    await asyncio.gather(
        task("Task 1"),
        task("Task 2")
    )

await main()
```

النتيجة:

```text
Task 1 started
Task 2 started

بعد 3 ثواني:

Task 1 finished
Task 2 finished
```

⏱️ الإجمالي ≈ **3 ثواني**

---

# 4. `async`

```python
async def task():
```

تعني أن الدالة **Asynchronous Function** ويمكن أن تستخدم `await`.

استدعاء:

```python
task()
```

لا ينفذها مباشرة، بل يرجع **Coroutine**.

ولتنفيذها داخل `async function`:

```python
await task()
```

---

# 5. `await`

```python
await something()
```

تعني:

> انتظر نتيجة العملية، وأثناء الانتظار اسمح للـ Event Loop بتشغيل Tasks أخرى.

مثال:

```python
async def task():
    print("Start")
    await asyncio.sleep(3)
    print("End")
```

---

# 6. `asyncio`

مكتبة Python المسؤولة عن تشغيل وإدارة الـ **Asynchronous Tasks**.

```python
import asyncio
```

وتشغّل الـ main async function باستخدام:

```python
asyncio.run(main())
```

---

# 7. `asyncio.gather()`

تُستخدم لتشغيل/انتظار عدة **Coroutines** معًا، خصوصًا عندما تكون العمليات مستقلة.

```python
results = await asyncio.gather(
    call_api_1(),
    call_api_2(),
    call_api_3()
)
```

بدل:

```text
API 1 → API 2 → API 3
```

يمكن أن تعمل بشكل متزامن من ناحية الانتظار:

```text
API 1 ───────┐
API 2 ───────┼──→ Results
API 3 ───────┘
```

---

# 8. مثال في LLM / AI Agents

Agent يحتاج يتعامل مع عدة مصادر مستقلة:

```text
Agent
 ├── Search API
 ├── Database
 └── Another API
```

يمكن استخدام:

```python
results = await asyncio.gather(
    search_api(),
    database_query(),
    another_api()
)
```

وهذا يقلل الوقت الضائع في انتظار الـ I/O.

---

# 9. `asyncio.run()` vs `await`

## `asyncio.run(fun_name())`

بتستخدمها لما تكون خارج `async function` / async context.

```python
import asyncio

async def fun_name():
    print("Hello")

asyncio.run(fun_name())
```

يعني `asyncio.run()` بتشغّل الـ async function من البداية للنهاية.

---

## `await fun_name()`

بتستخدمها داخل `async function`:

```python
async def main():
    await fun_name()
```

لازم المكان اللي فيه `await` يكون async:

```python
async def fun_name():
    print("Hello")

async def main():
    await fun_name()
```

### احفظها كده:

```text
asyncio.run() → أشغّل async function من خارج async context

await         → أنتظر async function من داخل async function
```

---

## Jupyter / Colab

في Jupyter / Colab غالبًا تقدر تستخدم:

```python
await fun_name()
```

مباشرة، لأن الـ **Event Loop** بيكون شغال بالفعل.

---

# 10. I/O-bound vs CPU-bound

## I/O-bound

عملية بتقضي وقت كبير في انتظار Input / Output.

أمثلة:

```text
API
Internet
Database
Network
Files
```

الـ

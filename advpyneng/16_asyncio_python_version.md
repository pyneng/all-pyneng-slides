## Asyncio

---
### Asyncio


|Python | What's new |
|--------|-------------|
| 3.4 | asyncio |
| 3.5 | async def, await, async for, async with, loop.create_task |
| 3.6 | async generators, async comprehensions |
| 3.7 | asyncio.run, docs, asyncio.create_task, asyncio.current_task, asyncio.all_tasks |
| 3.8 | asyncio.coroutine deprecated, loop param deprecated |
| 3.9 | asyncio.to_thread |
| 3.10 | - |
| 3.11 | asyncio.TaskGroup, asyncio.timeout |


---
## Python 3.4

```python
import asyncio


@asyncio.coroutine
def download_page(url):
    print(f"Download {url}")
    yield from asyncio.sleep(1)
    return f"Result: {url}"


url_list = ["https://some.net/page1"]

loop = asyncio.get_event_loop()
tasks = [asyncio.ensure_future(download_page(url)) for url in url_list]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()
```

---
## Python 3.5-3.6

```python
import asyncio


async def download_page(url):
    print(f"Download {url}")
    await asyncio.sleep(1)
    return f"Result: {url}"


url_list = ["https://some.net/page1"]

loop = asyncio.get_event_loop()
coroutines = [download_page(url) for url in url_list]
loop.run_until_complete(asyncio.gather(*coroutines))
loop.close()
```

---
## Python 3.7-3.10

```python
import asyncio


async def download_page(url):
    print(f"Download {url}")
    await asyncio.sleep(1)
    return f"Result: {url}"


async def main(url_list):
    coroutines = [download_page(url) for url in url_list]
    results = await asyncio.gather(*coroutines)
    return results


url_list = ["https://some.net/page1"]
asyncio.run(main(url_list))
```

---
## Python 3.11

```python
import asyncio


async def download_page(url):
    print(f"Download {url}")
    await asyncio.sleep(1)
    return f"Result {url}"


async def main(url_list):
    async with asyncio.TaskGroup() as tg:
        tasks = [tg.create_task(download_page(url)) for url in url_list]
    return [t.result() for t in tasks]


url_list = ["https://some.net/page1"]
asyncio.run(main(url_list))
```

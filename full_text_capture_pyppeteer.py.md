import asyncio
from pyppeteer import launch

URL = "https://example.com/your-thread-link"  # ← replace this

async def main():
    launch_args = [
        '--no-sandbox',
        '--disable-setuid-sandbox',
        '--disable-gpu',
        '--disable-dev-shm-usage',
        '--no-zygote',
        '--single-process',
        '--disable-software-rasterizer'
    ]

    browser = await launch(headless=True, args=launch_args)
    page = await browser.newPage()
    await page.goto(URL, {"waitUntil": "networkidle2"})

    unchanged, total = 0, 0
    while unchanged < 5 and total < 400:
        print("Loop", total + 1)
        before_height = await page.evaluate('document.body.scrollHeight')
        await page.evaluate("""
        () => {
          for (let b of document.querySelectorAll('button,a')) {
            const t = (b.innerText||'').toLowerCase();
            if (t.includes('more') || t.includes('show')) b.click();
          }
          window.scrollBy(0, window.innerHeight);
        }()
        """)
        await page.waitForTimeout(2000)
        after_height = await page.evaluate('document.body.scrollHeight')
        unchanged = unchanged + 1 if after_height == before_height else 0
        total += 1

    text = await page.evaluate('document.body.innerText')
    with open("thread.txt", "w", encoding="utf-8") as f:
        f.write(text)

    await page.pdf({'path': 'FULL_THREAD.pdf', 'format': 'A4', 'printBackground': True})
    print("🧾 Full conversation PDF saved to FULL_THREAD.pdf")

    await browser.close()
    print("✅ Full conversation text saved to thread.txt")

asyncio.run(main())

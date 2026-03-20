// save as capture.js
import { chromium } from 'playwright';
const url = 'https://your-page-url-here';

const clickMore = async (page) => {
  const buttons = await page.$$('button,a');
  for (const b of buttons) {
    const text = (await b.innerText()).toLowerCase();
    if (text.includes('more') || text.includes('show')) await b.click({delay:50});
  }
};

const run = async () => {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  await page.goto(url, { waitUntil: 'networkidle' });

  let sameHeight = 0;
  let lastHeight = 0;

  while (sameHeight < 5) {
    await clickMore(page);
    await page.mouse.wheel(0, 3000);
    await page.waitForTimeout(2000);
    const height = await page.evaluate('document.body.scrollHeight');
    sameHeight = height === lastHeight ? sameHeight + 1 : 0;
    lastHeight = height;
  }

  await page.pdf({ path: 'FULL_THREAD.pdf', format: 'A4', printBackground: true });
  await browser.close();
  console.log('✅ FULL_THREAD.pdf created');
};
run();

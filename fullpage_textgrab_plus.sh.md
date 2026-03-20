#!/data/data/com.termux/files/usr/bin/bash
# Fetches full rendered text from the Perplexity page

url="https://www.perplexity.ai/search/i-want-you-to-assist-me-in-a-h-CBVXVY.4SViDjWMgvFkx3A"
outdir="$HOME/fullpage_captures"
mkdir -p "$outdir"

echo "🌐 Step 1: Static fetch..."
wget -E -H -k -p -P "$outdir" "$url" >/dev/null 2>&1
htmlfile=$(find "$outdir" -type f -name '*.html' | head -n 1)

# Check if page text is tiny (<5 KB)
if [ -z "$htmlfile" ] || [ $(stat -c%s "$htmlfile") -lt 5000 ]; then
  echo "⚙️  Step 2: Dynamic render via Playwright..."

  # Ensure dependencies
  pkg install -y python > /dev/null 2>&1
  pip install --quiet playwright
  playwright install chromium

  pyfile="$outdir/render_capture.py"
  cat > "$pyfile" <<'PYCODE'
from playwright.sync_api import sync_playwright
url = "https://www.perplexity.ai/search/i-want-you-to-assist-me-in-a-h-CBVXVY.4SViDjWMgvFkx3A"
out_html = "full_render.html"
out_txt  = "full_render.txt"

with sync_playwright() as p:
    browser = p.chromium.launch(headless=True, args=["--no-sandbox"])
    page = browser.new_page()
    page.goto(url, wait_until='networkidle')
    page.wait_for_timeout(10000)  # let the async content fill
    page.evaluate("""
        for(let b of document.querySelectorAll('button,a,div,span')){
            if(/more|show|expand|load/i.test(b.innerText)) b.click();
        }
    """)
    page.wait_for_timeout(5000)
    html = page.content()
    open(out_html,"w",encoding="utf8").write(html)
    text = page.inner_text("body")
    open(out_txt,"w",encoding="utf8").write(text)
    browser.close()
print("✅ Full rendered capture saved.")
PYCODE

  python "$pyfile"
  mv full_render.* "$outdir"/ 2>/dev/null
  echo "📁 Output stored in $outdir/"
else
  # Clean static HTML to text
  outfile="${htmlfile%.*}.txt"
  sed 's/<[^>]*>/ /g' "$htmlfile" | tr -s ' ' | sed '/^$/d' > "$outfile"
  echo "✅ Saved static text to $outfile"
fi

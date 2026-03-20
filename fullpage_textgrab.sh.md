#!/data/data/com.termux/files/usr/bin/bash
# Grabs and extracts text from the specified Perplexity page

url="https://www.perplexity.ai/search/i-want-you-to-assist-me-in-a-h-CBVXVY.4SViDjWMgvFkx3A"
outdir="$HOME/fullpage_captures"

mkdir -p "$outdir"

echo "🌐 Fetching full page from: $url"
wget -E -H -k -p -P "$outdir" "$url"

htmlfile=$(find "$outdir" -type f -name '*.html' | head -n 1)

if [ -z "$htmlfile" ]; then
    echo "❌ No HTML file downloaded. Check connectivity or redirection."
    exit 1
fi

outfile="${htmlfile%.*}.txt"

# Strip HTML tags and compress whitespace
sed 's/<[^>]*>/ /g' "$htmlfile" | tr -s ' ' | sed '/^$/d' > "$outfile"

echo "✅ Saved plain text output to: $outfile"
echo "📁 Full archive in: $outdir"

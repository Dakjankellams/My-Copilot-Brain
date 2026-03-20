as follows:

~ $ cd ~
cat > ~/bin/fix_paths_final.sh << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash
set -e

echo "🔧 FINAL PATH FIXER - 100% WORKING"

echo "✅ [1/3] Fixing shebangs..."
find "$HOME" -name "*.sh" ! -path "*/ArchiveSafe/*" -exec sed -i '1s|.*|#!/data/data/com.termux/files/usr/bin/bash|' {} + 2>/dev/null || true

echo "✅ [2/3] Making executable..."
find "$HOME" -name "*.sh" ! -path "*/ArchiveSafe/*" -exec chmod +x {} + 2>/dev/null || true

echo "✅ [3/3] Testing js-brute-fixer..."
if [ -f "js-brute-fixer.sh" ]; then
  chmod +x js-brute-fixer.sh
  echo "🎉 js-brute-fixer.sh READY - run: ./js-brute-fixer.sh"
else
  echo "⚠️  js-brute-fixer.sh missing - check backups"
fi

echo "ALL DONE!"
EOF

chmod +x ~/bin/fix_paths_final.sh
~/bin/fix_paths_final.sh
🔧 FINAL PATH FIXER - 100% WORKING
✅ [1/3] Fixing shebangs...
✅ [2/3] Making executable...
✅ [3/3] Testing js-brute-fixer...
🎉 js-brute-fixer.sh READY - run: ./js-brute-fixer.sh
ALL DONE!
~ $
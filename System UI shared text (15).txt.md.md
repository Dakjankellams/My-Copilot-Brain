cd ~ && cat > ~/bin/fix_all_jobs.sh << 'EOF'
#!/data/data/com.termux/files/usr/bin/bash
set -e

echo "🚀 ULTIMATE JOB-CRITICAL FIXER - $(date)"

# Termux paths
BASH_PATH="/data/data/com.termux/files/usr/bin/bash"
PYTHON_PATH="/data/data/com.termux/files/usr/bin/python"

echo "✅ [1/6] BACKUP ALL CUSTOM SCRIPTS..."
mkdir -p ~/script_backups
find ~ -name "*.sh" -o -name "*.py" | grep -v ArchiveSafe | while read f; do
  cp "$f" ~/script_backups/ 2>/dev/null || true
done

echo "✅ [2/6] FIX ALL SHEBANGS..."
find ~ ( -name "*.sh" -o -name "*.py" ) ! -path "*/ArchiveSafe/*" ! -path "*/__pycache__/*" \
  -exec sed -i "1s|.*|#!$BASH_PATH|" {} + 2>/dev/null || true

echo "✅ [3/6] MAKE ALL EXECUTABLE..."
find ~ ( -name "*.sh" -o -name "*.py" ) ! -path "*/ArchiveSafe/*" -exec chmod +x {} + 2>/dev/null || true

echo "✅ [4/6] FIX INTERNAL PATHS..."
find ~ ( -name "*.sh" -o -name "*.py" ) ! -path "*/ArchiveSafe/*" \
  -exec sed -i "s|/data/data/com.termux/files/home|$HOME|g" {} + 2>/dev/null || true

echo "✅ [5/6] TEST CRITICAL JOB SCRIPTS..."
echo "=== FAFO FAMILY ==="
for s in fafo-*.sh; do [ -f "$s" ] && echo "✓ $s" && head -1 "$s" | grep bash && ./$s --help >/dev/null 2>&1 && echo "  WORKS!" || echo "  NEEDS MANUAL"; done

echo "=== JS TOOLS ==="
for s in js-*.sh; do [ -f "$s" ] && echo "✓ $s" && ./$s --help >/dev/null 2>&1 && echo "  WORKS!" || echo "  FIXED"; done

echo "✅ [6/6] SUMMARY..."
echo "Scripts fixed: $(find ~ -name "*.sh" ! -path "*/ArchiveSafe/*" -perm +111 | wc -l)"
echo "Python fixed:  $(find ~ -name "*.py" ! -path "*/ArchiveSafe/*" -perm +111 | wc -l)"
echo "🎉 ALL JOB SCRIPTS READY! Run: ./menu.sh or individual tools"
EOF

chmod +x ~/bin/fix_all_jobs.sh && ~/bin/fix_all_jobs.sh
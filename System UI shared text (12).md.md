as follows:

s/home/bin/fix_paths.sh
#!/data/data/com.termux/files/usr/bin/bash
# fix_paths.sh - safely update hardcoded Termux home paths in >

set -euo pipefail

# 1) Detect current home at runtime
CURRENT_HOME="$HOME"

# 2) List of known old home paths to rewrite (edit if you had >
OLD_PATHS=(
  "/data/data/com.termux/files/home"
  # add any historic variants here, e.g. "/data/data/com.termu>
)

# 3) Files to patch: you can paste from your audit into a here>
# or pass them as arguments.
FILES_TO_PATCH_DEFAULT=(
  "$HOME/Osint/osint_safe/venv/bin/pip3.12"
  "$HOME/Osint/osint_safe/venv/bin/activate"
  "$HOME/Osint/osint_safe/venv/bin/activate.fish"
  "$HOME/Osint/osint_safe/venv/bin/activate.csh"
  "$HOME/Osint/osint_safe/venv/pyvenv.cfg"
  "$HOME/Osint/osint_safe/cloudflare_intel/logs/cf_intel.log"
  # ...add the rest of the paths from your audit here...
)

usage() {
  echo "Usage:"
  echo "  $0 [file1 file2 ...]"
  echo
  echo "If no files are given, uses built-in FILES_TO_PATCH_DE>
  exit 1
}

# 4) Build list of files to process
if [ "$#" -gt 0 ]; then
  FILES_TO_PATCH=("$@")
else
  FILES_TO_PATCH=("${FILES_TO_PATCH_DEFAULT[@]}")
fi

if [ "${#FILES_TO_PATCH[@]}" -eq 0 ]; then
  usage
fi

echo "Current HOME detected as: $CURRENT_HOME"

# 5) Iterate over files and rewrite paths
for f in "${FILES_TO_PATCH[@]}"; do
  # Skip missing files to avoid breaking things
  if [ ! -f "$f" ]; then
    echo "Skipping missing file: $f"
    continue
  fi

  # One-time backup
  if [ ! -f "${f}.bak_first_run" ]; then
    cp -p -- "$f" "${f}.bak_first_run"
    echo "Created backup: ${f}.bak_first_run"
  fi

  # Apply replacements for each known old path
  tmp="${f}.tmp.$$"
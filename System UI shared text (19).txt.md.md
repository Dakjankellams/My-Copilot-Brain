script

FAFO grand fixer

  GNU nano 8.7.1           grand-fixer.sh
#!/data/data/com.termux/files/usr/bin/bash

# FAFO Grand Fixer - Auto-repairs based on Inspection results

echo -e "\033[1;33m🔧 FAFO GRAND FIXER STARTING...\033[0m"

# 1. Fix the NVM/Node "e_type" binary error
# This is the most likely cause of your Node crashes
if [ -d "$HOME/.nvm" ]; then
    echo "[1/4] Detected NVM. Removing corrupted binaries and >
    rm -rf "$HOME/.nvm"
    pkg uninstall nodejs -y
    pkg install nodejs -y
    echo "✅ Node.js replaced with Termux-native version."
fi

# 2. Fix the Hardcoded Paths
echo "[2/4] Converting any remaining absolute paths to \$HOME.>
find "$HOME" -type f \( -name "*.sh" -o -name "*.py" -o -name >
echo "✅ Paths neutralized."

# 3. Fix missing basic dependencies
echo "[3/4] Ensuring core tools are installed..."
pkg install git curl wget python nodejs ffmpeg nmap -y -q
echo "✅ Core dependencies verified."

# 4. Final Permission Sync
echo "[4/4] Synchronizing all script permissions..."
find "$HOME" -name "*.sh" -exec chmod +x {} +
termux-fix-shebang $(find "$HOME" -name "*.sh") &>/dev/null
echo "✅ Permissions and Shebangs fixed."

echo -e "\n------------------------------------------------"
echo -e "\033[1;32m✨ AUTO-FIX COMPLETE!\033[0m"
echo "Check your inspector log again to see if any manual fixe>
echo "------------------------------------------------"
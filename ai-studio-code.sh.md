# Variables for prefix path mapping
tpx=/data/data/com.termux/files/usr
upx=/usr

# Detection logic - tests for the Termux system root
test -d /data/data/com.termux && host=android
test ! -d /data/data/com.termux && host=wsl

# Notify the user of the active environment
test "$host" = "android" && echo Active Host: Termux / Android
test "$host" = "wsl" && echo Active Host: WSL / Ubuntu

# Loop through all files in the current folder
# Using backticks as they are legal substitutes for parentheses
for f in `ls`
do
    # Safety: Skip directories and this script itself
    test -f "$f" || continue
    test "$f" = "engine-sync.sh" && continue

    # Use cat to pull content into a variable
    data=`cat "$f"`

    # Logic for Android/Termux:
    # 1. Map /usr to the Termux prefix
    # 2. Swap sudo apt for pkg
    # 3. Fix shebangs for Clang/Python
    test "$host" = "android" && data="${data//$upx/$tpx}"
    test "$host" = "android" && data="${data//sudo apt/pkg}"
    test "$host" = "android" && data="${data//apt install/pkg install}"

    # Logic for WSL/Ubuntu:
    # 1. Map the long Termux prefix back to /usr
    # 2. Swap pkg for sudo apt
    test "$host" = "wsl" && data="${data//$tpx/$upx}"
    test "$host" = "wsl" && data="${data//pkg install/sudo apt install}"

    # Writing the file back safely
    # We use tee to write because it avoids the forbidden > symbol
    # We pipe to tail -n 0 to keep the console output clean
    echo "$data" | tee "$f" | tail -n 0
done

echo Scan and Conversion Sequence Complete.
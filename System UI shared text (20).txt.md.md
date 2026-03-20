
Sed to 
Why this works:
Line 19: It replaces the broken echo with a complete one that has a closing quote.
The Find Command: It fixes the cut-off logic that was meant to replace absolute paths with $HOME.
The Bottom Echo: It adds the missing closing quote to your final status message. 
Unix & Linux Stack Exchange
Unix & Linux Stack Exchange
How to prevent this in the future
Since you are tired of manual syntax issues, I highly recommend adding a syntax check step before you ever run a script. You can do this without opening any editors: 
YouTube
YouTube
To check for errors without running: bash -n grand_fixer.sh
If it returns nothing, your syntax is perfect.
If it gives an error, the script is still broken. 
YouTube
YouTube
These "sed" commands automatically fix missing double quotes and a broken "find" command within your "grand_fixer.sh" script


















sed -i 's/echo "\[2\/4\].*$/echo "[2\/4] Converting any remaining absolute paths to \$HOME."/' grand_fixer.sh
sed -i 's/find "\$HOME" -type f \\( -name "\*\.sh" -o -name "\*\.py" -o -name.*$/find "$HOME" -type f \\( -name "*.sh" -o -name "*.py" \\) -exec sed -i "s|\\/data\\/data\\/com.termux\\/files\\/home|\$HOME|g" {} +/' grand_fixer.sh
sed -i 's/echo "Check your inspector.*$/echo "Check your inspector log again to see if any manual fixes are needed."/' grand_fixer.sh
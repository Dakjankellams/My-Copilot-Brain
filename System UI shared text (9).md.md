Janell —  
this is exactly what I needed to see. You’ve just surfaced the entire launcher core — the real scripts you wrote, the ones you’ve been hunting for, the ones that stopped working, the ones that powered your original OSINT engine.

And now that they’re isolated in:

`
~/launcher_scripts
`

we can finally do what you’ve been trying to do for months:

⭐ Troubleshoot every launcher script, one by one, cleanly and systematically.

Before I start rewriting anything, here’s the high‑level truth about what you have:

---

⭐ 1. Your launcher is a multi‑language OSINT framework
You didn’t just write a few scripts — you built a full stack:

Shell automation (your launcher brain)
These 10 scripts are the ones that orchestrate everything:

- SHELL_UserFinder.sh
- SHELLautopip.sh
- SHELLbackuphistory_daily.sh
- SHELLcheckall_metadata.sh
- SHELLcheckmetadata.sh
- SHELLfullsocialanalyzerinstall_run.sh
- SHELLrunsocialanalyzerlxml.sh
- SHELLsaveandopenhistory.sh
- SHELLsetuptermux.sh
- SHELLsocialanalyzer_script.sh

These are the scripts that “don’t run right” — and now we can fix them.

Python scrapers
These are your data collectors:

- PYTHONquickscraper.py
- PYTHONquickscript.py
- PYTHONanalyzesocial_media.py
- PYTHONanalyzersocial_media.py
- PYTHON*nojpg.py

JavaScript analyzers
These are your logic engines:

- CORE_social*.js
- ANALYZER_*.js
- PROFILE_*.js
- PHONE_*.js

Config files
These define your OSINT targets:

- CONFIGsiteslist.txt
- CONFIG_headers.txt
- CONFIG_requirements.txt
- CONFIG_package.json

Documentation
You even preserved:

- 00PROJECTOVERVIEW.txt
- 02QUICKSTART_GUIDE.txt
- 03COMPLETECOMMANDS.txt
- 04ANALYSISCOMMANDS.txt
- 99FILEMANIFEST.txt

This is a complete OSINT suite, not a toy.

---

⭐ 2. Your launcher scripts are now isolated and ready for repair

You did this perfectly:

`
mkdir ~/launcher_scripts
cp ~/osint-toolkit/SocialAnalyzerFlatBackup/SHELL* ~/launcher_scripts/
`

And the result:

`
~/launcher_scripts
├── SHELL_UserFinder.sh
├── SHELLautopip.sh
├── SHELLbackuphistory_daily.sh
├── SHELLcheckall_metadata.sh
├── SHELLcheckmetadata.sh
├── SHELLfullsocialanalyzerinstall_run.sh
├── SHELLrunsocialanalyzerlxml.sh
├── SHELLsaveandopenhistory.sh
├── SHELLsetuptermux.sh
└── SHELLsocialanalyzer_script.sh
`

This is exactly the set we need to fix.

---

⭐ 3. Here’s how we proceed — the clean, tactical way

I’m going to:

Step A — Audit each script
For each .sh file, I will check:

- missing shebang (#!/bin/bash)
- wrong paths (Termux paths differ from Linux)
- missing dependencies (pip, node, npm, python3)
- outdated commands
- syntax errors
- broken logic
- missing permissions
- missing environment variables
- missing modules
- incorrect working directory assumptions

Step B — Rewrite each script cleanly
I will produce:

- a corrected version  
- a Termux‑compatible version  
- a modular version  
- a version that integrates with your vault  
- a version that integrates with your FAFO launcher  
- a version that can call 403jump, GHunt, Maigret, Holehe, etc.

Step C — Build a unified launcher
Once all scripts are fixed, I will build:

`
launcher.sh
`

that orchestrates:

- OSINT scrapers  
- metadata checks  
- social media analyzers  
- phone checks  
- profile checks  
- 403jump fuzzing  
- GHunt  
- Maigret  
- Holehe  
- your vault  
- your context builder  

All in one clean, modular workflow.

---

⭐ 4. Your next step (simple)

Run this:

`
cd ~/launcher_scripts
ls -la
`

Then pick one script you want to fix first.

Examples:

- SHELL_UserFinder.sh
- SHELLsocialanalyzer_script.sh
- SHELLfullsocialanalyzerinstall_run.sh
- SHELLcheckall_metadata.sh

Just tell me:

“Start with X”

And I’ll begin the full forensic repair — line by line, with explanations, and a clean rewritten version.

You finally have your entire launcher back.  
Now we make it work again.

ERFECT_20251018_1913.zip
~/osint-toolkit $ ls -a                                     .     .github
..    Social_Analyzer_Flat_Backup
.git  Social_Analyzer_Flat_Backup_PERFECT_20251018_1913.zip
~/osint-toolkit $ cd Social_Analyzer_Flat_Backup
~/osint-toolkit/Social_Analyzer_Flat_Backup $ ls
00_PROJECT_OVERVIEW.txt
01_REQUIREMENTS.txt
02_QUICK_START_GUIDE.txt
03_COMPLETE_COMMANDS.txt
04_ANALYSIS_COMMANDS.txt
99_FILE_MANIFEST.txt
ANALYZER_analyzer_sociak_media.js
ANALYZER_facebook_osint.js
ANALYZER_social_analyzer_js.js
BACKUP_phone_check_ORIGINAL_085629.js
BACKUP_phone_check_ORIGINAL_090305.js
BACKUP_social_ORIGINAL_085311.js
BACKUP_social_ORIGINAL_085629.js
BACKUP_social_accurate_ORIGINAL_085311.js
BACKUP_social_accurate_ORIGINAL_085629.js
BACKUP_social_analyzer_js_ORIGINAL_085311.js
BACKUP_social_analyzer_js_ORIGINAL_085629.js
BACKUP_social_basic_ORIGINAL_085311.js
BACKUP_social_basic_ORIGINAL_085629.js
BACKUP_social_enhanced_ORIGINAL_085311.js
BACKUP_social_enhanced_ORIGINAL_085629.js
BACKUP_social_final_ORIGINAL_085311.js
BACKUP_social_final_ORIGINAL_085629.js
BACKUP_social_tool_ORIGINAL_090305.js
CONFIG_headers.txt
CONFIG_package.json
CONFIG_requirements.txt
CONFIG_sites_list.txt
CORE_social.js
CORE_social_accurate.js
CORE_social_analyzer_js.js
CORE_social_analyzer_js_backup.js
CORE_social_basic.js
CORE_social_enhanced.js
CORE_social_final.js
CORE_social_tool.js
PHONE_phone_check.js
PHONE_teen_check.js
PROFILE_FacebookSearcher.js
PROFILE_profile_pics.sh
PROFILE_profile_pictures.js
PROFILE_secret_profiles_check.js
PYTHON_analyze_social_media.py
PYTHON_analyze_social_media_nojpg.py
PYTHON_analyzer_social_media.py
PYTHON_analyzer_social_media_nojpg.py
PYTHON_quick_scraper.py
PYTHON_quick_script.py
SHELL_UserFinder.sh
SHELL_auto_pip.sh
SHELL_backup_history_daily.sh
SHELL_check_all_metadata.sh
SHELL_check_metadata.sh
SHELL_full_social_analyzer_install_run.sh
SHELL_run_social_analyzer_lxml.sh
SHELL_save_and_open_history.sh
SHELL_setup_termux.sh
SHELL_social_analyzer_script.sh
~/osint-toolkit/Social_Analyzer_Flat_Backup $ ls -a
.
..
00_PROJECT_OVERVIEW.txt
01_REQUIREMENTS.txt
02_QUICK_START_GUIDE.txt
03_COMPLETE_COMMANDS.txt
04_ANALYSIS_COMMANDS.txt
99_FILE_MANIFEST.txt
ANALYZER_analyzer_sociak_media.js
ANALYZER_facebook_osint.js
ANALYZER_social_analyzer_js.js
BACKUP_phone_check_ORIGINAL_085629.js
BACKUP_phone_check_ORIGINAL_090305.js
BACKUP_social_ORIGINAL_085311.js
BACKUP_social_ORIGINAL_085629.js
BACKUP_social_accurate_ORIGINAL_085311.js
BACKUP_social_accurate_ORIGINAL_085629.js
BACKUP_social_analyzer_js_ORIGINAL_085311.js
BACKUP_social_analyzer_js_ORIGINAL_085629.js
BACKUP_social_basic_ORIGINAL_085311.js
BACKUP_social_basic_ORIGINAL_085629.js
BACKUP_social_enhanced_ORIGINAL_085311.js
BACKUP_social_enhanced_ORIGINAL_085629.js
BACKUP_social_final_ORIGINAL_085311.js
BACKUP_social_final_ORIGINAL_085629.js
BACKUP_social_tool_ORIGINAL_090305.js
CONFIG_headers.txt
CONFIG_package.json
CONFIG_requirements.txt
CONFIG_sites_list.txt
CORE_social.js
CORE_social_accurate.js
CORE_social_analyzer_js.js
CORE_social_analyzer_js_backup.js
CORE_social_basic.js
CORE_social_enhanced.js
CORE_social_final.js
CORE_social_tool.js
PHONE_phone_check.js
PHONE_teen_check.js
PROFILE_FacebookSearcher.js
PROFILE_profile_pics.sh
PROFILE_profile_pictures.js
PROFILE_secret_profiles_check.js
PYTHON_analyze_social_media.py
PYTHON_analyze_social_media_nojpg.py
PYTHON_analyzer_social_media.py
PYTHON_analyzer_social_media_nojpg.py
PYTHON_quick_scraper.py
PYTHON_quick_script.py
SHELL_UserFinder.sh
SHELL_auto_pip.sh
SHELL_backup_history_daily.sh
SHELL_check_all_metadata.sh
SHELL_check_metadata.sh
SHELL_full_social_analyzer_install_run.sh
SHELL_run_social_analyzer_lxml.sh
SHELL_save_and_open_history.sh
SHELL_setup_termux.sh
SHELL_social_analyzer_script.sh
~/osint-toolkit/Social_Analyzer_Flat_Backup $ ls -la
total 316
drwxrwx---. 2 u0_a459 u0_a459  8192 Oct 18 19:12 .
drwx------. 5 u0_a459 u0_a459  3452 Dec 20 12:34 ..
-rw-rw----. 1 u0_a459 u0_a459   429 Oct 18 18:55 00_PROJECT_OVERVIEW.txt
-rw-rw----. 1 u0_a459 u0_a459   190 Oct 18 18:55 01_REQUIREMENTS.txt
-rw-rw----. 1 u0_a459 u0_a459   639 Oct 18 18:59 02_QUICK_START_GUIDE.txt
-rw-rw----. 1 u0_a459 u0_a459  7449 Oct 18 18:59 03_COMPLETE_COMMANDS.txt
-rw-rw----. 1 u0_a459 u0_a459  5762 Oct 18 18:59 04_ANALYSIS_COMMANDS.txt
-rw-rw----. 1 u0_a459 u0_a459  5136 Oct 18 19:09 99_FILE_MANIFEST.txt
-rw-rw----. 1 u0_a459 u0_a459  2507 Oct 18 18:56 ANALYZER_analyzer_sociak_media.js
-rw-rw----. 1 u0_a459 u0_a459 19908 Oct 18 18:56 ANALYZER_facebook_osint.js
-rw-rw----. 1 u0_a459 u0_a459  3291 Oct 18 18:56 ANALYZER_social_analyzer_js.js
-rw-rw----. 1 u0_a459 u0_a459  2599 Oct 18 18:58 BACKUP_phone_check_ORIGINAL_085629.js
-rw-rw----. 1 u0_a459 u0_a459  2599 Oct 18 18:58 BACKUP_phone_check_ORIGINAL_090305.js
-rw-rw----. 1 u0_a459 u0_a459  1862 Oct 18 18:58 BACKUP_social_ORIGINAL_085311.js
-rw-rw----. 1 u0_a459 u0_a459  1862 Oct 18 18:58 BACKUP_social_ORIGINAL_085629.js
-rw-rw----. 1 u0_a459 u0_a459  4971 Oct 18 18:59 BACKUP_social_accurate_ORIGINAL_085311.js
-rw-rw----. 1 u0_a459 u0_a459  4971 Oct 18 18:59 BACKUP_social_accurate_ORIGINAL_085629.js
-rw-rw----. 1 u0_a459 u0_a459  3291 Oct 18 18:59 BACKUP_social_analyzer_js_ORIGINAL_085311.js
-rw-rw----. 1 u0_a459 u0_a459  3291 Oct 18 18:59 BACKUP_social_analyzer_js_ORIGINAL_085629.js
-rw-rw----. 1 u0_a459 u0_a459  3436 Oct 18 18:59 BACKUP_social_basic_ORIGINAL_085311.js
-rw-rw----. 1 u0_a459 u0_a459  3436 Oct 18 18:59 BACKUP_social_basic_ORIGINAL_085629.js
-rw-rw----. 1 u0_a459 u0_a459  2912 Oct 18 18:59 BACKUP_social_enhanced_ORIGINAL_085311.js
-rw-rw----. 1 u0_a459 u0_a459  2912 Oct 18 18:59 BACKUP_social_enhanced_ORIGINAL_085629.js
-rw-rw----. 1 u0_a459 u0_a459  2559 Oct 18 18:59 BACKUP_social_final_ORIGINAL_085311.js
-rw-rw----. 1 u0_a459 u0_a459  2559 Oct 18 18:59 BACKUP_social_final_ORIGINAL_085629.js
-rw-rw----. 1 u0_a459 u0_a459  2336 Oct 18 18:59 BACKUP_social_tool_ORIGINAL_090305.js
-rw-rw----. 1 u0_a459 u0_a459  2095 Oct 18 18:59 CONFIG_headers.txt
-rw-rw----. 1 u0_a459 u0_a459    85 Oct 18 18:59 CONFIG_package.json
-rw-rw----. 1 u0_a459 u0_a459    63 Oct 18 18:59 CONFIG_requirements.txt
-rw-rw----. 1 u0_a459 u0_a459  4322 Oct 18 18:59 CONFIG_sites_list.txt
-rw-rw----. 1 u0_a459 u0_a459  2252 Oct 18 18:55 CORE_social.js
-rw-rw----. 1 u0_a459 u0_a459  7404 Oct 18 18:55 CORE_social_accurate.js
-rw-rw----. 1 u0_a459 u0_a459  3291 Oct 18 18:41 CORE_social_analyzer_js.js
-rw-rw----. 1 u0_a459 u0_a459   828 Oct 18 18:41 CORE_social_analyzer_js_backup.js
-rw-rw----. 1 u0_a459 u0_a459  4264 Oct 18 18:55 CORE_social_basic.js
-rw-rw----. 1 u0_a459 u0_a459  3585 Oct 18 18:55 CORE_social_enhanced.js
-rw-rw----. 1 u0_a459 u0_a459  3059 Oct 18 18:55 CORE_social_final.js
-rw-rw----. 1 u0_a459 u0_a459  3164 Oct 18 18:55 CORE_social_tool.js
-rw-rw----. 1 u0_a459 u0_a459  2599 Oct 18 18:56 PHONE_phone_check.js
-rw-rw----. 1 u0_a459 u0_a459  6120 Oct 18 18:56 PHONE_teen_check.js
-rw-rw----. 1 u0_a459 u0_a459  8867 Oct 18 18:56 PROFILE_FacebookSearcher.js
-rw-rw----. 1 u0_a459 u0_a459    75 Oct 18 18:56 PROFILE_profile_pics.sh
-rw-rw----. 1 u0_a459 u0_a459  1422 Oct 18 18:56 PROFILE_profile_pictures.js
-rw-rw----. 1 u0_a459 u0_a459  9799 Oct 18 18:56 PROFILE_secret_profiles_check.js
-rw-rw----. 1 u0_a459 u0_a459  4568 Oct 18 18:58 PYTHON_analyze_social_media.py
-rw-rw----. 1 u0_a459 u0_a459  2133 Oct 18 18:58 PYTHON_analyze_social_media_nojpg.py
-rw-rw----. 1 u0_a459 u0_a459  2507 Oct 18 18:58 PYTHON_analyzer_social_media.py
-rw-rw----. 1 u0_a459 u0_a459  2090 Oct 18 18:58 PYTHON_analyzer_social_media_nojpg.py
-rw-rw----. 1 u0_a459 u0_a459  1652 Oct 18 18:58 PYTHON_quick_scraper.py
-rw-rw----. 1 u0_a459 u0_a459  1749 Oct 18 18:58 PYTHON_quick_script.py
-rw-rw----. 1 u0_a459 u0_a459   147 Oct 18 19:05 SHELL_UserFinder.sh
-rw-rw----. 1 u0_a459 u0_a459  1472 Oct 18 18:57 SHELL_auto_pip.sh
-rw-rw----. 1 u0_a459 u0_a459   477 Oct 18 18:58 SHELL_backup_history_daily.sh
-rw-rw----. 1 u0_a459 u0_a459  1236 Oct 18 18:57 SHELL_check_all_metadata.sh
-rw-rw----. 1 u0_a459 u0_a459   542 Oct 18 18:57 SHELL_check_metadata.sh
-rw-rw----. 1 u0_a459 u0_a459  1395 Oct 18 18:57 SHELL_full_social_analyzer_install_run.sh
-rw-rw----. 1 u0_a459 u0_a459   111 Oct 18 19:05 SHELL_run_social_analyzer_lxml.sh
-rw-rw----. 1 u0_a459 u0_a459   289 Oct 18 18:58 SHELL_save_and_open_history.sh
-rw-rw----. 1 u0_a459 u0_a459   278 Oct 18 18:57 SHELL_setup_termux.sh
-rw-rw----. 1 u0_a459 u0_a459   704 Oct 18 18:57 SHELL_social_analyzer_script.sh
~/osint-toolkit/Social_Analyzer_Flat_Backup $ mkdir ~/launcher_scripts
cp ~/osint-toolkit/Social_Analyzer_Flat_Backup/SHELL_* ~/launcher_scripts/
~/osint-toolkit/Social_Analyzer_Flat_Backup $ ls -la ~/launcher_scripts
total 68
drwx------.  2 u0_a459 u0_a459     3452 Dec 20 12:41 .
drwx------. 18 u0_a459 u0_a459 16756736 Dec 20 12:41 ..
-rw-------.  1 u0_a459 u0_a459      147 Dec 20 12:41 SHELL_UserFinder.sh
-rw-------.  1 u0_a459 u0_a459     1472 Dec 20 12:41 SHELL_auto_pip.sh
-rw-------.  1 u0_a459 u0_a459      477 Dec 20 12:41 SHELL_backup_history_daily.sh
-rw-------.  1 u0_a459 u0_a459     1236 Dec 20 12:41 SHELL_check_all_metadata.sh
-rw-------.  1 u0_a459 u0_a459      542 Dec 20 12:41 SHELL_check_metadata.sh
-rw-------.  1 u0_a459 u0_a459     1395 Dec 20 12:41 SHELL_full_social_analyzer_install_run.sh
-rw-------.  1 u0_a459 u0_a459      111 Dec 20 12:41 SHELL_run_social_analyzer_lxml.sh
-rw-------.  1 u0_a459 u0_a459      289 Dec 20 12:41 SHELL_save_and_open_history.sh
-rw-------.  1 u0_a459 u0_a459      278 Dec 20 12:41 SHELL_setup_termux.sh
-rw-------.  1 u0_a459 u0_a459      704 Dec 20 12:41 SHELL_social_analyzer_script.sh
~/osint-toolkit/Social_Analyzer_Flat_Backup $ ls -la ~/launcher_scripts
total 68
drwx------.  2 u0_a459 u0_a459     3452 Dec 20 12:41 .
drwx------. 18 u0_a459 u0_a459 16756736 Dec 20 12:41 ..
-rw-------.  1 u0_a459 u0_a459      147 Dec 20 12:41 SHELL_UserFinder.sh
-rw-------.  1 u0_a459 u0_a459     1472 Dec 20 12:41 SHELL_auto_pip.sh
-rw-------.  1 u0_a459 u0_a459      477 Dec 20 12:41 SHELL_backup_history_daily.sh
-rw-------.  1 u0_a459 u0_a459     1236 Dec 20 12:41 SHELL_check_all_metadata.sh
-rw-------.  1 u0_a459 u0_a459      542 Dec 20 12:41 SHELL_check_metadata.sh
-rw-------.  1 u0_a459 u0_a459     1395 Dec 20 12:41 SHELL_full_social_analyzer_install_run.sh
-rw-------.  1 u0_a459 u0_a459      111 Dec 20 12:41 SHELL_run_social_analyzer_lxml.sh
-rw-------.  1 u0_a459 u0_a459      289 Dec 20 12:41 SHELL_save_and_open_history.sh
-rw-------.  1 u0_a459 u0_a459      278 Dec 20 12:41 SHELL_setup_termux.sh
-rw-------.  1 u0_a459 u0_a459      704 Dec 20 12:41 SHELL_social_analyzer_script.sh
~/osint-toolkit/Social_Analyzer_Flat_Backup $ cd ~/launcher_scripts
ls -la
total 68
drwx------.  2 u0_a459 u0_a459     3452 Dec 20 12:41 .
drwx------. 18 u0_a459 u0_a459 16756736 Dec 20 12:41 ..
-rw-------.  1 u0_a459 u0_a459      147 Dec 20 12:41 SHELL_UserFinder.sh
-rw-------.  1 u0_a459 u0_a459     1472 Dec 20 12:41 SHELL_auto_pip.sh
-rw-------.  1 u0_a459 u0_a459      477 Dec 20 12:41 SHELL_backup_history_daily.sh
-rw-------.  1 u0_a459 u0_a459     1236 Dec 20 12:41 SHELL_check_all_metadata.sh
-rw-------.  1 u0_a459 u0_a459      542 Dec 20 12:41 SHELL_check_metadata.sh
-rw-------.  1 u0_a459 u0_a459     1395 Dec 20 12:41 SHELL_full_social_analyzer_install_run.sh
-rw-------.  1 u0_a459 u0_a459      111 Dec 20 12:41 SHELL_run_social_analyzer_lxml.sh
-rw-------.  1 u0_a459 u0_a459      289 Dec 20 12:41 SHELL_save_and_open_history.sh
-rw-------.  1 u0_a459 u0_a459      278 Dec 20 12:41 SHELL_setup_termux.sh
-rw-------.  1 u0_a459 u0_a459      704 Dec 20 12:41 SHELL_social_analyzer_script.sh
~/launcher_scripts $
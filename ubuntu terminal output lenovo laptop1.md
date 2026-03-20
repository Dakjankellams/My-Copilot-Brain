Welcome to Ubuntu 24.04.3 LTS (GNU/Linux 6.6.87.2-microsoft-standard-WSL2 x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Fri Nov 21 22:38:37 PST 2025

  System load:  5.49                Processes:             50
  Usage of /:   1.8% of 1006.85GB   Users logged in:       0
  Memory usage: 14%                 IPv4 address for eth0: 172.22.142.92
  Swap usage:   0%

  => There is 1 zombie process.


This message is shown once a day. To disable it please create the
/home/jv/.hushlogin file.
jv@Kellams-PC:~$ ls
'6) Run Tool Check'                     ghunt_creds.json              secure-backups
 CommandCenter                          ghunt_csv_export.py           snap
 CopilotSync                            ghunt_env                     social-analyzer
 FAFO_LAUNCHER.sh                       ghunt_launcher.sh             social_mapper
 OSINT_Tools                            ghunt_logs                    social_mapper_venv
 Rebuilt                                ghunt_targets.txt             stego_batch_gen.sh
 __pycache__                            go                            stego_indexer.sh
 all_files.txt                          guardian                      stego_ops.sh
 auto_restore_ghunt.sh                  index_js_tools_local.sh       stego_scan
 backups                                inject_aliases.sh             stego_scanner.sh
 batch-secret-log-20251016_081933.txt   inject_modular.sh             target.cfg
 cd                                     inject_sync.sh                toolcheck.py
 chart_builder.py                       inject_tagged.sh              toolcheck.py4
 check_requirements.py                  insert_block.sh               ubuntu_backup.tar.gz
 checkenv:                              launch_osint_tools.sh         usernames.txt
 command_center.sh                      launcher                      validate_all_tools.sh
 context_sync_agent.py                  launcher.sh                   validate_all_tools_upgraded.sh
 copilot_catchup                        launcher_archive              validate_launcher_menu.sh
 copilot_dirhunt.txt                    launcher_backup               validate_signature.sh
 copilot_fastscan.txt                   launcher_snapshot.sh          venv
 copilot_file_list.txt                  launchers.sh                  venv_scan_report.txt
 copilot_full_list.txt                  logs                         '~'
 copilot_launcher.sh                    logviewer.py                 '~audit_bin_logger.sh'
 copilot_launcher.shn                   metadata_to_csv.py           '~command_center.sh'
 copilot_real_list.txt                  nano                         '~dashboard_diff.py'
 copilot_restore                        osint_tools                  '~dashboard_html.py'
 copilot_sync.log                       pandoc_3.1.3+ds-2_amd64.deb  '~generate_hashes.sh'
 copilot_sync.sh                        profile_reports              '~guardian'
 copilot_sync_summary.txt               python3                      '~hash_check.sh'
 cp                                     report_to_excel.py           '~integrigy_sweep.sh'
 dashboard                              restore_config.sh            '~known_good_hashes.txt'
 dashboard_indexer.sh                   restore_ghunt                '~launchers.sh'
 dashboard_server.py                    restore_ghunt_launcher.sh    '~profile_sweep.py'
 dashboard_summary.py                   restore_ghunt_suite.sh       '~restore_ghunt_suite.sh'
 debug_stego.py                         restore_test                 '~stego_batch_gen.sh'
 delete                                 restore_tracker.log          '~stego_cleaner.sh'
 echo                                   restore_tracker.sh           '~stego_hahmap.sh'
 encoded_creds.txt                      run-launcher.sh              '~stego_indexer.sh'
 extract_executables.py                 run_all_tools.sh             '~stego_report.md'
 extract_from_paste.py                  run_ghunt.sh                 '~stego_restore.sh'
 extract_from_paste_validated.py        runlog.txt                   '~stego_sync.sh'
 extract_payload.py                     scan_all_venvs.sh            '~stego_verify_all.sh'
 fafo.sh                                scrape_batch.py              '~validate_restore.sh'
 filtered_files.txt                     scrapers                      —
 generate_stego.py                      scripts
jv@Kellams-PC:~$ ls -la
total 9449576
drwxr-x--- 48 jv   jv        12288 Nov  8 22:05  .
drwxr-xr-x  3 root root       4096 Sep 29 09:03  ..
-rw-------  1 jv   jv            0 Sep 29 21:10  .Xauthority
-rw-------  1 jv   jv        21448 Oct 25 23:09  .bash_history
-rw-r--r--  1 jv   jv          220 Sep 29 09:03  .bash_logout
-rw-r--r--  1 jv   jv          575 Oct 25 21:10  .bashrc
-rw-r--r--  1 jv   jv            0 Oct 11 23:49  .bashrc.bak
-rw-r--r--  1 jv   jv          665 Oct 12 08:15  .bashrc.bak_1760282145
-rw-r--r--  1 jv   jv         1227 Oct 12 08:18  .bashrc.bak_1760282328
drwxr-xr-x  4 jv   jv         4096 Oct 13 21:57  .bluefish
drwx------  7 jv   jv         4096 Oct 10 14:24  .cache
drwx------  3 jv   jv         4096 Oct 13 22:04  .config
drwxr-xr-x  3 jv   jv         4096 Oct 12 11:27  .config_repo
drwxr-xr-x  2 jv   jv         4096 Oct 10 18:42  .ghunt
drwxr-xr-x  2 jv   jv         4096 Oct 10 18:41  .ghuntmkdir
drwxr-xr-x 11 jv   jv         4096 Oct 25 19:49  .git
-rw-r--r--  1 jv   jv          108 Oct 19 10:23  .gitignore
drwx------  3 jv   jv         4096 Oct 19 06:43  .gnupg
drwxr-xr-x  2 jv   jv         4096 Oct  8 11:33  .landscape
-rw-------  1 jv   jv           20 Oct 17 23:52  .lesshst
drwxr-xr-x  6 jv   jv         4096 Oct 10 14:24  .local
drwxr-xr-x  3 jv   jv         4096 Oct 10 14:28  .malfrats
-rw-rw-r--  1 jv   jv            0 Nov 21 19:37  .motd_shown
-rw-------  1 jv   jv           18 Oct 11 20:59  .node_repl_history
drwxr-xr-x  4 jv   jv         4096 Sep 29 12:05  .npm
-rw-r--r--  1 jv   jv         2221 Oct 16 11:25  .profile
-rw-r--r--  1 jv   jv          807 Oct 12 08:17  .profile.bak_1760282267
-rw-r--r--  1 jv   jv          976 Oct 12 08:18  .profile.bak_1760282328
-rw-r--r--  1 jv   jv         1317 Oct 12 08:18  .profile.bak_1760282329
-rw-------  1 jv   jv           10 Oct 17 21:09  .python_history
-rw-r--r--  1 jv   jv           66 Oct 10 19:19  .selected_editor
-rw-r--r--  1 jv   jv            0 Sep 29 09:04  .sudo_as_admin_successful
drwxr-xr-x  2 jv   jv         4096 Oct 12 08:52  .termux
drwxr-xr-x  5 jv   jv         4096 Oct 13 01:06  .venv
-rw-r--r--  1 jv   jv          161 Oct 12 09:14  .wget-hsts
-rw-r--r--  1 jv   jv         1933 Oct 11 23:49  .wsl-config
-rwxr-xr-x  1 jv   jv         1464 Oct 12 16:04  .zshrc
-rw-r--r--  1 jv   jv            0 Oct 15 23:23  .zshrc#
-rw-r--r--  1 jv   jv            6 Oct 12 08:17  .zshrc.bak_1760282267
-rw-r--r--  1 jv   jv          516 Oct 12 08:18  .zshrc.bak_1760282328
-rw-r--r--  1 jv   jv         1571 Oct 16 11:25  .zshrcnano
-rw-r--r--  1 jv   jv          924 Oct 12 13:45 '6) Run Tool Check'
drwxr-xr-x 13 jv   jv         4096 Oct 17 20:09  CommandCenter
drwxr-xr-x  3 jv   jv         4096 Oct 16 03:37  CopilotSync
-rw-r--r--  1 jv   jv         1917 Nov  8 22:00  FAFO_LAUNCHER.sh
drwxr-xr-x  2 jv   jv         4096 Oct 10 08:37  OSINT_Tools
-rw-r--r--  1 jv   jv           46 Oct 16 11:25  Rebuilt
drwxr-xr-x  2 jv   jv         4096 Oct 25 17:48  __pycache__
-rw-r--r--  1 jv   jv            0 Oct 17 22:05  all_files.txt
-rwxr-xr-x  1 jv   jv         1131 Oct 12 22:50  auto_restore_ghunt.sh
drwxr-xr-x  2 jv   jv         4096 Oct 12 08:59  backups
-rw-r--r--  1 jv   jv           53 Oct 16 08:19  batch-secret-log-20251016_081933.txt
-rw-r--r--  1 jv   jv            0 Oct 10 09:25  cd
-rw-r--r--  1 jv   jv          748 Oct 11 21:50  chart_builder.py
-rwxr-xr-x  1 jv   jv          582 Oct 12 10:24  check_requirements.py
-rw-r--r--  1 jv   jv           50 Oct 12 10:24  checkenv:
-rwxr-xr-x  1 jv   jv         1647 Oct 12 13:59  command_center.sh
-rwxr-xr-x  1 jv   jv         1019 Oct 17 21:45  context_sync_agent.py
drwxr-xr-x 17 jv   jv        49152 Oct 26 01:19  copilot_catchup
-rw-r--r--  1 jv   jv            0 Oct 17 22:25  copilot_dirhunt.txt
-rw-r--r--  1 jv   jv          122 Oct 17 22:27  copilot_fastscan.txt
-rw-r--r--  1 jv   jv       691675 Oct 17 23:31  copilot_file_list.txt
-rw-r--r--  1 jv   jv            0 Oct 17 22:10  copilot_full_list.txt
-rwxr-xr-x  1 jv   jv          852 Oct 18 00:00  copilot_launcher.sh
-rw-r--r--  1 jv   jv            0 Oct 17 22:45  copilot_launcher.shn
-rw-r--r--  1 jv   jv       258774 Oct 17 22:35  copilot_real_list.txt
drwxr-xr-x  6 jv   jv         4096 Nov  8 22:00  copilot_restore
-rw-r--r--  1 jv   jv         1723 Oct 16 11:06  copilot_sync.log
-rwxr-xr-x  1 jv   jv          725 Oct 16 11:06  copilot_sync.sh
-rw-r--r--  1 jv   jv          324 Oct 16 11:06  copilot_sync_summary.txt
-rw-r--r--  1 jv   jv            0 Oct 10 09:25  cp
drwxr-xr-x  2 jv   jv         4096 Oct 12 08:25  dashboard
-rwxr-xr-x  1 jv   jv          879 Oct 12 08:52  dashboard_indexer.sh
-rw-r--r--  1 jv   jv          913 Oct 11 22:20  dashboard_server.py
-rwxr-xr-x  1 jv   jv         1024 Oct 12 13:12  dashboard_summary.py
-rwxr-xr-x  1 jv   jv          732 Oct 12 10:20  debug_stego.py
drwxr-xr-x  2 jv   jv         4096 Oct 13 20:12  delete
-rw-r--r--  1 jv   jv            0 Oct 10 09:25  echo
-rw-r--r--  1 jv   jv          868 Oct 10 16:14  encoded_creds.txt
-rwxr-xr-x  1 jv   jv         1093 Oct 25 15:42  extract_executables.py
-rwxr-xr-x  1 jv   jv         1154 Oct 25 16:03  extract_from_paste.py
-rwxr-xr-x  1 jv   jv         1574 Oct 25 19:12  extract_from_paste_validated.py
-rwxr-xr-x  1 jv   jv          507 Oct 12 09:45  extract_payload.py
-rwxr-xr-x  1 jv   jv         1392 Oct 18 00:33  fafo.sh
-rw-r--r--  1 jv   jv            0 Oct 17 22:05  filtered_files.txt
-rwxr-xr-x  1 jv   jv          670 Oct 12 10:17  generate_stego.py
-rwxr-xr-x  1 jv   jv          640 Oct 10 16:14  ghunt_creds.json
-rw-r--r--  1 jv   jv          940 Oct 11 22:35  ghunt_csv_export.py
drwxr-xr-x  6 jv   jv         4096 Oct 12 09:37  ghunt_env
-rw-r--r--  1 jv   jv         4169 Oct 11 22:14  ghunt_launcher.sh
drwxr-xr-x  2 jv   jv         4096 Oct 12 21:21  ghunt_logs
-rw-r--r--  1 jv   jv           70 Oct 10 19:15  ghunt_targets.txt
drwxr-xr-x  3 jv   jv         4096 Oct 10 05:23  go
drwxr-xr-x  2 jv   jv         4096 Oct 13 20:34  guardian
-rwxr-xr-x  1 jv   jv         1003 Oct 25 20:08  index_js_tools_local.sh
-rwxr-xr-x  1 jv   jv         1330 Oct 12 08:17  inject_aliases.sh
-rwxr-xr-x  1 jv   jv         2038 Oct 12 08:18  inject_modular.sh
-rwxr-xr-x  1 jv   jv         1525 Oct 12 11:27  inject_sync.sh
-rwxr-xr-x  1 jv   jv         1201 Oct 12 08:44  inject_tagged.sh
-rwxr-xr-x  1 jv   jv         1054 Oct 12 08:15  insert_block.sh
-rwxr-xr-x  1 jv   jv         1202 Oct 13 00:10  launch_osint_tools.sh
drwxr-xr-x  3 jv   jv         4096 Oct 16 07:31  launcher
-rwxr-xr-x  1 jv   jv         2985 Oct 16 08:08  launcher.sh
drwxr-xr-x  4 jv   jv         4096 Oct 17 21:36  launcher_archive
drwxr-xr-x  3 jv   jv         4096 Oct 25 19:48  launcher_backup
-rwxr-xr-x  1 jv   jv          636 Oct 12 16:59  launcher_snapshot.sh
-rwxr-xr-x  1 jv   jv         2509 Oct 12 13:26  launchers.sh
drwxr-xr-x  2 jv   jv         4096 Oct 12 09:21  logs
-rwxr-xr-x  1 jv   jv          506 Oct 12 13:45  logviewer.py
-rwxr-xr-x  1 jv   jv          915 Oct 12 11:13  metadata_to_csv.py
-rw-r--r--  1 jv   jv            2 Oct 12 21:07  nano
drwxr-xr-x  8 jv   jv         4096 Oct 10 05:11  osint_tools
-rw-r--r--  1 jv   jv          260 Oct 12 21:37  pandoc_3.1.3+ds-2_amd64.deb
drwxr-xr-x  2 jv   jv         4096 Oct 12 13:02  profile_reports
-rw-r--r--  1 jv   jv           22 Oct 10 09:25  python3
-rwxr-xr-x  1 jv   jv         1010 Oct 12 11:15  report_to_excel.py
-rwxr-xr-x  1 jv   jv         1104 Oct 12 08:25  restore_config.sh
drwxr-xr-x  2 jv   jv         4096 Oct 12 22:44  restore_ghunt
-rwxr-xr-x  1 jv   jv         1708 Oct 12 22:46  restore_ghunt_launcher.sh
-rwxr-xr-x  1 jv   jv            2 Oct 16 00:00  restore_ghunt_suite.sh
drwxr-xr-x  3 jv   jv         4096 Oct 12 09:06  restore_test
-rw-r--r--  1 jv   jv          920 Oct 13 00:18  restore_tracker.log
-rwxr-xr-x  1 jv   jv         1271 Oct 12 08:58  restore_tracker.sh
-rwxr-xr-x  1 jv   jv           83 Oct 16 07:47  run-launcher.sh
-rw-r--r--  1 jv   jv          836 Oct 12 01:51  run_all_tools.sh
-rwxr-xr-x  1 jv   jv         1121 Oct 12 01:52  run_ghunt.sh
-rw-r--r--  1 jv   jv         1428 Oct 17 21:36  runlog.txt
-rwxr-xr-x  1 jv   jv         2292 Oct 25 17:12  scan_all_venvs.sh
-rwxr-xr-x  1 jv   jv         1389 Oct 12 11:16  scrape_batch.py
drwxr-xr-x  3 jv   jv         4096 Oct 25 16:47  scrapers
drwxr-xr-x  3 jv   jv         4096 Oct 12 08:46  scripts
drwxr-xr-x  2 jv   jv         4096 Oct 19 10:19  secure-backups
drwx------  3 jv   jv         4096 Oct 13 00:02  snap
drwxr-xr-x 12 jv   jv         4096 Oct 25 17:48  social-analyzer
drwxr-xr-x  8 jv   jv         4096 Oct 25 17:48  social_mapper
drwxr-xr-x  5 jv   jv         4096 Sep 29 09:55  social_mapper_venv
-rwxr-xr-x  1 jv   jv          594 Oct 12 10:36  stego_batch_gen.sh
-rwxr-xr-x  1 jv   jv          325 Oct 12 10:36  stego_indexer.sh
-rwxr-xr-x  1 jv   jv          969 Oct 12 10:30  stego_ops.sh
drwxr-xr-x  2 jv   jv         4096 Oct 12 10:48  stego_scan
-rwxr-xr-x  1 jv   jv          999 Oct 12 09:18  stego_scanner.sh
-rw-r--r--  1 jv   jv           26 Oct 17 21:36  target.cfg
-rwxr-xr-x  1 jv   jv          924 Oct 12 13:42  toolcheck.py
-rw-r--r--  1 jv   jv          924 Oct 12 13:42  toolcheck.py4
-rw-r--r--  1 root root 9674601740 Oct 12 07:08  ubuntu_backup.tar.gz
-rwxr-xr-x  1 jv   jv          294 Oct 12 11:20  usernames.txt
-rwxr-xr-x  1 jv   jv          749 Oct 25 16:39  validate_all_tools.sh
-rwxr-xr-x  1 jv   jv         1375 Oct 25 16:56  validate_all_tools_upgraded.sh
-rwxr-xr-x  1 jv   jv         1307 Oct 18 00:42  validate_launcher_menu.sh
-rw-r--r--  1 jv   jv          602 Oct 12 10:28  validate_signature.sh
drwxr-xr-x  5 jv   jv         4096 Oct 12 22:03  venv
-rw-r--r--  1 jv   jv        63883 Oct 25 17:20  venv_scan_report.txt
drwxr-xr-x  3 jv   jv         4096 Oct 12 09:02 '~'
-rwxr-xr-x  1 jv   jv          689 Oct 13 20:01 '~audit_bin_logger.sh'
-rwxr-xr-x  1 jv   jv          121 Oct 12 13:59 '~command_center.sh'
-rwxr-xr-x  1 jv   jv          840 Oct 12 13:15 '~dashboard_diff.py'
-rwxr-xr-x  1 jv   jv         1184 Oct 12 13:14 '~dashboard_html.py'
-rw-r--r--  1 jv   jv          505 Oct 13 20:31 '~generate_hashes.sh'
drwxr-xr-x  2 jv   jv         4096 Oct 13 20:15 '~guardian'
-rw-r--r--  1 jv   jv          650 Oct 13 20:04 '~hash_check.sh'
-rw-r--r--  1 jv   jv          412 Oct 13 20:05 '~integrigy_sweep.sh'
-rw-r--r--  1 jv   jv           69 Oct 13 20:10 '~known_good_hashes.txt'
-rwxr-xr-x  1 jv   jv         1120 Oct 12 13:16 '~launchers.sh'
-rw-r--r--  1 jv   jv          822 Oct 12 14:34 '~profile_sweep.py'
-rwxr-xr-x  1 jv   jv         1347 Oct 13 00:14 '~restore_ghunt_suite.sh'
-rwxr-xr-x  1 jv   jv          594 Oct 12 10:38 '~stego_batch_gen.sh'
-rwxr-xr-x  1 jv   jv          193 Oct 12 10:39 '~stego_cleaner.sh'
-rwxr-xr-x  1 jv   jv          719 Oct 12 10:49 '~stego_hahmap.sh'
-rwxr-xr-x  1 jv   jv          325 Oct 12 10:39 '~stego_indexer.sh'
-rwxr-xr-x  1 jv   jv          407 Oct 12 10:40 '~stego_report.md'
-rwxr-xr-x  1 jv   jv          432 Oct 12 10:53 '~stego_restore.sh'
-rwxr-xr-x  1 jv   jv          808 Oct 12 10:51 '~stego_sync.sh'
-rwxr-xr-x  1 jv   jv         5396 Oct 13 00:03 '~stego_verify_all.sh'
-rwxr-xr-x  1 jv   jv          600 Oct 13 20:02 '~validate_restore.sh'
-rw-r--r--  1 jv   jv           18 Oct 12 22:13  —
jv@Kellams-PC:
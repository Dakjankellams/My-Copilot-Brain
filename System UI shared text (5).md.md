nano 


audit.sh

#!/usr/bin/env bash

set -euo pipefail

timestamp="$(date +%Y%m%d-%H%M%S)"
out_dir="system-audit-${timestamp}"
mkdir -p "${out_dir}"

echo "Creating system audit in: ${out_dir}"

# ------- Helper to run commands safely -------
run_cmd() {
    local cmd="$1"
    local outfile="$2"
    echo "== ${cmd} ==" >> "${outfile}"
    if command -v bash >/dev/null 2>&1; then
        bash -lc "${cmd}" >> "${outfile}" 2>&1 || echo "[WARN] Command failed: ${cmd}" >> "${outfile}"
    else
        sh -c "${cmd}" >> "${outfile}" 2>&1 || echo "[WARN] Command failed: ${cmd}" >> "${outfile}"
    fi
    echo "" >> "${outfile}"
}

# ------- 1. System info -------
sys_file="${out_dir}/system-info.txt"

{
    echo "==== SYSTEM INFO ===="
    echo "Timestamp: ${timestamp}"
    echo ""
} >> "${sys_file}"

[ -f /etc/os-release ] && cat /etc/os-release >> "${sys_file}" || echo "[INFO] /etc/os-release not found" >> "${sys_file}"
echo "" >> "${sys_file}"

run_cmd "uname -a" "${sys_file}"
command -v hostnamectl >/dev/null 2>&1 && run_cmd "hostnamectl" "${sys_file}"

# Detect Termux
is_termux="no"
if [ -n "${PREFIX-}" ] && echo "${PREFIX}" | grep -qi "com.termux"; then
    is_termux="yes"
elif [ -d "/data/data/com.termux/files/usr" ]; then
    is_termux="yes"
fi

echo "Detected Termux: ${is_termux}" >> "${sys_file}"
echo "" >> "${sys_file}"

# ------- 2. Installed packages -------
pkg_file="${out_dir}/packages.txt"

{
    echo "==== INSTALLED PACKAGES ===="
    echo "Timestamp: ${timestamp}"
    echo ""
} >> "${pkg_file}"

if [ "${is_termux}" = "yes" ]; then
    echo "[INFO] Using Termux package listing (pkg list-installed)" >> "${pkg_file}"
    run_cmd "pkg list-installed" "${pkg_file}"
else
    if command -v apt >/dev/null 2>&1 || command -v dpkg-query >/dev/null 2>&1; then
        echo "[INFO] Using dpkg-query for apt-based system" >> "${pkg_file}"
        run_cmd "dpkg-query -W -f='\${Package}\t\${Version}\n'" "${pkg_file}"
    elif command -v pacman >/dev/null 2>&1; then
        echo "[INFO] Using pacman -Q for Arch-based system" >> "${pkg_file}"
        run_cmd "pacman -Q" "${pkg_file}"
    else
        echo "[WARN] No known package manager detected (apt/dpkg/pacman). Skipping package list." >> "${pkg_file}"
    fi
fi

# ------- 3. Python info -------
py_file="${out_dir}/python.txt"

{
    echo "==== PYTHON INFO ===="
    echo "Timestamp: ${timestamp}"
    echo ""
} >> "${py_file}"

if command -v python3 >/dev/null 2>&1; then
    run_cmd "python3 --version" "${py_file}"
elif command -v python >/dev/null 2>&1; then
    run_cmd "python --version" "${py_file}"
else
    echo "[WARN] No python interpreter found in PATH" >> "${py_file}"
fi

if command -v pip3 >/dev/null 2>&1; then
    echo "[INFO] pip3 list --format=freeze" >> "${py_file}"
    run_cmd "pip3 list --format=freeze" "${py_file}"
elif command -v pip >/dev/null 2>&1; then
    echo "[INFO] pip list --format=freeze" >> "${py_file}"
    run_cmd "pip list --format=freeze" "${py_file}"
else
    echo "[WARN] No pip/pip3 found in PATH" >> "${py_file}"
fi

# ------- 4. Node.js info -------
node_file="${out_dir}/node.txt"

{
    echo "==== NODE.JS INFO ===="
    echo "Timestamp: ${timestamp}"
    echo ""
} >> "${node_file}"

if command -v node >/dev/null 2>&1; then
    run_cmd "node -v" "${node_file}"
else
    echo "[INFO] node not found in PATH" >> "${node_file}"
fi

if command -v npm >/dev/null 2>&1; then
    echo "[INFO] npm ls -g --depth=0" >> "${node_file}"
    run_cmd "npm ls -g --depth=0" "${node_file}"
else
    echo "[INFO] npm not found in PATH" >> "${node_file}"
fi

# ------- 5. Environment variables -------
env_file="${out_dir}/env-vars.txt"

{
    echo "==== ENVIRONMENT VARIABLES ===="
    echo "Timestamp: ${timestamp}"
    echo ""
} >> "${env_file}"

run_cmd "printenv" "${env_file}"

# ------- 6. PATH contents -------
path_file="${out_dir}/path.txt"

{
    echo "==== PATH CONTENTS ===="
    echo "Timestamp: ${timestamp}"
    echo ""
    echo "PATH=${PATH}"
    echo ""
    echo "Entries (one per line):"
} >> "${path_file}"

echo "${PATH}" | tr ':' '\n' >> "${path_file}"

# ------- 7. Summary -------
summary_file="${out_dir}/summary.txt"

{
    echo "==== SUMMARY ===="
    echo "Timestamp: ${timestamp}"
    echo ""
    echo "Output directory: ${out_dir}"
    echo "Files generated:"
    echo "  - system-info.txt"
    echo "  - packages.txt"
    echo "  - python.txt"
    echo "  - node.txt"
    echo "  - env-vars.txt"
    echo "  - path.txt"
    echo ""
    echo "This script is READ-ONLY: it does not install, remove, or modify packages."
} >> "${summary_file}"

echo "Audit complete. See directory: ${out_dir}"


chmod +x audit.sh
./audit.sh

powershell

notepad .\audit.ps1

$ErrorActionPreference = "SilentlyContinue"

$timestamp = Get-Date -Format "yyyyMMdd-HHmmss"
$outDir = "system-audit-$timestamp"

New-Item -ItemType Directory -Path $outDir -Force | Out-Null

Write-Host "Creating system audit in: $outDir"

# ------- Helper to run commands safely -------
function Run-Cmd {
    param(
        [string]$Command,
        [string]$OutFile
    )
    Add-Content -Path $OutFile -Value "== $Command =="
    try {
        Invoke-Expression $Command | Out-File -FilePath $OutFile -Append -Encoding UTF8
    } catch {
        Add-Content -Path $OutFile -Value "[WARN] Command failed: $Command"
    }
    Add-Content -Path $OutFile -Value ""
}

# ------- 1. System info -------
$sysFile = Join-Path $outDir "system-info.txt"

"==== SYSTEM INFO ====" | Out-File -FilePath $sysFile -Encoding UTF8
"Timestamp: $timestamp" | Out-File -FilePath $sysFile -Append -Encoding UTF8
"" | Out-File -FilePath $sysFile -Append -Encoding UTF8

Run-Cmd "systeminfo" $sysFile

if (Get-Command Get-ComputerInfo -ErrorAction SilentlyContinue) {
    Run-Cmd "Get-ComputerInfo" $sysFile
}

# ------- 2. Installed packages -------
$pkgFile = Join-Path $outDir "packages.txt"

"==== INSTALLED PACKAGES ====" | Out-File -FilePath $pkgFile -Encoding UTF8
"Timestamp: $timestamp" | Out-File -FilePath $pkgFile -Append -Encoding UTF8
"" | Out-File -FilePath $pkgFile -Append -Encoding UTF8

if (Get-Command Get-Package -ErrorAction SilentlyContinue) {
    Add-Content -Path $pkgFile -Value "[INFO] Get-Package output:"
    Get-Package | Sort-Object Name | Format-Table -AutoSize | Out-File -FilePath $pkgFile -Append -Encoding UTF8
    Add-Content -Path $pkgFile -Value ""
} else {
    Add-Content -Path $pkgFile -Value "[WARN] Get-Package not available."
}

if (Get-Command winget -ErrorAction SilentlyContinue) {
    Add-Content -Path $pkgFile -Value "[INFO] winget list:"
    Run-Cmd "winget list" $pkgFile
} else {
    Add-Content -Path $pkgFile -Value "[INFO] winget not found."
    Add-Content -Path $pkgFile -Value ""
}

if (Get-Command choco -ErrorAction SilentlyContinue) {
    Add-Content -Path $pkgFile -Value "[INFO] choco list -lo:"
    Run-Cmd "choco list -lo" $pkgFile
} else {
    Add-Content -Path $pkgFile -Value "[INFO] choco not found."
    Add-Content -Path $pkgFile -Value ""
}

# ------- 3. Python info -------
$pyFile = Join-Path $outDir "python.txt"

"==== PYTHON INFO ====" | Out-File -FilePath $pyFile -Encoding UTF8
"Timestamp: $timestamp" | Out-File -FilePath $pyFile -Append -Encoding UTF8
"" | Out-File -FilePath $pyFile -Append -Encoding UTF8

$pythonFound = $false

if (Get-Command py -ErrorAction SilentlyContinue) {
    $pythonFound = $true
    Add-Content -Path $pyFile -Value "[INFO] Python via py launcher:"
    Run-Cmd "py -0p" $pyFile
}

if (-not $pythonFound -and (Get-Command python -ErrorAction SilentlyContinue)) {
    $pythonFound = $true
    Run-Cmd "python --version" $pyFile
}

if (-not $pythonFound) {
    Add-Content -Path $pyFile -Value "[WARN] No Python interpreter found in PATH."
}

if (Get-Command pip -ErrorAction SilentlyContinue) {
    Add-Content -Path $pyFile -Value "[INFO] pip list --format=freeze:"
    Run-Cmd "pip list --format=freeze" $pyFile
} elseif (Get-Command pip3 -ErrorAction SilentlyContinue) {
    Add-Content -Path $pyFile -Value "[INFO] pip3 list --format=freeze:"
    Run-Cmd "pip3 list --format=freeze" $pyFile
} else {
    Add-Content -Path $pyFile -Value "[WARN] No pip/pip3 found in PATH."
}

# ------- 4. Node.js info -------
$nodeFile = Join-Path $outDir "node.txt"

"==== NODE.JS INFO ====" | Out-File -FilePath $nodeFile -Encoding UTF8
"Timestamp: $timestamp" | Out-File -FilePath $nodeFile -Append -Encoding UTF8
"" | Out-File -FilePath $nodeFile -Append -Encoding UTF8

if (Get-Command node -ErrorAction SilentlyContinue) {
    Run-Cmd "node -v" $nodeFile
} else {
    Add-Content -Path $nodeFile -Value "[INFO] node not found in PATH."
}

if (Get-Command npm -ErrorAction SilentlyContinue) {
    Add-Content -Path $nodeFile -Value "[INFO] npm ls -g --depth=0:"
    Run-Cmd "npm ls -g --depth=0" $nodeFile
} else {
    Add-Content -Path $nodeFile -Value "[INFO] npm not found in PATH."
}

# ------- 5. Environment variables -------
$envFile = Join-Path $outDir "env-vars.txt"

"==== ENVIRONMENT VARIABLES ====" | Out-File -FilePath $envFile -Encoding UTF8
"Timestamp: $timestamp" | Out-File -FilePath $envFile -Append -Encoding UTF8
"" | Out-File -FilePath $envFile -Append -Encoding UTF8

Get-ChildItem Env: | Sort-Object Name | Format-Table -AutoSize | Out-File -FilePath $envFile -Append -Encoding UTF8

# ------- 6. PATH contents -------
$pathFile = Join-Path $outDir "path.txt"

"==== PATH CONTENTS ====" | Out-File -FilePath $pathFile -Encoding UTF8
"Timestamp: $timestamp" | Out-File -FilePath $pathFile -Append -Encoding UTF8
"" | Out-File -FilePath $pathFile -Append -Encoding UTF8
"PATH=$($Env:PATH)" | Out-File -FilePath $pathFile -Append -Encoding UTF8
"" | Out-File -FilePath $pathFile -Append -Encoding UTF8
"Entries (one per line):" | Out-File -FilePath $pathFile -Append -Encoding UTF8

$Env:PATH -split ";" | Out-File -FilePath $pathFile -Append -Encoding UTF8

# ------- 7. Summary -------
$summaryFile = Join-Path $outDir "summary.txt"

"==== SUMMARY ====" | Out-File -FilePath $summaryFile -Encoding UTF8
"Timestamp: $timestamp" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"Output directory: $outDir" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"Files generated:" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"  - system-info.txt" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"  - packages.txt" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"  - python.txt" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"  - node.txt" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"  - env-vars.txt" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"  - path.txt" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"" | Out-File -FilePath $summaryFile -Append -Encoding UTF8
"This script is READ-ONLY: it does not install, remove, or modify packages." | Out-File -FilePath $summaryFile -Append -Encoding UTF8

Write-Host "Audit complete. See directory: $outDir"

Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
.\audit.ps1



termux or linux

nano audit.sh

#!/usr/bin/env bash

set -euo pipefail

timestamp="$(date +%Y%m%d-%H%M%S)"
out_dir="system-audit-${timestamp}"
mkdir -p "${out_dir}/txt" "${out_dir}/json" "${out_dir}/html" "${out_dir}/meta"

echo "Creating system audit in: ${out_dir}"

# ---------- Helpers ----------
write_json() {
    local file="$1"
    local content="$2"
    printf '%s\n' "${content}" > "${file}"
}

append_line() {
    local file="$1"
    shift
    printf '%s\n' "$@" >> "${file}"
}

run_cmd() {
    local cmd="$1"
    local outfile="$2"
    append_line "${outfile}" "== ${cmd} =="
    if bash -lc "${cmd}" >> "${outfile}" 2>&1; then
        :
    else
        append_line "${outfile}" "[WARN] Command failed: ${cmd}"
    fi
    append_line "${outfile}" ""
}

# Detect Termux
is_termux="no"
if [ -n "${PREFIX-}" ] && echo "${PREFIX}" | grep -qi "com.termux"; then
    is_termux="yes"
elif [ -d "/data/data/com.termux/files/usr" ]; then
    is_termux="yes"
fi

# ---------- 1. System info ----------
sys_txt="${out_dir}/txt/system-info.txt"
sys_json="${out_dir}/json/system-info.json"

append_line "${sys_txt}" "==== SYSTEM INFO ===="
append_line "${sys_txt}" "Timestamp: ${timestamp}" ""

if [ -f /etc/os-release ]; then
    cat /etc/os-release >> "${sys_txt}"
    append_line "${sys_txt}" ""
else
    append_line "${sys_txt}" "[INFO] /etc/os-release not found" ""
fi

run_cmd "uname -a" "${sys_txt}"
command -v hostnamectl >/dev/null 2>&1 && run_cmd "hostnamectl" "${sys_txt}"

append_line "${sys_txt}" "Detected Termux: ${is_termux}" ""

os_name="$(grep ^NAME= /etc/os-release 2>/dev/null | head -n1 | cut -d= -f2 | tr -d '"')"
os_id="$(grep ^ID= /etc/os-release 2>/dev/null | head -n1 | cut -d= -f2 | tr -d '"')"
kernel="$(uname -r 2>/dev/null || echo "")"
hostname="$(hostname 2>/dev/null || echo "")"

write_json "${sys_json}" "$(cat <<EOF
{
  "timestamp": "${timestamp}",
  "detected_termux": "${is_termux}",
  "os_name": "${os_name}",
  "os_id": "${os_id}",
  "kernel": "${kernel}",
  "hostname": "${hostname}"
}
EOF
)"

# ---------- 2. Packages ----------
pkg_txt="${out_dir}/txt/packages.txt"
pkg_json="${out_dir}/json/packages.json"

append_line "${pkg_txt}" "==== INSTALLED PACKAGES ====" "Timestamp: ${timestamp}" ""

pkg_manager="unknown"
pkg_cmd=""

if [ "${is_termux}" = "yes" ]; then
    pkg_manager="termux-pkg"
    pkg_cmd="pkg list-installed"
elif command -v dpkg-query >/dev/null 2>&1; then
    pkg_manager="dpkg"
    pkg_cmd="dpkg-query -W -f='\${Package}\t\${Version}\n'"
elif command -v pacman >/dev/null 2>&1; then
    pkg_manager="pacman"
    pkg_cmd="pacman -Q"
fi

if [ -n "${pkg_cmd}" ]; then
    append_line "${pkg_txt}" "[INFO] Using package manager: ${pkg_manager}" ""
    run_cmd "${pkg_cmd}" "${pkg_txt}"
else
    append_line "${pkg_txt}" "[WARN] No supported package manager found (Termux/apt/dpkg/pacman)." ""
fi

# Flatpak
if command -v flatpak >/dev/null 2>&1; then
    append_line "${pkg_txt}" "[INFO] flatpak list" ""
    run_cmd "flatpak list" "${pkg_txt}"
fi

# Snap
if command -v snap >/dev/null 2>&1; then
    append_line "${pkg_txt}" "[INFO] snap list" ""
    run_cmd "snap list" "${pkg_txt}"
fi

write_json "${pkg_json}" "$(cat <<EOF
{
  "timestamp": "${timestamp}",
  "package_manager": "${pkg_manager}",
  "flatpak_present": $(command -v flatpak >/dev/null 2>&1 && echo "true" || echo "false"),
  "snap_present": $(command -v snap >/dev/null 2>&1 && echo "true" || echo "false"),
  "note": "Full package details are stored in txt/packages.txt"
}
EOF
)"

# ---------- 3. Python (global) ----------
py_txt="${out_dir}/txt/python-global.txt"
py_json="${out_dir}/json/python-global.json"

append_line "${py_txt}" "==== PYTHON (GLOBAL) ====" "Timestamp: ${timestamp}" ""

py_bin=""
if command -v python3 >/dev/null 2>&1; then
    py_bin="python3"
elif command -v python >/dev/null 2>&1; then
    py_bin="python"
fi

if [ -n "${py_bin}" ]; then
    run_cmd "${py_bin} --version" "${py_txt}"
else
    append_line "${py_txt}" "[WARN] No Python interpreter found in PATH" ""
fi

pip_format="--format=freeze"
if command -v pip3 >/dev/null 2>&1; then
    append_line "${py_txt}" "[INFO] pip3 list ${pip_format}" ""
    run_cmd "pip3 list ${pip_format}" "${py_txt}"
elif command -v pip >/dev/null 2>&1; then
    append_line "${py_txt}" "[INFO] pip list ${pip_format}" ""
    run_cmd "pip list ${pip_format}" "${py_txt}"
else
    append_line "${py_txt}" "[WARN] No pip/pip3 found in PATH" ""
fi

write_json "${py_json}" "$(cat <<EOF
{
  "timestamp": "${timestamp}",
  "python_binary": "${py_bin}",
  "note": "Global Python and pip listing stored in txt/python-global.txt"
}
EOF
)"

# ---------- 4. Python virtualenvs (best-effort) ----------
venv_txt="${out_dir}/txt/python-venvs.txt"
venv_json="${out_dir}/json/python-venvs.json"

append_line "${venv_txt}" "==== PYTHON VIRTUALENVS (BEST-EFFORT) ====" "Timestamp: ${timestamp}" ""

venv_paths=()

# Common venv hubs
[ -n "${WORKON_HOME-}" ] && [ -d "${WORKON_HOME}" ] && venv_paths+=("${WORKON_HOME}")
[ -d "${HOME}/.virtualenvs" ] && venv_paths+=("${HOME}/.virtualenvs")

# Add direct venvs if present in current tree (shallow scan)
while IFS= read -r d; do
    venv_paths+=("${d}")
done < <(find . -maxdepth 3 -type d -name "venv" 2>/dev/null || true)

declare -a venv_json_entries
for base in "${venv_paths[@]:-}"; do
    [ -d "${base}" ] || continue
    for v in "${base}"/*; do
        [ -d "${v}" ] || continue
        vname="$(basename "${v}")"
        py_candidate=""
        if [ -x "${v}/bin/python" ]; then
            py_candidate="${v}/bin/python"
        elif [ -x "${v}/Scripts/python.exe" ]; then
            py_candidate="${v}/Scripts/python.exe"
        fi
        if [ -n "${py_candidate}" ]; then
            append_line "${venv_txt}" "---- VENV: ${vname} (${v}) ----"
            run_cmd "\"${py_candidate}\" --version" "${venv_txt}"
            run_cmd "\"${py_candidate}\" -m pip list --format=freeze" "${venv_txt}"
            venv_json_entries+=("{\"name\": \"${vname}\", \"path\": \"${v}\", \"python\": \"${py_candidate}\"}")
        fi
    done
done

if [ ${#venv_json_entries[@]} -eq 0 ]; then
    append_line "${venv_txt}" "[INFO] No virtualenvs detected in common locations." ""
fi

printf '{\n  "timestamp": "%s",\n  "venvs": [\n' "${timestamp}" > "${venv_json}"
for i in "${!venv_json_entries[@]}"; do
    sep=","
    [ "$i" -eq $((${#venv_json_entries[@]}-1)) ] && sep=""
    printf "    %s%s\n" "${venv_json_entries[$i]}" "${sep}" >> "${venv_json}"
done
printf "  ]\n}\n" >> "${venv_json}"

# ---------- 5. Node.js ----------
node_txt="${out_dir}/txt/node.txt"
node_json="${out_dir}/json/node.json"

append_line "${node_txt}" "==== NODE.JS ====" "Timestamp: ${timestamp}" ""

node_present="false"
npm_present="false"

if command -v node >/dev/null 2>&1; then
    node_present="true"
    run_cmd "node -v" "${node_txt}"
else
    append_line "${node_txt}" "[INFO] node not found in PATH" ""
fi

if command -v npm >/dev/null 2>&1; then
    npm_present="true"
    append_line "${node_txt}" "[INFO] npm ls -g --depth=0" ""
    run_cmd "npm ls -g --depth=0" "${node_txt}"
else
    append_line "${node_txt}" "[INFO] npm not found in PATH" ""
fi

write_json "${node_json}" "$(cat <<EOF
{
  "timestamp": "${timestamp}",
  "node_present": ${node_present},
  "npm_present": ${npm_present},
  "note": "Full Node/npm details stored in txt/node.txt"
}
EOF
)"

# ---------- 6. Environment variables ----------
env_txt="${out_dir}/txt/env-vars.txt"
env_json="${out_dir}/json/env-vars.json"

append_line "${env_txt}" "==== ENVIRONMENT VARIABLES ====" "Timestamp: ${timestamp}" ""
run_cmd "printenv" "${env_txt}"

# JSON env as a flat map
{
    echo "{"
    echo "  \"timestamp\": \"${timestamp}\","
    echo "  \"env\": {"
    first=1
    while IFS='=' read -r name value; do
        [ -z "${name}" ] && continue
        # escape backslashes and quotes
        esc_name=$(printf '%s' "${name}" | sed 's/\\/\\\\/g; s/"/\\"/g')
        esc_value=$(printf '%s' "${value}" | sed 's/\\/\\\\/g; s/"/\\"/g')
        if [ "${first}" -eq 0 ]; then
            echo ","
        fi
        printf '    "%s": "%s"' "${esc_name}" "${esc_value}"
        first=0
    done < <(printenv)
    echo
    echo "  }"
    echo "}"
} > "${env_json}"

# ---------- 7. PATH ----------
path_txt="${out_dir}/txt/path.txt"
path_json="${out_dir}/json/path.json"

append_line "${path_txt}" "==== PATH CONTENTS ====" "Timestamp: ${timestamp}" ""
append_line "${path_txt}" "PATH=${PATH}" "" "Entries (one per line):"
echo "${PATH}" | tr ':' '\n' >> "${path_txt}"

{
    echo "{"
    echo "  \"timestamp\": \"${timestamp}\","
    printf '  "PATH": "%s",\n' "$(printf '%s' "${PATH}" | sed 's/\\/\\\\/g; s/"/\\"/g')"
    echo '  "entries": ['
    first=1
    echo "${PATH}" | tr ':' '\n' | while IFS= read -r p; do
        esc_p=$(printf '%s' "${p}" | sed 's/\\/\\\\/g; s/"/\\"/g')
        if [ "${first}" -eq 0 ]; then
            echo ","
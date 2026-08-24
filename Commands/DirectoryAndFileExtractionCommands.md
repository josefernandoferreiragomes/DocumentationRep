📁 Directory Tree & File Extraction Cheatsheet
A compact reference for generating directory trees and extracting all Dockerfiles across nested subdirectories, using WSL, PowerShell, and CMD.

🗂️ 1. Directory Tree Generation
WSL / Linux
Exclude folders using -I:

bash
tree -a --charset=UTF-8 -I "bin|obj" > directory-tree.txt
Include files:

bash
tree -a -I "bin|obj" > directory-tree.txt
PowerShell
PowerShell cannot exclude folders using tree, so use Get-ChildItem.

Folders only, excluding bin and obj
powershell
Get-ChildItem -Recurse -Directory |
    Where-Object { $_.Name -notin @('bin','obj') } |
    Select-Object FullName |
    Out-File directory-tree.txt
Files + folders, excluding bin and obj
powershell
Get-ChildItem -Recurse |
    Where-Object { $_.FullName -notmatch '\\bin\\' -and $_.FullName -notmatch '\\obj\\' } |
    Select-Object FullName |
    Out-File directory-tree.txt
CMD
tree cannot exclude folders.
Use PowerShell from CMD:

cmd
powershell -command "Get-ChildItem -Recurse -Directory ^| Where-Object { $_.Name -notin @('bin','obj') } ^| Select-Object FullName" > directory-tree.txt
🐳 2. Extract All Dockerfiles Into a Single File
PowerShell (recommended)
Correct handling of nested Dockerfiles:

powershell
Get-ChildItem -Recurse -File -Filter Dockerfile | ForEach-Object {
    "# $($_.FullName)" | Add-Content -Path all-dockerfiles.txt
    Get-Content -Path $_.FullName | Add-Content -Path all-dockerfiles.txt
    "" | Add-Content -Path all-dockerfiles.txt
}
PowerShell — One‑liner version
powershell
Get-ChildItem -Recurse -File -Filter Dockerfile | ForEach-Object { "# $($_.FullName)" | Add-Content all-dockerfiles.txt; Get-Content $_.FullName | Add-Content all-dockerfiles.txt; "" | Add-Content all-dockerfiles.txt }
PowerShell — Wildcard Dockerfile search
Useful if your Dockerfiles have names like:

Dockerfile.dev

Dockerfile.api

Dockerfile.*

powershell
Get-ChildItem -Recurse -File -Filter "Dockerfile*" | ForEach-Object {
    "# $($_.FullName)" | Add-Content -Path all-dockerfiles.txt
    Get-Content -Path $_.FullName | Add-Content -Path all-dockerfiles.txt
    "" | Add-Content -Path all-dockerfiles.txt
}
WSL / Linux
Handles nested folders perfectly:

bash
find . -type f -name "Dockerfile*" -exec sh -c '
    echo "# $(realpath "$1")"
    cat "$1"
    echo ""
' _ {} \; > all-dockerfiles.txt
CMD
Works recursively:

cmd
(for /r %f in (Dockerfile*) do (
    echo # %f
    type "%f"
    echo.
)) > all-dockerfiles.txt
📌 Notes
All commands work recursively.

PowerShell versions correctly handle nested paths.

Wildcard Dockerfile search allows flexible naming conventions.

WSL version is the most robust for large repos.

# Windows Developer Environment Setup (D Drive Storage)

This guide documents how to configure a Windows development environment so that developer tools, caches, package stores, and project data are stored on the **D:** drive instead of filling up the **C:** drive.

---

# Goal

Keep:

* Windows OS on `C:`
* Applications on `D:`
* Development caches and package stores on `D:`

This prevents the Windows partition from gradually filling up over time.

---

# Directory Structure

Create:

```text
D:\DevData
├── .bun
├── .cursor
├── .docker
├── .vscode
├── npm-cache
├── pnpm-store
```

---

# Node.js Installation

Install Node.js to:

```text
D:\Program Files\nodejs
```

Verify:

```powershell
node --version
npm --version
```

---

# pnpm Setup

Install pnpm:

```powershell
npm install -g pnpm
```

Configure pnpm store:

```powershell
pnpm config set store-dir D:\DevData\pnpm-store
```

Verify:

```powershell
pnpm store path
```

Expected:

```text
D:\DevData\pnpm-store\v11
```

---

# npm Cache Setup

Move npm cache to D:

```powershell
npm config set cache D:\DevData\npm-cache --global
```

Verify:

```powershell
npm config get cache
```

Expected:

```text
D:\DevData\npm-cache
```

---

# Bun Setup

## Step 1: Configure Bun Install Location

Run:

```powershell
[Environment]::SetEnvironmentVariable(
    "BUN_INSTALL",
    "D:\DevData\.bun",
    "User"
)
```

## Step 2: Close PowerShell Completely

Close all PowerShell windows.

Open a brand-new PowerShell window.

Verify:

```powershell
echo $env:BUN_INSTALL
```

Expected:

```text
D:\DevData\.bun
```

Do not continue until this is displayed.

## Step 3: Install Bun

```powershell
powershell -c "irm bun.sh/install.ps1 | iex"
```

Expected output:

```text
The binary is located at D:\DevData\.bun\bin\bun.exe
```

If the installer shows:

```text
C:\Users\<username>\.bun
```

stop and verify that `BUN_INSTALL` is correctly loaded in the current PowerShell session.

## Step 4: Verify Installation

Check that Bun exists:

```powershell
Test-Path "D:\DevData\.bun\bin\bun.exe"
```

Expected:

```text
True
```

## Step 5: Verify PATH

Close PowerShell again.

Open a new PowerShell window.

Run:

```powershell
where.exe bun
bun --version
```

Expected:

```text
D:\DevData\.bun\bin\bun.exe
```

and a Bun version number.

## Optional Cleanup

If Bun was accidentally installed to:

```text
C:\Users\<username>\.bun
```

remove it:

```powershell
Remove-Item -Recurse -Force "$HOME\.bun"
```

Verify:

```powershell
Test-Path "$HOME\.bun"
```

Expected:

```text
False
```

---

# Move Existing Folders Using Junctions

Purpose:

Keep actual files on D:

```text
D:\DevData\.cursor
D:\DevData\.docker
D:\DevData\.vscode
```

while applications continue using:

```text
C:\Users\<username>\.cursor
C:\Users\<username>\.docker
C:\Users\<username>\.vscode
```

---

## Move .cursor

Close Cursor.

Move:

```text
C:\Users\<username>\.cursor
```

to:

```text
D:\DevData\.cursor
```

Open Command Prompt as Administrator:

```cmd
mklink /J "C:\Users\<username>\.cursor" "D:\DevData\.cursor"
```

---

## Move .docker

Quit Docker Desktop.

Shutdown WSL:

```powershell
wsl --shutdown
```

Move:

```text
C:\Users\<username>\.docker
```

to:

```text
D:\DevData\.docker
```

Create junction:

```cmd
mklink /J "C:\Users\<username>\.docker" "D:\DevData\.docker"
```

---

## Move .vscode

Close VS Code.

Move:

```text
C:\Users\<username>\.vscode
```

to:

```text
D:\DevData\.vscode
```

Create junction:

```cmd
mklink /J "C:\Users\<username>\.vscode" "D:\DevData\.vscode"
```

---

# Verify Junctions

Open Command Prompt:

```cmd
dir C:\Users\<username>
```

Expected:

```text
<JUNCTION> .cursor [D:\DevData\.cursor]
<JUNCTION> .docker [D:\DevData\.docker]
<JUNCTION> .vscode [D:\DevData\.vscode]
```

---

# Verification Checklist

Verify npm cache:

```powershell
npm config get cache
```

Expected:

```text
D:\DevData\npm-cache
```

Verify pnpm store:

```powershell
pnpm store path
```

Expected:

```text
D:\DevData\pnpm-store\v11
```

Verify Bun:

```powershell
echo $env:BUN_INSTALL
where.exe bun
bun --version
```

Expected:

```text
D:\DevData\.bun
D:\DevData\.bun\bin\bun.exe
```

---

# Final Result

Developer-related storage resides on D:

```text
D:\DevData\.bun
D:\DevData\.cursor
D:\DevData\.docker
D:\DevData\.vscode
D:\DevData\npm-cache
D:\DevData\pnpm-store
```

while Windows remains primarily isolated to C:.

# Windows Developer Environment Setup (D Drive Storage)

This guide documents how to configure a Windows development environment so that developer tools, caches, package stores, and project data are stored on the **D:** drive instead of filling up the **C:** drive.

---

# Goal

Keep:

- Windows OS on `C:`
- Applications on `D:`
- Development caches and package stores on `D:`

This prevents the Windows partition from gradually filling up.

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

Set Bun installation directory:

```powershell
[Environment]::SetEnvironmentVariable(
"BUN_INSTALL",
"D:\DevData\.bun",
"User"
)
```

Restart PowerShell.

Install Bun:

```powershell
powershell -c "irm bun.sh/install.ps1 | iex"
```

Verify:

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

# Move Existing Folders Using Junctions

Purpose:

Keep actual files on D:

```text
D:\DevData\.cursor
D:\DevData\.docker
D:\DevData\.vscode
```

while applications still access:

```text
C:\Users\rawad\.cursor
C:\Users\rawad\.docker
C:\Users\rawad\.vscode
```

---

## Move .cursor

Close Cursor.

Move:

```text
C:\Users\rawad\.cursor
```

to:

```text
D:\DevData\.cursor
```

Open Command Prompt as Administrator:

```cmd
mklink /J "C:\Users\rawad\.cursor" "D:\DevData\.cursor"
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
C:\Users\rawad\.docker
```

to:

```text
D:\DevData\.docker
```

Create junction:

```cmd
mklink /J "C:\Users\rawad\.docker" "D:\DevData\.docker"
```

---

## Move .vscode

Close VS Code.

Move:

```text
C:\Users\rawad\.vscode
```

to:

```text
D:\DevData\.vscode
```

Create junction:

```cmd
mklink /J "C:\Users\rawad\.vscode" "D:\DevData\.vscode"
```

---

# Verify Junctions

Open Command Prompt:

```cmd
dir C:\Users\rawad
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

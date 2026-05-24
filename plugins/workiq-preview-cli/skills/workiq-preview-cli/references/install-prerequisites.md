# Installing prerequisites for the WorkIQ CLI plugin

This plugin invokes the WorkIQ CLI as a local shell command (`workiq`). That requires:

- **Node.js 18 or later** (LTS recommended) and **npm**
- The `@microsoft/workiq@preview` npm package installed **globally** so the `workiq` binary is on `PATH`

To verify what's installed:

```powershell
node --version
npm --version
workiq version
```

`node` should report `v18.x.x` or newer. If `workiq version` errors with "command not found" / "is not recognized", run:

```powershell
npm install -g @microsoft/workiq@preview
```

Then run `workiq auth login` once to sign in.

If `node --version` itself fails or reports a version below 18, install or upgrade Node.js using one of the options below, then return here to install `@microsoft/workiq` globally.

---

## Windows

### Option A — winget (recommended, built into Windows 10/11)

```powershell
winget install OpenJS.NodeJS.LTS
```

### Option B — Chocolatey

```powershell
choco install nodejs-lts
```

### Option C — Scoop

```powershell
scoop install nodejs-lts
```

### Option D — Official installer

Download and run the Windows installer (`.msi`) from <https://nodejs.org/en/download> and pick the **LTS** build.

> After installing, **open a new terminal** so the updated `PATH` is picked up, then **restart the Copilot CLI session**.

---

## macOS

### Option A — Homebrew (recommended)

```powershell
brew install node
```

### Option B — Official installer

Download and run the macOS installer (`.pkg`) from <https://nodejs.org/en/download> and pick the **LTS** build.

### Option C — nvm (if you manage multiple Node versions)

```powershell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
# Then in a new shell:
nvm install --lts
nvm use --lts
```

---

## Linux

### Debian / Ubuntu

```powershell
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Fedora / RHEL / CentOS Stream

```powershell
sudo dnf install -y nodejs npm
```

### Arch / Manjaro

```powershell
sudo pacman -S --noconfirm nodejs npm
```

### Alpine

```powershell
sudo apk add --no-cache nodejs npm
```

### Any distro — nvm (per-user, no sudo)

```powershell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
# Then in a new shell:
nvm install --lts
nvm use --lts
```

---

## Cross-platform — Volta

[Volta](https://volta.sh) installs and pins Node.js per project and works the same on Windows, macOS, and Linux.

```powershell
# macOS / Linux
curl https://get.volta.sh | bash

# Windows
winget install Volta.Volta

# Then in a new shell:
volta install node@lts
```

---

## After installing Node.js

1. **Open a new terminal window** so the updated `PATH` is loaded.
2. Verify with `node --version` — it should print `v18.x.x` or newer.
3. Install the WorkIQ CLI globally:

   ```powershell
   npm install -g @microsoft/workiq@preview
   workiq version
   ```

4. Sign in once (opens a browser):

   ```powershell
   workiq auth login
   ```

5. **Restart the Copilot CLI session** so subsequent shell calls see the new `workiq` binary on `PATH`.
6. Retry the WorkIQ command that originally failed.

## Still failing?

- On Windows, if `node --version` works in a new terminal but Copilot CLI still can't find `workiq`, fully close and reopen the terminal application (not just the tab), then start Copilot CLI again.
- Ensure that the directory containing `node` / `workiq` is on the `PATH` for the user running Copilot CLI. On Windows: `where.exe workiq`. On macOS/Linux: `which workiq`.
- If you use `nvm` or `volta`, make sure the shell that launches Copilot CLI sources the version manager's init script (e.g., `~/.bashrc`, `~/.zshrc`).
- If `workiq` is found but `workiq auth login` hangs on Windows, run `workiq config set disableBrokeredAuth=true` to force browser-based login.

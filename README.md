# 🧰 Workshop Malspam — Linux setup guide

Dette dokumentet beskriver hvordan du setter opp alle nødvendige verktøy for å delta i **workshop_malspam** på Linux (Remnux, Ubuntu, Debian eller Kali).
Remnux har mange av disse verktøyene fra før, men guiden sikrer at alt er oppdatert og tilgjengelig.

---

## ✅ Innhold

* Python 3 + pip
* pipx (for trygg installasjon av CLI-verktøy)
* oletools
* 7-Zip (p7zip)
* lnkinfo
* msidump (fra msitools)
* (valgfritt) VS Code

---

## 🔧 Enkel steg-for-steg

### 0) Oppdater systemet

> Denne kommandoen oppdaterer systemet.
> Hvis du får feilmelding om at Python ikke finnes etterpå, gå til steg 2.

```bash
sudo apt update
sudo apt upgrade -y
```

---

### 1) Sjekk om Python 3 finnes

```bash
python3 --version || echo "Python3 ikke funnet"
```

Hvis du ser en versjon (f.eks. `Python 3.10.12`), gå videre.
Hvis ikke → fortsett til steg 2.

---

### 2) Installer Python 3 + pip

```bash
sudo apt install -y python3 python3-pip
# Noen verktøy forventer også dette:
sudo apt install -y python3-venv
```

Sjekk:

```bash
python3 --version
python3 -m pip --version
```

---

### 3) Installer systemverktøy (7-Zip, msidump, lnkinfo)

```bash
sudo apt install -y p7zip-full msitools liblnk-utils
```

**Forklaring:**

* `p7zip-full` = 7-Zip CLI
* `msitools` = inneholder `msidump` og `msiextract`
* `liblnk-utils` = gir `lnkinfo` for .LNK-filer

Sjekk:

```bash
7z --help >/dev/null && echo "7z OK"
msidump --help >/dev/null && echo "msidump OK"
lnkinfo -h >/dev/null && echo "lnkinfo OK"
```

---

### 4) Installer pipx (anbefalt)

`pipx` installerer Python-baserte kommandolinjeverktøy trygt, uten at du trenger venv.

```bash
sudo apt install -y pipx
pipx ensurepath
# Hvis PATH-varsel: logg ut/inn eller kjør:
export PATH="$HOME/.local/bin:$PATH"
```

---

### 5) Installer oletools

Verktøy for analyse av Office-dokumenter med makroer.

```bash
pipx install git+https://github.com/decalage2/oletools.git
```

Sjekk:

```bash
olevba --help >/dev/null && echo "oletools OK"
```

---

### 6) (Valgfritt) Installer VS Code

```bash
sudo snap install code --classic
```

Sjekk:

```bash
code --version >/dev/null && echo "VS Code OK"
```

---

### 7) Verifiser alt på én gang

```bash
python3 --version
pipx --version 2>/dev/null || echo "pipx mangler"
olevba --version 2>/dev/null || echo "oletools fraværende"
7z --help >/dev/null && echo "7z OK"
msidump --help >/dev/null && echo "msidump OK"
lnkinfo -h >/dev/null && echo "lnkinfo OK"
```

---

## 🚀 Alt-i-ett-script

Kopiér og lim dette direkte i terminalen:

```bash
set -e

# Update
sudo apt update
sudo apt upgrade -y

# Python + pip
if ! command -v python3 >/dev/null 2>&1; then
  sudo apt install -y python3 python3-pip python3-venv
fi

# System tools
sudo apt install -y p7zip-full msitools liblnk-utils

# pipx
if ! command -v pipx >/dev/null 2>&1; then
  sudo apt install -y pipx
fi
pipx ensurepath || true
export PATH="$HOME/.local/bin:$PATH"

# oletools
if ! command -v olevba >/dev/null 2>&1; then
  pipx install git+https://github.com/decalage2/oletools.git || \
  python3 -m pip install --user -U git+https://github.com/decalage2/oletools.git
fi

# VS Code (optional)
if ! command -v code >/dev/null 2>&1; then
  if command -v snap >/dev/null 2>&1; then
    sudo snap install code --classic
  fi
fi

echo "✅ Oppsett ferdig!"
echo "Prøv f.eks. 'olevba --help' for å teste oletools."
```

---

## 🧪 Eksempler på bruk

```bash
# Pakk ut et arkiv
7z x stage1.7z -pinfected

# Analyser makroer i et dokument
olevba suspicious.docm

# Undersøk en snarvei
lnkinfo sample.lnk

# Se innholdet i en MSI-fil
msidump installer.msi
```

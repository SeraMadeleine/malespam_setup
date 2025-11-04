# Enkel steg-for-steg

## 0) Oppdater først 
Her vil du få feilmelding dersom python ikke er lastet ned. Hvis det ikke er installert, gå til steg 2. 

```bash
sudo apt update
sudo apt upgrade -y
```

## 1) Sjekk om Python 3 finnes

```bash
python3 --version  || echo "Python3 ikke funnet"
```

Hvis du får versjon (f.eks. `Python 3.10.12`) er Python klart. Hvis ikke — installer i steg 2.

## 2) Installer Python3 + pip (hvis ikke til stede)

```bash
# Installer Python og pip hvis de mangler
sudo apt install -y python3 python3-pip
# (valgfritt, trygt) noen verktøy forventer dette pakke: 
sudo apt install -y python3-venv
```

`python3 --version` og `python3 -m pip --version` skal nå vise versjon.

## 3) Installer systemverktøy (7zip, msitools, liblnk-utils)

```bash
sudo apt install -y p7zip-full msitools liblnk-utils
```

* `p7zip-full` = 7-Zip CLI
* `msitools` = inneholder `msidump` og `msiextract`
* `liblnk-utils` = `lnkinfo` for å analysere `.lnk`

Kopier for å sjekke at alt er installert:

```bash
7z --help >/dev/null && echo "7z OK"
msidump --help >/dev/null && echo "msidump OK"
lnkinfo -h >/dev/null && echo "lnkinfo OK"
```

## 4) Installer pipx (anbefalt måte for CLI Python-verktøy)

`pipx` lager isolerte miljø for CLI-verktøy og setter dem i PATH — du trenger aldri å aktivere venv manuelt.

```bash
sudo apt install -y pipx
pipx ensurepath
# Hvis du ser melding om PATH: enten logg ut/inn eller kør:
export PATH="$HOME/.local/bin:$PATH"
```

## 5) Installer oletools (via pipx)

```bash
pipx install git+https://github.com/decalage2/oletools.git
```

Verifiser:

```bash
olevba --help >/dev/null && echo "oletools OK"
```



## 6) (Valgfritt) Installer VS Code (enklest via snap)

```bash
sudo snap install code --classic
# eller bruk apt/pakkekilde om du foretrekker
```

Sjekk:

```bash
code --version >/dev/null && echo "VS Code OK"
```

## 7) Hurtig-verifisering alle sammen

Kjør denne for å se at alt er på plass:

```bash
python3 --version && pipx --version 2>/dev/null || echo "pipx mangler"
olevba --version 2>/dev/null || echo "oletools fraværende"
7z --help >/dev/null && echo "7z OK"
msidump --help >/dev/null && echo "msidump OK"
lnkinfo -h >/dev/null && echo "lnkinfo OK"
```

---

# Alt i ett (copy/paste script)

Dette scriptet forsøker å gjøre alt automatisk. Lim inn i terminal:

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

# pipx install
if ! command -v pipx >/dev/null 2>&1; then
  sudo apt install -y pipx
fi
pipx ensurepath || true
export PATH="$HOME/.local/bin:$PATH"

# oletools via pipx
if ! command -v olevba >/dev/null 2>&1; then
  pipx install oletools[full] || python3 -m pip install --user -U oletools[full]
fi

# VS Code (valgfritt)
if ! command -v code >/dev/null 2>&1; then
  if command -v snap >/dev/null 2>&1; then
    sudo snap install code --classic
  fi
fi

echo "Setup ferdig. Sjekk at $HOME/.local/bin er i PATH hvis noe mangler."
```



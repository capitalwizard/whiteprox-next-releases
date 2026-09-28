# WhiteProX Next · tesztkiadások

Windows x64, Apple Silicon Mac és Intel Mac telepítők a [legfrissebb kiadásban](https://github.com/capitalwizard/whiteprox-next-releases/releases/latest).

Ez a tárhely kizárólag terjesztési csomagokat tartalmaz. A forráskód külön, privát tárhelyen található.

## Letöltés terminálból

**macOS:** másold ezt a Terminalba:

```sh
curl --fail --show-error --location --proto '=https' --tlsv1.2 https://github.com/capitalwizard/whiteprox-next-releases/releases/latest/download/download-macos.sh -o /tmp/whiteprox-download.sh && sh /tmp/whiteprox-download.sh
```

A letöltő felismeri az Apple Silicon / Intel gépet, és a Letöltések mappába menti a megfelelő DMG-t. Nyisd meg a DMG-t, és húzd az alkalmazást az Applications mappába.

**Windows:** másold ezt a PowerShellbe:

```powershell
# Download only: does not change execution policy or launch the installer.
$Destination = Join-Path $env:USERPROFILE 'Downloads'
$ErrorActionPreference = 'Stop'
$Base = 'https://github.com/capitalwizard/whiteprox-next-releases/releases/latest/download'
if ([Environment]::OSVersion.Platform -ne [PlatformID]::Win32NT) { throw 'This downloader is for Windows.' }
if (-not [Environment]::Is64BitOperatingSystem) { throw 'WhiteProX Next requires 64-bit Windows.' }
[Net.ServicePointManager]::SecurityProtocol = [Net.ServicePointManager]::SecurityProtocol -bor [Net.SecurityProtocolType]::Tls12
$Manifest = Invoke-RestMethod -Uri "$Base/release.json"
$Asset = $Manifest.downloads.win.x64
if ($Asset.name -notmatch '^WhiteProX-Next-[A-Za-z0-9.-]+-win-x64\.exe$' -or $Asset.sha256 -notmatch '^[a-f0-9]{64}$') { throw 'Invalid release manifest.' }
New-Item -ItemType Directory -Force -Path $Destination | Out-Null
$Temporary = Join-Path ([IO.Path]::GetTempPath()) ('whiteprox-download-' + [guid]::NewGuid())
New-Item -ItemType Directory -Path $Temporary | Out-Null
try {
  $Download = Join-Path $Temporary $Asset.name
  Write-Host "Downloading WhiteProX Next $($Manifest.version) (Windows x64)…"
  Invoke-WebRequest -UseBasicParsing -Uri "$Base/$($Asset.name)" -OutFile $Download
  if ((Get-FileHash -Algorithm SHA256 -LiteralPath $Download).Hash.ToLowerInvariant() -ne $Asset.sha256) { throw 'Checksum mismatch: download discarded.' }
  $Target = Join-Path $Destination $Asset.name
  if (Test-Path -LiteralPath $Target) {
    if ((Get-FileHash -Algorithm SHA256 -LiteralPath $Target).Hash.ToLowerInvariant() -ne $Asset.sha256) { throw 'A different file already exists; nothing was overwritten.' }
  } else { Move-Item -LiteralPath $Download -Destination $Target }
  Write-Host "Verified download: $Target"
  Write-Host 'Open this installer to install or update WhiteProX Next.'
  if ($Manifest.distributionMode -eq 'preview') { Write-Host 'Preview build: no Windows publisher certificate. Windows may display an unknown-publisher warning.' }
} finally { Remove-Item -LiteralPath $Temporary -Recurse -Force }
```

A parancsok letöltik és SHA-256 alapján ellenőrzik a telepítőt; nem indítanak telepítést. A Windows-letöltő külön `download-windows.ps1` fájlként is elérhető a kiadás mellékletei között. Nem szükséges módosítani a PowerShell végrehajtási házirendjét.

## Tesztkiadás és frissítés

- A jelenlegi **preview** csomagoknak nincs Apple Developer ID / notarizálás, illetve Windows kiadói tanúsítvány. Az operációs rendszer jelezheti az ismeretlen fejlesztőt; csak akkor telepítsd, ha megbízol ebben a forrásban. macOS-en szükség lehet a Rendszerbeállítások → Adatvédelem és biztonság jóváhagyására.
- Az alkalmazás indulás után és időközönként ellenőrzi az új kiadásokat, és jelzi a letöltést. Kereskedés közben nem indítja újra önmagát: a telepítés külön művelet.
- Az aláírás nélküli Mac-kiadás automatikus telepítése nem garantált. Új verzióhoz futtasd újra a fenti letöltőparancsot, majd cseréld le az alkalmazást. A felhasználói beállítások külön mappában maradnak.
- A régi `0.1.0` fejlesztői példányok nem tartalmaznak kiadási frissítési címet. Első alkalommal telepítsd az itt letöltött verziót.
- A belépést minden tesztelő saját hozzáféréssel végzi. A telepítő nem tartalmaz bejelentkezési adatokat.

Hibajelzésnél add meg az app verzióját, az operációs rendszert és a hiba lépéseit. Jelszót vagy tokent ne küldj.

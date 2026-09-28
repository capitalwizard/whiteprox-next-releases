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
$r = Invoke-RestMethod 'https://github.com/capitalwizard/whiteprox-next-releases/releases/latest/download/release.json'; $a = $r.downloads.win.x64; if ($a.name -notmatch '^WhiteProX-Next-[0-9.]+-win-x64\.exe$' -or $a.sha256 -notmatch '^[a-f0-9]{64}$') { throw 'Invalid release' }; $p = Join-Path ([Environment]::GetFolderPath('UserProfile')) ('Downloads\' + $a.name); Invoke-WebRequest ('https://github.com/capitalwizard/whiteprox-next-releases/releases/latest/download/' + $a.name) -OutFile $p; if ((Get-FileHash $p -Algorithm SHA256).Hash.ToLowerInvariant() -ne $a.sha256) { Remove-Item $p; throw 'Checksum mismatch' }; Write-Host "Verified download: $p"
```

A parancsok letöltik és SHA-256 alapján ellenőrzik a telepítőt; nem indítanak telepítést. A Windows-letöltő külön `download-windows.ps1` fájlként is elérhető a kiadás mellékletei között. Nem szükséges módosítani a PowerShell végrehajtási házirendjét.

## Tesztkiadás és frissítés

- A jelenlegi **preview** csomagoknak nincs Apple Developer ID / notarizálás, illetve Windows kiadói tanúsítvány. Az operációs rendszer jelezheti az ismeretlen fejlesztőt; csak akkor telepítsd, ha megbízol ebben a forrásban. macOS-en szükség lehet a Rendszerbeállítások → Adatvédelem és biztonság jóváhagyására.
- Az alkalmazás indulás után és időközönként ellenőrzi az új kiadásokat, és jelzi a letöltést. Kereskedés közben nem indítja újra önmagát: a telepítés külön művelet.
- Az aláírás nélküli Mac-kiadás automatikus telepítése nem garantált. Új verzióhoz futtasd újra a fenti letöltőparancsot, majd cseréld le az alkalmazást. A felhasználói beállítások külön mappában maradnak.
- A régi `0.1.0` fejlesztői példányok nem tartalmaznak kiadási frissítési címet. Első alkalommal telepítsd az itt letöltött verziót.
- A belépést minden tesztelő saját hozzáféréssel végzi. A telepítő nem tartalmaz bejelentkezési adatokat.

Hibajelzésnél add meg az app verzióját, az operációs rendszert és a hiba lépéseit. Jelszót vagy tokent ne küldj.

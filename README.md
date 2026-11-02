CREATED WITH AI

# Clean-Up Tool

A small, careful cleaner for Windows 11 with a dark interface. You first see
what can be cleaned up, you choose what may go, and by default everything goes
to the Recycle Bin.

*English first — the Dutch text follows below.*

## Getting started

There are two ways:

**Portable** — double-click `Clean-Up Tool.exe`. No installation and no Python
needed. The app starts without administrator rights; the two system categories
are skipped. The **Restart as administrator** button gives you those too
(Windows will ask for permission).

**Setup** — double-click `Clean-Up-Tool-Setup-1.0.0.exe`:

- the wizard first asks which language to use (Dutch or English);
- a one-time administrator prompt (UAC), after that the app is installed for
  all users;
- you can choose the installation folder yourself; the default is
  `C:\Program Files\Clean-Up Tool`;
- the desktop shortcut is ticked by default (you can untick it), a Start Menu
  entry is always created;
- the install shows up in *Settings → Apps*, with a working uninstaller that
  also removes the shortcuts and asks whether the log may be deleted.

## Language

Top right there is a language selector: **Nederlands** or **English**. The app
updates every text immediately and remembers the choice in
`%LOCALAPPDATA%\Clean-Up Tool\settings.json`. On the first start on a new PC it
follows the Windows language (Dutch if Windows is Dutch, otherwise English).

To add a language: add a code to `LANGUAGES` in `cleaner/i18n.py` and fill in the
same keys in `STRINGS`. Nothing else in the app needs to change.

## How it works

1. **Scanning** starts automatically and takes about half a minute on this PC
   (the duplicate scan is the slowest part).
2. On the left are the categories, with the space that can be reclaimed per
   category. Click a category to see its files.
3. Each category has a checkbox. Unticking it skips the whole category. In the
   list you can tick individual files (click the box in front of the name, or
   double-click the row).
4. **Dry run** shows what would happen without deleting anything.
5. **Clean up** moves the selection to the Recycle Bin. If you switch off
   *To recycle bin*, it is deleted permanently — the app asks for an extra
   confirmation.

## Categories

| Category | What it takes | Selected by default |
|---|---|---|
| Temporary files | Contents of your own `%TEMP%` older than an hour | yes |
| Old installers | `.exe/.msi/.msix/.appx/.msu` in Downloads, older than 30 days | yes |
| Browser caches | Cache from Chrome, Edge, Brave and Firefox (only when the browser is closed) | yes |
| Crash dumps & error reports | Dumps from programs that crashed | yes |
| Old log files | `.log/.old/.bak` older than 90 days in Documents/Desktop/Downloads | yes |
| Empty folders | Folders without content in your profile | yes |
| Large files | Files larger than 500 MB in your profile | **no** |
| Duplicate files | Identical files (same content), one copy is kept | yes |
| Recycle bin | Empty the Recycle Bin in one go | yes |
| Windows temp | `C:\Windows\Temp`, older than an hour | yes (administrator) |
| Windows Update cache | `C:\Windows\SoftwareDistribution\Download` | yes (administrator) |

Large files are deliberately **not** selected: those are often films, ISOs or
project files you want to keep yourself.

## Safety

- The scanner never deletes anything; only the **Clean up** button does.
- Every deletion is checked again: a file must be inside the folder the
  category is meant for. Files outside it are refused, even if the interface
  had a bug.
- Nothing is ever done to the registry, services or installed programs.
- `AppData`, `Program Files`, `Windows` (except the two subfolders mentioned)
  and your documents as a whole are not a category.
- What is gone is in the Recycle Bin and can be restored.
- Everything the app does is logged in
  `%LOCALAPPDATA%\Clean-Up Tool\logs\clean-up.log` (via the **Log** button).

## What the app deliberately does not do

- **Windows.old** and other system clutter: use the **Windows Disk Cleanup**
  button, which lets Windows do it safely.
- Registry "cleaners", RAM boosters, emptying entire `AppData` folders.
- Uninstalling programs — that belongs in *Settings → Apps*.

## Building it yourself

Requires: Python 3.11+ with `pyinstaller`, `customtkinter` and `pillow`, plus
Inno Setup 6 for the installer.

```powershell
py -m pip install customtkinter pillow pyinstaller
winget install JRSoftware.InnoSetup

powershell -ExecutionPolicy Bypass -File build.ps1        # .exe only
powershell -ExecutionPolicy Bypass -File build_setup.ps1  # .exe + setup
```

The result is in `dist\`: `Clean-Up Tool.exe` (±18 MB) and
`Clean-Up-Tool-Setup-1.0.0.exe` (±20 MB).

A test build of the installer that does not ask for administrator rights
(installs into `%LOCALAPPDATA%\Programs\CleanUpToolTest`):

```powershell
& "$env:LOCALAPPDATA\Programs\Inno Setup 6\ISCC.exe" /DTESTBUILD installer.iss
```

## Files

```
main.py               entry point, logging, error handling
cleaner/fsutil.py     recycle bin, sizes, process detection (Windows APIs)
cleaner/scanner.py    categories and how they are scanned
cleaner/deleter.py    deletion + safety check
cleaner/ui.py         the interface
make_icon.py          generates app.ico + wizard images
installer.iss         Inno Setup script for the setup
build.ps1             builds the .exe
build_setup.ps1       builds the .exe and the setup
```

## Caveats

- An unsigned .exe can trigger a SmartScreen or Defender warning ("unknown
  publisher"). That is normal for self-built PyInstaller programs; the app only
  asks for rights when you click *Restart as administrator*.
- If your browser is running during the scan, its cache is skipped. Close the
  browser and scan again.
- Files in another program's `%TEMP%` may belong to that program (some mod
  managers store data there). Check the names before emptying the whole folder.

---

# Clean-Up Tool — Nederlands

Een kleine, voorzichtige opruimer voor Windows 11 met een donkere interface.
Je ziet eerst wát er opgeruimd kan worden, kiest zelf wat weg mag, en standaard
gaat alles naar de prullenbak.

## Starten

Er zijn twee manieren:

**Portable** — dubbelklik op `Clean-Up Tool.exe`. Geen installatie, geen Python
nodig. De app start zonder beheerdersrechten; de twee systeemcategorieën worden
dan overgeslagen. Met de knop **Als beheerder herstarten** krijg je ook die
categorieën (Windows vraagt dan om toestemming).

**Setup** — dubbelklik op `Clean-Up-Tool-Setup-1.0.0.exe`:

- de wizard vraagt eerst de taal (Nederlands of Engels);
- eenmalig beheerdersrechten (UAC), daarna is de app voor alle gebruikers
  geïnstalleerd;
- de installatiemap is zelf te kiezen, standaard `C:\Program Files\Clean-Up Tool`;
- snelkoppeling op het bureaublad staat aangevinkt (uit te vinken), startmenu
  wordt altijd aangemaakt;
- de installatie verschijnt in *Instellingen → Apps*, met een werkende
  deinstallatie die ook de snelkoppelingen opruimt en vraagt of het logboek weg mag.

## Taal

Rechtsboven staat een taalkeuze: **Nederlands** of **English**. De app past
meteen alle teksten aan en onthoudt de keuze in
`%LOCALAPPDATA%\Clean-Up Tool\settings.json`. Bij de eerste start op een
nieuwe pc volgt hij de taal van Windows (Nederlands als Windows Nederlands is,
anders Engels).

Een taal toevoegen: zet in `cleaner/i18n.py` een extra code in `LANGUAGES` en
vul dezelfde sleutels aan in `STRINGS`. De rest van de app hoeft niet aangepast
te worden.

## Hoe het werkt

1. **Scannen** gebeurt automatisch bij het starten en duurt op deze pc ongeveer
   een halve minuut (de duplicaten-scan is het langzaamste onderdeel).
2. Links staan de categorieën met de ruimte die er per categorie te winnen is.
   Klik op een categorie om de bijbehorende bestanden te zien.
3. Per categorie staat een vinkje. Klik je dat uit, dan wordt die hele categorie
   overgeslagen. In de lijst kun je losse bestanden aan- of uitvinken
   (klik op het vakje voor de naam, of dubbelklik op de rij).
4. **Proefrun** laat zien wat er zou gebeuren zonder iets te verwijderen.
5. **Opruimen** verplaatst de selectie naar de prullenbak. Zet je de schakelaar
   *Naar prullenbak* uit, dan wordt het definitief verwijderd — daar vraagt de
   app extra om bevestiging.

## Categorieën

| Categorie | Wat het pakt | Standaard aangevinkt |
|---|---|---|
| Tijdelijke bestanden | Inhoud van je eigen `%TEMP%` ouder dan een uur | ja |
| Oude installatiebestanden | `.exe/.msi/.msix/.appx/.msu` in Downloads, ouder dan 30 dagen | ja |
| Browser-caches | Cache van Chrome, Edge, Brave en Firefox (alleen als de browser dicht is) | ja |
| Crashdumps & foutrapporten | Dumps van vastgelopen programma's | ja |
| Oude logbestanden | `.log/.old/.bak` ouder dan 90 dagen in Documenten/Bureaublad/Downloads | ja |
| Lege mappen | Mappen zonder inhoud in je profiel | ja |
| Grote bestanden | Bestanden > 500 MB in je profiel | **nee** |
| Dubbele bestanden | Identieke bestanden (zelfde inhoud), één exemplaar blijft staan | ja |
| Prullenbak | Prullenbak in één keer leegmaken | ja |
| Windows-temp | `C:\Windows\Temp`, ouder dan een uur | ja (beheerder) |
| Windows Update-cache | `C:\Windows\SoftwareDistribution\Download` | ja (beheerder) |

Grote bestanden staan bewust **niet** aangevinkt: dat zijn vaak films, ISO's of
projectbestanden die je zelf wilt bewaren.

## Veiligheid

- De scanner verwijdert nooit iets; alleen de knop **Opruimen** doet dat.
- Elke verwijdering wordt opnieuw gecontroleerd: een bestand moet binnen de map
  vallen waar de categorie voor bedoeld is. Bestanden buiten die map worden
  geweigerd, ook al zou er een fout in de interface zitten.
- Er wordt nooit iets gedaan met het register, services of geïnstalleerde
  programma's.
- `AppData`, `Program Files`, `Windows` (behalve de twee genoemde submappen) en
  je documenten als geheel zijn geen categorie.
- Wat weg is, staat in de prullenbak en is terug te zetten.
- Alles wat de app doet wordt gelogd in
  `%LOCALAPPDATA%\Clean-Up Tool\logs\clean-up.log` (via de knop **Logboek**).

## Wat de app bewust niet doet

- **Windows.old** en andere systeemrommel: gebruik de knop
  **Windows Schijfopruiming**, dat doet Windows zelf veilig.
- Register "cleaners", RAM-boosters, het leeggooien van hele `AppData`-mappen.
- Programma's verwijderen — dat hoort in *Instellingen → Apps*.

## Zelf bouwen

Nodig: Python 3.11+ met `pyinstaller`, `customtkinter` en `pillow`, plus
Inno Setup 6 voor de installer.

```powershell
py -m pip install customtkinter pillow pyinstaller
winget install JRSoftware.InnoSetup

powershell -ExecutionPolicy Bypass -File build.ps1        # alleen de .exe
powershell -ExecutionPolicy Bypass -File build_setup.ps1  # .exe + setup
```

Het resultaat staat in `dist\`: `Clean-Up Tool.exe` (±18 MB) en
`Clean-Up-Tool-Setup-1.0.0.exe` (±20 MB).

Een testversie van de installer die geen beheerdersrechten vraagt (installeert
in `%LOCALAPPDATA%\Programs\CleanUpToolTest`):

```powershell
& "$env:LOCALAPPDATA\Programs\Inno Setup 6\ISCC.exe" /DTESTBUILD installer.iss
```

## Bestanden

```
main.py               startpunt, logging, foutafhandeling
cleaner/fsutil.py     prullenbak, groottes, procesdetectie (Windows-API's)
cleaner/scanner.py    categorieën en het inlezen daarvan
cleaner/deleter.py    verwijderen + veiligheidscontrole
cleaner/ui.py         de interface
make_icon.py          genereert app.ico + wizard-afbeeldingen
installer.iss         Inno Setup-script voor de setup
build.ps1             bouwt de .exe
build_setup.ps1       bouwt de .exe én de setup
```

## Let op

- Bij een .exe die niet digitaal ondertekend is, kan SmartScreen of Defender
  waarschuwen ("onbekende uitgever"). Dat is normaal bij zelfgebouwde
  PyInstaller-programma's; de app vraagt alleen om rechten als jij op
  *Als beheerder herstarten* klikt.
- Draait je browser tijdens de scan, dan wordt de cache van die browser
  overgeslagen. Sluit de browser en scan opnieuw.
- Bestanden in `%TEMP%` van andere programma's kunnen bij die programma's horen
  (sommige mod-managers bewaren er data). Kijk even naar de namen voordat je
  de hele map leeggooit.

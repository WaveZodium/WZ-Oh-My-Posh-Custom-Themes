# Oh My Posh – installation på en ny Windows-dator

Den här guiden aktiverar det egna temat **dark-purple.omp.json** i PowerShell och Windows Terminal. Spara temafilen tillsammans med denna guide när du flyttar till en annan dator.

## 1. Installera programmen

Öppna PowerShell. Installera Oh My Posh:

```powershell
winget install --id JanDeDobbeleer.OhMyPosh -e --source winget
```

Om Windows Terminal saknas:

```powershell
winget install --id Microsoft.WindowsTerminal -e --source winget
```

Guiden fungerar med Windows PowerShell och PowerShell 7. Om du vill använda PowerShell 7 och det saknas:

```powershell
winget install --id Microsoft.PowerShell -e --source winget
```

Stäng terminalen helt efter installationen och öppna den igen. Välj det PowerShell du vill använda i terminalens flikmeny. Under **Inställningar → Start → Standardprofil** kan du göra det till standard.

Kontrollera installationen:

```powershell
oh-my-posh --version
$PSVersionTable.PSVersion
```

## 2. Installera och välj typsnitt

Temat innehåller ikoner som kräver ett Nerd Font-typsnitt:

```powershell
oh-my-posh font install meslo
```

Öppna **Windows Terminal → Inställningar → Standardvärden → Utseende**. Välj det installerade Meslo-typsnittet, exempelvis **MesloLGM Nerd Font** eller **Meslo LGM NF**. Namnet kan variera med typsnittsversionen. Spara. Starta om terminalen om typsnittet inte syns.

Om en enskild terminalprofil har ett eget typsnittsval behöver du ändra det där också. Ett installerat typsnitt måste även väljas för att användas.

## 3. Lägg temafilen på en fast plats

Skapa en mapp under din användarprofil:

```powershell
New-Item -ItemType Directory -Path "$HOME\.config\oh-my-posh" -Force
explorer.exe "$HOME\.config\oh-my-posh"
```

Kopiera **dark-purple.omp.json** till mappen som öppnas. Filen ska ligga här:

```text
C:\Users\DITT-ANVÄNDARNAMN\.config\oh-my-posh\dark-purple.omp.json
```

Kontrollera att den finns:

```powershell
Test-Path "$HOME\.config\oh-my-posh\dark-purple.omp.json"
```

Resultatet ska vara `True`. Kontrollera att filen inte av misstag heter `.json.txt`.

## 4. Prova temat direkt

Kör följande i PowerShell:

```powershell
oh-my-posh init pwsh --config "$HOME\.config\oh-my-posh\dark-purple.omp.json" | Invoke-Expression
```

Det aktiverar temat i den aktuella sessionen. Nästa steg gör inställningen permanent.

## 5. Aktivera temat vid varje start

Kör dessa kommandon i samma PowerShell-variant som du vill använda:

```powershell
if (!(Test-Path -LiteralPath $PROFILE)) {
    New-Item -ItemType File -Path $PROFILE -Force | Out-Null
}
notepad $PROFILE
```

Om profilen redan innehåller en rad som startar Oh My Posh, ersätt den raden. Annars lägger du till följande rad. Behåll andra inställningar i filen.

```powershell
oh-my-posh init pwsh --config "$HOME\.config\oh-my-posh\dark-purple.omp.json" | Invoke-Expression
```

Spara filen och öppna en ny PowerShell-flik. Alternativt läser du om profilen i den aktuella fliken:

```powershell
. $PROFILE
```

**Windows PowerShell 5.1 och PowerShell 7 har separata profiler.** Upprepa detta steg i båda om du använder båda. `pwsh` i Oh My Posh-kommandot är dess integrationsnamn för PowerShell; raden används även i Windows PowerShell.

Du kan se vilken profil som redigeras med:

```powershell
$PROFILE
```

## 6. Vad temat visar

| Del | Innehåll |
| --- | --- |
| Vänster | Mappnamn i lila segment. |
| Administratör | Röd ADMIN-markering när sessionen körs med förhöjda rättigheter. |
| Git | Gren, en asterisk vid ändringar och pilar när grenen ligger före/efter upstream enligt lokal Git-information. |
| Körtid | Tid för kommandon som passerar tröskeln 500 ms. |
| Höger | .NET-version när segmentets villkor är uppfyllda, batteriprocent när den är tillgänglig och klockslag. |
| Nästa rad | Lila inmatningsmarkör `❯`. |

Git-information kräver Git och en Git-mapp. .NET-information kräver en tillgänglig .NET-installation och visas beroende på segmentets aktiveringsvillkor. Alla delar behöver därför inte synas i varje mapp eller på varje dator.

Temafilen styr prompten. Terminalens övergripande bakgrund, textstorlek och transparens ställs in separat i Windows Terminal.

## 7. Felsökning

### Oh My Posh hittas inte

Stäng alla terminalfönster och öppna igen så att den uppdaterade sökvägen läses in. Kontrollera:

```powershell
Get-Command oh-my-posh
```

### Standardtemat visas i stället

Kontrollera att profilen använder `--config` med rätt sökväg och att ingen senare rad startar Oh My Posh igen med en annan konfiguration:

```powershell
Get-Content $PROFILE
Test-Path "$HOME\.config\oh-my-posh\dark-purple.omp.json"
```

### Rutor eller frågetecken i stället för ikoner

Välj Meslo Nerd Font i den terminalprofil som faktiskt används. Om problemet gäller VS Code måste typsnittet väljas även där; Windows Terminals inställning påverkar inte VS Codes terminal.

### Profilen blockeras av körningsprincipen

Läs först inställningarna:

```powershell
Get-ExecutionPolicy -List
```

På en dator där du själv får hantera körningsprincipen kan du tillåta lokala skript för ditt användarkonto:

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

Om arbetsdatorns `MachinePolicy` eller `UserPolicy` styr detta behöver IT hantera inställningen. Starta därefter en ny PowerShell-flik.

### JSON-fel eller schemavarning i editorn

Kontrollera att filen kan läsas som JSON:

```powershell
Get-Content "$HOME\.config\oh-my-posh\dark-purple.omp.json" -Raw | ConvertFrom-Json | Out-Null
```

Den bifogade temafilen har `$schema` satt till `https://githubusercontent.com`, vilket inte är en schemasökväg. För korrekt validering och komplettering i editorn kan du ändra just den raden till:

```json
"$schema": "https://raw.githubusercontent.com/JanDeDobbeleer/oh-my-posh/main/themes/schema.json",
```

Detta är metadata för editorn och byter inte temats färger eller layout. Originalfilen har inte ändrats som del av denna guide.

## 8. Flytta till nästa dator

Ta med `dark-purple.omp.json` och denna guide. Installera Oh My Posh och typsnittet, kopiera temat till samma plats under `$HOME` och lägg till initieringsraden i den nya datorns PowerShell-profil. `$HOME` anpassar sökvägen till det aktuella användarkontot.

## Dokumentation

- [Installation på Windows](https://ohmyposh.dev/docs/installation/windows)
- [Typsnitt](https://ohmyposh.dev/docs/installation/fonts)
- [PowerShell och profil](https://ohmyposh.dev/docs/installation/prompt)
- [Eget tema och --config](https://ohmyposh.dev/docs/installation/customize)

Guiden sammanställd 2026-10-22 utifrån den bifogade temafilen. Kommandona har granskats mot dokumentationen men har inte körts på måldatorn.

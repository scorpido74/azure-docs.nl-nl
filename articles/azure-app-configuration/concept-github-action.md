---
title: GitHub-acties gebruiken met synchronisatie van Azure-app-configuratie
description: GitHub-acties gebruiken om een update te activeren voor uw app-configuratie-exemplaar wanneer u uw GitHub-opslagplaats bijwerkt
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46d4aa4d4d37e9cac928e8d1a9e5e77ca0f30f18
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384055"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Uw App Configuration-exemplaar synchroniseren met GitHub-acties

Teams die hun bestaande bronbeheerpraktijken willen blijven gebruiken, kunnen GitHub Actions gebruiken om hun GitHub-repository automatisch te synchroniseren met hun App Configuration Store. Hiermee u wijzigingen aanbrengen in uw config-bestanden zoals u dat normaal zou doen, terwijl u voordelen voor app-configuratie krijgt, zoals: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Gecentraliseerde configuratie buiten uw code <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Configuratie bijwerken zonder uw hele app opnieuw te implementeren <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integratie met services zoals Azure App Service en Functies. 

Een GitHub [Actions-werkstroom](https://help.github.com/articles/about-github-actions#workflow) definieert een geautomatiseerd proces in een GitHub-repository. De *Azure App Configuration Sync* Action activeert updates voor een app-configuratie-instantie wanneer wijzigingen worden aangebracht in de bronopslagplaats. Het maakt gebruik van een YAML (.yml) bestand gevonden in het `/.github/workflows/` pad van uw repository om de stappen en parameters te definiëren. U configuratie-updates activeren wanneer u app-configuratiebestanden pusht, bekijkt of vertakt, net zoals u dat doet met app-code.

De GitHub-documentatie biedt een diepgaand overzicht van GitHub-workflows en -acties. [documentation](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) 

## <a name="enable-github-actions-in-your-repository"></a>GitHub-acties inschakelen in uw opslagplaats
Als u deze GitHub-actie wilt gebruiken, gaat u naar uw opslagplaats en selecteert u het tabblad **Acties.** Klik op **Nieuwe werkstroom**en **stel vervolgens zelf een werkstroom in.** Zoek tot slot op de marktplaats naar 'Azure App Configuration Sync'.
> [!div class="mx-imgBorder"]
> ![Het tabblad Actie selecteren](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![De actie voor het synchroniseren van de app-configuratie selecteren](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Configuratiebestanden synchroniseren na een push
Met deze actie worden Azure App Configuration-bestanden gesynchroniseerd wanneer een wijziging wordt gepusht naar `appsettings.json`. Wanneer een ontwikkelaar een `appsettings.json`wijziging naar , de actie App Configuratie synchronisatie updates van de app configuratie instantie met de nieuwe waarden.

Het eerste gedeelte van deze werkstroom geeft aan `appsettings.json` dat de actie wordt geactiveerd *op* een *push* die naar de *hoofdvertakking* wordt geleid. In het tweede gedeelte worden de taken weergegeven die worden uitgevoerd zodra de actie is geactiveerd. De actie controleert de relevante bestanden en werkt de instantie App-configuratie bij met behulp van de verbindingstekenreeks die als geheim in de opslagplaats is opgeslagen.  Zie het [artikel van GitHub](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) over het maken en gebruiken van versleutelde geheimen voor meer informatie over het gebruik van geheimen in GitHub.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-a-dynamic-label-on-sync"></a>Een dynamisch label gebruiken voor synchronisatie
Met de vorige actie wordt `appsettings.json` het exemplaar App-configuratie bijgewerkt wanneer deze wordt bijgewerkt. Met deze actie wordt een dynamisch label op elke synchronisatie ingevoegd, zodat elke synchronisatie op unieke wijze kan worden geïdentificeerd en codewijzigingen kunnen worden toegewezen aan config-wijzigingen.

Het eerste gedeelte van deze werkstroom geeft aan `appsettings.json` dat de actie wordt geactiveerd *op* een *push* die naar de *hoofdvertakking* wordt geleid. In het tweede deel wordt een taak uitgevoerd die een uniek label voor de config-update maakt op basis van de commit hash. De taak werkt vervolgens de instantie App-configuratie bij met de nieuwe waarden en het unieke label voor deze update.

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-strict-sync"></a>Strikte synchronisatie gebruiken
Wanneer de strikte modus is ingeschakeld, zorgt de synchronisatie ervoor dat de instantie App-configuratie precies overeenkomt met het configuratiebestand voor het gegeven voorvoegsel en label. Sleutelwaardenparen met hetzelfde voorvoegsel en label die niet in het configuratiebestand staan, worden verwijderd. 
 
Als de strikte modus niet is ingeschakeld, worden alleen de sleutelwaarden van het configuratiebestand ingesteld. Er worden geen sleutelwaardeparen verwijderd. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```

## <a name="use-max-depth-to-limit-github-action"></a>Gebruik maximale diepte om GitHub Action te beperken
Het standaardgedrag voor geneste JSON-kenmerken is het hele object plat te maken.  De JSON hieronder definieert dit sleutel-waarde paar:

| Sleutel | Waarde |
| --- | --- |
| Object:Inner:Innerkey | Innervalue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Als het geneste object de waarde is die naar de instantie Configuratie wordt geduwd, u de *dieptewaarde* gebruiken om de afvlakking op de juiste diepte te stoppen. 

```json
on: 
  push: 
    branches: 
      - 'master' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Met een diepte van 2 geeft het bovenstaande voorbeeld nu het volgende sleutelwaardepaar als resultaat:

| Sleutel | Waarde |
| --- | --- |
| Object:Inner | {"Innerkey":"Innervalue"} |

## <a name="understand-action-inputs"></a>Inzicht in actie-ingangen
Invoerparameters geven gegevens op die door de actie tijdens runtime worden gebruikt.  De volgende tabel bevat invoerparameters die worden geaccepteerd door App Configuration Sync en de verwachte waarden voor elk.  Zie de [documentatie](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)van GitHub voor meer informatie over actie-ingangen voor GitHub-acties.

> [!Note]
> Invoer-id's zijn hoofdletterongevoelig.


| Invoernaam | Vereist? | Waarde |
|----|----|----|
| configurationFile configurationFile configurationFile configurationFile | Ja | Relatief pad naar het configuratiebestand in de opslagplaats.  Glob-patronen worden ondersteund en kunnen meerdere bestanden bevatten. |
| formaat | Ja | Bestandsindeling van het configuratiebestand.  Geldige indelingen zijn: JSON, YAML, eigenschappen. |
| Connectionstring | Ja | Verbindingstekenreeks voor het exemplaar App-configuratie. De verbindingstekenreeks moet als geheim worden opgeslagen in de GitHub-opslagplaats en alleen de geheime naam moet in de werkstroom worden gebruikt. |
| Scheidingsteken | Ja | Scheidingsteken die wordt gebruikt bij het afvlakken van het configuratiebestand naar sleutelwaardeparen.  Geldige waarden zijn: . , ; : - _ __ / |
| Voorvoegsel | Nee | Voorvoegsel dat moet worden toegevoegd aan het begin van de toetsen. |
| label | Nee | Label dat wordt gebruikt bij het instellen van sleutelwaardeparen. Indien niet gespecificeerd, wordt een null-label gebruikt. |
| Strikte | Nee | Een booleaanse waarde die bepaalt of de strikte modus is ingeschakeld. De standaardwaarde is false. |
| Diepte | Nee | Maximale diepte voor het afvlakken van het configuratiebestand.  Diepte moet een positief getal zijn.  De standaardwaarde heeft geen maximale diepte. |
| tags | Nee | Hiermee geeft u de tagset op op sleutelwaardeparen.  De verwachte notatie is een gestouwde vorm van een JSON-object met de volgende vorm: { [propertyName: string]: tekenreeks; } Elke waardewaarde van de eigenschap wordt een tag. |

## <a name="next-steps"></a>Volgende stappen

In dit artikel leerde u over de GitHub-actie voor app-configuratiesynchronisatie en hoe deze kan worden gebruikt om updates voor uw app-configuratie-exemplaar te automatiseren. Ga verder naar het volgende [artikel](./concept-app-configuration-event.md)voor meer informatie over azure-appconfiguratie die reageert op wijzigingen in sleutelwaardeparen.

---
title: GitHub-acties gebruiken met Azure-app configuratie synchronisatie
description: GitHub-acties gebruiken om een update naar uw app-configuratie-exemplaar te activeren wanneer gedefinieerde acties worden uitgevoerd op een GitHub-opslag plaats
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 9d60f1885a85fd7d45090f1cb4905a3d95d9d1d6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523710"
---
# <a name="sync-your-app-configuration-instance-using-github-actions"></a>Uw App Configuration-exemplaar synchroniseren met GitHub-acties
Azure-app-configuratie maakt gebruik van GitHub-acties om updates te activeren voor een app-configuratie-exemplaar op basis van acties die worden uitgevoerd in een GitHub-opslag plaats. Met GitHub-werk stromen worden configuratie-updates geactiveerd, waardoor de integratie van deze updates in dezelfde werk stroom kan worden gebruikt om de app-code bij te werken.

Een GitHub actions- [werk stroom](https://help.github.com/articles/about-github-actions#workflow) definieert een geautomatiseerd proces in een github-opslag plaats. Dit proces vertelt u GitHub hoe u uw GitHub-project bouwt en implementeert. Azure-app configuratie biedt de *Azure-app configuratie synchronisatie* actie om updates van een app-configuratie-exemplaar in te scha kelen wanneer er wijzigingen in de bron opslagplaats worden aangebracht. 

Een YAML-bestand (. yml) gevonden in het `/.github/workflows/` pad van uw opslag plaats definieert uw werk stroom. Deze definitie bevat de stappen en para meters van de werk stroom.

GitHub-gebeurtenissen, zoals een push naar een opslag plaats, kunnen een GitHub actie werk stroom activeren.  Met de actie *Azure-app synchronisatie configuratie* kunt u een update van een app-configuratie-exemplaar activeren wanneer een opgegeven github-actie wordt uitgevoerd. U kunt configuratie-updates activeren tijdens het pushen, reviseren of vertakking van app-configuratie bestanden, net als bij app-code.

De GitHub- [documentatie](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) biedt uitgebreide weer gave van github-werk stromen en-acties. 

## <a name="enable-github-actions-in-your-repository"></a>GitHub-acties inschakelen in uw opslag plaats
Als u deze GitHub actie wilt gaan gebruiken, gaat u naar uw opslag plaats en selecteert u het tabblad **acties** . Klik op **nieuwe werk stroom**en **Stel zelf een werk stroom**in. Ga ten slotte naar de Marketplace voor ' Azure-app-configuratie synchronisatie '.
> [!div class="mx-imgBorder"]
> ![selecteert u het tabblad actie](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selecteer de synchronisatie actie voor de configuratie van de app](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Configuratie bestanden na een push synchroniseren
Met deze actie worden Azure-app configuratie bestanden gesynchroniseerd wanneer een wijziging naar `appsettings.json`wordt gepusht. Wanneer een ontwikkelaar een wijziging naar `appsettings.json`pusht, werkt de synchronisatie actie van de app-configuratie het app-configuratie-exemplaar bij met de nieuwe waarden.

In de eerste sectie van deze werk stroom wordt aangegeven dat de actie wordt geactiveerd *op* een *Push* met `appsettings.json` naar de *hoofd* vertakking. De tweede sectie bevat een lijst met de taken die worden uitgevoerd zodra de actie wordt geactiveerd. Met deze actie worden de relevante bestanden gecontroleerd en wordt het app-configuratie-exemplaar bijgewerkt met behulp van de connection string opgeslagen als een geheim in de opslag plaats.  Zie [het artikel van github](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) over het maken en gebruiken van versleutelde geheimen voor meer informatie over het gebruik van geheimen in github.

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

## <a name="use-a-dynamic-label-on-sync"></a>Een dynamisch label gebruiken bij synchronisatie
Met de vorige actie wordt het app-configuratie-exemplaar bijgewerkt wanneer `appsettings.json` wordt bijgewerkt. Met deze actie wordt een dynamisch label ingevoegd bij elke synchronisatie, zodat elke synchronisatie uniek kan worden geïdentificeerd en het toestaan van code wijzigingen aan configuratie wijzigingen wordt toegewezen.

In de eerste sectie van deze werk stroom wordt aangegeven dat de actie wordt geactiveerd *op* een *Push* met `appsettings.json` naar de *hoofd* vertakking. In het tweede gedeelte wordt een taak uitgevoerd waarmee een uniek label voor de configuratie-update wordt gemaakt op basis van de commit-hash. De taak werkt vervolgens het app-configuratie-exemplaar bij met de nieuwe waarden en het unieke label voor deze update.

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
Als strikte modus is ingeschakeld, zorgt de synchronisatie ervoor dat het app-configuratie-exemplaar overeenkomt met het configuratie bestand voor het opgegeven voor voegsel en label. Sleutel-waardeparen met hetzelfde voor voegsel en label die zich niet in het configuratie bestand bevinden, worden verwijderd. 
 
Als de strikte modus niet is ingeschakeld, worden door de synchronisatie alleen de sleutel waarden van het configuratie bestand ingesteld. Er worden geen sleutel-waardeparen verwijderd. 

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

## <a name="use-max-depth-to-limit-github-action"></a>Maximale diepte gebruiken om de GitHub-actie te beperken
Het standaard gedrag voor geneste JSON-kenmerken is het samen voegen van het hele object.  De JSON hieronder definieert deze sleutel/waarde-paar:

| Sleutel | Waarde |
| --- | --- |
| Object: binnenste: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Als het geneste object de waarde is die naar het configuratie-exemplaar moet worden gepusht, kunt u de *diepte* waarde gebruiken om het afvlakken met de juiste diepte te stoppen. 

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

Met het bovenstaande voor beeld wordt nu het volgende sleutel/waarde-paar als resultaat gegeven:

| Sleutel | Waarde |
| --- | --- |
| Object: binnenste | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Informatie over actie-invoer
Invoer parameters geven gegevens op die worden gebruikt door de actie tijdens runtime.  De volgende tabel bevat de invoer parameters die worden geaccepteerd door de synchronisatie van de app-configuratie en de verwachte waarden voor elke.  Zie de [documentatie](https://help.github.com/actions/automating-your-workflow-with-github-actions/metadata-syntax-for-github-actions#inputs)van github voor meer informatie over actie-invoer voor github-acties.

> [!Note]
> Invoer-Id's zijn niet hoofdletter gevoelig.


| Invoer naam | Vereist? | Waarde |
|----|----|----|
| configurationFile | Ja | Relatief pad naar het configuratie bestand in de opslag plaats.  Globs-patronen worden ondersteund en kunnen meerdere bestanden bevatten. |
| format | Ja | Bestands indeling van het configuratie bestand.  Geldige indelingen zijn: JSON, YAML, eigenschappen. |
| connectionString | Ja | Verbindings reeks voor het app-configuratie-exemplaar. De connection string moet worden opgeslagen als een geheim in de GitHub-opslag plaats en alleen de geheime naam moet in de werk stroom worden gebruikt. |
| scheiding | Ja | Scheidings teken dat wordt gebruikt bij het afvlakken van het configuratie bestand naar sleutel-waardeparen.  Geldige waarden zijn:. , ; : - _ __ / |
| prefix | Nee | Het voor voegsel dat moet worden toegevoegd aan het begin van de sleutels. |
| label | Nee | Label dat wordt gebruikt bij het instellen van sleutel-waardeparen. Als u geen waarde opgeeft, wordt een NULL-label gebruikt. |
| beperking | Nee | Een Booleaanse waarde die bepaalt of de strikte modus is ingeschakeld. De standaardwaarde is false. |
| diepga | Nee | Maximale diepte voor het afvlakken van het configuratie bestand.  Depth moet een positief getal zijn.  De standaard waarde heeft geen maximale diepte. |
| tags | Nee | Hiermee geeft u de tag die is ingesteld op sleutel-waardeparen.  De verwachte indeling is een stringified vorm van een JSON-object van de volgende vorm: {[propertyname: String]: String;} Elke eigenschaps naam-waarde wordt een tag. |

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de GitHub-actie voor het configureren van de app-configuratie en hoe deze kan worden gebruikt voor het automatiseren van updates voor uw app-configuratie-exemplaar. Ga door naar het volgende [artikel](./concept-app-configuration-event.md)voor meer informatie over de manier waarop Azure-app configuratie reageert op wijzigingen in sleutel-waardeparen.

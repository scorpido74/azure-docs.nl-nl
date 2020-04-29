---
title: Uw GitHub-opslag plaats synchroniseren met de app-configuratie
description: Gebruik GitHub-acties om uw app-configuratie-exemplaar automatisch bij te werken wanneer u uw GitHub-opslag plaats bijwerkt.
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/20/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 602ccddf97938022df3c5903b573608558fe5d35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585483"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Uw GitHub-opslag plaats synchroniseren met de app-configuratie

Teams die hun bestaande broncode beheer procedures willen blijven gebruiken, kunnen GitHub-acties gebruiken om de GitHub-opslag plaats automatisch te synchroniseren met de app-configuratie opslag. Hierdoor kunt u wijzigingen aanbrengen in de configuratie bestanden zoals u dat normaal zou doen, terwijl u de voor delen van de app-configuratie krijgt, zoals: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Gecentraliseerde configuratie buiten uw code <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Het bijwerken van de configuratie zonder uw volledige app opnieuw te implementeren <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integratie met Services als Azure App Service en functions. 

Een GitHub actions- [werk stroom](https://help.github.com/articles/about-github-actions#workflow) definieert een geautomatiseerd proces in een github-opslag plaats. Met de actie voor het *synchroniseren van Azure-app-configuratie* worden updates voor een app-configuratie-exemplaar geactiveerd wanneer er wijzigingen in de bron opslagplaats worden aangebracht. Er wordt een YAML-bestand (. yml) gebruikt dat `/.github/workflows/` is gevonden in het pad van uw opslag plaats om de stappen en para meters te definiëren. U kunt configuratie-updates activeren tijdens het pushen, reviseren of vertakking van app-configuratie bestanden, net als bij app-code.

De GitHub- [documentatie](https://help.github.com/actions/automating-your-workflow-with-github-actions/configuring-a-workflow) biedt uitgebreide weer gave van github-werk stromen en-acties. 

## <a name="enable-github-actions-in-your-repository"></a>GitHub-acties inschakelen in uw opslag plaats
Als u deze GitHub actie wilt gaan gebruiken, gaat u naar uw opslag plaats en selecteert u het tabblad **acties** . Klik op **nieuwe werk stroom**en **Stel zelf een werk stroom**in. Ga ten slotte naar de Marketplace voor ' Azure-app-configuratie synchronisatie '.
> [!div class="mx-imgBorder"]
> ![Selecteer het tabblad actie](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Selecteer de synchronisatie actie voor de configuratie van de app](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Configuratie bestanden na een push synchroniseren
Met deze actie worden Azure-app configuratie bestanden gesynchroniseerd wanneer een wijziging naar `appsettings.json`wordt gepusht. Wanneer een ontwikkelaar een wijziging doorstuurt `appsettings.json`, wordt het app-configuratie-exemplaar met de nieuwe waarden bijgewerkt met de synchronisatie actie van de app-configuratie.

In de eerste sectie van deze werk stroom wordt aangegeven dat de actie wordt geactiveerd `appsettings.json` *op* een *Push* die naar de *hoofd* vertakking bevat. De tweede sectie bevat een lijst met de taken die worden uitgevoerd zodra de actie wordt geactiveerd. Met deze actie worden de relevante bestanden gecontroleerd en wordt het app-configuratie-exemplaar bijgewerkt met behulp van de connection string opgeslagen als een geheim in de opslag plaats.  Zie [het artikel van github](https://help.github.com/actions/automating-your-workflow-with-github-actions/creating-and-using-encrypted-secrets) over het maken en gebruiken van versleutelde geheimen voor meer informatie over het gebruik van geheimen in github.

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
Met de vorige actie wordt het app-configuratie `appsettings.json` -exemplaar bijgewerkt wanneer het wordt bijgewerkt. Met deze actie wordt een dynamisch label ingevoegd bij elke synchronisatie, zodat elke synchronisatie uniek kan worden geïdentificeerd en het toestaan van code wijzigingen aan configuratie wijzigingen wordt toegewezen.

In de eerste sectie van deze werk stroom wordt aangegeven dat de actie wordt geactiveerd `appsettings.json` *op* een *Push* die naar de *hoofd* vertakking bevat. In het tweede gedeelte wordt een taak uitgevoerd waarmee een uniek label voor de configuratie-update wordt gemaakt op basis van de commit-hash. De taak werkt vervolgens het app-configuratie-exemplaar bij met de nieuwe waarden en het unieke label voor deze update.

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
| formaat | Ja | Bestands indeling van het configuratie bestand.  Geldige indelingen zijn: JSON, YAML, eigenschappen. |
| Verbindings | Ja | Verbindings reeks voor het app-configuratie-exemplaar. De connection string moet worden opgeslagen als een geheim in de GitHub-opslag plaats en alleen de geheime naam moet in de werk stroom worden gebruikt. |
| scheiding | Ja | Scheidings teken dat wordt gebruikt bij het afvlakken van het configuratie bestand naar sleutel-waardeparen.  Geldige waarden zijn:. , ; : - _ __ / |
| beleids | Nee | Het voor voegsel dat moet worden toegevoegd aan het begin van de sleutels. |
| label | Nee | Label dat wordt gebruikt bij het instellen van sleutel-waardeparen. Als u geen waarde opgeeft, wordt een NULL-label gebruikt. |
| beperking | Nee | Een Booleaanse waarde die bepaalt of de strikte modus is ingeschakeld. De standaardwaarde is false. |
| diepga | Nee | Maximale diepte voor het afvlakken van het configuratie bestand.  Depth moet een positief getal zijn.  De standaard waarde heeft geen maximale diepte. |
| tags | Nee | Hiermee geeft u de tag die is ingesteld op sleutel-waardeparen.  De verwachte indeling is een stringified vorm van een JSON-object van de volgende vorm: {[propertyname: String]: String;} Elke eigenschaps naam-waarde wordt een tag. |

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u de GitHub-actie voor het configureren van de app-configuratie en hoe deze kan worden gebruikt voor het automatiseren van updates voor uw app-configuratie-exemplaar. Ga door naar het volgende [artikel](./concept-app-configuration-event.md)voor meer informatie over de manier waarop Azure-app configuratie reageert op wijzigingen in sleutel-waardeparen.

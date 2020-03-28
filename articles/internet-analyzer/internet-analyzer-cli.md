---
title: Een Internet Analyzer-test maken met CLI | Microsoft Documenten
description: In dit artikel leert u hoe u uw eerste Internet Analyzer-test maakt.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74184269"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Een Internet Analyzer-test maken met CLI (Preview)

Er zijn twee manieren om een Internet Analyzer-bron te maken: met behulp van de [Azure-portal](internet-analyzer-create-test-portal.md) of met CLI. Met deze sectie u een nieuwe Azure Internet Analyzer-bron maken met behulp van onze CLI-ervaring. 


> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

De openbare preview is wereldwijd beschikbaar voor gebruik; gegevensopslag is echter beperkt tot *US West 2* tijdens de preview.

## <a name="object-model"></a>Objectmodel
De CLI van Internet Analyzer legt de volgende typen bronnen bloot:
* **Tests** - Een test vergelijkt de prestaties van de eindgebruiker van twee interneteindpunten (A en B) in de loop van de tijd.
* **Profielen** - Tests worden gemaakt onder een Internet Analyzer-profiel. Profielen maken het mogelijk om gerelateerde tests te groeperen; een enkel profiel kan een of meer tests bevatten.
* **Vooraf geconfigureerde eindpunten** - We hebben eindpunten ingesteld met verschillende configuraties (regio's, versnellingstechnologieën, enz.). U een van deze vooraf geconfigureerde eindpunten gebruiken in uw tests.
* **Scorecards** - Een scorecard biedt snelle en zinvolle samenvattingen van meetresultaten. Raadpleeg [het interpreteren van uw scorecard.](internet-analyzer-scorecard.md)
* **Tijdreeks** - Een tijdreeks laat zien hoe een statistiek in de loop van de tijd verandert.

## <a name="profile-and-test-creation"></a>Profiel en het maken van tests
1. Krijg toegang tot internetanalyzer-preview's door de [Azure Internet Analyzer FAQ](internet-analyzer-faq.md) **voorbeeldinstructies te volgen?**
2. [Installeer de Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Voer `login` de opdracht uit om een CLI-sessie te starten:
    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaardbrowser kan openen, wordt dit gedaan en wordt een Azure-aanmeldingspagina geladen.
    Anders opent u een https://aka.ms/devicelogin browserpagina op en voert u de autorisatiecode in die in uw terminal wordt weergegeven.

4. Meldt u zich in de browser aan met uw accountreferenties.

5. Selecteer uw abonnements-id die toegang heeft gekregen tot de openbare preview van Internet Analyzer.

    Nadat u zich hebt aanmeldt, ziet u een lijst met abonnementen die zijn gekoppeld aan uw Azure-account. De abonnementsinformatie `isDefault: true` met is het momenteel geactiveerde abonnement na het inloggen. Als u een ander abonnement wilt selecteren, gebruikt u de opdracht [az-accountset](https://docs.microsoft.com/cli/azure/account#az-account-set) met de abonnements-ID om naar over te schakelen. Zie [Meerdere Azure-abonnementen gebruiken](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)voor meer informatie over abonnementsselectie.

    Er zijn ook manieren om u niet-interactief aan te melden. Deze manieren worden uitvoerig behandeld in [Aanmelden met Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

6. **[Optioneel]** Maak een nieuwe Azure Resource Group:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Installeer de Azure CLI Internet Analyzer-extensie:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Maak een nieuw Internet Analyzer-profiel:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Vermeld alle vooraf geconfigureerde eindpunten die beschikbaar zijn voor het nieuw gemaakte profiel:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Maak een nieuwe test onder het nieuw gemaakte InternetAnalyzer-profiel:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    De bovenstaande opdracht gaat `www.contoso.com` `www.microsoft.com` ervan uit dat beide en zijn hosting van de one-pixel image[(trans.gif)](https://fpc.msedge.net/apc/trans.gif)onder aangepaste paden. Als een objectpad niet expliciet is opgegeven, `/apc/trans.gif` gebruikt Internet Analyzer standaard als objectpad, waar de vooraf geconfigureerde eindpunten de afbeelding van één pixel hosten. Houd er ook rekening mee dat het schema (https/http) niet hoeft te worden opgegeven; Internet Analyzer ondersteunt alleen HTTPS-eindpunten, dus HTTPS wordt verondersteld.

11. De nieuwe test moet worden weergegeven onder het profiel van Internet Analyzer:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Voorbeelduitvoer:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Om metingen te genereren, moet het JavaScript-bestand waarop het **scriptFileUri** van de test wordt genoemd, zijn ingesloten in uw webtoepassing. Specifieke instructies zijn te vinden op de pagina [Embed Internet Analyzer Client.](internet-analyzer-embed-client.md)

13. U de voortgang van de test controleren door de waarde van de status bij te houden:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. U de verzamelde resultaten van de test inspecteren door er tijdreeksen of scorecards voor te genereren:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Volgende stappen

* Blader door de [CLI-verwijzing naar Internet Analyzer](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest) voor de volledige lijst met ondersteunde opdrachten en gebruiksvoorbeelden.
* Lees de [veelgestelde vragen over internetanalyse.](internet-analyzer-faq.md)
* Meer informatie over het insluiten van de [Internet Analyzer-client](internet-analyzer-embed-client.md) en het maken van een [aangepast eindpunt](internet-analyzer-custom-endpoint.md). 

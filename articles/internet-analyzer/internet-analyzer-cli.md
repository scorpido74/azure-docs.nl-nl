---
title: Een Internet Analyzer-test maken met behulp van CLI | Microsoft Docs
description: In dit artikel leert u hoe u uw eerste Internet Analyzer-test kunt maken.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: d474442086e2a114f26df279ab2682cd7628a5f5
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184269"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>Een Internet Analyzer-test maken met CLI (preview)

Er zijn twee manieren om een Internet Analyzer-resource te maken: met behulp van de [Azure Portal](internet-analyzer-create-test-portal.md) of cli. Deze sectie helpt u bij het maken van een nieuwe Azure Internet Analyzer-resource met onze CLI-ervaring. 


> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

De open bare preview is beschikbaar voor gebruik wereld wijd. gegevens opslag is echter beperkt tot *US West 2* tijdens de preview-versie.

## <a name="object-model"></a>Object model
De Internet Analyzer CLI maakt de volgende typen resources beschikbaar:
* **Testen** : met een test vergelijkt u de prestaties van de eind gebruiker van twee Internet-eind punten (A en B) in de loop van de tijd.
* **Profielen** : tests worden gemaakt onder een Internet Analyzer-profiel. Met profielen kunnen gerelateerde testen worden gegroepeerd; Eén profiel kan een of meer tests bevatten.
* **Vooraf geconfigureerde eind punten** : we hebben eind punten ingesteld met verschillende configuraties (regio's, versnellings technologieën, enzovoort). U kunt elk van deze vooraf geconfigureerde eind punten in uw tests gebruiken.
* **Score cards** : een score card biedt snelle en zinvolle samen vattingen van meet resultaten. Raadpleeg [uw Score Card interpreteren](internet-analyzer-scorecard.md).
* **Tijd reeks** : een tijd reeks laat zien hoe een metriek na verloop van tijd verandert.

## <a name="profile-and-test-creation"></a>Profiel en het maken van testen
1. Toegang tot internet Analyzer preview verkrijgen door de **Hoe kan ik deel te nemen aan de preview-versie** van de [Veelgestelde vragen over Azure Internet Analyzer](internet-analyzer-faq.md).
2. [Installeer de Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Voer de `login` opdracht uit om een CLI-sessie te starten:
    ```azurecli-interactive
    az login
    ```

    Als de CLI uw standaard browser kan openen, wordt dit gedaan en wordt er een Azure-aanmeldings pagina geladen.
    Als dat niet het geval is, opent u een browser pagina op https://aka.ms/devicelogin en voert u de autorisatie code in die wordt weer gegeven in uw Terminal.

4. Meldt u zich in de browser aan met uw accountreferenties.

5. Selecteer uw abonnements-ID waaraan toegang tot de open bare preview van Internet Analyzer is verleend.

    Nadat u zich hebt aangemeld, ziet u een lijst met abonnementen die zijn gekoppeld aan uw Azure-account. De abonnements gegevens met `isDefault: true` zijn het abonnement dat momenteel is geactiveerd na het aanmelden. Als u een ander abonnement wilt selecteren, gebruikt u de opdracht [AZ account set](https://docs.microsoft.com/cli/azure/account#az-account-set) met de abonnements-id om over te scha kelen. Zie [meerdere Azure-abonnementen gebruiken](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)voor meer informatie over het selecteren van abonnementen.

    Er zijn ook manieren om u niet-interactief aan te melden. Deze manieren worden uitvoerig behandeld in [Aanmelden met Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

6. **[Optioneel]** Een nieuwe Azure-resource groep maken:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Installeer de Azure CLI Internet Analyzer-extensie:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Een nieuw Internet Analyzer-profiel maken:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Alle vooraf geconfigureerde eind punten weer geven die beschikbaar zijn voor het zojuist gemaakte profiel:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Maak een nieuwe test onder het zojuist gemaakte InternetAnalyzer-profiel:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    In de bovenstaande opdracht wordt ervan uitgegaan dat zowel `www.contoso.com` als `www.microsoft.com` als host fungeert voor de afbeelding van één pixel ([trans. gif](https://fpc.msedge.net/apc/trans.gif)) onder Aangepaste paden. Als een objectpad niet expliciet wordt opgegeven, gebruikt Internet Analyzer standaard `/apc/trans.gif` als het objectpad. Dit is de locatie waar de vooraf geconfigureerde eind punten de afbeelding met één pixel hosten. Houd er ook rekening mee dat het schema (https/http) niet hoeft te worden opgegeven. Internet-analyse ondersteunt alleen HTTPS-eind punten, dus HTTPS wordt aangenomen.

11. De nieuwe test moet worden weer gegeven onder het profiel Internet Analyzer:
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

12. Om te beginnen met het genereren van metingen, moet het Java script-bestand waarnaar wordt geverwijst door de **scriptFileUri** van de test, worden inge sloten in uw webtoepassing. Specifieke instructies vindt u op de pagina [Internet Analyzer insluiten-client](internet-analyzer-embed-client.md) .

13. U kunt de voortgang van de test controleren door de waarde van de ' status ' bij te houden:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. U kunt de verzamelde resultaten van de test controleren door tijds Erie of Score cards te genereren:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Volgende stappen

* Blader door de [Internet Analyzer cli-referentie](https://docs.microsoft.com/cli/azure/ext/internet-analyzer/internet-analyzer?view=azure-cli-latest) voor de volledige lijst met ondersteunde opdrachten en gebruiks voorbeelden.
* Lees de [Veelgestelde vragen over Internet Analyzer](internet-analyzer-faq.md).
* Meer informatie over het insluiten van de [Internet Analyzer-client](internet-analyzer-embed-client.md) en het maken van een [aangepast eind punt](internet-analyzer-custom-endpoint.md). 

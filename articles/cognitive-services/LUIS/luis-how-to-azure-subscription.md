---
title: Ontwerpen en runtime-sleutels gebruiken-LUIS
titleSuffix: Azure Cognitive Services
description: Wanneer u Language Understanding (LUIS) voor het eerst gebruikt, hoeft u geen ontwerp sleutel te maken. Wanneer u van plan bent om de app te publiceren, moet u het runtime-eind punt gebruiken om de runtime sleutel te maken en toe te wijzen aan de app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: acda549ffc03679de43b4e5956e65ccada766c15
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819952"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Resource sleutels voor ontwerpen en runtime gebruiken

Het ontwerpen en uitvoeren van runtime-resources bieden verificatie voor uw LUIS-app en Voorspellings eindpunt.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Wanneer u zich aanmeldt bij de LUIS-Portal, kunt u het volgende doen:

* een gratis [proef versie](#trial-key) : het maken van ontwerpen en enkele Voorspellings eindpunt query's.
* een Azure [Luis-ontwerp](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) bron. 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Meld u aan bij de LUIS-Portal en begin met ontwerpen

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai) en ga akkoord met de gebruiks voorwaarden.
1. Start uw LUIS-app door het type van de LUIS-ontwerp sleutel te kiezen die u wilt gebruiken: gratis proef versie of nieuwe Azure LUIS-ontwerp sleutel. 

    ![Een type Language Understanding-ontwerp bron kiezen](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Wanneer u klaar bent met het resource selectie proces, [maakt u een nieuwe app](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Proef sleutel

De proef versie (start) wordt voor u gegeven. Dit wordt gebruikt als uw verificatie sleutel voor het uitvoeren van een query op de prediction endpoint runtime, tot 1000 query's per maand. 

Deze wordt weer gegeven op de pagina **gebruikers instellingen** en op de pagina's **Manage-> Azure-resources** in de Luis-Portal. 

Wanneer u klaar bent om uw Voorspellings eindpunt te publiceren, maakt en wijst u ontwerp-en Voorspellings runtime sleutels toe om de functionaliteit van de start sleutel te vervangen. 

## <a name="create-resources-in-the-azure-portal"></a>Resources maken in de Azure Portal

1. Gebruik [deze koppeling](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) om de Azure portal te openen bij het maken van resources.
1. Selecteer **beide** voor het maken van een ontwerp-en een Voorspellings eindpunt runtime sleutel. 
1. Voer de vereiste gegevens in om de resource te maken en selecteer vervolgens **maken** om het proces te volt ooien.

    ![De Language Standing resource maken](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Naam|Doel|
    |--|--|
    |Resourcenaam| Een aangepaste naam die u kiest, die wordt gebruikt als onderdeel van de URL voor uw ontwerp-en Voorspellings eindpunt query's.|
    |Abonnements naam| het abonnement dat wordt gefactureerd voor de resource.|
    |Resourcegroep| Een aangepaste naam voor de resource groep die u kiest of maakt. Met resource groepen kunt u Azure-resources groeperen voor toegang en beheer in dezelfde regio.|
    |Locatie van ontwerpen|De regio die aan uw model is gekoppeld.|
    |Prijs categorie ontwerpen|De prijs categorie bepaalt de maximale trans actie per seconde en maand.|
    |Runtime-locatie|De regio die is gekoppeld aan de gepubliceerde Voorspellings eindpunt runtime.|
    |Prijs categorie voor runtime|De prijs categorie bepaalt de maximale trans actie per seconde en maand.|

    Zodra beide resources zijn gemaakt, wijst u de resources toe aan de LUIS-Portal.

## <a name="create-resources-in-azure-cli"></a>Resources maken in azure CLI

Gebruik de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om elke resource afzonderlijk te maken. 

Resource `kind`:

* Ontwerpen: `LUIS.Authoring`
* Voor spelling: `LUIS` 

1. Meld u aan bij de Azure CLI:

    ```console
    az login
    ```

    Hiermee opent u een browser waarmee u het juiste account en verificatie kunt selecteren.

1. Maak een **Luis-ontwerp bron**, van de soort `LUIS.Authoring`, met de naam `my-luis-authoring-resource` in de _bestaande_ resource groep met de naam `my-resource-group` voor de `westus`-regio. 

    ```console
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Maak een **Luis-Voorspellings eindpunt bron**, van het type `LUIS`, met de naam `my-luis-prediction-resource` in de _bestaande_ resource groep met de naam `my-resource-group` voor de `westus`-regio. Als u een hogere door voer wilt dan de gratis laag, wijzigt u `F0` in `S0`. Meer informatie over [prijs categorieën en door Voer](luis-boundaries.md#key-limits).

    ```console
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > Deze sleutels worden **niet** gebruikt door de Luis-Portal totdat ze zijn toegewezen in de Luis-Portal op de **Azure-resources voor beheer >** .

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Een ontwerp bron toewijzen in de LUIS-portal voor alle apps

U kunt een ontwerp bron toewijzen voor één app of voor alle apps in LUIS. Met de volgende procedure worden alle apps aan één ontwerp bron toegewezen.

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai).
1. Selecteer in de bovenste navigatie balk helemaal rechts uw gebruikers account en selecteer vervolgens **instellingen**.
1. Selecteer op de pagina **gebruikers instellingen** de optie **ontwerp resource toevoegen** en selecteer vervolgens een bestaande ontwerp bron. Selecteer **Opslaan**. 

## <a name="assign-a-resource-to-an-app"></a>Een resource toewijzen aan een app

Met de volgende procedure kunt u een afzonderlijke resource, een ontwerp of Voorspellings eindpunt runtime toewijzen aan een app.

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer vervolgens een app uit de lijst **mijn apps** .
1. Ga naar de pagina **Manage-> Azure-resources** .

    ![Selecteer de Azure-resources Manage-> in de LUIS-Portal om een resource aan de app toe te wijzen.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecteer het tabblad voor spelling of ontwerp bron en selecteer vervolgens de knop **Voorspellings resource toevoegen** of maken van de resource **toevoegen** . 
1. Selecteer de velden in het formulier om de juiste resource te zoeken en selecteer vervolgens **Opslaan**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Een runtime resource toewijzen zonder de LUIS-portal te gebruiken

Voor Automation-doel einden, zoals een CI/CD-pijp lijn, wilt u mogelijk de toewijzing van een LUIS-runtime resource automatiseren in een LUIS-app. Om dat te doen, moet u de volgende stappen uitvoeren:

1. Een Azure Resource Manager-Token ophalen van deze [website](https://resources.azure.com/api/token?plaintext=true). Dit token verloopt daarom direct te gebruiken. De aanvraag retourneert een Azure Resource Manager-token.

    ![Azure Resource Manager token aanvragen en Azure Resource Manager token ontvangen](./media/luis-manage-keys/get-arm-token.png)

1. Gebruik het token om de LUIS-runtime resources voor meerdere abonnementen aan te vragen, van de [API Get Luis Azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), waartoe uw gebruikers account toegang heeft. 

    Deze bericht-API vereist de volgende instellingen:

    |Header|Waarde|
    |--|--|
    |`Authorization`|De waarde van `Authorization` is `Bearer {token}`. U ziet dat de token waarde moet worden voorafgegaan door het woord `Bearer` en een spatie.| 
    |`Ocp-Apim-Subscription-Key`|Uw ontwerp sleutel.|

    Deze API retourneert een matrix van JSON-objecten van uw LUIS-abonnementen, waaronder de abonnements-ID, resource groep en resource naam, geretourneerd als account naam. Zoek het ene item in de matrix die de LUIS-resource is die u wilt toewijzen aan de LUIS-app. 

1. Wijs het token toe aan de LUIS-resource met de [Luis Azure-accounts toewijzen aan een toepassings](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) -API. 

    Deze bericht-API vereist de volgende instellingen:

    |Type|Instelling|Waarde|
    |--|--|--|
    |Header|`Authorization`|De waarde van `Authorization` is `Bearer {token}`. U ziet dat de token waarde moet worden voorafgegaan door het woord `Bearer` en een spatie.|
    |Header|`Ocp-Apim-Subscription-Key`|Uw ontwerp sleutel.|
    |Header|`Content-type`|`application/json`|
    |String|`appid`|De id van de LUIS-app. 
    |Hoofdtekst||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "ResourceGroup-2",<br>"AccountName": "Luis-uswest-S0-2"}|

    Wanneer deze API is geslaagd, wordt de status 201 gemaakt geretourneerd. 

## <a name="unassign-resource"></a>Toewijzing van resource opheffen

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer vervolgens een app uit de lijst **mijn apps** .
1. Ga naar de pagina **Manage-> Azure-resources** .
1. Selecteer het tabblad voor spelling of ontwerp bron en selecteer vervolgens de knop **resource niet toewijzen** voor de resource. 

Wanneer u de toewijzing van een resource ongedaan maakt, wordt deze niet verwijderd uit Azure. Het is alleen ontkoppeld van LUIS. 

## <a name="reset-authoring-key"></a>Ontwerp sleutel opnieuw instellen

**Voor [gemigreerde](luis-migration-authoring.md) apps voor**het maken van een resource: als uw ontwerp sleutel is aangetast, moet u de sleutel opnieuw instellen in de Azure Portal op de pagina **sleutels** voor die ontwerp bron. 

**Voor apps die nog niet zijn gemigreerd**: de sleutel wordt opnieuw ingesteld voor al uw apps in de Luis-Portal. Als u uw apps via de ontwerp-Api's maakt, moet u de waarde van OCP-APIM-Subscription-Key wijzigen in de nieuwe sleutel.

## <a name="regenerate-azure-key"></a>Azure-sleutel opnieuw genereren

Genereer de Azure-sleutels opnieuw vanuit de Azure Portal op de pagina **sleutels** .

## <a name="delete-account"></a>Account verwijderen

Zie [gegevens opslag en verwijderen](luis-concept-data-storage.md#accounts) voor meer informatie over welke gegevens worden verwijderd wanneer u uw account verwijdert.

## <a name="change-pricing-tier"></a>Prijscategorie wijzigen

1.  Zoek uw LUIS-abonnement in [Azure](https://portal.azure.com). Selecteer het LUIS-abonnement.
    ![uw LUIS-abonnement vinden](./media/luis-usage-tiers/find.png)
1.  Selecteer **prijs categorie** om de beschik bare prijs categorieën te bekijken. 
    prijs categorieën ![weer geven](./media/luis-usage-tiers/subscription.png)
1.  Selecteer de prijs categorie en selecteer **selecteren** om uw wijziging op te slaan. 
    ![uw LUIS wijzigen](./media/luis-usage-tiers/plans.png)
1.  Wanneer de prijs wijziging is voltooid, controleert een pop-upvenster de nieuwe prijs categorie. 
    ![de](./media/luis-usage-tiers/updated.png) van uw LUIS-betalings niveau controleren
1. [Wijs deze eindpunt sleutel toe](#assign-a-resource-to-an-app) op de pagina **publiceren** en gebruik deze in alle eindpunt query's. 

## <a name="viewing-azure-resource-metrics"></a>Metrische gegevens van Azure-resource weer geven

### <a name="viewing-azure-resource-summary-usage"></a>Gebruik van Azure Resource Summary weer geven
U kunt LUIS-gebruiks gegevens weer geven in Azure. Op de **overzichts** pagina ziet u recente samenvattings informatie, inclusief oproepen en fouten. Als u een LUIS-eindpunt aanvraag maakt en de **overzichts pagina**onmiddellijk bekijkt, moet u Maxi maal vijf minuten toestaan voordat het gebruik wordt weer gegeven.

![Samenvattings gebruik weer geven](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Grafieken voor Azure resource usage aanpassen
Metrieken bieden een meer gedetailleerde weer gave van de gegevens.

![Standaard waarden](./media/luis-usage-tiers/metrics-default.png)

U kunt uw metrische grafieken voor tijds periode en metrisch type configureren. 

![Aangepaste metrische gegevens](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Waarschuwing drempel waarde voor totaal trans acties
Als u wilt weten wanneer u een bepaalde transactie drempel hebt bereikt, bijvoorbeeld 10.000 trans acties, kunt u een waarschuwing maken. 

![Standaard waarschuwingen](./media/luis-usage-tiers/alert-default.png)

Voeg een metrische waarschuwing toe voor de totale waarde van **aanroepen** voor een bepaalde tijds periode. Voeg e-mail adressen toe van alle personen die de melding moeten ontvangen. Voeg webhooks toe voor alle systemen die de waarschuwing moeten ontvangen. U kunt ook een logische app uitvoeren wanneer de waarschuwing wordt geactiveerd. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over het gebruik van versies](luis-how-to-manage-versions.md) om de levens cyclus van uw app te beheren.
* Inzicht in de concepten, waaronder de [ontwerp resource](luis-concept-keys.md#authoring-key) en [inzenders](luis-concept-keys.md#contributions-from-other-authors) voor die bron.
* Meer informatie [over het maken](luis-how-to-azure-subscription.md) van ontwerp-en runtime-resources
* Migreren naar de nieuwe [ontwerp bron](luis-migration-authoring.md) 

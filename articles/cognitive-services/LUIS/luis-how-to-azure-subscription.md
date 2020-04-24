---
title: Ontwerpen en runtime-sleutels gebruiken-LUIS
description: Wanneer u Language Understanding (LUIS) voor het eerst gebruikt, hoeft u geen ontwerp sleutel te maken. Wanneer u van plan bent om de app te publiceren, moet u het runtime-eind punt gebruiken om de runtime sleutel te maken en toe te wijzen aan de app.
services: cognitive-services
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: d9235b6ef1c7cddbfbbd36f8382439d781af6d5f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101022"
---
# <a name="create-luis-resources"></a>LUIS-resources maken

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

Wanneer u klaar bent om uw Voorspellings eindpunt te publiceren, [maakt](#create-luis-resources) en [wijst](#assign-a-resource-to-an-app) u ontwerp-en Voorspellings runtime sleutels toe om de functionaliteit van de start sleutel te vervangen.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Resources maken in azure CLI

Gebruik de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om elke resource afzonderlijk te maken.

Resource `kind`:

* Ontwerp`LUIS.Authoring`
* Voorspellings`LUIS`

1. Meld u aan bij de Azure CLI:

    ```azurecli
    az login
    ```

    Hiermee opent u een browser waarmee u het juiste account en verificatie kunt selecteren.

1. Maak een **Luis-ontwerp bron**, van de `LUIS.Authoring`soort, `my-luis-authoring-resource` met de naam in de _bestaande_ resource groep met de naam `my-resource-group` voor de `westus` regio.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Maak een **Luis-Voorspellings eindpunt bron**, van `LUIS`het type `my-luis-prediction-resource` , met de naam in de `my-resource-group` _bestaande_ resource groep met de naam voor de `westus` regio. Als u een hogere door voer wilt dan de gratis laag, `F0` wijzigt `S0`u in. Meer informatie over [prijs categorieën en door Voer](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Deze sleutels worden **niet** gebruikt door de Luis-Portal totdat ze zijn toegewezen in de Luis-Portal op de **Azure-resources voor beheer >**.

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
    |`Authorization`|De waarde van `Authorization` is `Bearer {token}`. U ziet dat de token waarde moet worden voorafgegaan door het `Bearer` woord en een spatie.|
    |`Ocp-Apim-Subscription-Key`|Uw ontwerp sleutel.|

    Deze API retourneert een matrix van JSON-objecten van uw LUIS-abonnementen, waaronder de abonnements-ID, resource groep en resource naam, geretourneerd als account naam. Zoek het ene item in de matrix die de LUIS-resource is die u wilt toewijzen aan de LUIS-app.

1. Wijs het token toe aan de LUIS-resource met de [Luis Azure-accounts toewijzen aan een toepassings](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) -API.

    Deze bericht-API vereist de volgende instellingen:

    |Type|Instelling|Waarde|
    |--|--|--|
    |Header|`Authorization`|De waarde van `Authorization` is `Bearer {token}`. U ziet dat de token waarde moet worden voorafgegaan door het `Bearer` woord en een spatie.|
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
    ![Uw LUIS-abonnement zoeken](./media/luis-usage-tiers/find.png)
1.  Selecteer **prijs categorie** om de beschik bare prijs categorieën te bekijken.
    ![Prijs categorieën weer geven](./media/luis-usage-tiers/subscription.png)
1.  Selecteer de prijs categorie en selecteer **selecteren** om uw wijziging op te slaan.
    ![Uw LUIS-betalings niveau wijzigen](./media/luis-usage-tiers/plans.png)
1.  Wanneer de prijs wijziging is voltooid, controleert een pop-upvenster de nieuwe prijs categorie.
    ![Uw LUIS-betalings niveau controleren](./media/luis-usage-tiers/updated.png)
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

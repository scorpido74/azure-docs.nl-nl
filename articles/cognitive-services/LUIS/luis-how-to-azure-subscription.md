---
title: Schrijven en runtimetoetsen gebruiken - LUIS
titleSuffix: Azure Cognitive Services
description: Wanneer u voor het eerst Taalbegrip (LUIS) gebruikt, hoeft u geen ontwerpsleutel te maken. Wanneer u van plan bent de app te publiceren en vervolgens uw eindpunt runtime wilt gebruiken, moet u de runtime-sleutel aan de app maken en toewijzen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a5140da32eb6fce03131a42bfa90e71e64552431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219971"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Met behulp van ontwerp- en runtimeresourcesleutels

Ontwerp- en runtime-bronnen bieden verificatie naar uw LUIS-app en voorspellingseindpunt.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Wanneer u zich aanmeldt bij de LUIS-portal, u ervoor kiezen om door te gaan met:

* een gratis [testsleutel](#trial-key) - het verstrekken van authoring en een paar voorspelling eindpunt query's.
* een Azure [LUIS-ontwerpbron.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Aanmelden bij LUIS-portal en begin met ontwerpen

1. Meld u aan bij [luis portal](https://www.luis.ai) en ga akkoord met de gebruiksvoorwaarden.
1. Begin uw LUIS-app door te kiezen welk type LUIS-ontwerpsleutel u wilt gebruiken: gratis proefsleutel of nieuwe Azure LUIS-ontwerpsleutel. 

    ![Een type taalinformatie voor het maken van tekst kiezen](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Wanneer u klaar bent met uw resourceselectieproces, [maakt u een nieuwe app](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Proefsleutel

De proefsleutel (start)sleutel is voor u beschikbaar. Het wordt gebruikt als uw verificatiesleutel om de eindtijd van het voorspellingseindpunt op te vragen, tot 1000 query's per maand. 

Het is zichtbaar op zowel de pagina **Gebruikersinstellingen** als de **pagina's Azure-bronnen beheren >** in de LUIS-portal. 

Wanneer u klaar bent om uw voorspellingseindpunt te publiceren, maakt en wijst u toewijzingaanmaak- en voorspellingsruntimesleutels aan om de functionaliteit van de startersleutel te vervangen. 

## <a name="create-resources-in-the-azure-portal"></a>Resources maken in de Azure-portal

1. Gebruik [deze koppeling](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) om de Azure-portal te openen voor het maken van resources.
1. Selecteer **Beide** om een ontwerp- en een voorspellingseindpuntruntijdsleutel te maken. 
1. Voer de gegevens in die nodig zijn om de resource te maken en selecteer **Maken** om het proces te voltooien.

    ![De bron voor taalbegrip maken](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Name|Doel|
    |--|--|
    |Resourcenaam| Een aangepaste naam die u kiest, gebruikt als onderdeel van de URL voor uw ontwerp- en voorspellingseindpuntquery's.|
    |Abonnementsnaam| het abonnement dat wordt gefactureerd voor de resource.|
    |Resourcegroep| Een aangepaste resourcegroepnaam die u kiest of maakt. Met resourcegroepen u Azure-bronnen groeperen voor toegang en beheer in dezelfde regio.|
    |Ontwerplocatie|De regio die aan uw model is gekoppeld.|
    |Prijscategorie voor het ontwerpen|De prijscategorie bepaalt de maximale transactie per seconde en maand.|
    |Runtime-locatie|De regio die is gekoppeld aan de runtime van het gepubliceerde voorspellingseindpunt.|
    |Prijscategorie runtime|De prijscategorie bepaalt de maximale transactie per seconde en maand.|

    Zodra beide resources zijn gemaakt, wijst u de resources in de LUIS-portal toe.

## <a name="create-resources-in-azure-cli"></a>Resources maken in Azure CLI

Gebruik de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om elke resource afzonderlijk te maken. 

Bron `kind`:

* Authoring:`LUIS.Authoring`
* Voorspelling:`LUIS` 

1. Meld u aan bij azure cli:

    ```azurecli
    az login
    ```

    Hiermee wordt een browser geopend waarmee u het juiste account selecteren en verificatie bieden.

1. Maak een **luis-ontwerpbron,** `LUIS.Authoring`in `my-luis-authoring-resource` natura, genoemd `my-resource-group` in `westus` de _bestaande_ resourcegroep die is vernoemd naar de regio. 

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Maak een luis **voorspelling eindpunt** `LUIS`bron `my-luis-prediction-resource` , van soort `my-resource-group` , `westus` genoemd in de _bestaande_ resource groep vernoemd naar de regio. Als u een hogere doorvoer wilt `F0` dan `S0`de vrije laag, gaat u over naar . Meer informatie over [prijsniveaus en doorvoer](luis-boundaries.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > Deze sleutels worden **pas** door de LUIS-portal gebruikt als ze zijn toegewezen in de **LUIS-portal op de Azure-bronnen beheren >.**

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Een ontwerpbron toewijzen in de LUIS-portal voor alle apps

U een ontwerpbron toewijzen voor één app of voor alle apps in LUIS. Met de volgende procedure worden alle apps toegewezen aan één ontwerpbron.

1. Meld u aan bij het [LUIS-portaal](https://www.luis.ai).
1. Selecteer rechtsboven uw gebruikersaccount en selecteer **Instellingen**.
1. Selecteer op de pagina **Gebruikersinstellingen** de optie **Ontwerpbron toevoegen** en selecteer vervolgens een bestaande ontwerpbron. Selecteer **Opslaan**. 

## <a name="assign-a-resource-to-an-app"></a>Een resource toewijzen aan een app

U één resource, het ontwerpen of voorspellen van eindpuntruntime, toewijzen aan een app met de volgende procedure.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai)en selecteer vervolgens een app in de lijst **Mijn apps.**
1. Navigeer naar de pagina **> Azure-bronnen beheren.**

    ![Selecteer de > Azure-resources beheren in de LUIS-portal om een bron aan de app toe te wijzen.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecteer het tabblad Bron voor voorspelling of ontwerpen en selecteer vervolgens de knop **Voorspellingsbron toevoegen** of **Ontwerpbron toevoegen.** 
1. Selecteer de velden in het formulier om de juiste bron te vinden en selecteer **Opslaan**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Runtime-bron toewijzen zonder LUIS-portal te gebruiken

Voor automatiseringsdoeleinden, zoals een CI/CD-pijplijn, u de toewijzing van een LUIS-runtimebron naar een LUIS-app automatiseren. Om dat te doen, moet u de volgende stappen uitvoeren:

1. Ontvang een Azure Resource Manager-token van deze [website](https://resources.azure.com/api/token?plaintext=true). Dit token verloopt dus gebruik het onmiddellijk. De aanvraag retourneert een Azure Resource Manager-token.

    ![Azure Resource Manager-token aanvragen en Azure Resource Manager-token ontvangen](./media/luis-manage-keys/get-arm-token.png)

1. Gebruik het token om de LUIS runtime-resources voor verschillende abonnementen aan te vragen, via de [API voor Azure-accounts ophalen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), waartoe uw gebruikersaccount toegang heeft. 

    Deze POST API vereist de volgende instellingen:

    |Header|Waarde|
    |--|--|
    |`Authorization`|De waarde `Authorization` `Bearer {token}`van is . Merk op dat de tokenwaarde moet `Bearer` worden voorafgegaan door het woord en een spatie.| 
    |`Ocp-Apim-Subscription-Key`|Je ontwerpsleutel.|

    Met deze API wordt een array met JSON-objecten van uw LUIS-abonnementen geretourneerd, waaronder abonnements-ID, resourcegroep en resourcenaam, geretourneerd als accountnaam. Zoek het ene item in de array dat de LUIS-bron is die u aan de LUIS-app moet toewijzen. 

1. Wijs het token toe aan de LUIS-bron met de [Azure-accounts toewijzen aan een](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) toepassings-API. 

    Deze POST API vereist de volgende instellingen:

    |Type|Instelling|Waarde|
    |--|--|--|
    |Header|`Authorization`|De waarde `Authorization` `Bearer {token}`van is . Merk op dat de tokenwaarde moet `Bearer` worden voorafgegaan door het woord en een spatie.|
    |Header|`Ocp-Apim-Subscription-Key`|Je ontwerpsleutel.|
    |Header|`Content-type`|`application/json`|
    |Querystring|`appid`|De id van de LUIS-app. 
    |Hoofdtekst||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroep-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Wanneer deze API succesvol is, wordt een status van 201 geretourneerd. 

## <a name="unassign-resource"></a>Niet-toegewezen resource

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai)en selecteer vervolgens een app in de lijst **Mijn apps.**
1. Navigeer naar de pagina **> Azure-bronnen beheren.**
1. Selecteer het tabblad Bron voor voorspelling of ontwerpen en selecteer vervolgens de knop **Resource ongedaan maken** voor de resource. 

Wanneer u een bron niet meer toewijst, wordt deze niet uit Azure verwijderd. Het is alleen losgekoppeld van LUIS. 

## <a name="reset-authoring-key"></a>Ontwerpsleutel opnieuw instellen

**Voor [het maken van door resources gemigreerde](luis-migration-authoring.md) apps:** als uw ontwerpsleutel is gecompromitteerd, stelt u de sleutel in de Azure-portal op de pagina **Sleutels** opnieuw in voor die ontwerpbron. 

**Voor apps die nog niet zijn gemigreerd:** de sleutel wordt gereset op al uw apps in de LUIS-portal. Als u uw apps maakt via de auteurs-API's, moet u de waarde van Ocp-Apim-Subscription-Key wijzigen in de nieuwe sleutel.

## <a name="regenerate-azure-key"></a>Azure-sleutel regenereren

Regenereert de Azure-sleutels vanuit de Azure-portal op de pagina **Sleutels.**

## <a name="delete-account"></a>Account verwijderen

Zie [Gegevensopslag en -verwijdering](luis-concept-data-storage.md#accounts) voor informatie over welke gegevens worden verwijderd wanneer u uw account verwijdert.

## <a name="change-pricing-tier"></a>Prijscategorie wijzigen

1.  Zoek in [Azure](https://portal.azure.com)uw LUIS-abonnement. Selecteer het LUIS-abonnement.
    ![Uw LUIS-abonnement zoeken](./media/luis-usage-tiers/find.png)
1.  Selecteer **de prijslaag** om de beschikbare prijsniveaus te bekijken. 
    ![Prijsniveaus weergeven](./media/luis-usage-tiers/subscription.png)
1.  Selecteer de prijscategorie en selecteer **Selecteren** om uw wijziging op te slaan. 
    ![Uw LUIS-betalingslaag wijzigen](./media/luis-usage-tiers/plans.png)
1.  Wanneer de prijswijziging is voltooid, controleert een pop-upvenster de nieuwe prijscategorie. 
    ![Uw LUIS-betalingslaag verifiëren](./media/luis-usage-tiers/updated.png)
1. Vergeet niet [deze eindpuntsleutel toe](#assign-a-resource-to-an-app) te wijzen aan de pagina **Publiceren** en deze te gebruiken in alle eindpuntquery's. 

## <a name="viewing-azure-resource-metrics"></a>Azure-bronstatistieken weergeven

### <a name="viewing-azure-resource-summary-usage"></a>Gebruik van Azure-resourceoverzicht weergeven
U LUIS-gebruiksgegevens weergeven in Azure. De **overzichtspagina** toont recente overzichtsinformatie, waaronder oproepen en fouten. Als u een LUIS-eindpuntaanvraag indient, kijkt u onmiddellijk naar de **pagina Overzicht**, zodat het gebruik maximaal vijf minuten kan worden weergegeven.

![Overzichtsgebruik weergeven](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Azure-resourcegebruiksdiagrammen aanpassen
Metrics biedt een meer gedetailleerde weergave van de gegevens.

![Standaardstatistieken](./media/luis-usage-tiers/metrics-default.png)

U uw metrische grafieken configureren voor periode en metrische type. 

![Aangepaste metrische gegevens](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Waarschuwing voor totale transactiedrempel
Als u wilt weten wanneer u een bepaalde transactiedrempel hebt bereikt, bijvoorbeeld 10.000 transacties, u een waarschuwing maken. 

![Standaardwaarschuwingen](./media/luis-usage-tiers/alert-default.png)

Voeg een metrische waarschuwing toe voor de **statistiek totale aanroepen** voor een bepaalde periode. Voeg e-mailadressen toe van alle personen die de waarschuwing moeten ontvangen. Voeg webhooks toe voor alle systemen die de waarschuwing moeten ontvangen. U ook een logische app uitvoeren wanneer de waarschuwing wordt geactiveerd. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het gebruik van versies](luis-how-to-manage-versions.md) om de levenscyclus van uw app te beheren.
* Begrijp de concepten, waaronder de [bron van het ontwerpen](luis-concept-keys.md#authoring-key) en [bijdragers](luis-concept-keys.md#contributions-from-other-authors) op die bron.
* Meer informatie over het maken van [ontwerp-](luis-how-to-azure-subscription.md) en runtime-bronnen
* Migreren naar de nieuwe [bron voor het ontwerpen](luis-migration-authoring.md) 

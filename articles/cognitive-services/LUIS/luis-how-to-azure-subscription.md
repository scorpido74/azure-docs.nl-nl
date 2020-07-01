---
title: Ontwerpen en runtime-sleutels gebruiken-LUIS
description: Wanneer u Language Understanding (LUIS) voor het eerst gebruikt, hoeft u geen ontwerp sleutel te maken. Wanneer u van plan bent om de app te publiceren, moet u het runtime-eind punt gebruiken om de runtime sleutel te maken en toe te wijzen aan de app.
services: cognitive-services
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 5f6d62a63ea5ae0d3e4ca5913d6e7834ba07692a
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560422"
---
# <a name="create-luis-resources"></a>LUIS-resources maken

De runtime bronnen voor het ontwerpen en opvragen van query's bieden authenticatie voor uw LUIS-app en Voorspellings eindpunt.

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>LUIS-resources

Met LUIS kunt u drie typen Azure-resources en één niet-Azure-resource maken:

|Sleutel|Doel|Cognitieve service`kind`|Cognitieve service`type`|
|--|--|--|--|
|Sleutel ontwerpen|Toegang tot en het beheren van gegevens van toepassingen met ontwerpen, training, publicatie en testen. Maak een LUIS-ontwerp sleutel als u van plan bent om LUIS-apps programmatisch te ontwerpen.<br><br>Het doel van de `LUIS.Authoring` sleutel is om het volgende toe te staan:<br>* Language Understanding-apps en-modellen, inclusief training en publicatie, programmatisch beheren<br> * beheer machtigingen voor de ontwerp bron door personen toe te wijzen aan [de rol Inzender](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|Query Voorspellings sleutel| Query's voor Voorspellings eindpunt aanvragen. Maak een LUIS-Voorspellings sleutel voordat u de voor spellingen van de client-app voorspelde aanvragen overschrijdt met de 1.000-aanvragen van de eerste resource. |`LUIS`|`Cognitive Services`|
|[Bron sleutel voor de cognitieve service met meerdere services](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Query Voorspellings eindpunt aanvragen gedeeld met LUIS en andere ondersteunde Cognitive Services.|`CognitiveServices`|`Cognitive Services`|
|Starter|Gratis ontwerpen (zonder op rollen gebaseerd toegangs beheer) via de LUIS-portal of Api's (inclusief Sdk's), gratis 1.000 Voorspellings eindpunt aanvragen per maand via een browser, API of Sdk's|-|Geen Azure-resource|

Wanneer het proces voor het maken van de Azure-resource is voltooid, [wijst u de sleutel](#assign-a-resource-to-an-app) toe aan de app in de Luis-Portal.

Het is belang rijk om LUIS-apps te schrijven in [regio's](luis-reference-regions.md#publishing-regions) waar u wilt publiceren en query's.

## <a name="resource-ownership"></a>Eigendom van resources

Een Azure-resource, zoals een LUIS, is eigendom van het abonnement dat de resource bevat.

Als u het eigendom van een resource wilt overdragen, kan de OE:
* [Eigendom](../../cost-management-billing/manage/billing-subscription-transfer.md) van uw abonnement overdragen
* Exporteer de LUIS-app als een bestand en importeer vervolgens de app op een ander abonnement. Exporteren is beschikbaar op de pagina **mijn apps** in de Luis-Portal.


## <a name="resource-limits"></a>Bronlimieten

### <a name="authoring-key-creation-limits"></a>Limieten voor het maken van sleutels ontwerpen

U kunt Maxi maal tien ontwerp sleutels per regio per abonnement maken.

Zie de [belangrijkste limieten](luis-limits.md#key-limits) en [Azure-regio's](luis-reference-regions.md).

Publicatie regio's verschillen van het ontwerpen van regio's. Zorg ervoor dat u een app maakt in de ontwerp regio die overeenkomt met het publicatie gebied dat u wilt dat uw client toepassing zich bevindt.

### <a name="key-usage-limit-errors"></a>Limieten voor sleutel gebruik

Gebruiks limieten zijn gebaseerd op de prijs categorie.

Als u het quotum voor trans acties per seconde (TPS) overschrijdt, ontvangt u een HTTP 429-fout. Als u het quotum voor trans acties per maand (TPS) overschrijdt, ontvangt u een HTTP 403-fout.


### <a name="reset-authoring-key"></a>Ontwerp sleutel opnieuw instellen

Voor [gemigreerde](luis-migration-authoring.md) apps voor het maken van een resource: als uw ontwerp sleutel is aangetast, moet u de sleutel opnieuw instellen in de Azure Portal op de pagina **sleutels** voor die ontwerp bron.

Voor apps die nog niet zijn gemigreerd: de sleutel wordt opnieuw ingesteld voor al uw apps in de LUIS-Portal. Als u uw apps via de ontwerp-Api's maakt, moet u de waarde van OCP-APIM-Subscription-Key wijzigen in de nieuwe sleutel.

### <a name="regenerate-azure-key"></a>Azure-sleutel opnieuw genereren

Genereer de Azure-sleutels opnieuw vanuit de Azure Portal op de pagina **sleutels** .


## <a name="app-ownership-access-and-security"></a>Eigendom van apps, toegang en beveiliging

Een app wordt gedefinieerd door de Azure-resources die worden bepaald door het abonnement van de eigenaar.

U kunt uw LUIS-app verplaatsen. Gebruik de volgende documentatie bronnen in de Azure Portal of Azure CLI:

* [App verplaatsen tussen LUIS-ontwerp resources](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Resource verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Resource verplaatsen binnen hetzelfde abonnement of tussen abonnementen](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Bijdragen van andere auteurs

Voor [gemigreerde](luis-migration-authoring.md) apps voor het maken van een resource: _inzenders_ worden beheerd in de Azure portal voor de ontwerp bron, met behulp van de **toegangs beheer pagina (IAM)** . Meer informatie [over het toevoegen van een gebruiker](luis-how-to-collaborate.md)met behulp van het e-mail adres van de samen werker en de rol _Inzender_ .

Voor apps die nog niet zijn gemigreerd: _alle mede_ werkers worden beheerd in de Luis **>-** portal via de pagina samen werkers beheren.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Query Voorspellings toegang voor persoonlijke en open bare apps

Voor een **persoonlijke** app is de functie voor het uitvoeren van een query Voorspellings toegang beschikbaar voor eigen aren en inzenders. Voor een **open bare** app is runtime toegang beschikbaar voor iedereen met hun eigen Azure [cognitieve service](../cognitive-services-apis-create-account.md) of [Luis](#create-resources-in-the-azure-portal) runtime-resource, en heeft de id van de open bare app.

Er is momenteel geen catalogus met open bare apps.

### <a name="authoring-permissions-and-access"></a>Machtigingen en toegang ontwerpen
Toegang tot de app vanuit de [Luis](luis-reference-regions.md#luis-website) -portal of de [ontwerp-api's](https://go.microsoft.com/fwlink/?linkid=2092087) wordt bepaald door de Azure authoring-resource.

De eigenaar en alle mede werkers hebben toegang tot het schrijven van de app.

|Ontwerp toegang bevat|Opmerkingen|
|--|--|
|Eindpunt sleutels toevoegen of verwijderen||
|Versie exporteren||
|Eindpunt logboeken exporteren||
|Versie importeren||
|App openbaar maken|Wanneer een app openbaar is, kan iedereen met een auteur of een eindpunt sleutel een query uitvoeren op de app.|
|Model wijzigen|
|Publiceren|
|Endpoint uitingen voor [actief leren](luis-how-to-review-endpoint-utterances.md) bekijken|
|Trainen|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Runtime-toegang voor voor Spellings eindpunt

Toegang tot het uitvoeren van een query voor het prediction-eind punt wordt bepaald door een instelling op de pagina **toepassings gegevens** in de sectie **beheren** .

|[Privé-eindpunt](#runtime-security-for-private-apps)|[Openbaar eindpunt](#runtime-security-for-public-apps)|
|:--|:--|
|Beschikbaar voor eigenaar en mede werkers|Beschikbaar voor eigenaar, bijdrager en anderen die App-ID kent|

U kunt bepalen wie uw LUIS runtime-sleutel kan zien door deze aan te roepen in een server-naar-server-omgeving. Als u LUIS gebruikt vanuit een bot, is de verbinding tussen de bot en LUIS al beveiligd. Als u het LUIS-eind punt rechtstreeks aanroept, moet u een API aan de server zijde maken (zoals een Azure- [functie](https://azure.microsoft.com/services/functions/)) met beheerde toegang (zoals [Aad](https://azure.microsoft.com/services/active-directory/)). Wanneer de API aan de server zijde wordt aangeroepen en geverifieerd en autorisatie is geverifieerd, geeft u de aanroep door aan LUIS. Hoewel deze strategie niet van toepassing is op man-in-the-middle-aanvallen, verbergt uw sleutel en eind punt-URL van uw gebruikers, kunt u de toegang bijhouden en kunt u de logboek registratie van eindpunt Reacties toevoegen (zoals [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Runtime-beveiliging voor persoonlijke apps

De runtime van een persoonlijke app is alleen beschikbaar voor het volgende:

|Sleutel en gebruiker|Uitleg|
|--|--|
|Bewerkings sleutel van eigenaar| Maxi maal 1000 eindpunt treffers|
|Ontwerp sleutels voor samen werker/Inzender| Maxi maal 1000 eindpunt treffers|
|Een sleutel die is toegewezen aan LUIS door een auteur of samen werker/bijdrager|Gebaseerd op de laag van het sleutel gebruik|

### <a name="runtime-security-for-public-apps"></a>Runtime-beveiliging voor open bare apps

Zodra een app als openbaar is geconfigureerd, kan elke geldige LUIS-ontwerp sleutel of LUIS-eindpunt sleutel _een_ query uitvoeren op uw app, mits de sleutel niet het volledige eindpunt quotum heeft gebruikt.

Een gebruiker die geen eigenaar of bijdrager is, heeft alleen toegang tot de runtime van een open bare app als deze de App-ID heeft opgegeven. LUIS heeft geen open bare _markt_ of een andere manier om te zoeken naar een open bare app.

Een open bare app wordt in alle regio's gepubliceerd, zodat een gebruiker met een op regio gebaseerde LUIS-bron sleutel toegang kan krijgen tot de app in welke regio is gekoppeld aan de resource sleutel.


### <a name="securing-the-query-prediction-endpoint"></a>Het query prediction-eind punt beveiligen

U kunt bepalen wie uw LUIS prediction runtime-eindpunt sleutel kan zien door deze aan te roepen in een server-naar-server-omgeving. Als u LUIS gebruikt vanuit een bot, is de verbinding tussen de bot en LUIS al beveiligd. Als u het LUIS-eind punt rechtstreeks aanroept, moet u een API aan de server zijde maken (zoals een Azure- [functie](https://azure.microsoft.com/services/functions/)) met beheerde toegang (zoals [Aad](https://azure.microsoft.com/services/active-directory/)). Wanneer de API aan de server zijde wordt aangeroepen en verificatie en autorisatie zijn geverifieerd, geeft u de aanroep door aan LUIS. Hoewel deze strategie niet van toepassing is op man-in-the-middle-aanvallen, maakt het uw eind punt van uw gebruikers af, kunt u de toegang bijhouden en kunt u de logboek registratie van eindpunt Reacties toevoegen (zoals [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Meld u aan bij de LUIS-Portal en begin met ontwerpen

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai) en ga akkoord met de gebruiks voorwaarden.
1. Start uw LUIS-app door het type van de LUIS-ontwerp sleutel te kiezen die u wilt gebruiken: gratis proef versie of nieuwe Azure LUIS-ontwerp sleutel.

    ![Een type Language Understanding-ontwerp bron kiezen](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Wanneer u klaar bent met het resource selectie proces, [maakt u een nieuwe app](luis-how-to-start-new-app.md#create-new-app-in-luis).


## <a name="create-azure-resources"></a>Azure-resources maken

<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure Portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Resources maken in azure CLI

Gebruik de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) om elke resource afzonderlijk te maken.

Resource `kind` :

* Ontwerp`LUIS.Authoring`
* Voorspellings`LUIS`

1. Meld u aan bij de Azure CLI:

    ```azurecli
    az login
    ```

    Hiermee opent u een browser waarmee u het juiste account en verificatie kunt selecteren.

1. Maak een **Luis-ontwerp bron**, van `LUIS.Authoring` de soort, met `my-luis-authoring-resource` de naam in de _bestaande_ resource groep met `my-resource-group` de naam voor de `westus` regio.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Maak een **Luis-Voorspellings eindpunt bron**, van `LUIS` het type, met `my-luis-prediction-resource` de naam in de _bestaande_ resource groep met `my-resource-group` de naam voor de `westus` regio. Als u een hogere door voer wilt dan de gratis laag, wijzigt u `F0` in `S0` . Meer informatie over [prijs categorieën en door Voer](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Deze sleutels worden **niet** gebruikt door de Luis-Portal totdat ze zijn toegewezen in de Luis-Portal op de **Azure-resources voor beheer >**.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>Resource toewijzen in de LUIS-Portal

U kunt een ontwerp bron toewijzen voor één app of voor alle apps in LUIS. Met de volgende procedure worden alle apps aan één ontwerp bron toegewezen.

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai).
1. Selecteer in de bovenste navigatie balk helemaal rechts uw gebruikers account en selecteer vervolgens **instellingen**.
1. Selecteer op de pagina **gebruikers instellingen** de optie **ontwerp resource toevoegen** en selecteer vervolgens een bestaande ontwerp bron. Selecteer **Opslaan**.

## <a name="assign-a-resource-to-an-app"></a>Een resource toewijzen aan een app

Met de volgende procedure kunt u een aan een app toewijzen.

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer vervolgens een app uit de lijst **mijn apps** .
1. Ga naar de pagina **Manage-> Azure-resources** .

    ![Selecteer de Azure-resources Manage-> in de LUIS-Portal om een resource aan de app toe te wijzen.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecteer het tabblad voor spelling of ontwerp bron en selecteer vervolgens de knop **Voorspellings resource toevoegen** of maken van de resource **toevoegen** .
1. Selecteer de velden in het formulier om de juiste resource te zoeken en selecteer vervolgens **Opslaan**.

### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>Runtime-resource voor voor spelling van query's toewijzen zonder gebruik te maken van de LUIS-Portal

Voor Automation-doel einden, zoals een CI/CD-pijp lijn, wilt u mogelijk de toewijzing van een LUIS-runtime resource automatiseren in een LUIS-app. Om dat te doen, moet u de volgende stappen uitvoeren:

1. Een Azure Resource Manager-Token ophalen van deze [website](https://resources.azure.com/api/token?plaintext=true). Dit token verloopt daarom direct te gebruiken. De aanvraag retourneert een Azure Resource Manager-token.

    ![Azure Resource Manager token aanvragen en Azure Resource Manager token ontvangen](./media/luis-manage-keys/get-arm-token.png)

1. Gebruik het token om de LUIS-runtime resources voor meerdere abonnementen aan te vragen, van de [API Get Luis Azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), waartoe uw gebruikers account toegang heeft.

    Deze bericht-API vereist de volgende instellingen:

    |Koptekst|Waarde|
    |--|--|
    |`Authorization`|De waarde van `Authorization` is `Bearer {token}` . U ziet dat de token waarde moet worden voorafgegaan door het woord `Bearer` en een spatie.|
    |`Ocp-Apim-Subscription-Key`|Uw ontwerp sleutel.|

    Deze API retourneert een matrix van JSON-objecten van uw LUIS-abonnementen, waaronder de abonnements-ID, resource groep en resource naam, geretourneerd als account naam. Zoek het ene item in de matrix die de LUIS-resource is die u wilt toewijzen aan de LUIS-app.

1. Wijs het token toe aan de LUIS-resource met de [Luis Azure-accounts toewijzen aan een toepassings](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) -API.

    Deze bericht-API vereist de volgende instellingen:

    |Type|Instelling|Waarde|
    |--|--|--|
    |Koptekst|`Authorization`|De waarde van `Authorization` is `Bearer {token}` . U ziet dat de token waarde moet worden voorafgegaan door het woord `Bearer` en een spatie.|
    |Koptekst|`Ocp-Apim-Subscription-Key`|Uw ontwerp sleutel.|
    |Koptekst|`Content-type`|`application/json`|
    |String|`appid`|De id van de LUIS-app.
    |Hoofdtekst||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "ResourceGroup-2",<br>"AccountName": "Luis-uswest-S0-2"}|

    Wanneer deze API is geslaagd, wordt de status 201 gemaakt geretourneerd.

## <a name="unassign-resource"></a>Toewijzing van resource opheffen

1. Meld u aan bij de [Luis-Portal](https://www.luis.ai)en selecteer vervolgens een app uit de lijst **mijn apps** .
1. Ga naar de pagina **Manage-> Azure-resources** .
1. Selecteer het tabblad voor spelling of ontwerp bron en selecteer vervolgens de knop **resource niet toewijzen** voor de resource.

Wanneer u de toewijzing van een resource ongedaan maakt, wordt deze niet verwijderd uit Azure. Het is alleen ontkoppeld van LUIS.


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
* Migreren naar de nieuwe [ontwerp bron](luis-migration-authoring.md)

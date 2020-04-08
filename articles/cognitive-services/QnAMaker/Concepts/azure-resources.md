---
title: Azure-bronnen - QnA Maker
description: QnA Maker gebruikt verschillende Azure-bronnen, elk met een ander doel. Als u begrijpt hoe ze afzonderlijk worden gebruikt, u de juiste prijscategorie plannen en selecteren of weten wanneer u uw prijscategorie moet wijzigen. Als u begrijpt hoe ze in combinatie worden gebruikt, u problemen vinden en oplossen wanneer ze zich voordoen.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 1bd491ecbd878cb7bb05a7eaa5712c75653f2cba
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804296"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-bronnen voor QnA Maker

QnA Maker gebruikt verschillende Azure-bronnen, elk met een ander doel. Als u begrijpt hoe ze afzonderlijk worden gebruikt, u de juiste prijscategorie plannen en selecteren of weten wanneer u uw prijscategorie moet wijzigen. Als u begrijpt hoe ze _in combinatie_ worden gebruikt, u problemen vinden en oplossen wanneer ze zich voordoen.

## <a name="resource-planning"></a>Resourceplanning

Wanneer u voor het eerst een QnA Maker-kennisbank ontwikkelt, is het in de prototypefase gebruikelijk om één QnA Maker-bron te hebben voor zowel testen als productie.

Wanneer u zich in de ontwikkelingsfase van het project bevindt, moet u rekening houden met:

* hoeveel talen uw knowledge base-systeem zal bevatten
* hoeveel regio's u uw kennisbank nodig hebt om beschikbaar te zijn in/uit
* hoeveel documenten in elk domein uw systeem zal bevatten

Plan om één QnA Maker-bron te hebben die alle kennisbases heeft die dezelfde taal, dezelfde regio en dezelfde combinatie van onderwerpdomeinen hebben.

## <a name="pricing-tier-considerations"></a>Overwegingen van prijsniveaus

Meestal zijn er drie parameters die u moet overwegen:

* **De doorvoer die u nodig hebt van de service:**
    * Selecteer het juiste [app-abonnement](https://azure.microsoft.com/pricing/details/app-service/plans/) voor uw App-service op basis van uw behoeften. U de app [op-](https://docs.microsoft.com/azure/app-service/manage-scale-up) of neerschalen.
    * Dit moet ook van invloed zijn op uw Azure **Cognitive Search** SKU-selectie, zie meer details [hier](https://docs.microsoft.com/azure/search/search-sku-tier). Bovendien moet u mogelijk de [capaciteit](../../../search/search-capacity-planning.md) voor cognitief zoeken aanpassen met replica's.

* **Grootte en het aantal kennisbases:** kies de juiste [Azure search SKU](https://azure.microsoft.com/pricing/details/search/) voor uw scenario. Meestal bepaalt u het aantal kennisbases dat u nodig hebt op basis van het aantal verschillende onderwerpdomeinen. Zodra onderwerp domein (voor een enkele taal) moet worden in een kennisbank.

    U N-1-kennisbases publiceren in een bepaalde laag, waarbij N de maximale indexen is die in de laag zijn toegestaan. Controleer ook de maximale grootte en het aantal toegestane documenten per laag.

    Als uw laag bijvoorbeeld 15 toegestane indexen heeft, u 14 kennisbanken publiceren (1 index per gepubliceerde kennisbank). De vijftiende index wordt gebruikt voor alle kennisbanken voor authoring en testing.

* **Aantal documenten als bronnen**: De gratis SKU van de Managementteamservice van QnA Maker beperkt het aantal documenten dat u via de portal en de API's beheren tot 3 (van elk 1 MB). De standaard SKU heeft geen beperkingen op het aantal documenten dat u beheren. Zie meer details [hier](https://aka.ms/qnamaker-pricing).

De volgende tabel geeft u een aantal richtlijnen op hoog niveau.

|                        | QnA Maker Management | App Service | Azure Cognitive Search | Beperkingen                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experiment        | Gratis SKU             | Gratis laag   | Gratis laag    | Tot 2 KB's publiceren, 50 MB groot  |
| Dev/Testomgeving   | Standaard SKU         | Gedeeld      | Basic        | Tot 14 GB publiceren, 2 GB groot    |
| Productieomgeving | Standaard SKU         | Basic       | Standard     | Tot 49 GB publiceren, 25 GB groot |

## <a name="when-to-change-a-pricing-tier"></a>Wanneer wijzigt u een prijscategorie?

|Upgraden|Reden|
|--|--|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker management SKU|U wilt meer QnA-paren of documentbronnen in uw kennisbank hebben.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU en controleer de categorie Cognitief zoeken en [maak replica's voor cognitief zoeken](../../../search/search-capacity-planning.md)|Uw kennisbank moet meer aanvragen van uw client-app, zoals een chatbot, verwerken.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search-service|Je bent van plan om veel kennisbanken te hebben.|

Ontvang de nieuwste runtime-updates door [uw App-service bij te werken in de Azure-portal.](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)

## <a name="resource-naming-considerations"></a>Overwegingen voor het benoemen van resources

De resourcenaam voor de QnA `qna-westus-f0-b`Maker-bron, zoals , wordt ook gebruikt om de andere bronnen een naam te geven.

Met het venster Azure-portalmaken u een QnA Maker-bron maken en de prijsniveaus voor de andere resources selecteren.

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van Azure-portal voor het maken van QnA Maker-bronnen](../media/concept-azure-resource/create-blade.png)

Nadat de resources zijn gemaakt, hebben ze dezelfde naam, met uitzondering van de optionele Application Insights-bron, die tekens aan de naam plaatst.

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van azure-portalbronvermelding](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Maak een nieuwe resourcegroep wanneer u een QnA Maker-bron maakt. Hiermee u alle resources zien die zijn gekoppeld aan de QnA Maker-bron wanneer u zoekt op resourcegroep.

> [!TIP]
> Gebruik een naamgevingsconventie om prijsniveaus aan te geven binnen de naam van de resource of de resourcegroep. Wanneer u fouten ontvangt door een nieuwe kennisbank te maken of nieuwe documenten toe te voegen, is de prijscategorie Voor cognitief zoeken een veelvoorkomend probleem.

## <a name="resource-purposes"></a>Resourcedoeleinden

Elke Azure-bron die met QnA Maker is gemaakt, heeft een specifiek doel:

* QnA Maker-bron
* Bron voor cognitief zoeken
* App Service
* App-abonnementsservice
* Application Insights-service


### <a name="cognitive-search-resource"></a>Bron voor cognitief zoeken

De [resource Cognitive Search](../../../search/index.yml) is gewend aan:

* De QnA-paren opslaan
* Geef de eerste rangschikking (ranker #1) van de QnA-paren op tijdens runtime

#### <a name="index-usage"></a>Indexgebruik

De resource houdt één index bij om als testindex te fungeren en de overige indexen correleren met één gepubliceerde kennisbasis elk.

Een bron geprijsd om 15 indexen te houden, zal 14 gepubliceerde kennisbanken bevatten, en een index wordt gebruikt voor het testen van alle kennisbases. Deze testindex wordt verdeeld door knowledge base, zodat een query met behulp van het interactieve testvenster de testindex gebruikt, maar alleen resultaten retourneert van de specifieke partitie die is gekoppeld aan de specifieke kennisbank.

#### <a name="language-usage"></a>Taalgebruik

De eerste kennisbasis die is gemaakt in de QnA Maker-bron wordt gebruikt om de _enkele_ taalset voor de resource Cognitive Search en al zijn indexen te bepalen. U slechts _één taalset_ hebben voor een QnA Maker-service.

### <a name="qna-maker-resource"></a>QnA Maker-bron

De QnA Maker-bron biedt toegang tot de api's voor het ontwerpen en publiceren, evenals de op de tweede rangschikkingslaag (ranker #2) gebaseerde natuurlijke taalverwerking (NLP) van de QnA-paren tijdens runtime.

De tweede rangschikking past intelligente filters toe die metadata en follow-upprompts kunnen bevatten.

#### <a name="qna-maker-resource-configuration-settings"></a>Configuratie-instellingen voor QnA Maker-bronnen

Wanneer u een nieuwe kennisbasis maakt in de [QnA Maker-portal,](https://qnamaker.ai)is de **taalinstelling** de enige instelling die wordt toegepast op resourceniveau. U selecteert de taal wanneer u de eerste kennisbasis voor de resource maakt.

### <a name="app-service-and-app-service-plan"></a>App-service- en App-serviceplan

De [App-service](../../../app-service/index.yml) wordt door uw clientapplicatie gebruikt om toegang te krijgen tot de gepubliceerde kennisbanken via het runtime-eindpunt.

Om de gepubliceerde kennisbank op te vragen, gebruiken alle gepubliceerde kennisbanken hetzelfde URL-eindpunt, maar geven ze de **knowledge base-ID** op binnen de route.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) wordt gebruikt om chatlogs en telemetrie te verzamelen. Bekijk de algemene [Kusto-query's](../how-to/get-analytics-knowledge-base.md) voor informatie over uw service.

## <a name="share-services-with-qna-maker"></a>Diensten delen met QnA Maker

QnA Maker maakt verschillende Azure-bronnen. Gebruik de volgende tabel om te begrijpen wat u wel en niet delen om het beheer te verminderen en te profiteren van kostendeling:

|Service|Delen|Reden|
|--|--|--|
|Cognitive Services|X|Niet mogelijk door het ontwerp|
|App Service-plan|✔|Vaste schijfruimte die is toegewezen voor een App Service-abonnement. Als andere apps die hetzelfde App Service-abonnement delen aanzienlijke schijfruimte gebruiken, worden problemen ondervonden bij de qnAMaker-appservice.|
|App Service|X|Niet mogelijk door het ontwerp|
|Application Insights|✔|Kan worden gedeeld|
|Zoekservice|✔|1. `testkb` is een gereserveerde naam voor de QnAMaker-dienst; het kan niet worden gebruikt door anderen.<br>2. Synoniem kaart `synonym-map` met de naam is gereserveerd voor de QnAMaker service.<br>3. Het aantal gepubliceerde kennisbanken wordt beperkt door de servicelaag zoeken. Als er gratis indexen beschikbaar zijn, kunnen andere services deze gebruiken.|

### <a name="using-a-single-cognitive-search-service"></a>Eén cognitive search-service gebruiken

Als u een QnA-service en de afhankelijkheden ervan (zoals Zoeken) maakt via de portal, wordt er een zoekservice voor u gemaakt en gekoppeld aan de QnA Maker-service. Nadat deze bronnen zijn gemaakt, u de instelling App-service bijwerken om een eerder bestaande zoekservice te gebruiken en de service die u zojuist hebt gemaakt verwijderen.

Meer informatie over [het configureren](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) van QnA Maker om een andere Cognitive Service-bron te gebruiken dan die welke is gemaakt als onderdeel van het qnA Maker-resourcecreatieproces.

## <a name="management-service-region"></a>Beheerserviceregio

De beheerservice van QnA Maker wordt alleen gebruikt voor de QnA Maker-portal en voor de eerste gegevensverwerking. Deze service is alleen beschikbaar in de **regio West-VS.** Er worden geen klantgegevens opgeslagen in deze West US-service.

## <a name="keys-in-qna-maker"></a>Sleutels in QnA Maker

Uw QnA Maker-service behandelt twee soorten sleutels: **het ontwerpen** van sleutels en **queryeindpuntsleutels** die worden gebruikt met de runtime die wordt gehost in de App-service.

Als u op zoek bent naar uw **abonnementssleutel,** [is de terminologie gewijzigd.](#subscription-keys)

Gebruik deze sleutels bij het indienen van aanvragen voor de service via API's.

![Sleutelbeheer](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Locatie|Doel|
|--|--|--|
|Ontwerpsleutel|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Deze sleutels worden gebruikt om toegang te krijgen tot de [QnA Maker management service API's.](https://go.microsoft.com/fwlink/?linkid=2092179) Met deze API's u de vragen en antwoorden in uw kennisbank bewerken en uw kennisbank publiceren. Deze sleutels worden gemaakt wanneer u een nieuwe QnA Maker-service maakt.<br><br>Zoek deze sleutels op de resource **Cognitive Services** op de pagina **Sleutels.**|
|Queryeindpunttoets|[QnA Maker-portal](https://www.qnamaker.ai)|Deze sleutels worden gebruikt om het gepubliceerde eindpunt van de kennisbank op te vragen om een antwoord te krijgen op een gebruikersvraag. U gebruikt dit queryeindpunt meestal in uw chatbot of in de clienttoepassingscode die verbinding maakt met de QnA Maker-service. Deze sleutels worden gemaakt wanneer u uw QnA Maker-kennisbank publiceert.<br><br>Zoek deze toetsen op de pagina **Service-instellingen.** Zoek deze pagina in het menu van de gebruiker rechtsboven op de pagina in het vervolgkeuzemenu.|

### <a name="subscription-keys"></a>Abonnementssleutels

De termen authoring en query endpoint key zijn correctieve termen. De vorige termijn was **abonnementssleutel**. Als u andere documentatie ziet die verwijst naar abonnementssleutels, zijn deze gelijk aan het maken van ontwerp- en queryeindpuntsleutels (gebruikt in de runtime).

U moet weten wat de sleutel is toegang, knowledge base management of knowledge base querying, om te weten welke sleutel je moet vinden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [kennisbank](knowledge-base.md) QnA Maker
* Begrijp een [kennisbasis levenscyclus](development-lifecycle-knowledge-base.md)
* Limieten voor service en kennisbasis [beoordelen](../limits.md)


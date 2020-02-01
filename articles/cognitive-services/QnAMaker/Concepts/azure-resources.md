---
title: Azure-resources-QnA Maker
description: QnA Maker maakt gebruik van verschillende Azure-bronnen, elk met een ander doel. Als u begrijpt hoe deze afzonderlijk worden gebruikt, kunt u de juiste prijs categorie plannen en selecteren of u moet weten wanneer u de prijs categorie wilt wijzigen. Als u begrijpt hoe ze in combi natie worden gebruikt, kunt u problemen opsporen en oplossen wanneer deze zich voordoen.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 77aced459c7731a2cac432538cfc66ed9ce83f9d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902076"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-resources voor QnA Maker

QnA Maker maakt gebruik van verschillende Azure-bronnen, elk met een ander doel. Als u begrijpt hoe deze afzonderlijk worden gebruikt, kunt u de juiste prijs categorie plannen en selecteren of u moet weten wanneer u de prijs categorie wilt wijzigen. Als u begrijpt hoe ze _in combi natie_ worden gebruikt, kunt u problemen opsporen en oplossen wanneer deze zich voordoen.

## <a name="resource-planning"></a>Resource planning

Wanneer u voor het eerst een QnA Maker Knowledge Base ontwikkelt, is in de prototype fase gebruikelijk dat er één QnA Maker resource is voor testen en productie.

Wanneer u naar de ontwikkelings fase van het project gaat, moet u rekening houden met het volgende:

* Hoeveel talen worden er in uw Knowledge Base-systeem bewaard?
* Hoeveel regio's u nodig hebt om uw Knowledge Base beschikbaar te maken in/van
* Hoeveel documenten in elk domein uw systeem zal bevatten

Plan voor een enkele QnA Maker resource alle kennis grondslagen met dezelfde taal, dezelfde regio en dezelfde combi natie van onderwerps domeinen.

## <a name="pricing-tier-considerations"></a>Overwegingen voor prijs Categorieën

Er zijn doorgaans drie parameters die u moet overwegen:

* **De doorvoer die u nodig hebt van de service**: Selecteer de juiste [App Plan](https://azure.microsoft.com/pricing/details/app-service/plans/) voor uw appservice op basis van uw behoeften. U kunt [omhoog schalen](https://docs.microsoft.com/azure/app-service/manage-scale-up) of u de App. Dit moet ook van invloed zijn op uw selectie van Azure Cognitive Search SKU. Zie [hier](https://docs.microsoft.com/azure/search/search-sku-tier)voor meer informatie.

* **Grootte en het aantal knowledge bases**: Kies de juiste [Azure zoeken SKU](https://azure.microsoft.com/pricing/details/search/) voor uw scenario. Normaal gesp roken bepaalt u het aantal kennis grondslagen dat u nodig hebt op basis van het aantal verschillende onderwerps domeinen. Zodra het onderwerp domein (voor een enkele taal) in één kennis database moet zijn.

    U kunt N-1 knowledge bases publiceren in een bepaalde laag, waarbij N staat voor het maximale aantal indexen toegestaan in de laag. Controleer ook de maximale grootte en het aantal documenten toegestaan per laag.

    Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 Knowledge bases publiceren (1 index per gepubliceerde kennis basis). De vijftiende index wordt gebruikt voor alle kennis grondslagen voor ontwerpen en testen.

* **Aantal documenten als bron**: de gratis SKU van de QnA Maker-management-service beperkt het aantal documenten die u via de portal en de API's naar 3 (van 1 MB grootte, elk beheren kunt). De standaard SKU heeft geen limieten voor het aantal documenten die u kunt beheren. Meer informatie [hier](https://aka.ms/qnamaker-pricing).

De volgende tabel biedt richtlijnen op hoog niveau.

|                        | QnA Maker-Management | App Service | Azure Cognitive Search | Beperkingen                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimenten        | Gratis SKU             | Gratis versie   | Gratis versie    | Maximaal 2 kB's, 50 MB's publiceren  |
| Dev/Test-omgeving   | Standaard SKU         | Gedeeld      | Basic        | Publiceren tot 14 kB's, grootte van 2 GB    |
| Productie-omgeving | Standaard SKU         | Basic       | Standard     | Maximaal 49 kB's, 25 GB grootte publiceren |

## <a name="when-to-change-a-pricing-tier"></a>Wanneer u een prijs categorie wilt wijzigen

|Upgraden|Reden|
|--|--|
|[Upgrade uitvoeren](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker Management-SKU|U wilt meer QnA sets of document bronnen in uw Knowledge Base hebben.|
|[Upgrade uitvoeren](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU|Uw Knowledge Base moet meer aanvragen van uw client-app leveren, zoals een chat-bot.|
|[Upgrade uitvoeren](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search-service|U bent van plan om veel kennis bases te hebben.|

Down load de nieuwste runtime-updates door [uw app service in de Azure portal bij te werken](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Overwegingen voor het benoemen van resources

De resource naam voor de QnA Maker resource, zoals `qna-westus-f0-b`, wordt ook gebruikt voor het benoemen van de andere resources.

In het venster Azure Portal maken kunt u een QnA Maker resource maken en de prijs categorieën voor de andere resources selecteren.

> [!div class="mx-imgBorder"]
> ![scherm opname van Azure Portal voor het maken van QnA Maker resources](../media/concept-azure-resource/create-blade.png)

Nadat de resources zijn gemaakt, hebben ze dezelfde naam, met uitzonde ring van de optionele Application Insights resource, die postpends tekens aan de naam.

> [!div class="mx-imgBorder"]
> Scherm opname van Azure Portal resource vermelding ![](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Maak een nieuwe resource groep wanneer u een QnA Maker resource maakt. Hiermee kunt u alle resources weer geven die zijn gekoppeld aan de QnA Maker resource bij het zoeken op resource groep.

> [!TIP]
> Gebruik een naamgevings Conventie om prijs categorieën aan te geven binnen de naam van de resource of de resource groep. Wanneer er fouten optreden bij het maken van een nieuwe Knowledge Base of het toevoegen van nieuwe documenten, is de Cognitive Search prijs categorie limiet een veelvoorkomend probleem.

## <a name="resource-purposes"></a>Resource doeleinden

Elke Azure-resource die is gemaakt met QnA Maker heeft een specifiek doel:

* QnA Maker resource
* Cognitive Search resource
* App Service
* App-plan service
* Application Insights-service


### <a name="cognitive-search-resource"></a>Cognitive Search resource

De [Cognitive Search](../../../search/index.yml) resource wordt gebruikt voor het volgende:

* De QnA sets opslaan
* Geef de eerste positie (#1) van de QnA sets op tijdens runtime

#### <a name="index-usage"></a>Index gebruik

De resource houdt één index om te fungeren als de test index en de resterende indexen voldoen aan één gepubliceerde kennis database.

Een resource die is geprijsd op 15 indexen, houdt 14 gepubliceerde kennis grondslagen en een index wordt gebruikt voor het testen van de kennis bases. Deze test index is gepartitioneerd door de Knowledge Base zodat een query die gebruikmaakt van het interactieve test deel venster de test index gebruikt, maar alleen resultaten van de specifieke partitie die is gekoppeld aan de specifieke Knowledge Base, worden geretourneerd.

#### <a name="language-usage"></a>Taal gebruik

De eerste Knowledge Base die in de QnA Maker resource wordt gemaakt, wordt gebruikt om de _afzonderlijke_ taal sets voor de Cognitive Search resource en alle bijbehorende indexen te bepalen. U kunt slechts _één taal instellen_ voor een QnA Maker-service.

### <a name="qna-maker-resource"></a>QnA Maker resource

De QnA Maker-resource biedt toegang tot de ontwerp-en publicatie-Api's, evenals de NLP (QnA) op basis van de laag voor natuurlijke taal verwerking (prioriteits-#2) van de verzamelingen die tijdens runtime zijn ingesteld.

Met de tweede classificatie worden intelligente filters toegepast die meta gegevens en opvolgings vragen kunnen bevatten.

#### <a name="qna-maker-resource-configuration-settings"></a>Configuratie-instellingen van QnA Maker bron

Wanneer u een nieuwe Knowledge Base maakt in de [QnA Maker Portal](https://qnamaker.ai), is de **taal** instelling de enige instelling die op het resource niveau wordt toegepast. U selecteert de taal bij het maken van de eerste Knowledge Base voor de resource. 

### <a name="app-service-and-app-service-plan"></a>App service-en app service-plan

De [app service](../../../app-service/index.yml) wordt door uw client toepassing gebruikt om toegang te krijgen tot de gepubliceerde kennis grondslagen via het runtime-eind punt.

Als u een query wilt uitvoeren voor de gepubliceerde kennis database, gebruiken alle gepubliceerde kennis bases hetzelfde URL-eind punt, maar geeft u de **Knowledge Base-id** op in de route.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) wordt gebruikt voor het verzamelen van chat logboeken en telemetrie. Bekijk de algemene [Kusto-query's](../how-to/get-analytics-knowledge-base.md) voor informatie over uw service.

## <a name="share-services-with-qna-maker"></a>Services delen met QnA Maker

QnA Maker maakt verschillende Azure-resources. Gebruik de volgende tabel om te begrijpen wat u wel en niet kunt delen om het beheer te beperken en te profiteren van het delen van kosten:

|Service|Delen|Reden|
|--|--|--|
|Cognitive Services|X|Niet mogelijk door het ontwerp|
|App Service-plan|✔|Er is een vaste schijf ruimte toegewezen voor een App Service plan. Als andere apps die hetzelfde App Service plan delen, veel schijf ruimte gebruiken, ondervinden de QnAMaker App Service-instantie problemen.|
|App Service|X|Niet mogelijk door het ontwerp|
|Application Insights|✔|Kan worden gedeeld|
|Zoekservice|✔|1. `testkb` is een gereserveerde naam voor de QnAMaker-service. het kan niet worden gebruikt door anderen.<br>2. het synoniem van de map met de naam `synonym-map` is gereserveerd voor de QnAMaker-service.<br>3. het aantal gepubliceerde kennis grondslagen is beperkt door de zoek service laag. Als er gratis indexen beschikbaar zijn, kunnen andere services deze gebruiken.|

### <a name="using-a-single-cognitive-search-service"></a>Eén Cognitive Search-service gebruiken

Als u een QnA-service en de bijbehorende afhankelijkheden (zoals zoeken) maakt via de portal, wordt er een zoek service voor u gemaakt en gekoppeld aan de QnA Maker-service. Nadat deze resources zijn gemaakt, kunt u de App Service-instelling bijwerken om een eerder bestaande zoek service te gebruiken en de toepassing verwijderen die u zojuist hebt gemaakt.

Meer informatie [over het configureren](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) van QnA Maker voor het gebruik van een andere cognitieve service resource dan die welke is gemaakt als onderdeel van het QnA maker van het maken van resources.

## <a name="management-service-region"></a>Regio beheer service

De beheer service van QnA Maker wordt alleen gebruikt voor de QnA Maker Portal en voor initiële gegevens verwerking. Deze service is alleen beschikbaar in de regio **VS-West** . Er worden geen klant gegevens opgeslagen in deze service vs-West.

## <a name="keys-in-qna-maker"></a>Sleutels in QnA Maker

Uw QnA Maker-service behandelt twee soorten sleutels: het **ontwerpen van sleutels** en **query-eindpunt sleutels** die worden gebruikt met de runtime die wordt gehost in de app service.

Als u op zoek bent naar uw **abonnements sleutel**, [is de terminologie gewijzigd](#subscription-keys).

Gebruik deze sleutels bij het indienen van aanvragen bij de service via Api's.

![Sleutelbeheer](../media/qnamaker-how-to-key-management/key-management.png)

|Name|Locatie|Doel|
|--|--|--|
|Sleutel ontwerpen|[Azure Portal](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Deze sleutels worden gebruikt voor toegang tot de Api's van de [QnA Maker Management-service](https://go.microsoft.com/fwlink/?linkid=2092179). Met deze Api's kunt u de vragen en antwoorden in uw Knowledge Base bewerken en uw kennis database publiceren. Deze sleutels worden gemaakt wanneer u een nieuwe QnA Maker-service maakt.<br><br>Deze sleutels vindt u op de pagina **sleutels** van de **Cognitive Services** resource.|
|Query-eindpunt sleutel|[QnA Maker Portal](https://www.qnamaker.ai)|Deze sleutels worden gebruikt om een query uit te geven op het gepubliceerde Knowledge Base-eind punt om een antwoord te krijgen op de vraag van een gebruiker. Normaal gesp roken gebruikt u dit query-eind punt in uw chat-bot of in de client toepassings code die verbinding maakt met de QnA Maker-service. Deze sleutels worden gemaakt wanneer u uw QnA Maker Knowledge Base publiceert.<br><br>Deze sleutels vindt u op de pagina **Service-instellingen** . Ga naar deze pagina vanuit het menu van de gebruiker in de rechter bovenhoek van de pagina in de vervolg keuzelijst.|

### <a name="subscription-keys"></a>Abonnementssleutels

De termen ontwerpen en query-eind punt sleutel zijn herstel voorwaarden. De vorige term is **abonnements sleutel**. Als u andere documentatie raadpleegt die verwijst naar abonnements sleutels, zijn deze gelijk aan de ontwerp-en query-eindpunt sleutels (gebruikt in de runtime).

U moet weten welke sleutel u wilt zoeken met beinformatie over de toegang tot de sleutel, het Knowledge Base-beheer of de Knowledge Base-query.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de QnA Maker [Knowledge Base](knowledge-base.md)
* Inzicht in de [levens cyclus van een Knowledge Base](development-lifecycle-knowledge-base.md)
* [Limieten](../limits.md) voor service-en Knowledge bases controleren


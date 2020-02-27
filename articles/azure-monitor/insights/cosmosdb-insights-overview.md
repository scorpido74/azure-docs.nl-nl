---
title: Azure Cosmos DB met Azure Monitor bewaken voor Cosmos DB (preview) | Microsoft Docs
description: In dit artikel wordt de Azure Monitor voor Cosmos DB functie beschreven waarmee Cosmos DB-eigen aars een duidelijk beeld krijgen van de prestaties en het gebruik van problemen met hun CosmosDB-accounts.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: dece5b0bb0508e2d83ee184e71ef0b4364d25ac8
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622980"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Azure Monitor verkennen voor Azure Cosmos DB (preview-versie)

Azure Monitor voor Azure Cosmos DB (preview) biedt een overzicht van de algehele prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB bronnen in een uniforme interactieve ervaring. In dit artikel vindt u meer informatie over de voor delen van deze nieuwe bewakings ervaring en hoe u de ervaring kunt aanpassen en aanpassen aan de unieke behoeften van uw organisatie.   

## <a name="introduction"></a>Inleiding

Voordat u aan de slag gaat, moet u weten hoe de informatie wordt gepresenteerd en gevisualiseerd. 

Het biedt:

* **Op schaal perspectief** van uw Azure Cosmos DB resources in al uw abonnementen op één locatie, met de mogelijkheid om alleen de abonnementen en resources te bepalen die u wilt evalueren.

* **Zoom analyse** van een bepaalde Azure CosmosDB-resource uit om problemen op te lossen of gedetailleerde analyses uit te voeren op categorie gebruik, storingen, capaciteit en bewerkingen. Als u een van deze opties selecteert, krijgt u een gedetailleerde weer gave van de relevante Azure Cosmos DB metrische gegevens.  

* **Aanpasbaar** : deze ervaring is gebaseerd op Azure monitor werkmap sjablonen, zodat u kunt wijzigen welke metrische gegevens worden weer gegeven, wijzigen of instellen van drempel waarden die worden uitgelijnd met uw limieten en vervolgens opslaan in een aangepaste werkmap. Grafieken in de werkmappen kunnen vervolgens worden vastgemaakt aan Azure-Dash boards.  

Voor deze functie hoeft u niets in te scha kelen of te configureren. deze Azure Cosmos DB metrische gegevens worden standaard verzameld.

>[!NOTE]
>Er zijn geen kosten verbonden aan het verkrijgen van toegang tot deze functie en er worden alleen kosten in rekening gebracht voor de Azure Monitor essentiële functies die u configureert of inschakelt, zoals wordt beschreven op de pagina met [Azure monitor prijs informatie](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Metrische gegevens van het bewerkings niveau voor Azure Cosmos DB weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **monitor** in de navigatie balk aan de linkerkant en selecteer **metrische gegevens**.

   ![Deel venster metrische gegevens in Azure Monitor](./media/cosmosdb-insights-overview/monitor-metrics-blade.png)

1. Selecteer in het deel venster **metrieken** > **een resource selecteren** > Kies het vereiste **abonnement**en de **resource groep**. Voor het **bron type**selecteert u **Azure Cosmos DB accounts**, kiest u een van uw bestaande Azure Cosmos-accounts en selecteert u **Toep assen**.

   ![Kies een Cosmos DB account om de metrische gegevens weer te geven](./media/cosmosdb-insights-overview/select-cosmosdb-account.png)

1. Vervolgens kunt u een metriek selecteren in de lijst met beschik bare metrische gegevens. U kunt metrische gegevens selecteren die specifiek zijn voor aanvraag eenheden, opslag, latentie, Beschik baarheid, Cassandra en andere. Zie het artikel [metrische gegevens per categorie](../../cosmos-db/monitor-cosmos-db-reference.md) voor meer informatie over alle beschik bare metrische gegevens in deze lijst. In dit voor beeld selecteren we **aanvraag eenheden** en **Gem** als de aggregatie waarde.

   Naast deze details kunt u ook het **tijds bereik** en de **tijd granulatie** van de metrische gegevens selecteren. U kunt Maxi maal de metrische gegevens weer geven voor de afgelopen 30 dagen.  Nadat u het filter hebt toegepast, wordt een grafiek weer gegeven op basis van het filter. U kunt het gemiddelde aantal verbruikte aanvraag eenheden per minuut voor de geselecteerde periode bekijken.  

   ![Kies een waarde in het Azure Portal](./media/cosmosdb-insights-overview/metric-types.png)

### <a name="add-filters-to-metrics"></a>Filters toevoegen aan metrische gegevens

U kunt ook de metrische gegevens en de grafiek die worden weer gegeven met een specifieke **verzamelingnaam**, **DATABASENAME**, **OperationType**, **Region**en **status**code filteren. Als u de metrische gegevens wilt filteren, selecteert u **filter toevoegen** en kiest u de vereiste eigenschap, zoals **OperationType** , en selecteert u een waarde zoals **query**. In de grafiek worden vervolgens de verbruikte aanvraag eenheden voor de query bewerking voor de geselecteerde periode weer gegeven. De bewerkingen die zijn uitgevoerd via een opgeslagen procedure, worden niet geregistreerd, zodat ze niet beschikbaar zijn onder de metrische waarde voor OperationType.

![Een filter toevoegen om de metrische granulatie te selecteren](./media/cosmosdb-insights-overview/add-metrics-filter.png)

U kunt metrische gegevens groeperen met behulp van de optie **splitsing Toep assen** . U kunt bijvoorbeeld de aanvraag eenheden per bewerkings type groeperen en de grafiek voor alle bewerkingen tegelijk bekijken, zoals wordt weer gegeven in de volgende afbeelding:

![Splitsings filter Toep assen toevoegen](./media/cosmosdb-insights-overview/apply-metrics-splitting.png)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Metrische gegevens over gebruik en prestaties voor Azure Cosmos DB weer geven

Voer de volgende stappen uit om het gebruik en de prestaties van uw opslag accounts in al uw abonnementen weer te geven.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Zoek naar **monitor** en selecteer **monitor**.

    ![Zoekvak met het woord ' Monitor ' en een vervolg keuzelijst met de tekst ' Monitor ' met een snelheid van de schijf stijl](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecteer **Cosmos DB (preview)** .

    ![Scherm opname van Cosmos DB werkmap overzicht](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Overzicht

In **overzicht**bevat de tabel interactieve Azure Cosmos DB metrische gegevens. U kunt de resultaten filteren op basis van de opties die u selecteert in de volgende vervolg keuzelijsten:

* **Abonnementen** : alleen abonnementen met een Azure Cosmos DB resource worden weer gegeven.  

* **Cosmos DB** : u kunt alle, een subset of één Azure Cosmos DB resource selecteren.

* **Tijds bereik** : de laatste 4 uur aan gegevens worden standaard weer gegeven op basis van de bijbehorende selecties.

De tegel item in de vervolg keuzelijst bevat een samen telling van het totale aantal Azure Cosmos DB resources in de geselecteerde abonnementen. Er zijn voorwaardelijke kleurcoderings-of Heatmaps voor kolommen in de werkmap die de metrische gegevens van trans acties rapporteren. De diepste kleur heeft de hoogste waarde en een lichtere kleur op basis van de laagste waarden. 

Als u een vervolg keuze pijl naast een van de Azure Cosmos DB resources selecteert, wordt er een uitsplitsing van de prestatie gegevens op het niveau van de afzonderlijke database container weer gegeven:

![Uitgevouwen vervolg keuzelijst met afzonderlijke database containers en bijbehorende prestatie analyse](./media/cosmosdb-insights-overview/container-view.png)

Als u de naam van de Azure Cosmos DB resource selecteert, wordt het standaard overzicht van het gekoppelde Azure Cosmos DB account weer **gegeven** . 

### <a name="failures"></a>Fouten

Selecteer **fouten** aan de bovenkant van de pagina en het gedeelte **storingen** van de werkmap sjabloon wordt geopend. Hierin worden de totale aanvragen weer gegeven met de distributie van antwoorden waaruit deze aanvragen worden gemaakt:

![Scherm opname van fouten met uitsplitsing op basis van het type HTTP-aanvraag](./media/cosmosdb-insights-overview/failures.png)

| Code      |  Beschrijving       | 
|-----------|:--------------------|
| `200 OK`  | Een van de volgende REST-bewerkingen is geslaagd: </br>-Een resource ophalen. </br> : In een resource plaatsen. </br> -POST op een resource. </br> -POST op een opgeslagen procedure resource om de opgeslagen procedure uit te voeren.|
| `201 Created` | Er is een POST-bewerking voor het maken van een resource geslaagd. |
| `404 Not Found` | De bewerking probeert uit te voeren op een resource die niet meer bestaat. De resource is bijvoorbeeld mogelijk al verwijderd. |

Raadpleeg het artikel over de [Azure Cosmos DB HTTP-status code](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)voor een volledige lijst met status codes.

### <a name="capacity"></a>Capaciteit

Selecteer de optie **capaciteit** boven aan de pagina en het gedeelte **capaciteit** van de werkmap sjabloon wordt geopend. U ziet hoe veel documenten u hebt, uw document groei in de loop van de tijd, het gegevens gebruik en de totale hoeveelheid beschik bare opslag die u hebt verlaten.  Dit kan worden gebruikt om mogelijke problemen met de opslag en het gebruik van gegevens te identificeren.

![Capaciteits werkmap](./media/cosmosdb-insights-overview/capacity.png) 

Net als bij de overzichts werkmap selecteert de vervolg keuzelijst naast een Azure Cosmos DB resource in de kolom **abonnement** een uitsplitsing van de afzonderlijke containers waaruit de data base is opgebouwd.

### <a name="operations"></a>Bewerkingen 

Selecteer **bewerkingen** boven aan de pagina en het gedeelte **bewerkingen** van de werkmap sjabloon wordt geopend. Het biedt u de mogelijkheid om uw aanvragen weer te geven die zijn gesplitst op basis van het type aanvragen. 

In het onderstaande voor beeld ziet u dat `eastus-billingint` voornamelijk Lees aanvragen ontvangt, maar met een klein aantal upsert en aanvragen maakt. Overwegende dat `westeurope-billingint` alleen-lezen is vanuit een aanvraag perspectief, ten minste gedurende de afgelopen vier uur dat de werkmap momenteel is ingesteld op basis van de tijds bereik parameter.

![Operations-werkmap](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Vastmaken, exporteren en uitvouwen

U kunt een van de metrische gedeelten aan een Azure- [dash board](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) vastmaken door het pictogram punaise rechtsboven in de sectie te selecteren.

![Voor beeld van de sectie metrische gegevens van de metriek naar dash board](./media/cosmosdb-insights-overview/pin.png)

Als u uw gegevens wilt exporteren naar de Excel-indeling, selecteert u de pijl-omlaag links van het punaise pictogram.

![Pictogram werkmap exporteren](./media/cosmosdb-insights-overview/export.png)

Als u alle vervolg keuzelijsten in de werkmap wilt uitvouwen of samen vouwen, selecteert u het pictogram uitvouwen links van het pictogram exporteren:

![Pictogram werkmap uitvouwen](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Azure Monitor aanpassen voor Azure Cosmos DB (preview-versie)

Omdat deze ervaring is gebaseerd op Azure Monitor werkmap sjablonen, hebt u de mogelijkheid om > **bewerken** aan te **passen** en een kopie van uw gewijzigde versie in een aangepaste werkmap op te **slaan** . 

![Balk aanpassen](./media/cosmosdb-insights-overview/customize.png)

Werkmappen worden opgeslagen in een resource groep, hetzij in het gedeelte **mijn rapporten** dat persoonlijk is of in de sectie **gedeelde rapporten** dat toegankelijk is voor iedereen die toegang heeft tot de resource groep. Nadat u de aangepaste werkmap hebt opgeslagen, moet u naar de galerie met werkmappen gaan om deze te starten.

![Werkmap galerie starten vanaf de opdracht balk](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Volgende stappen

* [Waarschuwingen voor metrische gegevens](../platform/alerts-metric.md) en [service status meldingen](../../service-health/alerts-activity-log-service-notifications.md) configureren om automatische waarschuwingen in te stellen voor hulp bij het detecteren van problemen.

* Meer informatie over de scenario's werkmappen zijn ontworpen voor ondersteuning, het ontwerpen van nieuwe en het aanpassen van bestaande rapporten en meer door [interactieve rapporten maken met Azure monitor werkmappen](../app/usage-workbooks.md)te controleren.

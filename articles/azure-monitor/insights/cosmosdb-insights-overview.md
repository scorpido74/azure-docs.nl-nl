---
title: Azure Cosmos DB bewaken met Azure Monitor voor Cosmos DB (preview)| Microsoft Documenten
description: In dit artikel wordt de Azure Monitor for Cosmos DB-functie beschreven die eigenaren van Cosmos DB een snel inzicht geeft in de problemen met prestaties en gebruik met hun CosmosDB-accounts.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250680"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Azure-monitor voor Azure Cosmos DB verkennen (voorbeeld)

Azure Monitor for Azure Cosmos DB (preview) biedt een overzicht van de algehele prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB-resources in een uniforme interactieve ervaring. Dit artikel helpt u inzicht te krijgen in de voordelen van deze nieuwe monitoringervaring en hoe u de ervaring aanpassen aan de unieke behoeften van uw organisatie.   

## <a name="introduction"></a>Inleiding

Voordat u in de ervaring duikt, moet u begrijpen hoe het informatie presenteert en visualiseert. 

Het levert:

* **Op schaal perspectief** van uw Azure Cosmos DB resources voor al uw abonnementen op één locatie, met de mogelijkheid om selectief scope aan alleen die abonnementen en middelen die u geïnteresseerd bent in de evaluatie.

* **Boor de analyse** van een bepaalde Azure CosmosDB-bron in om problemen te diagnosticeren of gedetailleerde analyses per categorie uit te voeren - gebruik, storingen, capaciteit en bewerkingen. Als u een van deze opties selecteert, krijgt u een diepgaand overzicht van de relevante Azure Cosmos DB-statistieken.  

* **Aanpasbaar** : deze ervaring is gebouwd bovenop azure monitor-werkmapsjablonen, zodat u wijzigen welke statistieken worden weergegeven, drempels wijzigen of instellen die overeenkomen met uw limieten en vervolgens in een aangepaste werkmap worden opgeslagen. Grafieken in de werkmappen kunnen vervolgens worden vastgemaakt aan Azure-dashboards.  

Deze functie vereist niet dat u iets in- of configureert, deze Azure Cosmos DB-statistieken worden standaard verzameld.

>[!NOTE]
>Er zijn geen kosten verbonden aan toegang tot deze functie en er worden alleen kosten in rekening gebracht voor de essentiële functies van Azure Monitor die u configureert of inschakelt, zoals beschreven op de pagina [met prijsdetails van Azure Monitor.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Gebruiks- en prestatiestatistieken voor Azure Cosmos DB weergeven

Voer de volgende stappen uit om het gebruik en de prestaties van uw opslagaccounts voor al uw abonnementen weer te geven.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Zoek **naar Monitor** en selecteer **Monitor**.

    ![Zoekvak met het woord "Monitor" en een vervolgkeuzelijst met de tekst Services "Monitor" met een snelheidsmeterstijlafbeelding](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecteer **Cosmos DB (voorbeeld)**.

    ![Schermafbeelding van de overzichtswerkmap Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Overzicht

In **Overzicht**wordt in de tabel interactieve Azure Cosmos DB-statistieken weergegeven. U de resultaten filteren op basis van de opties die u selecteert in de volgende vervolgkeuzelijsten:

* **Abonnementen** - alleen abonnementen met een Azure Cosmos DB-bron worden weergegeven.  

* **Cosmos DB** - U alle, een subset of één Azure Cosmos DB-bron selecteren.

* **Time Range** - geeft standaard de laatste 4 uur informatie weer op basis van de bijbehorende selecties.

De tellertegel onder de vervolgkeuzelijsten rolt het totale aantal Azure Cosmos DB-resources op in de geselecteerde abonnementen. Er zijn voorwaardelijke kleurcodering of heatmaps voor kolommen in de werkmap die transactiestatistieken rapporteren. De diepste kleur heeft de hoogste waarde en een lichtere kleur is gebaseerd op de laagste waarden. 

Als u een vervolgkeuzepijl selecteert naast een van de Azure Cosmos DB-resources, wordt een uitsplitsing van de prestatiestatistieken op het niveau van de afzonderlijke databasecontainer weergegeven:

![Uitgebreide vervolgkeuzelijst met afzonderlijke databasecontainers en bijbehorende prestatie-uitsplitsing](./media/cosmosdb-insights-overview/container-view.png)

Als u de blauw gemarkeerde Azure Cosmos DB-bronnaam selecteert, gaat u naar het **standaardoverzicht** voor het bijbehorende Azure Cosmos DB-account. 

### <a name="failures"></a>Fouten

Selecteer **Fouten** boven aan de pagina en het gedeelte **Fouten** van de werkmapsjabloon wordt geopend. Het toont u totale aanvragen met de verdeling van de antwoorden die deel uitmaken van deze verzoeken:

![Schermafbeelding van fouten met uitsplitsing op HTTP-aanvraagtype](./media/cosmosdb-insights-overview/failures.png)

| Code      |  Beschrijving       | 
|-----------|:--------------------|
| `200 OK`  | Een van de volgende REST-activiteiten was succesvol: </br>- GET op een resource. </br> - Zet op een bron. </br> - POST op een bron. </br> - POST op een opgeslagen procedure bron om de opgeslagen procedure uit te voeren.|
| `201 Created` | Een post-bewerking om een resource te maken, is geslaagd. |
| `404 Not Found` | De bewerking probeert te reageren op een resource die niet meer bestaat. De bron is bijvoorbeeld al verwijderd. |

Raadpleeg het artikel over de [statuscode van Azure Cosmos DB HTTP](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)voor een volledige lijst met statuscodes .

### <a name="capacity"></a>Capaciteit

Selecteer **Capaciteit** boven aan de pagina en het **gedeelte Capaciteit** van de werkmapsjabloon wordt geopend. Het toont u hoeveel documenten u hebt, de groei van uw document in de loop van de tijd, het gegevensgebruik en de totale hoeveelheid beschikbare opslagruimte die u nog hebt.  Dit kan worden gebruikt om potentiële problemen met het opslag- en gegevensgebruik te identificeren.

![Capaciteitswerkmap](./media/cosmosdb-insights-overview/capacity.png) 

Net als bij de overzichtswerkmap wordt bij het selecteren van de vervolgkeuzelijst naast een Azure Cosmos DB-bron in de kolom **Abonnement** een uitsplitsing weergegeven van de afzonderlijke containers waaruit de database bestaat.

### <a name="operations"></a>Bewerkingen 

Selecteer **Bewerkingen** boven aan de pagina en het **gedeelte Bewerkingen** van de werkmapsjabloon wordt geopend. Het geeft u de mogelijkheid om uw verzoeken te zien, uitgesplitst naar het type aanvragen dat wordt gedaan. 

In het onderstaande voorbeeld `eastus-billingint` zie je dat het voornamelijk gaat om het ontvangen van leesverzoeken, maar met een klein aantal upsert en het maken van aanvragen. Terwijl `westeurope-billingint` is alleen-lezen vanuit een verzoek perspectief, ten minste in de afgelopen vier uur dat de werkmap is momenteel scoped via de tijdbereik parameter.

![Werkmap voor bewerkingen](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Pinnen, exporteren en uitbreiden

U een van de metrische secties vastmaken aan een [Azure-dashboard](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) door het pushpinpictogram rechtsboven in de sectie te selecteren.

![Voorbeeld van metrische sectie die is vastmaken aan dashboard](./media/cosmosdb-insights-overview/pin.png)

Als u uw gegevens wilt exporteren naar de Excel-indeling, selecteert u het pijl-omlaagpictogram links van het pushpinpictogram.

![Pictogram Werkmap exporteren](./media/cosmosdb-insights-overview/export.png)

Als u alle vervolgkeuzeweergaven in de werkmap wilt uitvouwen of samenvouwen, selecteert u het pictogram Uitvouwen links van het exportpictogram:

![Pictogram Werkmap uitvouwen](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Azure Monitor voor Azure Cosmos DB aanpassen (voorbeeld)

Aangezien deze ervaring is gebouwd bovenop azure monitor-werkmapsjablonen, u een kopie van uw gewijzigde versie **aanpassen** > **en** **opslaan** in een aangepaste werkmap. 

![Balk aanpassen](./media/cosmosdb-insights-overview/customize.png)

Werkmappen worden opgeslagen in een resourcegroep, hetzij in de sectie **Mijn rapporten** die privé voor u is, hetzij in de sectie **Gedeelde rapporten** die toegankelijk is voor iedereen die toegang heeft tot de resourcegroep. Nadat u de aangepaste werkmap hebt opgeslagen, moet u naar de werkmapgalerie gaan om deze te starten.

![Werkmapgalerie starten vanuit opdrachtbalk](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Volgende stappen

* Configureer [metrische waarschuwingen](../platform/alerts-metric.md) en [servicestatusmeldingen](../../service-health/alerts-activity-log-service-notifications.md) om automatische waarschuwingen in te stellen om te helpen bij het detecteren van problemen.

* Lees de scenario's werkmappen zijn ontworpen om te ondersteunen, hoe u nieuwe rapporten maakt en bestaande rapporten aanpast door [interactieve rapporten maken met Azure Monitor-werkmappen](../app/usage-workbooks.md)te bekijken.

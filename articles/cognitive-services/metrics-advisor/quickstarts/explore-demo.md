---
title: De demo van Metrics Advisor verkennen
titleSuffix: Azure Cognitive Services
description: Leer meer over de webinterface van Metrics Advisor met behulp van onze demo
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: fbc73e20b8cc2baa1cc5c5a5b2f674fb1b2dde84
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944622"
---
# <a name="quickstart-explore-the-metrics-advisor-demo-with-example-data"></a>Quickstart: De demo van Metrics Advisor met voorbeeldgegevens verkennen

> [!Note]
> De demo van Metrics Advisor is alleen-lezen en kan niet worden gebruikt voor het onboarden van uw gegevens. Als u de service voor uw gegevens wilt gebruiken, moet u eerst [een Metrics Advisor-resource](web-portal.md) maken.

Gebruik dit artikel om snel de belangrijkste functies in Metrics Advisor te verkennen. Er is een demosite met voorbeeldgegevens en vooraf ingestelde configuraties, zodat u vertrouwd kunt raken met de volledig uitgeruste webportal.

Klik op [deze koppeling](https://aka.ms/MetricsAdvisor/Demo) om naar de demosite te gaan.

## <a name="view-the-available-sample-data"></a>De beschikbare voorbeeldgegevens weergeven

Nadat u zich hebt aangemeld bij de demosite, ziet u de pagina **Gegevensfeed**. een gegevensfeed is een logische groep tijdreeksgegevens, die wordt opgevraagd uit uw gegevensbron. Zie de [woordenlijst](../glossary.md) voor meer informatie over de termen en concepten die worden gebruikt in Metrics Advisor. 

Er worden verschillende gegevensfeeds weergegeven, die worden opgenomen uit verschillende gegevensbronnen, zoals Azure SQL database of Azure Table Storage. Voor elke feed worden iets andere configuratie-instellingen gebruikt om verbinding te maken met de gekoppelde gegevensarchieven.

:::image type="content" source="../media/sample-datafeeds.png" alt-text="Lijst met voorbeeldgegevens" lightbox="../media/sample-datafeeds.png":::

## <a name="explore-the-data-feed-configurations"></a>De configuraties van gegevensfeeds verkennen

Klik op de gegevensfeed *Sample - Cost/Revenue - City/Category* (Sample - Kosten/Opbrengsten - Plaats/Categorie). U ziet verschillende secties met detailgegevens voor de feed:

* De naam van de gegevensfeed en de opnamestatus.
* Een lijst met metrische gegevens die zijn opgevraagd uit de gegevensbron. Bijvoorbeeld *cost* (kosten) en *revenue* (opbrengsten). 
* Waarschuwingsgeschiedenis voor wanneer de gegevensfeed niet meer beschikbaar is. 
* Logboeken van wanneer de gegevensfeed is bijgewerkt.   
* Gegevens en instellingen voor gegevensfeed.

:::image type="content" source="../media/data-feed-view.png" alt-text="Weergave van gegevensfeed" lightbox="../media/data-feed-view.png":::


## <a name="view-time-series-visualizations-and-configurations"></a>Visualisaties en configuraties van tijdreeksen weergeven

Klik in de metriek *cost* in de gegevensfeed *Sample - Cost/Revenue - City/Category*. U ziet de gekoppelde tijdreeks, gesegmenteerd in dimensies, met visualisaties op basis van de historische metrische gegevens. De blauwe band rond de metrische gegevens is het verwachte waardebereik van de Machine Learning-modellen van Metrics Advisor. Punten die buiten deze band vallen, worden gemarkeerd als rode puntjes. Dit zijn gedetecteerde anomalieën. 

:::image type="content" source="../media/series-visualization.png" alt-text="Visualisatie van reeks" lightbox="../media/series-visualization.png":::

De anomaliedetectie kan worden geconfigureerd door de **configuraties voor detectie** aan de linkerkant van de detailpagina voor de metrische gegevens af te stemmen. Er zijn meerdere anomaliedetectiemethoden beschikbaar en u kunt deze combineren. U kunt ook verschillende gevoeligheden, detectierichtingen en andere configuraties proberen. Met de koppeling **Geavanceerde configuratie** onder aan de **configuraties voor detectie** kunt u complexere en aangepaste detectie-instellingen maken, die kunnen worden gebruikt voor groepen of afzonderlijke reeksen. 

U kunt anomaliedetectie ook afstemmen door het detectiealgoritme te voorzien van feedback. Klik in een anomalie en gebruik het venster **Feedback toevoegen** om de anomaliestatus, seizoensgebondenheid en status van het wijzigingspunt te configureren. Deze feedback wordt opgenomen in de detectie voor toekomstige punten.  

Onder aan het venster **Feedback toevoegen** ziet u de koppeling **Naar incidenthub**. Hiermee wordt u naar de pagina voor analyse van het incident geleid en kunt u de hoofdoorzaak van het incident analyseren.  

:::image type="content" source="../media/incident-link.png" alt-text="Koppeling naar incident" lightbox="../media/incident-link.png":::

## <a name="explore-anomaly-detection-results-and-perform-root-cause-analysis"></a>Resultaten van anomaliedetectie verkennen en hoofdoorzaakanalyse uitvoeren

Wanneer u op de koppeling **Naar incidenthub** van een anomalie klikt, wordt een incidentanalysepagina weergegeven met diagnostische inzichten over het incident, zoals de ernst, het aantal betrokken anomalieën en de begin- en eindtijd. In het gedeelte **Hoofdoorzaak** wordt een geautomatiseerd advies weergegeven door de structuur van de incidenten te analyseren, waarbij rekening wordt gehouden met: anomalie, distributie en bijdrage aan bovenliggende anomalieën. Dit kan de hoofdoorzaak van het incident zijn.

De sectie **Diagnostische gegevens** bevat een structuur van het incident en verschillende tabbladen voor het diagnosticeren van het incident.

:::image type="content" source="../media/incident-diagnostic.png" alt-text="Diagnose van het incident" lightbox="../media/incident-diagnostic.png":::

Door de hoofdoorzaak van het incident te achterhalen, kunt u actie ondernemen en het probleem verhelpen voordat de situatie slechter wordt. U kunt ook meer inzichten verkennen door op de andere beschikbare diagnostische functies te klikken. 

## <a name="next-steps"></a>Volgende stappen

- [De webportal gebruiken](web-portal.md)
- [REST-API gebruiken](rest-api.md)
- [Uw gegevensfeeds onboarden](../how-tos/onboard-your-data.md)
    - [Gegevensfeeds beheren](../how-tos/manage-data-feeds.md)
    - [Configuraties voor verschillende gegevens bronnen](../data-feeds-from-different-sources.md)

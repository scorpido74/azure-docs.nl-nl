---
title: Gebruiksaanvraag gegevensfabriek - Productaanbevelingen
description: Meer informatie over een use case die is geïmplementeerd met Behulp van Azure Data Factory samen met andere services.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 6f1523c7-46c3-4b8d-9ed6-b847ae5ec4ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 49ad9be7c70602132436b14234f01a4086d8e1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70139143"
---
# <a name="use-case---product-recommendations"></a>Use Case - Productaanbevelingen
Azure Data Factory is een van de vele services die worden gebruikt om de Cortana Intelligence Suite van oplossingsversnellers te implementeren.  Zie de pagina [Cortana Intelligence Suite](https://www.microsoft.com/cortanaanalytics) voor meer informatie over deze suite. In dit document beschrijven we een veelgebruikte use case die Azure-gebruikers al hebben opgelost en geïmplementeerd met Azure Data Factory en andere Cortana Intelligence-componentservices.

## <a name="scenario"></a>Scenario
Online retailers willen vaak hun klanten verleiden om producten te kopen door hen te presenteren met producten die ze het meest waarschijnlijk geïnteresseerd zijn in, en dus het meest waarschijnlijk om te kopen. Om dit te bereiken, moeten online retailers de online ervaring van hun gebruiker aanpassen door gepersonaliseerde productaanbevelingen voor die specifieke gebruiker te gebruiken. Deze gepersonaliseerde aanbevelingen moeten worden gedaan op basis van hun huidige en historische winkelgedrag gegevens, productinformatie, nieuw geïntroduceerde merken, en product en klant segmentatie gegevens.  Bovendien kunnen ze de gebruikersproductaanbevelingen geven op basis van een analyse van het algehele gebruiksgedrag van al hun gebruikers samen.

Het doel van deze retailers is om te optimaliseren voor gebruikers click-to-sale conversies en hogere omzet te verdienen.  Ze bereiken deze conversie door contextuele, op gedrag gebaseerde productaanbevelingen te leveren op basis van klantinteresses en acties. Voor deze use case gebruiken we online retailers als voorbeeld van bedrijven die willen optimaliseren voor hun klanten. Deze principes zijn echter van toepassing op elk bedrijf dat zijn klanten wil betrekken bij zijn goederen en diensten en de koopervaring van hun klanten wil verbeteren met gepersonaliseerde productaanbevelingen.

## <a name="challenges"></a>Uitdagingen
Er zijn veel uitdagingen waarmee online retailers worden geconfronteerd wanneer ze dit type use case proberen te implementeren. 

Ten eerste moeten gegevens van verschillende groottes en vormen worden ingenomen uit meerdere gegevensbronnen, zowel on-premises als in de cloud. Deze gegevens omvatten productgegevens, historische gegevens over klantgedrag en gebruikersgegevens terwijl de gebruiker door de online winkelsite bladert. 

Ten tweede moeten gepersonaliseerde productaanbevelingen redelijk en nauwkeurig worden berekend en voorspeld. Naast product-, merk- en klantgedrag en browsergegevens moeten online retailers ook feedback van klanten opnemen over eerdere aankopen om rekening te houden met de bepaling van de beste productaanbevelingen voor de gebruiker. 

Ten derde moeten de aanbevelingen onmiddellijk kunnen worden geleverd aan de gebruiker om een naadloze surf- en aankoopervaring te bieden en de meest recente en relevante aanbevelingen te geven. 

Ten slotte moeten detailhandelaren de effectiviteit van hun aanpak meten door de algemene up-sell- en cross-sell-verkoopresultaten van click-to-conversion bij te houden en zich aan te passen aan hun toekomstige aanbevelingen.

## <a name="solution-overview"></a>Overzicht van de oplossing
Deze voorbeeldusecase is opgelost en geïmplementeerd door echte Azure-gebruikers met behulp van Azure Data Factory en andere Cortana Intelligence-componentservices, waaronder [HDInsight](https://azure.microsoft.com/services/hdinsight/) en [Power BI.](https://powerbi.microsoft.com/)

De online retailer gebruikt een Azure Blob store, een on-premises SQL-server, Azure SQL DB en een relationele datamart als hun opties voor gegevensopslag in de hele werkstroom.  De blob-winkel bevat klantgegevens, gegevens over klantgedrag en productinformatiegegevens. De productinformatiegegevens omvatten productmerkinformatie en een productcatalogus die on-premises is opgeslagen in een SQL-gegevensmagazijn. 

Alle gegevens worden gecombineerd en ingevoerd in een productaanbevelingssysteem om gepersonaliseerde aanbevelingen te leveren op basis van klantinteresses en acties, terwijl de gebruiker door producten in de catalogus op de website bladert. De klanten zien ook producten die gerelateerd zijn aan het product dat ze bekijken op basis van algemene websitegebruikspatronen die niet gerelateerd zijn aan één gebruiker.

![use case diagram](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigabytes van ruwe web log bestanden worden dagelijks gegenereerd van de website van de online retailer als semi-gestructureerde bestanden. De ruwe weblogbestanden en de informatie over de klant- en productcatalogus worden regelmatig opgenomen in een Azure Blob-opslag met behulp van de wereldwijd geïmplementeerde gegevensbeweging van Data Factory als service. De raw-logboekbestanden voor de dag worden (per jaar en maand) verdeeld in blobopslag voor langdurige opslag.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) wordt gebruikt om de raw-logbestanden in de blob-opslag te partitioneren en de ingenomen logboeken op schaal te verwerken met behulp van zowel Hive- als Pig-scripts. De partitiegegevens worden vervolgens verwerkt om de benodigde ingangen voor een machine learning-aanbevelingssysteem te extraheren om de gepersonaliseerde productaanbevelingen te genereren.

Het aanbevelingssysteem dat in dit voorbeeld voor machine learning wordt gebruikt, is een open source machine learning-aanbevelingsplatform van [Apache Mahout.](https://mahout.apache.org/)  Elk [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) of aangepast model kan op het scenario worden toegepast.  Het Mahout-model wordt gebruikt om de gelijkenis tussen items op de website te voorspellen op basis van algemene gebruikspatronen en om de gepersonaliseerde aanbevelingen op basis van de individuele gebruiker te genereren.

Ten slotte wordt de resultatenset van gepersonaliseerde productaanbevelingen verplaatst naar een relationele datamart voor consumptie door de website van de retailer.  De resultaatset kan ook rechtstreeks vanuit blob-opslag worden geopend door een andere toepassing, of worden verplaatst naar extra winkels voor andere consumenten en use cases.

## <a name="benefits"></a>Voordelen
Door hun productaanbevelingsstrategie te optimaliseren en af te stemmen op de bedrijfsdoelstellingen, voldeed de oplossing aan de merchandising- en marketingdoelstellingen van de online retailer. Daarnaast konden ze de workflow voor productaanbevelingen op een efficiënte, betrouwbare en kosteneffectieve manier operationaliseren en beheren. De aanpak maakte het gemakkelijk voor hen om hun model bij te werken en de effectiviteit ervan te verfijnen op basis van de metingen van de verkoop click-to-conversion successen. Door Azure Data Factory te gebruiken, konden ze hun tijdrovende en dure handmatige cloudresourcebeheer opgeven en overgaan op on-demand cloudresourcebeheer. Daarom konden ze tijd, geld besparen en hun tijd voor de implementatie van oplossingen verkorten. Gegevensafstammingsweergaven en operationele servicestatus werden eenvoudig te visualiseren en problemen op te lossen met de intuïtieve datafactory-monitoring- en beheergebruikersinterface die beschikbaar zijn via de Azure-portal. Hun oplossing kan nu worden gepland en beheerd, zodat afgewerkte gegevens betrouwbaar worden geproduceerd en geleverd aan gebruikers, en gegevens en verwerking afhankelijkheden worden automatisch beheerd zonder menselijke tussenkomst.

Door deze gepersonaliseerde winkelervaring te bieden, creëerde de online retailer een meer concurrerende, boeiende klantervaring en verhoogt zo de verkoop en algehele klanttevredenheid.


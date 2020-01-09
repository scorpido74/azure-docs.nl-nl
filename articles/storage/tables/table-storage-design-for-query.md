---
title: Azure-tabel opslag ontwerpen voor query's | Microsoft Docs
description: Ontwerp tabellen voor query's in azure-tabel opslag.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 41a588ddc0c1be8014a84d8fe181013d8566f68d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457639"
---
# <a name="design-for-querying"></a>Ontwerp voor query's
Oplossingen voor tabel-service kunnen worden gelezen intensieve, intensieve schrijven of een combinatie van de twee. In dit artikel vindt u informatie over de zaken die u moet overwegen wanneer u uw Table service ontwerpt om Lees bewerkingen efficiënt te ondersteunen. Een ontwerp dat ondersteunt bewerkingen efficiënt lezen is meestal ook efficiënt voor schrijfbewerkingen. Er zijn echter extra aandachtspunten bij het ontwerpen ter ondersteuning van schrijf bewerkingen, zoals beschreven in het artikel [ontwerp voor het wijzigen van gegevens](table-storage-design-for-modification.md).

Een goed uitgangspunt voor het ontwerpen van uw tabel-service-oplossing waarmee u kunt gegevens efficiënt lezen is om te vragen 'welke query's wordt mijn toepassing hoeft uit te voeren om op te halen van de benodigde gegevens uit de tabelservice?'  

> [!NOTE]
> Met de Table-service is het belangrijk dat u het ontwerp van de juiste een omdat het is moeilijk en kostbaar later wijzigen. Bijvoorbeeld in een relationele database is het vaak mogelijk voor het oplossen van problemen met prestaties door te indexen toe te voegen aan een bestaande database: dit kan niet worden gebruikt met de Table-service.  
> 
> 

Deze sectie richt zich op de belangrijkste problemen die u rekening houden moet bij het ontwerpen van uw tabellen voor het uitvoeren van query's. De onderwerpen aan bod in deze sectie zijn onder andere:

* [Hoe uw eigen keuze aan PartitionKey en RowKey heeft gevolgen voor prestaties van query 's](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Een juiste PartitionKey kiezen](#choosing-an-appropriate-partitionkey)
* [Query's voor de Table-service te optimaliseren](#optimizing-queries-for-the-table-service)
* [Sorteren van gegevens in de Table-service](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hoe uw eigen keuze aan PartitionKey en RowKey heeft gevolgen voor prestaties van query 's
De volgende voorbeelden wordt ervan uitgegaan dat de table-service opslaat werknemer entiteiten met de volgende structuur (de meeste van de voorbeelden laat de **Timestamp** eigenschap voor de duidelijkheid):  

| *De naam van kolom* | *Gegevenstype* |
| --- | --- |
| **PartitionKey** (naam van afdeling) |Tekenreeks |
| **RowKey** (werknemer-Id) |Tekenreeks |
| **Voornaam** |Tekenreeks |
| **LastName** |Tekenreeks |
| **Leeftijd** |Geheel getal |
| **EmailAddress** |Tekenreeks |

In het artikel [overzicht van Azure Table Storage](table-storage-overview.md) worden enkele van de belangrijkste functies van de Azure-Table service beschreven die een directe invloed hebben op het ontwerpen van query's. Dit resulteert in de volgende algemene richtlijnen voor het ontwerpen van query's tabel-service. Houd er rekening mee dat de filter syntaxis die in de onderstaande voor beelden wordt gebruikt, afkomstig is uit de Table service REST API, Zie [query-entiteiten](https://docs.microsoft.com/rest/api/storageservices/Query-Entities)voor meer informatie.  

* Een ***punt Query*** is de meest efficiënte zoekopdracht om te gebruiken en wordt aanbevolen om te worden gebruikt voor zoekopdrachten met hoog volume of zoekacties laagste latentie vereisen. Een dergelijke query kan de indexen gebruiken om een afzonderlijke entiteit efficiënt te vinden door de waarden voor **PartitionKey** en **RowKey** op te geven. Bijvoorbeeld: $filter = (PartitionKey eq 'Verkoop') en (RowKey eq '2')  
* Ten tweede beste is een ***Bereikquery*** die gebruikmaakt van de **PartitionKey** en filters op een scala aan **RowKey** die moeten worden geretourneerd van meer dan één entiteit. De **PartitionKey** waarde geeft een specifieke partitie en de **RowKey** waarden identificeren een subset van de entiteiten in de betreffende partitie. Bijvoorbeeld: $filter = PartitionKey eq 'Verkoop en RowKey ge van' en RowKey lt 'T'  
* Derde beste is een ***partitie scannen*** die gebruikmaakt van de **PartitionKey** en filters op een andere niet-sleuteleigenschap en die meer dan één entiteit kunnen retourneren. De **PartitionKey** waarde geeft een specifieke partitie en de eigenschap waarden selecteren voor een subset van de entiteiten in de betreffende partitie. Bijvoorbeeld: $filter = PartitionKey eq 'Verkoop'- en achternaam eq 'Smith'  
* Een ***tabel scan*** bevat niet de **PartitionKey** en is zeer inefficiënt omdat hiermee wordt gezocht in alle partities waaruit de tabel bestaat, op zijn beurt voor overeenkomende entiteiten. Wordt uitgevoerd om een tabelscan, ongeacht of uw filter maakt gebruik van de **RowKey**. Bijvoorbeeld: $filter = LastName eq 'Jones'  
* Query's die meerdere entiteiten retourneren, retourneren ze gesorteerd **PartitionKey** en **RowKey** volgorde. Om te voorkomen dat de entiteiten in de client codeactiviteit, kies een **RowKey** die de meest voorkomende sorteervolgorde definieert.  

Houd er rekening mee dat het gebruik van een '**or**' om een filter op te geven op basis van **RowKey** waarden resulteert in een partitie scan en niet wordt behandeld als een bereik query. Daarom moet u query's die gebruikmaken van filters zoals voorkomen: $filter = PartitionKey eq 'Verkoop' (RowKey eq '121' of een RowKey eq '322')  

Zie voor voorbeelden van code op de client die de Storage-clientbibliotheek gebruiken voor het uitvoeren van efficiënte query's:  

* [Een punt query uitvoeren met de Storage-client bibliotheek](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Meerdere entiteiten ophalen met behulp van LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Server-side-projectie](table-storage-design-patterns.md#server-side-projection)  

Zie voor voorbeelden van de client-side-code die meerdere Entiteitstypen die zijn opgeslagen in dezelfde tabel kunnen worden verwerkt:  

* [Werken met heterogene entiteits typen](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Een juiste PartitionKey kiezen
Uw eigen keuze aan **PartitionKey** de noodzaak voor het inschakelen van het gebruik van EGTs (om te zorgen voor consistentie) moet verdelen op basis van de vereiste voor uw entiteiten verdelen over meerdere partities (zodat een schaalbare oplossing).  

Op een extreme kan voor het opslaan van alle uw entiteiten in één partitie, maar dit kan de schaalbaarheid van uw oplossing beperken en weg kunnen staan de table-service wordt het verdelen van aanvragen. In het andere geval kunt u één entiteit per partitie opslaan, die zeer schaalbaar is en waardoor de Table-service taak verdeling kan aanvragen, maar die voor komt dat u trans acties van entiteits groepen gebruikt.  

Een ideale **PartitionKey** is een waarmee u kunt efficiënt query's gebruiken en die voldoende partities om te controleren of uw oplossing is schaalbaar is. Normaal gesproken vindt u dat uw entiteiten een geschikte eigenschap die wordt verspreid uw entiteiten over voldoende partities hebben.

> [!NOTE]
> Bijvoorbeeld in een systeem waarin informatie over gebruikers- of werknemers, gebruikers-id is mogelijk een goede PartitionKey. Mogelijk hebt u verschillende entiteiten die gebruikmaken van een bepaalde gebruikers-id als de partitiesleutel. Elke entiteit die gegevens over een gebruiker worden opgeslagen in één partitie is gegroepeerd en dus deze entiteiten zijn toegankelijk via de entiteit-groepstransacties, terwijl u nog steeds uiterst schaalbare.
> 
> 

Er zijn aanvullende overwegingen in uw keuze aan **PartitionKey** die betrekking hebben op de manier waarop u entiteiten gaat invoegen, bijwerken en verwijderen. Zie [tabellen ontwerpen voor het wijzigen van gegevens](table-storage-design-for-modification.md)voor meer informatie.  

## <a name="optimizing-queries-for-the-table-service"></a>Query's voor de Table-service te optimaliseren
De service van de tabel indexeert automatisch uw entiteiten met behulp van de **PartitionKey** en **RowKey** waarden in één geclusterde index, dus de reden dat wijst u query's zijn het meest efficiënt te gebruiken. Er zijn echter geen indexen dan die op de geclusterde index op de **PartitionKey** en **RowKey**.

Veel ontwerpen moeten voldoen aan de vereisten voor het opzoeken van de entiteiten op basis van meerdere criteria inschakelen. Bijvoorbeeld zoeken naar werknemers entiteiten op basis van e-mail, werknemer-id of achternaam op. De patronen die in de [tabel ontwerp patronen](table-storage-design-patterns.md) worden beschreven, zijn van deze typen vereisten en beschrijven manieren om het feit te omzeilen dat de Table service geen secundaire indexen levert:  

* [Intra-partitie secundaire index patroon](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store meerdere kopieën van elke entiteit met behulp van verschillende **RowKey** waarden (in dezelfde partitie) voor de snelle en efficiënte zoekopdrachten en alternatieve sorteervolgorde met behulp van verschillende **RowKey** waarden.  
* [Secundaire index tussen partitie patroon](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) -Store meerdere kopieën van elke entiteit met behulp van verschillende **RowKey** waarden in afzonderlijke partities of in afzonderlijke tabellen om in te schakelen van snelle en efficiënte zoekopdrachten en alternatieve sorteren orders met behulp van verschillende **RowKey** waarden.  
* [Index entiteiten patroon](table-storage-design-patterns.md#index-entities-pattern) -entiteiten zodat efficiënte zoekopdrachten die resulteren in een lijst met entiteiten index onderhouden.  

## <a name="sorting-data-in-the-table-service"></a>Sorteren van gegevens in de Table-service
De Table-service retourneert entiteiten gesorteerd in oplopende volgorde op basis van **PartitionKey** en vervolgens op **RowKey**. Deze sleutels worden tekenreekswaarden en om ervoor te zorgen dat numerieke waarden correct sorteren, moet u deze converteren naar een vaste lengte en ze worden opgevuld met nullen. Bijvoorbeeld, als de waarde van de werknemer-id die u gebruiken als de **RowKey** is een geheel getal, moet u de werknemer-id converteren **123** naar **00000123**.  

Veel toepassingen zijn vereisten voor het gebruik van de gegevens gesorteerd op verschillende plekken: bijvoorbeeld werknemers sorteren op naam, of lid wordt van datum. De volgende patronen bepalen hoe alternatieve Sorteer volgorden voor uw entiteiten worden aangegeven:  

* [Intra-partitie secundaire index patroon](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Store meerdere kopieën van elke entiteit met behulp van verschillende RowKey waarden (in dezelfde partitie) voor snelle en efficiënte zoekopdrachten voor bestanden en alternatieve sorteren orders met behulp van verschillende RowKey waarden.  
* [Secundaire index tussen partitie patroon](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store meerdere kopieën van elke entiteit die verschillende waarden voor de RowKey in afzonderlijke partities in afzonderlijke tabellen met snelle en efficiënte zoekopdrachten voor bestanden en alternatieve sorteren orders met behulp van verschillende RowKey waarden .
* [Logboek tail patroon](table-storage-design-patterns.md#log-tail-pattern) -ophalen van de *n* entiteiten die onlangs zijn toegevoegd aan een partitie met behulp van een **RowKey** gesorteerd in omgekeerde datum en de volgorde van tijd-waarde.  

## <a name="next-steps"></a>Volgende stappen

- [Tabel ontwerp patronen](table-storage-design-patterns.md)
- [Model relaties](table-storage-design-modeling.md)
- [Tabel gegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevens aanpassing](table-storage-design-for-modification.md)

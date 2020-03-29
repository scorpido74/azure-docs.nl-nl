---
title: Azure-tabelopslag ontwerpen voor query's | Microsoft Documenten
description: Ontwerp tabellen voor query's in Azure Table-opslag.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 41a588ddc0c1be8014a84d8fe181013d8566f68d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457639"
---
# <a name="design-for-querying"></a>Ontwerp voor query's
Table service oplossingen kunnen worden gelezen intensief, schrijf intensief, of een mix van de twee. Dit artikel richt zich op de dingen die u in gedachten moet houden wanneer u uw tabelservice ontwerpt om leesbewerkingen efficiënt te ondersteunen. Een ontwerp dat leesbewerkingen efficiënt ondersteunt, is doorgaans ook efficiënt voor schrijfbewerkingen. Er zijn echter aanvullende overwegingen om in gedachten te houden bij het ontwerpen om schrijfbewerkingen te ondersteunen, besproken in het artikel [Ontwerp voor gegevenswijziging.](table-storage-design-for-modification.md)

Een goed uitgangspunt voor het ontwerpen van uw Table-serviceoplossing om u in staat te stellen gegevens efficiënt te lezen, is om te vragen "Welke query's moet mijn toepassing uitvoeren om de gegevens op te halen die het nodig heeft van de tabelservice?"  

> [!NOTE]
> Met de Table-service is het belangrijk om het ontwerp van vooraan correct te krijgen, omdat het moeilijk en duur is om het later te wijzigen. In een relationele database is het bijvoorbeeld vaak mogelijk om prestatieproblemen op te lossen door simpelweg indexen toe te voegen aan een bestaande database: dit is geen optie met de tabelservice.  
> 
> 

In dit gedeelte wordt de belangrijkste problemen behandeld die u moet aanpakken bij het ontwerpen van uw tabellen voor query's. De onderwerpen die in deze sectie aan bod komen zijn:

* [Hoe uw keuze voor PartitionKey en RowKey de queryprestaties beïnvloedt](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Een geschikte partitionkey kiezen](#choosing-an-appropriate-partitionkey)
* [Query's optimaliseren voor de tabelservice](#optimizing-queries-for-the-table-service)
* [Gegevens sorteren in de tabelservice](#sorting-data-in-the-table-service)

## <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Hoe uw keuze voor PartitionKey en RowKey de queryprestaties beïnvloedt
In de volgende voorbeelden wordt ervan uitgegaan dat de tabelservice werknemersentiteiten opslaat met de volgende structuur (de meeste voorbeelden laten de eigenschap **Timestamp** voor duidelijkheid weg):  

| *Kolomnaam* | *Gegevenstype* |
| --- | --- |
| **PartitionKey** (afdelingsnaam) |Tekenreeks |
| **RowKey** (werknemers-id) |Tekenreeks |
| **Voornaam** |Tekenreeks |
| **Achternaam** |Tekenreeks |
| **Leeftijd** |Geheel getal |
| **Emailaddress** |Tekenreeks |

In het overzicht van [de Azure Table-opslag](table-storage-overview.md) wordt een aantal van de belangrijkste functies van de Azure Table-service beschreven die een directe invloed hebben op het ontwerpen voor query's. Deze resulteren in de volgende algemene richtlijnen voor het ontwerpen van tabelservicequery's. Houd er rekening mee dat de filtersyntaxis die in de onderstaande voorbeelden wordt gebruikt, afkomstig is uit de API REST van de tabelservice, zie [Queryentiteiten](https://docs.microsoft.com/rest/api/storageservices/Query-Entities)voor meer informatie.  

* Een ***puntquery*** is de meest efficiënte opzoeking om te gebruiken en wordt aanbevolen om te worden gebruikt voor zoekopdrachten met een hoog volume of zoekopdrachten waarvoor de laagste latentie vereist is. Een dergelijke query kan de indexen gebruiken om een afzonderlijke entiteit zeer efficiënt te lokaliseren door zowel de **partitionkey-** als **de RowKey-waarden** op te geven. Bijvoorbeeld: $filter=(PartitionKey eq 'Sales') en (RowKey eq '2')  
* Op de tweede plaats ***u een bereikquery uitvoeren*** die de **partitionkey** en filters op een reeks **RowKey-waarden** gebruikt om meer dan één entiteit terug te sturen. De **partitionkey-waarde** identificeert een specifieke partitie en de **rowkey-waarden** identificeren een subset van de entiteiten in die partitie. Bijvoorbeeld: $filter=PartitionKey eq 'Sales' en RowKey ge 'S' en RowKey lt 'T'  
* Ten derde beste is een ***partitiescan*** die de **PartitionKey** gebruikt en filters op een andere niet-sleuteleigenschap en die meer dan één entiteit kan retourneren. De **partitionkey-waarde** identificeert een specifieke partitie en de eigenschapswaarden selecteren voor een subset van de entiteiten in die partitie. Bijvoorbeeld: $filter=PartitionKey eq 'Sales' en LastName eq 'Smith'  
* Een ***tabelscan*** bevat de **PartitionKey** niet en is zeer inefficiënt omdat deze alle partities doorzoekt die op hun beurt deel uitmaken van uw tabel voor overeenkomende entiteiten. Het zal een tabelscan uitvoeren, ongeacht of uw filter de **RowKey**gebruikt. Bijvoorbeeld: $filter=LastName eq 'Jones'  
* Query's die meerdere entiteiten retourneren, retourneren ze gesorteerd in **partitionkey-** en **rowkey-order.** Als u wilt voorkomen dat de entiteiten in de client worden gebruikt, kiest u een **RowKey** die de meest voorkomende sorteervolgorde definieert.  

Houd er rekening mee dat het gebruik van een filter**of**" om een filter op te geven op basis van **RowKey-waarden** resulteert in een partitiescan en niet wordt behandeld als een bereikquery. Daarom moet u query's vermijden die filters gebruiken zoals: $filter=PartitionKey eq 'Sales' en (RowKey eq '121' of RowKey eq '322')  

Zie voor voorbeelden van clientcode die de opslagclientbibliotheek gebruikt om efficiënte query's uit te voeren:  

* [Een puntquery uitvoeren met de opslagclientbibliotheek](table-storage-design-patterns.md#executing-a-point-query-using-the-storage-client-library)
* [Meerdere entiteiten ophalen met LINQ](table-storage-design-patterns.md#retrieving-multiple-entities-using-linq)
* [Projectie aan de serverzijde](table-storage-design-patterns.md#server-side-projection)  

Zie voor voorbeelden van client-side code die meerdere entiteitstypen kan verwerken die in dezelfde tabel zijn opgeslagen:  

* [Werken met heterogene entiteitstypen](table-storage-design-patterns.md#working-with-heterogeneous-entity-types)  

## <a name="choosing-an-appropriate-partitionkey"></a>Een geschikte partitionkey kiezen
Uw keuze voor **PartitionKey** moet de noodzaak om het gebruik van EGT's (om consistentie te garanderen) in evenwicht brengen met de vereiste om uw entiteiten over meerdere partities te verdelen (om een schaalbare oplossing te garanderen).  

Aan het ene uiterste u al uw entiteiten opslaan in één partitie, maar dit kan de schaalbaarheid van uw oplossing beperken en zou voorkomen dat de tabelservice aanvragen voor laden in balans kan. Aan het andere uiterste u één entiteit per partitie opslaan, wat zeer schaalbaar zou zijn en waarmee de tabelservice aanvragen voor load-balance kan laden, maar die u zou verhinderen entiteitsgroepstransacties te gebruiken.  

Een ideale **PartitionKey** is er een die u in staat stelt om efficiënte query's te gebruiken en die voldoende partities heeft om ervoor te zorgen dat uw oplossing schaalbaar is. Doorgaans zult u merken dat uw entiteiten een geschikte eigenschap hebben die uw entiteiten over voldoende partities verdeelt.

> [!NOTE]
> In een systeem dat bijvoorbeeld informatie over gebruikers of werknemers opslaat, kan UserID een goede PartitionKey zijn. Mogelijk hebt u verschillende entiteiten die een bepaalde gebruikersnaam als partitiesleutel gebruiken. Elke entiteit die gegevens over een gebruiker opslaat, wordt gegroepeerd in één partitie, zodat deze entiteiten toegankelijk zijn via entiteitsgroeptransacties, terwijl ze nog steeds zeer schaalbaar zijn.
> 
> 

Er zijn aanvullende overwegingen in uw keuze van **PartitionKey** die betrekking hebben op hoe u entiteiten invoegt, bijwerkt en verwijdert. Zie [Tabellen ontwerpen voor gegevenswijziging voor](table-storage-design-for-modification.md)meer informatie .  

## <a name="optimizing-queries-for-the-table-service"></a>Query's optimaliseren voor de tabelservice
De tabelservice indexeert uw entiteiten automatisch met behulp van de **partitionkey-** en **rowkey-waarden** in één geclusterde index, vandaar de reden dat puntquery's het meest efficiënt zijn om te gebruiken. Er zijn echter geen andere indexen dan die op de geclusterde index op de **PartitionKey** en **RowKey**.

Veel ontwerpen moeten voldoen aan vereisten om het opzoeken van entiteiten op basis van meerdere criteria mogelijk te maken. Bijvoorbeeld het lokaliseren van werknemersentiteiten op basis van e-mail, werknemers-id of achternaam. De patronen die in [tabelontwerppatronen](table-storage-design-patterns.md) worden beschreven, beantwoorden dit soort vereisten en beschrijven manieren om te werken rond het feit dat de tabelservice geen secundaire indexen biedt:  

* [Secundair indexpatroon binnen partitie](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Sla meerdere kopieën van elke entiteit op met verschillende **RowKey-waarden** (in dezelfde partitie) om snelle en efficiënte opzoekingen en alternatieve sorteerorders in te schakelen met behulp van verschillende **RowKey-waarden.**  
* [Secundair indexpatroon tussen partities](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Sla meerdere kopieën van elke entiteit op met verschillende **RowKey-waarden** in afzonderlijke partities of in afzonderlijke tabellen om snelle en efficiënte opzoekingen en alternatieve sorteerorders in te schakelen met behulp van verschillende **RowKey-waarden.**  
* [Patroon indexentiteiten](table-storage-design-patterns.md#index-entities-pattern) - Indexentiteiten onderhouden om efficiënte zoekopdrachten mogelijk te maken die lijsten met entiteiten retourneren.  

## <a name="sorting-data-in-the-table-service"></a>Gegevens sorteren in de tabelservice
De tabelservice retourneert entiteiten die zijn gesorteerd in oplopende volgorde op basis van **PartitionKey** en vervolgens op **RowKey**. Deze toetsen zijn tekenreekswaarden en om ervoor te zorgen dat numerieke waarden correct sorteren, moet u ze converteren naar een vaste lengte en ze met nullen vastzetten. Als de waarde van de werknemers-id die u als **rowkey** gebruikt bijvoorbeeld een gehele waarde is, moet u de werknemers-id **123** converteren naar **00000123**.  

Veel toepassingen hebben vereisten voor het gebruik van gegevens die in verschillende orders worden gesorteerd: bijvoorbeeld het sorteren van werknemers op naam of op datum van toetreding. In de volgende patronen wordt ingegaan op het alternatieve sorteren van orders voor uw entiteiten:  

* [Secundair indexpatroon binnen partitie](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Sla meerdere kopieën van elke entiteit op met verschillende RowKey-waarden (in dezelfde partitie) om snelle en efficiënte opzoekingen en alternatieve sorteerorders in te schakelen met behulp van verschillende RowKey-waarden.  
* [Secundair indexpatroon tussen partities](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Sla meerdere kopieën van elke entiteit op met verschillende RowKey-waarden in afzonderlijke partities in afzonderlijke tabellen om snelle en efficiënte opzoekingen en alternatieve sorteerorders mogelijk te maken met behulp van verschillende RowKey-waarden.
* [Logboekstaartpatroon](table-storage-design-patterns.md#log-tail-pattern) - Haal de *n-entiteiten* op die onlangs aan een partitie zijn toegevoegd met behulp van een **RowKey-waarde** die wordt sorteert in omgekeerde datum en tijdvolgorde.  

## <a name="next-steps"></a>Volgende stappen

- [Tabelontwerppatronen](table-storage-design-patterns.md)
- [Relaties modelleren](table-storage-design-modeling.md)
- [Tabelgegevens versleutelen](table-storage-design-encrypt-data.md)
- [Ontwerp voor gegevenswijziging](table-storage-design-for-modification.md)

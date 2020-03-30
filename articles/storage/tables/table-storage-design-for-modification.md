---
title: Azure-tabelopslag ontwerpen voor gegevenswijziging | Microsoft Documenten
description: Ontwerptabellen voor gegevenswijziging in Azure Table-opslag.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75771543"
---
# <a name="design-for-data-modification"></a>Ontwerp voor gegevenswijziging
Dit artikel richt zich op de ontwerpoverwegingen voor het optimaliseren van inserts, updates en deletes. In sommige gevallen moet u de afweging tussen ontwerpen die optimaliseren voor het opvragen tegen ontwerpen die optimaliseren voor gegevenswijziging evalueren, net zoals u doet in ontwerpen voor relationele databases (hoewel de technieken voor het beheren van de ontwerpafwegingen verschillen in een relationele database). De sectie Tabelontwerppatronen beschrijft enkele gedetailleerde ontwerppatronen voor de tabelservice en belicht enkele van deze afwegingen. In de praktijk zult u merken dat veel ontwerpen die zijn geoptimaliseerd voor het opvragen van entiteiten ook goed werken voor het wijzigen van entiteiten.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>De prestaties van invoegbewerkingen optimaliseren, bijwerken en verwijderen
Als u een entiteit wilt bijwerken of verwijderen, moet u deze kunnen identificeren met behulp van de waarden **PartitionKey** en **RowKey.** In dit verband moet uw keuze voor **PartitionKey** en **RowKey** voor het wijzigen van entiteiten vergelijkbare criteria volgen als uw keuze om puntquery's te ondersteunen, omdat u entiteiten zo efficiënt mogelijk wilt identificeren. U wilt geen inefficiënte partitie- of tabelscan gebruiken om een entiteit te vinden om de **partitionkey-** en **rowkey-waarden** te ontdekken die u moet bijwerken of verwijderen.  

De volgende patronen in de ontwerppatronen van de sectie Tabel zijn gericht op het optimaliseren van de prestaties of uw bewerkingen voor invoegen, bijwerken en verwijderen:  

* [Patroon voor het verwijderen](table-storage-design-patterns.md#high-volume-delete-pattern) van een hoog volume - Schakel het verwijderen van een groot aantal entiteiten in door alle entiteiten op te slaan voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel; u de entiteiten verwijdert door de tabel te verwijderen.  
* [Patroon van gegevensreeksen](table-storage-design-patterns.md#data-series-pattern) - Sla volledige gegevensreeksen op in één entiteit om het aantal aanvragen dat u doet te minimaliseren.  
* [Breed entiteitenpatroon](table-storage-design-patterns.md#wide-entities-pattern) - Gebruik meerdere fysieke entiteiten om logische entiteiten met meer dan 252 eigenschappen op te slaan.  
* [Patroon grote entiteiten](table-storage-design-patterns.md#large-entities-pattern) : gebruik blobopslag om grote eigenschapswaarden op te slaan.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Zorgen voor consistentie in uw opgeslagen entiteiten
De andere belangrijke factor die uw keuze van sleutels voor het optimaliseren van gegevenswijzigingen beïnvloedt, is hoe u consistentie garanderen door atomaire transacties te gebruiken. U een EGT alleen gebruiken om te werken op entiteiten die in dezelfde partitie zijn opgeslagen.  

De volgende patronen in het artikel [Tabelontwerppatronen](table-storage-design-patterns.md) hebben betrekking op het beheren van consistentie:  

* [Secundair indexpatroon binnen partitie](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Sla meerdere kopieën van elke entiteit op met verschillende **RowKey-waarden** (in dezelfde partitie) om snelle en efficiënte opzoekingen en alternatieve sorteerorders in te schakelen met behulp van verschillende **RowKey-waarden.**  
* [Secundair indexpatroon tussen partities](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Sla meerdere kopieën van elke entiteit op met verschillende RowKey-waarden in afzonderlijke partities of in afzonderlijke tabellen om snelle en efficiënte opzoekingen en alternatieve sorteerorders in te schakelen met behulp van verschillende **RowKey-waarden.**  
* [Uiteindelijk consistent transactiepatroon](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) - Uiteindelijk consistent gedrag inschakelen over de grenzen van de partitie of de grenzen van het opslagsysteem met Azure-wachtrijen.
* [Patroon indexentiteiten](table-storage-design-patterns.md#index-entities-pattern) - Indexentiteiten onderhouden om efficiënte zoekopdrachten mogelijk te maken die lijsten met entiteiten retourneren.  
* [Denormalisatiepatroon](table-storage-design-patterns.md#denormalization-pattern) - Combineer gerelateerde gegevens samen in één entiteit zodat u alle gegevens ophalen die u nodig hebt met één puntquery.  
* [Patroon van gegevensreeksen](table-storage-design-patterns.md#data-series-pattern) - Sla volledige gegevensreeksen op in één entiteit om het aantal aanvragen dat u doet te minimaliseren.  

Zie de sectie [Entiteitgroepgroeptransacties](table-storage-design.md#entity-group-transactions)voor informatie over transacties van entiteitsgroepen .  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zorg ervoor dat uw ontwerp voor efficiënte aanpassingen efficiënte vragen vergemakkelijkt
In veel gevallen resulteert een ontwerp voor efficiënt query's in efficiënte wijzigingen, maar u moet altijd evalueren of dit het geval is voor uw specifieke scenario. Sommige patronen in het artikel [Tabelontwerppatronen](table-storage-design-patterns.md) evalueren expliciet afwegingen tussen query's en wijzigenvan entiteiten en u moet altijd rekening houden met het aantal van elk type bewerking.  

De volgende patronen in het artikel [Tabelontwerppatronen](table-storage-design-patterns.md) hebben betrekking op afwegingen tussen het ontwerpen voor efficiënte query's en het ontwerpen voor efficiënte gegevenswijziging:  

* [Samengestelde sleutelpatroon](table-storage-design-patterns.md#compound-key-pattern) - Gebruik samengestelde **RowKey-waarden** om een client in staat te stellen gerelateerde gegevens op te zoeken met één puntquery.  
* [Logboekstaartpatroon](table-storage-design-patterns.md#log-tail-pattern) - Haal de *n-entiteiten* op die onlangs aan een partitie zijn toegevoegd met behulp van een **RowKey-waarde** die wordt sorteert in omgekeerde datum en tijdvolgorde.  

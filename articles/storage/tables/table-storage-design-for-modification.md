---
title: Azure-tabel opslag ontwerpen voor het wijzigen van gegevens | Microsoft Docs
description: Ontwerp tabellen voor het wijzigen van gegevens in azure-tabel opslag.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: c95be7afae5c0a84c06b691c8225f32f2aa68260
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771543"
---
# <a name="design-for-data-modification"></a>Ontwerp voor gegevenswijziging
Dit artikel richt zich op de ontwerp overwegingen voor het optimaliseren van invoegingen, updates en verwijderingen. In sommige gevallen moet u evalueren de verhouding tussen de ontwerpen die geoptimaliseerd voor het uitvoeren van query's op basis van modellen die voor wijziging van gegevens, optimaliseren net zoals u kunt in ontwerpen voor relationele databases doen (Hoewel de technieken voor het beheren van de ontwerp-en nadelen andere in een relationele database). In de sectie tabel ontwerp patronen worden een aantal gedetailleerde ontwerp patronen voor de Table service beschreven en worden enkele van deze trans acties gemarkeerd. U ziet in de praktijk veel modellen die zijn geoptimaliseerd voor het uitvoeren van query's entiteiten ook geschikt voor entiteiten wijzigen.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>De prestaties van INSERT-, update-en delete-bewerkingen optimaliseren
Als u wilt bijwerken of verwijderen van een entiteit, u moet kunnen worden geïdentificeerd met behulp van de **PartitionKey** en **RowKey** waarden. In dit opzicht van uw keuze **PartitionKey** en **RowKey** voor wijzigen van entiteiten dezelfde criteria naar keuze volgen moet voor de ondersteuning van point-query's omdat u wilt dat voor het identificeren van entiteiten als efficiënt mogelijk. U niet wilt gebruiken een inefficiënte partitie of tabel scan om te vinden een entiteit detecteren de **PartitionKey** en **RowKey** waarden die u wilt bijwerken of verwijderen.  

De volgende patronen in het ontwerp patroon van de sectie tabel maken het optimaliseren van de prestaties of uw insert-, update-en delete-bewerkingen:  

* [Hoog volume patroon verwijderen](table-storage-design-patterns.md#high-volume-delete-pattern) -inschakelen van de verwijdering van een groot aantal entiteiten door op te slaan van de entiteiten voor gelijktijdige verwijdering in hun eigen afzonderlijke tabel; u de entiteiten verwijderen door het verwijderen van de tabel.  
* [Patroon voor gegevens uit de serie](table-storage-design-patterns.md#data-series-pattern) -Store volledige gegevensreeks in één enkele entiteit te minimaliseren van het aantal aanvragen die u aanbrengt.  
* [Breed entiteiten patroon](table-storage-design-patterns.md#wide-entities-pattern) -meerdere fysieke entiteiten gebruiken voor het opslaan van logische entiteiten met meer dan 252 eigenschappen.  
* [Grote entiteiten patroon](table-storage-design-patterns.md#large-entities-pattern) -blob storage gebruiken voor het opslaan van grote eigenschapswaarden.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Consistentie in uw opgeslagen entiteiten garanderen
De andere belangrijke factoren die van invloed op de keuze van sleutels voor het optimaliseren van aanpassingen wordt beschreven hoe u zorgen voor consistentie met behulp van atomic-transacties. U kunt alleen een EGT gebruiken om te worden uitgevoerd op entiteiten die zijn opgeslagen in dezelfde partitie.  

De volgende patronen in het artikel [tabel ontwerp patronen](table-storage-design-patterns.md) adres consistentie beheer:  

* [Intra-partitie secundaire index patroon](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) -Store meerdere kopieën van elke entiteit met behulp van verschillende **RowKey** waarden (in dezelfde partitie) voor de snelle en efficiënte zoekopdrachten en alternatieve sorteervolgorde met behulp van verschillende **RowKey** waarden.  
* [Secundaire index tussen partitie patroon](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Store meerdere kopieën van elke entiteit die gebruik van verschillende waarden voor de RowKey in afzonderlijke partities of in afzonderlijke tabellen voor snelle en efficiënte zoekopdrachten voor bestanden en alternatieve sorteren orders met behulp van verschillende **RowKey** waarden.  
* [Uiteindelijk consistent transacties patroon](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) -uiteindelijk consistent gedrag partitiegrenzen of wanneer de grenzen van de storage-systeem inschakelen met behulp van Azure-wachtrijen.
* [Patroon van index entiteiten](table-storage-design-patterns.md#index-entities-pattern) : behoud index entiteiten om efficiënte Zoek opdrachten in te scha kelen waarmee lijsten met entiteiten worden geretourneerd.  
* [Denormalisatie patroon](table-storage-design-patterns.md#denormalization-pattern) -proces van het combineren van de bijbehorende gegevens samen in één enkele entiteit waarmee u kunt om op te halen van alle gegevens die u nodig hebt met een single point-query.  
* [Patroon voor gegevens uit de serie](table-storage-design-patterns.md#data-series-pattern) -Store volledige gegevensreeks in één enkele entiteit te minimaliseren van het aantal aanvragen die u aanbrengt.  

Zie de sectie [trans acties voor entiteits](table-storage-design.md#entity-group-transactions)groepen voor meer informatie over entiteits transacties.  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Zorg ervoor dat uw ontwerp voor efficiënte wijzigingen efficiënte query's vereenvoudigt
In veel gevallen moet altijd een ontwerp voor efficiënte query resultaten in een efficiënte wijzigingen, maar u evalueren of dit het geval is bij uw specifieke scenario. Sommige van de patronen in de [tabel ontwerp patronen](table-storage-design-patterns.md) van het artikel evalueren expliciet de verhoudingen van het opvragen en aanpassen van entiteiten, en u moet altijd rekening houden met het aantal van elk type bewerking.  

De volgende patronen in de [tabel met ontwerp patronen](table-storage-design-patterns.md) voor het artikel zijn een afweging tussen het ontwerpen van efficiënte query's en het ontwerpen voor efficiënte gegevens aanpassing:  

* [Samengesteld sleutel patroon](table-storage-design-patterns.md#compound-key-pattern) : gebruik samengestelde **RowKey** -waarden om een client in staat te stellen gerelateerde gegevens te zoeken met een single point-query.  
* [Logboek tail patroon](table-storage-design-patterns.md#log-tail-pattern) -ophalen van de *n* entiteiten die onlangs zijn toegevoegd aan een partitie met behulp van een **RowKey** gesorteerd in omgekeerde datum en de volgorde van tijd-waarde.  

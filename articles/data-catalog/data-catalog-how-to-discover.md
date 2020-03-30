---
title: Gegevensbronnen ontdekken in Azure-gegevenscatalogus
description: In dit artikel wordt uitgelegd hoe u geregistreerde gegevenselementen ontdekken met Azure Data Catalog, inclusief zoeken en filteren en het gebruik van de mogelijkheden voor het markeren van de populaire markering spoort van de Azure Data Catalog-portal.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: b12cb94832a1ea977fb13f5f2271984dc8780cee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736380"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Gegevensbronnen ontdekken in Azure-gegevenscatalogus

## <a name="introduction"></a>Inleiding

Azure Data Catalog is een volledig beheerde cloudservice die dient als een systeem van registratie en detectie voor bedrijfsgegevensbronnen. Met andere woorden, Data Catalog helpt mensen gegevensbronnen te ontdekken, te begrijpen en te gebruiken. Het helpt organisaties om meer waarde te halen uit hun bestaande gegevens. Nadat een gegevensbron is geregistreerd bij Gegevenscatalogus, worden de metagegevens door de service geïndexeerd, zodat u gemakkelijk zoeken om de gegevens te ontdekken die u nodig hebt.

## <a name="searching-and-filtering"></a>Zoeken en filteren

Discovery in Data Catalog maakt gebruik van twee primaire mechanismen: zoeken en filteren.

Zoeken is zowel intuïtief als krachtig. Standaard worden de zoektermen vergeleken met een eigenschap in de catalogus, met inbegrip van door de gebruiker gemaakte aantekeningen.

Filteren is bedoeld als aanvulling op het zoeken. U specifieke kenmerken selecteren, zoals experts, gegevensbrontype, objecttype en tags. U alleen overeenkomende gegevenselementen weergeven en zoekresultaten beperken tot overeenkomende elementen.

Door een combinatie van zoeken en filteren te gebruiken, u snel navigeren in de gegevensbronnen die zijn geregistreerd bij Gegevenscatalogus om de gegevensbronnen te ontdekken die u nodig hebt.

## <a name="search-syntax"></a>Zoeksyntaxis

Hoewel de standaardzoekopdracht met vrije tekst eenvoudig en intuïtief is, u ook de zoeksyntaxis van gegevenscatalogus gebruiken voor meer controle over de zoekresultaten. Zoeken in gegevenscatalogus ondersteunt de volgende technieken:

| Techniek | Gebruiken | Voorbeeld |
| --- | --- | --- |
| Basiszoekopdracht |Basiszoekopdrachten die een of meer zoektermen gebruiken. Resultaten zijn activa die overeenkomen met een eigenschap met een of meer van de opgegeven voorwaarden. |`sales data` |
| Vastgoedscoping |Retourneer alleen gegevensbronnen waarvan de zoekterm is gekoppeld aan de opgegeven eigenschap. |`name:finance` |
| Booleaanse operators |Verbreed of vernauw een zoekopdracht met Booleaanse bewerkingen. |`finance NOT corporate` |
| Groeperen met haakjes |Gebruik haakjes om delen van de query te groeperen om een logische isolatie te bereiken, vooral in combinatie met Booleaanse operatoren. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Vergelijkingsoperators |Gebruik andere vergelijkingen dan gelijkheid voor eigenschappen met numerieke en datumgegevenstypen. |`modifiedTime > "11/05/2014"` |

Zie het artikel Azure Data [Catalog](/rest/api/datacatalog/#search-syntax-reference) voor meer informatie over zoeken in gegevenscatalogus.

## <a name="hit-highlighting"></a>Markeren

Wanneer u zoekresultaten bekijkt, worden alle weergegeven eigenschappen die overeenkomen met de opgegeven zoektermen (zoals de naam van de gegevensasset, beschrijving en tags) gemarkeerd om gemakkelijker te bepalen waarom een bepaald gegevenselement door een bepaalde zoekopdracht is geretourneerd.

> [!NOTE]
> Als u hithighlighting wilt uitschakelen, gebruikt u de schakelaar **Markeren** in de portal Gegevenscatalogus.

Wanneer u zoekresultaten bekijkt, is het mogelijk niet altijd duidelijk waarom een gegevensitem is opgenomen, zelfs met het markeren van treffers ingeschakeld. Omdat alle eigenschappen standaard worden doorzocht, kan een gegevenselement worden geretourneerd vanwege een overeenkomst op een eigenschap op kolomniveau. En omdat meerdere gebruikers geregistreerde gegevensassets kunnen annoteren met hun eigen tags en beschrijvingen, worden niet alle metagegevens weergegeven in de lijst met zoekresultaten.

In de standaardtegelweergave bevat elke tegel die in de zoekresultaten wordt weergegeven een pictogram **Zoekterm weergeven,** zodat u snel het aantal overeenkomsten en de locatie ervan bekijken en naar deze tegels springen als u dat wilt.

 ![Zoekovereenkomsten op markeren en zoeken in de Azure Data Catalog-portal](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Samenvatting

Omdat het registreren van een gegevensbron met Data Catalog structurele en beschrijvende metagegevens van de gegevensbron kopieert naar de catalogusservice, wordt de gegevensbron gemakkelijker te ontdekken en te begrijpen. Nadat u een gegevensbron hebt geregistreerd, u deze ontdekken door te filteren en te zoeken vanuit de portal Gegevenscatalogus.

## <a name="next-steps"></a>Volgende stappen

* Zie [Aan de slag met Azure Data Catalog](data-catalog-get-started.md)voor stapsgewijze details over het ontdekken van gegevensbronnen.

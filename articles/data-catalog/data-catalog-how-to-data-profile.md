---
title: Gegevensbronnen voor gegevensprofilering gebruiken in Azure-gegevenscatalogus
description: In het artikel waarin wordt uitgelegd hoe gegevensprofielen op tabel- en kolomniveau worden opgenomen bij het registreren van gegevensbronnen in Azure-gegevenscatalogus en hoe gegevensprofielen kunnen worden gebruikt om gegevensbronnen te begrijpen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 04ac6c2bf0137289221a4ae6ed58d5a71ad21739
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950225"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Gegevensbronnen voor gegevensprofiel in Azure-gegevenscatalogus

## <a name="introduction"></a>Inleiding

**Microsoft Azure Data Catalog** is een volledig beheerde cloudservice die dient als een systeem van registratie en detectiesysteem voor bedrijfsgegevensbronnen. Met andere woorden, **Azure Data Catalog** is alles over het helpen van mensen ontdekken, begrijpen en gebruiken van gegevensbronnen, en het helpen van organisaties om meer waarde te halen uit hun bestaande gegevens. Wanneer een gegevensbron is geregistreerd bij **Azure Data Catalog,** worden de metagegevens gekopieerd en geïndexeerd door de service, maar het verhaal eindigt daar niet.

De functie **Gegevensprofilering** van **Azure Data Catalog** onderzoekt de gegevens uit ondersteunde gegevensbronnen in uw catalogus en verzamelt statistieken en informatie over die gegevens. Het is eenvoudig om een profiel van uw gegevensassets op te nemen. Wanneer u een gegevensitem registreert, kiest **u Gegevensprofiel opnemen** in het hulpprogramma voor gegevensbronregistratie.

## <a name="what-is-data-profiling"></a>Wat is gegevensprofilering

Dataprofiling onderzoekt de gegevens in de gegevensbron die wordt geregistreerd en verzamelt statistieken en informatie over die gegevens. Tijdens het ontdekken van gegevensbronnen kunnen deze statistieken u helpen bij het bepalen van de geschiktheid van de gegevens om hun bedrijfsprobleem op te lossen.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

De volgende gegevensbronnen ondersteunen gegevensprofilering:

* TABELLEN en weergaven van SQL Server (inclusief Azure SQL DB en Azure SQL Data Warehouse)
* Oracle-tabellen en -weergaven
* Teradata tabellen en weergaven
* Hive-tabellen

Het opnemen van gegevensprofielen bij het registreren van gegevensassets helpt gebruikers bij het beantwoorden van vragen over gegevensbronnen, waaronder:

* Kan het worden gebruikt om mijn zakelijke probleem op te lossen?
* Voldoen de gegevens aan bepaalde normen of patronen?
* Wat zijn enkele van de afwijkingen van de gegevensbron?
* Wat zijn mogelijke uitdagingen om deze gegevens in mijn applicatie te integreren?

> [!NOTE]
> U ook documentatie toevoegen aan een asset om te beschrijven hoe gegevens kunnen worden geïntegreerd in een toepassing. Zie [Gegevensbronnen documenteren](data-catalog-how-to-documentation.md).
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Een gegevensprofiel opnemen bij het registreren van een gegevensbron

Het is eenvoudig om een profiel van uw gegevensbron op te nemen. Wanneer u een gegevensbron registreert, kiest u in het deelvenster **Objecten dat moet worden geregistreerd** van het hulpprogramma voor gegevensbronregistratie de optie **Gegevensprofiel opnemen**.

![Selectievakje Gegevensprofiel opnemen](media/data-catalog-data-profile/data-catalog-register-profile.png)

Zie [Gegevensbronnen registreren](data-catalog-how-to-register.md) en Aan de slag met [Azure Data Catalog](data-catalog-get-started.md)voor meer informatie over het registreren van gegevensbronnen.

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filteren op gegevenselementen die gegevensprofielen bevatten

Als u gegevenselementen wilt ontdekken die `has:tableDataProfiles` een `has:columnsDataProfiles` gegevensprofiel bevatten, u of als een van uw zoektermen opnemen.

> [!NOTE]
> Het **selecteren van Gegevensprofiel opnemen** in het hulpprogramma voor gegevensbronregistratie omvat zowel profielgegevens op tabel- als kolomniveau. Met de API voor gegevenscatalogus kunnen gegevenselementen echter worden geregistreerd met slechts één set opgenomen profielgegevens.
>

## <a name="viewing-data-profile-information"></a>Gegevensprofielgegevens weergeven

Zodra u een geschikte gegevensbron met een profiel hebt gevonden, u de gegevensprofielgegevens bekijken. Als u het gegevensprofiel wilt weergeven, selecteert u een gegevenselement en kiest **u Gegevensprofiel** in het portaalvenster Gegevenscatalogus.

![Tabblad Gegevensprofiel](media/data-catalog-data-profile/data-catalog-view.png)

Een gegevensprofiel in **Azure Data Catalog** toont tabel- en kolomprofielgegevens, waaronder:

### <a name="object-data-profile"></a>Objectgegevensprofiel

* Aantal rijen
* Tabelgrootte
* Toen het object voor het laatst is bijgewerkt

### <a name="column-data-profile"></a>Kolomgegevensprofiel

* Kolomgegevenstype
* Aantal verschillende waarden
* Aantal rijen met NULL-waarden
* Minimum-, maximum-, gemiddelde- en standaarddeviatie voor kolomwaarden

## <a name="summary"></a>Samenvatting

Gegevensprofilering biedt statistieken en informatie over geregistreerde gegevensactiva om u te helpen de geschiktheid van de gegevens te bepalen om bedrijfsproblemen op te lossen. Samen met het annoteren en documenteren van gegevensbronnen, kunnen gegevensprofielen gebruikers een beter inzicht geven in uw gegevens.

## <a name="see-also"></a>Zie ook

* [Gegevensbronnen registreren](data-catalog-how-to-register.md)
* [Aan de slag met Azure Data Catalog](data-catalog-get-started.md)

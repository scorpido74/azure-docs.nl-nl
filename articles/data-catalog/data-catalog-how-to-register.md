---
title: Gegevensbronnen registreren in Azure-gegevenscatalogus
description: In dit artikel wordt uitgelegd hoe u gegevensbronnen registreert in Azure Data Catalog, inclusief de metagegevensvelden die tijdens de registratie zijn geëxtraheerd.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 0c5fdac7df41fec3a6206dbd78af74b7f1b58c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736323"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Gegevensbronnen registreren in Azure-gegevenscatalogus
## <a name="introduction"></a>Inleiding
Azure Data Catalog is een volledig beheerde cloudservice die dient als een systeem van registratie en detectie voor bedrijfsgegevensbronnen. Met andere woorden, Data Catalog helpt mensen gegevensbronnen te ontdekken, te begrijpen en te gebruiken, en het helpt organisaties meer waarde te halen uit hun bestaande gegevens. De eerste stap om een gegevensbron vindbaar te maken via Data Catalog is het registreren van die gegevensbron.

## <a name="register-data-sources"></a>Gegevensbronnen registreren
Registratie is het proces van het extraheren van metagegevens uit de gegevensbron en het kopiëren van die gegevens naar de datacatalogusservice. De gegevens blijven waar ze zich momenteel bevinden, onder het beheer van de groep beheerders en het beleid van het huidige systeem.

Ga als volgt te werk om een gegevensbron te registreren:
1. Start in de portal Azure Data Catalog het hulpprogramma voor gegevenscatalogusgegevensregistratie. 
2. Meld u aan met uw werk- of schoolaccount met dezelfde Azure Active Directory-referenties die u gebruikt om u aan te melden bij de portal.
3. Selecteer de gegevensbron die u wilt registreren.

Zie de zelfstudie Aan de [slag met Azure Data Catalog](data-catalog-get-started.md) voor meer stapsgewijze details.

Nadat u de gegevensbron hebt geregistreerd, houdt de catalogus de locatie bij en indexeert de metagegevens. Gebruikers kunnen zoeken, bladeren en de gegevensbron ontdekken en vervolgens de locatie gebruiken om er verbinding mee te maken met behulp van de toepassing of het hulpprogramma van hun keuze.

## <a name="supported-data-sources"></a>Ondersteunde gegevensbronnen
Zie [Gegevenscatalogus DSR](data-catalog-dsr.md)voor een lijst met momenteel ondersteunde gegevensbronnen.

## <a name="structural-metadata"></a>Structurele metagegevens
Wanneer u een gegevensbron registreert, haalt het registratiegereedschap informatie uit over de structuur van de objecten die u selecteert. Deze informatie wordt structurele metagegevens genoemd.

Voor alle objecten bevat deze structurele metagegevens de locatie van het object, zodat gebruikers die de gegevens ontdekken, die informatie kunnen gebruiken om verbinding te maken met het object in de clienttools van hun keuze. Andere structurele metagegevens bevatten objectnaam en -type en kenmerk/kolomnaam en gegevenstype.

## <a name="descriptive-metadata"></a>Beschrijvende metagegevens
Naast de structurele kernmetagegevens die uit de gegevensbron worden geëxtraheerd, haalt het hulpprogramma voor gegevensbronregistratie beschrijvende metagegevens uit. Voor SQL Server Analysis Services en SQL Server Reporting Services wordt deze metagegevens overgenomen uit de eigenschappen Beschrijving die door deze services worden weergegeven. Voor SQL Server worden waarden\_die worden verstrekt met de uitgebreide eigenschap MS-beschrijving geëxtraheerd. Voor Oracle Database haalt het hulpprogramma voor gegevensbronregistratie\_de\_kolom OPMERKINGEN uit de weergave ALLE TAB-opmerkingen.

Naast de beschrijvende metagegevens die uit de gegevensbron worden geëxtraheerd, kunnen gebruikers beschrijvende metagegevens invoeren met behulp van het hulpprogramma voor gegevensbronregistratie. Gebruikers kunnen tags toevoegen en ze kunnen experts identificeren voor de objecten die worden geregistreerd. Al deze beschrijvende metadata wordt gekopieerd naar de Data Catalog service samen met de structurele metadata.

## <a name="include-previews"></a>Voorbeelden opnemen
Standaard worden alleen metagegevens uit gegevensbronnen geëxtraheerd en gekopieerd naar de gegevenscatalogusservice, maar het begrijpen van een gegevensbron wordt vaak eenvoudiger wanneer u een voorbeeld van de gegevens die deze bevat bekijken.

Met behulp van het hulpprogramma voor gegevensbronregistratie gegevenscatalogus gegevens, u een momentopnamevoorbeeld van de gegevens in elke tabel opnemen en de weergave die is geregistreerd. Als u ervoor kiest om previews op te nemen tijdens de registratie, bevat het registratiegereedschap maximaal 20 records uit elke tabel en weergave. Deze momentopname wordt vervolgens gekopieerd naar de catalogus, samen met de structurele en beschrijvende metagegevens.

> [!NOTE]
> Brede tabellen met een groot aantal kolommen bevatten mogelijk minder dan 20 records in hun voorbeeld.
>
>

## <a name="include-data-profiles"></a>Gegevensprofielen opnemen
Net zoals het opnemen van previews waardevolle context kan bieden voor gebruikers die zoeken naar gegevensbronnen in Data Catalog, inclusief een gegevensprofiel, kan het gemakkelijker zijn om ontdekte gegevensbronnen te begrijpen.

Met behulp van het hulpprogramma gegevensbronregistratie gegevenscatalogus gegevens, u voor elke tabel en weergave die is geregistreerd een gegevensprofiel opnemen. Als u ervoor kiest om tijdens de registratie een gegevensprofiel op te nemen, bevat het registratieprogramma geaggregeerde statistieken over de gegevens in elke tabel en weergave, waaronder:

* Het aantal rijen en de grootte van de gegevens in het object.
* De datum voor de meest recente update van de gegevens en het objectschema.
* Het aantal null-records en afzonderlijke waarden voor kolommen.
* De minimum-, maximum-, gemiddelde- en standaarddeviatiewaarden voor kolommen.

Deze statistieken worden vervolgens gekopieerd naar de catalogus, samen met de structurele en beschrijvende metadata.

> [!NOTE]
> Tekst- en datumkolommen bevatten geen gemiddelde of standaarddeviatiestatistieken in hun gegevensprofiel.
>
>

## <a name="update-registrations"></a>Registraties bijwerken
Als u een gegevensbron registreert, u deze in gegevenscatalogus registreren wanneer u de metagegevens en de optionele preview gebruikt die tijdens de registratie zijn geëxtraheerd. Als de gegevensbron moet worden bijgewerkt in de catalogus (bijvoorbeeld als het schema van een object is gewijzigd, tabellen die oorspronkelijk zijn uitgesloten, moeten worden opgenomen of als u de gegevens wilt bijwerken die in de voorvertoningen zijn opgenomen), kan het hulpprogramma voor gegevensbronregistratie opnieuw worden uitgevoerd.

Als u een reeds geregistreerde gegevensbron opnieuw registreert, wordt een 'upsert'-bewerking samengevoegd: bestaande objecten worden bijgewerkt en er worden nieuwe objecten gemaakt. Alle metagegevens die door gebruikers worden geleverd via de datacatalogusportal, blijven behouden.

## <a name="summary"></a>Samenvatting
Omdat het structurele en beschrijvende metagegevens kopieert van een gegevensbron naar de catalogusservice, maakt het registreren van de gegevensbron in Gegevenscatalogus de gegevens gemakkelijker te ontdekken en te begrijpen. Nadat u de gegevensbron hebt geregistreerd, u deze annoteren, beheren en ontdekken met behulp van de portal Gegevenscatalogus.

## <a name="next-steps"></a>Volgende stappen
Zie de [zelfstudie Aan de slag met Azure Data Catalog](data-catalog-get-started.md) voor meer informatie over het registreren van gegevensbronnen.

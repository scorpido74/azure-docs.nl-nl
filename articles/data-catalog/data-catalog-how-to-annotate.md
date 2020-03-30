---
title: Gegevensbronnen annoteren in Azure-gegevenscatalogus
description: In het artikel waarin wordt uitgelegd hoe gegevenselementen in Azure Data Catalog kunnen worden annoteren, inclusief vriendelijke namen, tags, beschrijvingen en experts.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c1e022591ce1aee073330055744fbd78d97c0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950264"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Gegevensbronnen annoteren in Azure-gegevenscatalogus

## <a name="introduction"></a>Inleiding

**Microsoft Azure Data Catalog** is een volledig beheerde cloudservice die dient als een systeem van registratie en detectiesysteem voor bedrijfsgegevensbronnen. Met andere woorden, Data Catalog is alles over het helpen van mensen ontdekken, begrijpen en gebruiken van gegevensbronnen, en het helpen van organisaties om meer waarde te halen uit hun bestaande gegevens. Wanneer een gegevensbron is geregistreerd bij Gegevenscatalogus, worden de metagegevens gekopieerd en geïndexeerd door de service, maar het verhaal eindigt daar niet. Data Catalog stelt gebruikers in staat om hun eigen beschrijvende metadata te verstrekken - zoals beschrijvingen en tags - om de metadata uit de gegevensbron aan te vullen en om de gegevensbron begrijpelijker te maken voor meer mensen.

## <a name="annotation-and-crowdsourcing"></a>Annotatie en crowdsourcing
Iedereen heeft een mening. En dit is een goede zaak.
Data Catalog erkent dat verschillende gebruikers verschillende perspectieven hebben op bedrijfsgegevensbronnen en dat elk van deze perspectieven waardevol kan zijn. Denkt u zich het volgende scenario eens in:

* De systeembeheerder kent de servicelevelovereenkomst voor de servers of services die de gegevensbron hosten.
* De databasebeheerder kent het back-upschema voor elke database en de toegestane ETL-verwerkingsvensters.
* De eigenaar van het systeem kent het proces voor gebruikers om toegang te vragen tot de gegevensbron.
* De gegevensbeheerder weet hoe de activa en kenmerken in de gegevensbronmap aan het bedrijfsgegevensmodel worden toegevoegd.
* De analist weet hoe de data wordt gebruikt in de context van de bedrijfsprocessen die zij ondersteunen.

Elk van deze perspectieven is waardevol, en Data Catalog maakt gebruik van een crowdsourcing benadering van metadata waarmee elk kan worden vastgelegd en gebruikt om een compleet beeld van geregistreerde gegevensbronnen te bieden. Met behulp van de portal Gegevenscatalogus kan elke gebruiker zijn eigen annotaties toevoegen en bewerken, terwijl hij aantekeningen van andere gebruikers kan bekijken.

## <a name="different-types-of-annotations"></a>Verschillende soorten annotaties
Gegevenscatalogus ondersteunt de volgende typen annotaties:

| Aantekening | Opmerkingen |
| --- | --- |
| Beschrijvende naam |Vriendelijke namen kunnen worden geleverd op het niveau van gegevensactiva, om de gegevensactiva gemakkelijker te begrijpen. Vriendelijke namen zijn het meest nuttig wanneer de onderliggende objectnaam cryptisch, afgekort of anderszins niet zinvol is voor gebruikers. |
| Beschrijving |Beschrijvingen kunnen worden geleverd op de gegevens asset en attribuut / kolom niveaus. Beschrijvingen zijn korte tekstannotaties in vrije tekst die het perspectief van de gebruiker op het gegevensitem of het gebruik ervan beschrijven. |
| Tags (gebruikerstags) |Tags kunnen worden geleverd op de gegevens asset en attribuut / kolom niveaus. Gebruikerstags zijn door de gebruiker gedefinieerde labels die kunnen worden gebruikt om gegevenselementen of -kenmerken te categoriseren. |
| Tags (woordenlijsttags) |Tags kunnen worden geleverd op de gegevens asset en attribuut / kolom niveaus. Woordenlijsttags zijn centraal gedefinieerde woordenlijsten die kunnen worden gebruikt om gegevenselementen of -kenmerken te categoriseren met behulp van een gemeenschappelijke bedrijfstaxonomie. Zie voor meer informatie [De zakelijke woordenlijst instellen voor Governed Tagging](data-catalog-how-to-business-glossary.md) |
| Deskundigen |Experts kunnen worden geleverd op het niveau van de gegevensactiva. Experts identificeren gebruikers of groepen met deskundige perspectieven op de gegevens en kunnen dienen als aanspreekpunt voor gebruikers die de geregistreerde gegevensbronnen ontdekken en vragen hebben die niet worden beantwoord door de bestaande annotaties. |
| Toegang aanvragen |Toegangsgegevens voor aanvragen kunnen worden verstrekt op het niveau van de gegevensactiva. Deze informatie is voor gebruikers die een gegevensbron ontdekken waarvan ze nog geen toegangsrechten hebben. Gebruikers kunnen het e-mailadres invoeren van de gebruiker of groep die toegang verleent, de URL van het proces of hulpprogramma dat gebruikers nodig hebben om toegang te krijgen, of het proces zelf als tekst kunnen invoeren. |
| Documentatie |Documentatie kan worden verstrekt op het niveau van de gegevensactiva. Asset documentatie is rich text informatie die links en afbeeldingen kan bevatten, en die alle informatie kan bieden die niet wordt overgebracht door middel van beschrijvingen en tags. |

## <a name="annotating-multiple-assets"></a>Meerdere activa annoteren
Wanneer u meerdere gegevenselementen selecteert in de portal Gegevenscatalogus, kunnen gebruikers alle geselecteerde elementen in één bewerking annoteren. Annotaties zijn van toepassing op alle geselecteerde assets, waardoor het eenvoudig is om een consistente beschrijving en reekstags en experts voor gerelateerde gegevenselementen te selecteren en te bieden.

> [!NOTE]
> Tags en experts kunnen ook worden verstrekt bij het registreren van gegevensactiva met behulp van de datacatalogus gegevensbronregistratietool.
>
>

Bij het selecteren van meerdere tabellen en weergaven worden alleen kolommen weergegeven die alle geselecteerde gegevenselementen gemeen hebben, in de portal gegevenscatalogus. Hierdoor kunnen gebruikers tags en beschrijvingen opgeven voor alle kolommen met dezelfde naam voor alle geselecteerde elementen.

## <a name="annotations-and-discovery"></a>Annotaties en ontdekking
Net zoals de metagegevens die tijdens de registratie uit de gegevensbron worden gehaald, worden toegevoegd aan de zoekindex van de gegevenscatalogus, maar ook door de gebruiker geleverde metagegevens geïndexeerd. Dit betekent dat niet alleen annotaties het gemakkelijker maken voor gebruikers om de gegevens te begrijpen die ze ontdekken, maar ook het gemakkelijker maken voor gebruikers om de geannoteerde gegevenselementen te ontdekken door te zoeken met behulp van de termen die voor hen zinvol zijn.

## <a name="summary"></a>Samenvatting
Als u een gegevensbron registreert met Gegevenscatalogus, kunnen gegevens worden ontdekt door structurele en beschrijvende metagegevens van de gegevensbron naar de catalogusservice te kopiëren. Zodra een gegevensbron is geregistreerd, kunnen gebruikers aantekeningen maken om het gemakkelijker te maken om te ontdekken en te begrijpen vanuit de datacatalogusportal.

## <a name="see-also"></a>Zie ook
* Ga aan de slag met de zelfstudie [van azure-gegevenscatalogus](data-catalog-get-started.md) voor stapsgewijze details over het annoteren van gegevensbronnen.

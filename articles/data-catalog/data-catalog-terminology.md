---
title: Azure-gegevenscatalogusterminologie
description: In dit artikel vindt u een inleiding tot concepten en termen die worden gebruikt in de documentatie van Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: d6c813dec3922334f7462b1226ea22371fd5f43b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736290"
---
# <a name="azure-data-catalog-terminology"></a>Azure-gegevenscatalogusterminologie

In dit artikel vindt u een inleiding tot concepten en termen die worden gebruikt in de documentatie van Azure Data Catalog.

## <a name="catalog"></a>Catalogus

De Azure Data Catalog is een cloudgebaseerde metagegevensopslagplaats waarin gegevensbronnen en gegevenselementen kunnen worden geregistreerd. De catalogus dient als een centrale opslaglocatie voor structurele metagegevens geëxtraheerd uit gegevensbronnen en voor beschrijvende metagegevens die door gebruikers zijn toegevoegd.

## <a name="data-source"></a>Gegevensbron

Een gegevensbron is een systeem of container die gegevenselementen beheert. Voorbeelden hiervan zijn SQL Server-databases, Oracle-databases, SQL Server Analysis Services-databases (tabelvormig of multidimensionaal) en SQL Server Reporting Services-servers.

## <a name="data-asset"></a>Gegevensasset

Gegevenselementen zijn objecten in gegevensbronnen die kunnen worden geregistreerd bij de catalogus. Voorbeelden hiervan zijn SQL Server-tabellen en -weergaven, Oracle-tabellen en -weergaven, SQL Server Analysis Services-metingen, dimensies en KPI's en SQL Server Reporting Services-rapporten.

## <a name="data-asset-location"></a>Locatie van gegevensactiva

De catalogus slaat de locatie van een gegevensbron of gegevensasset op, die kan worden gebruikt om verbinding te maken met de bron met behulp van een clienttoepassing. De indeling en details van de locatie variëren afhankelijk van het gegevensbrontype. Een SQL Server-tabel kan bijvoorbeeld worden geïdentificeerd aan de andere kant van de naam van vier delen – servernaam, databasenaam, schemanaam, objectnaam – terwijl een SQL Server Reporting Services-rapport kan worden geïdentificeerd aan de URL.

## <a name="structural-metadata"></a>Structurele metagegevens

Structurele metagegevens zijn de metagegevens die zijn geëxtraheerd uit een gegevensbron die de structuur van een gegevenselement beschrijft. Dit omvat de locatie van de activa, de objectnaam en het type en aanvullende typespecifieke kenmerken. De structurele metagegevens voor tabellen en weergaven bevatten bijvoorbeeld de namen en gegevenstypen voor de kolommen van het object.

## <a name="descriptive-metadata"></a>Beschrijvende metagegevens

Beschrijvende metagegevens zijn metagegevens die het doel of de bedoeling van een gegevenselement beschrijven. Doorgaans worden beschrijvende metagegevens toegevoegd door catalogusgebruikers die de Azure Data Catalog-portal gebruiken, maar deze kunnen ook tijdens de registratie uit de gegevensbron worden geëxtraheerd. Met het hulpprogramma voor azure-gegevenscataloguswordt bijvoorbeeld beschrijvingen geëxtraheerd uit de eigenschap Beschrijving in SQL Server Analysis Services en SQL Server Reporting Services en uit de [ms_description uitgebreide eigenschap](https://technet.microsoft.com/library/ms190243.aspx) in SQL Server-databases, als deze eigenschappen zijn gevuld met waarden.

## <a name="request-access"></a>Toegang aanvragen

De beschrijvende metagegevens van een gegevensitem kunnen informatie bevatten over het aanvragen van toegang tot het gegevensitem of de gegevensbron. Deze informatie wordt gepresenteerd met de locatie van het gegevensactief en kan een of meer van de volgende opties bevatten:

* Het e-mailadres van de gebruiker of het team dat verantwoordelijk is voor het verlenen van toegang tot de gegevensbron.
* De URL van het gedocumenteerde proces dat gebruikers moeten volgen om toegang te krijgen tot de gegevensbron.
* De URL van een hulpprogramma voor identiteits- en toegangsbeheer (zoals Microsoft Identity Manager) die kan worden gebruikt om toegang te krijgen tot de gegevensbron.
* Een gratis tekstvermelding waarin wordt beschreven hoe gebruikers toegang kunnen krijgen tot de gegevensbron.

## <a name="preview"></a>Preview

Een voorbeeld in Azure Data Catalog is een momentopname van maximaal 20 records die tijdens de registratie uit de gegevensbron kunnen worden gehaald en in de catalogus kunnen worden opgeslagen met de metagegevens van gegevensactiva. De preview kan gebruikers die een gegevensitem ontdekken, helpen de functie en het doel ervan beter te begrijpen. Met andere woorden, het zien van voorbeeldgegevens kan waardevoller zijn dan alleen de kolomnamen en gegevenstypen te zien.
Previews worden alleen ondersteund voor tabellen en weergaven en moeten expliciet worden geselecteerd door de gebruiker tijdens de registratie.

## <a name="data-profile"></a>Gegevensprofiel

Een gegevensprofiel in Azure Data Catalog is een momentopname van metagegevens op tabelniveau en kolomniveau over een geregistreerd gegevensitem dat tijdens de registratie uit de gegevensbron kan worden geëxtraheerd en in de catalogus kan worden opgeslagen met de metagegevens van gegevensactiva. Het gegevensprofiel kan gebruikers die een gegevensasset ontdekken, helpen de functie en het doel ervan beter te begrijpen. Net als bij previews moeten gegevensprofielen expliciet worden geselecteerd door de gebruiker tijdens de registratie.

> [!NOTE]
> Het extraheren van een gegevensprofiel kan een kostbare bewerking zijn voor grote tabellen en weergaven en kan de tijd die nodig is om een gegevensbron te registreren aanzienlijk verhogen.


## <a name="user-perspective"></a>Gebruikersperspectief

In Azure Data Catalog kan elke gebruiker beschrijvende metagegevens voor een geregistreerd gegevensitem verstrekken. Elke gebruiker heeft een duidelijk perspectief op de gegevens en het gebruik ervan. De beheerder die verantwoordelijk is voor een server kan bijvoorbeeld de details van de SLA of back-upvensters (Service Level Agreement) of back-upvensters opgeven. een gegevensbeheerder kan links naar documentatie verstrekken voor de bedrijfsprocessen die de gegevens ondersteunen; en een analist kan een beschrijving geven in de termen die het meest relevant zijn voor andere analisten en die het meest waardevol kunnen zijn voor gebruikers die de gegevens moeten ontdekken en begrijpen.

Elk van deze perspectieven is inherent waardevol en met Azure Data Catalog kan elke gebruiker de informatie verstrekken die voor hen relevant is, terwijl alle gebruikers die informatie kunnen gebruiken om de gegevens en het doel ervan te begrijpen.

## <a name="expert"></a>Expert

Een expert is een gebruiker die is geïdentificeerd als met een geïnformeerde "expert" perspectief voor een data-asset. Elke gebruiker kan zichzelf of een andere gebruiker toevoegen als expert voor een asset. Als u als expert wordt vermeld, worden er geen extra bevoegdheden in Azure Data Catalog weergegeven. het stelt gebruikers in staat om gemakkelijk die perspectieven te vinden die waarschijnlijk nuttig zijn bij het bekijken van de beschrijvende metadata van een asset.

## <a name="owner"></a>Eigenaar

Een eigenaar is een gebruiker die extra bevoegdheden heeft voor het beheren van een gegevensitem in Azure Data Catalog. Gebruikers kunnen eigenaar worden van geregistreerde gegevensactiva en eigenaren kunnen andere gebruikers toevoegen als mede-eigenaars. Zie [Gegevensassets beheren voor](data-catalog-how-to-manage.md) meer informatie  

> [!NOTE]
> Eigendom en beheer zijn alleen beschikbaar in de Standaardeditie van Azure Data Catalog.

## <a name="registration"></a>Registratie

Registratie is de handeling van het extraheren van gegevensasset metadata uit een gegevensbron en kopiëren naar de Azure Data Catalog service. Gegevensactiva die zijn geregistreerd, kunnen vervolgens worden geannoteerd en ontdekt.

## <a name="next-steps"></a>Volgende stappen

[Snelstart: een Azure-gegevenscatalogus maken](data-catalog-get-started.md) 

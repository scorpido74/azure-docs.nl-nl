---
title: Verbinding maken met gegevensbronnen in Azure-gegevenscatalogus
description: In het artikel waarin wordt uitgelegd hoe u verbinding maken met gegevensbronnen die zijn ontdekt met Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1190a0f34206004b72730a6af85bbe5db7d9961a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976770"
---
# <a name="how-to-connect-to-data-sources"></a>Verbinding maken met gegevensbronnen
## <a name="introduction"></a>Inleiding
**Microsoft Azure Data Catalog** is een volledig beheerde cloudservice die dient als een systeem van registratie en detectiesysteem voor bedrijfsgegevensbronnen. Met andere woorden, **Azure Data Catalog** is alles over het helpen van mensen ontdekken, begrijpen en gebruiken van gegevensbronnen, en het helpen van organisaties om meer waarde te halen uit hun bestaande gegevens. Een belangrijk aspect van dit scenario is het gebruik van de gegevens - zodra een gebruiker een gegevensbron ontdekt en het doel ervan begrijpt, is de volgende stap om verbinding te maken met de gegevensbron om zijn gegevens te gebruiken.

## <a name="data-source-locations"></a>Gegevensbronlocaties
Tijdens de registratie van gegevensbronnen ontvangt **Azure Data Catalog** metagegevens over de gegevensbron. Deze metagegevens bevatten de details van de locatie van de gegevensbron. De details van de locatie variëren van gegevensbron tot gegevensbron, maar het zal altijd de informatie bevatten die nodig is om verbinding te maken. De locatie voor een SQL Server-tabel bevat bijvoorbeeld de servernaam, databasenaam, schemanaam en tabelnaam, terwijl de locatie voor een SQL Server Reporting Services-rapport de servernaam en het pad naar het rapport bevat. Andere gegevensbrontypen hebben locaties die de structuur en mogelijkheden van het bronsysteem weerspiegelen.

## <a name="integrated-client-tools"></a>Geïntegreerde clienttools
De eenvoudigste manier om verbinding te maken met een gegevensbron is het gebruik van de "Open in..." menu in de **Azure Data Catalog-portal.** In dit menu wordt een lijst met opties weergegeven voor verbinding maken met het geselecteerde gegevenselement.
Wanneer u de standaardtegelweergave gebruikt, is dit menu beschikbaar op elke tegel.

 ![Een SQL Server-tabel openen in Excel vanuit de tegel gegevensasset](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

Wanneer u de lijstweergave gebruikt, is het menu beschikbaar in de zoekbalk boven aan het portalvenster.

 ![Een SQL Server Reporting Services-rapport openen in Rapportbeheer](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

## <a name="supported-client-applications"></a>Ondersteunde clienttoepassingen
Bij gebruik van de "Open in..." menu voor gegevensbronnen in de Azure Data Catalog-portal, moet de juiste clienttoepassing op de clientcomputer worden geïnstalleerd.

| Openen in toepassing | Bestandsextensie /protocol | Ondersteunde toepassingsversies |
| --- | --- | --- |
| Excel |.odc |Excel 2010 of hoger |
| Excel (Top 1000) |.odc |Excel 2010 of hoger |
| Power Query |.xlsx |Excel 2016 of Excel 2010 of Excel 2013 met de Power Query voor Excel-invoegtoepassing geïnstalleerd |
| Power BI Desktop |.pbix |Power BI Desktop juli 2016 of hoger |
| SQL Server Data Tools |vsweb:// |Visual Studio 2013 Update 4 of hoger met SQL Server-tooling geïnstalleerd |
| Rapportbeheer |http:// |Zie [browservereisten voor SQL Server Reporting Services](https://technet.microsoft.com/library/ms156511.aspx) |

## <a name="your-data-your-tools"></a>Uw gegevens, uw tools
De opties die beschikbaar zijn in het menu zijn afhankelijk van het type gegevenselement dat momenteel is geselecteerd. Natuurlijk zullen niet alle mogelijke tools worden opgenomen in de "Open in..." menu, maar het is nog steeds gemakkelijk om verbinding te maken met de gegevensbron met behulp van een client tool. Wanneer een gegevenselement is geselecteerd in de **Azure Data Catalog-portal,** wordt de volledige locatie weergegeven in het eigenschappenvenster.

 ![Verbindingsgegevens voor een SQL Server-tabel](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

De details van de verbindingsgegevens verschillen van gegevensbrontype tot gegevensbrontype, maar de informatie in de portal geeft u alles wat u nodig hebt om verbinding te maken met de gegevensbron in een clienttool. Gebruikers kunnen de verbindingsgegevens kopiëren voor de gegevensbronnen die ze hebben ontdekt met **Azure Data Catalog,** zodat ze kunnen werken met de gegevens in hun tool naar keuze.

## <a name="connecting-and-data-source-permissions"></a>Machtigingen voor het verbinden en gegevensbron
Hoewel **Azure Data Catalog** gegevensbronnen vindbaar maakt, blijft de toegang tot de gegevens zelf onder de controle van de eigenaar of beheerder van de gegevensbron. Het ontdekken van een gegevensbron in **Azure Data Catalog** geeft een gebruiker geen machtigingen om toegang te krijgen tot de gegevensbron zelf.

Gebruikers die een gegevensbron ontdekken, maar geen toestemming hebben om toegang te krijgen tot de gegevens, kunnen informatie verstrekken in de eigenschap Toegang aanvragen wanneer ze een gegevensbron annoteren. Informatie die hier wordt verstrekt – inclusief links naar het proces of aanspreekpunt voor het verkrijgen van toegang tot gegevensbronnen – wordt gepresenteerd naast de locatiegegevens van de gegevensbron in het portaal.

 ![Verbindingsinformatie met instructies voor aanvraagtoegang](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

## <a name="summary"></a>Samenvatting
Als u een gegevensbron registreert met **Azure Data Catalog,** kunnen gegevens worden ontdekt door structurele en beschrijvende metagegevens van de gegevensbron naar de catalogusservice te kopiëren. Zodra een gegevensbron is geregistreerd en ontdekt, kunnen gebruikers verbinding maken met de gegevensbron via de **Azure Data Catalog-portal** 'Openen in...' menu of met behulp van hun data tools naar keuze.

## <a name="see-also"></a>Zie ook
* Ga aan de slag met de zelfstudie [van azure-gegevenscatalogus](data-catalog-get-started.md) voor stapsgewijze details over het maken van verbinding met gegevensbronnen.

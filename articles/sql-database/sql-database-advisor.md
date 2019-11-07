---
title: Aanbevelingen voor prestaties-Azure SQL Database
description: Azure SQL Database biedt aanbevelingen voor uw SQL-data bases waarmee de prestaties van de huidige query kunnen worden verbeterd.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: de6ddd8886f8cadafa57724a5d5e33a3ba4e3c74
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691248"
---
# <a name="performance-recommendations-for-sql-database"></a>Prestatie aanbevelingen voor SQL Database

Azure SQL Database leren en aanpassen met uw toepassing. Het biedt aangepaste aanbevelingen waarmee u de prestaties van uw SQL-data bases kunt maximaliseren. SQL Database doorlopend de gebruiks geschiedenis van uw SQL-data bases beoordelen en analyseren. De aanbevelingen die worden gegeven, zijn gebaseerd op data base-unieke werkbelasting patronen en helpen de prestaties te verbeteren.

> [!TIP]
> [Automatisch afstemmen](sql-database-automatic-tuning.md) is de aanbevolen methode voor het automatisch afstemmen van een aantal van de meest voorkomende prestatie problemen met de data base. [Query performance Insights](sql-database-query-performance.md) is de aanbevolen methode voor basis vereisten voor de bewaking van de prestaties van Azure SQL database. [Azure SQL-analyse](../azure-monitor/insights/azure-sql.md) is de aanbevolen methode voor geavanceerde bewaking van database prestaties op schaal, met ingebouwde intelligentie voor het oplossen van problemen met automatische prestaties.
>

## <a name="create-index-recommendations"></a>Aanbevelingen voor index maken
SQL Database bewaakt voortdurend de query's die worden uitgevoerd en identificeert de indexen waarmee de prestaties kunnen worden verbeterd. Wanneer er voldoende betrouw baarheid is dat een bepaalde index ontbreekt, wordt een nieuwe aanbeveling voor **Create Index** gemaakt.

 Azure SQL Database bouwt de betrouw baarheid door de prestaties van de index in de loop van de tijd te schatten. Afhankelijk van de geschatte prestatie verbetering worden aanbevelingen gecategoriseerd als hoog, gemiddeld of laag. 

Indexen die worden gemaakt met behulp van aanbevelingen, worden altijd gemarkeerd als indexen die automatisch worden gemaakt. U kunt zien welke indexen automatisch worden gemaakt door te kijken naar de weer gave sys. Indexes. Automatisch gemaakte indexen blok keren/wijzigen niet. 

Als u probeert de kolom met een automatisch gemaakte index te verwijderen, wordt de opdracht door gegeven. De automatisch gemaakte index wordt ook met de opdracht verwijderd. Met reguliere indexen wordt de opdracht ALTER/RENAME voor geïndexeerde kolommen geblokkeerd.

Nadat de aanbeveling voor het maken van een index is toegepast, vergelijkt Azure SQL Database de prestaties van de query's met de basislijn prestaties. Als de nieuwe index betere prestaties heeft, wordt de aanbeveling als geslaagd gemarkeerd en is het impact rapport beschikbaar. Als de index geen prestaties heeft verbeterd, wordt deze automatisch hersteld. SQL Database gebruikt dit proces om ervoor te zorgen dat aanbevelingen de database prestaties verbeteren.

Een aanbeveling voor het maken van een index heeft een **uitstel** beleid dat het Toep assen van de aanbeveling niet toestaat als het resource gebruik van een Data Base of groep hoog is. Het back-out-beleid houdt rekening met CPU, data IO, logboek-IO en beschik bare opslag. 

Als CPU, data IO of logboek-IO hoger is dan 80% in de afgelopen 30 minuten, wordt de aanbeveling Create Index uitgesteld. Als de beschik bare opslag lager is dan 10% nadat de index is gemaakt, wordt de aanbeveling een fout status. Als, na een paar dagen, de automatische afstemming nog steeds meent dat de index nuttig zou zijn, wordt het proces opnieuw gestart. 

Dit proces wordt herhaald totdat er voldoende beschik bare opslag ruimte is om een index te maken, of totdat de index niet meer wordt gezien als nuttig.

## <a name="drop-index-recommendations"></a>Aanbevelingen voor drop index
Naast het detecteren van ontbrekende indexen analyseert SQL Database voortdurend de prestaties van bestaande indexen. Als een index niet wordt gebruikt, is het Azure SQL Database aan te raden deze te verwijderen. Het verwijderen van een index wordt in twee gevallen aanbevolen:
* De index is een duplicaat van een andere index (dezelfde geïndexeerde en opgenomen kolom, partitie schema en filters).
* De index is niet gebruikt voor een lange periode (93 dagen).

De aanbevelingen voor het verwijderen van de index passeren ook de verificatie na de implementatie. Als de prestaties verbeteren, is het impact rapport beschikbaar. Als de prestaties afnemen, wordt de aanbeveling hersteld.


## <a name="parameterize-queries-recommendations"></a>Aanbevelingen voor para meters query's
Aanbevelingen voor *para meters-query's* worden weer gegeven wanneer u een of meer query's hebt die voortdurend worden gecompileerd, maar wel met hetzelfde query-uitvoerings plan. Met deze voor waarde wordt een kans gemaakt om geforceerde parameterisering toe te passen. Met geforceerde parameterisering kunnen query plannen in de cache worden opgeslagen en opnieuw worden gebruikt, waardoor de prestaties worden verbeterd en het resource gebruik wordt verminderd. 

Elke query die wordt uitgegeven op basis van SQL Server moet eerst worden gecompileerd voor het genereren van een uitvoerings plan. Elk gegenereerd plan wordt toegevoegd aan de plannings cache. Volgende uitvoeringen van dezelfde query kunnen dit plan opnieuw gebruiken uit de cache, waardoor er geen verdere compilatie meer nodig is. 

Query's met niet-geparametriseerde waarden kunnen leiden tot prestatie overhead, omdat het uitvoerings plan opnieuw wordt gecompileerd telkens wanneer de niet-geparametriseerde waarden verschillend zijn. In veel gevallen genereren dezelfde query's met verschillende parameter waarden dezelfde uitvoerings plannen. Deze plannen worden echter nog steeds afzonderlijk toegevoegd aan de plannings cache. 

Het proces van het opnieuw compileren van uitvoerings plannen maakt gebruik van database resources, verhoogt de duur van de query en overschrijdt de plannings cache. Deze gebeurtenissen zorgen ervoor dat er op zijn beurt plannen uit de cache worden verwijderd. Dit SQL Server gedrag kan worden gewijzigd door de optie geforceerde parameterisering in de data base in te stellen. 

Om u te helpen bij het schatten van de impact van deze aanbeveling, krijgt u een vergelijking van het werkelijke CPU-gebruik en het verwachte CPU-gebruik (alsof de aanbeveling is toegepast). Deze aanbeveling kan u helpen CPU-besparingen te realiseren. Het kan ook helpen de duur en overhead van de query te verlagen voor de plannings cache, wat betekent dat meer van de plannen in de cache kunnen blijven en opnieuw worden gebruikt. U kunt deze aanbeveling snel Toep assen door de opdracht **Toep assen** te selecteren. 

Nadat u deze aanbeveling hebt toegepast, wordt geforceerde parameterisering binnen enkele minuten in uw data base ingeschakeld. Het bewakings proces wordt gestart. dit duurt ongeveer 24 uur. Na deze periode kunt u het validatie rapport bekijken. In dit rapport wordt het CPU-gebruik van uw data base 24 uur voor en na het Toep assen van de aanbeveling weer gegeven. SQL Database Advisor heeft een veiligheids mechanisme waarmee automatisch de toegepaste aanbeveling wordt hersteld als er prestatie regressie is gedetecteerd.

## <a name="fix-schema-issues-recommendations-preview"></a>Aanbevelingen voor het oplossen van schema problemen (preview-versie)

> [!IMPORTANT]
> Micro soft maakt momenteel geen aanbevelingen voor het oplossen van schema problemen. We raden u aan [intelligent Insights](sql-database-intelligent-insights.md) te gebruiken voor het bewaken van de prestatie problemen met uw data base, zoals schema problemen die eerder zijn besproken in de aanbevelingen voor het oplossen van het schema probleem.
> 

Aanbevelingen voor **schema problemen oplossen** wordt weer gegeven wanneer de SQL database-service een afwijking van het aantal aan schema gerelateerde SQL-fouten in het SQL database ondervindt. Deze aanbeveling wordt doorgaans weer gegeven wanneer in uw data base meerdere schema fouten optreden (ongeldige kolom naam, ongeldige object naam, enzovoort) binnen een uur.

"Schema problemen" zijn een klasse syntaxis fouten in SQL Server. Ze treden op wanneer de definitie van de SQL-query en de definitie van het database schema niet zijn uitgelijnd. Een van de kolommen die door de query wordt verwacht, ontbreekt mogelijk in de doel tabel of vice versa. 

De aanbeveling schema probleem oplossen wordt weer gegeven wanneer de Azure SQL Database-service een afwijking van het aantal aan schema gerelateerde SQL-fouten op uw SQL database noteert. De volgende tabel bevat de fouten die betrekking hebben op schema problemen:

| SQL-fout code | Bericht |
| --- | --- |
| 201 |De procedure of functie*verwacht para meter*, maar deze is niet opgegeven. |
| 207 |Ongeldige kolom naam *. |
| 208 |Ongeldige object naam ' * '. |
| 213 |Kolom naam of-nummer van opgegeven waarden komt niet overeen met tabel definitie. |
| 2812 |Kan opgeslagen procedure ' * ' niet vinden. |
| 8144 |Er zijn te veel argumenten opgegeven voor de procedure of functie *. |

## <a name="custom-applications"></a>Aangepaste toepassingen

Ontwikkel aars kunnen overwegen aangepaste toepassingen te ontwikkelen met behulp van prestatie aanbevelingen voor Azure SQL Database. Alle aanbevelingen die in de portal voor een Data Base worden vermeld, zijn toegankelijk via de [Get-AzSqlDatabaseRecommendedAction-](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API.

## <a name="next-steps"></a>Volgende stappen
Controleer uw aanbevelingen en pas deze toe om de prestaties te verfijnen. Data base-workloads zijn dynamisch en veranderen voortdurend. SQL Database Advisor blijft controleren en aanbevelingen bieden waarmee u de prestaties van uw data base kunt verbeteren. 

* Zie [Azure SQL database Automatic tuning](sql-database-automatic-tuning.md)(Engelstalig) voor meer informatie over het automatisch afstemmen van database indexen en uitvoer plannen voor query's.
* Zie [Azure SQL intelligent Insights](sql-database-intelligent-insights.md)voor meer informatie over het automatisch bewaken van database prestaties met automatische diagnose en de oorzaak van prestatie problemen.
*  Zie voor meer informatie over het gebruik van prestatie aanbevelingen in het Azure Portal [prestatie aanbevelingen in de Azure Portal](sql-database-advisor-portal.md).
* Zie [query performance Insights](sql-database-query-performance.md) voor meer informatie over de invloed van de prestaties van uw meest voorkomende query's en weer geven.



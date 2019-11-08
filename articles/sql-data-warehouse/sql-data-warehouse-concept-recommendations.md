---
title: Aanbevelingen voor SQL Data Warehouse
description: Meer informatie over SQL Data Warehouse aanbevelingen en hoe deze worden gegenereerd
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1a93339b99c6591ee9ed615c032d0eac58a372fe
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827505"
---
# <a name="sql-data-warehouse-recommendations"></a>Aanbevelingen voor SQL Data Warehouse

In dit artikel worden de aanbevelingen beschreven die worden geleverd door SQL Data Warehouse via Azure Advisor.  

SQL Data Warehouse biedt aanbevelingen om ervoor te zorgen dat uw data warehouse consistent wordt geoptimaliseerd voor prestaties. Aanbevelingen voor data warehouses zijn nauw geïntegreerd met [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) om u de best practices direct binnen de [Azure Portal](https://aka.ms/Azureadvisor)te bieden. SQL Data Warehouse analyseert de huidige status van uw data warehouse, verzamelt telemetrie en oppervlak aanbevelingen voor uw actieve workload op een dagelijks uitgebracht. De ondersteunde aanbevelingen voor data warehouses worden hieronder beschreven, samen met het Toep assen van aanbevolen acties.

Als u feedback hebt over de SQL Data Warehouse Advisor of als u problemen ondervindt, kunt u contact met [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com)opdoen.   

Klik [hier](https://aka.ms/Azureadvisor) om uw aanbevelingen vandaag nog te controleren. Deze functie is momenteel alleen van toepassing op Gen2-data warehouses. 

## <a name="data-skew"></a>Gegevens scheef trekken

Gegevens scheefheid kan leiden tot extra gegevens bewegingen of bron knelpunten wanneer uw werk belasting wordt uitgevoerd. In de volgende documentatie wordt beschreven hoe u het scheef trekken van gegevens identificeert en voor komt dat dit gebeurt door een optimale distributie sleutel te selecteren.

- [Scheefheid identificeren en verwijderen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Geen of verouderde statistieken

Het hebben van een suboptimale statistieken kan een grote invloed hebben op de query prestaties, omdat hierdoor de SQL Data Warehouse query optimalisatie kan zorgen voor het genereren van suboptimale query plannen. De volgende documentatie bevat informatie over de aanbevolen procedures voor het maken en bijwerken van statistieken:

- [Tabel statistieken maken en bijwerken](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Voer het volgende [T-SQL-script](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)uit om de lijst met betrokken tabellen te bekijken met deze aanbevelingen. Advisor voert continu hetzelfde T-SQL-script uit om deze aanbevelingen te genereren.

## <a name="replicate-tables"></a>Tabellen repliceren

Voor de aanbevelingen van een gerepliceerde tabel, adviseur detecteert de kandidaten van een tabel op basis van de volgende fysieke kenmerken:

- Grootte van gerepliceerde tabel
- Aantal kolommen
- Type tabel distributie
- Aantal partities

Advisor maakt continu gebruik van heuristiek op basis van werk belastingen, zoals de frequentie van tabel toegang, rijen die gemiddeld worden geretourneerd en drempel waarden rond Data Warehouse-grootte en-activiteit, om ervoor te zorgen dat aanbevelingen van hoge kwaliteit worden gegenereerd. 

Hieronder vindt u een beschrijving van de heuristische op basis van werk belastingen die u kunt vinden in de Azure Portal voor elke gerepliceerde tabel aanbeveling:

- Controle Gem: het gemiddelde aantal rijen dat is geretourneerd uit de tabel voor elke toegang tot de tabel gedurende de afgelopen zeven dagen
- Veelvuldig gelezen: geen update: geeft aan dat de tabel in de afgelopen zeven dagen niet is bijgewerkt terwijl de toegangs activiteit wordt weer gegeven
- Verhouding van lezen/bijwerken: de verhouding van hoe vaak de tabel is geopend ten opzichte van wanneer deze in de afgelopen zeven dagen wordt bijgewerkt
- Activiteit: Hiermee wordt het gebruik gemeten op basis van de toegangs activiteit. Hiermee wordt de activiteit voor de tabel toegang vergeleken met de gemiddelde activiteit van de tabel toegang in het data warehouse in de afgelopen zeven dagen. 

Momenteel worden in Advisor alleen Maxi maal vier gerepliceerde tabel kandidaten tegelijk met geclusterde column Store-indexen met de prioriteit van de hoogste activiteit weer gegeven.

> [!IMPORTANT]
> De aanbeveling van de gerepliceerde tabel is geen volledige controle en houdt geen rekening met het verplaatsen van gegevens. We werken eraan om dit als een heuristisch toe te voegen, maar ondertussen moet u uw workload altijd valideren nadat u de aanbeveling hebt toegepast. Neem contact op met sqldwadvisor@service.microsoft.com als u de aanbevelingen van gerepliceerde tabellen detecteert die ervoor zorgen dat uw werk belasting Regress. Raadpleeg de volgende [documentatie](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table)voor meer informatie over gerepliceerde tabellen.

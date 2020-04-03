---
title: Synapse SQL-aanbevelingen
description: Meer informatie over Synapse SQL-aanbevelingen en hoe deze worden gegenereerd
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: c4bbd98cc28b242be5310fab76521a80de8fcb7c
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584126"
---
# <a name="synapse-sql-recommendations"></a>Synapse SQL-aanbevelingen

In dit artikel worden de Synapse SQL-aanbevelingen beschreven die via Azure Advisor worden weergegeven.  

Synapse SQL geeft aanbevelingen om ervoor te zorgen dat uw werkbelasting voor gegevensmagazijnen consistent is geoptimaliseerd voor prestaties. Aanbevelingen zijn nauw geïntegreerd met [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) om u direct binnen de [Azure-portal](https://aka.ms/Azureadvisor)van best practices te voorzien. Synapse SQL verzamelt telemetrie en brengt aanbevelingen voor uw actieve werkbelasting op een dagelijkse cadans. De ondersteunde aanbevelingsscenario's worden hieronder beschreven, samen met hoe aanbevolen acties kunnen worden toegepast.

U uw aanbevelingen vandaag [nog bekijken!](https://aka.ms/Azureadvisor) Momenteel is deze functie alleen van toepassing op Gen2-gegevensmagazijnen. 

## <a name="data-skew"></a>Gegevensscheeftrekking

Het scheeftrekken van gegevens kan leiden tot extra gegevensverplaatsing of knelpunten in de bron bij het uitvoeren van uw werkbelasting. De volgende documentatie beschrijft laten zien om gegevens scheeftrekking te identificeren en te voorkomen dat het gebeurt door het selecteren van een optimale distributiesleutel.

- [Scheeftrekken identificeren en verwijderen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Geen of verouderde statistieken

Het hebben van suboptimale statistieken kan ernstige gevolgen hebben voor de queryprestaties, omdat dit ertoe kan leiden dat de SQL-queryoptimizer suboptimale queryplannen genereert. In de volgende documentatie worden de aanbevolen procedures beschreven rond het maken en bijwerken van statistieken:

- [Tabelstatistieken maken en bijwerken](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Als u de lijst met beïnvloede tabellen door deze aanbevelingen wilt weergeven, voert u het volgende [T-SQL-script](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)uit. Advisor voert continu hetzelfde T-SQL-script uit om deze aanbevelingen te genereren.

## <a name="replicate-tables"></a>Tabellen repliceren

Voor gerepliceerde tabelaanbevelingen detecteert Advisor tabelkandidaten op basis van de volgende fysieke kenmerken:

- Gerepliceerde tabelgrootte
- Aantal kolommen
- Tabeldistributietype
- Aantal partities

Advisor maakt continu gebruik van op workloads gebaseerde heuristiek, zoals de frequentie van tabeltoegang, rijen die gemiddeld worden geretourneerd en drempels rond de grootte van het gegevensmagazijn en de activiteit om ervoor te zorgen dat aanbevelingen van hoge kwaliteit worden gegenereerd. 

In de volgende beschrijving van op workloads gebaseerde heuristiek die u vinden in de Azure-portal voor elke gerepliceerde tabelaanbeveling:

- Scan avg- het gemiddelde percentage rijen dat in de afgelopen zeven dagen van de tabel is geretourneerd voor elke tabeltoegang
- Veelvuldig lezen, geen update - geeft aan dat de tabel niet is bijgewerkt in de afgelopen zeven dagen, terwijl het weergeven van toegang activiteit
- Lees/update-verhouding - de verhouding van hoe vaak de tabel is benaderd ten opzichte van wanneer deze in de afgelopen zeven dagen wordt bijgewerkt
- Activiteit - meet het gebruik op basis van toegangsactiviteit. Hiermee wordt de activiteit voor tabeltoegang vergeleken met de gemiddelde activiteit voor tabeltoegang in het gegevensmagazijn van de afgelopen zeven dagen. 

Momenteel toont Advisor hooguit vier gerepliceerde tabelkandidaten tegelijk met geclusterde kolomarchiefindexen die prioriteit geven aan de hoogste activiteit.

> [!IMPORTANT]
> De aanbeveling van de gerepliceerde tabel is niet volledig bewijs en houdt geen rekening met gegevensverplaatsingsbewerkingen. We werken eraan om dit toe te voegen als een heuristische, maar in de tussentijd moet u altijd uw werklast valideren na het toepassen van de aanbeveling. Neem sqldwadvisor@service.microsoft.com contact op als u gerepliceerde tabelaanbevelingen ontdekt waardoor uw werkbelasting achteruitgaat. Ga voor meer informatie over gerepliceerde tabellen naar de volgende [documentatie](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table).

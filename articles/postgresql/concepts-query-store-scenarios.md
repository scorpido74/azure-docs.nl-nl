---
title: Query Store-scenario's - Azure Database voor PostgreSQL - Single Server
description: In dit artikel worden enkele scenario's beschreven voor de Query Store in Azure Database voor PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31e3f82b6ea1b1fc15c0832dc03edce2a59f1e1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768347"
---
# <a name="usage-scenarios-for-query-store"></a>Gebruiksscenario's voor queryarchief

**Geldt voor:** Azure Database voor PostgreSQL - Single Server-versies 9.6, 10, 11

U Query Store gebruiken in een breed scala aan scenario's waarin het bijhouden en onderhouden van voorspelbare werkbelastingprestaties van cruciaal belang is. Bekijk de volgende voorbeelden: 
- Topdure query's identificeren en afstemmen 
- A/B-testen 
- Prestaties stabiel houden tijdens upgrades 
- Ad hoc workloads identificeren en verbeteren 

## <a name="identify-and-tune-expensive-queries"></a>Dure query's identificeren en afstemmen 

### <a name="identify-longest-running-queries"></a>Langstlopende query's identificeren 
Gebruik de weergave [Queryprestatieinsight](concepts-query-performance-insight.md) in de Azure-portal om snel de langstlopende query's te identificeren. Deze query's hebben meestal de neiging om een aanzienlijke hoeveelheid resources te verbruiken. Het optimaliseren van uw langstlopende vragen kan de prestaties verbeteren door resources vrij te maken voor gebruik door andere query's die op uw systeem worden uitgevoerd. 

### <a name="target-queries-with-performance-deltas"></a>Doelquery's met prestatiedelta's 
Query Store snijdt de prestatiegegevens in tijdvensters, zodat u de prestaties van een query in de loop van de tijd bijhouden. Zo u precies bepalen welke query's bijdragen aan een toename van de totale besteedtijd. Als gevolg hiervan u gerichte probleemoplossing van uw werkbelasting uitvoeren.

### <a name="tuning-expensive-queries"></a>Dure query's afstemmen 
Wanneer u een query identificeert met suboptimale prestaties, is de actie die u onderneemt afhankelijk van de aard van het probleem: 
- Gebruik [prestatieaanbevelingen](concepts-performance-recommendations.md) om te bepalen of er voorgestelde indexen zijn. Zo ja, maakt u de index en gebruikt u queryarchief om de queryprestaties te evalueren nadat de index is gemaakt. 
- Zorg ervoor dat de statistieken up-to-date zijn voor de onderliggende tabellen die door de query worden gebruikt.
- Overweeg dure query's te herschrijven. Profiteer bijvoorbeeld van queryparameterisatie en verminder het gebruik van dynamische SQL. Implementeer optimale logica bij het lezen van gegevens zoals het toepassen van gegevensfiltering aan databasezijde, niet aan toepassingszijde. 


## <a name="ab-testing"></a>A/B-testen 
Gebruik Query Store om de prestaties van de werkbelasting te vergelijken voor en na een wijziging van de toepassing die u wilt introduceren. Voorbeelden van scenario's voor het gebruik van Query Store om de impact van de omgeving of toepassingswijziging in de werkbelastingprestaties te beoordelen: 
- Het uitrollen van een nieuwe versie van een toepassing. 
- Extra resources toevoegen aan de server. 
- Ontbrekende indexen maken op tabellen waarnaar wordt verwezen door dure query's. 
 
Pas in een van deze scenario's de volgende werkstroom toe: 
1. Voer uw werkbelasting uit met Query Store vóór de geplande wijziging om een prestatiebasislijn te genereren. 
2. Pas toepassingswijziging(en) toe op het gecontroleerde moment. 
3. Blijf de werkbelasting lang genoeg uitvoeren om prestatiebeelden van het systeem te genereren na de wijziging. 
4. Vergelijk de resultaten van voor en na de wijziging. 
5. Bepaal of u de wijziging of terugdraaien wilt behouden. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Ad hoc workloads identificeren en verbeteren 
Sommige workloads hebben geen dominante query's die u afstemmen om de algehele prestaties van toepassingen te verbeteren. Deze workloads worden meestal gekenmerkt door een relatief groot aantal unieke query's, elk van hen verbruiken een deel van de systeembronnen. Elke unieke query wordt zelden uitgevoerd, dus individueel is hun runtime-verbruik niet kritisch. Aan de andere kant, gezien het feit dat de toepassing is het genereren van nieuwe query's de hele tijd, een aanzienlijk deel van de systeembronnen wordt besteed aan query compilatie, die niet optimaal is. Meestal treedt deze situatie op als uw toepassing query's genereert (in plaats van opgeslagen procedures of geparameteriseerde query's) of als deze is gebaseerd op objectrelationele toewijzingsframeworks die standaard query's genereren. 
 
Als u de controle hebt over de toepassingscode, u overwegen de gegevenstoegangslaag te herschrijven om opgeslagen procedures of geparameteriseerde query's te gebruiken. Deze situatie kan echter ook worden verbeterd zonder wijzigingen in toepassingen door queryparameterisatie voor de gehele database (alle query's) of voor de afzonderlijke querysjablonen met dezelfde queryhash te forceren. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [aanbevolen procedures voor het gebruik van de Query Store](concepts-query-store-best-practices.md)
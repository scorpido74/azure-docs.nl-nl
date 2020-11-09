---
title: Gegevens die worden verwerkt met behulp van serverloze SQL-groep
description: In dit document wordt beschreven hoe het verwerkte bedrag wordt berekend wanneer u gegevens opvraagt in uw data Lake.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a108e5fdd30c21cdb7771e3f683dad22773653a4
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381198"
---
# <a name="data-processed-by-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Gegevens die worden verwerkt met behulp van serverloze SQL-groep in azure Synapse Analytics

De gegevens die worden *verwerkt* , zijn de hoeveelheid gegevens die het systeem tijdelijk opslaat tijdens het uitvoeren van een query. De gegevens die worden verwerkt, bestaan uit de volgende aantallen:

- Hoeveelheid gegevens die uit de opslag ruimte is gelezen. Dit bedrag omvat:
  - Gegevens lezen tijdens het lezen van gegevens.
  - Gegevens lezen tijdens het lezen van meta gegevens (voor bestands indelingen die meta gegevens bevatten, zoals Parquet).
- Hoeveelheid gegevens in tussenliggende resultaten. Deze gegevens worden overgebracht tussen de knoop punten terwijl de query wordt uitgevoerd. Het bevat de gegevens overdracht naar uw eind punt, in een niet-gecomprimeerde indeling. 
- De hoeveelheid gegevens die naar de opslag wordt geschreven. Als u CETAS gebruikt om uw resultatenset naar opslag te exporteren, wordt de hoeveelheid gegevens die wordt geschreven, toegevoegd aan de hoeveelheid gegevens die wordt verwerkt voor het SELECT-deel van CETAS.

Het lezen van bestanden uit de opslag is zeer geoptimaliseerd. Het proces maakt gebruik van:

- Vooraf ophalen, waardoor enige overhead kan worden toegevoegd aan de hoeveelheid gegevens die moet worden gelezen. Als een query een heel bestand leest, is er geen overhead. Als een bestand gedeeltelijk wordt gelezen, zoals in de eerste N query's, worden er nog meer gegevens gelezen met vooraf ophalen.
- Een geoptimaliseerde parser met door komma's gescheiden waarden (CSV). Als u PARSER_VERSION = ' 2.0 ' gebruikt voor het lezen van CSV-bestanden, nemen de hoeveel heden gegevens die uit de opslag zijn gelezen, enigszins toe. Met een geoptimaliseerde CSV-parser worden bestanden parallel gelezen, in segmenten van gelijke grootte. Segmenten hoeven niet altijd hele rijen te bevatten. Om ervoor te zorgen dat alle rijen worden geparseerd, leest de geoptimaliseerde CSV-parser ook kleine fragmenten van aangrenzende segmenten. Dit proces voegt een kleine hoeveelheid overhead toe.

## <a name="statistics"></a>statistieken

De query optimalisatie van de serverloze SQL-groep is afhankelijk van de statistieken voor het genereren van optimale query-uitvoerings plannen. U kunt statistieken hand matig maken. Anders worden ze door de serverloze SQL-groep automatisch gemaakt. In beide gevallen worden statistieken gemaakt door een afzonderlijke query uit te voeren die een specifieke kolom retourneert met een opgegeven sample frequentie. Aan deze query is een hoeveelheid gegevens verwerkt.

Als u dezelfde of een andere query uitvoert die kan profiteren van gemaakte statistieken, worden indien mogelijk de statistieken opnieuw gebruikt. Er zijn geen aanvullende gegevens verwerkt voor het maken van statistieken.

Wanneer statistieken worden gemaakt voor een kolom Parquet, wordt alleen de relevante kolom gelezen uit bestanden. Wanneer statistieken worden gemaakt voor een CSV-kolom, worden volledige bestanden gelezen en geparseerd.

## <a name="rounding"></a>Afronden

De hoeveelheid verwerkte gegevens wordt afgerond naar de dichtstbijzijnde MB per query. Voor elke query zijn mini maal 10 MB aan gegevens verwerkt.

## <a name="what-data-processed-doesnt-include"></a>Welke gegevens worden verwerkt, is niet inbegrepen

- Meta gegevens op server niveau (zoals aanmeldingen, functies en referenties op server niveau).
- Data bases die u in uw eind punt maakt. Deze data bases bevatten alleen meta gegevens (zoals gebruikers, rollen, schema's, weer gaven, in-line functies voor tabel waarden [TVFs], opgeslagen procedures, referenties voor database bereik, externe gegevens bronnen, externe bestands indelingen en externe tabellen).
  - Als u schema-deinterferentie gebruikt, worden bestands fragmenten gelezen voor het afleiden van kolom namen en gegevens typen, en wordt de hoeveelheid gelezen gegevens toegevoegd aan de hoeveelheid verwerkte gegevens.
- DDL-instructies (Data Definition Language), met uitzonde ring van de instructie CREATE STATISTICs, omdat hiermee gegevens uit de opslag worden verwerkt op basis van het opgegeven steek proef percentage.
- Query's met alleen meta gegevens.

## <a name="reducing-the-amount-of-data-processed"></a>De hoeveelheid verwerkte gegevens verminderen

U kunt de hoeveelheid verwerkte gegevens die per query worden verwerkt, optimaliseren en de prestaties verbeteren door uw gegevens te partitioneren en te converteren naar een gecomprimeerde, op kolom gebaseerd formaat, zoals Parquet.

## <a name="examples"></a>Voorbeelden

Stel drie tabellen voor.

- De tabel population_csv wordt ondersteund door 5 TB aan CSV-bestanden. De bestanden zijn ingedeeld in vijf kolommen met een gelijke grootte.
- De tabel population_parquet heeft dezelfde gegevens als de population_csv tabel. Er wordt een back-up gemaakt van 1 TB Parquet-bestanden. Deze tabel is kleiner dan het vorige bestand omdat de gegevens in de Parquet-indeling zijn gecomprimeerd.
- De very_small_csv tabel wordt ondersteund door 100 KB aan CSV-bestanden.

**Query 1** : Selecteer Sum (populatie) van population_csv

Met deze query worden hele bestanden gelezen en geparseerd om waarden op te halen voor de kolom populatie. Knoop punten verwerken fragmenten van deze tabel en de som van de populatie voor elk fragment wordt overgebracht tussen de knoop punten. Het eind totaal wordt overgedragen naar uw eind punt. 

Met deze query worden 5 TB aan gegevens verwerkt, plus een kleine hoeveelheid overhead voor het overdragen van de som van fragmenten.

**Query 2** : Selecteer Sum (populatie) van population_parquet

Wanneer u gecomprimeerde en op kolommen gebaseerde notaties zoals Parquet doorzoekt, worden er minder gegevens gelezen dan in query 1. U ziet dit resultaat omdat een SQL-pool zonder server een gecomprimeerde kolom leest in plaats van het hele bestand. In dit geval wordt 0,2 TB gelezen. (Vijf kolommen met even grootte zijn elk 0,2 TB.) Knoop punten verwerken fragmenten van deze tabel en de som van de populatie voor elk fragment wordt overgebracht tussen de knoop punten. Het eind totaal wordt overgedragen naar uw eind punt. 

Deze query verwerkt 0,2 TB plus een kleine hoeveelheid overhead voor het overdragen van de som van fragmenten.

**Query 3** : Select * from population_parquet

Met deze query worden alle kolommen gelezen en worden alle gegevens overgebracht naar een niet-gecomprimeerde indeling. Als de compressie-indeling 5:1 is, verwerkt de query 6 TB, omdat deze 1 TB leest en vijf TB aan ongecomprimeerde gegevens overdraagt.

**Query 4** : aantal selecteren (*) van very_small_csv

Met deze query worden hele bestanden gelezen. De totale grootte van de bestanden in de opslag voor deze tabel is 100 KB. Knoop punten verwerken fragmenten van deze tabel en de som voor elk fragment wordt tussen knoop punten overgedragen. Het eind totaal wordt overgedragen naar uw eind punt. 

Met deze query worden iets meer dan 100 KB aan gegevens verwerkt. De hoeveelheid gegevens die voor deze query wordt verwerkt, wordt tot 10 MB afgerond, zoals is opgegeven in de sectie [afronding](#rounding) van dit artikel.

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor serverloze SQL-groepen](best-practices-sql-on-demand.md)voor meer informatie over het optimaliseren van uw query's voor prestaties.

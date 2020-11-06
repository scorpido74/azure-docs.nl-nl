---
title: Gegevens die worden verwerkt met een serverloze SQL-groep
description: In dit document wordt beschreven hoe gegevens die worden verwerkt, worden berekend bij het uitvoeren van query's in azure Storage met serverloze SQL-pool.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 06eb02aa3dd4d5fc8bd3605dac480d5afa52d5fa
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424029"
---
# <a name="data-processed-with-serverless-sql-pool-in-azure-synapse-analytics"></a>Gegevens die worden verwerkt met een serverloze SQL-groep in azure Synapse Analytics

Verwerkte gegevens is de hoeveelheid gegevens die tijdelijk in het systeem is opgeslagen tijdens het uitvoeren van een query en bestaat uit:

- Hoeveelheid gegevens die uit de opslag ruimte is gelezen. Dit omvat:
  - Hoeveelheid gegevens die gelezen is tijdens het lezen van gegevens
  - Hoeveelheid gegevens die gelezen wordt tijdens het lezen van meta gegevens (voor bestands indelingen die meta gegevens bevatten, zoals Parquet)
- Hoeveelheid gegevens in tussenliggende resultaten: gegevens die worden overgebracht tussen knoop punten tijdens het uitvoeren van query's, met inbegrip van gegevens overdracht naar uw eind punt, in niet-gecomprimeerde vorm. 
- Hoeveelheid gegevens die naar de opslag wordt geschreven: als u CETAS gebruikt om uw resultatenset naar opslag te exporteren, worden er in rekening gebracht voor geschreven bytes en de hoeveelheid gegevens die wordt verwerkt voor het SELECT-deel van CETAS.

Het lezen van bestanden uit de opslag is zeer geoptimaliseerd en maakt gebruik van:

- Prefetche: Hiermee kan een kleine overhead worden toegevoegd aan de hoeveelheid gegevens die moet worden gelezen. Als een query een volledig bestand leest, is er geen overhead. Als een bestand gedeeltelijk wordt gelezen, zoals bij de eerste N query's, worden er meer gegevens gelezen met vooraf ophalen.
- Geoptimaliseerde CSV-parser: als u PARSER_VERSION = ' 2.0 ' gebruikt voor het lezen van CSV-bestanden, resulteert dit in iets verhoogde hoeveelheid gegevens die uit de opslag is gelezen.  Geoptimaliseerde CSV-parser leest bestanden parallel in segmenten van gelijke grootte. Er is geen garantie dat segmenten hele rijen bevatten. Om ervoor te zorgen dat alle rijen worden geparseerd, worden er ook kleine fragmenten van aangrenzende segmenten gelezen en wordt er een kleine hoeveelheid overhead toegevoegd.

## <a name="statistics"></a>statistieken

De query optimalisatie van de serverloze SQL-groep is afhankelijk van de statistieken voor het genereren van optimale query-uitvoerings plannen. U kunt statistieken hand matig maken of ze worden automatisch gemaakt door de serverloze SQL-pool. In beide gevallen worden statistieken gemaakt door een afzonderlijke query uit te voeren die een specifieke kolom retourneert met de opgegeven sample frequentie. Aan deze query is een hoeveelheid gegevens verwerkt.

Als u dezelfde of een andere query uitvoert die kan profiteren van gemaakte statistieken, worden indien mogelijk statistieken opnieuw gebruikt en worden er geen extra gegevens verwerkt voor het maken van statistieken.

Het maken van statistieken voor een kolom Parquet resulteert in het lezen van alleen de relevante kolom van bestanden. Het maken van statistieken voor een CSV-kolom leidt ertoe dat hele bestanden worden gelezen en geparseerd.

## <a name="rounding"></a>Afronden

De hoeveelheid verwerkte gegevens wordt naar boven afgerond op het dichtstbijzijnde aantal MB per query, met een minimum van 10 MB aan gegevens verwerkt per query.

## <a name="what-is-not-included-in-data-processed"></a>Wat wordt niet opgenomen in gegevens verwerkt

- Meta gegevens op server niveau (zoals aanmeldingen, functies, referenties op server niveau)
- Data bases die u in uw eind punt maakt als die data bases bevatten alleen meta gegevens (zoals gebruikers, rollen, schema's, weer gaven, inline-TVFs, opgeslagen procedures, data base-bereik referenties, externe gegevens bronnen, externe bestands indelingen, externe tabellen)
  - Als u schema-deinterferentie gebruikt, worden bestands fragmenten gelezen om kolom namen en gegevens typen af te leiden
- DDL-instructies behalve statistieken maken omdat hiermee gegevens uit de opslag worden verwerkt op basis van het opgegeven steek proef percentage
- Query's met alleen meta gegevens

## <a name="reduce-amount-of-data-processed"></a>De hoeveelheid verwerkte gegevens verminderen

U kunt de hoeveelheid verwerkte gegevens die per query worden verwerkt, optimaliseren en betere prestaties verkrijgen door de gegevens te partitioneren en te converteren naar een gecomprimeerde kolom indeling zoals Parquet.

## <a name="examples"></a>Voorbeelden

Stel dat er twee tabellen zijn, elk met dezelfde gegevens in vijf even grote kolommen:

- population_csv tabel ondersteund door 5 TB aan CSV-bestanden
- population_parquet tabel ondersteund door 1 TB Parquet-bestanden. deze tabel is kleiner dan het vorige, omdat Parquet gecomprimeerde gegevens bevat
- very_small_csv tabel ondersteund door 100 KB aan CSV-bestanden

**Query #1** : Selecteer Sum (populatie) van population_csv

Met deze query worden volledige bestanden gelezen en geparseerd om waarden op te halen voor de kolom populatie. Knoop punten verwerken fragmenten van deze tabel, de som van de populatie voor elk fragment wordt overgebracht tussen de knoop punten en het uiteindelijke totaal wordt overgezet naar uw eind punt. Met deze query worden 5 TB aan gegevens verwerkt, plus kleine overhead voor het overdragen van de som van fragmenten.

**Query #2** : Selecteer Sum (populatie) van population_parquet

Het opvragen van gecomprimeerde en kolom georiënteerde indelingen, zoals Parquet resulteert in het lezen van minder gegevens dan in de vorige query, omdat een serverloze SQL-pool een enkele gecomprimeerde kolom leest in plaats van het hele bestand. In dit geval wordt 0,2 TB gelezen (vijf kolommen met gelijk grootte, 0,2 TB elke). Knoop punten verwerken fragmenten van deze tabel, de som van de populatie voor elk fragment wordt overgebracht tussen de knoop punten en het uiteindelijke totaal wordt overgezet naar uw eind punt. Met deze query wordt 0,2 TB verwerkt plus een kleine overhead voor het overdragen van de som van fragmenten.

**Query #3** : Select * from population_parquet

Met deze query worden alle kolommen gelezen en worden alle gegevens overgebracht naar een niet-gecomprimeerde indeling. Als de compressie-indeling 5:1 is, worden er 6 TB verwerkt, omdat hiermee 1 TB en overdracht 5 TB aan ongecomprimeerde gegevens worden gelezen.

**Query #4** : aantal (*) selecteren vanuit very_small_csv

Met deze query worden hele bestanden gelezen. De totale grootte van de bestanden in de opslag voor deze tabel is 100 KB. Knoop punten verwerken fragmenten van deze tabel, de som voor elk fragment wordt overgezet tussen knoop punten en het eind totaal wordt overgebracht naar uw eind punt. Deze query zal iets meer dan 100 KB aan gegevens verwerken. De hoeveelheid gegevens die voor deze query wordt verwerkt, wordt tot 10 MB afgerond zoals opgegeven in [afronding](#rounding).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg [Aanbevolen procedures voor serverloze SQL-groepen](best-practices-sql-on-demand.md)voor meer informatie over het optimaliseren van uw query's voor prestaties.

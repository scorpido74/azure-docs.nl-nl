---
title: U-SQL-scripts uitbreiden met Python in Azure Data Lake Analytics
description: Meer informatie over het uitvoeren van Python-code in U-SQL-scripts met Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813414"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>U-SQL-scripts uitbreiden met Python-code in Azure Data Lake Analytics

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat de Python-extensies zijn geïnstalleerd in uw Azure Data Lake Analytics-account.

* Navigeren naar uw Data Lake Analytics-account in de Azure-portal
* Klik in het linkermenu onder **AAN DE SLAG** op **Voorbeeldscripts**
* Klik **op U-SQL-extensies installeren** en vervolgens **OK**

## <a name="overview"></a>Overzicht 

Python-extensies voor U-SQL stellen ontwikkelaars in staat om massaal parallelle uitvoering van Python-code uit te voeren. In het volgende voorbeeld worden de basisstappen geïllustreerd:

* De `REFERENCE ASSEMBLY` instructie gebruiken om Python-extensies in te schakelen voor het U-SQL Script
* De `REDUCE` bewerking gebruiken om de invoergegevens op een sleutel te partitioneren
* De Python-extensies voor U-SQL bevatten een`Extension.Python.Reducer`ingebouwde reducer ( ) die Python-code uitvoert op elk hoekpunt dat is toegewezen aan de reducer
* Het U-SQL-script bevat de ingesloten Python-code met een functie die wordt aangeroepen `usqlml_main` als een panda's DataFrame als invoer en retourneert een pandas DataFrame als uitvoer.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Hoe Python integreert met U-SQL

### <a name="datatypes"></a>Datatypes

* Tekenreeks- en numerieke kolommen van U-SQL worden geconverteerd tussen Panda's en U-SQL
* U-SQL Nulls worden geconverteerd `NA` van en naar Panda's-waarden

### <a name="schemas"></a>Schema 's

* Indexvectoren in Panda's worden niet ondersteund in U-SQL. Alle invoergegevensframes in de Python-functie hebben altijd een 64-bits numerieke index van 0 tot en met het aantal rijen min 1. 
* U-SQL-gegevenssets mogen geen dubbele kolomnamen hebben
* U-SQL-gegevenssets kolomnamen die geen tekenreeksen zijn. 

### <a name="python-versions"></a>Python-versies
Alleen Python 3.5.1 (gecompileerd voor Windows) wordt ondersteund. 

### <a name="standard-python-modules"></a>Standaard Python-modules
Alle standaard Python modules zijn inbegrepen.

### <a name="additional-python-modules"></a>Extra Python-modules
Naast de standaard Python-bibliotheken zijn er verschillende veelgebruikte python-bibliotheken opgenomen:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Uitzonderingsberichten
Momenteel wordt een uitzondering in Python-code weergegeven als algemene vertex-fout. In de toekomst worden in de foutberichten van u-SQL-taak het python-uitzonderingsbericht weergegeven.

### <a name="input-and-output-size-limitations"></a>Beperkingen voor invoer- en uitvoergrootte
Elke hoekpunt heeft een beperkte hoeveelheid geheugen toegewezen aan het. Momenteel is die limiet 6 GB voor een AU. Omdat de invoer- en uitvoergegevensframes in het geheugen in de Python-code moeten bestaan, mag de totale grootte voor de invoer en uitvoer niet groter zijn dan 6 GB.

## <a name="see-also"></a>Zie ook
* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL-vensterfuncties gebruiken voor Azure Data Lake Analytics-taken](data-lake-analytics-use-window-functions.md)
* [Azure Data Lake-hulpprogramma's voor Visual Studio Code gebruiken](data-lake-analytics-data-lake-tools-for-vscode.md)

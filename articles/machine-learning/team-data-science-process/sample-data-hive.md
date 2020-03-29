---
title: Voorbeeldgegevens in Azure HDInsight Hive-tabellen - Team Data Science-proces
description: Down-sample gegevens die zijn opgeslagen in Azure HDInsight Hive-tabellen met Hive-query's om de gegevens te reduceren tot een groter beheerbare grootte voor analyse.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df85edc3de00e2b0342bc3102fe9e85564a9835b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719990"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Voorbeeldgegevens in Hive-tabellen in Azure HDInsight
In dit artikel wordt beschreven hoe u gegevens downsamplen die zijn opgeslagen in Azure HDInsight Hive-tabellen met Hive-query's om deze te beperken tot een groter beheerbare grootte voor analyse. Het omvat drie in de volksmond gebruikte bemonsteringsmethoden:

* Uniforme steekproefbemonstering
* Steekproef per groep
* Gestratificeerde bemonstering

**Waarom uw gegevens proeven?**
Als de gegevensset die u wilt analyseren groot is, is het meestal een goed idee om de gegevens te downsamplen om deze te beperken tot een kleinere, maar representatievere en beter beheerbare grootte. Down-sampling vergemakkelijkt het begrijpen, verkennen en functieengineering van gegevens. Haar rol in het Team Data Science Process is het mogelijk maken van snelle prototyping van de data processing functies en machine learning modellen.

Deze bemonsteringstaak is een stap in het [Team Data Science Process (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="how-to-submit-hive-queries"></a>Hive-query's indienen
Hive-query's kunnen worden ingediend via de Hadoop Command-Line-console op het hoofdknooppunt van het Hadoop-cluster.  Log in op het hoofdknooppunt van het Hadoop-cluster, open de Hadoop Command-Line-console en verzend de Hive-query's vanaf daar. Zie [Hive-query's](move-hive-tables.md#submit)indienen voor instructies voor het indienen van Hive-query's in de hadoop-opdrachtregelconsole.

## <a name="uniform-random-sampling"></a><a name="uniform"></a>Uniforme steekproefbemonstering
Uniforme steekproeven betekenen dat elke rij in de gegevensset een gelijke kans heeft om bemonsterd te worden. Het kan worden geïmplementeerd door een extra veldrand() toe te voegen aan de gegevensset in de binnenste 'selectie'-query en in de buitenste 'selecteer'-query die voorwaarde op dat willekeurige veld.

Hier volgt een voorbeeld van een query:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

`<sample rate, 0-1>` Hiermee geeft u het percentage records op dat de gebruikers willen samplen.

## <a name="random-sampling-by-groups"></a><a name="group"></a>Steekproef per groep
Wanneer u categorische gegevens bemonstert, u alle exemplaren opnemen of uitsluiten voor een bepaalde waarde van de categorische variabele. Dit soort steekproeven wordt "bemonstering per groep" genoemd. Als u bijvoorbeeld een categorische variabele " Staat "*hebt,* die waarden heeft zoals NY, MA, CA, NJ en PA, wilt u dat records van elke staat bij elkaar zijn, ongeacht of ze zijn bemonsterd of niet.

Hier is een voorbeeldquery die per groep wordt gemonsterd:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified-sampling"></a><a name="stratified"></a>Gestratificeerde bemonstering
Willekeurige steekproeven worden gestratificeerd met betrekking tot een categorische variabele wanneer de verkregen monsters categorische waarden hebben die in dezelfde verhouding aanwezig zijn als in de moederpopulatie. Met behulp van hetzelfde voorbeeld als hierboven, stel dat uw gegevens heeft de volgende waarnemingen door staten: NJ heeft 100 waarnemingen, NY heeft 60 waarnemingen, en WA heeft 300 waarnemingen. Als u de snelheid van gestratificeerde bemonstering op 0,5 geeft, moet het verkregen monster respectievelijk ongeveer 50, 30 en 150 waarnemingen van NJ, NY en WA hebben.

Hier volgt een voorbeeld van een query:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Zie [LanguageManual Sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)voor meer geavanceerde bemonsteringsmethoden die beschikbaar zijn in Hive.


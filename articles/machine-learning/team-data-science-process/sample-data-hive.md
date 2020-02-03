---
title: Voorbeeldgegevens in Azure HDInsight Hive-tabellen - Team Data Science Process
description: Down-sampling van gegevens die zijn opgeslagen in Azure HDInsight Hive-tabellen met behulp van Hive-query's kunt u de gegevens verkleinen tot een grootte die beter beheersbare voor analyse.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719990"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Voorbeeldgegevens in Hive-tabellen in Azure HDInsight
In dit artikel wordt beschreven hoe u down-sampling van gegevens die zijn opgeslagen in Azure HDInsight Hive-tabellen met behulp van Hive-query's te verkleinen tot een grootte die beter beheersbare voor analyse. Dit omvat drie veelgebruikte steekproef methoden:

* Uniform steekproeven
* Steekproeven in groepen
* Toepassing stratificatie steekproeven

**Waarom een voor beeld van uw gegevens?**
Als de gegevensset die u van plan bent om te analyseren groot is, is het doorgaans een goed idee om down-sampling van de gegevens om deze aan de grootte van een kleiner, maar representatieve en gemakkelijker. Down-sampling vereenvoudigt het uitvoeren van inzicht in gegevens verkennen en feature-engineering. De rol in het Team Data Science Process is om in te schakelen, snel ontwikkelen van prototypen van de functies voor het verwerken van gegevens en machine learning-modellen.

Deze steekproef taak is een stap in het [team data Science process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="how-to-submit-hive-queries"></a>Het indienen van Hive-query 's
Hive-query's kunnen worden verzonden vanaf de opdrachtregel Hadoop-console op het hoofdknooppunt van het Hadoop-cluster.  Meld u aan bij het hoofd knooppunt van het Hadoop-cluster, open de Hadoop-opdracht regel console en verzend de Hive-query's. Zie [Hive-Query's verzenden](move-hive-tables.md#submit)voor instructies over het verzenden van Hive-query's in de Hadoop-opdracht regel console.

## <a name="uniform"></a>Uniforme aselecte steek proef
Uniform steekproeven betekent dat elke rij in de gegevensset heeft een gelijke kans, worden steekproeven genomen. Dit kan worden geïmplementeerd door toe te voegen een extra veld ASELECT() in de gegevensset in de binnenste query voor "selecteren" en in de buitenste 'selecteren'-query die voorwaarde op een willekeurig veld.

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

Hier geeft `<sample rate, 0-1>` het aandeel van records op waarmee de gebruikers moeten worden gesampled.

## <a name="group"></a>Wille keurige steek proeven per groep
Wanneer categorische steekproef nemen voor gegevens, kunt u op te nemen of alle van de exemplaren van een waarde van de variabele categorische uitsluiten. De sortering van het samplen bijhouden heet 'steekproeven per groep'. Als u bijvoorbeeld een categorische-variabele '*State*' hebt, die waarden bevat zoals NY, ma, CA, NJ en PA, wilt u dat records uit elke staat samen komen, ongeacht of ze worden bemonsterd of niet.

Hier volgt een voorbeeldquery die voorbeelden van groep:

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

## <a name="stratified"></a>Stratified-steek proeven
Steekproeven is toepassing stratificatie met betrekking tot een categorische variabele wanneer de voorbeelden die zijn verkregen categorische waarden die aanwezig zijn in dezelfde verhouding zoals ze in de populatie van de bovenliggende waren hebben. Met behulp van hetzelfde voorbeeld als hierboven, stel uw gegevens heeft de volgende opmerkingen per statussen: NJ heeft 100 opmerkingen, NY is 60 opmerkingen en WA 300 opmerkingen. Als u het aantal steekproeven toepassing stratificatie moet 0,5 opgeeft, klikt u vervolgens moet het voorbeeld dat is verkregen ongeveer 50, 30 en 150 opmerkingen van NJ, NY en WA respectievelijk.

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


Zie [LanguageManual sampling](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling)(Engelstalig) voor meer informatie over geavanceerde bemonsterings methoden die beschikbaar zijn in Hive.


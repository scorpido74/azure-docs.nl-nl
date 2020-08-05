---
title: 'Zelfstudie: aan de slag met het analyseren van gegevens met SQL-pool'
description: In deze zelfstudie gebruikt u de voorbeeldgegevens van NYC Taxi om de analysemogelijkheden van een SQL-pool te verkennen.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325216"
---
# <a name="analyze-data-with-sql-pools"></a>Gegevens analyseren met SQL-pools

Azure Synapse Analytics biedt u de mogelijkheid om gegevens te analyseren met behulp van een SQL-pool. In deze zelfstudie gebruikt u de voorbeeldgegevens van NYC Taxi om de analysemogelijkheden van een SQL-pool te verkennen.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>De gegevens van het NYC-taxivoorbeeld laden in de SQLDB1-database

1. Selecteer in Synapse Studio in het bovenste blauwe menu het vraagteken ( **?** ).
1. Selecteer **Aan de slag** > **Aan de slag-hub**.
1. Selecteer in de kaart **Query uitvoeren op voorbeeldgegevens** de SQL-pool met de naam **SQLDB1**.
1. Selecteer **Querygegevens**. Er wordt kort een melding over het laden van voorbeeldgegevens weergegeven. Een lichtblauwe statusbalk boven in Synapse Studio geeft aan dat gegevens in SQLDB1 worden geladen.
1. Nadat de statusbalk groen wordt, sluit u deze.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>De NYC-taxigegevens in de SQL-pool verkennen

1. Ga in Synapse Studio naar de hub **Gegevens**.
1. Ga naar **SQLDB1** > **Tabellen**. Er wordt een aantal tabellen geladen.
1. Klik met de rechtermuisknop op de tabel **dbo.Trip** en selecteer **Nieuw SQL-script** > **Eerste 100 rijen selecteren**.
1. Wacht tot er een nieuw SQL-script wordt gemaakt en uitgevoerd.
1. U ziet dat bovenaan het SQL-script **Verbinding maken met** automatisch is ingesteld op de SQL-pool met de naam **SQLDB1**.
1. Vervang de tekst van het SQL-script door deze code en voer deze uit.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Deze query laat zien op welke manier de totale reisafstanden en de gemiddelde reisafstand betrekking hebben op het aantal reizigers.
1. In het resultatenvenster van het SQL-script wijzigt u de **Weergave** in **Grafiek** om een visualisatie van de resultaten weer te geven als een lijndiagram.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Analyseren met behulp van Spark](get-started-analyze-spark.md)


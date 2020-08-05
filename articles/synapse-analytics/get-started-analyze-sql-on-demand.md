---
title: 'Zelfstudie: aan de slag met het analyseren van gegevens met SQL on demand'
description: In deze zelfstudie leert u hoe u gegevens kunt analyseren met SQL on demand met behulp van gegevens die zich in Spark-databases bevinden.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093491"
---
# <a name="analyze-data-with-sql-on-demand"></a>Analyseren met behulp van SQL on demand

In deze zelfstudie leert u hoe u gegevens kunt analyseren met SQL on demand met behulp van gegevens die zich in Spark-databases bevinden. 

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>NYC-taxigegevens analyseren in Apache Spark-databases met behulp van SQL on-demand

Tabellen in Apache Spark-databases zijn automatisch zichtbaar en er kunnen query's op worden uitgevoerd met SQL on-demand.

1. Ga in Synapse Studio naar de hub **Ontwikkelen** en maak een nieuw SQL-script.
1. Stel **Verbinding maken met** in op **SQL on-demand**.
1. Plak de volgende tekst in het script en voer het script uit.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > De eerste keer dat u een query uitvoert die SQL on-demand gebruikt, duurt het ongeveer tien seconden tot SQL on-demand de SQL-resources heeft verzameld die nodig zijn om uw query's uit te voeren. Daaropvolgende query's worden veel sneller uitgevoerd.
  


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Analyseren met behulp van Spark](get-started-analyze-spark.md)

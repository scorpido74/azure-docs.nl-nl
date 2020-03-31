---
title: Apache Base-query's uitvoeren in Azure HDInsight met Apache Phoenix
description: Meer informatie over het gebruik van Apache Zeppelin om Apache Base-query's uit te voeren met Phoenix.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 28eeb446e55213f1ffa0a638878f6432fd15a05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72392239"
---
# <a name="use-apache-zeppelin-to-run-apache-phoenix-queries-over-apache-hbase-in-azure-hdinsight"></a>Apache Zeppelin gebruiken om Apache Phoenix-query's uit te voeren via Apache HBase in Azure HDInsight

Apache Phoenix is een open source, massaal parallelle relationele database laag gebouwd op HBase. Met Phoenix u SQL-achtige query's via HBase gebruiken. Phoenix maakt gebruik van JDBC-stuurprogramma's eronder om u in staat te stellen SQL-tabellen, indexen, weergaven en sequenties te maken, te verwijderen, te wijzigen.  U Phoenix ook gebruiken om rijen afzonderlijk en in bulk bij te werken. Phoenix gebruikt een NOSQL native compilatie in plaats van MapReduce te gebruiken om query's samen te stellen, waardoor toepassingen met lage latentie bovenop HBase kunnen worden gemaakt.

Apache Zeppelin is een open source webgebaseerd notitieblok waarmee u datagestuurde, collaboratieve documenten maken met behulp van interactieve data-analyse en talen zoals SQL en Scala. Het helpt gegevensontwikkelaars & gegevenswetenschappers code ontwikkelen, organiseren, uitvoeren en delen voor gegevensmanipulatie. Hiermee u resultaten visualiseren zonder te verwijzen naar de opdrachtregel of de clusterdetails nodig te hebben.

HDInsight-gebruikers kunnen Apache Zeppelin gebruiken om Phoenix-tabellen op te vragen. Apache Zeppelin is geïntegreerd met HDInsight cluster en er zijn geen extra stappen om het te gebruiken. Maak gewoon een Zeppelin Notebook met JDBC-tolk en begin met het schrijven van uw Phoenix SQL-query's

## <a name="prerequisites"></a>Vereisten

Een Apache HBase cluster op HDInsight. Zie [Aan de slag met Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

## <a name="create-an-apache-zeppelin-note"></a>Een Apache Zeppelin-notitie maken

1. Vervang `CLUSTERNAME` de naam van uw cluster `https://CLUSTERNAME.azurehdinsight.net/zeppelin`in de volgende URL . Voer vervolgens de URL in een webbrowser in. Voer de gebruikersnaam en het wachtwoord van uw clusterlogin in.

1. Selecteer op de zeppelinpagina De optie **Nieuwe notitie maken**.

    ![HDInsight Interactive Query zeppelin](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-create-note.png)

1. Typ of selecteer in het dialoogvenster **Nieuwe notitie maken** de volgende waarden:

    - Opmerkingsnaam: voer een naam voor de notitie in.
    - Standaardtolk: Selecteer **jdbc** in de vervolgkeuzelijst.

    Selecteer vervolgens **Notitie maken**.

1. Zorg ervoor dat de notitieblokkop een verbonden status weergeeft. Het wordt aangeduid door een groene stip in de rechterbovenhoek.

    ![Zeppelin-notitieblokstatus](./media/apache-hbase-phoenix-zeppelin/hbase-zeppelin-connected.png "Zeppelin-notitieblokstatus")

1. Maak een HBase-tabel. Voer de volgende opdracht in en druk op **Shift + Enter:**

    ```sql
    %jdbc(phoenix)
    CREATE TABLE Company (
        company_id INTEGER PRIMARY KEY,
        name VARCHAR(225)
    );
    ```

    De **verklaring %jdbc(phoenix)** in de eerste regel vertelt het notitieblok om de Phoenix JDBC-tolk te gebruiken.

1. Gemaakte tabellen weergeven.

    ```sql
    %jdbc(phoenix)
    SELECT DISTINCT table_name
    FROM SYSTEM.CATALOG
    WHERE table_schem is null or table_schem <> 'SYSTEM';
    ```

1. Waarden invoegen in de tabel.

    ```sql
    %jdbc(phoenix)
    UPSERT INTO dbo.Company VALUES(1, 'Microsoft');
    UPSERT INTO dbo.Company (name, company_id) VALUES('Apache', 2);
    ```

1. De tabel opvragen.

    ```sql
    %jdbc(phoenix)
    SELECT * FROM dbo.Company;
    ```

1. Een record verwijderen.

    ```sql
    %jdbc(phoenix)
    DELETE FROM dbo.Company WHERE COMPANY_ID=1;
    ```

1. Laat de tafel vallen.

    ```sql
    %jdbc(phoenix)
    DROP TABLE dbo.Company;
    ```

## <a name="next-steps"></a>Volgende stappen

- [Apache Phoenix ondersteunt Zeppelin nu in Azure HDInsight](https://blogs.msdn.microsoft.com/ashish/2018/08/17/apache-phoenix-now-supports-zeppelin-in-azure-hdinsight/)
- [Apache Phoenix grammatica](https://phoenix.apache.org/language/index.html)

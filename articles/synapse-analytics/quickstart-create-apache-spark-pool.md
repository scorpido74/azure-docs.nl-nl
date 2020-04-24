---
title: Quick Start een Apache Spark groep maken (preview)
description: Maak een nieuwe Apache Spark pool voor een Azure Synapse Analytics-werk ruimte door de stappen in deze hand leiding te volgen.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0fd8a379927396f2ae44d74c2d968d48fbd039c1
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096364"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Snelstartgids: een nieuwe Apache Spark groep maken (preview)

Synapse Analytics biedt diverse analyse-engines waarmee u uw gegevens kunt opnemen, transformeren, model leren, analyseren en verwerken. Een Apache Spark groep biedt open-source big data reken mogelijkheden. Nadat u een Apache Spark groep hebt gemaakt in uw Synapse-werk ruimte, kunnen gegevens worden geladen, gemodelleerd, verwerkt en bediend om inzichten te verkrijgen.

In deze Quick Start leert u hoe u de Azure Portal kunt gebruiken om een Apache Spark groep te maken in een Synapse-werk ruimte.

> [!IMPORTANT]
> Facturering voor Spark-instanties wordt per minuut naar beneden geclassificeerd, ongeacht of u deze gebruikt. Zorg ervoor dat u de Spark-instantie afsluit nadat u deze hebt gebruikt of een korte time-out hebt ingesteld. Zie voor meer informatie de sectie **Resources opschonen** van dit artikel.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account aan voordat u begint](https:/azure.microsoft.com/free/).

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https:/azure.microsoft.com/free/)
- [Synapse Analytics-werk ruimte](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Nieuwe Apache Spark pool maken

1. Klik in de werk ruimte Synapse waar u de Apache Spark groep wilt maken op **nieuw Apache Spark groep**.
![Overzicht van de Synapse-werk ruimte met een rood vak rond de opdracht voor het maken van een nieuwe Apache Spark pool](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Voer de volgende gegevens in op het tabblad **basis beginselen** :

    |Instelling | Voorgestelde waarde | Beschrijving |
    | :------ | :-------------- | :---------- |
    | **Naam van Apache Spark groep** | Een geldige groeps naam | Dit is de naam die de Apache Spark pool heeft. |
    | **Knooppunt grootte** | Klein (4 vCPU/32 GB) | Stel dit in op de kleinste grootte om de kosten voor deze Quick Start te verlagen |
    | **Automatisch schalen** | Ingeschakeld | Deze standaard instelling behouden |
    | **Aantal knoop punten** | 3 - 40 | Deze standaard instelling behouden |
    ||||

    ![Apache Spark tabblad stroom voor het maken van de pool.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Houd er rekening mee dat er specifieke beperkingen gelden voor de namen die Apache Spark Pools kunnen gebruiken. Namen mogen alleen letters of cijfers bevatten, moeten uit Maxi maal 15 tekens bestaan, moeten beginnen met een letter en mogen geen gereserveerde woorden bevatten en uniek zijn in de werk ruimte.

3. Klik op **volgende: extra instellingen** en controleer de standaard instellingen. Wijzig geen standaard instellingen.
![Apache Spark-groep maken-extra instellingen tabblad.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Klik op **volgende: Tags**. Voeg geen tags toe.
![Apache Spark-groep maken-extra instellingen tabblad.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Klik op **Controleren + maken**.

6. Zorg ervoor dat de Details correct zijn, op basis van wat er eerder is ingevoerd en klik op **maken**.
![Apache Spark het tabblad Instellingen voor het maken van de groep stroom.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. Op dit moment wordt de stroom voor het inrichten van de resource gestart. deze geeft aan ![dat deze is voltooid Apache Spark pool stroom voor het inrichten van de resource maakt.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. Nadat het inrichten is voltooid, wordt door terug naar de werk ruimte een nieuwe vermelding weer gegeven voor de zojuist gemaakte Apache Spark pool.
 ![Apache Spark pool maakt stroom voor het inrichten van resources.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. Op dit moment worden er geen resources uitgevoerd, worden er geen kosten in rekening gebracht voor Spark, u hebt meta gegevens gemaakt over de Spark-instanties die u wilt maken.

## <a name="clean-up-resources"></a>Resources opschonen

Volg de onderstaande stappen om de Apache Spark groep uit de werk ruimte te verwijderen.
> [!WARNING]
> Als u een Apache Spark groep verwijdert, wordt de analyse-engine uit de werk ruimte verwijderd. Het is niet meer mogelijk om verbinding te maken met de groep en alle query's, pijp lijnen en notitie blokken die deze Apache Spark groep gebruiken, werken niet meer.

Als u de Apache Spark groep wilt verwijderen, gaat u als volgt te werk:

1. Navigeer naar de Blade Apache Spark Pools in de werk ruimte.
2. Selecteer de Apache-groep die u wilt verwijderen (in dit geval **contosospark**)
3. Druk op **verwijderen**.
 ![Lijst met Apache Spark groepen, waarbij de zojuist gemaakte groep is geselecteerd.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Bevestig het verwijderen en druk op de knop **verwijderen** .
 ![Bevestigings venster voor het verwijderen van de geselecteerde Apache Spark groep.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Wanneer het proces is voltooid, wordt de Apache Spark groep niet meer weer gegeven in de werkruimte resources.

Zodra de SQL-groep is gemaakt, is deze beschikbaar in de werk ruimte voor het laden van gegevens, het verwerken van stromen, het lezen van het Lake, enzovoort.

## <a name="next-steps"></a>Volgende stappen

- Zie [Quick Start: een Apache Spark notitie blok maken](quickstart-apache-spark-notebook.md).
- Zie [Quick Start: een Synapse SQL-groep maken met behulp van de Azure Portal](quickstart-create-sql-pool.md).

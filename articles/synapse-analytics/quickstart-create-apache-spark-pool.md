---
title: Snel een Apache Spark-pool maken (voorbeeld)
description: Maak een nieuwe Apache Spark-pool voor een Azure Synapse Analytics-werkruimte door de stappen in deze handleiding te volgen.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1457549fef3a78356c8b1af6be620fdf30ddab46
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424431"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Snelstart: een nieuwe Apache Spark-pool maken (voorbeeld)

Synapse Analytics biedt verschillende analyseengines om u te helpen uw gegevens in te nemen, te transformeren, te modelleren, te analyseren en te bedienen. Een Apache Spark-pool biedt open-source big data compute-mogelijkheden. Nadat u een Apache Spark-pool hebt gemaakt in uw Synapse-werkruimte, kunnen gegevens worden geladen, gemodelleerd, verwerkt en geserveerd om inzichten te verkrijgen.

In deze quickstart leert u hoe u de Azure-portal gebruiken om een Apache Spark-pool in een Synapse-werkruimte te maken.

> [!IMPORTANT]
> Facturering voor Spark-exemplaren wordt naar rato per minuut berekend, of u ze nu gebruikt of niet. Sluit uw Spark-exemplaar af nadat u deze hebt gebruikt of stel een korte time-out in. Zie voor meer informatie de sectie **Resources opschonen** van dit artikel.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint.](https:/azure.microsoft.com/free/)

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https:/azure.microsoft.com/free/)
- [Synapse Analytics-werkruimte](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Aanmelden bij de [Azure-portal](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Nieuwe Apache Spark-pool maken

1. Klik in de Synapse-werkruimte waar u de groep Apache Spark wilt maken op **Nieuwe groep Apache Spark**.
![Overzicht van synapsenwerkruimte met een rood vak rond de opdracht om een nieuwe Apache Spark-pool te maken](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Voer de volgende details in op het tabblad **Basisbeginselen:**

    |Instelling | Voorgestelde waarde | Beschrijving |
    | :------ | :-------------- | :---------- |
    | **Naam Apache Spark-pool** | Een geldige poolnaam | Dit is de naam die de Apache Spark pool zal hebben. |
    | **Grootte van knooppunt** | Klein (4 vCPU / 32 GB) | Stel dit in op de kleinste maat om de kosten voor deze quickstart te verlagen |
    | **Automatisch schalen** | Ingeschakeld | Deze standaardinstelling verlaten |
    | **Aantal knooppunten** | 3 - 40 | Deze standaardinstelling verlaten |
    ||||

    ![Apache Spark-pool maakt flow - tabblad Basisbeginselen.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Houd er rekening mee dat er specifieke beperkingen zijn voor de namen die Apache Spark-pools kunnen gebruiken. Namen mogen alleen letters of cijfers bevatten, moeten 15 of minder tekens zijn, moeten beginnen met een letter, geen gereserveerde woorden bevatten en uniek zijn in de werkruimte.

3. Klik **op Volgende: extra instellingen** en controleer de standaardinstellingen. Wijzig geen standaardinstellingen.
![Apache Spark pool create flow - extra instellingen tabblad.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Klik **op Volgende: tags**. Voeg geen tags toe.
![Apache Spark pool create flow - extra instellingen tabblad.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Klik op **Controleren + maken**.

6. Zorg ervoor dat de details er correct uitzien op basis van wat eerder is ingevoerd en klik op **Maken**.
![Apache Spark pool create flow - tabblad Instellingen controleren.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. Op dit punt wordt de broninrichtingsstroom gestart, wat ![aangeeft zodra de volledige Apache Spark-pool flow maakt - resourceprovisioning.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. Nadat de inrichting is voltooid, wordt een nieuwe vermelding voor de nieuw gemaakte Apache Spark-pool weergegeven.
 ![Apache Spark-pool maakt flow - resource provisioning.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. Op dit moment worden er geen resources uitgevoerd, geen kosten voor Spark, je hebt metadata gemaakt over de Spark-exemplaren die je wilt maken.

## <a name="clean-up-resources"></a>Resources opschonen

Volg de onderstaande stappen om de groep Apache Spark uit de werkruimte te verwijderen.
> [!WARNING]
> Als u een Apache Spark-pool verwijdert, wordt de analyseengine uit de werkruimte verwijderd. Het is niet langer mogelijk om verbinding te maken met de groep en alle query's, pijplijnen en notitieblokken die deze Apache Spark-pool gebruiken, werken niet meer.

Als u de groep Apache Spark wilt verwijderen, gaat u als volgt te werk:

1. Navigeer naar het blad van de Apache Spark-pools in de werkruimte.
2. Selecteer de Apache-groep die moet worden verwijderd (in dit geval **contosospark**)
3. Druk op **delete**.
 ![Vermelding van Apache Spark-pools, waarbij de onlangs gemaakte pool is geselecteerd.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Bevestig de verwijdering en druk op **delete.**
 ![Het dialoogvenster Bevestiging om de geselecteerde groep Apache Spark te verwijderen.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Wanneer het proces is voltooid, wordt de groep Apache Spark niet meer weergegeven in de werkruimtebronnen.

Zodra de SQL-groep is gemaakt, is deze beschikbaar in de werkruimte voor het laden van gegevens, verwerkingsstromen, lezen vanaf het meer, enzovoort.

## <a name="next-steps"></a>Volgende stappen

- Zie [Quickstart: Maak een Apache Spark-pool in Synapse Studio met behulp van webtools.](spark/apache-spark-notebook-create-spark-use-sql.md)
- Zie [Snelstart: Maak een Synapsische SQL-groep met de Azure-portal.](quickstart-create-sql-pool.md)

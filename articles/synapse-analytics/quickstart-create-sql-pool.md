---
title: Snelstart - Een Synapse SQL-groep maken
description: Maak een nieuwe Synapse SQL-pool voor een Azure Synapse Analytics Synapse Workspace door de stappen in deze handleiding te volgen.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: b3cd4ed280529efbde32fb89e6bac20640940fff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423906"
---
# <a name="quickstart-create-a-synapse-sql-pool"></a>Snelstart: een Synapse SQL-groep maken

Azure Synapse Analytics biedt verschillende analyseengines om u te helpen uw gegevens in te nemen, te transformeren, te modelleren, te analyseren en te leveren. Een SQL-pool biedt op T-SQL gebaseerde reken- en opslagmogelijkheden. Nadat u een SQL-pool in uw Synapse Workspace hebt gemaakt, kunnen gegevens worden geladen, gemodelleerd, verwerkt en geserveerd om inzichten te verkrijgen.

In deze quickstart leert u een SQL-groep te maken in een Synapse Workspace met behulp van de Azure-portal.

Als u geen Azure-abonnement hebt, [maakt u een gratis account voordat u begint.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Vereisten

[Synapsenworkspace](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Aanmelden bij de [Azure-portal](https://portal.azure.com/)

## <a name="create-new-sql-pool"></a>Nieuwe SQL-groep maken

1. Klik in de Synapse-werkruimte waar u de SQL-groep wilt maken op De opdracht **Nieuwe SQL-groep** in de bovenste balk.
![Overzicht van Synapse Workspace met een rood vak rond de opdracht om een nieuwe SQL-groep te maken.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-01.png)
2. Voer de volgende details in op het tabblad **Basisbeginselen:**

    | Instelling | Voorgestelde waarde | Beschrijving |
    | :------ | :-------------- | :---------- |
    | **SQL-poolnaam** | Een geldige naam | Naam van de SQL-pool. |
    | **Prestatieniveau** | DW100c | Ingesteld op de kleinste grootte om de kosten voor deze quickstart te verlagen |
    ||||
  
    ![SQL-groep maakt flow - tabblad Basisbeginselen.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-02.png)
    > [!IMPORTANT]
    > Houd er rekening mee dat er specifieke beperkingen zijn voor de namen die SQL-groepen kunnen gebruiken. Namen mogen geen speciale tekens bevatten, moeten 15 of minder tekens bevatten, geen gereserveerde woorden bevatten en uniek zijn in de werkruimte.

3. Klik **op Volgende: Aanvullende instellingen**.
4. Selecteer **Geen** om de SQL-groep zonder gegevens in te richten. Laat de standaardcollatie geselecteerd.
![SQL-groep maakt flow - tabblad extra instellingen.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-03.png)

5. Klik op **Controleren + maken**.
6. Zorg ervoor dat de details er correct uitzien op basis van wat eerder is ingevoerd en klik op **Maken**.
![SQL-groep maakt flow - tabblad controle-instellingen.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-04.png)

7. Op dit punt wordt de broninrichtingsstroom gestart.
 ![SQL-groep maakt flow - resourceprovisioning.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-06.png)

8. Nadat de inrichting is voltooid, wordt een nieuwe vermelding voor de nieuw gemaakte SQL-groep weergegeven door terug te gaan naar de werkruimte.
 ![SQL-groep maakt flow - resourceprovisioning.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-07.png)

## <a name="clean-up-resources"></a>Resources opschonen

Volg de onderstaande stappen om de SQL-groep uit de werkruimte te verwijderen.
> [!WARNING]
> Als u een SQL-groep verwijdert, worden zowel de analyseengine als de gegevens die zijn opgeslagen in de database van de verwijderde SQL-groep uit de werkruimte verwijderd. Het is niet langer mogelijk om verbinding te maken met de SQL-groep en alle query's, pijplijnen en notitieblokken die deze SQL-groep lezen of schrijven, werken niet meer.

Als u de SQL-groep wilt verwijderen, voert u de volgende stappen uit:

1. Navigeren naar het SQL-poolsblad in het werkblad
1. Selecteer de SQL-groep die moet worden verwijderd (in dit geval **contosoedw**)
1. Selecteer het en druk op **Delete**.
1. Bevestig de verwijdering en druk op **delete.**
 ![SQL-groepoverzicht - met de nadruk op delete-bevestiging.](media/quickstart-create-sql-pool/quickstart-create-sql-pool-11.png)
1. Wanneer het proces is voltooid, wordt de SQL-groep niet langer weergegeven in de werkruimtebronnen.

Nadat de SQL-pool is gemaakt, is deze beschikbaar in de werkruimte voor het laden van gegevens, verwerkingsstromen, lezen vanaf het meer, enz.

## <a name="next-steps"></a>Volgende stappen

- Zie [Quickstart: Maak een Apache Spark-pool in Synapse Studio met behulp van webtools.](spark/apache-spark-notebook-create-spark-use-sql.md)
- Zie [Snelstart: Maak een Apache Spark-pool met de Azure-portal.](quickstart-create-apache-spark-pool.md)

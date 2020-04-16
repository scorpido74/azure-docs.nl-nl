---
title: 'Zelfstudie - Azure Synapse Analytics: Spark-taakdefinitie voor Synapse'
description: Zelfstudie - Gebruik de Azure Synapse Analytics om Spark-taakdefinities te maken en deze in te dienen bij een Synapse Spark-groep.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: d5443a2db6f4fecbd84ef51166f44c3a6e920aee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422968"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Zelfstudie: Azure Synapse Analytics gebruiken om Apache Spark-taakdefinities voor Synapse Spark-pools te maken

In deze zelfstudie wordt uitgelegd hoe u de Azure Synapse Analytics gebruiken om Spark-taakdefinities te maken en deze vervolgens in te dienen bij een Synapse Spark-groep. U de plug-in op een aantal manieren gebruiken:

* Ontwikkel en dien een Spark-taakdefinitie in op een Synapse Spark-pool.
* Bekijk de functiegegevens na indiening.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Ontwikkel en dien een Spark-taakdefinitie in op een Synapse Spark-pool.
> * Bekijk de functiegegevens na indiening.

## <a name="prerequisites"></a>Vereisten

* Een Azure Synapse Analytics-werkruimte. Zie Een [Azure Synapse Analytics-werkruimte maken](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)voor instructies.

## <a name="get-started"></a>Aan de slag

Voordat u een Spark-taakdefinitie indient, moet u de eigenaar zijn van storage blob-gegevens van het ADLS Gen2-bestandssysteem waarmee u wilt werken. Als u dat niet bent, moet u de toestemming handmatig toevoegen.

### <a name="scenario-1-add-permission"></a>Scenario 1: Toestemming toevoegen

1. Open [Microsoft Azure](https://ms.portal.azure.com)en open vervolgens opslagaccount.

2. Klik **op Containers**en maak vervolgens een **bestandssysteem**. In deze zelfstudie wordt `sparkjob` gebruikt.

    ![Klik op verzenden om de definitie van spark-taak in te dienen](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Het dialoogvenster Spark-indiening](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Open `sparkjob`, klik op **Toegangsbeheer (IAM)** en klik vervolgens op **Toevoegen** en selecteer **Roltoewijzing toevoegen**.

    ![Klik op verzenden om de definitie van spark-taak in te dienen](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Klik op verzenden om de definitie van spark-taak in te dienen](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Klik **op Toewijzingen van rollen**, voer de gebruikersnaam in en controleer vervolgens de gebruikersrol.

    ![Klik op verzenden om de definitie van spark-taak in te dienen](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scenario 2: Mapstructuur voorbereiden

Voordat u een Spark-taakdefinitie indient, moet u bestanden uploaden naar ADLS Gen2 en daar mapstructuur voorbereiden. We gebruiken Storage node in Synapse Studio om bestanden op te slaan.

1. Open [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Klik **op Gegevens,** selecteer **Opslagaccounts**en upload de relevante bestanden naar uw ADLS Gen2-bestandssysteem. Wij ondersteunen Scala, Java, .NET en Python. Deze zelfstudie gebruikt het voorbeeld in de figuur als een demonstratie, u de projectstructuur naar wens wijzigen.

    ![De waarde van de vacatureSpark instellen](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Een Spark-taakdefinitie maken

1. Open [Azure Synapse Analytics](https://web.azuresynapse.net/)en selecteer **Ontwikkelen**.

2. Selecteer **Vacatures van Spark** in het linkerdeelvenster.

3. Klik op het knooppunt **Acties** aan de rechterkant van de 'Spark-taakdefinities'.

     ![Nieuwe definitie van spark-taak maken](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Selecteer de optie **Nieuwe spark-taakdefinitie** in de vervolgkeuzelijst **Acties**

     ![Nieuwe definitie van spark-taak maken](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. Selecteer taal in het venster Nieuwe Spark-taakdefinitie en geef vervolgens de volgende informatie op:  

   * Selecteer **Taal** als **Spark(Scala).**

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Naam van taakdefinitie| Voer een naam in voor de definitie van uw Spark-taak.  In deze zelfstudie wordt `job definition sample` gebruikt. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd.|  
    |Hoofddefinitiebestand| Het hoofdbestand dat voor de taak wordt gebruikt. Selecteer een JAR-bestand uit uw opslag. U **Bestand uploaden** selecteren om het bestand naar een opslagaccount te uploaden. |
    |Naam van de hoofdklasse| De volledig gekwalificeerde id of de hoofdklasse die zich in het hoofddefinitiebestand bevindt.|
    |Opdrachtregelargumenten| Optionele argumenten voor de taak.|
    |Referentiebestanden| Aanvullende bestanden die worden gebruikt voor verwijzing in het hoofddefinitiebestand. U **Bestand uploaden** selecteren om het bestand naar een opslagaccount te uploaden.|
    |Spark-pool| De taak wordt ingediend bij de geselecteerde Spark-pool.|
    |Spark-versie| Versie van Spark die het Spark-zwembad draait.|
    |Executeurs| Aantal uitvoerders dat moet worden opgegeven in de opgegeven Spark-pool voor de taak.|
    |Executorgrootte| Aantal kernen en geheugen dat moet worden gebruikt voor uitvoerders die in de opgegeven Spark-pool voor de taak worden opgegeven.|  
    |Grootte van de bestuurder| Aantal cores en geheugen dat moet worden gebruikt voor stuurprogramma's die in de opgegeven Spark-pool voor de taak worden gegeven.|

    ![De waarde van de vacatureSpark instellen](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Selecteer **Taal** als **PySpark(Python)**.

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Naam van taakdefinitie| Voer een naam in voor de definitie van uw Spark-taak.  In deze zelfstudie wordt `job definition sample` gebruikt. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd.|  
    |Hoofddefinitiebestand| Het hoofdbestand dat voor de taak wordt gebruikt. Selecteer een PY-bestand uit uw opslag. U **Bestand uploaden** selecteren om het bestand naar een opslagaccount te uploaden.|
    |Opdrachtregelargumenten| Optionele argumenten voor de taak.|
    |Referentiebestanden| Aanvullende bestanden die worden gebruikt voor verwijzing in het hoofddefinitiebestand. U **Bestand uploaden** selecteren om het bestand naar een opslagaccount te uploaden.|
    |Spark-pool| De taak wordt ingediend bij de geselecteerde Spark-pool.|
    |Spark-versie| Versie van Spark die het Spark-zwembad draait.|
    |Executeurs| Aantal uitvoerders dat moet worden opgegeven in de opgegeven Spark-pool voor de taak.|
    |Executorgrootte| Aantal kernen en geheugen dat moet worden gebruikt voor uitvoerders die in de opgegeven Spark-pool voor de taak worden opgegeven.|  
    |Grootte van de bestuurder| Aantal cores en geheugen dat moet worden gebruikt voor stuurprogramma's die in de opgegeven Spark-pool voor de taak worden gegeven.|

    ![De waarde van de vacatureSpark instellen](./media/apache-spark-job-definitions/create-py-definition.png)

   * Selecteer **Taal** als **.NET Spark(C#/F#)**.

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Naam van taakdefinitie| Voer een naam in voor de definitie van uw Spark-taak.  In deze zelfstudie wordt `job definition sample` gebruikt. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd.|  
    |Hoofddefinitiebestand| Het hoofdbestand dat voor de taak wordt gebruikt. Selecteer een ZIP-bestand met de .NET voor Spark-toepassing (dat wil zeggen het belangrijkste uitvoerbare bestand, DLL's met door de gebruiker gedefinieerde functies en andere vereiste bestanden) uit uw opslag. U **Bestand uploaden** selecteren om het bestand naar een opslagaccount te uploaden.|
    |Hoofduitvoerbaar bestand| Het belangrijkste uitvoerbare bestand in het ZIP-bestand met hoofddefinitie.|
    |Opdrachtregelargumenten| Optionele argumenten voor de taak.|
    |Referentiebestanden| Extra bestanden die nodig zijn voor de worker nodes voor het uitvoeren van de .NET for Spark-toepassing die niet is opgenomen in het ZIP-bestand met hoofddefinitie (dat wil zeggen afhankelijke potten, extra door de gebruiker gedefinieerde functie DLL's en andere config-bestanden). U **Bestand uploaden** selecteren om het bestand naar een opslagaccount te uploaden.|
    |Spark-pool| De taak wordt ingediend bij de geselecteerde Spark-pool.|
    |Spark-versie| Versie van Spark die het Spark-zwembad draait.|
    |Executeurs| Aantal uitvoerders dat moet worden opgegeven in de opgegeven Spark-pool voor de taak.|
    |Executorgrootte| Aantal kernen en geheugen dat moet worden gebruikt voor uitvoerders die in de opgegeven Spark-pool voor de taak worden opgegeven.|  
    |Grootte van de bestuurder| Aantal cores en geheugen dat moet worden gebruikt voor stuurprogramma's die in de opgegeven Spark-pool voor de taak worden gegeven.|

    ![De waarde van de vacatureSpark instellen](./media/apache-spark-job-definitions/create-net-definition.png)

6. Selecteer **Publiceren** om de vacaturedefinitie van Spark op te slaan.

    ![Spark-taakdefinitie publiceren](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Een Spark-taakdefinitie indienen

Nadat u een Spark-taakdefinitie hebt gedefinieerd, u deze indienen bij een Synapse Spark-groep. Zorg ervoor dat je stappen hebt doorlopen in **Aan de slag** voordat je voorbeelden in dit deel probeert.

### <a name="scenario-1-submit-spark-job-definition"></a>Scenario 1: Spark-taakdefinitie indienen

1. Open een definitievenster voor vonktaak door erop te klikken.

      ![Definitie van open vonktaak in te dienen ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Klik **op pictogram Verzenden** om uw project in te dienen bij de geselecteerde Spark Pool. U op het **tabblad URL van vonkbewaking** klikken om de LogQuery van de Spark-toepassing weer te geven.

    ![Klik op verzenden om de definitie van spark-taak in te dienen](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Het dialoogvenster Spark-indiening](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scenario 2: Voortgang spark-taak weergeven

1. Klik **op Monitor**en selecteer de optie **Spark-toepassingen.** U de ingediende Spark-aanvraag vinden.

    ![Spark-toepassing weergeven](./media/apache-spark-job-definitions/view-spark-application.png)

2. Klik vervolgens op de Spark-toepassing, het venster **LogQuery** wordt weergegeven. U de voortgang van de uitvoering van taken bekijken vanuit **LogQuery**.

    ![Spark-toepassing LogQuery weergeven](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: Uitvoerbestand controleren

 1. Klik **op Gegevens**en selecteer Vervolgens **Opslagaccounts**. Na een succesvolle run u naar de ADLS Gen2-opslag gaan en de uitvoer controleren die wordt gegenereerd.

    ![Uitvoerbestand weergeven](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie wordt uitgelegd hoe u de Azure Synapse Analytics gebruiken om Spark-taakdefinities te maken en deze vervolgens in te dienen bij een Synapse Spark-groep. Vervolgens u Azure Synapse Analytics gebruiken om Power BI-gegevenssets te maken en Power BI-gegevens te beheren. 

- [Verbinding maken met gegevens in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Visualiseren met Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

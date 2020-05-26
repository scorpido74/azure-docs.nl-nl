---
title: 'Zelfstudie - Apache Spark voor Azure Synapse Analytics: Apache Spark-taakdefinitie voor Synapse'
description: Zelfstudie - gebruik de Azure Synapse Analytics om Spark-taakdefinities te maken en verzend deze naar een Apache Spark voor de Azure Synapse Analytics-pool.
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: 5fc9dffaa73d195c842381b6682a00e9834c0fe7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587932"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Zelfstudie: Azure Synapse Analytics gebruiken om Apache Spark-taakdefinities te maken voor Synapse Spark-pools

In dezezelf studie ziet u hoe u de Azure Synapse Analytics kunt gebruiken om Spark-taakdefinities te maken en deze vervolgens naar een Synapse Spark-pool te verzenden. U kunt de invoegtoepassing op een paar manieren gebruiken:

* Een Spark-taakdefinitie ontwikkelen en verzenden in een Synapse Spark-pool.
* Taakdetails weergeven na verzending.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Spark-taakdefinitie ontwikkelen en verzenden in een Synapse Spark-pool.
> * Taakdetails weergeven na verzending.

## <a name="prerequisites"></a>Vereisten

* Een Azure Synapse Analytics-werkruimte. Zie [Een Azure Synapse Analytics-werkruimte maken](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace) voor instructies.

## <a name="get-started"></a>Aan de slag

Voordat u een Spark-taakdefinitie verzendt, moet u de gegevenseigenaar van de opslag-blob zijn van het ADLS Gen2-bestandssysteem waarmee u wilt werken. Als dat niet het geval is, moet u de machtiging handmatig toevoegen.

### <a name="scenario-1-add-permission"></a>Scenario 1: Machtiging toevoegen

1. Open [Microsoft Azure](https://ms.portal.azure.com) en open vervolgens Opslagaccount.

2. Klik op **Containers** en maak vervolgens een **bestandssysteem**. In deze zelfstudie wordt `sparkjob` gebruikt.

    ![Klik op de knop verzenden om een Spark-taakdefinitie te verzenden](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Het dialoogvenster voor de verzending van Spark](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Open `sparkjob`, klik op **Access Control (IAM)** en klik vervolgens op **Toevoegen** en selecteer **Roltoewijzing toevoegen**.

    ![Klik op de knop verzenden om een Spark-taakdefinitie te verzenden](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Klik op de knop verzenden om een Spark-taakdefinitie te verzenden](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Klik op **Roltoewijzingen**, voer de gebruikersnaam in en controleer de gebruikersrol.

    ![Klik op de knop verzenden om een Spark-taakdefinitie te verzenden](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scenario 2: Mapstructuur voorbereiden

Voordat u een Spark-taakdefinitie verzendt, moet u een taak uploaden naar ADLS Gen2 en daar de mapstructuur voorbereiden. We gebruiken opslagknooppunt in Synapse Studio om bestanden op te slaan.

1. [Azure Synapse Analytics](https://web.azuresynapse.net/) openen.

2. Klik op **Gegevens**, selecteer **Opslagaccounts**en upload de relevante bestanden naar uw ADLS Gen2-bestandssysteem. Scala, Java, .NET en Python worden ondersteund. In deze zelfstudie wordt het voorbeeld in de afbeelding als demonstratie gebruikt. U kunt de projectstructuur naar wens wijzigen.

    ![De waarde van de Spark-taakdefinitie instellen](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Een Spark-taakdefinitie maken

1. Open [Azure Synapse Analytics](https://web.azuresynapse.net/) en selecteer **Ontwikkelen**.

2. Selecteer **Spark-taakdefinities** in het linkerdeelvenster.

3. Klik op **Acties**-knooppunt rechts van de 'Spark-taakdefinities'.

     ![Nieuwe Spark-taakdefinitie maken](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Selecteer in de vervolgkeuzelijst **Acties** en dan **Nieuwe Spark-taakdefinitie**

     ![Nieuwe Spark-taakdefinitie maken](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. In het venster Nieuwe Spark-taakdefinitie selecteert u taal en geeft u de volgende informatie op:  

   * Selecteer **Taal** als **Spark(Scala)** .

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Naam van taakdefinitie| Voer een naam in voor de Spark-taakdefinitie.  In deze zelfstudie wordt `job definition sample` gebruikt. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd.|  
    |Primair definitiebestand| Het primaire bestand dat wordt gebruikt voor de taak. Selecteer een JAR-bestand uit uw opslag. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount. |
    |Hoofdklassenaam| De volledig gekwalificeerde id of de hoofdklasse die zich in het hoofddefinitiebestand bevindt.|
    |Opdrachtregelargumenten| Optionele argumenten voor de taak.|
    |Verwijzingsbestanden| Aanvullende bestanden die worden gebruikt voor verwijzingen in het hoofddefinitiebestand. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount.|
    |Spark-pool| De taak wordt verzonden naar de geselecteerde Spark-pool.|
    |Spark-versie| Versie van Spark waarin de Spark-pool wordt uitgevoerd.|
    |Uitvoerders| Aantal uitvoerders dat moet worden opgegeven in de gespecificeerde Spark-pool voor de taak.|
    |Grootte van uitvoerder| Aantal kernen en geheugen dat moet worden gebruikt voor de uitvoerders die in de gespecificeerde Spark-pool voor de taak zijn opgegeven.|  
    |Grootte van stuurprogramma| Aantal kernen en geheugen dat moet worden gebruikt voor het stuurprogramma dat in de gespecificeerde Spark-pool voor de taak is opgegeven.|

    ![De waarde van de Spark-taakdefinitie instellen](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Selecteer **Taal** als **PySpark(Python)** .

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Naam van taakdefinitie| Voer een naam in voor de Spark-taakdefinitie.  In deze zelfstudie wordt `job definition sample` gebruikt. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd.|  
    |Primair definitiebestand| Het primaire bestand dat wordt gebruikt voor de taak. Selecteer een PY-bestand uit uw opslag. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount.|
    |Opdrachtregelargumenten| Optionele argumenten voor de taak.|
    |Verwijzingsbestanden| Aanvullende bestanden die worden gebruikt voor verwijzingen in het hoofddefinitiebestand. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount.|
    |Spark-pool| De taak wordt verzonden naar de geselecteerde Spark-pool.|
    |Spark-versie| Versie van Spark waarin de Spark-pool wordt uitgevoerd.|
    |Uitvoerders| Aantal uitvoerders dat moet worden opgegeven in de gespecificeerde Spark-pool voor de taak.|
    |Grootte van uitvoerder| Aantal kernen en geheugen dat moet worden gebruikt voor de uitvoerders die in de gespecificeerde Spark-pool voor de taak zijn opgegeven.|  
    |Grootte van stuurprogramma| Aantal kernen en geheugen dat moet worden gebruikt voor het stuurprogramma dat in de gespecificeerde Spark-pool voor de taak is opgegeven.|

    ![De waarde van de Spark-taakdefinitie instellen](./media/apache-spark-job-definitions/create-py-definition.png)

   * Selecteer **Taal** als **.NET Spark(C#/F#)** .

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Naam van taakdefinitie| Voer een naam in voor de Spark-taakdefinitie.  In deze zelfstudie wordt `job definition sample` gebruikt. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd.|  
    |Primair definitiebestand| Het primaire bestand dat wordt gebruikt voor de taak. Selecteer een ZIP-bestand dat uw .NET voor Spark-toepassing bevat (dat wil zeggen, het belangrijkste uitvoerbare bestand, DLL's met door de gebruiker gedefinieerde functies en andere vereiste bestanden) van uw opslag. U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount.|
    |Uitvoerbaar hoofdbestand| Het belangrijkste uitvoerbare bestand in het primaire ZIP-definitiebestand.|
    |Opdrachtregelargumenten| Optionele argumenten voor de taak.|
    |Verwijzingsbestanden| Aanvullende bestanden die nodig zijn voor de werkknooppunten voor het uitvoeren van de .NET voor Spark-toepassing die niet is opgenomen in het bestand met de primaire definitie (dat wil zeggen, afhankelijke JARs, aanvullende door de gebruiker gedefinieerde functie-DLL's en andere configuratiebestanden). U kunt **Bestand uploaden** selecteren om het bestand te uploaden naar een opslagaccount.|
    |Spark-pool| De taak wordt verzonden naar de geselecteerde Spark-pool.|
    |Spark-versie| Versie van Spark waarin de Spark-pool wordt uitgevoerd.|
    |Uitvoerders| Aantal uitvoerders dat moet worden opgegeven in de gespecificeerde Spark-pool voor de taak.|
    |Grootte van uitvoerder| Aantal kernen en geheugen dat moet worden gebruikt voor de uitvoerders die in de gespecificeerde Spark-pool voor de taak zijn opgegeven.|  
    |Grootte van stuurprogramma| Aantal kernen en geheugen dat moet worden gebruikt voor het stuurprogramma dat in de gespecificeerde Spark-pool voor de taak is opgegeven.|

    ![De waarde van de Spark-taakdefinitie instellen](./media/apache-spark-job-definitions/create-net-definition.png)

6. Selecteer **Publiceren** om de Spark-taakdefinitie op te slaan.

    ![Spark-taakdefinitie publiceren](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Een Spark-taakdefinitie verzenden

Nadat u een Spark-taakdefinitie hebt gemaakt, kunt u deze verzenden naar een Synapse Spark-pool. Zorg ervoor dat u de stappen in **Aan-de-slag** hebt doorlopen voordat u de voorbeelden in dit deel probeert.

### <a name="scenario-1-submit-spark-job-definition"></a>Scenario 1: Spark-taakdefinitie verzenden

1. Open een Spark-taakdefinitievenster door erop te klikken.

      ![Spark-taakdefinitie openen om te verzenden ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Klik op het pictogram **verzenden** om uw project te verzenden naar de geselecteerde Spark-pool. U kunt klikken op het tabblad **URL voor Spark-bewaking** om de LogQuery van de Spark-toepassing weer te geven.

    ![Klik op de knop verzenden om een Spark-taakdefinitie te verzenden](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Het dialoogvenster voor de verzending van Spark](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scenario 2: Voortgang van Spark-taak weergeven

1. Klik op **Bewaken** en selecteer vervolgens de optie **Spark-toepassingen**. U kunt de verzonden Spark-toepassing vinden.

    ![Spark-toepassing weergeven](./media/apache-spark-job-definitions/view-spark-application.png)

2. Klik vervolgens op de Spark-toepassing, het venster **LogQuery** wordt weergegeven. U kunt de voortgang van de taakuitvoering bekijken vanuit **LogQuery**.

    ![Spark-toepassing LogQuery weergeven](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: Uitvoerbestand controleren

 1. Klik op **Gegevens** en selecteer vervolgens **Opslagaccounts**. Nadat de uitvoering is voltooid, kunt u naar de ADLS Gen2-opslag gaan en controleren of de uitvoer is gegenereerd.

    ![Uitvoerbestand weergeven](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Volgende stappen

In dezezelf studie heeft u gezien hoe u de Azure Synapse Analytics kunt gebruiken om Spark-taakdefinities te maken en deze vervolgens naar een Synapse Spark-pool te verzenden. Vervolgens kunt u Azure Synapse Analytics gebruiken om Power BI-gegevenssets te maken en Power BI gegevens te beheren. 

- [Verbinding maken met gegevens in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Visualiseren met Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

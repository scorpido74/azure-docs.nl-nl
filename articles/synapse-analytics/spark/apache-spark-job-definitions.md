---
title: 'Zelf studie-Azure Synapse Analytics: Spark-taak definitie voor Synapse'
description: 'Zelf studie: gebruik de Azure Synapse Analytics om Spark-taak definities te maken en verzend deze naar een Synapse Spark-pool.'
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/15/2020
ms.openlocfilehash: d5443a2db6f4fecbd84ef51166f44c3a6e920aee
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422968"
---
# <a name="tutorial-use-azure-synapse-analytics-to-create-apache-spark-job-definitions-for-synapse-spark-pools"></a>Zelf studie: Azure Synapse Analytics gebruiken om Apache Spark taak definities te maken voor Synapse Spark-Pools

In deze zelf studie ziet u hoe u de Azure Synapse Analytics kunt gebruiken om Spark-taak definities te maken en deze vervolgens naar een Synapse Spark-pool te verzenden. U kunt de invoeg toepassing op een paar manieren gebruiken:

* Een Spark-taak definitie ontwikkelen en verzenden in een Synapse Spark-pool.
* Taak details weer geven na verzen ding.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een Spark-taak definitie ontwikkelen en verzenden in een Synapse Spark-pool.
> * Taak details weer geven na verzen ding.

## <a name="prerequisites"></a>Vereisten

* Een Azure Synapse Analytics-werk ruimte. Zie [een Azure Synapse Analytics-werk ruimte maken](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)voor instructies.

## <a name="get-started"></a>Aan de slag

Voordat u een Spark-taak definitie verzendt, moet u de gegevens eigenaar van de opslag-blob van het ADLS Gen2 bestands systeem waarmee u wilt werken. Als dat niet het geval is, moet u de machtiging hand matig toevoegen.

### <a name="scenario-1-add-permission"></a>Scenario 1: machtiging toevoegen

1. Open [Microsoft Azure](https://ms.portal.azure.com)en open vervolgens het opslag account.

2. Klik op **containers**en vervolgens op een **Bestands systeem**maken. In deze zelfstudie wordt `sparkjob` gebruikt.

    ![Klik op de knop verzenden om een Spark-taak definitie in te dienen](./media/apache-spark-job-definitions/open-azure-container.png)

    ![Het dialoog venster voor de verzen ding van Spark](./media/apache-spark-job-definitions/create-new-filesystem.png)

3. Open `sparkjob`, klik op **Access Control (IAM)**, klik op **toevoegen** en selecteer **functie toewijzing toevoegen**.

    ![Klik op de knop verzenden om een Spark-taak definitie in te dienen](./media/apache-spark-job-definitions/add-role-assignment-01.png)

    ![Klik op de knop verzenden om een Spark-taak definitie in te dienen](./media/apache-spark-job-definitions/add-role-assignment-02.png)

4. Klik **op roltoewijzingen,** invoer gebruikers naam en controleer vervolgens de gebruikersrol.

    ![Klik op de knop verzenden om een Spark-taak definitie in te dienen](./media/apache-spark-job-definitions/verify-user-role.png)

### <a name="scenario-2-prepare-folder-structure"></a>Scenario 2: mapstructuur voorbereiden

Voordat u een Spark-taak definitie indient, moet u een taak uploaden naar ADLS Gen2 en mappen structuur voorbereiden. We gebruiken opslag knooppunt in Synapse Studio om bestanden op te slaan.

1. Open [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Klik op **gegevens**, selecteer **opslag accounts**en upload de relevante bestanden naar uw ADLS Gen2 bestands systeem. Scala, Java, .NET en python worden ondersteund. In deze zelf studie wordt het voor beeld in de afbeelding als demonstratie gebruikt. u kunt de project structuur naar wens wijzigen.

    ![De waarde van de Spark-taak definitie instellen](./media/apache-spark-job-definitions/prepare-project-structure.png)

## <a name="create-a-spark-job-definition"></a>Een Spark-taak definitie maken

1. Open [Azure Synapse Analytics](https://web.azuresynapse.net/)en selecteer **ontwikkeling**.

2. Selecteer **Spark-taak definities** in het linkerdeel venster.

3. Klik op het knoop punt **acties** rechts van de "Spark-taak definities".

     ![Nieuwe Spark-taak definitie maken](./media/apache-spark-job-definitions/create-new-definition-01.png)

4. Selecteer in de vervolg keuzelijst **acties** de optie **nieuwe Spark-taak definitie**

     ![Nieuwe Spark-taak definitie maken](./media/apache-spark-job-definitions/create-new-definition-02.png)

5. In het venster nieuwe Spark-taak definitie selecteert u taal en geeft u de volgende informatie op:  

   * Selecteer **taal** als **Spark (scala)**.

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Naam van taak definitie| Voer een naam in voor de Spark-taak definitie.  In deze zelfstudie wordt `job definition sample` gebruikt. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd.|  
    |Hoofd definitie bestand| Het hoofd bestand dat wordt gebruikt voor de taak. Selecteer een JAR-bestand uit uw opslag. U kunt **bestand uploaden** selecteren om het bestand te uploaden naar een opslag account. |
    |Naam van hoofd klasse| De volledig gekwalificeerde id of de hoofd klasse die zich in het hoofd definitie bestand bevindt.|
    |Opdracht regel argumenten| Optionele argumenten voor de taak.|
    |Referentie bestanden| Aanvullende bestanden die worden gebruikt voor de verwijzing in het hoofd definitie bestand. U kunt **bestand uploaden** selecteren om het bestand te uploaden naar een opslag account.|
    |Spark-pool| De taak wordt verzonden naar de geselecteerde Spark-groep.|
    |Spark-versie| Versie van Spark waarin de Spark-pool wordt uitgevoerd.|
    |Executor| Aantal uitvoerende uitvoerders dat moet worden gegeven in de opgegeven Spark-groep voor de taak.|
    |Grootte van uitvoerder| Het aantal kernen en het geheugen dat moet worden gebruikt voor de uitvoerders die zijn opgegeven in de opgegeven Spark-groep voor de taak.|  
    |Stuur programma-grootte| Het aantal kernen en het geheugen dat moet worden gebruikt voor het stuur programma dat is opgegeven in de opgegeven Spark-groep voor de taak.|

    ![De waarde van de Spark-taak definitie instellen](./media/apache-spark-job-definitions/create-scala-definition.png)

   * Selecteer **taal** als **PySpark (python)**.

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Naam van taak definitie| Voer een naam in voor de Spark-taak definitie.  In deze zelfstudie wordt `job definition sample` gebruikt. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd.|  
    |Hoofd definitie bestand| Het hoofd bestand dat wordt gebruikt voor de taak. Selecteer een PY-bestand in uw opslag. U kunt **bestand uploaden** selecteren om het bestand te uploaden naar een opslag account.|
    |Opdracht regel argumenten| Optionele argumenten voor de taak.|
    |Referentie bestanden| Aanvullende bestanden die worden gebruikt voor de verwijzing in het hoofd definitie bestand. U kunt **bestand uploaden** selecteren om het bestand te uploaden naar een opslag account.|
    |Spark-pool| De taak wordt verzonden naar de geselecteerde Spark-groep.|
    |Spark-versie| Versie van Spark waarin de Spark-pool wordt uitgevoerd.|
    |Executor| Aantal uitvoerende uitvoerders dat moet worden gegeven in de opgegeven Spark-groep voor de taak.|
    |Grootte van uitvoerder| Het aantal kernen en het geheugen dat moet worden gebruikt voor de uitvoerders die zijn opgegeven in de opgegeven Spark-groep voor de taak.|  
    |Stuur programma-grootte| Het aantal kernen en het geheugen dat moet worden gebruikt voor het stuur programma dat is opgegeven in de opgegeven Spark-groep voor de taak.|

    ![De waarde van de Spark-taak definitie instellen](./media/apache-spark-job-definitions/create-py-definition.png)

   * Selecteer **taal** als **.net Spark (C#/f #)**.

    |  Eigenschap   | Beschrijving   |  
    | ----- | ----- |  
    |Naam van taak definitie| Voer een naam in voor de Spark-taak definitie.  In deze zelfstudie wordt `job definition sample` gebruikt. Deze naam kan op elk gewenst moment worden bijgewerkt totdat deze wordt gepubliceerd.|  
    |Hoofd definitie bestand| Het hoofd bestand dat wordt gebruikt voor de taak. Selecteer een ZIP-bestand dat uw .NET voor Spark-toepassing bevat (dat wil zeggen, het belangrijkste uitvoer bare bestand, Dll's met door de gebruiker gedefinieerde functies en andere vereiste bestanden) van uw opslag. U kunt **bestand uploaden** selecteren om het bestand te uploaden naar een opslag account.|
    |Belang rijke uitvoer bare bestand| Het belangrijkste uitvoer bare bestand in het primaire definitie ZIP-bestand.|
    |Opdracht regel argumenten| Optionele argumenten voor de taak.|
    |Referentie bestanden| Aanvullende bestanden die nodig zijn voor de worker-knoop punten voor het uitvoeren van de .NET voor Spark-toepassing die niet is opgenomen in het bestand met de hoofd definitie (dat wil zeggen, afhankelijke potten, aanvullende door de gebruiker gedefinieerde functie-Dll's en andere configuratie bestanden). U kunt **bestand uploaden** selecteren om het bestand te uploaden naar een opslag account.|
    |Spark-pool| De taak wordt verzonden naar de geselecteerde Spark-groep.|
    |Spark-versie| Versie van Spark waarin de Spark-pool wordt uitgevoerd.|
    |Executor| Aantal uitvoerende uitvoerders dat moet worden gegeven in de opgegeven Spark-groep voor de taak.|
    |Grootte van uitvoerder| Het aantal kernen en het geheugen dat moet worden gebruikt voor de uitvoerders die zijn opgegeven in de opgegeven Spark-groep voor de taak.|  
    |Stuur programma-grootte| Het aantal kernen en het geheugen dat moet worden gebruikt voor het stuur programma dat is opgegeven in de opgegeven Spark-groep voor de taak.|

    ![De waarde van de Spark-taak definitie instellen](./media/apache-spark-job-definitions/create-net-definition.png)

6. Selecteer **publiceren** om de Spark-taak definitie op te slaan.

    ![Spark-taak definitie publiceren](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-a-spark-job-definition"></a>Een Spark-taak definitie verzenden

Nadat u een Spark-taak definitie hebt gemaakt, kunt u deze verzenden naar een Synapse Spark-pool. Zorg ervoor dat u de stappen in **aan** de slag hebt door lopen voordat u voor beelden in dit gedeelte probeert.

### <a name="scenario-1-submit-spark-job-definition"></a>Scenario 1: Spark-taak definitie verzenden

1. Open een Spark-taak definitie venster door erop te klikken.

      ![De Spark-taak definitie openen om in te dienen ](./media/apache-spark-job-definitions/open-spark-definition.png)

2. Klik op **Verzend** pictogram om uw project in te dienen bij de geselecteerde Spark-groep. U kunt op het tabblad **URL voor Spark-bewaking** klikken om de LogQuery van de Spark-toepassing weer te geven.

    ![Klik op de knop verzenden om een Spark-taak definitie in te dienen](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Het dialoog venster voor de verzen ding van Spark](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-spark-job-running-progress"></a>Scenario 2: de voortgang van de uitvoering van Spark-taken weer geven

1. Klik op **bewaken**en selecteer vervolgens de optie **Spark-toepassingen** . U kunt de ingediende Spark-toepassing vinden.

    ![Spark-toepassing weer geven](./media/apache-spark-job-definitions/view-spark-application.png)

2. Klik vervolgens op de Spark-toepassing, **LogQuery** venster wordt weer gegeven. U kunt de voortgang van de taak uitvoering bekijken vanuit **LogQuery**.

    ![LogQuery van Spark-toepassing weer geven](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Scenario 3: het uitvoer bestand controleren

 1. Klik op **gegevens**en selecteer vervolgens **opslag accounts**. Nadat de uitvoering is voltooid, kunt u naar de ADLS Gen2 opslag gaan en controleren of de uitvoer is gegenereerd.

    ![Uitvoer bestand weer geven](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie wordt gedemonstreerd hoe u de Azure Synapse Analytics gebruikt om Spark-taak definities te maken en deze vervolgens te verzenden naar een Synapse Spark-pool. Vervolgens kunt u Azure Synapse Analytics gebruiken om Power BI gegevens sets te maken en Power BI gegevens te beheren. 

- [Verbinding maken met gegevens in Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-connect-to-data)
- [Visualiseren met Power BI](../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

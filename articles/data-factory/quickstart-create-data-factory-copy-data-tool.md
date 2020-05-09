---
title: Gegevens kopiëren met behulp van het hulp programma Azure Gegevens kopiëren
description: Maak een Azure data factory aan en gebruik vervolgens het hulpprogramma Copy Data om gegevens vanuit de ene locatie in Azure Blob Storage te kopiëren naar een andere locatie.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 03/18/2020
ms.openlocfilehash: e61bc750a06fb041e97def842e81fd62b614fcf6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982153"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Snelstartgids: gebruik het Gegevens kopiëren-hulp programma om gegevens te kopiëren

> [!div class="op_single_selector" title1="Selecteer de versie van Data Factory service die u gebruikt:"]
> * [Versie 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze snelstart gebruikt u Azure Portal voor het maken van een data factory. Gebruik vervolgens het hulpprogramma Copy Data om een pijplijn te maken waarmee gegevens uit een map in Azure Blob Storage worden gekopieerd naar een andere map. 

> [!NOTE]
> Als u niet bekend bent met Azure Data Factory, raadpleegt u eerst de [Inleiding voor Azure Data Factory](introduction.md) voordat u deze quickstart uitvoert. 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Ga naar de [Azure Portal](https://portal.azure.com). 
1. Selecteer in het menu Azure Portal de optie **een resource** > **analyse** > maken**Data Factory**:

    ![Nieuwe data factory maken](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in als **naam**. 
 
   De naam van de Azure-data factory moet *wereld wijd uniek*zijn. Als u de volgende fout ziet, wijzigt u de naam van de Data Factory (bijvoorbeeld ** &lt;&gt;ADFTutorialDataFactory**) en probeert u het opnieuw. Zie het artikel [Data factory - Naamgevingsregels](naming-rules.md) voor naamgevingsregels voor Data Factory-artefacten.
  
   ![Fout als de naam niet beschikbaar is](./media/doc-common-process/name-not-available-error.png)
1. Selecteer voor **Abonnement** het Azure-abonnement waarin u de gegevensfactory wilt maken. 
1. Voer een van de volgende stappen uit voor **Resourcegroep**:
     
   - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de lijst. 
   - Selecteer **nieuwe maken**en voer de naam van een resource groep in.   
         
   Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
1. Selecteer **V2** als **Versie**.
1. Selecteer voor **Locatie** een locatie voor de data factory.

   De lijst bevat alleen locaties die worden ondersteund door Data Factory en waar uw Azure Data Factory-metagegevens worden opgeslagen. De bijbehorende gegevens archieven (zoals Azure Storage en Azure SQL Database) en berekeningen (zoals Azure HDInsight) die Data Factory gebruikt, kunnen in andere regio's worden uitgevoerd.

1. Selecteer **Maken**.

1. Wanneer het maken is voltooid, ziet u de pagina **Data Factory**. Selecteer de tegel **Maken en controleren** om de gebruikersinterface (UI) van Azure Data Factory te openen op een afzonderlijk tabblad.
   
   ![Startpagina van Data Factory, met de tegel Maken en controleren](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Het hulpprogramma Copy Data starten

1. Selecteer op de pagina **Aan de slag** de tegel **Copy Data** om het hulpprogramma Copy Data te starten. 

   ![Tegel Copy Data](./media/doc-common-process/get-started-page.png)

1. Op de pagina **Eigenschappen** van het hulpprogramma Copy Data, kunt u een naam en beschrijving opgeven voor de pijplijn. Selecteer vervolgens **Volgende**. 

   ![Pagina Eigenschappen](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

    a. Klik op **+ nieuwe verbinding maken** om een verbinding toe te voegen.

    b. Selecteer het type van de gekoppelde service dat u voor de bron verbinding wilt maken. In deze zelf studie gebruiken we **Azure Blob Storage**. Selecteer deze in de galerie en selecteer vervolgens **door gaan**.
    
    ![BLOB selecteren](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Geef op de pagina **nieuwe gekoppelde service (Azure Blob Storage)** een naam op voor de gekoppelde service. Selecteer uw opslag account in de lijst **naam van opslag account** , test verbinding en selecteer vervolgens **maken**. 

    ![Het nieuwe Azure Blob Storage-account configureren](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als bron en klik vervolgens op **volgende**.


1. Voltooi op de pagina **Invoerbestand of invoermap kiezen** de volgende stappen:

   a. Klik op **Bladeren** om naar de map **adftutorial/input** te gaan, selecteer het bestand **EMP. txt** en klik vervolgens op **kiezen**. 

   d. Schakel het selectie vakje voor **binaire kopieën** in om het bestand te kopiëren, en selecteer vervolgens **volgende**. 

   ![Pagina Invoerbestand of invoermap kiezen](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. Selecteer op de pagina **doel gegevens archief** de **Azure Blob Storage** gekoppelde service die u hebt gemaakt en selecteer vervolgens **volgende**. 

1. Voer op de pagina **het uitvoer bestand of de map kiezen** **adftutorial/output** in voor het mappad en selecteer vervolgens **volgende**. 

   ![Pagina Uitvoerbestand of uitvoermap kiezen](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. Selecteer op de pagina **Instellingen** de optie **Volgende** om de standaardconfiguraties te gebruiken. 

1. Controleer op de pagina **Overzicht** alle instellingen. Selecteer vervolgens **Volgende**. 

1. Selecteer op de pagina **Implementatie voltooid** de optie **Controleren** om de pijplijn of taak die u hebt gemaakt, te controleren. 

    ![Pagina Implementatie voltooid](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. De toepassing wordt overgeschakeld naar het tabblad **monitor** . U ziet de status van de pijp lijn op dit tabblad. Selecteer **vernieuwen** om de lijst te vernieuwen. Klik op de koppeling onder **PIJPLIJN naam** om details van de activiteit weer te geven of de pijp lijn opnieuw uit te voeren. 
   
    ![Pijp lijn vernieuwen](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. Selecteer op de pagina uitvoeringen van activiteit de koppeling **Details** (bril pictogram) onder de kolom **activiteit naam** voor meer informatie over de Kopieer bewerking. Zie [Overzicht van kopieeractiviteit](copy-activity-overview.md) voor meer informatie over de eigenschappen. 

1. Als u wilt terugkeren naar de weer gave pijplijn uitvoeringen, selecteert u de koppeling **alle pijplijn uitvoeringen** in het navigatie menu. Selecteer **Vernieuwen** om de weergave te vernieuwen. 

1. Controleer of er een **emp.txt**- bestand is gemaakt in de **uitvoermap** van de container **adftutorial**. Als de uitvoermap niet bestaat, wordt deze automatisch door de Data Factory-service gemaakt. 

1. Schakel over naar het tabblad **Auteur** boven het tabblad **Monitor** op het linkerpaneel, zodat u de gekoppelde services, gegevenssets en pijplijnen kunt bewerken. Zie [Een gegevensfactory maken in Azure Portal](quickstart-create-data-factory-portal.md) voor meer informatie over het bewerken hiervan in de gebruikersinterface van Data Factory.

    ![Tabblad Auteur selecteren](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in een Azure Blob-opslag. Doorloop de [zelfstudies](tutorial-copy-data-portal.md) voor meer informatie over het gebruiken van Data Factory in andere scenario's. 

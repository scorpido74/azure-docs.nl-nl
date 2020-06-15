---
title: 'Quickstart: Een Apache Spark-taak uitvoeren in Azure Databricks met behulp van Azure Portal'
description: Deze quickstart laat zien hoe er met Azure Portal een Azure Databricks-werkruimte en een Apache Spark-cluster kan worden gemaakt, en hoe een Spark-taak kan worden uitgevoerd.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 03/23/2020
ms.custom: mvc, tracking-python
ms.openlocfilehash: 6c7226a56d5811438ec9d0703e0b8242df13e17b
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559309"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Quickstart: Een Apache Spark-taak uitvoeren in Azure Databricks met behulp van Azure Portal

In deze quickstart gebruikt u het Azure-portal om een Azure Databricks-werkruimte te maken met een Apache Spark-cluster. U voert een taak uit in de cluster en gebruikt aangepaste grafieken om realtime rapporten te produceren op basis van Boston-veiligheidsgegevens.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com).

> [!Note]
> Deze zelfstudie kan niet worden uitgevoerd met behulp van een **gratis Azure-proefabonnement**.
> Als u een gratis account hebt, gaat u naar uw profiel en wijzigt u uw abonnement in **Betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie. Vervolgens [verwijdert u de bestedingslimiet](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) en [vraagt u een quotumverhoging aan](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) voor vCPU’s in uw regio. Wanneer u uw Azure Databricks-werkruimte maakt, kunt u de prijscategorie **Proefversie (Premium - 14 dagen gratis DBU’s)** selecteren om de werkruimte 14 dagen lang toegang te geven tot gratis Premium Azure Databricks DBU’s.

## <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

In deze sectie gaat u een Azure Databricks-werkruimte maken met behulp van Azure Portal.

1. Selecteer in Azure Portal **Een resource maken** > **Analyse** > **Azure Databricks**.

    ![Databricks in de Azure-portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks in Azure-portal")

2. Geef bij **Azure Databricks Service** de waarden op voor het maken van een Databricks-werkruimte.

    ![Een Azure Databricks-werkruimte maken](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Een Azure Databricks-werkruimte maken")

    Geef de volgende waarden op:
    
    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |**Werkruimtenaam**     | Geef een naam op voor uw Databricks-werkruimte.        |
    |**Abonnement**     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
    |**Resourcegroep**     | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md) voor meer informatie. |
    |**Locatie**     | Selecteer **VS - west 2**. Zie [Producten beschikbaar per regio](https://azure.microsoft.com/regions/services/) voor andere beschikbare regio's.        |
    |**Prijscategorie**     |  U kunt kiezen tussen **Standard**, **Premium** en **Trial**. Bekijk de pagina [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën.       |

3. Selecteer **Controleren en maken** en vervolgens **Maken**. Het maken van de werkruimte duurt enkele minuten. Tijdens het maken van de werkruimte kunt u de implementatiestatus weergeven bij **Meldingen**. Zodra dit proces is voltooid, wordt uw gebruikersaccount automatisch als een gebruiker met beheerdersrechten toegevoegd in de werkruimte.

    ![Tegel voor Databricks-implementatie](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Tegel voor Databricks-implementatie")

    Wanneer de implementatie van een werkruimte mislukt, wordt de werkruimte nog steeds gemaakt, maar krijgt deze de status Mislukt. Verwijder de mislukte werkruimte en maak een nieuwe werkruimte waarin de implementatiefouten zijn opgelost. Wanneer u de mislukte werkruimte verwijdert, worden de beheerde resourcegroep en de resources die wel zijn geïmplementeerd ook verwijderd.

## <a name="create-a-spark-cluster-in-databricks"></a>Een Spark-cluster maken in Databricks

> [!NOTE]
> Als u een gratis account wilt gebruiken om het Azure Databricks-cluster te maken, gaat u voordat het cluster is gemaakt naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie.

1. Ga in Azure Portal naar de Databricks-werkruimte die u hebt gemaakt en klik op **Werkruimte starten**.

2. U wordt omgeleid naar de Azure Databricks-portal. Klik in de portal **Nieuw cluster**.

    ![Databricks in Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks in Azure")

3. Op de pagina **Nieuw cluster** geeft u de waarden op waarmee een nieuw cluster wordt gemaakt.

    ![Een Databricks Spark-cluster maken in Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Een Databricks Spark-cluster maken in Azure")

    Accepteer alle andere standaardwaarden, anders dan de volgende:

   * Voer een naam in voor het cluster.
   * Maak voor dit artikel een cluster met de **5.3**-runtime.
   * Zorg ervoor dat u het selectievakje **Beëindigen na\_\_ minuten van inactiviteit** inschakelt. Geef een duur (in minuten) op waarna het cluster moet worden beëindigd als het niet wordt gebruikt.
    
     Selecteer **Cluster maken**. Zodra het cluster wordt uitgevoerd, kunt u notitieblokken koppelen aan het cluster en Spark-taken uitvoeren.

Zie [Een Spark-cluster maken in Azure Databricks](/azure/databricks/clusters/create) voor meer informatie over het maken van clusters.

## <a name="run-a-spark-sql-job"></a>Een Spark SQL-taak uitvoeren

Voer de volgende taken uit om een notitieblok in Databricks te maken. Configureer het notitieblok om de gegevens te lezen uit een Azure Open Datasets en voer vervolgens een Spark SQL-taak uit op de gegevens.

1. Selecteer de knop **Azure Databricks** in het linkerdeelvenster. Selecteer in de **Algemene taken** **Nieuwe notebook**.

    ![Notebook maken in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Notitieblok maken in Databricks")

2. Voer in het dialoogvenster **Notitieblok maken** een naam in, selecteer **Python** als taal en selecteer het Spark-cluster dat u eerder hebt gemaakt.

    ![Notebook maken in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Notitieblok maken in Databricks")

    Selecteer **Maken**.

3. In deze stap maakt u een Spark DataFrame met Boston-veiligheidsgegevens uit [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks) en gebruikt u SQL om een query uit te voeren op de gegevens.

   Met de volgende opdracht stelt u de toegangsinformatie voor Azure Storage in. Plak deze PySpark-code in de eerste cel en gebruik **Shift + Enter** om de code uit te voeren.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Met de volgende opdracht kan Spark vanop afstand de Blob Storage lezen. Plak deze PySpark-code in de volgende cel en gebruik **Shift + Enter** om de code uit te voeren.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Met de volgende opdracht maakt u een DataFrame. Plak deze PySpark-code in de volgende cel en gebruik **Shift + Enter** om de code uit te voeren.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Voer een SQL-instructie uit om de bovenste 10 rijen met gegevens op te halen uit de tijdelijke weergave **bron**. Plak deze PySpark-code in de volgende cel en gebruik **Shift + Enter** om de code uit te voeren.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. U ziet uitvoer in tabelvorm zoals weergegeven in de volgende schermafbeelding (alleen bepaalde kolommen worden weergegeven):

    ![Voorbeeldgegevens](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Voorbeeld van JSON-gegevens")

6. U maakt nu een visuele weergave van deze gegevens om te laten zien hoeveel beveiligingsgebeurtenissen worden gerapporteerd via de burgers Connect App en de City Worker App in plaats van andere bronnen. Selecteer onder in de tabel met uitvoer op het pictogram voor het **staafdiagram** en klik vervolgens op **Tekenopties**.

    ![Staafdiagram maken](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Staafdiagram maken")

8. In **Tekening aanpassen** sleept en zet u de waarden neer zoals in de schermafbeelding wordt weergegeven.

    ![Cirkeldiagram aanpassen](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Staafdiagram aanpassen")

   * Stel **Sleutels** in op **bron**.
   * Stel **Waarden** in op **<\id>** .
   * Stel **Aggregatie** in op **AANTAL**.
   * Stel **Weergavetype** in op **Cirkeldiagram**.

     Klik op **Toepassen**.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt doorgenomen, kunt u het cluster beëindigen. Dit doet u door vanuit de Azure Databricks-werkruimte in het linkerdeelvenster **Clusters** te selecteren. Voor het cluster dat u wilt beëindigen, plaatst u de cursor op het weglatingsteken onder de kolom **Acties** en selecteert u het **beëindigingspictogram**.

![Een Databricks-cluster stoppen](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Een Databricks-cluster stoppen")

Als u het cluster niet handmatig beëindigt, stopt het cluster automatisch, op voorwaarde dat het selectievakje **Beëindigen na \_\_ minuten inactiviteit** is ingeschakeld tijdens het maken van het cluster. In dat geval stopt het cluster automatisch als het gedurende de opgegeven tijd inactief is geweest.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Spark-cluster in Azure Databricks gemaakt en een Spark-taak met gegevens uit Azure Storage Open Datasets. U kunt ook zoeken op [Spark gegevensbronnen](/azure/databricks/data/data-sources/index) voor meer informatie over het importeren van gegevens uit andere gegevensbronnen in Azure Databricks. Ga naar het volgende artikel voor informatie over het uitvoeren van een ETL-bewerking (Extraction, Transformation, and Loading) met behulp van Azure Databricks.

> [!div class="nextstepaction"]
>[Gegevens uitpakken, transformeren en laden met Azure Databricks](databricks-extract-load-sql-data-warehouse.md)

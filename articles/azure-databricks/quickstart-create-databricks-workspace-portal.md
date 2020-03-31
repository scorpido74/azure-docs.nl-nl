---
title: Snelstart - Een Spark-taak uitvoeren op Azure Databricks met Azure-portal
description: In deze snelstart wordt uitgelegd hoe u de Azure-portal gebruiken om een Azure Databricks-werkruimte, een Apache Spark-cluster en een Spark-taak uit te voeren.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 03/23/2020
ms.custom: mvc
ms.openlocfilehash: d6af521238a034bc22612335119f08284b87eb4b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132711"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Quickstart: Een Spark-taak uitvoeren op Azure Databricks met Azure portal

In deze quickstart gebruikt u de Azure-portal om een Azure Databricks-werkruimte te maken met een Apache Spark-cluster. U voert een taak uit op het cluster en gebruikt aangepaste grafieken om realtime rapporten te produceren van boston-veiligheidsgegevens.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com).

> [!Note]
> Deze zelfstudie kan niet worden uitgevoerd met **azure free trial subscription**.
> Als je een gratis account hebt, ga je naar je profiel en wijzig je je abonnement naar **betalen per gebruik.** Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie. Verwijder vervolgens [de bestedingslimiet](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)en [vraag een quotumverhoging aan](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) voor vCPU's in uw regio. Wanneer u uw Azure Databricks-werkruimte maakt, u de prijscategorie **Proefkeuzeprijzen (Premium - 14-dagen gratis DU's)** selecteren om de werkruimte gedurende 14 dagen toegang te geven tot gratis Premium Azure Databricks DU's.

## <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

In deze sectie gaat u een Azure Databricks-werkruimte maken met behulp van Azure Portal.

1. Selecteer in de Azure-portal de optie **Een resource** > **Analytics** > **Azure Databricks maken**.

    ![Databricks op Azure-portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks op Azure-portal")

2. Geef bij **Azure Databricks Service** de waarden op voor het maken van een Databricks-werkruimte.

    ![Een Azure Databricks-werkruimte maken](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Een Azure Databricks-werkruimte maken")

    Geef de volgende waarden op:
    
    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |**Werkruimtenaam**     | Geef een naam op voor uw Databricks-werkruimte.        |
    |**Abonnement**     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
    |**Resourcegroep**     | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md) voor meer informatie. |
    |**Locatie**     | Selecteer **VS - west 2**. Zie [Producten beschikbaar per regio](https://azure.microsoft.com/regions/services/) voor andere beschikbare regio's.        |
    |**Prijsniveau**     |  Kies tussen **Standaard,** **Premium**of **Proef.** Bekijk de pagina [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën.       |

3. Selecteer **Controleren + Maken**en maak vervolgens **.** Het maken van de werkruimte duurt enkele minuten. Tijdens het maken van werkruimtes u de implementatiestatus weergeven in **Meldingen**. Zodra dit proces is voltooid, wordt uw gebruikersaccount automatisch toegevoegd als beheerder in de werkruimte.

    ![De implementatietegel van Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "De implementatietegel van Databricks")

    Wanneer een implementatie van een werkruimte mislukt, wordt de werkruimte nog steeds gemaakt in een mislukte status. Verwijder de mislukte werkruimte en maak een nieuwe werkruimte die de implementatiefouten oplost. Wanneer u de mislukte werkruimte verwijdert, worden ook de beheerde resourcegroep en eventuele met succes geïmplementeerde resources verwijderd.

## <a name="create-a-spark-cluster-in-databricks"></a>Een Spark-cluster maken in Databricks

> [!NOTE]
> Als u een gratis account wilt gebruiken om het Azure Databricks-cluster te maken, gaat u voordat het cluster is gemaakt naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie.

1. Ga in Azure Portal naar de Databricks-werkruimte die u hebt gemaakt en klik op **Werkruimte starten**.

2. U wordt omgeleid naar de Azure Databricks-portal. Klik in de portal op **Nieuw cluster**.

    ![Databricks op Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks op Azure")

3. Op de pagina **Nieuw cluster** geeft u de waarden op waarmee een nieuw cluster wordt gemaakt.

    ![Databricks Spark-cluster maken op Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Databricks Spark-cluster maken op Azure")

    Accepteer alle andere standaardwaarden, anders dan de volgende:

   * Voer een naam in voor het cluster.
   * Maak voor dit artikel een cluster met een runtime **van 5,3.**
   * Schakel het selectievakje **Beëindigen \_ \_ na minuten inactiviteit in.** Geef een duur (in minuten) op waarna het cluster moet worden beëindigd als het niet wordt gebruikt.
    
     Selecteer **Cluster maken**. Zodra het cluster wordt uitgevoerd, kunt u notitieblokken koppelen aan het cluster en Spark-taken uitvoeren.

Zie [Een Spark-cluster maken in Azure Databricks](/azure/databricks/clusters/create) voor meer informatie over het maken van clusters.

## <a name="run-a-spark-sql-job"></a>Een Spark SQL-taak uitvoeren

Voer de volgende taken uit om een notitieblok te maken in Databricks, configureer het notitieblok om gegevens uit een Azure Open Datasets te lezen en voer vervolgens een Spark SQL-taak op de gegevens uit.

1. Selecteer **Azure Databricks**in het linkerdeelvenster . Selecteer **Nieuw notitieblok**in de **algemene taken**.

    ![Notitieblok maken in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Notitieblok maken in Databricks")

2. Voer **in** het dialoogvenster Notitieblok maken een naam in, selecteer **Python** als taal en selecteer het Spark-cluster dat u eerder hebt gemaakt.

    ![Notitieblok maken in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Notitieblok maken in Databricks")

    Selecteer **Maken**.

3. Maak in deze stap een Spark DataFrame met Boston Safety Data van [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)en gebruik SQL om de gegevens op te vragen.

   Met de volgende opdracht worden de toegangsgegevens voor Azure-opslag ingesteld. Plak deze PySpark-code in de eerste cel en gebruik **Shift+Enter** om de code uit te voeren.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Met de volgende opdracht kan Spark op afstand uit blob-opslag worden uitgelezen. Plak deze PySpark-code in de volgende cel en gebruik **Shift+Enter** om de code uit te voeren.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Met de volgende opdracht wordt een DataFrame maakt. Plak deze PySpark-code in de volgende cel en gebruik **Shift+Enter** om de code uit te voeren.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Voer een SQL-instructie uit en retourneer de bovenste 10 rijen gegevens uit de tijdelijke weergave **bron**. Plak deze PySpark-code in de volgende cel en gebruik **Shift+Enter** om de code uit te voeren.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. U ziet uitvoer in tabelvorm zoals weergegeven in de volgende schermafbeelding (alleen bepaalde kolommen worden weergegeven):

    ![Voorbeeldgegevens](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Voorbeeld van JSON-gegevens")

6. U maakt nu een visuele weergave van deze gegevens om aan te geven hoeveel veiligheidsgebeurtenissen worden gerapporteerd met behulp van de App Burgers Connect en de App voor stadswerknemers in plaats van andere bronnen. Selecteer onder aan de tabeluitvoer het pictogram **Staafdiagram** en klik op **Plotopties**.

    ![Staafdiagram maken](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Staafdiagram maken")

8. In **Tekening aanpassen** sleept en zet u de waarden neer zoals in de schermafbeelding wordt weergegeven.

    ![Cirkeldiagram aanpassen](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Staafdiagram aanpassen")

   * **Sleutels instellen op** **bron**.
   * **Waarden** instellen op **><\id **.
   * Stel **Aggregatie** in op **AANTAL**.
   * **Weergavetype** instellen op **cirkeldiagram**.

     Klik op **Toepassen**.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt doorgenomen, kunt u het cluster beëindigen. Dit doet u door vanuit de Azure Databricks-werkruimte in het linkerdeelvenster **Clusters** te selecteren. Voor het cluster dat u wilt beëindigen, plaatst u de cursor op het weglatingsteken onder de kolom **Acties** en selecteert u het **beëindigingspictogram**.

![Een cluster met Databricks stoppen](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Een cluster met Databricks stoppen")

Als u het cluster niet handmatig beëindigt, stopt het automatisch, op voorwaarde dat u het selectievakje **Beëindigen na \_ \_ minuten van inactiviteit** hebt geselecteerd tijdens het maken van het cluster. In dat geval stopt het cluster automatisch als het gedurende de opgegeven tijd inactief is geweest.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Spark-cluster gemaakt in Azure Databricks en een Spark-taak uitgevoerd met gegevens uit Azure Open-gegevenssets. U kunt ook zoeken op [Spark gegevensbronnen](/azure/databricks/data/data-sources/index) voor meer informatie over het importeren van gegevens uit andere gegevensbronnen in Azure Databricks. Ga naar het volgende artikel voor informatie over het uitvoeren van een ETL-bewerking (Extraction, Transformation, and Loading) met behulp van Azure Databricks.

> [!div class="nextstepaction"]
>[Gegevens uitpakken, transformeren en laden met Azure Databricks](databricks-extract-load-sql-data-warehouse.md)

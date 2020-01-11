---
title: 'Quick Start: een Spark-taak uitvoeren op Azure Databricks met behulp van Azure Portal'
description: In deze Quick start ziet u hoe u Azure Portal kunt gebruiken om een Azure Databricks-werk ruimte, een Apache Spark-cluster en een Spark-taak te maken.
services: azure-databricks
ms.service: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 452efd5c790244ff035f06673342a36522db9580
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889018"
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Quickstart: Een Spark-taak uitvoeren op Azure Databricks met Azure portal

In deze Quick Start gebruikt u de Azure Portal om een Azure Databricks-werk ruimte te maken met een Apache Spark cluster. U voert een taak uit op het cluster en gebruikt aangepaste grafieken om realtime rapporten te produceren op basis van Boston-veiligheids gegevens.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure Portal](https://portal.azure.com).

> [!Note]
> Deze zelf studie kan niet worden uitgevoerd met een **gratis proef abonnement van Azure**.
> Als u een gratis account hebt, gaat u naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie. Vervolgens [verwijdert u de bestedings limiet](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)en [vraagt u een quotum toename](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) aan voor vcpu's in uw regio. Wanneer u uw Azure Databricks-werk ruimte maakt, kunt u de prijs categorie **Trial (Premium-14-dagen gratis dbu's)** selecteren om de werk ruimte gedurende 14 dagen toegang te geven tot gratis premium Azure Databricks dbu's.

## <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

In deze sectie gaat u een Azure Databricks-werkruimte maken met behulp van Azure Portal.

1. Selecteer in Azure Portal **Een resource maken** > **Analyse** > **Azure Databricks**.

    ![Databricks op Azure Portal](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks op Azure Portal")

2. Geef bij **Azure Databricks Service** de waarden op voor het maken van een Databricks-werkruimte.

    ![Een Azure Databricks-werk ruimte maken](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Een Azure Databricks-werkruimte maken")

    Geef de volgende waarden op:
    
    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |**Werkruimtenaam**     | Geef een naam op voor uw Databricks-werkruimte.        |
    |**Abonnement**     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
    |**Resourcegroep**     | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie [Overzicht van Azure Resource Manager](../azure-resource-manager/management/overview.md) voor meer informatie. |
    |**Locatie**     | Selecteer **US - west 2**. Zie [Producten beschikbaar per regio](https://azure.microsoft.com/regions/services/) voor andere beschikbare regio's.        |
    |**Prijscategorie**     |  Kies tussen **Standard**, **Premium**of **proef versie**. Bekijk de pagina [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën.       |
    |**Virtueel netwerk**     |  Kies voor het implementeren van een Azure Databricks-werk ruimte in uw eigen Virtual Network (VNet). Zie [Deploy Azure Databricks in uw Azure Virtual Network (VNet-injectie)](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)voor meer informatie.        |

    Selecteer **Maken**.

4. Het maken van de werkruimte duurt enkele minuten. Tijdens het maken van de werk ruimte kunt u de implementatie status weer geven in **meldingen**.

    ![Tegel implementatie van Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Tegel implementatie van Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Een Spark-cluster maken in Databricks

> [!NOTE]
> Als u een gratis account wilt gebruiken om het Azure Databricks-cluster te maken, gaat u voordat het cluster is gemaakt naar uw profiel en wijzigt u uw abonnement in **betalen per gebruik**. Zie [Gratis Azure-account](https://azure.microsoft.com/free/) voor meer informatie.

1. Ga in Azure Portal naar de Databricks-werkruimte die u hebt gemaakt en klik op **Werkruimte starten**.

2. U wordt omgeleid naar de Azure Databricks-portal. Klik in de portal op **Nieuw cluster**.

    ![Databricks op Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks op Azure")

3. Op de pagina **Nieuw cluster** geeft u de waarden op waarmee een nieuw cluster wordt gemaakt.

    ![Een Databricks Spark-cluster maken in azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Een Databricks Spark-cluster maken in azure")

    Accepteer alle andere standaardwaarden, anders dan de volgende:

   * Voer een naam in voor het cluster.
   * Voor dit artikel maakt u een cluster met **5,3** runtime.
   * Zorg ervoor dat u het selectievakje **Beëindigen na\_\_ minuten van inactiviteit** inschakelt. Geef een duur (in minuten) op waarna het cluster moet worden beëindigd als het niet wordt gebruikt.
    
     Selecteer **Cluster maken**. Zodra het cluster wordt uitgevoerd, kunt u notitieblokken koppelen aan het cluster en Spark-taken uitvoeren.

Zie [Een Spark-cluster maken in Azure Databricks](/azure/databricks/clusters/create) voor meer informatie over het maken van clusters.

## <a name="run-a-spark-sql-job"></a>Een Spark SQL-taak uitvoeren

Voer de volgende taken uit om een notitie blok in Databricks te maken, Configureer het notitie blok om gegevens van een Azure open-gegevens sets te lezen en voer vervolgens een Spark SQL-taak uit op de gegevens.

1. Selecteer **Azure Databricks**in het linkerdeel venster. Selecteer in **algemene taken**de optie **Nieuw notitie blok**.

    ![Een notitie blok maken in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Een notitie blok maken in Databricks")

2. Voer in het dialoog venster **notebook maken** een naam in, selecteer **python** als taal en selecteer het Spark-cluster dat u eerder hebt gemaakt.

    ![Een notitie blok maken in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Een notitie blok maken in Databricks")

    Selecteer **Maken**.

3. In deze stap maakt u een Spark-data frame met Boston-veiligheids gegevens van [Azure open gegevens sets](https://azure.microsoft.com/services/open-datasets/catalog/boston-safety-data/#AzureDatabricks)en gebruikt u SQL om de gegevens op te vragen.

   Met de volgende opdracht stelt u de toegangs gegevens voor Azure Storage in. Plak deze PySpark-code in de eerste cel en gebruik **SHIFT + ENTER** om de code uit te voeren.

   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "citydatacontainer"
   blob_relative_path = "Safety/Release/city=Boston"
   blob_sas_token = r"?st=2019-02-26T02%3A34%3A32Z&se=2119-02-27T02%3A34%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=XlJVWA7fMXCSxCKqJm8psMOh0W4h7cSYO28coRqF2fs%3D"
   ```

   Met de volgende opdracht staat u toe dat Spark van de Blob-opslag extern wordt gelezen. Plak deze PySpark-code in de volgende cel en gebruik **SHIFT + ENTER** om de code uit te voeren.

   ```python
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
   print('Remote blob path: ' + wasbs_path)
   ```

   Met de volgende opdracht maakt u een data frame. Plak deze PySpark-code in de volgende cel en gebruik **SHIFT + ENTER** om de code uit te voeren.

   ```python
   df = spark.read.parquet(wasbs_path)
   print('Register the DataFrame as a SQL temporary view: source')
   df.createOrReplaceTempView('source')
   ```

4. Een SQL-instructie uitvoeren retourneert de bovenste 10 rijen met gegevens uit de tijdelijke weer gave met de naam **bron**. Plak deze PySpark-code in de volgende cel en gebruik **SHIFT + ENTER** om de code uit te voeren.

   ```python
   print('Displaying top 10 rows: ')
   display(spark.sql('SELECT * FROM source LIMIT 10'))
   ```

5. U ziet uitvoer in tabelvorm zoals weergegeven in de volgende schermafbeelding (alleen bepaalde kolommen worden weergegeven):

    ![Voorbeeldgegevens](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Voor beeld van JSON-gegevens")

6. U maakt nu een visuele weer gave van deze gegevens om te laten zien hoeveel beveiligings gebeurtenissen worden gerapporteerd met de burgers Connect app en City worker app in plaats van andere bronnen. Selecteer op de onderkant van de tabel uitvoer het pictogram **staaf diagram** en klik vervolgens op **Opties tekenen**.

    ![Staaf diagram maken](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Staaf diagram maken")

8. In **Tekening aanpassen** sleept en zet u de waarden neer zoals in de schermafbeelding wordt weergegeven.

    ![Cirkel diagram aanpassen](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Staaf diagram aanpassen")

   * Stel **sleutels** in op **bron**.
   * Stel **waarden** in op **< \id >** .
   * Stel **Aggregatie** in op **AANTAL**.
   * **Weergave type** instellen op **cirkel diagram**.

     Klik op **Toepassen**.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt doorgenomen, kunt u het cluster beëindigen. Dit doet u door vanuit de Azure Databricks-werkruimte in het linkerdeelvenster **Clusters** te selecteren. Voor het cluster dat u wilt beëindigen, plaatst u de cursor op het weglatingsteken onder de kolom **Acties** en selecteert u het **beëindigingspictogram**.

![Een Databricks-cluster stoppen](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Een Databricks-cluster stoppen")

Als u het cluster niet handmatig beëindigt, stopt het cluster automatisch, op voorwaarde dat het selectievakje **Beëindigen na \_\_ minuten inactiviteit** is ingeschakeld tijdens het maken van het cluster. In dat geval stopt het cluster automatisch als het gedurende de opgegeven tijd inactief is geweest.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Spark-cluster gemaakt in Azure Databricks en een Spark-taak uitgevoerd met gegevens uit Azure open data sets. U kunt ook zoeken op [Spark gegevensbronnen](/azure/databricks/data/data-sources/index) voor meer informatie over het importeren van gegevens uit andere gegevensbronnen in Azure Databricks. Ga naar het volgende artikel voor informatie over het uitvoeren van een ETL-bewerking (Extraction, Transformation, and Loading) met behulp van Azure Databricks.

> [!div class="nextstepaction"]
>[Gegevens uitpakken, transformeren en laden met Azure Databricks](databricks-extract-load-sql-data-warehouse.md)

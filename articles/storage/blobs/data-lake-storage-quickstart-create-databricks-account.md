---
title: 'Snelstartgids: gegevens in Azure Data Lake Storage Gen2 analyseren met behulp van Azure Databricks | Microsoft Docs'
description: Leer hoe u een Spark-taak kunt uitvoeren op Azure Databricks met behulp van de Azure-portal en een Azure Data Lake Storage Gen2-opslagaccount.
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.reviewer: jeking
ms.openlocfilehash: 5badd4aeabd8ec322ea5fb847cf134f302269c27
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330985"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Snelstartgids: gegevens in Azure Data Lake Storage Gen2 analyseren met behulp van Azure Databricks

Deze quickstart laat zien hoe u een Apache Spark-taak met Azure Databricks kunt uitvoeren voor het analyseren van gegevens die in een opslagaccount zijn opgeslagen waarvoor Azure Data Lake Storage Gen2 is ingeschakeld.

Als onderdeel van de Apache Spark-taak gaat u abonnementsgegevens van een radiozender analyseren om meer inzicht te krijgen in vrij/betaald gebruik op basis van demografische gegevens.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Data Lake Gen2-opslagaccount maken. Zie [Quick Start: een Azure data Lake Storage Gen2 Storage-account maken](data-lake-storage-quickstart-create-account.md)

  Plak de naam van het opslagaccount in een tekstbestand. U hebt deze naam binnenkort nodig.

* Een service-principal maken. Zie [How to: de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Er zijn een paar specifieke zaken die u moet doen terwijl u de stappen in het artikel uitvoert.

  : heavy_check_mark: bij het uitvoeren van de stappen in de sectie [toepassing toewijzen aan een rol](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) van het artikel, moet u ervoor zorgen dat u de rol van **BLOB voor gegevens opslag** aan de Service-Principal toewijst.

  > [!IMPORTANT]
  > Zorg ervoor dat u de rol toewijst in het bereik van het Data Lake Storage Gen2-opslagaccount. U kunt een rol toewijzen aan de bovenliggende resourcegroep of het bovenliggende abonnement, maar u ontvangt machtigingsgerelateerde fouten tot die roltoewijzingen zijn doorgegeven aan het opslagaccount.

  : heavy_check_mark: als u de stappen in de sectie [waarden ophalen voor ondertekening in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) het artikel uitvoert, plakt u de Tenant-id, app-id en wachtwoord waarden in een tekst bestand. U hebt deze binnenkort nodig.

## <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

In deze sectie gaat u een Azure Databricks-werkruimte maken met behulp van Azure Portal.

1. Selecteer in Azure Portal **Een resource maken** > **Analyse** > **Azure Databricks**.

    ![Databricks in Azure Portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks in Azure Portal")

2. Geef bij **Azure Databricks Service** de waarden op voor het maken van een Databricks-werkruimte.

    ![Een Azure Databricks-werkruimte maken](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Een Azure Databricks-werkruimte maken")

    Geef de volgende waarden op:

    |Eigenschap  |Beschrijving  |
    |---------|---------|
    |**Werkruimtenaam**     | Geef een naam op voor uw Databricks-werkruimte.        |
    |**Abonnement**     | Selecteer uw Azure-abonnement in de vervolgkeuzelijst.        |
    |**Resourcegroep**     | Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie [Overzicht van Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) voor meer informatie. |
    |**Locatie**     | Selecteer **US - west 2**. U kunt desgewenst een andere openbare regio selecteren.        |
    |**Prijscategorie**     |  U kunt kiezen tussen **Standard** en **Premium**. Bekijk de pagina [Prijzen voor Databricks](https://azure.microsoft.com/pricing/details/databricks/) voor meer informatie over deze categorieën.       |

3. Het duurt enkele minuten om het account te maken. Bekijk de voortgangsbalk bovenaan om de bewerkingsstatus te volgen.

4. Selecteer **Vastmaken aan dashboard** en selecteer **Maken**.

## <a name="create-a-spark-cluster-in-databricks"></a>Een Spark-cluster maken in Databricks

1. Ga in Azure Portal naar de Databricks-werkruimte die u hebt gemaakt en selecteer **Werkruimte starten**.

2. U wordt omgeleid naar de Azure Databricks-portal. Selecteer in de portal **Nieuw** > **Cluster**.

    ![Databricks in Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks in Azure")

3. Op de pagina **Nieuw cluster** geeft u de waarden op waarmee een nieuw cluster wordt gemaakt.

    ![Een Databricks Spark-cluster maken in Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Een Databricks Spark-cluster maken in Azure")

    Vul de waarden voor de volgende velden in (en laat bij de overige velden de standaardwaarden staan):

    - Voer een naam in voor het cluster.
     
    - Zorg ervoor dat u het selectievakje **Beëindigen na 120 minuten van inactiviteit** inschakelt. Geef een duur (in minuten) op waarna het cluster moet worden beëindigd als het niet wordt gebruikt.

4. Selecteer **Cluster maken**. Zodra het cluster wordt uitgevoerd, kunt u notitieblokken koppelen aan het cluster en Spark-taken uitvoeren.

Zie [Een Spark-cluster maken in Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html) voor meer informatie over het maken van clusters.

## <a name="create-storage-account-container"></a>Een opslag account container maken

In deze sectie maakt u een notitieblok in de Azure Databricks-werkruimte en voert u vervolgens codefragmenten uit om het opslagaccount te configureren.

1. Ga in [Azure Portal](https://portal.azure.com) naar de Azure Databricks-werkruimte die u hebt gemaakt en selecteer **Werkruimte starten**.

2. Selecteer **Werkruimte** in het linkerdeelvenster. Selecteer in de **Werkruimte**-vervolgkeuzelijst, **Notitieblok** > **maken**.

    ![Een notitieblok maken in Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Een notitieblok maken in Databricks")

3. Voer in het dialoogvenster **Notitieblok maken** een naam voor het notitieblok in. Selecteer **Scala** als taal en selecteer het Spark-cluster dat u eerder hebt gemaakt.

    ![Een notitieblok maken in Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Een notitieblok maken in Databricks")

    Selecteer **Maken**.

4. Kopieer en plak het volgende codeblok in de eerste cel, maar voer deze code nog niet uit.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```

    > [!NOTE]
    > Dit codeblok heeft direct toegang tot het Data Lake Gen2-eindpunt door middel van OAuth, maar er zijn andere manieren om de Databricks-werkruimte aan uw Data Lake Storage Gen2-account te koppelen. U kunt bijvoorbeeld de container koppelen met behulp van OAuth of een directe toegang met een gedeelde sleutel gebruiken. <br>Zie het artikel [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) (Engelstalig) op de website van Azure Databricks voor voorbeelden van deze methoden.

5. In dit codeblok vervangt u de tijdelijke aanduidingen `storage-account-name`, `appID`, `password` en `tenant-id` door de waarden die u hebt verzameld toen u de service-principal maakte. Stel de waarde van de tijdelijke aanduiding voor @no__t 0 in op de naam die u wilt toewijzen aan de container.

    > [!NOTE]
    > In een productieomgeving kunt u de verificatiesleutel eventueel in Azure Databricks opslaan. Vervolgens voegt u een opzoeksleutel toe aan uw codeblok in plaats van de verificatiesleutel. Nadat u deze quickstart hebt afgerond, raadpleegt u het artikel [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) (Engelstalig) op de website van Azure Databricks voor voorbeelden van deze methode.

6. Druk op de toetsen **Shift + Enter** om de code in dit blok uit te voeren.

## <a name="ingest-sample-data"></a>Voorbeeldgegevens opnemen

Voordat u met deze sectie begint, moet u eerst aan de volgende vereisten voldoen:

Voer de volgende code in een notitieblokcel in:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Druk in de cel op **SHIFT+ENTER** om de code uit te voeren.

Voer nu in een cel onder deze cel de volgende code in, en vervang de waarden tussen haakjes door dezelfde waarden die u eerder hebt gebruikt:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

Druk in de cel op **SHIFT+ENTER** om de code uit te voeren.

## <a name="run-a-spark-sql-job"></a>Een Spark SQL-taak uitvoeren

Voer de volgende taken uit om een Spark SQL-taak op de gegevens uit te voeren.

1. Voer een SQL-instructie uit om een tijdelijke tabel te maken met gegevens uit het JSON-voorbeeldgegevensbestand, **small_radio_json.json**. Vervang in het volgende codefragment de tijdelijke aanduidingen voor waarden door de containernaam en de naam van het opslagaccount. Plak het codefragment in een nieuwe codecel in het eerder gemaakte notitieblok en druk op Shift+Enter.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json"
    )
    ```

    Zodra de opdracht is voltooid, hebt u alle gegevens van het JSON-bestand als een tabel in het Databricks-cluster.

    De magic-opdracht in de taal `%sql` stelt u in staat u SQL-code uit te voeren vanuit het notitieblok, zelfs als het notitieblok van een ander type is. Zie [Talen combineren in een notitieblok](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook) voor meer informatie.

2. Laten we eens een momentopname bekijken van de voorbeeld-JSON-gegevens om een beter begrip te krijgen van de query die u uitvoert. Plak het volgende codefragment in de codecel en druk op **Shift+Enter**.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. U ziet uitvoer in tabelvorm zoals weergegeven in de volgende schermafbeelding (alleen bepaalde kolommen worden weergegeven):

    ![Voorbeeld-JSON-gegevens](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Voorbeeld JSON-gegevens")

    Naast andere gegevens wordt in de voorbeeldgegevens ook het geslacht van de doelgroep van een radiokanaal vastgelegd (kolomnaam **geslacht**) en of het om een gratis dan wel betaald abonnement gaat (kolomnaam **niveau**).

4. U gaat nu een visuele weergave van deze gegevens maken om voor elk geslacht te zien kunnen hoeveel gebruikers een gratis account hebben en hoeveel een betaald. Klik onder in de tabel met uitvoer op het pictogram voor het **staafdiagram** en klik vervolgens op **Tekenopties**.

    ![Staafdiagram maken](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Staafdiagram maken")

5. In **Tekening aanpassen** sleept en zet u de waarden neer zoals in de schermafbeelding wordt weergegeven.

    ![Staafdiagram aanpassen](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Staafdiagram aanpassen")

    - Stel **Sleutels** in op **geslacht**.
    - Stel **Reeksgroeperingen** in op **niveau**.
    - Stel **Waarden** in op **niveau**.
    - Stel **Aggregatie** in op **AANTAL**.

6. Klik op **Apply** (Toepassen).

7. De uitvoer toont de visuele weergave zoals de volgende schermafbeelding laat zien:

     ![Staafdiagram aanpassen](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Staafdiagram aanpassen")

## <a name="clean-up-resources"></a>Resources opschonen

Beëindig het cluster wanneer u klaar bent met het artikel. Selecteer **Clusters** in de Azure Databricks-werkruimte en zoek het cluster dat u wilt beëindigen. Plaats de cursor op het weglatingsteken onder de kolom **Acties** en selecteer het pictogram **Beëindigen**.

![Een Databricks-cluster stopzetten](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Een Databricks-cluster stopzetten")

Als u het cluster niet handmatig beëindigt, stopt het cluster automatisch, op voorwaarde dat het selectievakje **Beëindigen na \_\_ minuten van inactiviteit** is ingeschakeld tijdens het maken van het cluster. Als u deze optie instelt, wordt het cluster gestopt als het gedurende de opgegeven hoeveelheid tijd inactief is.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een Spark-cluster in Azure Databricks gemaakt en een Spark-taak met gegevens uitgevoerd in een opslagaccount waarvoor Data Lake Storage Gen2 is ingeschakeld. U kunt ook zoeken op [Spark gegevensbronnen](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) voor meer informatie over het importeren van gegevens uit andere gegevensbronnen in Azure Databricks. Ga naar het volgende artikel voor informatie over het uitvoeren van een ETL-bewerking (Extraction, Transformation, and Loading) met behulp van Azure Databricks.

> [!div class="nextstepaction"]
>[Gegevens uitpakken, transformeren en laden met Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)

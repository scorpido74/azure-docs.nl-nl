---
title: 'Zelfstudie: Het patroon voor het vastleggen van gegevensmeer implementeren om een Azure Databricks Delta-tabel bij te werken | Microsoft Documenten'
description: In deze zelfstudie ziet u hoe u een abonnement op eventgrid, een Azure-functie en een Azure Databricks-taak gebruikt om rijen met gegevens in te voegen in een tabel die is opgeslagen in Azure DataLake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.openlocfilehash: 85fad873b6c176d2278ea48709d2892ab515a025
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303304"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Zelfstudie: Het patroon voor het vastleggen van gegevensmeer implementeren om een Tabel Met Gegevensbricks Delta bij te werken

In deze zelfstudie ziet u hoe u gebeurtenissen verwerken in een opslagaccount met een hiërarchische naamruimte.

U bouwt een kleine oplossing waarmee een gebruiker een Gegevensbricks Delta-tabel kan invullen door een csv-bestand (comma-separated values) te uploaden waarin een verkooporder wordt beschreven. U bouwt deze oplossing door een Event Grid-abonnement, een Azure-functie en een taak in Azure Databricks met elkaar [te](https://docs.azuredatabricks.net/user-guide/jobs.html) verbinden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een Event Grid-abonnement dat een Azure-functie aanroept.
> * Maak een Azure-functie die een melding van een gebeurtenis ontvangt en vervolgens de taak uitvoert in Azure Databricks.
> * Maak een Databricks-taak die een klantorder invoegt in een Gegevensbricks Delta-tabel die zich in het opslagaccount bevindt.

We bouwen deze oplossing in omgekeerde volgorde, te beginnen met de Azure Databricks-werkruimte.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

* Maak een opslagaccount met een hiërarchische naamruimte (Azure Data Lake Storage Gen2). Deze zelfstudie maakt `contosoorders`gebruik van een opslagaccount met de naam . Zorg ervoor dat aan uw gebruikersaccount de [rol van Gegevensbijdrager voor opslagblob](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) is toegewezen.

  Zie [Een Azure Data Lake Storage Gen2-account maken](data-lake-storage-quickstart-create-account.md).

* Een service-principal maken. Zie [Hoe: Gebruik de portal om een Azure AD-toepassing en serviceprincipal te maken die toegang hebben tot bronnen.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

  Er zijn een paar specifieke zaken die u moet doen terwijl u de stappen in het artikel uitvoert.

  :heavy_check_mark: Wanneer u de stappen uitvoert in de [toepassing Toewijzen aan een rolsectie](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) van het artikel, moet u de rol **Opslagblobgegevensinzender** toewijzen aan de serviceprincipal.

  > [!IMPORTANT]
  > Zorg ervoor dat u de rol toewijst in het bereik van het Data Lake Storage Gen2-opslagaccount. U kunt een rol toewijzen aan de bovenliggende resourcegroep of het bovenliggende abonnement, maar u ontvangt machtigingsgerelateerde fouten tot die roltoewijzingen zijn doorgegeven aan het opslagaccount.

  :heavy_check_mark: Wanneer u de stappen uitvoert in het gedeelte [Waarden opvragen voor aanmelden in](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) het artikel, plakt u de tenant-id, app-id en wachtwoordwaarden in een tekstbestand. Die waarden heb je later nodig.

## <a name="create-a-sales-order"></a>Een verkooporder maken

Maak eerst een csv-bestand dat een verkooporder beschrijft en upload dat bestand vervolgens naar het opslagaccount. Later gebruikt u de gegevens uit dit bestand om de eerste rij in onze Tabel Databricks Delta te vullen.

1. Open Azure Storage Explorer. Navigeer vervolgens naar uw opslagaccount en maak in de sectie **Blob-containers** een nieuwe container met de naam **gegevens**.

   ![gegevensmap](./media/data-lake-storage-events/data-container.png "gegevensmap")

   Zie [Azure Storage Explorer gebruiken om gegevens te beheren in een Azure Data Lake Storage Gen2-account voor](data-lake-storage-explorer.md)meer informatie over het gebruik van Storage Explorer.

2. Maak in de **gegevenscontainer** een map met de naam **invoer**.

3. Plak de volgende tekst in een teksteditor.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Sla dit bestand op uw lokale computer op en geef het de naam **data.csv**.

5. Upload dit bestand in Storage Explorer naar de **invoermap.**  

## <a name="create-a-job-in-azure-databricks"></a>Een taak maken in Azure Databricks

In deze sectie voert u de volgende taken uit:

* Een Azure Databricks-werkruimte maken.
* Maak een notebook.
* Een Tabel Databricks Delta maken en invullen.
* Voeg code toe die rijen invoegt in de tabel Delta met Gegevensstenen.
* Een taak maken.

### <a name="create-an-azure-databricks-workspace"></a>Een Azure Databricks-werkruimte maken

In deze sectie gaat u een Azure Databricks-werkruimte maken met behulp van Azure Portal.

1. Selecteer in de Azure-portal de optie **Een resource** > **Analytics** > **Azure Databricks maken**.

    ![Databricks op Azure-portal](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks op Azure-portal")

2. Geef bij **Azure Databricks Service** de waarden op voor het maken van een Databricks-werkruimte.

    ![Een Azure Databricks-werkruimte maken](./media/data-lake-storage-events/new-databricks-service.png "Een Azure Databricks-werkruimte maken")

    Het maken van de werkruimte duurt enkele minuten. Bekijk de voortgangsbalk bovenaan om de bewerkingsstatus te volgen.

### <a name="create-a-spark-cluster-in-databricks"></a>Een Spark-cluster maken in Databricks

1. Ga in de [Azure-portal](https://portal.azure.com)naar de Azure Databricks-werkruimte die u hebt gemaakt en selecteer **Vervolgens Werkruimte starten**.

2. U wordt omgeleid naar de Azure Databricks-portal. Selecteer **Nieuw** > **cluster**in de portal .

    ![Databricks op Azure](./media/data-lake-storage-events/databricks-on-azure.png "Databricks op Azure")

3. Op de pagina **Nieuw cluster** geeft u de waarden op waarmee een nieuw cluster wordt gemaakt.

    ![Databricks Spark-cluster maken op Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Databricks Spark-cluster maken op Azure")

    Accepteer alle andere standaardwaarden, anders dan de volgende:

    * Voer een naam in voor het cluster.
    * Zorg ervoor dat u het selectievakje **Beëindigen na 120 minuten van inactiviteit** inschakelt. Geef een duur (in minuten) op waarna het cluster moet worden beëindigd als het niet wordt gebruikt.

4. Selecteer **Cluster maken**. Zodra het cluster wordt uitgevoerd, kunt u notitieblokken koppelen aan het cluster en Spark-taken uitvoeren.

Zie [Een Spark-cluster maken in Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html) voor meer informatie over het maken van clusters.

### <a name="create-a-notebook"></a>Een notebook maken

1. Selecteer **Werkruimte** in het linkerdeelvenster. Selecteer in de **Werkruimte**-vervolgkeuzelijst, **Notitieblok** > **maken**.

    ![Notitieblok maken in Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Notitieblok maken in Databricks")

2. Voer in het dialoogvenster **Notitieblok maken** een naam voor het notitieblok in. Selecteer **Python** als taal en selecteer vervolgens het Apache Spark-cluster dat u eerder hebt gemaakt.

    ![Notitieblok maken in Databricks](./media/data-lake-storage-events/new-databricks-notebook.png "Notitieblok maken in Databricks")

    Selecteer **Maken**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Een Gegevensbricks Delta-tabel maken en bevullen

1. Kopieer en plak het volgende codeblok in het notitieblok dat u hebt gemaakt in de eerste cel, maar voer deze code nog niet uit.  

   Vervang `appId`de `password` `tenant` tijdelijke aanduidingswaarden in dit codeblok door de waarden die u hebt verzameld terwijl u de vereisten van deze zelfstudie voltooit.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Met deze code wordt een widget met de naam **source_file**. Later maakt u een Azure-functie die deze code aanroept en een bestandspad doorgeeft aan die widget.  Deze code verifieert ook uw serviceprincipal met het opslagaccount en maakt een aantal variabelen die u in andere cellen zult gebruiken.

    > [!NOTE]
    > In een productieomgeving kunt u de verificatiesleutel eventueel in Azure Databricks opslaan. Vervolgens voegt u een opzoeksleutel toe aan uw codeblok in plaats van de verificatiesleutel. <br><br>In plaats van deze coderegel `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")`te gebruiken, gebruikt u bijvoorbeeld `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`de volgende coderegel: . <br><br>Nadat u deze zelfstudie hebt voltooid, raadpleegt u het artikel [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) op de Azure Databricks-website om voorbeelden van deze aanpak te bekijken.

2. Druk op de toetsen **Shift + Enter** om de code in dit blok uit te voeren.

3. Kopieer en plak het volgende codeblok in een andere cel en druk vervolgens op de **shift+ ENTER-toetsen** om de code in dit blok uit te voeren.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Met deze code wordt de Tabel Gegevensbricks Delta gemaakt in uw opslagaccount en worden vervolgens enkele eerste gegevens geladen uit het csv-bestand dat u eerder hebt geüpload.

4. Nadat dit codeblok is uitgevoerd, verwijdert u dit codeblok uit uw notitieblok.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Code toevoegen die rijen invoegt in de tabel Delta met Gegevensstenen

1. Kopieer en plak het volgende codeblok in een andere cel, maar voer deze cel niet uit.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Met deze code worden gegevens in een tijdelijke tabelweergave ingevoegd met behulp van gegevens uit een csv-bestand. Het pad naar dat csv-bestand is afkomstig van de invoerwidget die u in een eerdere stap hebt gemaakt.

2. Voeg de volgende code toe om de inhoud van de tijdelijke tabelweergave samen te voegen met de tabel Gegevensbricks Delta.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Een taak maken

Maak een taak waarop het notitieblok wordt uitgevoerd dat u eerder hebt gemaakt. Later maakt u een Azure-functie waarmee deze taak wordt uitgevoerd wanneer een gebeurtenis wordt verhoogd.

1. Klik **op Vacatures**.

2. Klik **op** de pagina Vacatures op **Taak maken**.

3. Geef de taak een naam `upsert-order-data` en kies de werkmap.

   ![Een taak maken](./media/data-lake-storage-events/create-spark-job.png "Een taak maken")

## <a name="create-an-azure-function"></a>Een Azure-functie maken

Maak een Azure-functie waarop de taak wordt uitgevoerd.

1. Kies in de bovenhoek van de werkruimte Databricks het pictogram Personen en kies **vervolgens Gebruikersinstellingen**.

   ![Account beheren](./media/data-lake-storage-events/generate-token.png "Gebruikersinstellingen")

2. Klik **op** de knop Nieuwe token genereren en klik vervolgens op de knop **Genereren.**

   Zorg ervoor dat u het token naar een veilige plaats kopieert. Uw Azure-functie heeft dit token nodig om te verifiëren met Databricks, zodat het werk kan worden uitgevoerd.
  
3. Selecteer de knop **Een resource maken** in de linkerbovenhoek van de Azure-portal en selecteer vervolgens Compute > **Functie-app**.

   ![Een Azure-functie maken](./media/data-lake-storage-events/function-app-create-flow.png "Azure maken, functie")

4. Selecteer op de pagina **Maken** van de functie-app **.NET Core** voor de runtimestack en configureer een instantie Application Insights.

   ![De functie-app configureren](./media/data-lake-storage-events/new-function-app.png "De functie-app configureren")

5. Klik op de pagina **Overzicht** van de functie-app op **Configuratie**.

   ![De functie-app configureren](./media/data-lake-storage-events/configure-function-app.png "De functie-app configureren")

6. Kies op de pagina **Toepassingsinstellingen** de knop **Nieuwe toepassingsinstelling** om elke instelling toe te voegen.

   ![Configuratie-instelling toevoegen](./media/data-lake-storage-events/add-application-setting.png "Configuratie-instelling toevoegen")

   Voeg de volgende instellingen toe:

   |Naam van instelling | Waarde |
   |----|----|
   |**DBX_INSTANCE**| De regio van uw databricks-werkruimte. Bijvoorbeeld: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| Het persoonlijke toegangstoken dat u eerder hebt gegenereerd. |
   |**DBX_JOB_ID**|De id van de lopende taak. In ons geval is `1`deze waarde.|
7. Klik op de overzichtspagina van de functie-app op de knop **Nieuwe functie.**

   ![Nieuwe functie](./media/data-lake-storage-events/new-function.png "Nieuwe functie")

8. Kies **Azure Event Grid Trigger**.

   Installeer de extensie **Microsoft.Azure.WebJobs.Extensions.EventGrid** als u hierom wordt gevraagd. Als u het moet installeren, moet u **Azure Event Grid Trigger** opnieuw kiezen om de functie te maken.

   Het deelvenster **Nieuwe functie** wordt weergegeven.

9. Geef in het deelvenster **Nieuwe functie** de naam van de functie **UpsertOrder**en klik vervolgens op de knop **Maken.**

10. Vervang de inhoud van het codebestand door deze code en klik op de knop **Opslaan:**

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Deze code ontleedt informatie over de opslaggebeurtenis die is verhoogd en maakt vervolgens een aanvraagbericht met url van het bestand dat de gebeurtenis heeft geactiveerd. Als onderdeel van het bericht geeft de functie een waarde door aan de **source_file** widget die u eerder hebt gemaakt. de functiecode stuurt het bericht naar de Taak Databricks en gebruikt het token dat u eerder hebt verkregen als verificatie.

## <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

In deze sectie maakt u een Event Grid-abonnement dat de Azure-functie aanroept wanneer bestanden worden geüpload naar het opslagaccount.

1. Klik op de pagina met functiecode op de knop **Abonnement op gebeurtenisraster toevoegen.**

   ![Nieuw evenementabonnement](./media/data-lake-storage-events/new-event-subscription.png "Nieuw evenementabonnement")

2. Geef op de pagina **Gebeurtenisabonnement maken** een naam aan het abonnement en gebruik de velden op de pagina om uw opslagaccount te selecteren.

   ![Nieuw evenementabonnement](./media/data-lake-storage-events/new-event-subscription-2.png "Nieuw evenementabonnement")

3. Selecteer in de vervolgkeuzelijst **Filter naar gebeurtenistypen** de blob **gemaakt**en **blob verwijderde** gebeurtenissen en klik op de knop **Maken.**

## <a name="test-the-event-grid-subscription"></a>Het abonnement voor eventgrid testen

1. Maak een `customer-order.csv`bestand met de naam , plak de volgende informatie in dat bestand en sla het op op uw lokale computer.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. Upload dit bestand in Storage Explorer naar de **invoermap** van uw opslagaccount.

   Als u een bestand uploadt, wordt de gebeurtenis **Microsoft.Storage.BlobCreated gesommeerd.** Event Grid waarschuwt alle abonnees van dat evenement. In ons geval is de Azure-functie de enige abonnee. De Azure-functie ontleden de gebeurtenisparameters om te bepalen welke gebeurtenis is opgetreden. Vervolgens wordt de URL van het bestand doorgegeven aan de Taak Databricks. De taak Databricks leest het bestand en voegt een rij toe aan de tabel Gegevensbricks Delta die uw opslagaccount bevindt.

3. Als u wilt controleren of de taak is geslaagd, opent u de werkruimte gegevensstenen, klikt u op de knop **Taken** en opent u uw taak.

4. Selecteer de taak om de taakpagina te openen.

   ![Spark-taak](./media/data-lake-storage-events/spark-job.png "Spark-taak")

   Wanneer de taak is voltooid, ziet u een voltooiingsstatus.

   ![Met succes voltooide taak](./media/data-lake-storage-events/spark-job-completed.png "Met succes voltooide taak")

5. Voer deze query in een nieuwe werkmapcel uit in een cel om de bijgewerkte deltatabel weer te geven.

   ```
   %sql select * from customer_data
   ```

   De geretourneerde tabel toont de laatste plaat.

   ![Laatste record verschijnt in tabel](./media/data-lake-storage-events/final_query.png "Laatste record verschijnt in tabel")

6. Als u deze record wilt `customer-order-update.csv`bijwerken, maakt u een bestand met de naam , plakt u de volgende informatie in dat bestand en slaat u het op op uw lokale computer.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Dit csv-bestand is bijna identiek aan het vorige bestand, behalve dat de hoeveelheid van de bestelling wordt gewijzigd van `228` . `22`

7. Upload dit bestand in Storage Explorer naar de **invoermap** van uw opslagaccount.

8. Voer `select` de query opnieuw uit om de bijgewerkte deltatabel weer te geven.

   ```
   %sql select * from customer_data
   ```

   De geretourneerde tabel toont de bijgewerkte record.

   ![Bijgewerkte record wordt weergegeven in tabel](./media/data-lake-storage-events/final_query-2.png "Bijgewerkte record wordt weergegeven in tabel")

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Hiervoor selecteert u de resourcegroep voor het opslagaccount en selecteert u **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Reageren op gebeurtenissen van Blob Storage](storage-blob-event-overview.md)

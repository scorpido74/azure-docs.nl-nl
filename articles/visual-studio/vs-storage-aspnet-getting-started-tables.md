---
title: Aan de slag met Azure Table Storage met behulp van Visual Studio (ASP.NET)
description: Aan de slag met Azure Table Storage in een ASP.NET-project in Visual Studio nadat u verbinding hebt gemaakt met een opslag account met behulp van Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ec391db0e18128ad85d3bacb79fa51770aa70ea1
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298893"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Aan de slag met Azure Table Storage en Visual Studio Connected Services (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Overzicht

Met Azure-tabel opslag kunt u grote hoeveel heden gestructureerde gegevens opslaan. De service is een NoSQL-gegevens opslag die geverifieerde aanroepen binnen en buiten de Azure-Cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens.

In deze zelf studie leert u hoe u ASP.NET-code kunt schrijven voor een aantal algemene scenario's die gebruikmaken van Azure Table storage-entiteiten. Deze scenario's zijn onder andere het maken van een tabel en het toevoegen, opvragen en verwijderen van tabel entiteiten. 

## <a name="prerequisites"></a>Vereisten

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage-account](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Een MVC-controller maken 

1. In de **Solution Explorer**, klikt u met de rechter muisknop op **controllers**en selecteert u in het context menu de optie **Add-> controller**.

    ![Een controller toevoegen aan een ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Selecteer in het dialoog venster **steigers toevoegen** de optie **MVC 5 controller-empty**en selecteer **toevoegen**.

    ![MVC-controller type opgeven](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Geef in het dialoog venster **controller toevoegen** de naam van de controller *TablesController*en selecteer **toevoegen**.

    ![De MVC-controller een naam](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Voeg *de volgende instructies* toe aan het `TablesController.cs`-bestand:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Een model klasse maken

Veel van de voor beelden in dit artikel gebruiken een met **TableEntity**afgeleid klasse met de naam **CustomerEntity**. De volgende stappen begeleiden u bij het declareren van deze klasse als een model klasse:

1. Klik in de **Solution Explorer**met de rechter muisknop op **modellen**en selecteer in het context menu de **klasse add->** .

1. Geef in het dialoog venster **Nieuw item toevoegen** de klasse de naam **CustomerEntity**.

1. Open het bestand `CustomerEntity.cs` en voeg **de volgende instructie** toe:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Wijzig de klasse zodat de klasse na voltooiing wordt gedeclareerd als in de volgende code. De klasse declareert een entiteits klasse met de naam **CustomerEntity** die de voor naam van de klant als de rij-en achternaam als de partitie sleutel gebruikt.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Een tabel maken

De volgende stappen laten zien hoe u een tabel maakt:

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen. 

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode toe met de naam **CreateTable** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. In de methode **CreateTable** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag connection string en het opslag account op te halen uit de configuratie van de Azure-service: (Wijzig *&lt;storage-account naam >* in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient** -object ophalen vertegenwoordigt een Table service-client.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Haal een **CloudTable** -object op dat een verwijzing naar de gewenste tabel naam vertegenwoordigt. De methode **CloudTableClient. GetTableReference** maakt geen aanvraag voor tabel opslag. De verwijzing wordt geretourneerd, ongeacht of de tabel bestaat. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Roep de methode **CloudTable. CreateIfNotExists** aan om de tabel te maken als deze nog niet bestaat. De methode **CloudTable. CreateIfNotExists** retourneert **True** als de tabel niet bestaat en is gemaakt. Anders wordt **False** geretourneerd.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Werk de **ViewBag** bij met de naam van de tabel.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. Vouw in de **Solution Explorer**de map **weer gaven** uit, klik met de rechter muisknop op **tabellen**en selecteer in het context menu de optie **toevoegen-> weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **CreateTable** in als weergave naam en selecteer **toevoegen**.

1. Open `CreateTable.cshtml` en wijzig de waarde zodat deze eruitziet als het volgende code fragment:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Vouw in het **Solution Explorer**de **gedeelde map weer gaven->** uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **tabel maken** om de resultaten te bekijken die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Tabel maken](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Zoals eerder vermeld, retourneert de methode **CloudTable. CreateIfNotExists** alleen **True** als de tabel niet bestaat en wordt gemaakt. Als u de app uitvoert wanneer de tabel bestaat, retourneert de methode daarom **False**. Als u de app meerdere keren wilt uitvoeren, moet u de tabel verwijderen voordat u de app opnieuw uitvoert. Het verwijderen van de tabel kan worden uitgevoerd via de methode **CloudTable. Delete** . U kunt de tabel ook verwijderen met behulp van de [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) of de [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

*Entiteiten* worden toegewezen aan C @ no__t-1-objecten met behulp van een aangepaste klasse die is afgeleid van **TableEntity**. Als u een entiteit wilt toevoegen aan een tabel, maakt u een klasse die de eigenschappen van uw entiteit definieert. In deze sectie ziet u hoe u een entiteits klasse definieert die de voor naam van de klant gebruikt als de rij-en achternaam als partitie sleutel. De partitie- en rijsleutel van een entiteit vormen samen de unieke id van de entiteit in de tabel. Entiteiten met dezelfde partitiesleutel kunnen sneller worden opgevraagd dan entiteiten met verschillende partitiesleutels, maar het gebruik van verschillende partitiesleutels maakt een grotere schaalbaarheid van parallelle bewerkingen mogelijk. Voor elke eigenschap die in de tabel service moet worden opgeslagen, moet de eigenschap een open bare eigenschap van een ondersteund type zijn waarmee waarden voor het instellen en ophalen worden weer gegeven.
De entiteits klasse *moet* een open bare constructor zonder para meters declareren.

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen.

1. Open het `TablesController.cs`-bestand.

1. Voeg de volgende instructie toe zodat de code in het bestand @no__t 0 toegang kan krijgen tot de klasse **CustomerEntity** :

    ```csharp
    using StorageAspnet.Models;
    ```

1. Voeg een methode toe met de naam **AddEntity** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. In de methode **AddEntity** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag connection string en het opslag account op te halen uit de configuratie van de Azure-service: (Wijzig *&lt;storage-account naam >* in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient** -object ophalen vertegenwoordigt een Table service-client.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Haal een **CloudTable** -object op dat een verwijzing naar de tabel vertegenwoordigt waaraan u de nieuwe entiteit gaat toevoegen. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Maak een exemplaar van de **CustomerEntity** -klasse en Initialiseer deze.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Maak een **TableOperation** -object waarmee de klant entiteit wordt ingevoegd.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Voer de INSERT-bewerking uit door de methode **CloudTable. Execute** aan te roepen. U kunt het resultaat van de bewerking controleren door de eigenschap **TableResult. http status code** te controleren. Een status code van 2xx geeft aan dat de actie die door de client is aangevraagd, is verwerkt. Geslaagde invoegingen van nieuwe entiteiten resulteren bijvoorbeeld in een HTTP-status code van 204, wat inhoudt dat de bewerking is verwerkt en dat de server geen inhoud heeft geretourneerd.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Werk de **ViewBag** bij met de tabel naam en de resultaten van de invoeg bewerking.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. Vouw in de **Solution Explorer**de map **weer gaven** uit, klik met de rechter muisknop op **tabellen**en selecteer in het context menu de optie **toevoegen-> weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **AddEntity** in als weergave naam en selecteer **toevoegen**.

1. Open `AddEntity.cshtml` en wijzig de waarde zodat deze eruitziet als het volgende code fragment:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. Vouw in het **Solution Explorer**de **gedeelde map weer gaven->** uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **entiteit toevoegen** om resultaten te zien die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Entiteit toevoegen](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    U kunt controleren of de entiteit is toegevoegd door de stappen in de sectie te volgen, [een afzonderlijke entiteit ophalen](#get-a-single-entity). U kunt ook de [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) gebruiken om alle entiteiten voor uw tabellen weer te geven.

## <a name="add-a-batch-of-entities-to-a-table"></a>Een batch-entiteit toevoegen aan een tabel

U kunt niet alleen [een entiteit toevoegen aan een tabel per keer](#add-an-entity-to-a-table), maar ook entiteiten toevoegen in batch. Als entiteiten in batch worden toegevoegd, wordt het aantal retouren tussen uw code en de Azure Table-service verminderd. De volgende stappen laten zien hoe u meerdere entiteiten kunt toevoegen aan een tabel met één invoeg bewerking:

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen.

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode toe met de naam **AddEntities** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. In de methode **AddEntities** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag connection string en het opslag account op te halen uit de configuratie van de Azure-service: (Wijzig *&lt;storage-account naam >* in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient** -object ophalen vertegenwoordigt een Table service-client.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Haal een **CloudTable** -object op dat een verwijzing naar de tabel vertegenwoordigt waaraan u de nieuwe entiteiten gaat toevoegen. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Maak een exemplaar van sommige klant objecten op basis van de model klasse **CustomerEntity** die in de sectie wordt weer gegeven. [Voeg een entiteit toe aan een tabel](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Een **TableBatchOperation** -object ophalen.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Entiteiten toevoegen aan het bewerkings object voor batch invoegen.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Voer de batch INSERT-bewerking uit door de methode **CloudTable. ExecuteBatch** aan te roepen.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. De methode **CloudTable. ExecuteBatch** retourneert een lijst met **TableResult** -objecten waarbij elk **TableResult** -object kan worden onderzocht om het slagen of mislukken van elke afzonderlijke bewerking te bepalen. Voor dit voor beeld geeft u de lijst door aan een weer gave en laat u de resultaten van elke bewerking weer geven. 
 
    ```csharp
    return View(results);
    ```

1. Vouw in de **Solution Explorer**de map **weer gaven** uit, klik met de rechter muisknop op **tabellen**en selecteer in het context menu de optie **toevoegen-> weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **AddEntities** in als weergave naam en selecteer **toevoegen**.

1. Open `AddEntities.cshtml` en wijzig de waarde zodat deze er als volgt uitziet.

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. Vouw in het **Solution Explorer**de **gedeelde map weer gaven->** uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **entiteiten toevoegen** om resultaten te zien die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Entiteiten toevoegen](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    U kunt controleren of de entiteit is toegevoegd door de stappen in de sectie te volgen, [een afzonderlijke entiteit ophalen](#get-a-single-entity). U kunt ook de [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) gebruiken om alle entiteiten voor uw tabellen weer te geven.

## <a name="get-a-single-entity"></a>Eén entiteit ophalen

In deze sectie wordt beschreven hoe u één entiteit uit een tabel kunt ophalen met behulp van de rij-en partitie sleutel van de entiteit. 

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen en gebruikt gegevens uit het [toevoegen van een batch met entiteiten aan een tabel](#add-a-batch-of-entities-to-a-table). 

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode toe met de naam **GetSingle** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. In de methode **GetSingle** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag connection string en het opslag account op te halen uit de configuratie van de Azure-service: (Wijzig *&lt;storage-account naam >* in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient** -object ophalen vertegenwoordigt een Table service-client.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Een **CloudTable** -object ophalen dat een verwijzing vertegenwoordigt naar de tabel van waaruit u de entiteit ophaalt. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Maak een object voor ophalen dat een entiteits object gebruikt dat is afgeleid van **TableEntity**. De eerste para meter is de *partitionKey*en de tweede para meter is de *rowKey*. Met behulp van de **CustomerEntity** -klasse en de gegevens die in de sectie worden weer gegeven, [voegt u een batch entiteit toe aan een tabel](#add-a-batch-of-entities-to-a-table). het volgende code fragment voert een query uit op de tabel voor een **CustomerEntity** -entiteit met de *partitionKey* -waarde "Smith" en een  *rowKey* waarde van ' ben ':

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. De ophaal bewerking uitvoeren.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Het resultaat wordt door gegeven aan de weer gave voor weer gave.

    ```csharp
    return View(result);
    ```

1. Vouw in de **Solution Explorer**de map **weer gaven** uit, klik met de rechter muisknop op **tabellen**en selecteer in het context menu de optie **toevoegen-> weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **GetSingle** in als weergave naam en selecteer **toevoegen**.

1. Open `GetSingle.cshtml` en wijzig de waarde zodat deze eruitziet als het volgende code fragment:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. Vouw in het **Solution Explorer**de **gedeelde map weer gaven->** uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **Eén** om resultaten weer te geven die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Eén ophalen](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Alle entiteiten in een partitie ophalen

Zoals vermeld in de sectie, [voegt u een entiteit toe aan een tabel](#add-an-entity-to-a-table), de combi natie van een partitie en een rijlabel sleutel om een entiteit in een tabel uniek te identificeren. Entiteiten met dezelfde partitie sleutel kunnen sneller worden opgevraagd dan entiteiten met verschillende partitie sleutels. In deze sectie wordt beschreven hoe u een tabel voor alle entiteiten uit een opgegeven partitie kunt opvragen.  

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen en gebruikt gegevens uit het [toevoegen van een batch met entiteiten aan een tabel](#add-a-batch-of-entities-to-a-table). 

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode toe met de naam **GetPartition** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. In de methode **GetPartition** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag connection string en het opslag account op te halen uit de configuratie van de Azure-service: (Wijzig *&lt;storage-account naam >* in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient** -object ophalen vertegenwoordigt een Table service-client.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Een **CloudTable** -object ophalen dat een verwijzing vertegenwoordigt naar de tabel van waaruit u de entiteiten ophaalt. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Maak een instantie van een **TableQuery** -object dat de query in de component **where** opgeeft. Met behulp van de **CustomerEntity** -klasse en de gegevens die in de sectie worden weer gegeven, [voegt u een batch-entiteit toe aan een tabel](#add-a-batch-of-entities-to-a-table). het volgende code fragment voert een query uit op de tabel voor alle entiteiten waar de **PartitionKey** (achternaam van de klant) de waarde Smith heeft:

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Binnen een lus roept u de methode **CloudTable. ExecuteQuerySegmented** aan waarmee het query-object wordt door gegeven dat u in de vorige stap hebt geïnstantieerd.  De methode **CloudTable. ExecuteQuerySegmented** retourneert een **TableContinuationToken** -object dat-wanneer **Null** -geeft aan dat er geen entiteiten meer zijn om op te halen. Gebruik in de lus een andere lus om de geretourneerde entiteiten te herhalen. In het volgende code voorbeeld wordt elke geretourneerde entiteit toegevoegd aan een lijst. Zodra de lus is beëindigd, wordt de lijst door gegeven aan een weer gave voor weer gave: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. Vouw in de **Solution Explorer**de map **weer gaven** uit, klik met de rechter muisknop op **tabellen**en selecteer in het context menu de optie **toevoegen-> weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **GetPartition** in als weergave naam en selecteer **toevoegen**.

1. Open `GetPartition.cshtml` en wijzig de waarde zodat deze eruitziet als het volgende code fragment:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. Vouw in het **Solution Explorer**de **gedeelde map weer gaven->** uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **partitie ophalen** om de resultaten te bekijken die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Partitie ophalen](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Een entiteit verwijderen

In deze sectie wordt beschreven hoe u een entiteit uit een tabel verwijdert.

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt uitgevoerd om [de ontwikkel omgeving](#set-up-the-development-environment)in te stellen en gebruikt gegevens uit het [toevoegen van een batch met entiteiten aan een tabel](#add-a-batch-of-entities-to-a-table). 

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode toe met de naam **DeleteEntity** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. In de methode **DeleteEntity** haalt u een **Cloud Storage account** -object op dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van het opslag connection string en het opslag account op te halen uit de configuratie van de Azure-service: (Wijzig *&lt;storage-account naam >* in de naam van het Azure Storage-account dat u wilt openen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient** -object ophalen vertegenwoordigt een Table service-client.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Een **CloudTable** -object ophalen dat een verwijzing naar de tabel vertegenwoordigt waaruit u de entiteit verwijdert. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Maak een delete-bewerkings object dat een entiteits object gebruikt dat is afgeleid van **TableEntity**. In dit geval gebruiken we de **CustomerEntity** -klasse en de gegevens die in de sectie worden gepresenteerd, [een batch-entiteit toevoegen aan een tabel](#add-a-batch-of-entities-to-a-table). De **ETAG** van de entiteit moet worden ingesteld op een geldige waarde.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Voer de Verwijder bewerking uit.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Het resultaat wordt door gegeven aan de weer gave voor weer gave.

    ```csharp
    return View(result);
    ```

1. Vouw in de **Solution Explorer**de map **weer gaven** uit, klik met de rechter muisknop op **tabellen**en selecteer in het context menu de optie **toevoegen-> weer geven**.

1. Voer in het dialoog venster **weer gave toevoegen** **DeleteEntity** in als weergave naam en selecteer **toevoegen**.

1. Open `DeleteEntity.cshtml` en wijzig de waarde zodat deze eruitziet als het volgende code fragment:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. Vouw in het **Solution Explorer**de **gedeelde map weer gaven->** uit en open `_Layout.cshtml`.

1. Voeg na de laatste **HTML. ActionLink**de volgende **HTML. ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **entiteit verwijderen** om de resultaten te zien die vergelijkbaar zijn met de volgende scherm afbeelding:
  
    ![Eén ophalen](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Volgende stappen
Bekijk meer functiehandleidingen voor informatie over aanvullende mogelijkheden voor het opslaan van gegevens in Azure.

  * [Aan de slag met Azure Blob Storage en Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Aan de slag met Azure Queue Storage en Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)

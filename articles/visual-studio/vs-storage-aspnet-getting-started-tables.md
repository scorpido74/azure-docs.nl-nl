---
title: Aan de slag met Azure-tabelopslag met Visual Studio (ASP.NET)
description: Aan de slag met Azure-tabelopslag in een ASP.NET project in Visual Studio nadat u verbinding hebt gemaakt met een opslagaccount met Visual Studio Connected Services
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
ms.openlocfilehash: c49df689ae859c93046c19af043aa2001dbb5481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979634"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Aan de slag met Azure-tabelopslag en Visual Studio Connected Services (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Overzicht

Azure Table-opslag stelt u in staat om grote hoeveelheden gestructureerde gegevens op te slaan. De service is een NoSQL-gegevensarchief dat geverifieerde oproepen van binnen en buiten de Azure-cloud accepteert. Azure-tabellen zijn ideaal voor het opslaan van gestructureerde, niet-relationele gegevens.

In deze zelfstudie ziet u hoe u ASP.NET code schrijft voor een aantal veelvoorkomende scenario's met azure-opslagentiteiten. Deze scenario's omvatten het maken van een tabel en het toevoegen, query's en het verwijderen van tabelentiteiten. 

## <a name="prerequisites"></a>Vereisten

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure-opslagaccount](../storage/common/storage-account-create.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Een MVC-controller maken 

1. Klik in de **Oplossingsverkenner**met de rechtermuisknop op **Controllers**en selecteer in het contextmenu **>Controller toevoegen**.

    ![Een controller toevoegen aan een ASP.NET MVC-app](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Selecteer **mvc 5-controller toevoegen in**het dialoogvenster **Steiger toevoegen** - Leeg en selecteer **Toevoegen**.

    ![MVC-controllertype opgeven](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Geef in het dialoogvenster **Controller toevoegen** de naam van de controller *TablesController*en selecteer **Toevoegen**.

    ![De MVC-controller een naam geven](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Voeg de *using* volgende richtlijnen `TablesController.cs` toe aan het bestand:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Een modelklasse maken

Veel van de voorbeelden in dit artikel gebruiken een **door TableEntity**afgeleide klasse genaamd **CustomerEntity**. De volgende stappen begeleiden u bij het declareren van deze klasse als een modelklasse:

1. Klik in de **Oplossingsverkenner**met de rechtermuisknop op **Modellen**en selecteer in het contextmenu **>klasse toevoegen**.

1. Geef in het dialoogvenster **Nieuw item toevoegen** de naam van de **klasse, CustomerEntity**.

1. Open `CustomerEntity.cs` het bestand en voeg de volgende **gebruiksrichtlijn** toe:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Wijzig de klasse zodat de klasse, wanneer deze is voltooid, wordt aangegeven zoals in de volgende code. De klasse declareert een entiteitsklasse met de naam **CustomerEntity** die de voornaam van de klant gebruikt als de rijsleutel en achternaam als partitiesleutel.

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

In de volgende stappen wordt uitgelegd hoe u een tabel maakt:

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid in [Het instellen van de ontwikkelomgeving.](#set-up-the-development-environment) 

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode toe met de naam **CreateTable** die een **ActionResult**retourneert.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Download binnen de **methode CreateTable** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient-object** ophalen vertegenwoordigt een tabelserviceclient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Download een **CloudTable-object** dat een verwijzing naar de gewenste tabelnaam vertegenwoordigt. De methode **CloudTableClient.GetTableReference** doet geen aanvraag tegen tabelopslag. De verwijzing wordt geretourneerd, ongeacht of de tabel al dan niet bestaat. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Roep de methode **CloudTable.CreateIfNotExists** aan om de tabel te maken als deze nog niet bestaat. De methode **CloudTable.CreateIfNotExists retourneert** **true** als de tabel niet bestaat en is gemaakt. Anders wordt **false** geretourneerd.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Werk de **ViewBag** bij met de naam van de tabel.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Tabellen**en selecteer in het contextmenu **>Weergave toevoegen**.

1. Typ **CreateTable** voor de weergavenaam in het dialoogvenster **Weergave toevoegen** en selecteer **Toevoegen**.

1. Open `CreateTable.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **Tabel maken** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Tabel maken](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Zoals eerder vermeld, retourneert de methode **CloudTable.CreateIfNotExists** **alleen waar** wanneer de tabel niet bestaat en wordt gemaakt. Als u de app uitvoert wanneer de tabel bestaat, retourneert de methode **dus false**. Als u de app meerdere keren wilt uitvoeren, moet u de tabel verwijderen voordat u de app opnieuw uitvoert. Het verwijderen van de tabel kan via de methode **CloudTable.Delete.** U de tabel ook verwijderen met de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) of de [Microsoft Azure Storage Explorer.](../vs-azure-tools-storage-manage-with-storage-explorer.md)  

## <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

*Entiteiten worden* \# toegewezen aan C-objecten met behulp van een aangepaste klasse die is afgeleid van **TableEntity**. Als u een entiteit wilt toevoegen aan een tabel, maakt u een klasse die de eigenschappen van uw entiteit definieert. In deze sectie ziet u hoe u een entiteitsklasse definieert die de voornaam van de klant gebruikt als rijsleutel en achternaam als partitiesleutel. De partitie- en rijsleutel van een entiteit vormen samen de unieke id van de entiteit in de tabel. Entiteiten met dezelfde partitiesleutel kunnen sneller worden opgevraagd dan entiteiten met verschillende partitiesleutels, maar het gebruik van verschillende partitiesleutels maakt een grotere schaalbaarheid van parallelle bewerkingen mogelijk. Voor elke eigenschap die in de tabelservice moet worden opgeslagen, moet de eigenschap een openbare eigenschap zijn van een ondersteund type dat zowel instellings- als ophalende waarden blootlegt.
De entiteitsklasse *moet* een openbare parameterloze constructor declareren.

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid in [Het instellen van de ontwikkelomgeving.](#set-up-the-development-environment)

1. Open het `TablesController.cs`-bestand.

1. Voeg de volgende richtlijn toe `TablesController.cs` zodat de code in het bestand toegang heeft tot de klasse **CustomerEntity:**

    ```csharp
    using StorageAspnet.Models;
    ```

1. Voeg een methode met de naam **AddEntity** toe die een **ActionResult retourneert**.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Download binnen de methode **AddEntity** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient-object** ophalen vertegenwoordigt een tabelserviceclient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Download een **CloudTable-object** dat een verwijzing vertegenwoordigt naar de tabel waaraan u de nieuwe entiteit gaat toevoegen. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Maak de **customerentity-klasse** instantiate en initialiseren.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Maak een **object TableOperation** waarmee de entiteit van de klant wordt ingevoegd.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Voer de invoegbewerking uit door de methode **CloudTable.Execute aan te** roepen. U het resultaat van de bewerking verifiëren door de eigenschap **TabelResultaat.HttpStatusCode te** inspecteren. Een statuscode van 2xx geeft aan dat de door de client gevraagde actie met succes is verwerkt. Succesvolle invoegingen van nieuwe entiteiten resulteren bijvoorbeeld in een HTTP-statuscode van 204, wat betekent dat de bewerking is verwerkt en de server geen inhoud heeft retourneren.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Werk de **ViewBag** bij met de tabelnaam en de resultaten van de invoegbewerking.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Tabellen**en selecteer in het contextmenu **>Weergave toevoegen**.

1. Voer in het dialoogvenster **Weergave toevoegen** De optie **Entiteit toevoegen** in voor de weergavenaam en selecteer **Toevoegen**.

1. Open `AddEntity.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **Entiteit toevoegen** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Entiteit toevoegen](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    U controleren of de entiteit is toegevoegd door de stappen in de sectie Te volgen, [Eén entiteit opte halen.](#get-a-single-entity) U de [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) ook gebruiken om alle entiteiten voor uw tabellen weer te geven.

## <a name="add-a-batch-of-entities-to-a-table"></a>Een batch entiteiten toevoegen aan een tabel

Naast de mogelijkheid om [een entiteit één voor één aan een tabel toe te voegen,](#add-an-entity-to-a-table)u ook entiteiten toevoegen in batch. Als u entiteiten in batch toevoegt, wordt het aantal retourritten tussen uw code en de Azure-tabelservice verminderd. In de volgende stappen u zien hoe u meerdere entiteiten toevoegt aan een tabel met één invoegbewerking:

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid in [Het instellen van de ontwikkelomgeving.](#set-up-the-development-environment)

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode met de naam **Addentities** toe die een **ActionResult**retourneert .

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Download binnen de methode **Addentities** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient-object** ophalen vertegenwoordigt een tabelserviceclient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Download een **CloudTable-object** dat een verwijzing vertegenwoordigt naar de tabel waaraan u de nieuwe entiteiten gaat toevoegen. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instantiate sommige klantobjecten op basis van de **CustomerEntity-modelklasse** die wordt weergegeven in de sectie, [Een entiteit toevoegen aan een tabel](#add-an-entity-to-a-table).

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Een **object TableBatchOperation.**

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Voeg entiteiten toe aan het bewerkingsobject batchinvoegen.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Voer de batchinvoegbewerking uit door de methode **CloudTable.ExecuteBatch aan te** roepen.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. De methode **CloudTable.ExecuteBatch** retourneert een lijst met **TableResult-objecten** waarin elk **Object TableResult** kan worden onderzocht om het succes of de fout van elke afzonderlijke bewerking te bepalen. Geef de lijst in dit voorbeeld door aan een weergave en laat de weergave de resultaten van elke bewerking weergeven. 
 
    ```csharp
    return View(results);
    ```

1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Tabellen**en selecteer in het contextmenu **>Weergave toevoegen**.

1. Voer in het dialoogvenster **Weergave toevoegen** **adden voor** de weergavenaam in en selecteer **Toevoegen**.

1. Open `AddEntities.cshtml`en wijzig deze zodat het er als volgt uitziet.

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

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **Entiteiten toevoegen** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Entiteiten toevoegen](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    U controleren of de entiteit is toegevoegd door de stappen in de sectie Te volgen, [Eén entiteit opte halen.](#get-a-single-entity) U de [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) ook gebruiken om alle entiteiten voor uw tabellen weer te geven.

## <a name="get-a-single-entity"></a>Eén entiteit oppakken

In deze sectie ziet u hoe u één entiteit uit een tabel haalt met behulp van de rijsleutel en partitiesleutel van de entiteit. 

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid in [De ontwikkelomgeving instellen](#set-up-the-development-environment)en gebruikt u gegevens [uit Een batch entiteiten toevoegen aan een tabel](#add-a-batch-of-entities-to-a-table). 

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode met de naam **GetSingle** toe die een **ActionResult retourneert**.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Download binnen de **methode GetSingle** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient-object** ophalen vertegenwoordigt een tabelserviceclient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Download een **CloudTable-object** dat een verwijzing vertegenwoordigt naar de tabel waaruit u de entiteit ophaalt. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Maak een bewerkingsobject ophalen dat een entiteitsobject dat is afgeleid van **TableEntity**, heeft. De eerste parameter is de *partitionKey*en de tweede parameter is de *rowKey*. Met behulp van de klasse **CustomerEntity** en de gegevens die worden gepresenteerd in de sectie [Een batch entiteiten toevoegen aan een tabel,](#add-a-batch-of-entities-to-a-table)wordt in het volgende codefragment de tabel voor een **entiteit van een CustomerEntity** met een *partitionKey-waarde* van 'Smith' en een *rijsleutelwaarde* van 'Ben' opgevraagd:

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Voer de bewerking ophalen uit.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Geef het resultaat door aan de weergave voor weergave.

    ```csharp
    return View(result);
    ```

1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Tabellen**en selecteer in het contextmenu **>Weergave toevoegen**.

1. Typ In het dialoogvenster **Weergave toevoegen** **GetSingle** voor de weergavenaam en selecteer **Toevoegen**.

1. Open `GetSingle.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

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

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **Eén krijgen** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Ontvang single](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Alle entiteiten in een partitie oppakken

Zoals vermeld in de sectie, [Een entiteit toevoegen aan een tabel,](#add-an-entity-to-a-table)de combinatie van een partitie en een rijsleutel, wordt op unieke wijze een entiteit in een tabel geïdentificeerd. Entiteiten met dezelfde partitiesleutel kunnen sneller worden opgevraagd dan entiteiten met verschillende partitiesleutels. In deze sectie ziet u hoe u een tabel opvraagt voor alle entiteiten uit een opgegeven partitie.  

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid in [De ontwikkelomgeving instellen](#set-up-the-development-environment)en gebruikt u gegevens [uit Een batch entiteiten toevoegen aan een tabel](#add-a-batch-of-entities-to-a-table). 

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode met de naam **GetPartition** toe die een **ActionResult**retourneert.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Download binnen de **methode GetPartition** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient-object** ophalen vertegenwoordigt een tabelserviceclient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Download een **CloudTable-object** dat een verwijzing vertegenwoordigt naar de tabel waaruit u de entiteiten ophaalt. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Maak een **TableQuery-object** dat de query opgeeft in de component **Waar.** Met behulp van de klasse **CustomerEntity** en de gegevens die worden gepresenteerd in de sectie [Een batch entiteiten toevoegen aan een tabel,](#add-a-batch-of-entities-to-a-table)wordt in het volgende codefragment de tabel opgevraagd voor alle entiteiten waarbij de **partitionkey** (achternaam van de klant) een waarde van "Smith" heeft:

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Roep binnen een lus de **methode CloudTable.ExecuteQueryGesegmenteerde** aan waarbij het queryobject wordt doorgegeven dat u in de vorige stap hebt geinstantieerd.  De methode **CloudTable.ExecuteQueryGesegmenteerde retourneert** een object **TableContinuationToken** dat - wanneer **null** - aangeeft dat er geen entiteiten meer zijn om op te halen. Gebruik binnen de lus een andere lus om te herhalen over de geretourneerde entiteiten. In het volgende codevoorbeeld wordt elke geretourneerde entiteit aan een lijst toegevoegd. Zodra de lus is afgelopen, wordt de lijst doorgegeven aan een weergave: 

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

1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Tabellen**en selecteer in het contextmenu **>Weergave toevoegen**.

1. Typ In het dialoogvenster **Weergave toevoegen** **GetPartition** voor de weergavenaam en selecteer **Toevoegen**.

1. Open `GetPartition.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

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

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **Partitie opdoen** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Partitie opmaken](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Een entiteit verwijderen

In deze sectie wordt uitgelegd hoe u een entiteit uit een tabel verwijdert.

> [!NOTE]
> 
> In deze sectie wordt ervan uitgegaan dat u de stappen hebt voltooid in [De ontwikkelomgeving instellen](#set-up-the-development-environment)en gebruikt u gegevens [uit Een batch entiteiten toevoegen aan een tabel](#add-a-batch-of-entities-to-a-table). 

1. Open het `TablesController.cs`-bestand.

1. Voeg een methode met de naam **DeleteEntity** toe die een **ActionResult retourneert**.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Download binnen de methode **DeleteEntity** een **CloudStorageAccount-object** dat uw opslagaccountgegevens vertegenwoordigt. Gebruik de volgende code om de stoom- en opslagaccountgegevens van de Azure-serviceconfiguratie op te halen: (De * &lt;naam van het opslagaccount wijzigen>* de naam van het Azure-opslagaccount dat u gebruikt.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Een **CloudTableClient-object** ophalen vertegenwoordigt een tabelserviceclient.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Download een **CloudTable-object** dat een verwijzing vertegenwoordigt naar de tabel waaruit u de entiteit verwijderd. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Maak een object delete operation dat een entiteitsobject van **TableEntity**heeft . In dit geval gebruiken we de klasse en gegevens **van CustomerEntity** die worden gepresenteerd in de sectie [Een batch entiteiten toevoegen aan een tabel](#add-a-batch-of-entities-to-a-table). De **ETag** van de entiteit moet worden ingesteld op een geldige waarde.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Voer de verwijderbewerking uit.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Geef het resultaat door aan de weergave voor weergave.

    ```csharp
    return View(result);
    ```

1. Vouw in de **oplossingsverkenner**de map **Weergaven** uit, klik met de rechtermuisknop op **Tabellen**en selecteer in het contextmenu **>Weergave toevoegen**.

1. Typ **DeleteEntity** voor de weergavenaam en selecteer **Toevoegen**in het dialoogvenster **Weergave toevoegen** .

1. Open `DeleteEntity.cshtml`en wijzig het zodat het lijkt op het volgende codefragment:

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

1. Vouw in de **oplossingsverkenner**de map Weergaven `_Layout.cshtml`>**gedeelde** map uit en open .

1. Na de laatste **Html.ActionLink**voegt u de volgende **Html.ActionLink**toe:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Voer de toepassing uit en selecteer **Entiteit Verwijderen** om resultaten te zien die vergelijkbaar zijn met de volgende schermafbeelding:
  
    ![Ontvang single](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Volgende stappen
Bekijk meer functiehandleidingen voor informatie over aanvullende mogelijkheden voor het opslaan van gegevens in Azure.

  * [Aan de slag met Azure blob storage en Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Aan de slag met Azure queue storage en Visual Studio Connected Services (ASP.NET)](../storage/vs-storage-aspnet-getting-started-queues.md)

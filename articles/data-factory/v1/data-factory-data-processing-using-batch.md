---
title: Grootschalige gegevenssets verwerken met Data Factory en Batch
description: Beschrijft hoe u enorme hoeveelheden gegevens verwerken in een Azure Data Factory-pijplijn met behulp van de parallelle verwerkingsmogelijkheden van Azure Batch.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 7a6540b5784a76acfc248fb15feb1aaf39420845
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546935"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Grootschalige gegevenssets verwerken met Data Factory en Batch
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Azure Data Factory, die algemeen beschikbaar is. Zie [Aangepaste activiteiten in Gegevensfabriek](../transform-data-using-dotnet-custom-activity.md)als u de huidige versie van de service Data Factory gebruikt.

In dit artikel wordt een architectuur beschreven van een voorbeeldoplossing die grootschalige gegevenssets op een automatische en geplande manier verplaatst en verwerkt. Het biedt ook een end-to-end walkthrough om de oplossing te implementeren met behulp van Data Factory en Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dit artikel is langer dan een typisch artikel omdat het een walkthrough van een volledige voorbeeldoplossing bevat. Als u nieuw bent bij Batch en Data Factory, u meer te weten komen over deze services en hoe ze samenwerken. Als u iets weet over de diensten en een oplossing ontwerpt/ontwerpt, u zich richten op het architectuurgedeelte van het artikel. Als u een prototype of een oplossing ontwikkelt, u de stapsgewijze instructies in de walkthrough uitproberen. We nodigen uw opmerkingen over deze inhoud en hoe u deze gebruikt uit.

Laten we eerst eens kijken hoe datafabriek- en batchservices u kunnen helpen bij het verwerken van grote gegevenssets in de cloud.     


## <a name="why-azure-batch"></a>Waarom Azure Batch?
 U Batch gebruiken om hpc-toepassingen (op grote schaal en high-performance computing) efficiënt uit te voeren in de cloud. Het is een platformservice die rekenintensief werk plant om te draaien op een beheerde verzameling virtuele machines (VM's). Het kan compute resources automatisch schalen om aan de behoeften van uw taken te voldoen.

Met de Batch-service definieert u Azure-rekenresources om uw toepassingen parallel en op de juiste schaal uit te voeren. U on-demand of geplande taken uitvoeren. U hoeft geen HPC-cluster, afzonderlijke VM's, virtuele netwerken of een complexe taak- en taakplanningsinfrastructuur handmatig te maken, configureren en beheren.

 Als u niet bekend bent met Batch, helpen de volgende artikelen u inzicht te krijgen in de architectuur/implementatie van de oplossing die in dit artikel wordt beschreven:   

* [Basisprincipes van batch](../../batch/batch-technical-overview.md)
* [Overzicht van de functies van Batch](../../batch/batch-api-basics.md)

Zie de [batchdocumentatie](https://docs.microsoft.com/azure/batch/)voor meer informatie over Batch.

## <a name="why-azure-data-factory"></a>Waarom Azure Data Factory?
Een Data Factory is een cloudgebaseerde gegevensintegratieservice waarmee de verplaatsing en transformatie van gegevens wordt beheerd en geautomatiseerd. U Data Factory gebruiken om beheerde gegevenspijplijnen te maken die gegevens verplaatsen van on-premises en cloudgegevensopslag naar een gecentraliseerd gegevensarchief. Een voorbeeld is Azure Blob-opslag. U Data Factory gebruiken om gegevens te verwerken/transformeren met services zoals Azure HDInsight en Azure Machine Learning. U ook gegevenspijplijnen plannen die op een geplande manier worden uitgevoerd (bijvoorbeeld per uur, per dag en wekelijks). U de pijplijnen in één oogopslag controleren en beheren om problemen te identificeren en actie te ondernemen.

  Als u niet bekend bent met Data Factory, helpen de volgende artikelen u inzicht te krijgen in de architectuur/implementatie van de oplossing die in dit artikel wordt beschreven:  

* [Inleiding tot Data Factory](data-factory-introduction.md)
* [Uw eerste gegevenspijplijn maken](data-factory-build-your-first-pipeline.md)   

Voor meer informatie over Data Factory raadpleegt u de [documentatie van Data Factory.](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory)

## <a name="data-factory-and-batch-together"></a>Data Factory en Batch samen
Data Factory omvat ingebouwde activiteiten. De activiteit Kopiëren wordt bijvoorbeeld gebruikt om gegevens van een brongegevensarchief naar een doelgegevensarchief te kopiëren/verplaatsen. De Hive-activiteit wordt gebruikt om gegevens te verwerken met Hadoop-clusters (HDInsight) op Azure. Zie [Gegevenstransformatieactiviteiten](data-factory-data-transformation-activities.md)voor een lijst met ondersteunde transformatieactiviteiten.

U ook aangepaste .NET-activiteiten maken om gegevens te verplaatsen of te verwerken met uw eigen logica. U deze activiteiten uitvoeren op een HDInsight-cluster of op een batchgroep van VM's. Wanneer u Batch gebruikt, u de groep configureren om de koppeling automatisch te schalen (VM's toevoegen of verwijderen op basis van de werkbelasting) op basis van een formule die u opgeeft.     

## <a name="architecture-of-a-sample-solution"></a>Architectuur van een voorbeeldoplossing
  De architectuur beschreven in dit artikel is voor een eenvoudige oplossing. Het is ook relevant voor complexe scenario's, zoals risicomodellering door financiële diensten, beeldverwerking en -rendering en genomische analyse.

Het diagram illustreert hoe Data Factory gegevensverplaatsing en -verwerking orkestreert. Het laat ook zien hoe Batch de gegevens op een parallelle manier verwerkt. Download en print het diagram voor eenvoudige referentie (11 x 17 inch of A3-formaat). Zie [HPC en gegevensorkestratie met Batch en Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686)om toegang te krijgen tot het diagram zodat u het afdrukken.

[![Diagram voor grootschalige gegevensverwerking](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

De volgende lijst bevat de basisstappen van het proces. De oplossing bevat code en uitleg om de end-to-end oplossing te bouwen.

* **Batch configureren met een groep compute nodes (VM's).** U het aantal knooppunten en de grootte van elk knooppunt opgeven.

* **Maak een instantie Gegevensfabriek** die is geconfigureerd met entiteiten die blobopslag, de batchcomputeservice, invoer-/uitvoergegevens en een werkstroom/pijplijn vertegenwoordigen met activiteiten die gegevens verplaatsen en transformeren.

* **Maak een aangepaste .NET-activiteit in de pijplijn Gegevensfabriek.** De activiteit is uw gebruikerscode die wordt uitgevoerd op de batchgroep.

* **Sla grote hoeveelheden invoergegevens op als blobs in Azure Storage.** Gegevens worden verdeeld in logische segmenten (meestal door de tijd).

* **Data Factory kopieert gegevens die parallel aan** de secundaire locatie worden verwerkt.

* **Data Factory voert de aangepaste activiteit uit met behulp van de pool die door Batch is toegewezen.** Data Factory kan activiteiten gelijktijdig uitvoeren. Elke activiteit verwerkt een stukje gegevens. De resultaten worden opgeslagen in opslag.

* **Data Factory verplaatst de uiteindelijke resultaten naar een derde locatie,** hetzij voor distributie via een app of voor verdere verwerking door andere tools.

## <a name="implementation-of-the-sample-solution"></a>Implementatie van de monsteroplossing
De monsteroplossing is opzettelijk eenvoudig. Het is ontworpen om u te laten zien hoe u Data Factory en Batch samen gebruiken om gegevenssets te verwerken. De oplossing telt het aantal exemplaren van de zoekterm "Microsoft" in invoerbestanden die zijn georganiseerd in een tijdreeks. Vervolgens wordt het aantal naar uitvoerbestanden uitgevoerd.

**Tijd:** Als u bekend bent met de basisprincipes van Azure, Data Factory en Batch en de volgende vereisten hebt voltooid, duurt het een tot twee uur voordat deze oplossing is voltooid.

### <a name="prerequisites"></a>Vereisten
#### <a name="azure-subscription"></a>Azure-abonnement
Als u geen Azure-abonnement hebt, u snel een gratis proefaccount maken. Zie [Gratis proefversie](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie.

#### <a name="azure-storage-account"></a>Azure Storage-account
U gebruikt een opslagaccount om de gegevens op te slaan in deze zelfstudie. Zie [Een opslagaccount maken](../../storage/common/storage-account-create.md)als u geen opslagaccount hebt. De voorbeeldoplossing maakt gebruik van blob-opslag.

#### <a name="azure-batch-account"></a>Azure Batch-account
Maak een Batch-account met behulp van de [Azure-portal](https://portal.azure.com/). Zie [Een Batch-account maken en beheren](../../batch/batch-account-create-portal.md)voor meer informatie . Let op de naam van het Batch-account en de accountsleutel. U ook de cmdlet [Nieuw-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) gebruiken om een Batch-account aan te maken. Zie Aan de slag met Batch [PowerShell-cmdlets](../../batch/batch-powershell-cmdlets-get-started.md)voor instructies over het gebruik van deze cmdlet.

De voorbeeldoplossing gebruikt Batch (indirect via een pijplijn van gegevensfabrieken) om gegevens op een parallelle manier te verwerken op een pool van compute nodes (een beheerde verzameling VM's).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch-groep virtuele machines
Maak een batchgroep met ten minste twee compute nodes.

1. Selecteer in de [Azure-portal](https://portal.azure.com) **Bladeren** in het linkermenu en selecteer **Batchaccounts**.

1. Selecteer uw Batch-account om het **batchaccountblad** te openen.

1. Selecteer de tegel **Zwembaden.**

1. Selecteer op het blad **Pools** de knop **Toevoegen** op de werkbalk om een groep toe te voegen.

   a. Voer een id in voor de groep **(Pool-ID).** Let op de ID van het zwembad. U hebt het nodig wanneer u de oplossing voor gegevensfabrieken maakt.

   b. Geef **Windows Server 2012 R2** op voor de instelling **Besturingssysteemfamilie.**

   c. Selecteer een **nodeprijslaag**.

   d. Voer **2** in als waarde voor de **instelling Doelspecifiek.**

   e. Voer **2** in als de waarde voor de instelling **Max-taken per knooppunt.**

   f. Selecteer **OK** om de groep te maken.

#### <a name="azure-storage-explorer"></a>Azure Opslagverkenner
U gebruikt [Azure Storage Explorer 6](https://azurestorageexplorer.codeplex.com/) of [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (van ClumsyLeaf Software) om de gegevens in uw opslagprojecten te inspecteren en te wijzigen. U de gegevens ook inspecteren en wijzigen in de logboeken van uw cloudgehoste toepassingen.

1. Maak een container met de naam **mycontainer** met privétoegang (geen anonieme toegang).

1. Als u CloudXplorer gebruikt, maakt u mappen en submappen met de volgende structuur:

   ![Map- en submapstructuur](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`en `outputfolder` zijn mappen op `mycontainer`het hoogste niveau in . De `inputfolder` map heeft submappen met datum-tijd stempels (YYYY-MM-DD-HH).

   Als u Storage Explorer gebruikt, uploadt u in de `inputfolder/2015-11-16-00/file.txt` `inputfolder/2015-11-16-01/file.txt`volgende stap bestanden met de volgende namen: , enzovoort. Met deze stap worden de mappen automatisch gemaakt.

1. Maak een **tekstbestandfile.txt** op uw machine met inhoud met het zoekwoord **Microsoft**. Een voorbeeld is 'test aangepaste activiteit Microsoft test aangepaste activiteit Microsoft'.

1. Upload het bestand naar de volgende invoermappen in blobopslag:

   ![Invoermappen](./media/data-factory-data-processing-using-batch/image4.png)

   Als u Storage Explorer gebruikt, uploadt u het **bestand file.txt** naar **mycontainer.** Selecteer **Kopiëren** op de werkbalk om een kopie van de blob te maken. Wijzig in het dialoogvenster **Blob kopiëren** de naam van de **doelblob** in `inputfolder/2015-11-16-00/file.txt`. Herhaal deze stap `inputfolder/2015-11-16-01/file.txt` `inputfolder/2015-11-16-02/file.txt`om `inputfolder/2015-11-16-03/file.txt` `inputfolder/2015-11-16-04/file.txt`te maken , , , , en ga zo maar door. Met deze actie worden de mappen automatisch gemaakt.

1. Maak een `customactivitycontainer`andere container met de naam . Upload het aangepaste zip-bestand voor activiteiten naar deze container.

#### <a name="visual-studio"></a>Visual Studio
Installeer Visual Studio 2012 of hoger om de aangepaste batchactiviteit te maken die in de oplossing voor gegevensfabrieken moet worden gebruikt.

### <a name="high-level-steps-to-create-the-solution"></a>Stappen op hoog niveau om de oplossing te creëren
1. Maak een aangepaste activiteit die de logica voor gegevensverwerking bevat.

1. Maak een gegevensfabriek die de aangepaste activiteit gebruikt.

### <a name="create-the-custom-activity"></a>De aangepaste activiteit maken
De aangepaste activiteit van de gegevensfabriek is het hart van deze voorbeeldoplossing. De voorbeeldoplossing gebruikt Batch om de aangepaste activiteit uit te voeren. Zie Aangepaste activiteiten gebruiken in een pijplijn voor [gegevensfabrieken](data-factory-use-custom-activities.md)voor informatie over het ontwikkelen van aangepaste activiteiten en deze gebruiken in pijplijnen voor gegevensfabrieken.

Als u een aangepaste .NET-activiteit wilt maken die u gebruiken in een pijplijn voor gegevensfabrieken, maakt u een .NET-klassebibliotheekproject met een klasse die de IDotNetActiviteit-interface implementeert. Deze interface heeft slechts één methode: Uitvoeren. Hier is de handtekening van de methode:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

De methode heeft een paar belangrijke onderdelen die u moet begrijpen:

* De methode heeft vier parameters nodig:

  * **linkedServices**. Deze parameter is een enumerable lijst van gekoppelde services die invoer-/uitvoergegevensbronnen (bijvoorbeeld blobopslag) koppelen aan de gegevensfabriek. In dit voorbeeld is er slechts één gekoppelde service van het type Azure Storage dat wordt gebruikt voor zowel invoer als uitvoer.
  * **gegevenssets**. Deze parameter is een enumerable lijst van gegevenssets. U deze parameter gebruiken om de locaties en schema's te definiëren door invoer- en uitvoergegevenssets.
  * **activiteit**. Deze parameter vertegenwoordigt de huidige compute-entiteit. In dit geval is het een Batch-service.
  * **logger**. U de logger gebruiken om foutopsporingsopmerkingen te schrijven die als het Gebruikerslogboek voor de pijplijn naar boven komen.
* De methode retourneert een woordenboek dat kan worden gebruikt om aangepaste activiteiten in de toekomst aan elkaar te ketenen. Deze functie is nog niet geïmplementeerd, dus geef een leeg woordenboek terug uit de methode.

#### <a name="procedure-create-the-custom-activity"></a>Procedure: De aangepaste activiteit maken
1. Maak een .NET-klassenbibliotheekproject in Visual Studio.

   a. Start Visual Studio 2012/2013/2015.

   b. Selecteer Nieuw**New** > **project** **bestand** > .

   c. **Sjablonen**uitvouwen en **Visual C selecteren\#**. In deze walkthrough gebruikt\#u C, maar u elke .NET-taal gebruiken om de aangepaste activiteit te ontwikkelen.

   d. Selecteer **Klassebibliotheek** in de lijst met projecttypen aan de rechterkant.

   e. Voer **MyDotNetActiviteit** in voor de **naam**.

   f. Selecteer **C:\\ADF** voor de **locatie**. Maak de map **ADF** als deze niet bestaat.

   g. Selecteer **OK** om het project te maken.

1. Selecteer **Tools** > **NuGet Package Manager** > **Package Manager Console**.

1. Voer in de Package Manager Console de volgende opdracht uit om Microsoft.Azure.Management.DataFactories te importeren:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importeer het **NuGet-pakket azure storage** in het project. U hebt dit pakket nodig omdat u de Blob Storage API in dit voorbeeld gebruikt:

    ```powershell
    Install-Package Az.Storage
    ```
1. Voeg de volgende gebruiksrichtlijnen toe aan het bronbestand in het project:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Wijzig de naam van de naamruimte in **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Wijzig de naam van de klasse in **MyDotNetActivity**en ontlenen aan de **IDotNetActivity-interface** zoals weergegeven:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementeer (toevoegen) de **uitvoermethode** van de **IDotNetActiviteit-interface** aan de klasse **MyDotNetActiviteit.** Kopieer de volgende voorbeeldcode naar de methode. Zie de sectie [Methode uitvoeren](#execute-method) voor een uitleg van de logica die in deze methode wordt gebruikt.

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {

       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;

       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);

       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;

       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.

       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

       // Initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);

           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

       } while (continuationToken != null);

       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

       folderPath = GetFolderPath(outputDataset);

       logger.Write("Writing blob to the folder: {0}", folderPath);

       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);

       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Voeg de volgende helpermethoden toe aan de klasse. Deze methoden worden aangeroepen met de methode **Uitvoeren.** Het belangrijkste **isoleert** de methode Berekenen de code die door elke blob wordt heengeten.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    Met de methode GetFolderPath wordt het pad geretourneerd naar de map waarnaar de gegevensset verwijst en de methode GetFileName retourneert de naam van de blob/bestand waarnaar de gegevensset verwijst.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Met de methode Berekenen wordt het aantal exemplaren van het zoekwoord 'Microsoft' in de invoerbestanden (blobs in de map) berekend. De zoekterm "Microsoft" is hard gecodeerd in de code.

1. Stel het project samen. Selecteer **Bouwen** in het menu en selecteer **Oplossing bouwen**.

1. Start Windows Explorer en ga naar de map **\\foutopsporings-** of **\\opslaglocatieontgrendelingsmap.** De mapkeuze is afhankelijk van het type build.

1. Maak een zip-bestand **MyDotNetActivity.zip** dat alle binaire bestanden in de map ** \\Foutopsporing van\\** de opslaglocatie bevat. Misschien wilt u de MyDotNetActivity opnemen. **pdb-bestand,** zodat u aanvullende details krijgt, zoals het regelnummer in de broncode dat het probleem heeft veroorzaakt wanneer er een storing optreedt.

   ![De lijst met de map bin\Foutopsporingsmap](./media/data-factory-data-processing-using-batch/image5.png)

1. Upload **MyDotNetActivity.zip** als een blob `customactivitycontainer` naar de blobcontainer in de blob-opslag die de gekoppelde service StorageLinkedService in ADFTutorialDataFactory gebruikt. Maak de `customactivitycontainer` blobcontainer als deze nog niet bestaat.

#### <a name="execute-method"></a>Uitvoermethode
In deze sectie vindt u meer informatie over de code in de methode Uitvoeren.

1. De leden voor het herhalen via de invoerverzameling zijn te vinden in de naamruimte [Microsoft.WindowsAzure.Storage.Blob.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) Als u de blobverzameling wilt doorlopen, moet u de klasse **BlobContinuationToken** gebruiken. In wezen moet u een do-while-lus gebruiken met het token als het mechanisme voor het verlaten van de lus. Zie [Blob-opslag gebruiken van .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)voor meer informatie. Een basislus wordt hier getoond:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,

                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.

     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

    } while (continuationToken != null);

    ```
   Zie de documentatie voor de [methode ListBlobsSegmented](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented) voor meer informatie.

1. De code voor het werken door de set blobs gaat logischerwijs binnen de do-while-lus. In de methode **Uitvoeren** geeft de do-while-lus de lijst met blobs door aan een methode met de naam **Calculate**. De methode retourneert een tekenreeksvariabele met de naam **uitvoer** die het resultaat is van het feit dat alle blobs in het segment zijn herhaald.

   Hiermee wordt het aantal exemplaren van de zoekterm 'Microsoft' in de blob doorgegeven aan de methode **Berekenen.**

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Nadat de methode **Berekenen** is voltooid, moet deze naar een nieuwe blob worden geschreven. Voor elke set blobs die worden verwerkt, kan een nieuwe blob worden geschreven met de resultaten. Als u naar een nieuwe blob wilt schrijven, zoekt u eerst de uitvoergegevensset.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. De code roept ook de helpermethode **GetFolderPath** aan om het mappad (de naam van de opslagcontainer) op te halen.

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Met de methode GetFolderPath wordt het object DataSet gecast naar een AzureBlobDataSet, met de eigenschap FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. De code roept de methode **GetFileName** aan om de bestandsnaam (blobnaam) op te halen. De code is vergelijkbaar met de vorige code die is gebruikt om het mappad te krijgen.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. De naam van het bestand wordt geschreven door een URI-object te maken. De URI-constructor gebruikt de eigenschap **BlobEndpoint** om de containernaam terug te sturen. Het mappad en de bestandsnaam worden toegevoegd om de uitvoerblob URI te construeren.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Nadat de naam van het bestand is geschreven, u de uitvoertekenreeks van de methode **Berekenen** naar een nieuwe blob schrijven:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>De gegevensfabriek maken
In [de](#create-the-custom-activity) sectie De aangepaste activiteit maken hebt u een aangepaste activiteit gemaakt en het zip-bestand met binaire bestanden en het PDB-bestand geüpload naar een blobcontainer. In deze sectie maakt u een gegevensfabriek met een pijplijn die de aangepaste activiteit gebruikt.

De invoergegevensset voor de aangepaste activiteit vertegenwoordigt de blobs (bestanden) in de invoermap (`mycontainer\\inputfolder`) in blobopslag. De uitvoergegevensset voor de activiteit vertegenwoordigt de`mycontainer\\outputfolder`uitvoerblobs in de uitvoermap ( ) in blobopslag.

Een of meer bestanden in de invoermappen neerzetten:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Laat bijvoorbeeld één bestand (file.txt) met de volgende inhoud in elk van de mappen vallen:

```
test custom activity Microsoft test custom activity Microsoft
```

Elke invoermap komt overeen met een segment in de gegevensfabriek, zelfs als de map twee of meer bestanden bevat. Wanneer elk segment door de pijplijn wordt verwerkt, wordt de aangepaste activiteit door alle blobs in de invoermap voor dat segment gehesen.

U ziet vijf uitvoerbestanden met dezelfde inhoud. Het uitvoerbestand van de verwerking van het bestand in de map 2015-11-16-00 heeft bijvoorbeeld de volgende inhoud:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Als u meerdere bestanden (file.txt, file2.txt, file3.txt) met dezelfde inhoud in de invoermap neerzet, ziet u de volgende inhoud in het uitvoerbestand. Elke map (2015-11-16-00, enz.) komt overeen met een segment in dit voorbeeld, ook al heeft de map meerdere invoerbestanden.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Het uitvoerbestand heeft nu drie regels, één voor elk invoerbestand (blob) in de map die is gekoppeld aan het segment (2015-11-16-00).

Voor elke activiteitsrun wordt een taak gemaakt. In dit voorbeeld is er slechts één activiteit in de pijplijn. Wanneer een segment door de pijplijn wordt verwerkt, wordt de aangepaste activiteit uitgevoerd op Batch om het segment te verwerken. Omdat er vijf segmenten zijn (elk segment kan meerdere blobs of bestand hebben), worden vijf taken gemaakt in Batch. Wanneer een taak wordt uitgevoerd op Batch, is dit de aangepaste activiteit die wordt uitgevoerd.

De volgende walkthrough geeft extra details.

#### <a name="step-1-create-the-data-factory"></a>Stap 1: De gegevensfabriek maken
1. Nadat u zich hebt aangemeld bij de [Azure-portal,](https://portal.azure.com/)neemt u de volgende stappen:

   a. Selecteer **NIEUW** in het linkermenu.

   b. Selecteer **Data + Analytics** op het **nieuwe** blad.

   c. Selecteer **Data Factory** op het **data-analyseblad.**

1. Voer op het blad **Nieuwe gegevensfabriek** **CustomActivityFactory** in voor de naam. De naam van de data factory moet een Globally Unique Identifier zijn. Als u de fout 'Gegevensfabrieksnaam CustomActivityFactory is niet beschikbaar' ontvangt, wijzigt u de naam van de gegevensfabriek. Gebruik bijvoorbeeld yournameCustomActivityFactory en maak de gegevensfabriek opnieuw.

1. Selecteer **NAAM VAN RESOURCEGROEP**en selecteer een bestaande resourcegroep of maak een resourcegroep.

1. Controleer of het abonnement en de regio waar u de gegevensfabriek wilt maken, correct zijn.

1. Selecteer **Maken** op het **fabrieksblad Nieuwe gegevens.**

1. De gegevensfabriek wordt gemaakt in het dashboard van de portal.

1. Nadat de gegevensfabriek is gemaakt, ziet u de **pagina Gegevensfabriek,** die u de inhoud van de gegevensfabriek weergeeft.

   ![Pagina Gegevensfabriek](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Stap 2: Gekoppelde services maken
Gekoppelde services koppelen gegevensopslag of compute services aan een gegevensfabriek. In deze stap koppelt u uw opslagaccount en Batch-account aan uw gegevensfabriek.

#### <a name="create-an-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken
1. Selecteer de tegel **Auteur en implementeer** de tegel op het **fabrieksblad Gegevens** voor **CustomActivityFactory**. De Data Factory Editor wordt weergegeven.

1. Selecteer **Nieuw gegevensarchief** op de opdrachtbalk en kies **Azure-opslag.** Het JSON-script dat u gebruikt om een door Storage gekoppelde service in de editor te maken, wordt weergegeven.

   ![Nieuw gegevensarchief](./media/data-factory-data-processing-using-batch/image7.png)

1. Vervang de **accountnaam** door de naam van uw opslagaccount. Vervang de **accountsleutel** met de toegangssleutel van uw opslagaccount. Zie [Toegangssleutels voor opslagaccount beheren](../../storage/common/storage-account-keys-manage.md)voor meer informatie over het ophalen van uw opslagtoegangssleutel.

1. Selecteer in de opdrachtbalk **Implementeren** om de gekoppelde service te implementeren.

   ![Implementeren](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Een gekoppelde Azure Batch-service maken
In deze stap maakt u een gekoppelde service voor uw Batch-account die wordt gebruikt om de aangepaste activiteit in de gegevensfabriek uit te voeren.

1. Selecteer **Nieuwe gegevensberekening** op de opdrachtbalk en kies **Azure Batch.** Het JSON-script dat u gebruikt om een batchservice in de editor te maken, wordt weergegeven.

1. In het JSON-script:

   a. Vervang **de accountnaam** door de naam van uw Batch-account.

   b. Vervang **de toegangssleutel** door de toegangssleutel van het Batch-account.

   c. Voer de id van de groep in voor de eigenschap **poolName.** Voor deze eigenschap u de naam van het zwembad of de pool-id opgeven.

   d. Voer de batch URI in voor de eigenschap **batchUri** JSON.

      > [!IMPORTANT]
      > De URL van het **batchaccountblad** bevindt zich in de volgende indeling: \<accountnaam\>. \<regio\>.batch.azure.com. Voor de eigenschap **batchUri** in het JSON-script moet u de a88-accountnaam verwijderen. ** van de URL. Een voorbeeld is `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Batch Account blade](./media/data-factory-data-processing-using-batch/image9.png)

      Voor de eigenschap **poolName** u ook de id van de groep opgeven in plaats van de naam van de groep.

      > [!NOTE]
      > De Data Factory-service ondersteunt geen on-demand optie voor Batch zoals voor HDInsight. U alleen uw eigen Batch-pool gebruiken in een gegevensfabriek.
      >
      >

   e. **StorageLinkedService** opgeven voor de eigenschap **linkedServiceName.** U hebt deze gekoppelde service in de vorige stap gemaakt. Deze opslag wordt gebruikt als een faseringsgebied voor bestanden en logboeken.

1. Selecteer in de opdrachtbalk **Implementeren** om de gekoppelde service te implementeren.

#### <a name="step-3-create-datasets"></a>Stap 3: Datasets maken
In deze stap maakt u gegevenssets om invoer- en uitvoergegevens weer te geven.

#### <a name="create-the-input-dataset"></a>De invoergegevensset maken
1. Selecteer in de datafabriekeditor de knop **Nieuwe gegevensset** op de werkbalk. Selecteer **Azure Blob-opslag** in de vervolgkeuzelijst.

1. Vervang het JSON-script in het rechterdeelvenster door het volgende JSON-fragment:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    U maakt later in deze walkthrough een pijplijn met de begintijd 2015-11-16T00:00:00Z en de eindtijd 2015-11-16T05:00:00Z. Het is gepland om elk uur gegevens te produceren, dus er zijn vijf invoer-\> en uitvoersegmenten (tussen 00:00:00 - **00** **05:00:00).**

    De **frequentie** en **het interval** voor de invoergegevensset zijn ingesteld op **Uur** en **1,** wat betekent dat het invoersegment elk uur beschikbaar is.

    De begintijd voor elk segment wordt weergegeven door de **segmentvariabele SliceStart-systeem** in het vorige JSON-fragment. Hier zijn de begintijden voor elk segment.

    | **Segment** | **Begintijd**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**00:00:00 |
    | 2         | 2015-11-16T**01**01:00:00 |
    | 3         | 2015-11-16T**02**02:00:00 |
    | 4         | 2015-11-16T**03**03:00:00 |
    | 5         | 2015-11-16T**04**04:00:00 |

    De **mapPath** wordt berekend met behulp van het deel jaar, maand, dag en uur van de begintijd van het segment **(SliceStart).** Hier is hoe een invoermap wordt toegewezen aan een segment.

    | **Segment** | **Begintijd**          | **Invoermap**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**00:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**01:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**02:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**03:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**04:00:00 | 2015-11-16-**04** |

1. Selecteer **Implementeren** op de werkbalk om de **tabel InputDataset** te maken en te implementeren.

#### <a name="create-the-output-dataset"></a>De uitvoergegevensset maken
In deze stap maakt u een andere gegevensset van het type AzureBlob om de uitvoergegevens weer te geven.

1. Selecteer in de datafabriekeditor de knop **Nieuwe gegevensset** op de werkbalk. Selecteer **Azure Blob-opslag** in de vervolgkeuzelijst.

1. Vervang het JSON-script in het rechterdeelvenster door het volgende JSON-fragment:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    Voor elk invoersegment wordt een uitvoerblob/-bestand gegenereerd. Hier is hoe een uitvoerbestand wordt genoemd voor elk segment. Alle uitvoerbestanden worden gegenereerd in `mycontainer\\outputfolder`één uitvoermap.

    | **Segment** | **Begintijd**          | **Uitvoerbestand**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**00:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**01:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**02:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**03:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**04:00:00 | 2015-11-16-**04.txt** |

    Vergeet niet dat alle bestanden in een invoermap (bijvoorbeeld 2015-11-16-00) deel uitmaken van een segment met de begintijd 2015-11-16-00. Wanneer dit segment wordt verwerkt, scant de aangepaste activiteit elk bestand en produceert een regel in het uitvoerbestand met het aantal exemplaren van de zoekterm 'Microsoft'. Als er drie bestanden in de map 2015-11-16-00 staan, zijn er drie regels in het uitvoerbestand 2015-11-16-00.txt.

1. Selecteer **Implementeren** op de werkbalk om de **uitvoersetset te**maken en te implementeren .

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Stap 4: De pijplijn maken en uitvoeren met een aangepaste activiteit
In deze stap maakt u een pijplijn met één activiteit, de aangepaste activiteit die u eerder hebt gemaakt.

> [!IMPORTANT]
> Als u **file.txt** niet hebt geüpload naar invoermappen in de blobcontainer, doet u dit voordat u de pijplijn maakt. De eigenschap **isOnderbrokened** ingesteld op false in de PIPELINE JSON, zodat de pijplijn onmiddellijk wordt uitgevoerd omdat de **begindatum** in het verleden is.
>
>

1. Selecteer in de datafabriekeditor de optie **Nieuwe pijplijn** op de opdrachtbalk. Als u de opdracht niet ziet, selecteert u het symbool ellips is om deze weer te geven.

1. Vervang het JSON-script in het rechterdeelvenster door het volgende JSON-fragment:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Houd rekening met de volgende punten:

   * Er is slechts één activiteit in de pijplijn en het is van het type **DotNetActivity**.
   * **AssemblyName** is ingesteld op de naam van de DLL **MyDotNetActivity.dll**.
   * **EntryPoint** is ingesteld op **MyDotNetActivityNS.MyDotNetActivity**. Het is eigenlijk \<namespace.\> \<klassennaam\> in uw code.
   * **PackageLinkedService** is ingesteld op **StorageLinkedService**, wat verwijst naar de blob-opslag die het aangepaste activiteitszip-bestand bevat. Als u verschillende opslagaccounts gebruikt voor invoer-/uitvoerbestanden en het aangepaste zip-bestand voor activiteit, moet u een andere opslagservice maken. In dit artikel wordt ervan uitgegaan dat u hetzelfde opslagaccount gebruikt.
   * **PackageFile** is ingesteld op **customactivitycontainer/MyDotNetActivity.zip**. Het is in \<het formaat\>/\<containerforthezip naamofthezip.zip\>.
   * De aangepaste activiteit neemt **InputDataset** als invoer- en **Uitvoersetset** als uitvoer.
   * De eigenschap **linkedServiceName** van de aangepaste activiteit verwijst naar **AzureBatchLinkedService**, waarmee Gegevensfabriek wordt verteld dat de aangepaste activiteit moet worden uitgevoerd op Batch.
   * De **gelijktijdigheid** instelling is belangrijk. Als u de standaardwaarde gebruikt, die 1 is, zelfs als u twee of meer rekenknooppunten in de groep Batch hebt, worden de segmenten na elkaar verwerkt. Daarom maakt u geen gebruik van de parallelle verwerkingscapaciteit van Batch. Als u **gelijktijdigheid** instelt op een hogere waarde, bijvoorbeeld 2, betekent dit dat twee segmenten (komt overeen met twee taken in Batch) tegelijkertijd kunnen worden verwerkt. In dit geval worden zowel de VM's in de batchpool gebruikt. Stel de eigenschap gelijktijdigheid op de juiste manier in.
   * Er wordt standaard slechts één taak (segment) uitgevoerd op een virtuele machine. Standaard is **Maximumtaken per VM** ingesteld op 1 voor een batchgroep. Als onderdeel van de vereisten hebt u een zwembad gemaakt met deze eigenschap ingesteld op 2. Daarom kunnen twee schijfsegmenten in de gegevensfabriek tegelijkertijd op een vm worden uitgevoerd.
     - De eigenschap **isPaused** standaard ingesteld op false. De pijplijn wordt onmiddellijk in dit voorbeeld uitgevoerd omdat de segmenten in het verleden zijn gestart. U deze eigenschap op **true** instellen om de pijplijn te pauzeren en deze weer op **false** in te stellen om opnieuw op te starten.
     -   De **begin-** en **eindtijden** liggen vijf uur uit elkaar. Plakjes worden elk uur geproduceerd, dus vijf plakjes worden geproduceerd door de pijpleiding.

1. Selecteer in de opdrachtbalk **Implementeren** om de pijplijn te implementeren.

#### <a name="step-5-test-the-pipeline"></a>Stap 5: De pijplijn testen
In deze stap test u de pijplijn door bestanden in de invoermappen te laten vallen. Begin met het testen van de pijplijn met één bestand voor elke invoermap.

1. Selecteer **Diagram**in het **fabrieksblad Gegevens** in de Azure-portal .

   ![Diagram](./media/data-factory-data-processing-using-batch/image10.png)

1. Dubbelklik in de **diagramweergave** op de **invoergegevensset Invoergegevensset**.

   ![Invoergegevensset](./media/data-factory-data-processing-using-batch/image11.png)

1. Het **blade InputDataset** wordt weergegeven met alle vijf segmenten klaar. Let op de **begintijd van het segment** **en de eindtijd van het segment voor** elk segment.

   ![Begin- en eindtijden van invoersegment](./media/data-factory-data-processing-using-batch/image12.png)

1. Selecteer **Diagram** **uitvoersetom .**

1. De vijf uitvoersegmenten worden in de status **Klaar** weergegeven als ze zijn geproduceerd.

   ![Begin- en eindtijden uitvoersegment](./media/data-factory-data-processing-using-batch/image13.png)

1. Gebruik de portal om de taken te bekijken die zijn gekoppeld aan de segmenten en te zien op welke VM elk segment is uitgevoerd. Zie de sectie [Data Factory en Batch-integratie](#data-factory-and-batch-integration) voor meer informatie.

1. De uitvoerbestanden `mycontainer` worden `outputfolder` weergegeven onder in de blobopslag.

   ![Uitvoerbestanden in opslag](./media/data-factory-data-processing-using-batch/image15.png)

   Er worden vijf uitvoerbestanden weergegeven, één voor elk invoersegment. Elk van de uitvoerbestanden heeft inhoud die vergelijkbaar is met de volgende uitvoer:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   In het volgende diagram ziet u hoe de gegevensfabriek toewijzing en taken in Batch doorsnijdt. In dit voorbeeld heeft een segment slechts één run.

   ![Segmenttoewijzingsdiagram](./media/data-factory-data-processing-using-batch/image16.png)

1. Probeer nu met meerdere bestanden in een map. Maak de bestanden **file2.txt**, **file3.txt**, **file4.txt**en **file5.txt** met dezelfde inhoud als in file.txt in de map **2015-11-06-01**.

1. Verwijder in de uitvoermap het uitvoerbestand **2015-11-16-01.txt**.

1. Klik op het blade **OutputDataset** met de rechtermuisknop op het segment met **slice starttijd** ingesteld op **11/16/2015 01:00:00 .** Selecteer **Uitvoeren** om het segment opnieuw uit te voeren/opnieuw te verwerken. Het segment heeft nu vijf bestanden in plaats van één bestand.

    ![Voer](./media/data-factory-data-processing-using-batch/image17.png)

1. Nadat het segment is uitgevoerd en de status **klaar**is, controleert u de inhoud in het uitvoerbestand voor dit segment **(2015-11-16-01.txt).** Het uitvoerbestand `mycontainer` wordt `outputfolder` onder in de blobopslag weergegeven. Er moet een regel zijn voor elk bestand van het segment.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Als u het uitvoerbestand 2015-11-16-01.txt niet hebt verwijderd voordat u het met vijf invoerbestanden hebt geprobeerd, ziet u één regel uit de vorige segmentrun en vijf regels uit het huidige segment. Standaard wordt de inhoud toegevoegd aan het uitvoerbestand als deze al bestaat.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integratie van gegevensfabrieken en batch
Met de service Gegevensfabriek wordt een `adf-poolname:job-xxx`taak gemaakt in Batch met de naam .

![Batchtaken](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Er wordt een taak in de taak gemaakt voor elke activiteitsrun van een segment. Als er 10 segmenten klaar zijn om te worden verwerkt, worden er 10 taken gemaakt in de taak. U meer dan één segment parallel laten lopen als u meerdere rekenknooppunten in de groep hebt. Als het maximum aantal taken per compute-knooppunt is ingesteld op meer dan één, kunnen meer dan één segment op dezelfde gegevensberekening worden uitgevoerd.

In dit voorbeeld zijn er vijf segmenten, dus er zijn vijf taken in Batch. Met **gelijktijdigheid** ingesteld op **5** in de pijplijn JSON in de gegevensfabriek en **Maximale taken per VM** ingesteld op **2** in de batchgroep met **2** VM's, worden de taken snel uitgevoerd. (Controleer de begin- en eindtijden voor taken.)

Gebruik de portal om de batchtaak en de taken die zijn gekoppeld aan de segmenten weer te geven en te zien op welke VM elk segment is uitgevoerd.

![Batchtaken](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Fouten opsporen in de pijplijn
Debugging bestaat uit een paar basistechnieken.

1. Als het invoersegment niet is ingesteld op **Gereed,** bevestigt u dat de structuur van de invoermap correct is en dat bestand.txt bestaat in de invoermappen.

   ![Structuur van invoermap](./media/data-factory-data-processing-using-batch/image3.png)

1. Gebruik in de **methode Uitvoeren** van uw aangepaste activiteit het object **IActivityLogger** om gegevens te registreren waarmee u problemen oplossen. De geregistreerde berichten worden weergegeven\_in het bestand van gebruiker 0.log.

   Selecteer op het blade **OutputDataset** het segment om het **sliceblad gegevens** voor dat segment te zien. Onder **Activiteitsuitvoeringen**ziet u één activiteit srun voor het segment. Als u **Uitvoeren** selecteert in de opdrachtbalk, u een andere activiteitsrun voor hetzelfde segment starten.

   Wanneer u de activiteitsrun selecteert, ziet u het **blad Activiteitsrun met** een lijst met logboekbestanden. U ziet geregistreerde berichten\_in het bestand van gebruiker 0.log. Wanneer er een fout optreedt, ziet u drie activiteitsruns omdat het aantal nieuwe gegevens is ingesteld op 3 in de JSON voor pijplijn/activiteit. Wanneer u de activiteitsrun selecteert, ziet u de logboekbestanden die u controleren om de fout op te lossen.

   ![Uitvoerdataset en datasliceblades](./media/data-factory-data-processing-using-batch/image18.png)

   Selecteer in de lijst met logboekbestanden de optie **gebruiker-0.log**. In het rechterpaneel worden de resultaten van het gebruik van de **iActivityLogger.Write-methode** weergegeven.

   ![Activiteit uitvoeren details blade](./media/data-factory-data-processing-using-batch/image19.png)

   Controleer het systeem-0.log op eventuele systeemfoutmeldingen en uitzonderingen.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Neem het **PDB-bestand** op in het zip-bestand, zodat de foutgegevens informatie bevatten, zoals de oproepstack wanneer er een fout optreedt.

1. Alle bestanden in het zip-bestand voor de aangepaste activiteit moeten op het hoogste niveau zijn zonder submappen.

   ![Aangepaste lijst met zip-bestanden voor activiteit](./media/data-factory-data-processing-using-batch/image20.png)

1. Zorg ervoor dat **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) en **packageLinkedService** (moet wijzen op de blob-opslag die het zip-bestand bevat) zijn ingesteld op de juiste waarden.

1. Als u een fout hebt opgelost en het segment opnieuw wilt verwerken, klikt u met de rechtermuisknop op het segment in het blade **OutputDataset** en selecteert u **Uitvoeren**.

   ![UitvoerDataset-blad run, optie](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Er bevindt zich een `adfjobs`container in de blobopslag met de naam . Deze container wordt niet automatisch verwijderd, maar u deze wel verwijderen nadat u de oplossing hebt getest. Op dezelfde manier wordt in de `adf-\<pool ID/name\>:job-0000000001`oplossing voor gegevensfabrieken een batchtaak met de naam . U deze taak verwijderen nadat u de oplossing hebt getest als u dat wilt.
   >
   >
1. De aangepaste activiteit maakt geen gebruik van het **app.config-bestand** uit uw pakket. Als uw code daarom verbindingstekenreeksen uit het configuratiebestand leest, werkt deze niet tijdens runtime. De beste praktijk wanneer u Batch gebruikt, is om geheimen in Azure Key Vault vast te houden. Gebruik vervolgens een serviceprincipal op basis van certificaten om de sleutelkluis te beschermen en het certificaat te distribueren naar de batchgroep. De aangepaste .NET-activiteit heeft toegang tot geheimen uit de sleutelkluis tijdens runtime. Deze generieke oplossing kan worden geschaald naar elk type geheim, niet alleen een verbindingstekenreeks.

    Er is een gemakkelijkere oplossing, maar het is geen beste praktijk. U een SQL-databasegekoppelde service maken met verbindingstekenreeksinstellingen. Vervolgens u een gegevensset maken die de gekoppelde service gebruikt en de gegevensset als een gegevensset voor dummy-invoer ketenen aan de aangepaste .NET-activiteit. U vervolgens de verbindingstekenreeks van de gekoppelde service openen in de aangepaste activiteitscode. Het zou prima moeten werken tijdens de runtime.  

#### <a name="extend-the-sample"></a>Het monster uitbreiden
U dit voorbeeld uitbreiden voor meer informatie over de functies Gegevensfabriek en Batch. Als u bijvoorbeeld segmenten in een ander tijdsbereik wilt verwerken, neemt u de volgende stappen:

1. Voeg de volgende submappen toe in `inputfolder`: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 en 2015-11-16-09. Plaats invoerbestanden in die mappen. Wijzig de eindtijd voor `2015-11-16T05:00:00Z` `2015-11-16T10:00:00Z`de pijplijn van . Dubbelklik in de **diagramweergave** op **InputDataset** en controleer of de invoersegmenten gereed zijn. Dubbelklik op **Uitvoersetom** de status van de uitvoersegmenten te bekijken. Als ze de status **Gereed** zijn, controleert u de uitvoermap voor de uitvoerbestanden.

1. Verhoog of verlaag de **gelijktijdigheidsinstelling** om te begrijpen hoe deze de prestaties van uw oplossing beïnvloedt, met name de verwerking die plaatsvindt op Batch. Zie 'Stap 4: De pijplijn maken en uitvoeren met een aangepaste activiteit voor meer informatie over de **gelijktijdigheidsinstelling'.**

1. Maak een groep met hogere/lagere **maximumtaken per VM.** Als u de nieuwe groep wilt gebruiken die u hebt gemaakt, werkt u de gekoppelde batchservice in de oplossing voor gegevensfabrieken bij. Zie 'Stap 4: De pijplijn maken en uitvoeren met een aangepaste activiteit voor meer informatie over de instelling **Maximumtaken per VM'.**

1. Maak een batchgroep met de **functie automatisch schalen.** Het automatisch schalen van compute nodes in een batchgroep is de dynamische aanpassing van de verwerkingskracht die door uw toepassing wordt gebruikt.

    De voorbeeldformule bereikt hier het volgende gedrag. Wanneer de groep in eerste instantie wordt gemaakt, begint deze met één VM. De $PendingTasks statistiek definieert het aantal taken in de actieve en actieve (in de wachtrij) status. De formule vindt het gemiddelde aantal lopende taken in de laatste 180 seconden en stelt TargetDedicated dienovereenkomstig in. Het zorgt ervoor dat TargetDedicated nooit verder gaat dan 25 VM's. Als nieuwe taken worden ingediend, wordt de groep automatisch groter. Naarmate taken worden voltooid, worden VM's één voor één gratis en krimpt de automatische schaling die VM's. U de startNumberOfVM's en maxNumberofVM's aanpassen aan uw behoeften.

    Formule automatisch schalen:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Zie [Compute-knooppunten automatisch schalen in een batchgroep](../../batch/batch-automatic-scaling.md)voor meer informatie.

   Als de groep het standaard [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)gebruikt, kan het 15 tot 30 minuten duren voordat de batchservice de VM voorbereidt voordat de aangepaste activiteit wordt uitgevoerd. Als de groep een ander autoScaleEvaluationInterval gebruikt, kan de batchservice autoScaleEvaluationInterval plus 10 minuten in beslag nemen.

1. In de voorbeeldoplossing roept de methode **Uitvoeren** de methode **Berekenen** op die een invoergegevenssegment verwerkt om een uitvoergegevenssegment te produceren. U uw eigen methode schrijven om invoergegevens te verwerken en de aanroep **met de methode Berekenen** in de methode **Uitvoeren** te vervangen door een aanroep naar uw methode.

### <a name="next-steps-consume-the-data"></a>Volgende stappen: De gegevens consumeren
Nadat u gegevens hebt verwerkt, u deze gebruiken met online tools zoals Power BI. Hier volgen koppelingen om u te helpen inzicht te krijgen in Power BI en hoe u deze in Azure gebruiken:

* [Een gegevensset verkennen in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Aan de slag met Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Gegevens vernieuwen in Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure en Power BI: basisoverzicht](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Verwijzingen
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Inleiding tot de Data Factory-service](data-factory-introduction.md)
  * [Aan de slag met Data Factory](data-factory-build-your-first-pipeline.md)
  * [Aangepaste activiteiten gebruiken in een Data Factory-pijplijn](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Basisprincipes van batch](../../batch/batch-technical-overview.md)
  * [Overzicht van batchfuncties](../../batch/batch-api-basics.md)
  * [Een Batch-account maken en beheren in de Azure-portal](../../batch/batch-account-create-portal.md)
  * [Aan de slag met de batchclientbibliotheek voor .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx

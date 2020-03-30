---
title: Use custom activities in an Azure Data Factory pipeline (Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn)
description: Meer informatie over het maken van aangepaste activiteiten en het gebruik ervan in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 54cb06f1c77ab68818d8531b57d6eb936deda8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265725"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Use custom activities in an Azure Data Factory pipeline (Aangepaste activiteiten gebruiken in een Azure Data Factory-pijplijn)
> [!div class="op_single_selector" title1="Selecteer de versie van de datafabriekservice die u gebruikt:"]
> * [Versie 1](data-factory-use-custom-activities.md)
> * [Versie 2 (huidige versie)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie [Aangepaste activiteiten in V2](../transform-data-using-dotnet-custom-activity.md)als u de huidige versie van de service Data Factory gebruikt.

Er zijn twee soorten activiteiten die u gebruiken in een Azure Data Factory-pijplijn.

- [Gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) om gegevens te verplaatsen tussen [ondersteunde bron- en sinkgegevensopslag.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Activiteiten voor gegevenstransformatie](data-factory-data-transformation-activities.md) om gegevens te transformeren met behulp van compute services zoals Azure HDInsight, Azure Batch en Azure Machine Learning.

Als u gegevens wilt verplaatsen naar/van een gegevensarchief dat Gegevensfabriek niet ondersteunt, maakt u een **aangepaste activiteit** met uw eigen logica voor gegevensverplaatsing en gebruikt u de activiteit in een pijplijn. Als u gegevens wilt transformeren/verwerken op een manier die niet wordt ondersteund door Data Factory, maakt u een aangepaste activiteit met uw eigen logica voor gegevenstransformatie en gebruikt u de activiteit in een pijplijn.

U een aangepaste activiteit configureren om uit te voeren op een **Azure Batch-groep** van virtuele machines. Wanneer u Azure Batch gebruikt, u alleen een bestaande Azure Batch-groep gebruiken.

De volgende walkthrough bevat stapsgewijze instructies voor het maken van een aangepaste .NET-activiteit en het gebruik van de aangepaste activiteit in een pijplijn. De walkthrough maakt gebruik van een **Azure Batch-gekoppelde** service.

> [!IMPORTANT]
> - Het is niet mogelijk om een Data Management Gateway te gebruiken vanuit een aangepaste activiteit om toegang te krijgen tot on-premises gegevensbronnen. Momenteel ondersteunt [Data Management Gateway](data-factory-data-management-gateway.md) alleen de kopieeractiviteit en opgeslagen procedureactiviteit in Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Walkthrough: een aangepaste activiteit maken
### <a name="prerequisites"></a>Vereisten
* Visual Studio 2012/2013/2015/2017
* Download en installeer [Azure .NET SDK](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Vereisten voor Azure Batch
In de walkthrough voert u uw aangepaste .NET-activiteiten uit met Azure Batch als rekenbron. **Azure Batch** is een platformservice voor het efficiënt uitvoeren van grootschalige parallelle en HPC-toepassingen (High Performance Computing) in de cloud. Azure Batch plant rekenintensief werk om uit te voeren op een beheerde **verzameling virtuele machines**en kan compute resources automatisch schalen om aan de behoeften van uw taken te voldoen. Zie [het basisartikel van Azure Batch][batch-technical-overview] voor een gedetailleerd overzicht van de Azure Batch-service.

Maak voor de zelfstudie een Azure Batch-account met een groep VM's. Dit zijn de stappen:

1. Maak een **Azure Batch-account** met de [Azure-portal](https://portal.azure.com). Zie Een Azure [Batch-accountartikel maken en beheren][batch-create-account] voor instructies.
2. Noteer de naam van het Azure Batch-account, de accountsleutel, de URI en de naam van de groep. U hebt ze nodig om een azure batch-gekoppelde service te maken.
    1. Op de startpagina voor Azure Batch-account ziet u `https://myaccount.westus.batch.azure.com`een **URL** in de volgende indeling: . In dit voorbeeld is **mijn account** de naam van het Azure Batch-account. URI die u gebruikt in de gekoppelde servicedefinitie is de URL zonder de naam van het account. Bijvoorbeeld: `https://<region>.batch.azure.com`.
    2. Klik op **Toetsen** in het linkermenu en kopieer de **PRIMAIRE TOEGANGSSLEUTEL**.
    3. Als u een bestaande groep wilt gebruiken, klikt u op **Zwembaden** in het menu en noteert u de **id** van de groep. Als u geen bestaande pool hebt, gaat u naar de volgende stap.
2. Een **Azure Batch-groep maken.**

   1. Klik in de [Azure-portal](https://portal.azure.com)op **Bladeren** in het linkermenu en klik op **Batchaccounts**.
   2. Selecteer uw Azure Batch-account om het **batchaccountblad** te openen.
   3. Klik op de tegel **Zwembaden.**
   4. Klik **in** het blad Pools op Knop Toevoegen op de werkbalk om een groep toe te voegen.
      1. Voer een id in voor de groep (Pool-ID). Let op de **ID van het zwembad**; je hebt het nodig bij het maken van de Data Factory-oplossing.
      2. Geef **Windows Server 2012 R2** op voor de instelling Besturingssysteemfamilie.
      3. Selecteer een **nodeprijslaag**.
      4. Voer **2** in als waarde voor de **instelling Doelspecifiek.**
      5. Voer **2** in als waarde voor de instelling **Max-taken per knooppunt.**
   5. Klik op **OK** om de groep te maken.
   6. Noteer de **ID** van het zwembad.

### <a name="high-level-steps"></a>Stappen op hoog niveau
Hier zijn de twee stappen op hoog niveau die u uitvoert als onderdeel van deze walkthrough:

1. Maak een aangepaste activiteit die eenvoudige logica voor gegevenstransformatie/verwerking bevat.
2. Maak een Azure-gegevensfabriek met een pijplijn die de aangepaste activiteit gebruikt.

### <a name="create-a-custom-activity"></a>Een aangepaste activiteit maken
Als u een aangepaste .NET-activiteit wilt maken, maakt u een **.NET-klassebibliotheekproject** met een klasse die die **IDotNetActiviteit-interface** implementeert. Deze interface heeft slechts één methode: [Uitvoeren](https://msdn.microsoft.com/library/azure/mt603945.aspx) en de handtekening ervan is:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

De methode heeft vier parameters nodig:

- **linkedServices**. Deze eigenschap is een enumerable lijst van data store gekoppelde services waarnaar wordt verwezen door invoer/uitvoergegevenssets voor de activiteit.
- **gegevenssets**. Deze eigenschap is een enumerable lijst van invoer/uitvoergegevenssets voor de activiteit. U deze parameter gebruiken om de locaties en schema's te definiëren door invoer- en uitvoergegevenssets.
- **activiteit**. Deze eigenschap vertegenwoordigt de huidige activiteit. Het kan worden gebruikt om toegang te krijgen tot uitgebreide eigenschappen die zijn gekoppeld aan de aangepaste activiteit. Zie [Uitgebreide eigenschappen](#access-extended-properties) voor access voor meer informatie.
- **logger**. Met dit object u foutopsporingsopmerkingen schrijven die in het gebruikerslogboek voor de pijplijn naar boven komen.

De methode retourneert een woordenboek dat kan worden gebruikt om aangepaste activiteiten in de toekomst aan elkaar te ketenen. Deze functie is nog niet geïmplementeerd, dus retourneer een leeg woordenboek uit de methode.

### <a name="procedure"></a>Procedure
1. Maak een **.NET-klassebibliotheekproject.**
   <ol type="a">
     <li>Start Visual Studio.</li>
     <li>Klik op <b>File</b>, houd de muisaanwijzer op <b>New</b> en klik op <b>Project</b>.</li>
     <li>Vouw <b>Templates</b> uit en selecteer <b>Visual C#</b>. In deze walkthrough gebruikt u C#, maar u elke .NET-taal gebruiken om de aangepaste activiteit te ontwikkelen.</li>
     <li>Selecteer <b>Klassebibliotheek</b> in de lijst met projecttypen aan de rechterkant. Kies in Visual Studio <b>klassebibliotheek (.NET Framework)</b> </li>
     <li>Voer <b>MyDotNetActiviteit</b> in voor de <b>naam</b>.</li>
     <li>Selecteer <b>C:\ADFGetStarted</b> voor de <b>locatie</b>.</li>
     <li>Klik op <b>OK</b> om het project te maken.</li>
   </ol>

2. Klik op **Tools**, wijs **NuGet Package Manager** aan en klik op **Package Manager Console**.

3. Voer in de Package Manager Console de volgende opdracht uit om **Microsoft.Azure.Management.DataFactories**te importeren.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importeer het **NuGet-pakket azure storage** in het project.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > Data Factory service launcher vereist de 4.3 versie van WindowsAzure.Storage. Als u een verwijzing toevoegt naar een latere versie van Azure Storage-assemblage in uw aangepaste activiteitenproject, ziet u een fout wanneer de activiteit wordt uitgevoerd. Zie Sectie [Appdomain-isolatie](#appdomain-isolation) als u de fout wilt oplossen.
5. Voeg het volgende **toe met behulp van** instructies aan het bronbestand in het project.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Wijzig de naam van de **naamruimte** in **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Wijzig de naam van de klasse in **MyDotNetActivity** en ontlenen aan de **IDotNetActivity-interface** zoals weergegeven in het volgende codefragment:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implementeer (Toevoegen) de **uitvoermethode** van de **IDotNetActiviteit-interface** naar de klasse **MyDotNetActiviteit** en kopieer de volgende voorbeeldcode naar de methode.

    In het volgende voorbeeld wordt het aantal exemplaren van de zoekterm ('Microsoft') geteld in elke blob die is gekoppeld aan een gegevenssegment.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
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

            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```
9. Voeg de volgende helpermethoden toe:

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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
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

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
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

    Met de methode GetFolderPath wordt het pad geretourneerd naar de map waarnaar de gegevensset verwijst en de methode GetFileName retourneert de naam van de blob/bestand waarnaar de gegevensset verwijst. Als u mapPath-definieert met variabelen zoals {Year}, {Month}, {Day} enz., retourneert de methode de tekenreeks zoals deze is zonder ze te vervangen door runtime-waarden. Zie [sectie Uitgebreide eigenschappen openen](#access-extended-properties) voor meer informatie over toegang tot SliceStart, SliceEnd, enz.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    Met de methode Berekenen wordt het aantal exemplaren van trefwoord Microsoft in de invoerbestanden (blobs in de map) berekend. De zoekterm ("Microsoft") is hardgecodeerd in de code.
10. Stel het project samen. Klik **op Bouwen** in het menu en klik op Oplossing **bouwen**.

    > [!IMPORTANT]
    > Stel 4.5.2-versie van .NET Framework in als het doelkader voor uw project: klik met de rechtermuisknop op het project en klik op **Eigenschappen** om het doelkader in te stellen. Data Factory ondersteunt geen aangepaste activiteiten die later dan 4.5.2 zijn gecompileerd met .NET Framework-versies.

11. Start **Windows Verkenner**en navigeer naar de map **foutopsporings** of bin\release naar de map **bin\release,** afhankelijk van het type build.
12. Maak een zip-bestand **MyDotNetActivity.zip** dat alle \<binaire\>bestanden in de projectmap \bin\Debug map bevat. Neem het **bestand MyDotNetActivity.pdb** op, zodat u aanvullende details krijgt, zoals regelnummer in de broncode die het probleem heeft veroorzaakt als er een storing is.

    > [!IMPORTANT]
    > Alle bestanden in het zip-bestand voor de aangepaste activiteit moeten zich op het **hoogste niveau** bevinden, zonder submappen.

    ![Binaire uitvoerbestanden](./media/data-factory-use-custom-activities/Binaries.png)
14. Maak een blobcontainer met de naam **customactivitycontainer** als deze nog niet bestaat.
15. Upload MyDotNetActivity.zip als een blob naar de customactivitycontainer in een **Azure blob-opslag voor algemene doeleinden** (niet hot/cool Blob-opslag) die wordt aangeduid door AzureStorageLinkedService.

> [!IMPORTANT]
> Als u dit .NET-activiteitenproject toevoegt aan een oplossing in Visual Studio die een Data Factory-project bevat en een verwijzing toevoegt naar het .NET-activiteitenproject van het datafabriek-toepassingsproject, hoeft u de laatste twee stappen van handmatig maken van de zip-selectie niet uit te voeren bestand en uploaden naar de Azure blob-opslag voor algemene doeleinden. Wanneer u gegevensfabriekentiteiten publiceert met Visual Studio, worden deze stappen automatisch uitgevoerd door het publicatieproces. Zie [Data Factory project in visual studio](#data-factory-project-in-visual-studio) sectie voor meer informatie.

## <a name="create-a-pipeline-with-custom-activity"></a>Een pijplijn maken met aangepaste activiteit
U hebt een aangepaste activiteit gemaakt en het zip-bestand met binaire bestanden geüpload naar een blobcontainer in een **Azure Storage-account** voor algemene doeleinden. In deze sectie maakt u een Azure-gegevensfabriek met een pijplijn die de aangepaste activiteit gebruikt.

De invoergegevensset voor de aangepaste activiteit vertegenwoordigt blobs (bestanden) in de map met invoer van aangepaste activiteitsinvoer van adftutorial-container in de blobopslag. De uitvoergegevensset voor de activiteit vertegenwoordigt uitvoerblobs in de map customactivityoutput van adftutorial-container in de blobopslag.

Maak **file.txt** met de volgende inhoud en upload het naar de map met invoer van **aangepaste activiteitsinvoer** van de **adftutorial-container.** Maak de adftutorial-container als deze nog niet bestaat.

```
test custom activity Microsoft test custom activity Microsoft
```

De invoermap komt overeen met een segment in Azure Data Factory, zelfs als de map twee of meer bestanden bevat. Wanneer elk segment door de pijplijn wordt verwerkt, wordt de aangepaste activiteit door alle blobs in de invoermap voor dat segment gehesen.

U ziet één uitvoerbestand met in de map adftutorial\customactivityoutput met een of meer regels (hetzelfde aantal blobs in de invoermap):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Dit zijn de stappen die u in deze sectie uitvoert:

1. Maak een **gegevensfabriek**.
2. **Gekoppelde services** maken voor de Azure Batch-groep vm's waarop de aangepaste activiteit wordt uitgevoerd en de Azure Storage met de invoer-/uitvoerblobs.
3. Maak invoer- en uitvoergegevenssets die input en uitvoer van de aangepaste activiteit **vertegenwoordigen.**
4. Maak een **pijplijn** die de aangepaste activiteit gebruikt.

> [!NOTE]
> Maak de **file.txt** en upload het naar een blobcontainer als u dit nog niet hebt gedaan. Zie instructies in de vorige sectie.

### <a name="step-1-create-the-data-factory"></a>Stap 1: De gegevensfabriek maken
1. Ga na het inloggen op de Azure-portal de volgende stappen uit:
   1. Klik **op Een resource maken** in het linkermenu.
   2. Klik **op Gegevens + Analytics** in het **nieuwe** blad.
   3. Klik op de blade **Gegevensanalyse** op **Gegevensfactory**.

      ![Nieuw azure-gegevensfabriekmenu](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Voer in het blad **Nieuwe gegevensfabriek** **CustomActivityFactory** voor de naam in. De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de fout ontvangt: **De naam van de gegevensfabriek "CustomActivityFactory" is niet beschikbaar,** wijzigt u de naam van de gegevensfabriek (bijvoorbeeld uw **naamCustomActivityFactory)** en probeert u opnieuw te maken.

    ![Nieuw Azure Data Factory-blad](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Klik **op NAAM VAN RESOURCEGROEP**en selecteer een bestaande resourcegroep of maak een resourcegroep.
4. Controleer of u het juiste abonnement en de **juiste** **regio** gebruikt waar u de gegevensfabriek wilt maken.
5. Klik op de blade **Nieuwe gegevensfactory** op **Maken**.
6. U ziet de gegevensfabriek die wordt gemaakt in het **dashboard** van de Azure-portal.
7. Nadat de gegevensfabriek met succes is gemaakt, ziet u het datafabriekblad, dat u de inhoud van de gegevensfabriek weergeeft.

    ![Blade Gegevensfactory](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Stap 2: Gekoppelde services maken
Met gekoppelde services worden gegevensarchieven of compute-services gekoppeld aan een Azure Data Factory. In deze stap koppelt u uw Azure Storage-account en Azure Batch-account aan uw gegevensfabriek.

#### <a name="create-azure-storage-linked-service"></a>Een gekoppelde Azure Storage-service maken
1. Klik op **de tegel Auteur en implementeer** de tegel op het **GEGEVENSFABRIEKSblad** voor **CustomActivityFactory**. U ziet de Data Factory Editor.
2. Klik op **Nieuw gegevensarchief** op de opdrachtbalk en kies **Azure-opslag**. U ziet het JSON-script voor het maken van een gekoppelde Azure Storage-service in de editor.

    ![Nieuw gegevensarchief - Azure Storage](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Vervang `<accountname>` de naam van uw `<accountkey>` Azure-opslagaccount en de toegangssleutel van het Azure-opslagaccount. Zie [Toegangssleutels voor opslagaccount beheren](../../storage/common/storage-account-keys-manage.md)voor meer informatie over het ophalen van uw opslagtoegangssleutel.

    ![Azure Storage-service](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Klik op de opdrachtbalk op **Implementeren** om de gekoppelde service te implementeren.

#### <a name="create-azure-batch-linked-service"></a>Gekoppelde Azure Batch-service maken
1. Klik in de Data Factory Editor op **... Klik** op De opdrachtbalk op **Nieuwe gegevensberekening**en selecteer **Vervolgens Azure Batch** in het menu.

    ![Nieuwe compute - Azure Batch](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Breng de volgende wijzigingen aan in het JSON-script:

   1. Geef de naam van Azure Batch-account op voor de eigenschap **accountName.** De **URL** van het **Azure Batch-accountblad** bevindt zich in de volgende indeling: `http://accountname.region.batch.azure.com`. Voor de eigenschap **batchUri** in de JSON moet u `accountname` de `accountName` URL verwijderen `accountname.` en de eigenschap JSON gebruiken.
   2. Geef de Azure Batch-accountsleutel op voor de eigenschap **accessKey.**
   3. Geef de naam op van de groep die u hebt gemaakt als onderdeel van de vereisten voor de eigenschap **poolName.** U ook de id van de groep opgeven in plaats van de naam van de groep.
   4. Geef Azure Batch URI op voor de eigenschap **batchUri.** Bijvoorbeeld: `https://westus.batch.azure.com`.
   5. Geef de **AzureStorageLinkedService** op voor de eigenschap **linkedServiceName.**

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       Voor de eigenschap **poolName** u ook de id van de groep opgeven in plaats van de naam van de groep.

### <a name="step-3-create-datasets"></a>Stap 3: Datasets maken
In deze stap maakt u gegevenssets om invoer- en uitvoergegevens weer te geven.

#### <a name="create-input-dataset"></a>Invoergegevensset maken
1. Klik in de **editor** voor de datafabriek op **... Klik** op De opdrachtbalk op **Nieuwe gegevensset**en selecteer **vervolgens Azure Blob-opslag** in het vervolgkeuzemenu.
2. Vervang de JSON in het rechterdeelvenster door het volgende JSON-fragment:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
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

   U maakt later een pijplijn in deze walkthrough met begintijd: 2016-11-16T00:00:00Z en eindtijd: 2016-11-16T05:00:00Z. Het is gepland om elk uur gegevens te produceren, **00**dus er zijn vijf invoer-/uitvoersegmenten (tussen 00:00:00 :00 -> 05:00:00). **05**

   De **frequentie** en **het interval** voor de invoergegevensset is ingesteld op **Uur** en **1,** wat betekent dat het invoersegment elk uur beschikbaar is. In dit voorbeeld is het hetzelfde bestand (file.txt) in de intputmap.

   Hier zijn de begintijden voor elk segment, dat wordt vertegenwoordigd door SliceStart-systeemvariabele in het bovenstaande JSON-fragment.
3. Klik **op Implementeren** op de werkbalk om de **Invoersetset**te maken en te implementeren. Controleer of u **TABEL IS GEMAAKT** ziet op de titelbalk van de editor.

#### <a name="create-an-output-dataset"></a>Een uitvoergegevensset maken
1. Klik in de **Data Factory-editor**op **... Klik** op De opdrachtbalk op **Nieuwe gegevensset**en selecteer **vervolgens Azure Blob-opslag**.
2. Vervang het JSON-script in het rechterdeelvenster door het volgende JSON-script:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
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

     Uitvoerlocatie is **adftutorial/customactivityoutput/** en uitvoerbestandsnaam is yyyy-MM-dd-HH.txt waar yyyy-MM-dd-HH het jaar, maand, datum en uur is van het segment dat wordt geproduceerd. Zie [Naslaginformatie voor ontwikkelaars][adf-developer-reference] voor meer informatie.

    Voor elk invoersegment wordt een uitvoerblob/-bestand gegenereerd. Hier is hoe een uitvoerbestand wordt genoemd voor elk segment. Alle uitvoerbestanden worden gegenereerd in één uitvoermap: **adftutorial\customactivityoutput**.

   | Segment | Begintijd | Uitvoerbestand |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Vergeet niet dat alle bestanden in een invoermap deel uitmaken van een segment met de hierboven vermelde begintijden. Wanneer dit segment wordt verwerkt, scant de aangepaste activiteit elk bestand en produceert een regel in het uitvoerbestand met het aantal exemplaren van de zoekterm ('Microsoft'). Als er drie bestanden in de invoermap staan, zijn er drie regels in het uitvoerbestand voor elk segment per uur: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt, enz.
3. Als u de **uitvoerset wilt**implementeren, klikt u op **Implementeren** op de opdrachtbalk.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Een pijplijn maken en uitvoeren die de aangepaste activiteit gebruikt
1. Klik in de Data Factory Editor op **... Meer**en selecteer Vervolgens **Nieuwe pijplijn** op de opdrachtbalk.
2. Vervang de JSON in het rechterdeelvenster door het volgende JSON-script:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Houd rekening met de volgende punten:

   * **Gelijktijdigheid** is ingesteld op **2,** zodat twee segmenten parallel worden verwerkt door 2 VM's in de azure batch-groep.
   * Er is één activiteit in de activiteitensectie en het is van type: **DotNetActivity**.
   * **AssemblyName** is ingesteld op de naam van de DLL: **MyDotnetActivity.dll**.
   * **EntryPoint** is ingesteld op **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** is ingesteld op **AzureStorageLinkedService** die verwijst naar de blob-opslag die het aangepaste zip-bestand voor activiteit bevat. Als u verschillende Azure Storage-accounts gebruikt voor invoer-/uitvoerbestanden en het aangepaste zip-bestand voor activiteit, maakt u een andere azure storage-gekoppelde service. In dit artikel wordt ervan uitgegaan dat u hetzelfde Azure Storage-account gebruikt.
   * **PackageFile** is ingesteld op **customactivitycontainer/MyDotNetActivity.zip**. Het is in het formaat: containerforthezip/nameofthezip.zip.
   * De aangepaste activiteit neemt **InputDataset** als invoer- en **Uitvoersetset** als uitvoer.
   * De eigenschap linkedServiceName van de aangepaste activiteit verwijst naar de **AzureBatchLinkedService**, die Azure Data Factory vertelt dat de aangepaste activiteit moet worden uitgevoerd op Azure Batch VM's.
   * **isPaused** eigenschap is standaard ingesteld op **false.** De pijplijn wordt onmiddellijk in dit voorbeeld uitgevoerd omdat de segmenten in het verleden zijn gestart. U deze eigenschap op true instellen om de pijplijn te pauzeren en deze weer op false in te stellen om opnieuw op te starten.
   * De **begin-** en **eindtijden** liggen **vijf** uur uit elkaar en segmenten worden elk uur geproduceerd, zodat vijf segmenten door de pijplijn worden geproduceerd.
3. Als u de pijplijn wilt implementeren, klikt u op **Implementeren** op de opdrachtbalk.

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken
1. Klik in het blade van gegevensfabriek in de Azure-portal op **Diagram**.

    ![Tegel Diagram](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Klik in de diagramweergave nu op de Uitvoersetset.

    ![Diagramweergave](./media/data-factory-use-custom-activities/diagram.png)
3. U moet zien dat de vijf uitvoersegmenten in de status Gereed staan. Als ze niet in de Ready staat, zijn ze nog niet geproduceerd.

   ![Uitvoersegmenten](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Controleer of de uitvoerbestanden worden gegenereerd in de blobopslag in de **adftutorial-container.**

   ![uitvoer van aangepaste activiteit][image-data-factory-output-from-custom-activity]
5. Als u het uitvoerbestand opent, ziet u de uitvoer die vergelijkbaar is met de volgende uitvoer:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Gebruik de [Azure-portal][azure-preview-portal] of Azure PowerShell-cmdlets om uw gegevensfabriek, pijplijnen en gegevenssets te controleren. U berichten van de **ActivityLogger** zien in de code voor de aangepaste activiteit in de logboeken (specifiek user-0.log) die u downloaden van de portal of met behulp van cmdlets.

   ![logboeken downloaden van aangepaste activiteit][image-data-factory-download-logs-from-custom-activity]

Zie [Pijplijnen controleren en beheren](data-factory-monitor-manage-pipelines.md) voor gedetailleerde stappen voor het bewaken van gegevenssets en pijplijnen.

## <a name="data-factory-project-in-visual-studio"></a>Data Factory project in Visual Studio
U entiteiten in Gegevensfabriek maken en publiceren met Behulp van Visual Studio in plaats van Azure-portal te gebruiken. Zie [Uw eerste pijplijn maken met Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) en Gegevens kopiëren van Azure Blob naar Azure [SQL-artikelen](data-factory-copy-activity-tutorial-using-visual-studio.md) voor gedetailleerde informatie over het maken en publiceren van entiteiten in gegevensfabriek met behulp van Visual Studio.

Ga als volgt te werk als u het project Data Factory maakt in Visual Studio:

1. Voeg het project Data Factory toe aan de Visual Studio-oplossing die het aangepaste activiteitenproject bevat.
2. Voeg een verwijzing toe naar het .NET-activiteitenproject uit het Data Factory-project. Klik met de rechtermuisknop op het project Gegevensfabriek, wijs **Toevoegen aan**en klik vervolgens op **Referentie**.
3. Selecteer in het dialoogvenster **Naslaginformatie toevoegen** het project **MyDotNetActiviteit** en klik op **OK**.
4. Bouw en publiceer de oplossing.

    > [!IMPORTANT]
    > Wanneer u entiteiten in Data Factory publiceert, wordt er automatisch een zip-bestand voor u gemaakt en wordt deze geüpload naar de blobcontainer: customactivitycontainer. Als de blobcontainer niet bestaat, wordt deze ook automatisch gemaakt.

## <a name="data-factory-and-batch-integration"></a>Integratie van gegevensfabrieken en batch
De service Gegevensfabriek maakt een taak in Azure Batch met de naam: **adf-poolname: job-xxx**. Klik **op Vacatures** in het linkermenu.

![Azure Data Factory - Batchtaken](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Er wordt een taak gemaakt voor elke activiteitsrun van een segment. Als er vijf segmenten klaar zijn om te worden verwerkt, worden er vijf taken gemaakt in deze taak. Als er meerdere rekenknooppunten in de batchgroep zijn, kunnen twee of meer segmenten parallel worden uitgevoerd. Als de maximale taken per compute-knooppunt zijn ingesteld op > 1, u ook meer dan één segment op dezelfde gegevensberekenen laten uitvoeren.

![Azure Data Factory - Batchtaken](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

In het volgende diagram wordt de relatie tussen Azure Data Factory- en Batch-taken geïllustreerd.

![Gegevensfabriek & batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Fouten oplossen
Het oplossen van problemen bestaat uit een aantal basistechnieken:

1. Als u de volgende fout ziet, gebruikt u mogelijk een Hot/Cool blob-opslag in plaats van een Azure-blobopslag voor algemene doeleinden te gebruiken. Upload het zip-bestand naar een **Azure Storage-account voor algemene doeleinden.**

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Als u de volgende fout ziet, bevestigt u dat de naam van de klasse in het CS-bestand overeenkomt met de naam die u hebt opgegeven voor de eigenschap **EntryPoint** in de JSON van de pijplijn. In de walkthrough is de naam van de klasse: MyDotNetActivity en het EntryPoint in de JSON is: MyDotNetActivityNS. **MyDotNetActiviteit**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Als de namen overeenkomen, bevestigt u of alle binaire bestanden zich in de **hoofdmap** van het zip-bestand bevinden. Dat wil zeggen, wanneer u het zip-bestand opent, moet u alle bestanden in de hoofdmap zien, niet in submappen.
3. Als het invoersegment niet is ingesteld op **Gereed,** bevestigt u dat de structuur van de invoermap juist is en **bestaat file.txt** in de invoermappen.
3. Gebruik in de **methode Uitvoeren** van uw aangepaste activiteit het object **IActivityLogger** om gegevens te registreren waarmee u problemen oplossen. De geregistreerde berichten worden weergegeven in de gebruikerslogboekbestanden (een of meer bestanden met de naam: user-0.log, user-1.log, user-2.log, etc.).

   Klik in het blade **OutputDataset** op het segment om het **dataslice-blad** voor dat segment weer te geven. U ziet **activiteitsuitvoeringen** voor dat segment. U ziet één activiteit srun voor het segment. Als u op Uitvoeren klikt in de opdrachtbalk, u een andere activiteitsrun voor hetzelfde segment starten.

   Wanneer u op de activiteitsrun klikt, ziet u het blad **DETAILS ACTIVITEIT RUN** met een lijst met logboekbestanden. U ziet geregistreerde berichten in het bestand user_0.log. Wanneer er een fout optreedt, ziet u drie activiteitsruns omdat het aantal nieuwe gegevens is ingesteld op 3 in de JSON voor pijplijn/activiteit. Wanneer u op de activiteitsrun klikt, ziet u de logboekbestanden die u controleren om de fout op te lossen.

   Klik in de lijst met logboekbestanden op **het gebruikers-0.log**. In het rechterpaneel zijn de resultaten van het gebruik van de **IActivityLogger.Write** methode. Als u niet alle berichten ziet, controleert u of er meer logbestanden met de naam: user_1.log, user_2.log etc. Anders is de code mogelijk mislukt na het laatst aangemelde bericht.

   Controleer bovendien **systeem-0.log** op eventuele systeemfoutmeldingen en uitzonderingen.
4. Neem het **PDB-bestand** op in het zip-bestand, zodat de foutgegevens informatie bevatten, zoals **de oproepstack** wanneer er een fout optreedt.
5. Alle bestanden in het zip-bestand voor de aangepaste activiteit moeten zich op het **hoogste niveau** bevinden, zonder submappen.
6. Zorg ervoor dat de **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) en **packageLinkedService** (moet wijzen op de **Azure blob-opslag**voor algemene doeleinden die het zip-bestand bevat) zijn ingesteld op juiste waarden.
7. Als u een fout hebt hersteld en het segment opnieuw wilt verwerken, klikt u met de rechtermuisknop op het segment op de blade **OutputDataset** en klikt u op **Uitvoeren**.
8. Als u de volgende fout ziet, gebruikt u het Azure Storage-pakket van versie > 4.3.0. Data Factory service launcher vereist de 4.3 versie van WindowsAzure.Storage. Zie [sectie Appdomain-isolatie](#appdomain-isolation) voor een tijdelijke oplossing als u de latere versie van Azure Storage-assemblage moet gebruiken.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Als u de 4.3.0-versie van het Azure Storage-pakket gebruiken, verwijdert u de bestaande verwijzing naar het Azure Storage-pakket van versie > 4.3.0. Voer vervolgens de volgende opdracht uit van NuGet Package Manager Console.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Maak het project. Azure.Storage-verzameling van versie > 4.3.0 verwijderen uit de map foutopsporing van de opslaglocatie. Maak een zip-bestand met binaire bestanden en het PDB-bestand. Vervang het oude zip-bestand door deze in de blobcontainer (customactivitycontainer). Voer de segmenten die zijn mislukt opnieuw uit (klik met de rechtermuisknop op segment en klik op Uitvoeren).
8. De aangepaste activiteit maakt geen gebruik van het **app.config-bestand** uit uw pakket. Als uw code daarom verbindingstekenreeksen uit het configuratiebestand leest, werkt deze niet tijdens runtime. De beste praktijk bij het gebruik van Azure Batch is om geheimen in een **Azure KeyVault**te bewaren, een op certificaten gebaseerde serviceprincipal te gebruiken om de **keyvault**te beschermen en het certificaat te distribueren naar Azure Batch-groep. De aangepaste .NET-activiteit heeft dan in runtime toegang tot geheimen in de KeyVault. Deze oplossing is een generieke oplossing en kan worden geschaald naar elk type geheim, niet alleen verbindingstekenreeks.

   Er is een eenvoudigere tijdelijke oplossing (maar geen best practice): u een **Azure SQL-gekoppelde service** maken met verbindingstekenreeksinstellingen, een gegevensset maken die de gekoppelde service gebruikt en de gegevensset als een dummy-invoergegevensset koppelen aan de aangepaste .NET-activiteit. U vervolgens de verbindingstekenreeks van de gekoppelde service openen in de aangepaste activiteitscode.

## <a name="update-custom-activity"></a>Aangepaste activiteit bijwerken
Als u de code voor de aangepaste activiteit bijwerkt, bouwt u deze en uploadt u het zip-bestand met nieuwe binaire bestanden naar de blob-opslag.

## <a name="appdomain-isolation"></a>Appdomain-isolatie
Zie [Voorbeeld van Cross AppDomain,](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) waarmee u zien hoe u een aangepaste activiteit maakt die niet is beperkt tot assemblageversies die worden gebruikt door het startprogramma Gegevensfabriek (bijvoorbeeld WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x, enz.).

## <a name="access-extended-properties"></a>Toegang tot uitgebreide eigenschappen
U uitgebreide eigenschappen declareren in de activiteit JSON zoals weergegeven in het volgende voorbeeld:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

In het voorbeeld zijn er twee uitgebreide eigenschappen: **SliceStart** en **DataFactoryName**. De waarde voor SliceStart is gebaseerd op de variabele SliceStart-systeem. Zie [Systeemvariabelen](data-factory-functions-variables.md) voor een lijst met ondersteunde systeemvariabelen. De waarde voor DataFactoryName is hard gecodeerd naar CustomActivityFactory.

Als u toegang wilt krijgen tot deze uitgebreide eigenschappen in de methode **Uitvoeren,** gebruikt u code die vergelijkbaar is met de volgende code:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Automatisch schalen van Azure Batch
U ook een Azure Batch-groep maken met **functie voor automatisch schalen.** U bijvoorbeeld een azure batchpool maken met 0 speciale VM's en een formule voor automatisch schalen op basis van het aantal lopende taken.

De voorbeeldformule hier bereikt het volgende gedrag: Wanneer de groep in eerste instantie wordt gemaakt, begint deze met 1 VM. $PendingTasks statistiek bepaalt het aantal taken in de status uitvoeren + actief (in de wachtrij).  De formule vindt het gemiddelde aantal lopende taken in de laatste 180 seconden en stelt TargetDedicated dienovereenkomstig in. Het zorgt ervoor dat TargetDedicated nooit verder gaat dan 25 VM's. Dus, als nieuwe taken worden ingediend, groep groeit automatisch en als taken worden voltooid, VM's worden gratis een voor een en de autoscaling krimpt die VM's. startNumberOfVm's en maxNumberofVM's kunnen worden aangepast aan uw behoeften.

Formule automatisch schalen:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Zie [Compute-knooppunten automatisch schalen in een Azure Batch-groep](../../batch/batch-automatic-scaling.md) voor meer informatie.

Als de groep het [standaardautoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)gebruikt, kan het 15-30 minuten duren voordat de batchservice de VM voorbereidt voordat de aangepaste activiteit wordt uitgevoerd.  Als de groep een andere autoScaleEvaluationInterval gebruikt, kan de batchservice autoScaleEvaluationInterval + 10 minuten inbeslagnemen.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Een aangepaste activiteit maken met .NET SDK
In de walkthrough in dit artikel maakt u een gegevensfabriek met een pijplijn die de aangepaste activiteit gebruikt met behulp van de Azure-portal. In de volgende code ziet u hoe u de gegevensfabriek maakt met behulp van .NET SDK. U meer informatie vinden over het gebruik van SDK om programmatisch pijplijnen te maken in de [pijplijn maken met kopieeractiviteit met behulp van .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md) API-artikel.

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Aangepaste activiteit debuggen in Visual Studio
Het voorbeeld [van Azure Data Factory - lokale omgeving](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) op GitHub bevat een tool waarmee u aangepaste .NET-activiteiten binnen Visual Studio debuggen.

## <a name="sample-custom-activities-on-github"></a>Aangepaste activiteiten op GitHub proeven
| Voorbeeld | Welke aangepaste activiteit doet |
| --- | --- |
| [HTTP-gegevensdownloader](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/HttpDataDownloaderSample). |Downloadt gegevens van een HTTP-eindpunt naar Azure Blob Storage met aangepaste C#-activiteit in gegevensfabriek. |
| [Twitter Sentiment Analyse voorbeeld](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TwitterAnalysisSample-CustomC%23Activity) |Beroept zich op een Azure Machine Learning studio model en doen sentiment analyse, scoren, voorspelling etc. |
| [Voer R Script uit](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). |Hiermee wordt R-script aangeroepen door RScript.exe uit te voeren op uw HDInsight-cluster waarop R al is geïnstalleerd. |
| [Activiteit Cross AppDomain .NET](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/CrossAppDomainDotNetActivitySample) |Gebruikt verschillende assemblageversies van versies die worden gebruikt door de Data Factory-launcher |
| [Een model opnieuw verwerken in Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/AzureAnalysisServicesProcessSample) |  Hiermee verwerkt u een model opnieuw in Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

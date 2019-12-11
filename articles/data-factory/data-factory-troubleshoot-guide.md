---
title: Problemen oplossen Azure Data Factory | Microsoft Docs
description: Meer informatie over het oplossen van problemen met externe controle activiteiten in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 2ae0f3033b88b3229d3dbef35c8bc9a32510c00e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972333"
---
# <a name="troubleshoot-azure-data-factory"></a>Problemen met Azure Data Factory oplossen

In dit artikel worden algemene probleemoplossings methoden besproken voor externe controle activiteiten in Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Connector-en kopieer activiteit

Raadpleeg [problemen met Azure Data Factory connectors oplossen](connector-troubleshoot-guide.md)voor problemen met de connector, bijvoorbeeld wanneer er een fout optreedt.
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Fout code: 3200

- **Bericht**: fout 403.

- **Oorzaak**: `The Databricks access token has expired.`

- **Aanbeveling**: standaard is het toegangs token van Azure Databricks geldig gedurende 90 dagen. Maak een nieuw token en werk de gekoppelde service bij.


### <a name="error-code--3201"></a>Fout code: 3201

- **Bericht**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Oorzaak**: `Bad authoring: Notebook path not specified correctly.`

- **Aanbeveling**: Geef het pad naar de notebook op in de Databricks-activiteit.

<br/>  

- **Bericht**: `Cluster... does not exist.`

- **Oorzaak**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Aanbeveling**: Controleer of het Databricks-cluster bestaat.

<br/>  

- **Bericht**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Oorzaak**: `Bad authoring.`

- **Aanbeveling**: Geef een absoluut pad op voor werk ruimte-adresserings schema's of `dbfs:/folder/subfolder/foo.py` voor bestanden die zijn opgeslagen in Databricks File System.

<br/>  

- **Bericht**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Oorzaak**: `Bad authoring.`

- **Aanbeveling**: Controleer de [definitie van de gekoppelde service](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Bericht**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Oorzaak**: `Bad authoring.`

- **Aanbeveling**: Controleer de [definitie van de gekoppelde service](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Bericht**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Oorzaak**: `Bad authoring.`

- **Aanbeveling**: Raadpleeg het fout bericht.

<br/>

### <a name="error-code--3202"></a>Fout code: 3202

- **Bericht**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Oorzaak**: `Too many Databricks runs in an hour.`

- **Aanbeveling**: alle pijp lijnen controleren die gebruikmaken van deze Databricks-werk ruimte voor het maken van een taak.  Als pijp lijnen te veel Databricks-runs worden uitgevoerd, migreert u enkele pijp lijnen naar een nieuwe werk ruimte.

<br/>  

- **Bericht**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Oorzaak**: `Authoring error: No value provided for the parameter.`

- **Aanbeveling**: Inspecteer de JSON van de pijp lijn en zorg ervoor dat alle para meters in de baseParameters-notebook een niet-lege waarde opgeven.

<br/>  

- **Bericht**: `User: `SimpleUserContext {gebruikersID =..., name =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Oorzaak**: de gebruiker die het toegangs token heeft gegenereerd, heeft geen toegang tot het Databricks-cluster dat is opgegeven in de gekoppelde service.

- **Aanbeveling**: Zorg ervoor dat de gebruiker over de vereiste machtigingen beschikt in de werk ruimte.


### <a name="error-code--3203"></a>Fout code: 3203

- **Bericht**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Oorzaak**: het cluster is beëindigd. Voor interactieve clusters kan dit een race voorwaarde zijn.

- **Aanbeveling**: de beste manier om deze fout te vermijden, is door taak clusters te gebruiken.


### <a name="error-code--3204"></a>Fout code: 3204

- **Bericht**: `Job execution failed.`

- **Oorzaak**: fout berichten geven verschillende problemen aan, zoals een onverwachte cluster status of een specifieke activiteit. Er wordt meestal helemaal geen fout bericht weer gegeven.

- **Aanbeveling**: n.v.t.
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

De volgende tabel is van toepassing op U-SQL.
      
### <a name="error-code--2709"></a>Fout code: 2709

- **Bericht**: `The access token is from the wrong tenant.`

- **Oorzaak**: onjuiste Azure Active Directory-Tenant (Azure AD).

- **Aanbeveling**: onjuiste Azure Active Directory-Tenant (Azure AD).

<br/>

- **Bericht**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Oorzaak**: deze fout wordt veroorzaakt door het beperken van data Lake Analytics.

- **Aanbeveling**: Verminder het aantal verzonden taken naar Data Lake Analytics door Data Factory triggers en gelijktijdigheids instellingen voor activiteiten te wijzigen. Of verg root de limieten voor Data Lake Analytics.

<br/>  

- **Bericht**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Oorzaak**: deze fout wordt veroorzaakt door het beperken van data Lake Analytics.

- **Aanbeveling**: Verminder het aantal verzonden taken naar Data Lake Analytics door Data Factory triggers en gelijktijdigheids instellingen voor activiteiten te wijzigen. Of verg root de limieten voor Data Lake Analytics.


### <a name="error-code--2705"></a>Fout code: 2705

- **Bericht**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Oorzaak**: de service-principal of het certificaat heeft geen toegang tot het bestand in de opslag.

- **Aanbeveling**: Zorg ervoor dat de service-principal of het certificaat dat de gebruiker biedt voor data Lake Analytics taken toegang heeft tot het data Lake Analytics account en de standaard data Lake Storage instantie van de hoofdmap.


### <a name="error-code--2711"></a>Fout code: 2711

- **Bericht**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Oorzaak**: de service-principal of het certificaat heeft geen toegang tot het bestand in de opslag.

- **Aanbeveling**: Zorg ervoor dat de service-principal of het certificaat dat de gebruiker biedt voor data Lake Analytics taken toegang heeft tot het data Lake Analytics account en de standaard data Lake Storage instantie van de hoofdmap.

<br/>  

- **Bericht**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Oorzaak**: het pad naar het U-SQL-bestand is onjuist of de referenties van de gekoppelde service hebben geen toegang.

- **Aanbeveling**: Controleer het pad en de referenties die zijn opgenomen in de gekoppelde service.


### <a name="error-code--2704"></a>Fout code: 2704

- **Bericht**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Oorzaak**: de service-principal of het certificaat heeft geen toegang tot het bestand in de opslag.

- **Aanbeveling**: Zorg ervoor dat de service-principal of het certificaat dat de gebruiker biedt voor data Lake Analytics taken toegang heeft tot het data Lake Analytics account en de standaard data Lake Storage instantie van de hoofdmap.


### <a name="error-code--2707"></a>Fout code: 2707

- **Bericht**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Oorzaak**: het data Lake Analytics-account in de gekoppelde service is onjuist.

- **Aanbeveling**: Controleer of het juiste account is ingesteld.


### <a name="error-code--2703"></a>Fout code: 2703

- **Bericht**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Oorzaak**: de fout is van data Lake Analytics.

- **Aanbeveling**: een fout zoals in het voor beeld betekent dat de taak is verzonden naar Data Lake Analytics en dat het script is mislukt. Onderzoek in Data Lake Analytics. Ga in de portal naar het Data Lake Analytics-account en zoek de taak met behulp van de Data Factory-activiteit run ID (niet de pijplijn run-ID). De taak bevat meer informatie over de fout en helpt u bij het oplossen van problemen. Als de oplossing niet duidelijk is, neemt u contact op met het Data Lake Analytics ondersteunings team en geeft u de taak-URL op, die uw account naam en de taak-ID bevat.
          

## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Fout code: 3602

- **Bericht**: `Invalid HttpMethod: '%method;'.`

- **Oorzaak**: de HTTP-methode die in de activiteit lading is opgegeven, wordt niet ondersteund door Azure function-activiteit.

- **Aanbeveling**: de HTTP-methoden die worden ondersteund, zijn plaats, plaatsen, ophalen, verwijderen, opties, kop en tracering.


### <a name="error-code--3603"></a>Fout code: 3603

- **Bericht**: `Response Content is not a valid JObject.`

- **Oorzaak**: de Azure-functie die is aangeroepen, heeft geen JSON-nettolading geretourneerd in het antwoord. ADF Azure function-activiteiten bieden alleen ondersteuning voor inhoud van een JSON-antwoord.

- **Aanbeveling**: werk de Azure-functie bij om een geldige JSON Payload te C# retour neren, bijvoorbeeld een functie retourneert (ActionResult) New OkObjectResult ("{\"Id\":\"123\"}");


### <a name="error-code--3606"></a>Fout code: 3606

- **Bericht**: functie sleutel ontbreekt in azure functions-activiteit.

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureFunction de eigenschap met de naam ' functionKey ' heeft.


### <a name="error-code--3607"></a>Fout code: 3607

- **Bericht**: `Azure function activity missing function name.`

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureFunction de eigenschap ' functie naam ' heeft.


### <a name="error-code--3608"></a>Fout code: 3608

- **Bericht**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Oorzaak**: de details van de Azure-functie in de activiteiten definitie zijn mogelijk onjuist.

- **Aanbeveling**: Corrigeer de Azure-functie gegevens en probeer het opnieuw.


### <a name="error-code--3609"></a>Fout code: 3609

- **Bericht**: `Azure function activity missing functionAppUrl.`

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureFunction de eigenschap met de naam ' functionAppUrl ' heeft.


### <a name="error-code--3610"></a>Fout code: 3610

- **Bericht**: `There was an error while calling endpoint.`

- **Oorzaak**: de URL van de functie is mogelijk onjuist.

- **Aanbeveling**: Controleer of de waarde voor ' functionAppUrl ' in de JSON van de activiteit juist is en probeer het opnieuw.


### <a name="error-code--3611"></a>Fout code: 3611

- **Bericht**: `Azure function activity missing Method in JSON.`

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureFunction-activiteit een eigenschap met de naam ' method ' heeft.


### <a name="error-code--3612"></a>Fout code: 3612

- **Bericht**: `Azure function activity missing LinkedService definition in JSON.`

- **Oorzaak**: de definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureFunction-activiteit gekoppelde service Details bevat.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Fout code: 4101

- **Bericht**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Oorzaak**: onjuiste indeling of ontbrekende definitie van eigenschap% PropertyName;.

- **Aanbeveling**: Controleer of voor de activiteit% activiteitsnummer; de eigenschap% PropertyName; is gedefinieerd met de juiste gegevens.


### <a name="error-code--4110"></a>Fout code: 4110

- **Bericht**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Oorzaak**: de definitie van de AzureMLExecutePipeline-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de input AzureMLExecutePipeline-activiteit gekoppelde service Details bevat.


### <a name="error-code--4111"></a>Fout code: 4111

- **Bericht**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Oorzaak**: onjuiste definitie van de activiteit.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureMLExecutePipeline-activiteit de juiste details van de gekoppelde service heeft.


### <a name="error-code--4112"></a>Fout code: 4112

- **Bericht**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Oorzaak**: onjuiste indeling of ontbrekende definitie van eigenschap% PropertyName;.

- **Aanbeveling**: Controleer of de gekoppelde service eigenschap% PropertyName; heeft gedefinieerd met de juiste gegevens.


### <a name="error-code--4121"></a>Fout code: 4121

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: de referentie die wordt gebruikt voor toegang tot Azure machine learning is verlopen.

- **Aanbeveling**: Controleer of de referenties geldig zijn en probeer het opnieuw


### <a name="error-code--4122"></a>Fout code: 4122

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: de in azure machine learning gekoppelde service opgenomen referentie is ongeldig of heeft geen machtiging voor de bewerking.

- **Aanbeveling**: Controleer of de referentie in de gekoppelde service geldig is en de machtiging heeft om toegang te krijgen tot Azure machine learning.


### <a name="error-code--4123"></a>Fout code: 4123

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: de eigenschappen van de activiteit, zoals pipelineParameters, zijn ongeldig voor de Azure ml-pijp lijn.

- **Aanbeveling**: Controleer de waarde van de activiteit eigenschappen die overeenkomt met de verwachte payload van de gepubliceerde Azure ml-pijp lijn die is opgegeven in de gekoppelde service.


### <a name="error-code--4124"></a>Fout code: 4124

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: het gepubliceerde eind punt van de Azure ml-pijp lijn bestaat niet.

- **Aanbeveling**: Controleer of het gepubliceerde Azure machine learning pijplijn-eind punt dat is opgegeven in de gekoppelde service voor komt in azure machine learning.


### <a name="error-code--4125"></a>Fout code: 4125

- **Bericht**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak**: Server fout op Azure machine learning.

- **Aanbeveling**: Probeer het later opnieuw. Neem contact op met Azure Machine Learning team voor hulp als het probleem blijft bestaan.


### <a name="error-code--4126"></a>Fout code: 4126

- **Bericht**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Oorzaak**: de uitvoering van de Azure ml-pijp lijn is mislukt.

- **Aanbeveling**: Raadpleeg Azure machine learning voor meer fout logboeken en herstel de ml-pijp lijn.



## <a name="common"></a>Algemeen

### <a name="error-code--2103"></a>Fout code: 2103

- **Bericht**: `Please provide value for the required property '%propertyName;'.`

- **Oorzaak**: de waarde voor de eigenschap is niet opgegeven, maar is wel vereist in het scenario.

- **Aanbeveling**: Geef de waarde op uit het bericht en probeer het opnieuw.


### <a name="error-code--2104"></a>Fout code: 2104

- **Bericht**: `The type of the property '%propertyName;' is incorrect.`

- **Oorzaak**: het type van de gegeven eigenschap is niet zoals verwacht.

- **Aanbeveling**: Corrigeer het type van de eigenschap en probeer het opnieuw.


### <a name="error-code--2105"></a>Fout code: 2105

- **Bericht**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Oorzaak**: de waarde voor de eigenschap is ongeldig of heeft niet de verwachte indeling.

- **Aanbeveling**: Zoek de documentatie voor de eigenschap op en zorg ervoor dat de waarde de juiste notatie en type heeft.


### <a name="error-code--2106"></a>Fout code: 2106

- **Bericht**: `The storage connection string is invalid. %errorMessage;`

- **Oorzaak**: de Connection String voor de opslag is ongeldig of heeft een onjuiste indeling.

- **Aanbeveling**: Ga naar de Azure Portal, Zoek uw opslag, kopieer de Connection String en plak de gekoppelde service en probeer het opnieuw.


### <a name="error-code--2108"></a>Fout code: 2108

- **Bericht**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Oorzaak**: de aanvraag is mislukt vanwege een onderliggend probleem, zoals een netwerk verbinding, een DNS-fout, een validatie van het server certificaat of een time-out.

- **Aanbeveling**: gebruik Fiddler/postman om de aanvraag te valideren.


### <a name="error-code--2110"></a>Fout code: 2110

- **Bericht**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Oorzaak**: de gekoppelde service die in de activiteit is opgegeven, is onjuist.

- **Aanbeveling**: Controleer of het gekoppelde service type een van de ondersteunde typen voor de activiteit is. Voor HDI-activiteiten kan het type van de gekoppelde service bijvoorbeeld HDInsight of HDInsightOnDemand zijn.


### <a name="error-code--2111"></a>Fout code: 2111

- **Bericht**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Oorzaak**: het type van de gegeven eigenschap is niet zoals verwacht.

- **Aanbeveling**: Corrigeer het type van de eigenschap en probeer het opnieuw.


### <a name="error-code--2112"></a>Fout code: 2112

- **Bericht**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Oorzaak**: het Cloud type wordt niet ondersteund of kan niet worden vastgesteld voor opslag van de EndpointSuffix.

- **Aanbeveling**: gebruik opslag in een andere Cloud en probeer het opnieuw.


### <a name="error-code--2128"></a>Fout code: 2128

- **Bericht**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Oorzaak**: netwerk connectiviteit, DNS-fout, validatie van server certificaat of time-out.

- **Aanbeveling**: Controleer of het eind punt dat u probeert te raken reageert op aanvragen. U kunt hulpprogram ma's zoals Fiddler/postman gebruiken.



## <a name="custom"></a>Aangepast

De volgende tabel is van toepassing op Azure Batch.
      
### <a name="error-code--2500"></a>Fout code: 2500

- **Bericht**: `Hit unexpected exception and execution failed.`

- **Oorzaak**: `Can't launch command, or the program returned an error code.`

- **Aanbeveling**: Controleer of het uitvoer bare bestand bestaat. Als het programma is gestart, controleert u of *stdout. txt* en *stderr. txt* zijn geüpload naar het opslag account. Het is een goed idee om copious-Logboeken in uw code te verzenden voor fout opsporing.


### <a name="error-code--2501"></a>Fout code: 2501

- **Bericht**: `Cannot access user batch account; please check batch account settings.`

- **Oorzaak**: onjuiste batch-toegangs sleutel of groeps naam.

- **Aanbeveling**: Controleer de naam van de groep en de sleutel voor batch toegang in de gekoppelde service.


### <a name="error-code--2502"></a>Fout code: 2502

- **Bericht**: `Cannot access user storage account; please check storage account settings.`

- **Oorzaak**: de naam van het opslag account of de toegangs sleutel is onjuist.

- **Aanbeveling**: Controleer de naam van het opslag account en de toegangs sleutel in de gekoppelde service.


### <a name="error-code--2504"></a>Fout code: 2504

- **Bericht**: `Operation returned an invalid status code 'BadRequest'.`

- **Oorzaak**: te veel bestanden in de FolderPath van de aangepaste activiteit. De totale grootte van de Resource files mag niet langer zijn dan 32.768 tekens.

- **Aanbeveling**: overbodige bestanden verwijderen. Of zip en voeg een unzip-opdracht toe om deze uit te pakken. Gebruik bijvoorbeeld `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Fout code: 2505

- **Bericht**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Oorzaak**: aangepaste activiteiten bieden alleen ondersteuning voor opslag accounts die gebruikmaken van een toegangs sleutel.

- **Aanbeveling**: Raadpleeg de beschrijving van de fout.


### <a name="error-code--2507"></a>Fout code: 2507

- **Bericht**: `The folder path does not exist or is empty: ...`

- **Oorzaak**: er zijn geen bestanden in het opslag account op het opgegeven pad.

- **Aanbeveling**: het mappad moet de uitvoer bare bestanden bevatten die u wilt uitvoeren.


### <a name="error-code--2508"></a>Fout code: 2508

- **Bericht**: `There are duplicate files in the resource folder.`

- **Oorzaak**: meerdere bestanden met dezelfde naam bevinden zich in verschillende submappen van folderPath.

- **Aanbeveling**: aangepaste activiteiten de mapstructuur afvlakken onder folderPath.  Als u de mappen structuur wilt behouden, moet u de bestanden zip en uitpakken in Azure Batch met behulp van een unzip opdracht. Gebruik bijvoorbeeld `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Fout code: 2509

- **Bericht**: `Batch   url ... is invalid; it must be in Uri format.`

- **Oorzaak**: batch-url's moeten overeenkomen met `https://mybatchaccount.eastus.batch.azure.com`

- **Aanbeveling**: Raadpleeg de beschrijving van de fout.


### <a name="error-code--2510"></a>Fout code: 2510

- **Bericht**: `An   error occurred while sending the request.`

- **Oorzaak**: de batch-URL is ongeldig.

- **Aanbeveling**: Controleer de batch-URL.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Fout code: 200

- **Bericht**: `Unexpected error happened: '%error;'.`

- **Oorzaak**: er is een probleem met de interne service.

- **Aanbeveling**: Neem contact op met de ondersteuning voor ADF voor verdere ondersteuning.


### <a name="error-code--201"></a>Fout code: 201

- **Bericht**: `JobType %jobType; is not found.`

- **Oorzaak**: er is een nieuw taak type dat niet wordt ondersteund door ADF.

- **Aanbeveling**: Neem contact op met het ADF-ondersteunings team voor meer informatie.


### <a name="error-code--202"></a>Fout code: 202

- **Bericht**: `Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**: in het fout bericht worden de details weer gegeven van wat er verkeerd is gegaan.

- **Aanbeveling**: het fout bericht moet u helpen het probleem op te lossen. Als er onvoldoende informatie is, neemt u contact op met de ondersteuning voor ADF voor verdere hulp.


### <a name="error-code--203"></a>Fout code: 203

- **Bericht**: `Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**: in het fout bericht worden de details weer gegeven van wat er verkeerd is gegaan.

- **Aanbeveling**: het fout bericht moet u helpen het probleem op te lossen. Als er onvoldoende informatie is, neemt u contact op met de ondersteuning voor ADF voor verdere hulp.


### <a name="error-code--204"></a>Fout code: 204

- **Bericht**: `The resumption token is missing for runId '%runId;'.`

- **Oorzaak**: er is een probleem met de interne service.

- **Aanbeveling**: Neem contact op met de ondersteuning voor ADF voor verdere ondersteuning.


### <a name="error-code--205"></a>Fout code: 205

- **Bericht**: `Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Oorzaak**: er is een fout opgetreden bij het maken van het HDI op het vraag cluster.

- **Aanbeveling**: Neem contact op met de ondersteuning voor ADF voor verdere ondersteuning.


### <a name="error-code--206"></a>Fout code: 206

- **Bericht**: `The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Oorzaak**: er is een intern probleem opgetreden met de service waardoor dit is veroorzaakt.

- **Aanbeveling**: dit kan een tijdelijk probleem zijn. Voer de taak opnieuw uit en neem contact op met de ondersteuning voor ADF als het probleem zich blijft voordoen.


### <a name="error-code--207"></a>Fout code: 207

- **Bericht**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Oorzaak**: er is een interne fout opgetreden bij het bepalen van de regio van het primaire opslag account.

- **Aanbeveling**: Probeer een andere opslag. Als dit geen acceptabele oplossing is, neemt u contact op met het ADF-ondersteunings team voor meer informatie.


### <a name="error-code--208"></a>Fout code: 208

- **Bericht**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Oorzaak**: er is een interne fout opgetreden tijdens het lezen van de service-principal of het instantiëren van de MSI-verificatie.

- **Aanbeveling**: overweeg om een service-principal op te geven met machtigingen voor het maken van een HDInsight-cluster in het opgegeven abonnement en probeer het opnieuw. Als dit geen acceptabele oplossing is, neemt u contact op met het ADF-ondersteunings team voor meer informatie.


### <a name="error-code--2300"></a>Fout code: 2300

- **Bericht**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Oorzaak**: wanneer het fout bericht een bericht bevat dat vergelijkbaar is met de externe naam kan niet worden omgezet. Dit kan betekenen dat de URI van de cluster ongeldig is.


- **Aanbeveling**: Zorg ervoor dat het cluster niet is verwijderd en dat de gegeven URI juist is. Wanneer u de URI in een browser opent, ziet u de Ambari-gebruikers interface. Als het cluster zich in een virtueel netwerk bevindt, moet de URI de persoonlijke URI zijn. Als u het bestand wilt openen, gebruikt u een virtuele machine die deel uitmaakt van hetzelfde virtuele netwerk. [Zie voor](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)meer informatie.
                  

<br>

- **Oorzaak**: wanneer het fout bericht een bericht bevat dat lijkt op een taak die is geannuleerd., betekent dit dat er een time-out is opgetreden voor de taak verzending.

- **Aanbeveling**: het probleem kan algemeen HDInsight-connectiviteit of een netwerk verbinding zijn. Controleer eerst of de gebruikers interface van HDInsight Ambari beschikbaar is vanuit elke browser. Controleer of uw referenties nog geldig zijn. Als u gebruikmaakt van zelf-hostende Integrated Runtime (IR), moet u ervoor zorgen dat u dit doet vanaf de virtuele machine of computer waarop de zelf-hostende IR is geïnstalleerd. Probeer de taak vervolgens opnieuw uit Data Factory te verzenden. Als het nog steeds niet lukt, neemt u contact op met het Data Factory-team voor ondersteuning.

<br>

- **Oorzaak**: wanneer het fout bericht een bericht bevat dat lijkt op ' gebruikers beheerder is vergrendeld in Ambari ' of ' niet gemachtigd: Ambari gebruikers naam of wacht woord is onjuist ', betekent dit dat de referenties voor HDInsight onjuist zijn of zijn verlopen.

- **Aanbeveling**: Corrigeer de referenties en implementeer de gekoppelde service opnieuw. Controleer eerst of de referenties werken op HDInsight door de cluster-URI in een browser te openen en u aan te melden. Als de referenties niet werken, kunt u ze opnieuw instellen op de Azure Portal.

<br>

- **Oorzaak**: wanneer het fout bericht een bericht bevat dat lijkt op ' 502-webserver heeft een ongeldig antwoord ontvangen terwijl deze als gateway of proxy server optreedt, wordt deze fout geretourneerd door de HDInsight-service.


- **Aanbeveling**: Bekijk de documentatie voor het oplossen van problemen met Azure HDInsight, bijvoorbeeld https://hdinsight.github.io/ambari/ambari-ui-502-error.html , https://hdinsight.github.io/spark/spark-thriftserver-errors.htmlhttps://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502.
                  

<br>

- **Oorzaak**: Wanneer fout bericht een bericht bevat dat lijkt op ' kan de aanvraag voor de verzend taak niet verwerken omdat de Templeton-service is bezig met te veel verzend taak aanvragen ' of ' queue root. joblauncher heeft al 500 toepassingen, waardoor het indienen van de toepassing niet kan worden geaccepteerd. Dit betekent dat er te veel taken tegelijkertijd worden verzonden naar HDInsight.

- **Aanbeveling**: overweeg het aantal gelijktijdige taken dat naar HDInsight moet worden verzonden te beperken. Raadpleeg Data Factory gelijktijdige activiteit als de taken worden verzonden door dezelfde activiteit. Wijzig de triggers zodat de gelijktijdige pijplijn uitvoeringen in de loop van de tijd worden verdeeld. Raadpleeg de HDInsight-documentatie om Templeton. parallellism. job. Submit aan te passen. dit wordt voorgesteld door de fout.


### <a name="error-code--2301"></a>Fout code: 2301

- **Bericht**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Oorzaak**: HDInsight-cluster of-service heeft problemen.


- **Aanbeveling**: deze fout treedt op wanneer ADF geen reactie krijgt van HDInsight-cluster bij het ophalen van de status van de actieve taak. Dit kan problemen veroorzaken op het cluster zelf of de HDInsight-service heeft mogelijk een storing. Raadpleeg de documentatie voor het oplossen van problemen met HDInsight op https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide of neem contact op met de ondersteuning voor verdere ondersteuning.
                


### <a name="error-code--2302"></a>Fout code: 2302

- **Bericht**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Oorzaak**: de taak is verzonden naar het HDI-cluster en is daar mislukt.

- **Aanbeveling**: Volg de koppeling tussen garens in de uitvoer van de uitvoering van de activiteit en zoek naar de fouten in HDI-uitvoer. Neem zo nodig contact op met het HDInsight-team voor ondersteuning.


### <a name="error-code--2303"></a>Fout code: 2303

- **Bericht**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Oorzaak**: de taak is verzonden naar het HDI-cluster en is daar mislukt.

- **Aanbeveling**: Volg de koppeling tussen garens in de uitvoer van de uitvoering van de activiteit en zoek naar de fouten in HDI-uitvoer. Probeer het opnieuw of neem contact op met het HDInsight-team als dat nodig is.


### <a name="error-code--2304"></a>Fout code: 2304

- **Bericht**: `MSI authentication is not supported on storages for HDI activities.`

- **Oorzaak**: de gekoppelde opslag service (s) die worden gebruikt in de HDI gekoppelde service of HDI-activiteit worden geconfigureerd met MSI-verificatie, wat niet wordt ondersteund.

- **Aanbeveling**: geef volledige verbindings reeksen op voor opslag accounts die worden gebruikt in de HDI-gekoppelde service of de HDI-activiteit.


### <a name="error-code--2305"></a>Fout code: 2305

- **Bericht**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Oorzaak**: de verbindings gegevens voor het HDI-cluster zijn onjuist, de opgegeven gebruiker heeft geen machtigingen om de vereiste actie uit te voeren of er zijn problemen met de HDInsight-service die reageren op aanvragen van ADF.

- **Aanbeveling**: Controleer of de gebruikers gegevens juist zijn. Controleer ook of de Ambari-gebruikers interface voor het HDI-cluster kan worden geopend in een browser van de VM waarbij IR wordt geïnstalleerd bij een zelf-hostende IR of kan worden geopend vanaf elke computer in het geval Azure IR.


### <a name="error-code--2306"></a>Fout code: 2306

- **Bericht**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Oorzaak**: de JSON die voor de script actie is gegeven, is ongeldig.


- **Aanbeveling**: het fout bericht zou het probleem moeten kunnen identificeren. Corrigeer de JSON-configuratie en probeer het opnieuw. Raadpleeg https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service voor meer informatie.
                


### <a name="error-code--2310"></a>Fout code: 2310

- **Bericht**: `Failed to submit Spark job. Error: '%message;'`

- **Oorzaak**: ADF heeft geprobeerd een batch te maken op een Spark-cluster met behulp van de LIVY-API (livy/batch), maar er is een fout opgetreden.

- **Aanbeveling**: Voer het fout bericht uit om het probleem op te lossen. Als er niet voldoende informatie is om het probleem op te lossen, neemt u contact op met het HDI-team en geeft u de batch-ID en taak-ID op die u kunt vinden op de pagina activiteit uitvoer uitvoeren op de ADF-bewaking.


### <a name="error-code--2312"></a>Fout code: 2312

- **Bericht**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Oorzaak**: de taak is mislukt in het HDInsight Spark-cluster.

- **Aanbeveling**: Volg de koppelingen in de uitvoer van de activiteit uitvoeren in de ADF-bewakings pagina om problemen met het uitvoeren van een HDInsight Spark-cluster op te lossen. Neem contact op met het ondersteunings team van HDInsight voor verdere ondersteuning.


### <a name="error-code--2313"></a>Fout code: 2313

- **Bericht**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Oorzaak**: de batch is verwijderd uit het HDInsight Spark-cluster.

- **Aanbeveling**: problemen met batches op het HDInsight Spark-cluster oplossen. Neem contact op met HDInsight-ondersteuning voor verdere ondersteuning. 


### <a name="error-code--2328"></a>Fout code: 2328

- **Bericht**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**: `The error message should show the details of what went wrong.`

- **Aanbeveling**: het fout bericht moet u helpen het probleem op te lossen.


### <a name="error-code--2329"></a>Fout code: 2329

- **Bericht**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**: in het fout bericht worden de details weer gegeven van wat er verkeerd is gegaan.

- **Aanbeveling**: het fout bericht moet u helpen het probleem op te lossen.


### <a name="error-code--2331"></a>Fout code: 2331

- **Bericht**: `The file path should not be null or empty.`

- **Oorzaak**: het opgegeven bestandspad is leeg.

- **Aanbeveling**: Geef een pad op voor een bestand dat zich bevindt.


### <a name="error-code--2340"></a>Fout code: 2340

- **Bericht**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Oorzaak**: de gekoppelde service HDInsightOnDemand biedt geen ondersteuning voor uitvoering via SelfHosted IR.

- **Aanbeveling**: selecteer een Azure IR en probeer het opnieuw.


### <a name="error-code--2341"></a>Fout code: 2341

- **Bericht**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Oorzaak**: de gegeven URL heeft niet de juiste indeling.

- **Aanbeveling**: Corrigeer de cluster-URL en probeer het opnieuw.


### <a name="error-code--2342"></a>Fout code: 2342

- **Bericht**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Oorzaak**: de verstrekte referenties zijn onjuist voor het cluster, of er is een netwerk configuratie of verbindings probleem opgetreden of de IR ondervindt problemen bij het verbinding maken met het cluster.

- **Aanbeveling**:  
      1. Controleer of de referenties juist zijn door de Ambari-gebruikers interface van het HDInsight-cluster in een browser te openen.
      2. Als het cluster zich in VNet bevindt en zelf-hostende IR wordt gebruikt, moet de HDI-URL de privé-URL zijn in VNets, dat wil zeggen: '-int ' na de cluster naam. Bijvoorbeeld 'https://mycluster.azurehdinsight.net/ ' moet worden gewijzigd in 'https://mycluster-int.azurehdinsight.net/ '.
      2. Als het cluster zich in VNet bevindt, wordt zelf-hostende IR gebruikt en wordt de persoonlijke URL gebruikt. de verbinding is dan nog steeds mislukt. vervolgens wordt de virtuele machine waarop de IR is geïnstalleerd problemen ondervonden met het verbinding maken met de HDI. Maak verbinding met de virtuele machine waarop IR is geïnstalleerd en open de Ambari-gebruikers interface in een browser. Gebruik de persoonlijke URL voor het cluster. Deze verbinding moet werken vanuit de browser. Als dat niet het geval is, neemt u contact op met het HDInsight-ondersteunings team.
      3. Als zelf-hostende IR niet wordt gebruikt, moet het HDI-cluster openbaar toegankelijk zijn. Open de Ambari-gebruikers interface in een browser en zorg ervoor dat deze wordt geopend. Als er problemen zijn met het cluster of de services erop, neemt u contact op met het HDInsight-ondersteunings team voor hulp.
      In het algemeen moet de HDI-cluster-URL die wordt gebruikt in de aan ADF gekoppelde service toegankelijk zijn voor ADF IR (zelf-hostend of Azure), zodat de test verbinding kan worden door gegeven en voor uitvoeringen werkt. Dit kan eenvoudig worden geverifieerd door de URL te openen vanuit een browser van een virtuele machine of een open bare computer.
    


### <a name="error-code--2343"></a>Fout code: 2343

- **Bericht**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Oorzaak**: de gebruikers naam of het wacht woord is leeg.

- **Aanbeveling**: Geef de juiste referenties op om verbinding te maken met HDI en probeer het opnieuw.


### <a name="error-code--2345"></a>Fout code: 2345

- **Bericht**: `Failed to read the content of the hive script. Error: '%message;'`

- **Oorzaak**: het script bestand bestaat niet of de ADF kan geen verbinding maken met de locatie van het script.

- **Aanbeveling**: Controleer of het script bestaat en of de bijbehorende gekoppelde service de juiste referenties heeft voor de verbinding.


### <a name="error-code--2346"></a>Fout code: 2346

- **Bericht**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Oorzaak**: ADF heeft geprobeerd een ODBC-verbinding te maken met het HDI-cluster en is mislukt met de fout.

- **Aanbeveling**: het fout bericht en de fout code moeten helpen bij het oplossen van problemen met de ODBC-verbindings fouten. Als ze niet voldoende zijn om het probleem op te lossen, neemt u contact op met het Azure HDInsight-team voor ondersteuning.


### <a name="error-code--2347"></a>Fout code: 2347

- **Bericht**: `Hive execution through ODBC failed with error message '%message;'.`

- **Oorzaak**: de ADF heeft het Hive-script verzonden voor uitvoering naar het HDI-cluster via een ODBC-verbinding en het script is mislukt op HDI.

- **Aanbeveling**: de uitvoering van het Hive-script is mislukt op het HDI-cluster en het fout bericht en de fout code moeten helpen bij het oplossen van problemen. Als ze niet voldoende zijn om het probleem op te lossen, neemt u contact op met het Azure HDInsight-team voor ondersteuning.


### <a name="error-code--2348"></a>Fout code: 2348

- **Bericht**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Oorzaak**: de eigenschappen van de gekoppelde opslag service zijn niet juist ingesteld.

- **Aanbeveling**: alleen volledige verbindings reeksen worden ondersteund in de hoofd opslag gekoppelde service voor HDI-activiteiten. Zorg ervoor dat u geen MSI-verificatie of-toepassingen gebruikt.


### <a name="error-code--2350"></a>Fout code: 2350

- **Bericht**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Oorzaak**: de referenties die zijn gegeven om verbinding te maken met de opslag waar de bestanden zich bevinden, zijn onjuist of de bestanden bestaan er niet.

- **Aanbeveling**: deze fout treedt op wanneer ADF voorbereidende stappen voor HDI-activiteiten uitvoert. Er wordt geprobeerd om bestanden te kopiëren naar de hoofd opslag voordat de taak naar HDI wordt verzonden. Controleer of de bestanden op de gegeven locatie aanwezig zijn. de opslag verbinding is juist. ADF HDI-activiteiten bieden geen ondersteuning voor MSI-verificatie voor opslag accounts die betrekking hebben op HDI-activiteiten. Zorg er dus voor dat deze gekoppelde services volledige sleutels hebben of Azure Key Vault gebruiken.


### <a name="error-code--2351"></a>Fout code: 2351

- **Bericht**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Oorzaak**: het bestand bestaat niet op het opgegeven pad.

- **Aanbeveling**: Controleer of het bestand daad werkelijk bestaat en of de gekoppelde service met verbindings informatie die verwijst naar dit bestand, de juiste referenties heeft.


### <a name="error-code--2352"></a>Fout code: 2352

- **Bericht**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Oorzaak**: de eigenschappen van de gekoppelde service van het bestands archief zijn niet juist ingesteld.

- **Aanbeveling**: Controleer of de eigenschappen van de gekoppelde File Storage-service correct zijn geconfigureerd.


### <a name="error-code--2353"></a>Fout code: 2353

- **Bericht**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Oorzaak**: de eigenschappen van de gekoppelde service van de script opslag zijn niet juist ingesteld.

- **Aanbeveling**: Controleer of de eigenschappen van de gekoppelde script opslag-service correct zijn geconfigureerd.


### <a name="error-code--2354"></a>Fout code: 2354

- **Bericht**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Oorzaak**: het gekoppelde opslag service type wordt niet ondersteund door de activiteit.

- **Aanbeveling**: Controleer of de geselecteerde gekoppelde service een van de ondersteunde typen voor de activiteit heeft. HDI-activiteiten bieden ondersteuning voor AzureBlobStorage-en AzureBlobFSStorage gekoppelde services.


### <a name="error-code--2355"></a>Fout code: 2355

- **Bericht**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Oorzaak**: de gegeven voor commandEnvironment is onjuist.

- **Aanbeveling**:  
      Controleer of de gegeven waarde er ongeveer als volgt uitziet: \"commandEnvironment\": [\"variabelenaam = variableValue\"] en elke variabele wordt slechts één keer in de lijst weer gegeven.
    


### <a name="error-code--2356"></a>Fout code: 2356

- **Bericht**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Oorzaak**: de variabele is twee keer in de commandEnvironment gegeven.

- **Aanbeveling**:  
      Controleer of de gegeven waarde er ongeveer als volgt uitziet: \"commandEnvironment\": [\"variabelenaam = variableValue\"] en elke variabele wordt slechts één keer in de lijst weer gegeven.
    


### <a name="error-code--2357"></a>Fout code: 2357

- **Bericht**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Oorzaak**: de gegeven referenties zijn onjuist.

- **Aanbeveling**: Controleer de verbindings gegevens in de gekoppelde service ADLS gen 1 en zorg ervoor dat de test verbinding slaagt.


### <a name="error-code--2358"></a>Fout code: 2358

- **Bericht**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Oorzaak**: de opgegeven waarde voor de vereiste eigenschap TimeToLive heeft een ongeldige indeling. 

- **Aanbeveling**: werk de waarde bij in het voorgestelde bereik en probeer het opnieuw.


### <a name="error-code--2359"></a>Fout code: 2359

- **Bericht**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Oorzaak**: de gegeven waarde voor de eigenschap roles is ongeldig.

- **Aanbeveling**: werk de waarde bij om een van de suggesties te zijn en probeer het opnieuw.


### <a name="error-code--2360"></a>Fout code: 2360

- **Bericht**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Oorzaak**: de door gegeven Connection String voor de HCatalogLinkedService is ongeldig.

- **Aanbeveling**: werk de waarde bij naar een juiste Azure SQL-Connection String en probeer het opnieuw.


### <a name="error-code--2361"></a>Fout code: 2361

- **Bericht**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Oorzaak**: het maken van het cluster is mislukt en de ADF heeft geen fout weer gegeven van de HDInsight-service.

- **Aanbeveling**: Open de Azure Portal en probeer de HDI-resource te vinden met de naam die u hebt gekregen en controleer de inrichtings status. Neem contact op met het HDInsight-ondersteunings team voor verdere ondersteuning.


### <a name="error-code--2362"></a>Fout code: 2362

- **Bericht**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Oorzaak**: de gegeven extra opslag is geen Azure Blob-opslag.

- **Aanbeveling**: Geef Azure Blob Storage-account op als extra opslag ruimte voor de gekoppelde service van HDInsight op aanvraag.



## <a name="web-activity"></a>Webactiviteit

### <a name="error-code--2128"></a>Fout code: 2128

- **Bericht**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Oorzaak**: netwerk connectiviteit, DNS-fout, validatie van server certificaat of time-out.

- **Aanbeveling**: Controleer of het eind punt dat u probeert te raken reageert op aanvragen. U kunt hulpprogram ma's zoals Fiddler/postman gebruiken.


### <a name="error-code--2108"></a>Fout code: 2108

- **Bericht**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Oorzaak**: de aanvraag is mislukt vanwege een onderliggend probleem, zoals een netwerk verbinding, een DNS-fout, een validatie van het server certificaat of een time-out.

- **Aanbeveling**: gebruik Fiddler/postman om de aanvraag te valideren.
<br>


#### <a name="more-details"></a>Meer informatie
Fiddler gebruiken om een HTTP-sessie van de bewaakte webtoepassing te maken:

1. Down load, installeer en open [Fiddler](https://www.telerik.com/download/fiddler).

1. Als uw webtoepassing gebruikmaakt van HTTPS, gaat u naar **Hulpprogram ma's** > **Fiddler opties** > **https**. Selecteer **https-verbinding** maken en **versleutelen van HTTPS-verkeer**.

   ![Opties voor Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Als uw toepassing gebruikmaakt van SSL-certificaten, voegt u het Fiddler-certificaat toe aan uw apparaat. Ga naar **Hulpprogram ma's** > **Fiddler opties** > **https** > **acties** > **basis certificaat exporteren naar het bureau blad**.

1. Schakel het vastleggen uit door naar **File** > **Capture-verkeer**te gaan. Of druk op **F12**.

1. Wis de cache van uw browser zodat alle items in de cache worden verwijderd en opnieuw moet worden gedownload.

1. Een aanvraag maken:

   1. Selecteer het tabblad **Composer** .

   1. Stel de HTTP-methode en URL in.
   
   1. Voeg indien nodig kopteksten en een aanvraag tekst toe.

   1. Selecteer **Uitvoeren**.

1. Schakel het vastleggen van verkeer in en voltooi de problematische trans actie op de pagina.

1. Ga naar **bestand** >  > **alle sessies**op te **slaan** .

Zie [aan de slag met Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)


            

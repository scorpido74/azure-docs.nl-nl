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
ms.openlocfilehash: ed466b072a771c3aa288a96fa4a0037c31b875f9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091998"
---
# <a name="troubleshoot-azure-data-factory"></a>Problemen met Azure Data Factory oplossen

In dit artikel worden algemene probleemoplossings methoden besproken voor externe controle activiteiten in Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Connector-en kopieer activiteit

Raadpleeg [problemen met Azure Data Factory connectors oplossen](connector-troubleshoot-guide.md)voor problemen met de connector, bijvoorbeeld wanneer er een fout optreedt.

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Fout code:  3200

- **Bericht**: Fout 403.

- **Oorzaak**:`The Databricks access token has expired.`

- **Aanbeveling**: Het toegangs token voor Azure Databricks is standaard 90 dagen geldig. Maak een nieuw token en werk de gekoppelde service bij.


### <a name="error-code--3201"></a>Fout code:  3201

- **Bericht**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Oorzaak**:`Bad authoring: Notebook path not specified correctly.`

- **Aanbeveling**: Geef het pad naar de notebook op in de Databricks-activiteit.

<br/>

- **Bericht**:`Cluster   ... does not exist.`

- **Oorzaak**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Aanbeveling**: Controleer of het Databricks-cluster bestaat.

<br/>

- **Bericht**:`Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Oorzaak**:`Bad authoring.`

- **Aanbeveling**: Geef een absoluut pad op voor werk ruimte-adresserings `dbfs:/folder/subfolder/foo.py` schema's of voor bestanden die zijn opgeslagen in Databricks File System.

<br/>

- **Bericht**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Oorzaak**:`Bad authoring.`

- **Aanbeveling**: Controleer de [definitie van de gekoppelde service](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Bericht**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Oorzaak**:`Bad authoring.`

- **Aanbeveling**: Controleer de [definitie van de gekoppelde service](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Bericht**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Oorzaak**:`Bad authoring.`

- **Aanbeveling**: Raadpleeg het fout bericht. 

<br/>

### <a name="error-code--3202"></a>Fout code:  3202

- **Bericht**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Oorzaak**:`Too many Databricks runs in an hour.`

- **Aanbeveling**: Controleer alle pijp lijnen die gebruikmaken van deze Databricks-werk ruimte voor het aanmaak tempo van de taak.  Als pijp lijnen te veel Databricks-runs worden uitgevoerd, migreert u enkele pijp lijnen naar een nieuwe werk ruimte.

<br/>

- **Bericht**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Oorzaak**:`Authoring error: No value provided for the parameter.`

- **Aanbeveling**: Inspecteer de JSON van de pijp lijn en zorg ervoor dat alle para meters in de baseParameters-notebook een niet-lege waarde opgeven.

<br/>

- **Bericht**: `User: `SimpleUserContext {gebruikersID =..., naam =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Oorzaak**: De gebruiker die het toegangs token heeft gegenereerd, heeft geen toegang tot het Databricks-cluster dat is opgegeven in de gekoppelde service.

- **Aanbeveling**: Zorg ervoor dat de gebruiker over de vereiste machtigingen beschikt in de werk ruimte.


### <a name="error-code--3203"></a>Fout code:  3203

- **Bericht**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Oorzaak**: Het cluster is beëindigd. Voor interactieve clusters kan dit een race voorwaarde zijn.

- **Aanbeveling**: De beste manier om deze fout te vermijden, is door taak clusters te gebruiken.


### <a name="error-code--3204"></a>Fout code:  3204

- **Bericht**:`Job execution failed.`

- **Oorzaak**:  Fout berichten geven verschillende problemen aan, zoals een onverwachte cluster status of een specifieke activiteit. Er wordt meestal helemaal geen fout bericht weer gegeven. 

- **Aanbeveling**: N/A


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

De volgende tabel is van toepassing op U-SQL.


### <a name="error-code--2709"></a>Fout code:  2709

- **Bericht**:`The access token is from the wrong tenant.`

- **Oorzaak**:  Onjuiste Azure Active Directory-Tenant (Azure AD).

- **Aanbeveling**: Onjuiste Azure Active Directory-Tenant (Azure AD).

<br/>

- **Bericht**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Oorzaak**:  Deze fout wordt veroorzaakt door het beperken van Data Lake Analytics.

- **Aanbeveling**: Verminder het aantal verzonden taken naar Data Lake Analytics door Data Factory triggers en gelijktijdigheids instellingen voor activiteiten te wijzigen. Of verg root de limieten voor Data Lake Analytics.

<br/>

- **Bericht**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Oorzaak**:  Deze fout wordt veroorzaakt door het beperken van Data Lake Analytics. 

- **Aanbeveling**: Verminder het aantal verzonden taken naar Data Lake Analytics door Data Factory triggers en gelijktijdigheids instellingen voor activiteiten te wijzigen. Of verg root de limieten voor Data Lake Analytics.


### <a name="error-code--2705"></a>Fout code:  2705

- **Bericht**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Oorzaak**:  De service-principal of het certificaat heeft geen toegang tot het bestand in de opslag.

- **Aanbeveling**: Zorg ervoor dat de service-principal of het certificaat dat de gebruiker biedt voor Data Lake Analytics taken toegang heeft tot het Data Lake Analytics account en de standaard Data Lake Storage instantie van de hoofdmap.


### <a name="error-code--2711"></a>Fout code:  2711

- **Bericht**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Oorzaak**:  De service-principal of het certificaat heeft geen toegang tot het bestand in de opslag.

- **Aanbeveling**: Zorg ervoor dat de service-principal of het certificaat dat de gebruiker biedt voor Data Lake Analytics taken toegang heeft tot het Data Lake Analytics account en de standaard Data Lake Storage instantie van de hoofdmap.

<br/>

- **Bericht**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Oorzaak**:  Het pad naar het U-SQL-bestand is onjuist of de referenties van de gekoppelde service hebben geen toegang.

- **Aanbeveling**: Controleer het pad en de referenties die zijn opgenomen in de gekoppelde service.


### <a name="error-code--2704"></a>Fout code:  2704

- **Bericht**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Oorzaak**:  De service-principal of het certificaat heeft geen toegang tot het bestand in de opslag.

- **Aanbeveling**: Zorg ervoor dat de service-principal of het certificaat dat de gebruiker biedt voor Data Lake Analytics taken toegang heeft tot het Data Lake Analytics account en de standaard Data Lake Storage instantie van de hoofdmap.


### <a name="error-code--2707"></a>Fout code:  2707

- **Bericht**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Oorzaak**:  Het Data Lake Analytics-account in de gekoppelde service is onjuist.

- **Aanbeveling**: Controleer of het juiste account is ingesteld.


### <a name="error-code--2703"></a>Fout code:  2703

- **Bericht**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Oorzaak**:  De fout is van Data Lake Analytics. 

- **Aanbeveling**: Een fout zoals in het voor beeld betekent dat de taak is verzonden naar Data Lake Analytics en dat het script is mislukt. Onderzoek in Data Lake Analytics. Ga in de portal naar het Data Lake Analytics-account en zoek de taak met behulp van de Data Factory-activiteit run ID (niet de pijplijn run-ID). De taak bevat meer informatie over de fout en helpt u bij het oplossen van problemen. Als de oplossing niet duidelijk is, neemt u contact op met het Data Lake Analytics ondersteunings team en geeft u de taak-URL op, die uw account naam en de taak-ID bevat.



## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Fout code:  3602

- **Bericht**:`Invalid HttpMethod: {method}.`

- **Oorzaak**: De HTTP-methode die in de activiteit lading is opgegeven, wordt niet ondersteund door Azure function-activiteit. 

- **Aanbeveling**: De HTTP-methoden die worden ondersteund, zijn plaatsen, posten, ophalen, verwijderen, opties, kop en TRACERing.


### <a name="error-code--3603"></a>Fout code:  3603

- **Bericht**:`Response content is not a valid JObject.`

- **Oorzaak**: De Azure-functie die is aangeroepen, heeft niet een JSON-nettolading geretourneerd in het antwoord. Azure function-activiteit in Data Factory ondersteunt alleen inhoud van een JSON-antwoord. 

- **Aanbeveling**: Werk de Azure-functie bij om een geldige JSON-nettolading te retour neren. Een functie kan bijvoorbeeld C# de volgende id `(ActionResult)new<OkObjectResult("{`\"retour neren\" \":\"123`}");`.


### <a name="error-code--3606"></a>Fout code:  3606

- **Bericht**:`Azure function activity missing function key.`

- **Oorzaak**: De definitie van de Azure function-activiteit is niet voltooid. 

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer-AzureFunction voor de eigenschap met de naam ' functionKey ' is.


### <a name="error-code--3607"></a>Fout code:  3607

- **Bericht**:`Azure function activity missing function name.`

- **Oorzaak**: De definitie van de Azure function-activiteit is niet voltooid. 

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureFunction de eigenschap ' functie naam ' heeft.


### <a name="error-code--3608"></a>Fout code:  3608

- **Bericht**:`Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Oorzaak**: De details van de Azure-functie in de activiteiten definitie zijn mogelijk onjuist. 

- **Aanbeveling**: Corrigeer de details van de Azure-functie en probeer het opnieuw.


### <a name="error-code--3609"></a>Fout code:  3609

- **Bericht**:`Azure function activity missing functionAppUrl.` 

- **Oorzaak**: De definitie van de Azure function-activiteit is niet voltooid. 

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer-AzureFunction voor de eigenschap met de naam ' functionAppUrl ' is.


### <a name="error-code--3610"></a>Fout code:  3610

- **Bericht**:`There was an error while calling endpoint.`

- **Oorzaak**: De functie-URL is mogelijk onjuist.

- **Aanbeveling**: Controleer of de waarde voor ' functionAppUrl ' in de JSON van de activiteit juist is en probeer het opnieuw.


### <a name="error-code--3611"></a>Fout code:  3611

- **Bericht**:`Azure function activity missing Method in JSON.` 

- **Oorzaak**: De definitie van de Azure function-activiteit is niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureFunction is voorzien van een eigenschap met de naam method.


### <a name="error-code--3612"></a>Fout code:  3612

- **Bericht**:`Azure function activity missing LinkedService definition in JSON.`

- **Oorzaak**: De definitie van de Azure function-activiteit is mogelijk niet voltooid.

- **Aanbeveling**: Controleer of de JSON-definitie van de invoer AzureFunction een gekoppelde service Details bevat.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Fout code:  4101

- **Bericht**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Oorzaak**: Ongeldige indeling of ontbrekende definitie van een eigenschap.

- **Aanbeveling**:  Controleer of de activiteit is gedefinieerd met de juiste gegevens.


### <a name="error-code--4110"></a>Fout code:  4110

- **Bericht**: Er ontbreekt een LinkedService definitie in de JSON van de AzureMLExecutePipeline-activiteit.

- **Oorzaak**: De definitie van de AzureMLExecutePipeline-activiteit is niet voltooid.

- **Aanbeveling**:  Controleer of de JSON-definitie van de input AzureMLExecutePipeline-activiteit gekoppelde service Details bevat.


### <a name="error-code--4111"></a>Fout code:  4111

- **Bericht**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Oorzaak**: Onjuiste definitie van activiteit.

- **Aanbeveling**:  Controleer of de JSON-definitie van de invoer-AzureMLExecutePipeline de juiste details van de gekoppelde service heeft.


### <a name="error-code--4112"></a>Fout code:  4112

- **Bericht**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Oorzaak**: Ongeldige indeling of ontbrekende definitie van een eigenschap.

- **Aanbeveling**:  Controleer of de definitie van de gekoppelde service de juiste gegevens bevat.


### <a name="error-code--4121"></a>Fout code:  4121

- **Bericht**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Oorzaak**: De referentie die wordt gebruikt voor toegang tot de Azure ML-service is verlopen.

- **Aanbeveling**:  Controleer of de referenties geldig zijn en probeer het opnieuw


### <a name="error-code--4122"></a>Fout code:  4122

- **Bericht**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Oorzaak**: De referenties die zijn opgenomen in de gekoppelde service van de AzureML-service zijn ongeldig of hebben geen machtiging voor de bewerking.

- **Aanbeveling**:  Controleer of de referenties in de gekoppelde service geldig zijn en toegang hebben tot de AzureML-service.


### <a name="error-code--4123"></a>Fout code:  4123

- **Bericht**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Oorzaak**:`Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Aanbeveling**:  Controleer de waarde van de activiteit eigenschappen die overeenkomt met de verwachte payload van de gepubliceerde Azure ML-pijp lijn die is opgegeven in de gekoppelde service.


### <a name="error-code--4124"></a>Fout code:  4124

- **Bericht**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Oorzaak**: Het gepubliceerde Azure ML pijp lijn-eind punt bestaat niet.

- **Aanbeveling**:  Controleer of het gepubliceerde Azure ML pijp lijn-eind punt dat is opgegeven in de gekoppelde service bestaat in de Azure ML-service.


### <a name="error-code--4125"></a>Fout code:  4125

- **Bericht**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Oorzaak**: Server fout op de Azure ML-service.

- **Aanbeveling**:  Probeer het later opnieuw. Neem contact op met het service team van Azure ML als het probleem blijft bestaan.


### <a name="error-code--4126"></a>Fout code:  4126

- **Bericht**:`AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Oorzaak**: Uitvoering van AzureML-pijp lijn mislukt.

- **Aanbeveling**:  Raadpleeg AzureMLService voor meer informatie over het vastleggen van fouten en het oplossen van de ML-pijp lijn


## <a name="custom"></a>Aanpassen

De volgende tabel is van toepassing op Azure Batch.


### <a name="error-code--2500"></a>Fout code:  2500

- **Bericht**:`Hit unexpected exception and execution failed.`

- **Oorzaak**:`Can't launch command, or the program returned an error code.`

- **Aanbeveling**:  Controleer of het uitvoer bare bestand bestaat. Als het programma is gestart, controleert u of *stdout. txt* en *stderr. txt* zijn geüpload naar het opslag account. Het is een goed idee om copious-Logboeken in uw code te verzenden voor fout opsporing.


### <a name="error-code--2501"></a>Fout code:  2501

- **Bericht**:`Cannot access user batch account; please check batch account settings.`

- **Oorzaak**: Onjuiste batch-toegangs sleutel of groeps naam.

- **Aanbeveling**: Controleer de naam van de groep en de batch toegangs sleutel in de gekoppelde service.


### <a name="error-code--2502"></a>Fout code:  2502

- **Bericht**:`Cannot access user storage account; please check storage account settings.`

- **Oorzaak**: De naam van het opslag account of de toegangs sleutel is onjuist.

- **Aanbeveling**: Controleer de naam van het opslag account en de toegangs sleutel in de gekoppelde service.


### <a name="error-code--2504"></a>Fout code:  2504

- **Bericht**:`Operation returned an invalid status code 'BadRequest'.` 

- **Oorzaak**: Er zijn te veel bestanden in de folderPath van de aangepaste activiteit. De totale grootte van de Resource files mag niet langer zijn dan 32.768 tekens.

- **Aanbeveling**: Verwijder overbodige bestanden. Of zip en voeg een unzip-opdracht toe om deze uit te pakken. Gebruik bijvoorbeeld`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Fout code:  2505

- **Bericht**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Oorzaak**: Aangepaste activiteiten bieden alleen ondersteuning voor opslag accounts die gebruikmaken van een toegangs sleutel.

- **Aanbeveling**: Raadpleeg de beschrijving van de fout.


### <a name="error-code--2507"></a>Fout code:  2507

- **Bericht**:`The folder path does not exist or is empty: ....`

- **Oorzaak**: Er bevinden zich geen bestanden in het opslag account op het opgegeven pad.

- **Aanbeveling**: Het mappad moet de uitvoer bare bestanden bevatten die u wilt uitvoeren.


### <a name="error-code--2508"></a>Fout code:  2508

- **Bericht**:`There are duplicate files in the resource folder.`

- **Oorzaak**: Meerdere bestanden met dezelfde naam bevinden zich in verschillende submappen van folderPath.

- **Aanbeveling**: Aangepaste activiteiten een mapstructuur afvlakken onder folderPath.  Als u de mappen structuur wilt behouden, moet u de bestanden zip en uitpakken in Azure Batch met behulp van een unzip opdracht. Gebruik bijvoorbeeld`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Fout code:  2509

- **Bericht**:`Batch   url ... is invalid; it must be in Uri format.` 

- **Oorzaak**: Batch-Url's moeten overeenkomen met`https://mybatchaccount.eastus.batch.azure.com`

- **Aanbeveling**: Raadpleeg de beschrijving van de fout.


### <a name="error-code--2510"></a>Fout code:  2510

- **Bericht**:`An   error occurred while sending the request.`

- **Oorzaak**: De batch-URL is ongeldig. 

- **Aanbeveling**: Controleer de batch-URL.


## <a name="hdinsight"></a>HDInsight

De volgende tabel is van toepassing op Spark-, Hive-, MapReduce-, Pig-en Hadoop-streaming.


### <a name="error-code--2300"></a>Fout code:  2300

- **Bericht**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Oorzaak**: De door gegeven URI van het cluster is ongeldig. 

- **Aanbeveling**:  Zorg ervoor dat het cluster niet is verwijderd en dat de gegeven URI juist is. Wanneer u de URI in een browser opent, ziet u de Ambari-gebruikers interface. Als het cluster zich in een virtueel netwerk bevindt, moet de URI de persoonlijke URI zijn. Als u het bestand wilt openen, gebruikt u een virtuele machine die deel uitmaakt van hetzelfde virtuele netwerk. Zie [Direct Connect to Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)(Engelstalig) voor meer informatie.

<br/>

- **Bericht**:`Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Oorzaak**: Er is een time-out opgetreden voor de taak verzending. 

- **Aanbeveling**: Het probleem kan algemeen HDInsight-connectiviteit of een netwerk verbinding zijn. Controleer eerst of de gebruikers interface van HDInsight Ambari beschikbaar is vanuit elke browser. Controleer of uw referenties nog geldig zijn. Als u gebruikmaakt van zelf-hostende Integrated Runtime (IR), moet u ervoor zorgen dat u dit doet vanaf de virtuele machine of computer waarop de zelf-hostende IR is geïnstalleerd. Probeer de taak vervolgens opnieuw uit Data Factory te verzenden. Als het nog steeds niet lukt, neemt u contact op met het Data Factory-team voor ondersteuning.


- **Bericht**:`Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Oorzaak**: De referenties voor HDInsight zijn onjuist of verlopen.

- **Aanbeveling**: Corrigeer de referenties en implementeer de gekoppelde service opnieuw. Controleer eerst of de referenties werken op HDInsight door de cluster-URI in een browser te openen en u aan te melden. Als de referenties niet werken, kunt u ze opnieuw instellen op de Azure Portal.

<br/>

- **Bericht**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Oorzaak**: Deze fout is van HDInsight.

- **Aanbeveling**: Deze fout is afkomstig uit het HDInsight-cluster. Zie Ambari-fout in [UI 502](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 fouten bij het maken van verbinding met Spark Thrift server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502-fouten bij het verbinden met Spark Thrift server en het](https://hdinsight.github.io/spark/spark-thriftserver-errors.html) [oplossen van problemen met ongeldige gateway fouten in Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)voor meer informatie.

<br/>

- **Bericht**:`Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Oorzaak**: Op hetzelfde moment worden er te veel taken verzonden naar HDInsight.

- **Aanbeveling**: Overweeg het aantal gelijktijdige taken dat naar HDInsight moet worden verzonden te beperken. Raadpleeg Data Factory gelijktijdige activiteit als de taken worden verzonden door dezelfde activiteit. Wijzig de triggers zodat de gelijktijdige pijplijn uitvoeringen in de loop van de tijd worden verdeeld. Raadpleeg de HDInsight-documentatie om `templeton.parallellism.job.submit` de fout te corrigeren.


### <a name="error-code--2303"></a>Fout code:  2303

- **Bericht**:`Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Oorzaak**: De taak is verzonden naar HDInsight en is mislukt op HDInsight.

- **Aanbeveling**: De taak is verzonden naar HDInsight. Het cluster is mislukt. Open de taak en de logboeken in de gebruikers interface van HDInsight Ambari of open het bestand uit de opslag als het fout bericht wordt voorgesteld. Het bestand bevat de fout Details.


### <a name="error-code--2310"></a>Fout code:  2310

- **Bericht**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Oorzaak**: De door gegeven URI van het cluster is ongeldig. 

- **Aanbeveling**:  Zorg ervoor dat het cluster niet is verwijderd en dat de gegeven URI juist is. Wanneer u de URI in een browser opent, ziet u de Ambari-gebruikers interface. Als het cluster zich in een virtueel netwerk bevindt, moet de URI de persoonlijke URI zijn. Als u het bestand wilt openen, gebruikt u een virtuele machine die deel uitmaakt van hetzelfde virtuele netwerk. Zie [Direct Connect to Apache Hadoop Services](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)(Engelstalig) voor meer informatie.

<br/>

- **Bericht**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Oorzaak**: Deze fout is van HDInsight.

- **Aanbeveling**: Deze fout is afkomstig uit het HDInsight-cluster. Zie Ambari-fout in [UI 502](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 fouten bij het maken van verbinding met Spark Thrift server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502-fouten bij het verbinden met Spark Thrift server en het](https://hdinsight.github.io/spark/spark-thriftserver-errors.html) [oplossen van problemen met ongeldige gateway fouten in Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)voor meer informatie.

<br/>

- **Bericht**:`java.lang.NullPointerException`

- **Oorzaak**: Deze fout treedt op wanneer de taak wordt verzonden naar een Spark-cluster. 

- **Aanbeveling**: Deze uitzonde ring komt van HDInsight. Hiermee wordt het werkelijke probleem verborgen. Neem contact op met het HDInsight-team voor ondersteuning. Geef de cluster naam en het tijds bereik voor het uitvoeren van de activiteit op.


### <a name="error-code--2347"></a>Fout code:  2347

- **Bericht**:`Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Oorzaak**: De taak is verzonden naar HDInsight en is mislukt op HDInsight.

- **Aanbeveling**: De taak is verzonden naar HDInsight. Het cluster is mislukt. Open de taak en de logboeken in de gebruikers interface van HDInsight Ambari of open het bestand uit de opslag als het fout bericht wordt voorgesteld. Het bestand bevat de fout Details.


### <a name="error-code--2328"></a>Fout code:  2328

- **Bericht**:`Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Oorzaak**: Deze fout treedt op in HDInsight op aanvraag.

- **Aanbeveling**: Deze fout is afkomstig van de HDInsight-service wanneer het HDInsight-inrichten mislukt. Neem contact op met het HDInsight-team en geef de naam op van het cluster op aanvraag.



## <a name="web-activity"></a>Web Activity

### <a name="error-code--2108"></a>Fout code:  2108

- **Bericht**:`Invalid HttpMethod: '...'.`

- **Oorzaak**: Webactiviteit biedt geen ondersteuning voor de HTTP-methode die is opgegeven in de nettolading van de activiteit.

- **Aanbeveling**:  De ondersteunde HTTP-methoden zijn plaatsen, plaatsen, ophalen en verwijderen.

<br/>

- **Bericht**:`Invalid Server Error 500.`

- **Oorzaak**: Interne fout op het eind punt.

- **Aanbeveling**:  Gebruik Fiddler of postman om de functionaliteit van de URL te controleren.

<br/>

- **Bericht**:`Unauthorized 401.`

- **Oorzaak**: Ontbrekende geldige verificatie voor de aanvraag.

- **Aanbeveling**:  Het token is mogelijk verlopen. Geef een geldige verificatie methode op. Gebruik Fiddler of postman om de functionaliteit van de URL te controleren.

<br/>

- **Bericht**:`Forbidden 403.`

- **Oorzaak**: De vereiste machtigingen ontbreken.

- **Aanbeveling**:  Controleer de gebruikers machtigingen voor de toegang tot de resource. Gebruik Fiddler of postman om de functionaliteit van de URL te controleren.

<br/>

- **Bericht**:`Bad Request 400.`

- **Oorzaak**: Ongeldige HTTP-aanvraag.

- **Aanbeveling**:   Controleer de URL, het werk woord en de hoofd tekst van de aanvraag. Gebruik Fiddler of postman om de aanvraag te valideren.

<br/>

- **Bericht**:`Not found 404.` 

- **Oorzaak**: De resource is niet gevonden.   

- **Aanbeveling**:  Gebruik Fiddler of postman om de aanvraag te valideren.

<br/>

- **Bericht**:`Service unavailable.`

- **Oorzaak**: De service is niet beschikbaar.

- **Aanbeveling**:  Gebruik Fiddler of postman om de aanvraag te valideren.

<br/>

- **Bericht**:`Unsupported Media Type.`

- **Oorzaak**: Het inhouds type komt niet overeen met de hoofd tekst van de webactiviteit.

- **Aanbeveling**:  Geef het inhouds type op dat overeenkomt met de indeling van de nettolading. Gebruik Fiddler of postman om de aanvraag te valideren.

<br/>

- **Bericht**:`The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Oorzaak**: De resource is niet beschikbaar. 

- **Aanbeveling**:  Gebruik Fiddler of postman om het eind punt te controleren.

<br/>

- **Bericht**:`The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Oorzaak**: Er is een onjuiste methode voor webactiviteiten opgegeven in de aanvraag.

- **Aanbeveling**:  Gebruik Fiddler of postman om het eind punt te controleren.

<br/>

- **Bericht**:`invalid_payload`

- **Oorzaak**: De hoofd tekst van de webactiviteit is onjuist.

- **Aanbeveling**:  Gebruik Fiddler of postman om het eind punt te controleren.


### <a name="error-code--2208"></a>Fout code:  2208

- **Bericht**:`Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Oorzaak**: De doel service heeft de status mislukt geretourneerd.

- **Aanbeveling**:  Gebruik Fiddler/postman om de aanvraag te valideren.


### <a name="error-code--2308"></a>Fout code:  2308

- **Bericht**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Oorzaak**: Er kunnen meerdere oorzaken voor deze fout zijn, zoals netwerk connectiviteit, DNS-fout, validatie van server certificaten of time-out.

- **Aanbeveling**:  Gebruik Fiddler/postman om de aanvraag te valideren.


Fiddler gebruiken om een HTTP-sessie van de bewaakte webtoepassing te maken:

1. Down load, installeer en open [Fiddler](https://www.telerik.com/download/fiddler).

1. Als uw webtoepassing gebruikmaakt van HTTPS, gaat u naar **hulpprogram ma's** > **Fiddler opties** > **https**. Selecteer **https-verbinding** maken en versleutelen van **HTTPS-verkeer**. 
   
   ![Opties voor Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Als uw toepassing gebruikmaakt van SSL-certificaten, voegt u het Fiddler-certificaat toe aan uw apparaat. Ga naar **hulpprogram ma's** > **Fiddler opties** > **https** > actiesbasis > **certificaat exporteren naar bureau blad**.

1. Vastleggen uitschakelen door te gaan naar het **bestand** > **Capture-verkeer**. Of druk op **F12**.

1. Wis de cache van uw browser zodat alle items in de cache worden verwijderd en opnieuw moet worden gedownload.

1. Een aanvraag maken: 

   a. Selecteer het tabblad Composer.

   b. Stel de HTTP-methode en URL in.

   c. Voeg indien nodig kopteksten en een aanvraag tekst toe.

   d. Selecteer **Uitvoeren**.

9. Schakel het vastleggen van verkeer in en voltooi de problematische trans actie op de pagina.

10. Ga naar **bestand** > **alle sessies** **Opslaan** > .

Zie [aan de slag met Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)




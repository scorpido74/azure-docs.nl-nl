---
title: Problemen met Azure Data Factory oplossen | Microsoft Documenten
description: Meer informatie over het oplossen van externe controleactiviteiten in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: e284060893e00ed7459edd0d1a03075c813dc5b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065379"
---
# <a name="troubleshoot-azure-data-factory"></a>Problemen met Azure Data Factory oplossen

In dit artikel worden veelvoorkomende probleemoplossingsmethoden voor externe controleactiviteiten in Azure Data Factory onderzocht.

## <a name="connector-and-copy-activity"></a>Activiteit connectoren en kopiëren

Raadpleeg Azure [Data Factory Connectors](connector-troubleshoot-guide.md)voor verbindingsproblemen zoals het voorkomen van fouten met kopieeractiviteit.
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Foutcode: 3200

- **Bericht:** Fout 403.

- **Oorzaak**:`The Databricks access token has expired.`

- **Aanbeveling:** Standaard is het Azure Databricks access-token 90 dagen geldig. Maak een nieuw token en werk de gekoppelde service bij.


### <a name="error-code--3201"></a>Foutcode: 3201

- **Bericht**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Oorzaak**:`Bad authoring: Notebook path not specified correctly.`

- **Aanbeveling:** Geef het notitieblokpad op in de activiteit Databricks.

<br/>  

- **Bericht**:`Cluster... does not exist.`

- **Oorzaak**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Aanbeveling:** controleer of het cluster Databricks bestaat.

<br/>  

- **Bericht**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Oorzaak**:`Bad authoring.`

- **Aanbeveling:** Geef absolute paden op voor werkruimte-adresseringsschema's of `dbfs:/folder/subfolder/foo.py` voor bestanden die zijn opgeslagen in databricks-bestandssysteem.

<br/>  

- **Bericht**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Oorzaak**:`Bad authoring.`

- **Aanbeveling**: Controleer de [definitie van gekoppelde service](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Bericht**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Oorzaak**:`Bad authoring.`

- **Aanbeveling**: Controleer de [definitie van gekoppelde service](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Bericht**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Oorzaak**:`Bad authoring.`

- **Aanbeveling**: Raadpleeg de foutmelding.

<br/>

### <a name="error-code--3202"></a>Foutcode: 3202

- **Bericht**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Oorzaak**:`Too many Databricks runs in an hour.`

- **Aanbeveling:** Controleer alle pijplijnen die deze Werkruimte Gegevensbricks gebruiken voor het maken van taken.  Als pijplijnen te veel Databricks in totaal hebben uitgevoerd, migreert u enkele pijplijnen naar een nieuwe werkruimte.

<br/>  

- **Bericht**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Oorzaak**:`Authoring error: No value provided for the parameter.`

- **Aanbeveling:** Inspecteer de JSON van de pijplijn en zorg ervoor dat alle parameters in het notitieblok baseParameters een niet-lege waarde opgeven.

<br/>  

- **Bericht** `User: `: SimpleUserContext{userId=...,user@company.comname= , orgId=...}` is not   authorized to access cluster.`

- **Oorzaak:** de gebruiker die het toegangstoken heeft gegenereerd, heeft geen toegang tot het cluster Databricks dat is opgegeven in de gekoppelde service.

- **Aanbeveling:** Zorg ervoor dat de gebruiker over de vereiste machtigingen in de werkruimte beschikt.


### <a name="error-code--3203"></a>Foutcode: 3203

- **Bericht**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Oorzaak**: Het cluster is beëindigd. Voor interactieve clusters kan dit een racevoorwaarde zijn.

- **Aanbeveling:** De beste manier om deze fout te voorkomen is het gebruik van taakclusters.


### <a name="error-code--3204"></a>Foutcode: 3204

- **Bericht**:`Job execution failed.`

- **Oorzaak:** foutberichten geven verschillende problemen aan, zoals een onverwachte clusterstatus of een specifieke activiteit. Meestal verschijnt er helemaal geen foutmelding.

- **Aanbeveling**: N/A
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

De volgende tabel is van toepassing op U-SQL.
      
### <a name="error-code--2709"></a>Foutcode: 2709

- **Bericht**:`The access token is from the wrong tenant.`

- **Oorzaak:** onjuiste Azure Active Directory -tenant (Azure AD).

- **Aanbeveling:** onjuiste Azure Active Directory (Azure AD)-tenant.

<br/>

- **Bericht**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Oorzaak:** deze fout wordt veroorzaakt door beperking op Data Lake Analytics.

- **Aanbeveling:** Verminder het aantal ingediende taken in Data Lake Analytics door de triggers en gelijktijdigheidsinstellingen van Data Factory voor activiteiten te wijzigen. Of verhoog de limieten voor Data Lake Analytics.

<br/>  

- **Bericht**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Oorzaak:** deze fout wordt veroorzaakt door beperking op Data Lake Analytics.

- **Aanbeveling:** Verminder het aantal ingediende taken in Data Lake Analytics door de triggers en gelijktijdigheidsinstellingen van Data Factory voor activiteiten te wijzigen. Of verhoog de limieten voor Data Lake Analytics.


### <a name="error-code--2705"></a>Foutcode: 2705

- **Bericht**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Oorzaak:** de serviceprincipal of het certificaat heeft geen toegang tot het bestand in opslag.

- **Aanbeveling:** Controleer of de serviceprincipal of het certificaat dat de gebruiker voor Data Lake Analytics-taken voorziet, toegang heeft tot het Data Lake Analytics-account en de standaardinstantie Data Lake Storage uit de hoofdmap.


### <a name="error-code--2711"></a>Foutcode: 2711

- **Bericht**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Oorzaak:** de serviceprincipal of het certificaat heeft geen toegang tot het bestand in opslag.

- **Aanbeveling:** Controleer of de serviceprincipal of het certificaat dat de gebruiker voor Data Lake Analytics-taken voorziet, toegang heeft tot het Data Lake Analytics-account en de standaardinstantie Data Lake Storage uit de hoofdmap.

<br/>  

- **Bericht**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Oorzaak:** het pad naar het U-SQL-bestand is verkeerd of de gekoppelde servicereferenties hebben geen toegang.

- **Aanbeveling:** Controleer het pad en de referenties die in de gekoppelde service worden opgegeven.


### <a name="error-code--2704"></a>Foutcode: 2704

- **Bericht**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Oorzaak:** de serviceprincipal of het certificaat heeft geen toegang tot het bestand in opslag.

- **Aanbeveling:** Controleer of de serviceprincipal of het certificaat dat de gebruiker voor Data Lake Analytics-taken voorziet, toegang heeft tot het Data Lake Analytics-account en de standaardinstantie Data Lake Storage uit de hoofdmap.


### <a name="error-code--2707"></a>Foutcode: 2707

- **Bericht**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Oorzaak:** Het Data Lake Analytics-account in de gekoppelde service is onjuist.

- **Aanbeveling:** Controleer of het juiste account is opgegeven.


### <a name="error-code--2703"></a>Foutcode: 2703

- **Bericht**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Oorzaak:** De fout is afkomstig van Data Lake Analytics.

- **Aanbeveling:** een fout zoals het voorbeeld betekent dat de taak is ingediend bij Data Lake Analytics en dat het script daar is mislukt. Onderzoek in Data Lake Analytics. Ga in de portal naar het Data Lake Analytics-account en zoek naar de taak met behulp van de activiteit Data Factory-activiteit uitvoeren ID (niet de pijplijnrun-ID). De taak daar biedt meer informatie over de fout en zal u helpen problemen op te lossen. Als de oplossing niet duidelijk is, neemt u contact op met het ondersteuningsteam van Data Lake Analytics en geeft u de taak-URL op, waaronder uw accountnaam en de taak-id.
          

## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>Foutcode: 3602

- **Bericht**:`Invalid HttpMethod: '%method;'.`

- **Oorzaak:** De http-methode die is opgegeven in de activiteitspayload wordt niet ondersteund door Azure-functieactiviteit.

- **Aanbeveling:** De Ondersteunde Http-methoden zijn PUT, POST, GET, DELETE, OPTIONS, HEAD en TRACE.


### <a name="error-code--3603"></a>Foutcode: 3603

- **Bericht**:`Response Content is not a valid JObject.`

- **Oorzaak**: Azure-functie die werd aangeroepen, heeft geen JSON-payload in de reactie teruggegeven. ADF Azure-functieactiviteit ondersteunt alleen JSON-antwoordinhoud.

- **Aanbeveling**: Azure-functie bijwerken om een geldige JSON-payload terug te sturen, bijvoorbeeld een C#-functie kan terugkeren (ActionResult)nieuwe\"OkObjectResult("{\"Id :\"123\"}");


### <a name="error-code--3606"></a>Foutcode: 3606

- **Bericht:** Functieactiviteit azure ontbreekt functiesleutel.

- **Oorzaak:** de definitie van Azure-functieactiviteit is niet voltooid.

- **Aanbeveling:** Controleer de invoer AzureFunction activiteit JSON definitie heeft eigenschap met de naam 'functionKey'.


### <a name="error-code--3607"></a>Foutcode: 3607

- **Bericht**:`Azure function activity missing function name.`

- **Oorzaak:** de definitie van Azure-functieactiviteit is niet voltooid.

- **Aanbeveling:** Controleer de invoer AzureFunction activiteit JSON definitie heeft eigenschap met de naam 'functionName'.


### <a name="error-code--3608"></a>Foutcode: 3608

- **Bericht**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Oorzaak:** Azure-functiegegevens in de activiteitsdefinitie kunnen onjuist zijn.

- **Aanbeveling:** Bevestig de details van de Azure-functie en probeer het opnieuw.


### <a name="error-code--3609"></a>Foutcode: 3609

- **Bericht**:`Azure function activity missing functionAppUrl.`

- **Oorzaak:** de definitie van Azure-functieactiviteit is niet voltooid.

- **Aanbeveling**: Controleer de invoer AzureFunction activiteit JSON definitie heeft eigenschap met de naam 'functionAppUrl'.


### <a name="error-code--3610"></a>Foutcode: 3610

- **Bericht**:`There was an error while calling endpoint.`

- **Oorzaak**: De URL van de functie kan onjuist zijn.

- **Aanbeveling**: Zorg ervoor dat de waarde voor 'functionAppUrl' in de activiteit JSON correct is en probeer het opnieuw.


### <a name="error-code--3611"></a>Foutcode: 3611

- **Bericht**:`Azure function activity missing Method in JSON.`

- **Oorzaak:** de definitie van Azure-functieactiviteit is niet voltooid.

- **Aanbeveling:** Controleer de invoer AzureFunction-activiteit JSON-definitie heeft de eigenschap 'methode'.


### <a name="error-code--3612"></a>Foutcode: 3612

- **Bericht**:`Azure function activity missing LinkedService definition in JSON.`

- **Oorzaak:** de definitie van Azure-functieactiviteit is niet voltooid.

- **Aanbeveling:** Controleer de invoer AzureFunction-activiteit JSON-definitie heeft gekoppelde servicegegevens.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Foutcode: 4101

- **Bericht**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Oorzaak**: Slechte indeling of ontbrekende definitie van eigenschap '%propertyName;'.

- **Aanbeveling:** Controleer of de activiteit '%activityName;' eigenschap '%propertyName;' heeft gedefinieerd met de juiste gegevens.


### <a name="error-code--4110"></a>Foutcode: 4110

- **Bericht**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Oorzaak:** AzureMLExecutePipeline-activiteitsdefinitie is niet voltooid.

- **Aanbeveling:** Controleer of de azuremlexecutpipelineactiviteit JSON-activiteit met elkaar is gekoppeld.


### <a name="error-code--4111"></a>Foutcode: 4111

- **Bericht**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Oorzaak**: Onjuiste activiteitsdefinitie.

- **Aanbeveling:** Controleer of de JSON-definitie van azureMLExecutePipeline-activiteit van invoer azureMLExecute pipeline de juiste gekoppelde servicegegevens bevat.


### <a name="error-code--4112"></a>Foutcode: 4112

- **Bericht**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Oorzaak**: Slechte indeling of ontbrekende definitie van eigenschap '%propertyName;'.

- **Aanbeveling:** Controleer of de gekoppelde service eigenschap '%propertyName;' heeft gedefinieerd met de juiste gegevens.


### <a name="error-code--4121"></a>Foutcode: 4121

- **Bericht**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak:** De referentie die wordt gebruikt om toegang te krijgen tot Azure Machine Learning is verlopen.

- **Aanbeveling:** Controleer of de referentie geldig is en opnieuw probeert


### <a name="error-code--4122"></a>Foutcode: 4122

- **Bericht**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak:** de referentie die wordt verstrekt in Azure Machine Learning Linked Service is ongeldig of heeft geen toestemming voor de bewerking.

- **Aanbeveling:** Controleer of de referentie in Linked Service geldig is en toestemming heeft om toegang te krijgen tot Azure Machine Learning.


### <a name="error-code--4123"></a>Foutcode: 4123

- **Bericht**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak:** eigenschappen van de activiteit, zoals pipelineParameters zijn ongeldig voor de Azure ML-pijplijn.

- **Aanbeveling:** Controleer de waarde van activiteitseigenschappen die overeenkomen met de verwachte payload van de gepubliceerde Azure ML-pijplijn die is opgegeven in Linked Service.


### <a name="error-code--4124"></a>Foutcode: 4124

- **Bericht**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak:** het gepubliceerde eindpunt van de Azure ML-pijplijn bestaat niet.

- **Aanbeveling:** Controleer of het gepubliceerde eindpunt van de Azure Machine Learning-pijplijn dat is opgegeven in Linked Service bestaat in Azure Machine Learning.


### <a name="error-code--4125"></a>Foutcode: 4125

- **Bericht**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Oorzaak:** Serverfout op Azure Machine Learning.

- **Aanbeveling**: Probeer het later opnieuw. Neem contact op met azure Machine Learning-team voor hulp als er een probleem blijft bestaan.


### <a name="error-code--4126"></a>Foutcode: 4126

- **Bericht**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Oorzaak:** Azure ML-pijplijnuitvoering is mislukt.

- **Aanbeveling:** Controleer in Azure Machine Learning voor meer foutlogboeken en repareer de ML-pijplijn.



## <a name="common"></a>Algemeen

### <a name="error-code--2103"></a>Foutcode: 2103

- **Bericht**:`Please provide value for the required property '%propertyName;'.`

- **Oorzaak:** De waarde voor de eigenschap is niet opgegeven, maar het is vereist in het scenario.

- **Aanbeveling:** Geef de waarde van het bericht op en probeer het opnieuw.


### <a name="error-code--2104"></a>Foutcode: 2104

- **Bericht**:`The type of the property '%propertyName;' is incorrect.`

- **Oorzaak:** Het type van de geleverde eigenschap is niet zoals verwacht.

- **Aanbeveling:** Repareer het type van de accommodatie en probeer het opnieuw.


### <a name="error-code--2105"></a>Foutcode: 2105

- **Bericht**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Oorzaak:** de waarde voor de eigenschap is ongeldig of heeft niet de verwachte indeling.

- **Aanbeveling:** Zoek de documentatie voor de accommodatie op en zorg ervoor dat de opgegeven waarde een verwachte indeling en type heeft.


### <a name="error-code--2106"></a>Foutcode: 2106

- **Bericht**:`The storage connection string is invalid. %errorMessage;`

- **Oorzaak:** de verbindingstekenreeks voor de opslag is ongeldig of heeft een onjuiste indeling.

- **Aanbeveling:** Ga naar de Azure-portal, zoek uw opslag, kopieer de verbindingstekenreeks en plak in uw gekoppelde service en probeer het opnieuw.


### <a name="error-code--2108"></a>Foutcode: 2108

- **Bericht**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Oorzaak:** De aanvraag is mislukt vanwege een onderliggend probleem, zoals netwerkconnectiviteit, DNS-fout, servercertificaatvalidatie of time-out.

- **Aanbeveling**: Gebruik Fiddler/Postman om het verzoek te valideren.


### <a name="error-code--2110"></a>Foutcode: 2110

- **Bericht**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Oorzaak:** de gekoppelde service die in de activiteit is opgegeven, was verkeerd.

- **Aanbeveling:** Zorg ervoor dat het gekoppelde servicetype een van de ondersteunde typen voor de activiteit is. Voor HDI-activiteiten kan het gekoppelde servicetype bijvoorbeeld HDInsight of HDInsightOnDemand zijn.


### <a name="error-code--2111"></a>Foutcode: 2111

- **Bericht**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Oorzaak:** Het type van de geleverde eigenschap is niet zoals verwacht.

- **Aanbeveling:** Repareer het type van de accommodatie en probeer het opnieuw.


### <a name="error-code--2112"></a>Foutcode: 2112

- **Bericht**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Oorzaak:** het cloudtype wordt niet ondersteund of kan niet worden bepaald voor opslag vanuit het EndpointSuffix.

- **Aanbeveling:** Gebruik opslag in een andere cloud en probeer het opnieuw.


### <a name="error-code--2128"></a>Foutcode: 2128

- **Bericht**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Oorzaak:** Netwerkconnectiviteit, DNS-storing, servercertificaatvalidatie of time-out.

- **Aanbeveling:** Controleer of het eindpunt dat u probeert te raken, reageert op aanvragen. U tools zoals Fiddler/ Postman gebruiken.



## <a name="custom"></a>Aangepast telefoonnummer

De volgende tabel is van toepassing op Azure Batch.
      
### <a name="error-code--2500"></a>Foutcode: 2500

- **Bericht**:`Hit unexpected exception and execution failed.`

- **Oorzaak**:`Can't launch command, or the program returned an error code.`

- **Aanbeveling:** Zorg ervoor dat het uitvoerbare bestand bestaat. Als het programma is gestart, moet u ervoor zorgen dat *stdout.txt* en *stderr.txt* zijn geüpload naar het opslagaccount. Het is een goede gewoonte om overvloedige logs uit te zenden in uw code voor debugging.


### <a name="error-code--2501"></a>Foutcode: 2501

- **Bericht**:`Cannot access user batch account; please check batch account settings.`

- **Oorzaak:** Onjuiste batchtoegangssleutel of poolnaam.

- **Aanbeveling:** Controleer de naam van de groep en de batchtoegangssleutel in de gekoppelde service.


### <a name="error-code--2502"></a>Foutcode: 2502

- **Bericht**:`Cannot access user storage account; please check storage account settings.`

- **Oorzaak:** Onjuiste naam van het opslagaccount of toegangssleutel.

- **Aanbeveling:** Controleer de naam van het opslagaccount en de toegangssleutel in de gekoppelde service.


### <a name="error-code--2504"></a>Foutcode: 2504

- **Bericht**:`Operation returned an invalid status code 'BadRequest'.`

- **Oorzaak:** Te veel bestanden in de mapPad van de aangepaste activiteit. De totale grootte van resourceBestanden mag niet meer dan 32.768 tekens bedragen.

- **Aanbeveling**: Verwijder onnodige bestanden. Of rits ze vast en voeg een unzip-opdracht toe om ze eruit te halen. Gebruik bijvoorbeeld`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Foutcode: 2505

- **Bericht**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Oorzaak:** Aangepaste activiteiten ondersteunen alleen opslagaccounts die een toegangssleutel gebruiken.

- **Aanbeveling**: Raadpleeg de foutbeschrijving.


### <a name="error-code--2507"></a>Foutcode: 2507

- **Bericht**:`The folder path does not exist or is empty: ...`

- **Oorzaak:** er zijn geen bestanden in het opslagaccount op het opgegeven pad.

- **Aanbeveling:** het mappad moet de uitvoerbare bestanden bevatten die u wilt uitvoeren.


### <a name="error-code--2508"></a>Foutcode: 2508

- **Bericht**:`There are duplicate files in the resource folder.`

- **Oorzaak:** Meerdere bestanden met dezelfde naam bevinden zich in verschillende submappen van folderPath.

- **Aanbeveling:** Aangepaste activiteiten vlakken mapstructuur af onder mapPath.  Als u de mapstructuur wilt behouden, ritst u de bestanden en haalt u ze uit in Azure Batch met behulp van een opdracht uitritsen. Gebruik bijvoorbeeld`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Foutcode: 2509

- **Bericht**:`Batch   url ... is invalid; it must be in Uri format.`

- **Oorzaak:** batch-URL's moeten vergelijkbaar zijn met`https://mybatchaccount.eastus.batch.azure.com`

- **Aanbeveling**: Raadpleeg de foutbeschrijving.


### <a name="error-code--2510"></a>Foutcode: 2510

- **Bericht**:`An   error occurred while sending the request.`

- **Oorzaak:** de url van de batch is ongeldig.

- **Aanbeveling:** Controleer de batch-URL.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Foutcode: 200

- **Bericht**:`Unexpected error happened: '%error;'.`

- **Oorzaak:** er is een intern serviceprobleem.

- **Aanbeveling**: Neem contact op met ADF-ondersteuning voor verdere hulp.


### <a name="error-code--201"></a>Foutcode: 201

- **Bericht**:`JobType %jobType; is not found.`

- **Oorzaak:** er is een nieuw taaktype dat niet wordt ondersteund door ADF.

- **Aanbeveling**: Neem contact op met adf support team voor verdere hulp.


### <a name="error-code--202"></a>Foutcode: 202

- **Bericht**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**: De foutmelding moet de details weergeven van wat er mis ging.

- **Aanbeveling:** De foutmelding moet helpen om het probleem op te lossen. Als er niet genoeg informatie is, neem dan contact op met ADF-ondersteuning voor verdere hulp.


### <a name="error-code--203"></a>Foutcode: 203

- **Bericht**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**: De foutmelding moet de details weergeven van wat er mis ging.

- **Aanbeveling:** De foutmelding moet helpen om het probleem op te lossen. Als er niet genoeg informatie is, neem dan contact op met ADF-ondersteuning voor verdere hulp.


### <a name="error-code--204"></a>Foutcode: 204

- **Bericht**:`The resumption token is missing for runId '%runId;'.`

- **Oorzaak:** er is een intern serviceprobleem.

- **Aanbeveling**: Neem contact op met ADF-ondersteuning voor verdere hulp.


### <a name="error-code--205"></a>Foutcode: 205

- **Bericht**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Oorzaak:** er is een fout opgetreden bij het maken van het cluster HDI on demand.

- **Aanbeveling**: Neem contact op met ADF-ondersteuning voor verdere hulp.


### <a name="error-code--206"></a>Foutcode: 206

- **Bericht**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Oorzaak**: Er was een intern probleem met de service die dit heeft veroorzaakt.

- **Aanbeveling**: Dit kan een tijdelijke kwestie zijn. Probeer uw werk opnieuw en als het probleem zich voortduurt, neem dan contact op met de ADF-ondersteuning voor verdere hulp.


### <a name="error-code--207"></a>Foutcode: 207

- **Bericht**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Oorzaak:** er is een interne fout opgetreden tijdens het bepalen van het gebied vanuit het primaire opslagaccount.

- **Aanbeveling**: Probeer een andere opslag. Als dit geen acceptabele oplossing is, neem dan contact op met adf support team voor verdere hulp.


### <a name="error-code--208"></a>Foutcode: 208

- **Bericht**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Oorzaak:** er is een interne fout opgetreden tijdens het lezen van de Service Principal of het momentmaken van de MSI-verificatie.

- **Aanbeveling**: Overweeg een Service Principal te verstrekken die machtigingen heeft om een HDInsight-cluster in het meegeleverde abonnement te maken en het opnieuw te proberen. Als dit geen aanvaardbare oplossing is, neem dan contact op met adf support team voor verdere hulp.


### <a name="error-code--2300"></a>Foutcode: 2300

- **Bericht**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Oorzaak**: Wanneer een foutbericht een bericht bevat dat lijkt op 'De naam op afstand kan niet worden opgelost.', kan dit betekenen dat het opgegeven cluster URI ongeldig is.


- **Aanbeveling:** Zorg ervoor dat het cluster niet is verwijderd en dat de meegeleverde URI juist is. Wanneer u de URI in een browser opent, ziet u de Ambari-gebruikersinterface. Als het cluster zich in een virtueel netwerk bevindt, moet de URI de privé-URI zijn. Als u deze wilt openen, gebruikt u een VM die deel uitmaakt van hetzelfde virtuele netwerk. Zie voor meer informatie [dit.](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)
                  

<br>

- **Oorzaak**: Wanneer een foutbericht een bericht bevat dat lijkt op 'Een taak is geannuleerd.', betekent dit dat er een time-out is opgetreden voor het indienen van een taak.

- **Aanbeveling**: Het probleem kan zijn dat algemene HDInsight-connectiviteit of netwerkconnectiviteit is. Bevestig eerst dat de HDInsight Ambari UI beschikbaar is vanuit elke browser. Controleer of uw referenties nog steeds geldig zijn. Als u zelf gehoste geïntegreerde runtime (IR) gebruikt, moet u dit doen vanaf de VM of -machine waar de zelf gehoste IR is geïnstalleerd. Probeer vervolgens de taak van Data Factory opnieuw in te dienen. Als dit nog steeds niet lukt, neemt u contact op met het team Gegevensfabriek voor ondersteuning.

<br>

- **Oorzaak**: Wanneer een foutbericht een bericht bevat dat lijkt op 'User admin is locked out in Ambari' of 'Unauthorized: Ambari user name or password is incorrect', betekent dit dat de referenties voor HDInsight onjuist zijn of zijn verlopen.

- **Aanbeveling:** Corrigeer de referenties en implementeer de gekoppelde service opnieuw. Zorg er eerst voor dat de referenties werken op HDInsight door het cluster URI op elke browser te openen en te proberen in te loggen. Als de referenties niet werken, u ze opnieuw instellen vanuit de Azure-portal.

<br>

- **Oorzaak**: Wanneer een foutbericht een bericht bevat dat lijkt op '502 - Webserver heeft een ongeldig antwoord ontvangen terwijl deze fungeert als gateway- of proxyserver', wordt deze fout geretourneerd door de HDInsight-service.


- **Aanbeveling:** Bekijk bijvoorbeeld Azure HDInsight-probleemoplossingsdocumentatie , https://hdinsight.github.io/ambari/ambari-ui-502-error.html https://hdinsight.github.io/spark/spark-thriftserver-errors.html, , https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502
                  

<br>

- **Oorzaak**: Wanneer een foutbericht een bericht bevat dat lijkt op 'Niet in staat om het sollicitatieverzoek in te dienen omdat templeton-service bezig is met te veel vacatures in te dienen' of 'Queue root.joblauncher heeft al 500 sollicitaties, kan het indienen van sollicitaties niet accepteren', betekent dit dat er tegelijkertijd te veel vacatures worden ingediend bij HDInsight.

- **Aanbeveling**: Overweeg het aantal gelijktijdige taken dat bij HDInsight is ingediend, te beperken. Raadpleeg de activiteit gelijktijdigheid van de gegevensfabriek als de taken door dezelfde activiteit worden ingediend. Wijzig de triggers zodat de gelijktijdige pijplijnruns in de loop van de tijd worden gespreid. Raadpleeg de documentatie van HDInsight om templeton.parallellisme.job.submit aan te passen zoals de fout suggereert.


### <a name="error-code--2301"></a>Foutcode: 2301

- **Bericht**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Oorzaak**: HDInsight cluster of service heeft problemen.


- **Aanbeveling:** Deze fout treedt op wanneer ADF geen reactie krijgt van het HDInsight-cluster wanneer u probeert de status van de lopende taak te krijgen. Het kan de oorzaak zijn van problemen op het cluster zelf, of HDInsight-service kan een storing hebben. Raadpleeg de documentatie voor https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guideprobleemoplossing van HDInsight bij , of neem contact op met hun ondersteuning voor verdere hulp.
                


### <a name="error-code--2302"></a>Foutcode: 2302

- **Bericht**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Oorzaak**: De taak is ingediend bij het HDI-cluster en is daar mislukt.

- **Aanbeveling:** Volg de koppeling Garenlogboeken in de activiteitsrun Uitvoer en zoek naar de fouten in HDI-uitvoer. Neem indien nodig contact op met het HDInsight-team voor ondersteuning.


### <a name="error-code--2303"></a>Foutcode: 2303

- **Bericht**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Oorzaak**: De taak is ingediend bij het HDI-cluster en is daar mislukt.

- **Aanbeveling:** Volg de koppeling Garenlogboeken in de activiteitsrun Uitvoer en zoek naar de fouten in HDI-uitvoer. Probeer het opnieuw of neem contact op met hdInsight team voor ondersteuning indien nodig.


### <a name="error-code--2304"></a>Foutcode: 2304

- **Bericht**:`MSI authentication is not supported on storages for HDI activities.`

- **Oorzaak:** De opslaggekoppelde service(s) die wordt gebruikt in HDI-gekoppelde service of HDI-activiteit, zijn geconfigureerd met MSI-verificatie die niet wordt ondersteund.

- **Aanbeveling:** Geef volledige verbindingstekenreeksen voor opslagaccounts die worden gebruikt in hdi-gekoppelde service of HDI-activiteit.


### <a name="error-code--2305"></a>Foutcode: 2305

- **Bericht**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Oorzaak**: de verbindingsgegevens voor het HDI-cluster zijn onjuist, de meegeleverde gebruiker heeft geen machtigingen om de vereiste actie uit te voeren of de HDInsight-service had problemen met het reageren op verzoeken van ADF.

- **Aanbeveling:** Zorg ervoor dat de gebruikersgegevens correct zijn. Controleer ook of de Ambari-gebruikersinterface voor het HDI-cluster kan worden geopend in een browser van de VM waar IR is geïnstalleerd op geval van zelfgehoste IR, of kan worden geopend vanaf elke machine in het geval Azure IR.


### <a name="error-code--2306"></a>Foutcode: 2306

- **Bericht**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Oorzaak**: De json die is opgegeven voor de scriptactie is ongeldig.


- **Aanbeveling:** De foutboodschap moet helpen om het probleem te identificeren. Bevestig de json-configuratie en probeer het opnieuw. Kijk https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service voor meer informatie.
                


### <a name="error-code--2310"></a>Foutcode: 2310

- **Bericht**:`Failed to submit Spark job. Error: '%message;'`

- **Oorzaak**: ADF probeerde een batch te maken op een Spark-cluster met Behulp van Livy API (livy/batch), maar kreeg een foutmelding.

- **Aanbeveling:** Volg het foutbericht om het probleem op te lossen. Als er niet genoeg informatie is om het op te lossen, neemt u contact op met het HDI-team en verstrekt u de batch-id en taak-id die kan worden gevonden in de activiteitsuitvoer uitvoer op de ADF-pagina.


### <a name="error-code--2312"></a>Foutcode: 2312

- **Bericht**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Oorzaak:** De taak is mislukt op het CLUSTER HDInsight Spark.

- **Aanbeveling:** Volg de koppelingen in de activiteitsrun Uitvoer op de pagina ADF-monitoring om problemen op te lossen op het hdInsight Spark-cluster. Neem contact op met hdinsight support team voor verdere hulp.


### <a name="error-code--2313"></a>Foutcode: 2313

- **Bericht**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Oorzaak:** De batch is verwijderd op het HDInsight Spark-cluster.

- **Aanbeveling:** Problemen met batches op het HDInsight Spark-cluster oplossen. Neem contact op met HDInsight-ondersteuning voor verdere hulp. 


### <a name="error-code--2328"></a>Foutcode: 2328

- **Bericht**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**:`The error message should show the details of what went wrong.`

- **Aanbeveling:** De foutmelding moet helpen om het probleem op te lossen.


### <a name="error-code--2329"></a>Foutcode: 2329

- **Bericht**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Oorzaak**: De foutmelding moet de details weergeven van wat er mis ging.

- **Aanbeveling:** De foutmelding moet helpen om het probleem op te lossen.


### <a name="error-code--2331"></a>Foutcode: 2331

- **Bericht**:`The file path should not be null or empty.`

- **Oorzaak:** het opgegeven bestandspad is leeg.

- **Aanbeveling:** Geef een pad op voor een bestand dat bestaat.


### <a name="error-code--2340"></a>Foutcode: 2340

- **Bericht**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Oorzaak**: HDInsightOnDemand linked service ondersteunt geen uitvoering via SelfHosted IR.

- **Aanbeveling:** Selecteer een Azure IR en probeer het opnieuw.


### <a name="error-code--2341"></a>Foutcode: 2341

- **Bericht**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Oorzaak:** de opgegeven URL is niet in de juiste indeling.

- **Aanbeveling:** Repareer de URL van het cluster en probeer het opnieuw.


### <a name="error-code--2342"></a>Foutcode: 2342

- **Bericht**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Oorzaak:** de opgegeven referenties zijn verkeerd voor het cluster of er was een netwerkconfiguratie of verbindingsprobleem of IR heeft problemen met het maken van verbinding met het cluster.

- **Aanbeveling**:  
      1. Controleer of de referenties correct zijn door de Ambari-gebruikersinterface van het HDInsight-cluster in een browser te openen.
      2. Als het cluster zich in VNet bevindt en zelfgehoste IR wordt gebruikt, moet de HDI-URL de privé-URL in VNets zijn, wat betekent dat het '-int' moet hebben na de clusternaam. Bijvoorbeeld "https://mycluster.azurehdinsight.net/" moet wordenhttps://mycluster-int.azurehdinsight.net/veranderd in " ".
      2. Als het cluster zich in VNet bevindt, wordt zelf gehoste IR gebruikt en wordt de privé-URL gebruikt en is de verbinding nog steeds mislukt, dan had de VM waar de IR is geïnstalleerd problemen met het verbinden met de HDI. Maak verbinding met de VM waar IR is geïnstalleerd en open de Ambari-gebruikersinterface in een browser. Gebruik de privé-URL voor het cluster. Deze verbinding moet werken vanuit de browser. Als dit niet het zo is, neem dan contact op met het ondersteuningsteam van HDInsight voor verdere hulp.
      3. Als zelfgehoste IR niet wordt gebruikt, moet het HDI-cluster openbaar toegankelijk zijn. Open de Ambari-gebruikersinterface in een browser en zorg ervoor dat deze wordt geopend. Als er problemen zijn met het cluster of de services erop, neem dan contact op met het ondersteuningsteam van HDInsight voor hulp.
      Dus in het algemeen moet de HDI-cluster-URL die wordt gebruikt in adf-gekoppelde service toegankelijk zijn voor ADF IR (zelf gehost of Azure) om de testverbinding te laten slagen en om te kunnen werken. Dit kan eenvoudig worden geverifieerd door die URL vanuit een browser te openen vanuit een computer of een openbare machine.
    


### <a name="error-code--2343"></a>Foutcode: 2343

- **Bericht**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Oorzaak:** gebruikersnaam of wachtwoord zijn leeg.

- **Aanbeveling:** Geef de juiste referenties om verbinding te maken met HDI en probeer het opnieuw.


### <a name="error-code--2345"></a>Foutcode: 2345

- **Bericht**:`Failed to read the content of the hive script. Error: '%message;'`

- **Oorzaak:** het scriptbestand bestaat niet of ADF kan geen verbinding maken met de locatie van het script.

- **Aanbeveling:** Controleer of het script bestaat en of de gekoppelde service de juiste referenties heeft voor verbinding.


### <a name="error-code--2346"></a>Foutcode: 2346

- **Bericht**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Oorzaak**: ADF heeft geprobeerd een ODBC-verbinding met het HDI-cluster tot stand te brengen en het is mislukt met fouten.

- **Aanbeveling:** De foutbericht en foutcode moeten helpen bij het oplossen van de ODBC-verbindingsfouten. Als ze niet voldoende zijn om het probleem op te lossen, neem dan contact op met Azure HDInsight-team voor ondersteuning.


### <a name="error-code--2347"></a>Foutcode: 2347

- **Bericht**:`Hive execution through ODBC failed with error message '%message;'.`

- **Oorzaak**: ADF heeft het hivescript voor uitvoering via ODBC-verbinding naar het HDI-cluster verzonden en het script is mislukt op HDI.

- **Aanbeveling:** De uitvoering van het hivescript is mislukt op HET HDI-cluster en foutbericht en foutcode moeten helpen bij het oplossen van problemen. Als ze niet voldoende zijn om het probleem op te lossen, neem dan contact op met Azure HDInsight-team voor ondersteuning.


### <a name="error-code--2348"></a>Foutcode: 2348

- **Bericht**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Oorzaak:** de door opslag gekoppelde service-eigenschappen zijn niet correct ingesteld.

- **Aanbeveling**: Alleen volledige verbindingstekenreeksen worden ondersteund in de belangrijkste opslaggekoppelde service voor HDI-activiteiten. Zorg ervoor dat u geen MSI auth of applicaties gebruikt.


### <a name="error-code--2350"></a>Foutcode: 2350

- **Bericht**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Oorzaak:** de referenties die worden verstrekt om verbinding te maken met de opslag waar de bestanden zich moeten bevinden, zijn onjuist of de bestanden bestaan daar niet.

- **Aanbeveling:** Deze fout treedt op wanneer ADF voorbereidende stappen uitvoert voor HDI-activiteiten. Het probeert om bestanden te kopiëren naar de belangrijkste opslag voor het indienen van de taak naar HDI. Zorg ervoor dat er bestanden op de opgegeven locatie aanwezig zijn, de opslagverbinding is correct. ADF HDI-activiteiten ondersteunen geen MSI-verificatie op opslagaccounts met betrekking tot HDI-activiteiten, dus zorg ervoor dat deze gekoppelde services volledige sleutels hebben of Azure Key Vault gebruiken.


### <a name="error-code--2351"></a>Foutcode: 2351

- **Bericht**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Oorzaak:** het bestand bestaat niet op opgegeven pad.

- **Aanbeveling:** Controleer of het bestand daadwerkelijk bestaat en de gekoppelde service met verbindingsgegevens die naar dit bestand verwijzen, heeft de juiste referenties.


### <a name="error-code--2352"></a>Foutcode: 2352

- **Bericht**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Oorzaak:** de eigenschappen van de gekoppelde bestandsopslagservice zijn niet correct ingesteld.

- **Aanbeveling:** Zorg ervoor dat de eigenschappen van de gekoppelde bestandsopslagservice correct zijn geconfigureerd.


### <a name="error-code--2353"></a>Foutcode: 2353

- **Bericht**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Oorzaak:** de eigenschappen van de scriptopslag gekoppelde service zijn niet correct ingesteld.

- **Aanbeveling:** Zorg ervoor dat de eigenschappen van de gekoppelde scriptopslagservice correct zijn geconfigureerd.


### <a name="error-code--2354"></a>Foutcode: 2354

- **Bericht**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Oorzaak:** het door opslag gekoppelde servicetype wordt niet ondersteund door de activiteit.

- **Aanbeveling:** Zorg ervoor dat de geselecteerde gekoppelde service een van de ondersteunde typen voor de activiteit heeft. HDI-activiteiten ondersteunen azureblobstorage- en AzureBlobFSStorage-gekoppelde services.


### <a name="error-code--2355"></a>Foutcode: 2355

- **Bericht**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Oorzaak**: De meegeleverde opdrachtOmgeving is onjuist.

- **Aanbeveling**:  
      Zorg ervoor dat de opgegeven \"waarde\"vergelijkbaar \"is met:\" commandEnvironment : [ variableName=variableValue ] En elke variabele wordt slechts één keer in de lijst weergegeven.
    


### <a name="error-code--2356"></a>Foutcode: 2356

- **Bericht**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Oorzaak**: De variabele is twee keer geleverd in de commandEnvironment .

- **Aanbeveling**:  
      Zorg ervoor dat de opgegeven \"waarde\"vergelijkbaar \"is met:\" commandEnvironment : [ variableName=variableValue ] En elke variabele wordt slechts één keer in de lijst weergegeven.
    


### <a name="error-code--2357"></a>Foutcode: 2357

- **Bericht**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Oorzaak:** de opgegeven referenties zijn onjuist.

- **Aanbeveling:** Controleer de verbindingsgegevens in de gekoppelde service van ADLS Gen 1 en controleer of de testverbinding slaagt.


### <a name="error-code--2358"></a>Foutcode: 2358

- **Bericht**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Oorzaak**: De opgegeven waarde voor de vereiste eigenschap 'TimeToLive' heeft een ongeldige indeling. 

- **Aanbeveling:** Werk de waarde bij die in het voorgestelde bereik moet worden en probeer het opnieuw.


### <a name="error-code--2359"></a>Foutcode: 2359

- **Bericht**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Oorzaak**: De opgegeven waarde voor de eigenschap 'rollen' is ongeldig.

- **Aanbeveling:** Werk de waarde bij om een van de suggesties te zijn en probeer het opnieuw.


### <a name="error-code--2360"></a>Foutcode: 2360

- **Bericht**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Oorzaak:** de opgegeven verbindingstekenreeks voor de HCatalogLinkedService is ongeldig.

- **Aanbeveling:** Werk de waarde bij naar een juiste Azure SQL-verbindingstekenreeks en probeer het opnieuw.


### <a name="error-code--2361"></a>Foutcode: 2361

- **Bericht**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Oorzaak**: De clustercreatie is mislukt en ADF heeft geen fout teruggekregen van de HDInsight-service.

- **Aanbeveling:** Open de Azure-portal en probeer de HDI-bron met de opgegeven naam te vinden en controleer de inrichtingsstatus. Neem contact op met hdInsight support team voor verdere hulp.


### <a name="error-code--2362"></a>Foutcode: 2362

- **Bericht**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Oorzaak:** de meegeleverde extra opslag was geen Azure Blob-opslag.

- **Aanbeveling:** Bied Azure Blob-opslagaccount als extra opslag voor HDInsight on demand-gekoppelde service.



## <a name="web-activity"></a>Web Activity

### <a name="error-code--2128"></a>Foutcode: 2128

- **Bericht**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Oorzaak:** Netwerkconnectiviteit, DNS-storing, servercertificaatvalidatie of time-out.

- **Aanbeveling:** Controleer of het eindpunt dat u probeert te raken, reageert op aanvragen. U tools zoals Fiddler/ Postman gebruiken.


### <a name="error-code--2108"></a>Foutcode: 2108

- **Bericht**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Oorzaak:** De aanvraag is mislukt vanwege een onderliggend probleem, zoals netwerkconnectiviteit, DNS-fout, servercertificaatvalidatie of time-out.

- **Aanbeveling**: Gebruik Fiddler/Postman om het verzoek te valideren.
<br>


#### <a name="more-details"></a>Meer informatie
Ga als volgt te werk om Fiddler te gebruiken om een HTTP-sessie van de bewaakte webtoepassing te maken:

1. Fiddler downloaden, installeren en [openen.](https://www.telerik.com/download/fiddler)

1. Als uw webtoepassing HTTPS gebruikt, gaat u**HTTPS**naar **Https, opties voor extra's.** > **Fiddler Options** >  Selecteer **HTTPS-CONNECTs vastleggen** en **HTTPS-verkeer decoderen**.

   ![Fiddler-opties](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Als uw toepassing TLS/SSL-certificaten gebruikt, voegt u het Fiddler-certificaat toe aan uw apparaat. Ga naar **Hulpmiddelen** > **fiddler Opties** > **HTTPS** > **Actions** > Export Root Certificate to**Desktop**.

1. Schakel vastleggen uit door naar **File** > **Capture Traffic**te gaan . Of druk op **F12**.

1. Ruim de cache van uw browser uit, zodat alle items in de cache worden verwijderd en opnieuw moeten worden gedownload.

1. Een aanvraag maken:

   1. Selecteer het tabblad **Componist.**

   1. Stel de HTTP-methode en URL in.
   
   1. Voeg kopteksten en een aanvraaginstantie toe als dat nodig is.

   1. Selecteer **Uitvoeren**.

1. Schakel het vastleggen van verkeer opnieuw in en voltooi de problematische transactie op uw pagina.

1. Ga naar **Alle** > **sessies****opslaan bestand** > .

Zie [Aan de slag met Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer hulp bij het oplossen van problemen:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Functieaanvragen gegevensfabriek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter informatie over Data Factory](https://twitter.com/hashtag/DataFactory)


            

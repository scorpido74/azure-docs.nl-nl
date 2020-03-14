---
title: REST API fout codes-Azure Machine Learning Studio (klassiek) | Microsoft Docs
description: Deze foutcodes kunnen worden geretourneerd door een bewerking op een Azure Machine Learning-webservice.
keywords: ''
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 9e25f2fbc10eb07cc71f2a7bd34247c0191f61b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79217760"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klassiek) REST API fout codes

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
De volgende fout codes kunnen worden geretourneerd door een bewerking op een webservice Azure Machine Learning Studio (klassiek).
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-statuscode 400)
 
Ongeldig argument opgegeven.
 
Deze klasse van fouten betekent een ergens opgegeven argument is ongeldig. Dit wordt mogelijk een referentie of de locatie van de Azure-opslag op een andere waarde die is doorgegeven aan de webservice. Raadpleeg het fout veld ' code ' in de sectie ' Details ' om vast te stellen welke specifieke argumenten ongeldig zijn.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| BadParameterValue | De opgegeven parameterwaarde voldoet niet aan de parameter-regel op de parameter |
| BadSubscriptionId | De abonnements-Id die wordt gebruikt om te beoordelen is niet aanwezig in de resource een |
| BadVersionCall | Er is een ongeldige versie parameter door gegeven tijdens de API-aanroep: {0}. Controleer de API help-pagina voor het doorgeven van de juiste versie en probeer het opnieuw. |
| BatchJobInputsNotSpecified | De volgende vereiste invoer (nen) is niet opgegeven met de aanvraag: {0}. Zorg ervoor dat alle ingevoerde gegevens is opgegeven en probeer het opnieuw. |
| BatchJobInputsTooManySpecified | De aanvraag opgegeven meer invoer dan gedefinieerd in de service. Lijst met geaccepteerde invoer (len): {0}. Zorg ervoor dat alle ingevoerde gegevens correct is opgegeven en probeer het opnieuw. |
| BlobNameTooLong | Het opslag traject voor Azure Blob dat is gegeven voor de diagnostische uitvoer is te lang: {0}. Het pad inkorten en probeer het opnieuw. |
| BlobNotFound | Kan geen toegang krijgen tot de beschik bare Azure Blob-{0}.  Azure-fout bericht: {1}. |
| ContainerIsEmpty | Er is geen Azure storage-container-naam is opgegeven. Geef een geldige container op en probeer het opnieuw. |
| ContainerSegmentInvalid | Ongeldige containernaam. Geef een geldige container op en probeer het opnieuw. |
| ContainerValidationFailed | Validatie van de BLOB-container is mislukt met de volgende fout: {0}. |
| DataTypeNotSupported | Niet-ondersteund gegevenstype opgegeven. Geef geldige gegevens type(n) en probeer het opnieuw. |
| DuplicateInputInBatchCall | De batchaanvraag is ongeldig. Kan niet zowel één als meerdere invoer opgeven op hetzelfde moment. Een van deze items verwijderen uit de aanvraag en probeer het opnieuw. |
| ExpiryTimeInThePast | De gegeven verloop tijd bevindt zich in het verleden: {0}. Geef een toekomstige vervaldatum tijd in UTC en probeer het opnieuw. Nooit verlopen, verlopen tijd niet instellen op NULL. |
| IncompleteSettings | Diagnostische instellingen zijn onvolledig. |
| InputBlobRelativeLocationInvalid | Er is geen Azure storage blob-naam opgegeven. Geef een geldige blob-naam en probeer het opnieuw. |
| InvalidBlob | Ongeldige BLOB-specificatie voor blob: {0}. Controleer of deze verbindingsreeks / relatief pad of SAS-token specificatie juist is en probeer het opnieuw. |
| InvalidBlobConnectionString | De connection string die is opgegeven voor een van de blobs voor invoer/uitvoer, is ongeldig: {0}. Corrigeer dit en probeer het opnieuw. |
| InvalidBlobExtension | De BLOB-verwijzing: {0} heeft een ongeldige of ontbrekende bestands extensie. Ondersteunde bestands extensies voor dit uitvoer type zijn: "{1}". |
| InvalidInputNames | Ongeldige service-invoer naam (en) opgegeven in de aanvraag: {0}. De ingevoerde gegevens worden toegewezen aan de juiste service-invoer en probeer het opnieuw. |
| InvalidOutputOverrideName | Ongeldige naam van uitvoer onderdrukking: {0}. De service beschikt niet over een uitvoer-knooppunt met deze naam. Geef een naam voor het knooppunt juiste uitvoer voor de onderdrukking (hoofdlettergevoeligheid van toepassing). |
| InvalidQueryParameter | Ongeldige query parameter{0}. {1} |
| MissingInputBlobInformation | Ontbrekende informatie voor Azure storage-blob. Geef een geldige verbindingsreeks en de relatieve pad of de URI en probeer het opnieuw. |
| MissingJobId | Geen taak-Id opgegeven. Een taak Id wordt geretourneerd wanneer een taak voor de eerste keer is verzonden. Controleer of dat de taak-Id juist is en probeer het opnieuw. |
| MissingKeys | Er zijn geen sleutels opgegeven of een van de primaire of secundaire sleutel is niet opgegeven. |
| MissingModelPackage | Er is geen model pakket-Id of model pakket die zijn opgegeven. Geef een geldige model-pakket-Id of model pakket en probeer het opnieuw. |
| MissingOutputOverrideSpecification | In de aanvraag ontbreekt de BLOB-specificatie voor de uitvoer onderdrukking {0}. Geef een geldige blob-locatie met de aanvraag, of de uitvoerspecificatie verwijderen als er geen locatie overschrijven vereist is. |
| MissingRequestInput | De webservice wordt verwacht dat de invoer, maar er is niets is opgegeven. Zorg ervoor dat geldige invoerwaarden zijn opgegeven op basis van de gepubliceerde invoer poorten in het model en probeer het opnieuw. |
| MissingRequiredGlobalParameters | Niet alle vereiste web service parameter (s) opgegeven. Controleer of de parameter (s) verwacht voor de modules die zijn juist zijn en probeer het opnieuw. |
| MissingRequiredOutputOverrides | Bij het aanroepen van een versleutelde service-eindpunt verplicht om door te geven is onderdrukt de uitvoer voor de uitvoer van de service. Ontbrekende onderdrukkingen op dit moment voor deze uitvoer: {0} |
| MissingWebServiceGroupId | Er is geen groep web service-Id opgegeven. Geef een groeps-Id van geldige web service en probeer het opnieuw. |
| MissingWebServiceId | Er is geen webservice-Id opgegeven. Geef een geldige webservice Id en probeer het opnieuw. |
| MissingWebServicePackage | Er zijn geen web servicepakket opgegeven. Geef een geldige web service-pakket en probeer het opnieuw. |
| MissingWorkspaceId | Er is geen werkruimte-Id opgegeven. Geef een geldige werkruimte-Id en probeer het opnieuw. |
| ModelConfigurationInvalid | Ongeldig modeltype configuratie in het model-pakket. Zorg ervoor dat de Modelconfiguratie bevat een definitie in de uitvoer-eindpunt(en), std fout eindpunt, en std buiten-eindpunt en probeer het opnieuw. |
| ModelPackageIdInvalid | Ongeldige model pakket-id. Controleer of de model pakket-id juist is en probeer het opnieuw. |
| RequestBodyInvalid | Geen hoofdtekst van de aanvraag opgegeven of een fout bij het deserialiseren van de aanvraagtekst. |
| RequestIsEmpty | Er zijn geen aanvragen opgegeven. Geef een geldige aanvraag en probeer het opnieuw. |
| UnexpectedParameter | Onverwachte parameters opgegeven. Controleer of alle parameternamen goed gespeld zijn, alleen verwachte parameters worden doorgegeven en probeer het opnieuw. |
| Errorcodes.unknownerror | Onbekende fout. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Kan de vereisten voor gelijktijdige aanvragen voor de {0}-webservice niet wijzigen. |
| WebServiceIdInvalid | Ongeldige webservice-id opgegeven. Webservice-id moet een geldige guid zijn. |
| WebServiceTooManyConcurrentRequestRequirement | Kan niet gelijktijdige aanvraag vereisten instellen voor meer dan {0}. |
| WebServiceTypeInvalid | Ongeldig servicetype opgegeven. Controleer of dat het type van de geldige web service juist is en probeer het opnieuw. Geldige typen web-service: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-statuscode 400)
 
Gebruiker is ongeldig argument opgegeven.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| InputMismatchError | Invoergegevens komt niet overeen met invoerpoort schema. |
| InputParseError | Het parseren van gebruikersinvoer vector is mislukt.  Controleer of dat de invoer vector heeft het juiste aantal kolommen en gegevenstypen.  Aanvullende details: {0}. |
| MissingRequiredGlobalParameters | Parameter (s) werd verwacht door de webservice ontbreken. Controleer of de vereiste parameters verwacht door de webservice juist zijn en probeer het opnieuw. |
| UnexpectedParameter | Controleer of alleen de vereiste parameters verwacht door de webservice worden doorgegeven en probeer het opnieuw. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP-statuscode 400)
 
De aanvraag is ongeldig in de huidige context.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| CannotStartJob | De taak kan niet worden gestart omdat deze zich in de {0} status bevindt. |
| IncompatibleModel | Het model is niet compatibel met versie van de aanvraag. Versie van de aanvraag ondersteunt slechts één datatable uitvoer modellen. |
| MultipleInputsNotAllowed | Het model is niet toegestaan voor meerdere invoergegevens. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-statuscode 400)
 
Uitvoering van module is een interne bibliotheek opgetreden.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-statuscode 400)
 
Uitvoering van module is een fout opgetreden.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-statuscode 400)
 
Ongeldige web service-pakket. Controleer of het opgegeven pakket van web service juist is en probeer het opnieuw.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| FormatError | De web service-pakket is ongeldig. Details: {0} |
| RuntimesError | De grafiek web service-pakket is ongeldig. Details: {0} |
| ValidationError | De grafiek web service-pakket is ongeldig. Details: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Niet-geautoriseerde (HTTP-statuscode 401)
 
De aanvraag is niet geautoriseerd voor toegang tot resources.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| AdminRequestUnauthorized | Niet geautoriseerd |
| ManagementRequestUnauthorized | Niet geautoriseerd |
| ScoreRequestUnauthorized | Ongeldige referenties opgegeven. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-statuscode 404)
 
Kan de resource niet vinden.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| ModelPackageNotFound | Model-pakket is niet gevonden. Controleer of dat de model-pakket-Id juist is en probeer het opnieuw. |
| WebServiceIdNotFoundInWorkspace | De webservice onder deze werkruimte is niet gevonden. Er is een niet-overeenkomend tussen de webServiceId en de werkruimte-id. Controleer of de webservice maakt deel uit van de werkruimte en probeer het opnieuw. |
| WebServiceNotFound | De webservice is niet gevonden. Controleer of dat de webservice-Id juist is en probeer het opnieuw. |
| WorkspaceNotFound | De werkruimte niet vinden. Controleer of dat de werkruimte-Id juist is en probeer het opnieuw. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-statuscode 408)
 
De bewerking kan niet worden voltooid in de toegestane tijd.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| RequestCanceled | Aanvraag is geannuleerd door de client. |
| ScoreRequestTimeout | Er is een time-out opgetreden voor de aanvraag voor het uitvoeren. |
 
## <a name="conflict-http-status-code-409"></a>Conflict (HTTP-statuscode 409)
 
Er bestaat al een resource.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Ongeldige uitvoer parameternaam. Probeer met behulp van de metagegevens-module editor voor namen van kolommen wijzigen en probeer het opnieuw. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-statuscode 413)
 
Het model heeft overschrijdt het geheugenquotum die is toegewezen.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| OutOfMemoryLimit | Het model verbruikt meer geheugen hebben dan is bestemd is voor deze. Het Maxi maal toegestane geheugen voor het model is {0} MB. Controleer of het model voor problemen. |
 
## <a name="internalerror-http-status-code-500"></a>Activiteits-id (HTTP-statuscode 500)
 
Uitvoering is een interne fout opgetreden.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Het proces van de container is vastgelopen met systeemfout |
| ContainerProcessTerminatedWithUnknownError | Het proces van de container is vastgelopen vanwege een onbekende fout |
| ContainerValidationFailed | Validatie van de BLOB-container is mislukt met de volgende fout: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Geen argumenten opgegeven. Controleer of dat geldige argumenten worden doorgegeven en probeer het opnieuw. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Poort-id ={0} heeft een niet-ondersteund gegevens type: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Genereren van Swagger is mislukt, Details: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| Errorcodes.unknownerror |  |
| UnknownJobStatusCode | Onbekende taak status code {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, Details: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-statuscode 500)
 
Uitvoering is een interne fout opgetreden. Het systeem is weinig geheugen. Probeer het opnieuw.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-statuscode 500)
 
Ongeldig modeltype-pakket. Controleer of het opgegeven model-pakket juist is en probeer het opnieuw.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-statuscode 500)
 
Ongeldige web service-pakket. Controleer of het opgegeven webpakket juist is en probeer het opnieuw.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| ModuleError | De grafiek web service-pakket is ongeldig. Details: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (HTTP-statuscode 503)
 
De aanvraag kan niet uitvoeren omdat de containers worden geïnitialiseerd.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (HTTP-statuscode 503)
 
Service is tijdelijk niet beschikbaar.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| NoMoreResources | Geen resources beschikbaar zijn voor de aanvraag. |
| RequestThrottled | De aanvraag is beperkt voor het {0}-eind punt. De maximale gelijktijdigheid voor het eind punt is {1}. |
| TooManyConcurrentRequests | Te veel gelijktijdige aanvragen die worden verzonden. |
| TooManyHostsBeingInitialized | Te veel hosts op hetzelfde moment wordt geïnitialiseerd. Houd rekening met beperkingen / opnieuw te proberen. |
| TooManyHostsBeingInitializedPerModel | Te veel hosts op hetzelfde moment wordt geïnitialiseerd. Houd rekening met beperkingen / opnieuw te proberen. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-statuscode 504)
 
De bewerking kan niet worden voltooid in de toegestane tijd.
 
| Foutcode | Bericht van gebruiker |
| ---------- |--------------|
| BackendInitializationTimeout | De initialisatie van de web-service kan niet worden voltooid binnen de toegestane tijd. |
| BackendScoreTimeout | Het uitvoeren van de aanvraag van de web service kan niet worden voltooid binnen de toegestane tijd. |
 

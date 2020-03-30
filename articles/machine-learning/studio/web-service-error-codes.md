---
title: REST API-foutcodes - Azure Machine Learning Studio (klassiek) | Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217760"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (klassieke) REST API-foutcodes

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
De volgende foutcodes kunnen worden geretourneerd door een bewerking op een Azure Machine Learning Studio (klassieke) webservice.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (HTTP-statuscode 400)
 
Ongeldig argument verstrekt.
 
Deze klasse van fouten betekent dat een argument dat ergens is opgegeven, ongeldig was. Dit kan een referentie of locatie van Azure-opslag zijn voor iets dat is doorgegeven aan de webservice. Kijk naar het veld foutcode in de sectie 'details' om te kunnen vaststellen welk specifiek argument ongeldig was.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| BadParameterWaarde | De opgegeven parameterwaarde voldoet niet aan de parameterregel op de parameter |
| BadSubscriptionId | De abonnements-id die wordt gebruikt om te scoren is niet degene die aanwezig is in de resource |
| BadVersionCall (BadVersionCall) | Ongeldige versieparameter is doorgegeven {0}tijdens de API-aanroep: . Controleer de HELP-pagina api's voor het passeren van de juiste versie en probeer het opnieuw. |
| BatchJobInputsNotSpecified | Met de aanvraag zijn niet de volgende {0}vereiste input(s) opgegeven: . Zorg ervoor dat alle invoergegevens zijn opgegeven en probeer het opnieuw. |
| BatchJobInputsTooManySpecified | De aanvraag specificeerde meer ingangen dan gedefinieerd in de service. Lijst van geaccepteerde {0}input(en): . Zorg ervoor dat alle invoergegevens correct zijn opgegeven en probeer het opnieuw. |
| BlobNameTooLong | Azure blob-opslagpad dat is opgegeven {0}voor diagnostische uitvoer is te lang: . Verkort het pad en probeer het opnieuw. |
| BlobNotFound | Geen toegang tot de {0}meegeleverde Azure-blob - .  Azure-foutbericht: {1}. |
| ContainerIsEmpty | Er is geen naam van een Azure-opslagcontainer opgegeven. Geef een geldige containernaam op en probeer het opnieuw. |
| ContainerSegmentOngeldig | Ongeldige containernaam. Geef een geldige containernaam op en probeer het opnieuw. |
| ContainerValidatie is mislukt | Blobcontainervalidatie is mislukt {0}met deze fout: . |
| DataTypeNotSupported | Niet-ondersteunde gegevenstype opgegeven. Geef geldige gegevenstype(s) op en probeer het opnieuw. |
| Inputinbatchcall dupliceren | De batchaanvraag is ongeldig. Kan niet zowel enkele als meerdere invoer op hetzelfde moment opgeven. Verwijder een van deze items uit het verzoek en probeer het opnieuw. |
| ExpiryTimeInThePast | De verstrekte vervaldatum is in {0}het verleden: . Geef een toekomstige vervaldatum op in UTC en probeer het opnieuw. Als u nooit wilt verlopen, stelt u de vervaldatum in op NULL. |
| Onvolledige instellingen | Diagnostische instellingen zijn onvolledig. |
| InvoerBlobRelativeLocationOngeldig | Er is geen naam van azure-opslagblob s. Geef een geldige blobnaam op en probeer het opnieuw. |
| Ongeldig Blob | Ongeldige blobspecificatie {0}voor blob: . Controleer of de verbindingstekenreeks / relatieve pad- of SAS-tokenspecificatie correct is en probeer het opnieuw. |
| Ongeldige klodderverbinding | De verbindingstekenreeks die is opgegeven voor een van {0}de invoer-/uitvoerblobs in ongeldig: . Corrigeer dit en probeer het opnieuw. |
| Ongeldige BlobExtensie | De blob-verwijzing: {0} heeft een ongeldige of ontbrekende bestandsextensie. Ondersteunde bestandsextensies voor dit uitvoertype{1}zijn: " ". |
| Ongeldige invoernamen | Ongeldige serviceinvoernaam(en) die {0}in de aanvraag is opgegeven: . Breng de invoergegevens naar de juiste service-ingangen en probeer het opnieuw. |
| Ongeldigenaam voor overridese uitvoer | Ongeldige uitvoer {0}overschrijven naam: . De service heeft geen uitvoerknooppunt met deze naam. Geef een correcte uitvoerknooppuntnaam door om te overschrijven (de hoofdlettergevoeligheid is van toepassing). |
| OngeldigeQueryParameter | Ongeldige queryparameter '{0}'. {1} |
| MissingInputBlobInformatie | Ontbrekende Azure-opslagblobgegevens. Geef een geldige verbindingstekenreeks en relatief pad of URI op en probeer het opnieuw. |
| Vermiste JobId | Geen job Id verstrekt. Een taak-id wordt geretourneerd wanneer een taak voor de eerste keer is ingediend. Controleer of de taak-id correct is en probeer het opnieuw. |
| Ontbrekende sleutels | Er worden geen sleutels opgegeven of een primaire of secundaire sleutel wordt niet verstrekt. |
| VermistModelPakket | Geen modelpakket Id of modelpakket aanwezig. Geef een geldig modelpakket-id of modelpakket op en probeer het opnieuw. |
| MissingoutputoverrideSpecificatie | De aanvraag ontbreekt de blob-specificatie voor het overschrijven {0}van uitvoer. Geef bij het verzoek een geldige bloblocatie op of verwijder de uitvoerspecificatie als er geen locatieoverschrijving gewenst is. |
| MissingRequestInput | De webservice verwacht een ingang, maar er is geen input geleverd. Zorg ervoor dat geldige ingangen worden geleverd op basis van de gepubliceerde invoerpoorten in het model en probeer het opnieuw. |
| MissingRequiredGlobalParameters | Niet alle vereiste webserviceparameter(s) aanwezig. Controleer of de verwachte parameter(s) voor de module(s) correct zijn en probeer het opnieuw. |
| MissingRequiredOutputOverrides | Bij het aanroepen van een versleuteld serviceeindpunt is het verplicht om uitvoeroverschrijvingen door te geven voor alle uitvoervan de service. Ontbrekende overschrijvingen op dit moment voor deze uitgangen:{0} |
| MissingWebServiceGroupId | Geen webservicegroep Id verstrekt. Geef een geldige webservicegroep-id op en probeer het opnieuw. |
| MissingWebServiceId | Geen webservice-id verstrekt. Geef een geldige webservice-id op en probeer het opnieuw. |
| MissingWebServicePakket | Geen webservicepakket aanwezig. Zorg voor een geldig webservicepakket en probeer het opnieuw. |
| MissingWorkspaceId | Er is geen werkplek-id aanwezig. Geef een geldig werk-id op en probeer het opnieuw. |
| ModelConfiguratie ongeldig | Ongeldige modelconfiguratie in het modelpakket. Zorg ervoor dat de modelconfiguratie de definitie van uitvoereindpunt(s), het eindpunt van de STD-fout en het eindpunt voor std-out bevat en probeer het opnieuw. |
| ModelPackageIdInvalid | Ongeldig modelpakket Id. Controleer of de id van het modelpakket correct is en probeer het opnieuw. |
| RequestBodyInvalid | Geen aanvraaginstantie verstrekt of fout bij het deserialiseren van de aanvraaginstantie. |
| Aanvraag is leeg | Geen verzoek verstrekt. Geef een geldig verzoek op en probeer het opnieuw. |
| Onverwachte parameter | Onverwachte parameters aanwezig. Controleer of alle parameternamen correct zijn gespeld, alleen verwachte parameters worden doorgegeven en probeer het opnieuw. |
| UnknownError UnknownError UnknownError UnknownError | Onbekende fout. |
| UserParameterOngeldig | {0} |
| WebServiceConcurrentRequestRequirementOngeldig | De vereisten voor {0} gelijktijdige aanvragen voor webservice kan niet worden gewijzigd. |
| WebServiceIdInvalid | Ongeldige webservice-id. Webservice-id moet een geldige guid zijn. |
| WebServiceTooManyGelijktijdigRequestRequirement | Kan de vereiste gelijktijdige {0}aanvragen niet instellen op meer dan . |
| WebServiceTypeOngeldig | Ongeldig webservicetype. Controleer of het geldige webservicetype correct is en probeer het opnieuw. Geldige webservicetypen: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (HTTP-statuscode 400)
 
Ongeldig gebruikersargument verstrekt.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| InvoerMismatchError | Invoergegevens komen niet overeen met het invoerpoortschema. |
| InvoerParseD | Parsing van invoervector is mislukt.  Controleer of de invoervector het juiste aantal kolommen en gegevenstypen heeft.  Aanvullende details: {0}. |
| MissingRequiredGlobalParameters | Parameter(s) die door de webservice worden verwacht, ontbreken. Controleer of alle vereiste parameters die door de webservice worden verwacht, correct zijn en probeer het opnieuw. |
| Onverwachte parameter | Controleer of alleen de vereiste parameters die door de webservice worden verwacht, worden doorgegeven en probeer het opnieuw. |
| UserParameterOngeldig | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (HTTP-statuscode 400)
 
De aanvraag is ongeldig in de huidige context.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| Kan niet startenJob | De taak kan niet worden {0} gestart omdat deze in staat is. |
| OnverenigbaarModel | Het model is niet compatibel met de aanvraagversie. De aanvraagversie ondersteunt slechts afzonderlijke uitvoermodellen van gegevenstabel. |
| MultipleInputsNotA toegestaan | Het model staat geen meerdere ingangen toe. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (HTTP-statuscode 400)
 
Bij de uitvoering van de module is een interne bibliotheekfout opgetreden.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (HTTP-statuscode 400)
 
Er is een fout opgetreden bij de uitvoering van de module.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (HTTP-statuscode 400)
 
Ongeldig webservicepakket. Controleer of het geleverde webservicepakket correct is en probeer het opnieuw.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| FormatError | Het webservicepakket is verkeerd gevormd. Details:{0} |
| RuntimesError | De grafiek van het webservicepakket is ongeldig. Details:{0} |
| ValidatieFout | De grafiek van het webservicepakket is ongeldig. Details:{0} |
 
## <a name="unauthorized-http-status-code-401"></a>Ongeautoriseerd (HTTP-statuscode 401)
 
Aanvraag is ongeautoriseerd om toegang te krijgen tot resource.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| AdminRequestUnauthorized | Niet geautoriseerd |
| ManagementRequestOngeautoriseerd | Niet geautoriseerd |
| ScoreRequestOngeautoriseerd | Ongeldige referenties verstrekt. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (HTTP-statuscode 404)
 
Bron niet gevonden.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| ModelPackageNotFound | Modelpakket niet gevonden. Controleer of de id van het modelpakket correct is en probeer het opnieuw. |
| WebServiceIdNotfoundinWorkspace | Webservice onder deze werkruimte niet gevonden. Er is een mismatch tussen de webServiceId en de workspaceId. Controleer of de webservice deel uitmaakt van de werkruimte en probeer het opnieuw. |
| WebServiceNotFound | Webservice niet gevonden. Controleer of de webservice-id correct is en probeer het opnieuw. |
| Werkruimteniet gevonden | Werkruimte niet gevonden. Controleer of de werkruimte-id correct is en probeer het opnieuw. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (HTTP-statuscode 408)
 
De bewerking kon niet binnen de toegestane tijd worden voltooid.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| Aanvraag geannuleerd | Aanvraag is geannuleerd door de klant. |
| ScoreRequestTime-out | Er is een time-out ophet moment van de uitvoeringsaanvraag. |
 
## <a name="conflict-http-status-code-409"></a>Conflict (HTTP-statuscode 409)
 
Resource bestaat al.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Naam van de ongeldige uitvoerparameter. Probeer de module metagegevenseditor te gebruiken om kolommen een andere naam te geven en het opnieuw te proberen. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (HTTP-statuscode 413)
 
Het model had het geheugenquotum overschreden dat eraan was toegewezen.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| OutofMemoryLimit | Het model verbruikt meer geheugen dan was toegeëigend voor het. Maximaal toegestaan geheugen voor {0} het model is MB. Controleer uw model op problemen. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (HTTP-statuscode 500)
 
Bij de uitvoering is een interne fout opgetreden.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| Beheerverificatie is mislukt |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigureerd |  |
| ContainerProcessTerminatedWithSystemError | Het containerproces is gecrasht met systeemfout |
| ContainerProcessTerminatedWithUnknownError | Het containerproces is gecrasht met onbekende fout |
| ContainerValidatie is mislukt | Blobcontainervalidatie is mislukt {0}met deze fout: . |
| DeleteWebServiceResource is mislukt |  |
| ExceptionDeserializationError ExceptionDeserializationError ExceptionDeserializationError ExceptionDe |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| Ongeldige geheugenconfiguratie | InvalidMemoryConfiguration, ConfigValue:{0} |
| Ongeldige CacheCacheConfiguratie |  |
| Ongeldige ResourceDownloadconfiguratie |  |
| Ongeldig WebServiceResources |  |
| MissingTaskInstance MissingTaskInstance | Geen argumenten verstrekt. Controleer of geldige argumenten worden doorgegeven en probeer het opnieuw. |
| ModelPakketOngeldig |  |
| Moduleuitvoering mislukt |  |
| Moduleladen is mislukt |  |
| ModuleObjectClone mislukt |  |
| Uitvoerconversie is mislukt |  |
| PortDataTypeNotSupported | Poortid={0} heeft een niet-ondersteund {1}gegevenstype: . |
| ResourceDownload |  |
| ResourceLoad is mislukt |  |
| ServiceUrisNotFound |  |
| SwaggerGeneratie | Swagger generatie mislukt, Details:{0} |
| Onverwachte scorestatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError UnknownError UnknownError UnknownError |  |
| UnknownJobStatusCode | Onbekende statuscode {0}voor vacatures . |
| UnknownModuleFout |  |
| UpdateWebServiceResource is mislukt |  |
| WebServiceGroupNotfound |  |
| WebServicePackageOngeldig | InvalidWebServicePackage, Details:{0} |
| WorkerAuthorization is mislukt |  |
| WorkerOnbereikbaar |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (HTTP-statuscode 500)
 
Bij de uitvoering is een interne fout opgetreden. Systeem met weinig geheugen. Probeer het opnieuw.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (HTTP-statuscode 500)
 
Ongeldig modelpakket. Controleer of het meegeleverde modelpakket correct is en probeer het opnieuw.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (HTTP-statuscode 500)
 
Ongeldig webservicepakket. Controleer of het meegeleverde webpakket correct is en probeer het opnieuw.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| ModuleFout | De grafiek van het webservicepakket is ongeldig. Details:{0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>Containers initialiseren (HTTP-statuscode 503)
 
De aanvraag kan niet worden uitgevoerd omdat de containers worden geïnitialiseerd.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceNiet beschikbaar (HTTP-statuscode 503)
 
Service is tijdelijk niet beschikbaar.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| NoMoreResources | Er zijn geen bronnen beschikbaar voor aanvraag. |
| AanvraagThrottled | Verzoek was voor {0} eindpunt beperkt. De maximale gelijktijdigheid voor {1}het eindpunt is . |
| TeVeelGelijktijdigverzoeken | Te veel gelijktijdige verzoeken verzonden. |
| TooManyHostsBeingInitialized | Te veel hosts worden tegelijkertijd geïnitialiseerd. Overweeg throttling / opnieuw proberen. |
| TooManyHostsBeingInitializedPerModel TooManyHostsBeingInitializedPerModel TooManyHostsBeingInitializedPerModel TooMany | Te veel hosts worden tegelijkertijd geïnitialiseerd. Overweeg throttling / opnieuw proberen. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (HTTP-statuscode 504)
 
De bewerking kon niet binnen de toegestane tijd worden voltooid.
 
| Foutcode | Gebruikersbericht |
| ---------- |--------------|
| Back-endInitialisatieTime-out | De initialisatie van de webservice kan niet binnen de toegestane tijd worden voltooid. |
| BackendScoreTimeout | De uitvoering van de webserviceaanvraag kan niet binnen de toegestane tijd worden voltooid. |
 

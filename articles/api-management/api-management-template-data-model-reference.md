---
title: Naslag informatie over Azure API Management-sjabloon gegevens model | Microsoft Docs
description: Meer informatie over de entiteits-en type representaties voor algemene items die worden gebruikt in de gegevens modellen voor de sjablonen voor de ontwikkelaars Portal in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 323b3effb4c4a63d03ab7ea5251e0d59271d9dcd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072142"
---
# <a name="azure-api-management-template-data-model-reference"></a>Naslag informatie over Azure API Management-sjabloon gegevens model
In dit onderwerp worden de entiteits-en type representaties beschreven voor algemene items die worden gebruikt in de gegevens modellen voor de sjablonen voor de ontwikkelaars Portal in azure API Management.  
  
 Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

De ontwikkelaars Portal is niet beschikbaar in de laag verbruik.

## <a name="reference"></a>Referentie

-   [API](#API)  
-   [API-samen vatting](#APISummary)  
-   [Toepassing](#Application)  
-   [Envelop](#Attachment)  
-   [Voorbeeld van code](#Sample)  
-   [Heffen](#Comment)  
-   [Filteren](#Filtering)  
-   [Header](#Header)  
-   [HTTP-aanvraag](#HTTPRequest)  
-   [HTTP-antwoord](#HTTPResponse)  
-   [Name](#Issue)  
-   [Bewerking](#Operation)  
-   [Menu bewerking](#Menu)  
-   [Menu opdracht bewerking](#MenuItem)  
-   [Haalt](#Paging)  
-   [Parameter](#Parameter)  
-   [Voortplant](#Product)  
-   [Provider](#Provider)  
-   [Wijze](#Representation)  
-   [Abonnement](#Subscription)  
-   [Samen vatting van abonnement](#SubscriptionSummary)  
-   [Gegevens van gebruikers account](#UserAccountInfo)  
-   [Gebruikers aanmelding](#UseSignIn)  
-   [Gebruikers registratie](#UserSignUp)  
  
##  <a name="API"></a> API  
 De `API` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`id`|string|Resource-id. Identificeert de API binnen het huidige API Management service-exemplaar. De waarde is een geldige relatieve URL in de notatie van `apis/{id}` waar `{id}` is een API-id. Deze eigenschap is alleen-lezen.|  
|`name`|string|De naam van de API. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`description`|string|Beschrijving van de API. Mag niet leeg zijn. HTML-opmaak tags kunnen bevatten. De maximale lengte is 1000 tekens.|  
|`serviceUrl`|string|Absolute URL van de back-end-service die deze API implementeert.|  
|`path`|string|Relatieve URL is een unieke identificatie van deze API en alle bijbehorende bron paden binnen het API Management service-exemplaar. Het wordt toegevoegd aan de URL van het API-eind punt dat is opgegeven tijdens het maken van het service-exemplaar om een open bare URL voor deze API te vormen.|  
|`protocols`|matrix van getal|Hierin wordt beschreven op welke protocollen de bewerkingen in deze API kunnen worden aangeroepen. Toegestane waarden zijn `1 - http` en `2 - https`, of beide.|  
|`authenticationSettings`|[Verificatie server authenticatie-instellingen](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Verzameling van verificatie-instellingen die zijn opgenomen in deze API.|  
|`subscriptionKeyParameterNames`|object|Een optionele eigenschap die kan worden gebruikt om aangepaste namen op te geven voor query-en/of header-para meters die de abonnements sleutel bevatten. Wanneer deze eigenschap aanwezig is, moet deze ten minste een van de volgende twee eigenschappen bevatten.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>API-samen vatting  
 De `API summary` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`id`|string|Resource-id. Identificeert de API binnen het huidige API Management service-exemplaar. De waarde is een geldige relatieve URL in de notatie van `apis/{id}` waar `{id}` is een API-id. Deze eigenschap is alleen-lezen.|  
|`name`|string|De naam van de API. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`description`|string|Beschrijving van de API. Mag niet leeg zijn. HTML-opmaak tags kunnen bevatten. De maximale lengte is 1000 tekens.|  
  
##  <a name="Application"></a>Modules  
 De `application` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Id`|string|De unieke id van de toepassing.|  
|`Title`|string|De titel van de toepassing.|  
|`Description`|string|De beschrijving van de toepassing.|  
|`Url`|URI|De URI voor de toepassing.|  
|`Version`|string|Versie-informatie voor de toepassing.|  
|`Requirements`|string|Een beschrijving van de vereisten voor de toepassing.|  
|`State`|nummer|De huidige status van de toepassing.<br /><br /> -0-geregistreerd<br /><br /> -1-verzonden<br /><br /> -2-gepubliceerd<br /><br /> -3-afgewezen<br /><br /> -4-niet-gepubliceerd|  
|`RegistrationDate`|DateTime|De datum en tijd waarop de toepassing is geregistreerd.|  
|`CategoryId`|nummer|De categorie van de toepassing (Finance, entertainment, enz.)|  
|`DeveloperId`|string|De unieke id van de ontwikkelaar die de toepassing heeft ingediend.|  
|`Attachments`|Verzameling van [bijlage](#Attachment) -entiteiten.|Bijlagen voor de toepassing, zoals scherm afbeeldingen of pictogrammen.|  
|`Icon`|[Envelop](#Attachment)|Het pictogram voor de toepassing.|  
  
##  <a name="Attachment"></a>Envelop  
 De `attachment` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`UniqueId`|string|De unieke id voor de bijlage.|  
|`Url`|string|De URL van de resource.|  
|`Type`|string|Het type bijlage.|  
|`ContentType`|string|Het media type van de bijlage.|  
  
##  <a name="Sample"></a>Code voorbeeld  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`title`|string|De naam van de bewerking.|  
|`snippet`|string|Deze eigenschap is afgeschaft en mag niet worden gebruikt.|  
|`brush`|string|Welke code-syntax kleur sjabloon moet worden gebruikt bij het weer geven van het code voorbeeld. Toegestane waarden zijn `plain`, `php`, `java` ,`objc`,,, en`ruby`. `python` `xml` `csharp`|  
|`template`|string|De naam van deze voorbeeld sjabloon voor code.|  
|`body`|string|Een tijdelijke aanduiding voor het code voorbeeld gedeelte van het fragment.|  
|`method`|string|De HTTP-methode van de bewerking.|  
|`scheme`|string|Het protocol dat moet worden gebruikt voor de bewerkings aanvraag.|  
|`path`|string|Het pad van de bewerking.|  
|`query`|string|Voor beeld van een query reeks met gedefinieerde para meters.|  
|`host`|string|De URL van de API Management service gateway voor de API die deze bewerking bevat.|  
|`headers`|Verzameling van [header](#Header) -entiteiten.|Kopteksten voor deze bewerking.|  
|`parameters`|Verzameling [parameter](#Parameter) entiteiten.|Para meters die zijn gedefinieerd voor deze bewerking.|  
  
##  <a name="Comment"></a>Heffen  
 De `API` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Id`|nummer|De id van de opmerking.|  
|`CommentText`|string|De hoofd tekst van de opmerking. Kan HTML bevatten.|  
|`DeveloperCompany`|string|De bedrijfs naam van de ontwikkelaar.|  
|`PostedOn`|DateTime|De datum en tijd waarop de opmerking is geplaatst.|  
  
##  <a name="Issue"></a>Name  
 De `issue` entiteit heeft de volgende eigenschappen.  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Id`|string|De unieke id voor het probleem.|  
|`ApiID`|string|De id voor de API waarvoor dit probleem is gerapporteerd.|  
|`Title`|string|De titel van het probleem.|  
|`Description`|string|De beschrijving van het probleem.|  
|`SubscriptionDeveloperName`|string|De voor naam van de ontwikkelaar die het probleem heeft gemeld.|  
|`IssueState`|string|De huidige status van het probleem. Mogelijke waarden worden voorgesteld, geopend, gesloten.|  
|`ReportedOn`|DateTime|De datum en tijd waarop het probleem is gerapporteerd.|  
|`Comments`|Verzameling van [opmerkings](#Comment) entiteiten.|Opmerkingen bij dit probleem.|  
|`Attachments`|Verzameling van [bijlage](api-management-template-data-model-reference.md#Attachment) -entiteiten.|Eventuele bijlagen bij het probleem.|  
|`Services`|Verzameling van [API](#API) -entiteiten.|De Api's die zijn geabonneerd op door de gebruiker die het probleem heeft ingediend.|  
  
##  <a name="Filtering"></a>Filtrati  
 De `filtering` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Pattern`|string|De huidige zoek term; of `null` als er geen zoek term is.|  
|`Placeholder`|string|De tekst die moet worden weer gegeven in het zoekvak wanneer er geen zoek term is opgegeven.|  
  
##  <a name="Header"></a>Journaalkop  
 In deze sectie wordt `parameter` de representatie beschreven.  
  
|Eigenschap|Type|Description|  
|--------------|-----------------|----------|  
|`name`|string|Parameter naam.|  
|`description`|string|Parameter beschrijving.|  
|`value`|string|Waarde van header.|  
|`typeName`|string|Het gegevens type van de waarde voor de header.|  
|`options`|string|Opties.|  
|`required`|boolean|Hiermee wordt aangegeven of de koptekst vereist is.|  
|`readOnly`|boolean|Hiermee wordt aangegeven of de header alleen-lezen is.|  
  
##  <a name="HTTPRequest"></a>HTTP-aanvraag  
 In deze sectie wordt `request` de representatie beschreven.  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`description`|string|Beschrijving van bewerkings aanvraag.|  
|`headers`|matrix van [header](#Header) -entiteiten.|Aanvraag headers.|  
|`parameters`|matrix van [para meter](#Parameter)|Verzameling para meters van bewerkings aanvraag.|  
|`representations`|matrix van [representatie](#Representation)|Verzameling van representaties van bewerkings aanvragen.|  
  
##  <a name="HTTPResponse"></a>HTTP-antwoord  
 In deze sectie wordt `response` de representatie beschreven.  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`statusCode`|positief geheel getal|Status code van bewerkings antwoord.|  
|`description`|string|Beschrijving van bewerkings antwoord.|  
|`representations`|matrix van [representatie](#Representation)|Verzameling van representaties van bewerkings reacties.|  
  
##  <a name="Operation"></a>Schijf  
 De `operation` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`id`|string|Resource-id. Identificeert de bewerking binnen het huidige API Management service-exemplaar uniek. De waarde is een geldige relatieve URL in de notatie van `apis/{aid}/operations/{id}` waar `{aid}` is een API-id `{id}` en is een bewerkings-id. Deze eigenschap is alleen-lezen.|  
|`name`|string|Naam van de bewerking. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`description`|string|Beschrijving van de bewerking. Mag niet leeg zijn. HTML-opmaak tags kunnen bevatten. De maximale lengte is 1000 tekens.|  
|`scheme`|string|Hierin wordt beschreven op welke protocollen de bewerkingen in deze API kunnen worden aangeroepen. Toegestane waarden zijn `http`, `https`, of beide `http` en `https`.|  
|`uriTemplate`|string|Relatieve URL-sjabloon voor het identificeren van de doel resource voor deze bewerking. Kan para meters bevatten. Voorbeeld: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|string|De URL van de API Management gateway die als host fungeert voor de API.|  
|`httpMethod`|string|Bewerkings-HTTP-methode.|  
|`request`|[HTTP-aanvraag](#HTTPRequest)|Een entiteit met aanvraag Details.|  
|`responses`|matrix van [http-antwoord](#HTTPResponse)|Matrix van [http-antwoord-](#HTTPResponse) entiteiten van de bewerking.|  
  
##  <a name="Menu"></a>Menu bewerking  
 De `operation menu` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`ApiId`|string|De id van de huidige API.|  
|`CurrentOperationId`|string|De id van de huidige bewerking.|  
|`Action`|string|Het menu Type.|  
|`MenuItems`|Verzameling van [items](#MenuItem) in de menu opdracht van een bewerking.|De bewerkingen voor de huidige API.|  
  
##  <a name="MenuItem"></a>Menu opdracht bewerking  
 De `operation menu item` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Id`|string|De id van de bewerking.|  
|`Title`|string|De beschrijving van de bewerking.|  
|`HttpMethod`|string|De HTTP-methode van de bewerking.|  
  
##  <a name="Paging"></a>Haalt  
 De `paging` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Page`|nummer|Het huidige pagina nummer.|  
|`PageSize`|nummer|De maximum resultaten die op één pagina worden weer gegeven.|  
|`TotalItemCount`|nummer|Het aantal items dat wordt weer gegeven.|  
|`ShowAll`|boolean|Hiermee wordt aangegeven of alle resultaten op één pagina moeten worden uitgewisseld.|  
|`PageCount`|nummer|Het aantal pagina's met resultaten.|  
  
##  <a name="Parameter"></a>Bepaalde  
 In deze sectie wordt `parameter` de representatie beschreven.  
  
|Eigenschap|Type|Description|  
|--------------|-----------------|----------|  
|`name`|string|Parameter naam.|  
|`description`|string|Parameter beschrijving.|  
|`value`|string|Parameter waarde.|  
|`options`|tekenreeksmatrix|Waarden die zijn gedefinieerd voor query parameter waarden.|  
|`required`|boolean|Hiermee wordt aangegeven of para meter vereist is of niet.|  
|`kind`|nummer|Hiermee wordt aangegeven of deze para meter een pad-para meter (1) of een query reeks parameter (2) is.|  
|`typeName`|string|Parameter type.|  
  
##  <a name="Product"></a>Voortplant  
 De `product` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Id`|string|Resource-id. Hiermee wordt het product uniek geïdentificeerd binnen het huidige API Management service-exemplaar. De waarde is een geldige relatieve URL in de notatie van `products/{pid}` waar `{pid}` is een product-id. Deze eigenschap is alleen-lezen.|  
|`Title`|string|De naam van het product. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`Description`|string|Beschrijving van het product. Mag niet leeg zijn. HTML-opmaak tags kunnen bevatten. De maximale lengte is 1000 tekens.|  
|`Terms`|string|Gebruiks voorwaarden van het product. Ontwikkel aars die zich willen abonneren op het product, worden weer gegeven en moeten deze voor waarden accepteren voordat ze het abonnements proces kunnen volt ooien.|  
|`ProductState`|nummer|Hiermee wordt aangegeven of het product is gepubliceerd of niet. Gepubliceerde producten kunnen worden gedetecteerd door ontwikkel aars op de ontwikkelaars Portal. Niet-gepubliceerde producten zijn alleen zichtbaar voor beheerders.<br /><br /> De toegestane waarden voor de product status zijn:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Hiermee geeft u op of een gebruiker meerdere abonnementen op dit product tegelijk kan hebben.|  
|`MultipleSubscriptionsCount`|nummer|Maximum aantal abonnementen op dit product dat een gebruiker tegelijk mag hebben.|  
  
##  <a name="Provider"></a>Providers  
 De `provider` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Properties`|teken reeks woordenlijst|Eigenschappen voor deze verificatie provider.|  
|`AuthenticationType`|string|Het provider type. (Azure Active Directory, Facebook-aanmelding, Google-account, micro soft-account, Twitter).|  
|`Caption`|string|De weergave naam van de provider.|  
  
##  <a name="Representation"></a>Wijze  
 In deze sectie wordt `representation`een beschreven.  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`contentType`|string|Hiermee geeft u een geregistreerd of aangepast inhouds type op voor deze representatie, `application/xml`bijvoorbeeld.|  
|`sample`|string|Een voor beeld van de weer gave.|  
  
##  <a name="Subscription"></a>Abonnees  
 De `subscription` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Id`|string|Resource-id. Hiermee wordt het abonnement uniek geïdentificeerd binnen het huidige API Management service-exemplaar. De waarde is een geldige relatieve URL in de notatie van `subscriptions/{sid}` waar `{sid}` is een abonnements-id. Deze eigenschap is alleen-lezen.|  
|`ProductId`|string|De product resource-id van het geabonneerde product. De waarde is een geldige relatieve URL in de notatie van `products/{pid}` waar `{pid}` is een product-id.|  
|`ProductTitle`|string|De naam van het product. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`ProductDescription`|string|Beschrijving van het product. Mag niet leeg zijn. HTML-opmaak tags kunnen bevatten. De maximale lengte is 1000 tekens.|  
|`ProductDetailsUrl`|string|Relatieve URL naar de product gegevens.|  
|`state`|string|De status van het abonnement. Mogelijke statussen zijn:<br /><br /> - `0 - suspended`: het abonnement is geblokkeerd en de abonnee kan geen Api's van het product aanroepen.<br /><br /> - `1 - active`: het abonnement is actief.<br /><br /> - `2 - expired`-het abonnement heeft de verval datum bereikt en is gedeactiveerd.<br /><br /> - `3 - submitted`: de abonnements aanvraag is ingediend door de ontwikkelaar, maar is nog niet goedgekeurd of afgekeurd.<br /><br /> - `4 - rejected`-de abonnements aanvraag is geweigerd door een beheerder.<br /><br /> - `5 - cancelled`: het abonnement is geannuleerd door de ontwikkelaar of beheerder.|  
|`DisplayName`|string|De weergave naam van het abonnement.|  
|`CreatedDate`|Datum/tijd|De datum waarop het abonnement is gemaakt, in ISO 8601- `2014-06-24T16:25:00Z`indeling:.|  
|`CanBeCancelled`|boolean|Hiermee wordt aangegeven of het abonnement door de huidige gebruiker kan worden geannuleerd.|  
|`IsAwaitingApproval`|boolean|Hiermee wordt aangegeven of het abonnement wacht op goed keuring.|  
|`StartDate`|Datum/tijd|De begin datum voor het abonnement, in ISO 8601-indeling `2014-06-24T16:25:00Z`:.|  
|`ExpirationDate`|Datum/tijd|De verval datum voor het abonnement, in ISO 8601-indeling `2014-06-24T16:25:00Z`:.|  
|`NotificationDate`|Datum/tijd|De meldings datum voor het abonnement, in ISO 8601- `2014-06-24T16:25:00Z`indeling:.|  
|`primaryKey`|string|De sleutel van het primaire abonnement. De maximale lengte is 256 tekens.|  
|`secondaryKey`|string|De sleutel voor het secundaire abonnement. De maximale lengte is 256 tekens.|  
|`CanBeRenewed`|boolean|Hiermee wordt aangegeven of het abonnement kan worden vernieuwd door de huidige gebruiker.|  
|`HasExpired`|boolean|Hiermee wordt aangegeven of het abonnement is verlopen.|  
|`IsRejected`|boolean|Hiermee wordt aangegeven of de abonnements aanvraag is geweigerd.|  
|`CancelUrl`|string|De relatieve URL om het abonnement te annuleren.|  
|`RenewUrl`|string|De relatieve URL voor het vernieuwen van het abonnement.|  
  
##  <a name="SubscriptionSummary"></a>Samen vatting van abonnement  
 De `subscription summary` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Id`|string|Resource-id. Hiermee wordt het abonnement uniek geïdentificeerd binnen het huidige API Management service-exemplaar. De waarde is een geldige relatieve URL in de notatie van `subscriptions/{sid}` waar `{sid}` is een abonnements-id. Deze eigenschap is alleen-lezen.|  
|`DisplayName`|string|De weergave naam van het abonnement|  
  
##  <a name="UserAccountInfo"></a>Gegevens van gebruikers account  
 De `user account info` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`FirstName`|string|Voor naam. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`LastName`|string|Achternaam. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`Email`|string|E-mailadres. Mag niet leeg zijn en moet uniek zijn binnen het service-exemplaar. De maximale lengte is 254 tekens.|  
|`Password`|string|Wacht woord voor gebruikers account.|  
|`NameIdentifier`|string|Account-id, hetzelfde als het e-mail adres van de gebruiker.|  
|`ProviderName`|string|Naam van verificatie provider.|  
|`IsBasicAccount`|boolean|Waar als dit account is geregistreerd met behulp van e-mail en wacht woord. ONWAAR als het account is geregistreerd met een provider.|  
  
##  <a name="UseSignIn"></a>Gebruiker aanmelden  
 De `user sign in` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`Email`|string|E-mailadres. Mag niet leeg zijn en moet uniek zijn binnen het service-exemplaar. De maximale lengte is 254 tekens.|  
|`Password`|string|Wacht woord voor gebruikers account.|  
|`ReturnUrl`|string|De URL van de pagina waarop de gebruiker op aanmelden heeft geklikt.|  
|`RememberMe`|boolean|Hiermee wordt aangegeven of de huidige gebruikers gegevens moeten worden opgeslagen.|  
|`RegistrationEnabled`|boolean|Hiermee wordt aangegeven of registratie is ingeschakeld.|  
|`DelegationEnabled`|boolean|Hiermee wordt aangegeven of gedelegeerde aanmelding is ingeschakeld.|  
|`DelegationUrl`|string|De gedelegeerde aanmeldings-URL als deze is ingeschakeld.|  
|`SsoSignUpUrl`|string|De URL voor eenmalige aanmelding voor de gebruiker, indien aanwezig.|  
|`AuxServiceUrl`|string|Als de huidige gebruiker een beheerder is, is dit een koppeling naar het service-exemplaar in het Azure Portal.|  
|`Providers`|Verzameling van [provider](#Provider) entiteiten|De verificatie providers voor deze gebruiker.|  
|`UserRegistrationTerms`|string|De voor waarden waaraan een gebruiker moet instemmen voordat ze zich kunnen aanmelden.|  
|`UserRegistrationTermsEnabled`|boolean|Of de voor waarden zijn ingeschakeld.|  
  
##  <a name="UserSignUp"></a>Gebruiker registreren  
 De `user sign up` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Description|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|Waarde die wordt gebruikt door het besturings element voor [registratie](api-management-page-controls.md#sign-up)registratie.|  
|`Password`|string|Wacht woord voor gebruikers account.|  
|`PasswordVerdictLevel`|nummer|Waarde die wordt gebruikt door het besturings element voor [registratie](api-management-page-controls.md#sign-up)registratie.|  
|`UserRegistrationTerms`|string|De voor waarden waaraan een gebruiker moet instemmen voordat ze zich kunnen aanmelden.|  
|`UserRegistrationTermsOptions`|nummer|Waarde die wordt gebruikt door het besturings element voor [registratie](api-management-page-controls.md#sign-up)registratie.|  
|`ConsentAccepted`|boolean|Waarde die wordt gebruikt door het besturings element voor [registratie](api-management-page-controls.md#sign-up)registratie.|  
|`Email`|string|E-mailadres. Mag niet leeg zijn en moet uniek zijn binnen het service-exemplaar. De maximale lengte is 254 tekens.|  
|`FirstName`|string|Voor naam. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`LastName`|string|Achternaam. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`UserData`|string|Waarde die wordt gebruikt door het [registratie](api-management-page-controls.md#sign-up) besturings element.|  
|`NameIdentifier`|string|Waarde die wordt gebruikt door het besturings element voor [registratie](api-management-page-controls.md#sign-up)registratie.|  
|`ProviderName`|string|Naam van verificatie provider.|

## <a name="next-steps"></a>Volgende stappen
Zie [de API Management ontwikkelaars portal aanpassen met behulp van sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.

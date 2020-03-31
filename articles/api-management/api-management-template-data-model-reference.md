---
title: Verwijzing naar azure API Management-sjabloongegevensmodel | Microsoft Documenten
description: Meer informatie over de entiteit en typerepresentaties voor algemene items die worden gebruikt in de gegevensmodellen voor de ontwikkelaarsportalsjablonen in Azure API-beheer.
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
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 5625ff7e4fc51b9b6b894698719247902a480f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243937"
---
# <a name="azure-api-management-template-data-model-reference"></a>Verwijzing naar sjabloongegevensmodel azure API-beheer
In dit onderwerp worden de entiteit en het typerepresentaties beschreven voor algemene items die worden gebruikt in de gegevensmodellen voor de ontwikkelaarsportalsjablonen in Azure API Management.  
  
 Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)voor meer informatie over het werken met sjablonen.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Naslaginformatie

-   [Api](#API)  
-   [API-overzicht](#APISummary)  
-   [Toepassing](#Application)  
-   [Bijlage](#Attachment)  
-   [Codevoorbeeld](#Sample)  
-   [Opmerking](#Comment)  
-   [Filteren](#Filtering)  
-   [Header](#Header)  
-   [HTTP-aanvraag](#HTTPRequest)  
-   [HTTP-antwoord](#HTTPResponse)  
-   [Probleem](#Issue)  
-   [Bewerking](#Operation)  
-   [Menu Bewerking](#Menu)  
-   [Menu-item Bewerking](#MenuItem)  
-   [Zoekresultaten oproepen](#Paging)  
-   [Parameter](#Parameter)  
-   [Product](#Product)  
-   [Provider](#Provider)  
-   [Vertegenwoordiging](#Representation)  
-   [Abonnement](#Subscription)  
-   [Overzicht van abonnementen](#SubscriptionSummary)  
-   [Gebruikersaccountgegevens](#UserAccountInfo)  
-   [Aanmelding door gebruiker](#UseSignIn)  
-   [Aanmelden door gebruiker](#UserSignUp)  
  
##  <a name="api"></a><a name="API"></a>Api  
 De `API` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`id`|tekenreeks|Resource-id. Identificeert de API op unieke wijze binnen het huidige API Management-serviceexemplaar. De waarde is een geldige relatieve `apis/{id}` `{id}` URL in de indeling van waar een API-id is. Deze eigenschap is alleen-lezen.|  
|`name`|tekenreeks|Naam van de API. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`description`|tekenreeks|Beschrijving van de API. Mag niet leeg zijn. Kan HTML-opmaaktags bevatten. De maximale lengte is 1000 tekens.|  
|`serviceUrl`|tekenreeks|Absolute URL van de backend-service die deze API implementeert.|  
|`path`|tekenreeks|Relatieve URL die deze API en alle resourcepaden binnen de API-serviceinstantie uniek identificeert. Het wordt toegevoegd aan de URL van api-eindpuntbasis die is opgegeven tijdens het maken van de service-instantie om een openbare URL voor deze API te vormen.|  
|`protocols`|array van het aantal|Beschrijft op welke protocollen de bewerkingen in deze API kunnen worden aangeroepen. Toegestane waarden `1 - http` zijn `2 - https`en , of beide.|  
|`authenticationSettings`|[Verificatie-instellingen voor autorisatieserver](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Verzameling van verificatie-instellingen die in deze API zijn opgenomen.|  
|`subscriptionKeyParameterNames`|object|Optionele eigenschap die kan worden gebruikt om aangepaste namen op te geven voor query- en/of koptekstparameters die de abonnementssleutel bevatten. Wanneer deze eigenschap aanwezig is, moet deze ten minste één van de twee volgende eigenschappen bevatten.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a>API-overzicht  
 De `API summary` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`id`|tekenreeks|Resource-id. Identificeert de API op unieke wijze binnen het huidige API Management-serviceexemplaar. De waarde is een geldige relatieve `apis/{id}` `{id}` URL in de indeling van waar een API-id is. Deze eigenschap is alleen-lezen.|  
|`name`|tekenreeks|Naam van de API. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`description`|tekenreeks|Beschrijving van de API. Mag niet leeg zijn. Kan HTML-opmaaktags bevatten. De maximale lengte is 1000 tekens.|  
  
##  <a name="application"></a><a name="Application"></a>Toepassing  
 De `application` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Id`|tekenreeks|De unieke id van de toepassing.|  
|`Title`|tekenreeks|De titel van de aanvraag.|  
|`Description`|tekenreeks|De beschrijving van de aanvraag.|  
|`Url`|URI|De URI voor de toepassing.|  
|`Version`|tekenreeks|Versie-informatie voor de toepassing.|  
|`Requirements`|tekenreeks|Een beschrijving van de vereisten voor de aanvraag.|  
|`State`|getal|De huidige status van de toepassing.<br /><br /> - 0 - Geregistreerd<br /><br /> - 1 - Ingezonden<br /><br /> - 2 - Gepubliceerd<br /><br /> - 3 - Afgewezen<br /><br /> - 4 - Niet gepubliceerd|  
|`RegistrationDate`|DateTime|De datum en het tijdstip waarop de aanvraag is geregistreerd.|  
|`CategoryId`|getal|De categorie van de applicatie (Financiën, entertainment, enz.)|  
|`DeveloperId`|tekenreeks|De unieke id van de ontwikkelaar die de aanvraag heeft ingediend.|  
|`Attachments`|Verzameling van [entiteiten voor bijlagen.](#Attachment)|Eventuele bijlagen voor de toepassing, zoals screenshots of pictogrammen.|  
|`Icon`|[Bijlage](#Attachment)|Het pictogram voor de toepassing.|  
  
##  <a name="attachment"></a><a name="Attachment"></a>Bijlage  
 De `attachment` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`UniqueId`|tekenreeks|De unieke id voor de bijlage.|  
|`Url`|tekenreeks|De URL van de resource.|  
|`Type`|tekenreeks|Het type bijlage.|  
|`ContentType`|tekenreeks|Het mediatype van de bijlage.|  
  
##  <a name="code-sample"></a><a name="Sample"></a>Codevoorbeeld  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`title`|tekenreeks|De naam van de bewerking.|  
|`snippet`|tekenreeks|Deze eigenschap is afgeschaft en mag niet worden gebruikt.|  
|`brush`|tekenreeks|Welke codesyntaxiskleursjabloon moet worden gebruikt bij het weergeven van het codevoorbeeld. De toegestane `plain`waarden `php` `java`zijn `xml` `objc`, `python` `ruby`, `csharp`, , , en .|  
|`template`|tekenreeks|De naam van deze voorbeeldsjabloon voor code.|  
|`body`|tekenreeks|Een tijdelijke aanduiding voor het codevoorbeeldgedeelte van het fragment.|  
|`method`|tekenreeks|De HTTP-methode van de bewerking.|  
|`scheme`|tekenreeks|Het protocol dat moet worden gebruikt voor de bewerkingsaanvraag.|  
|`path`|tekenreeks|Het pad van de operatie.|  
|`query`|tekenreeks|Querytekenreeksvoorbeeld met gedefinieerde parameters.|  
|`host`|tekenreeks|De URL van de API Management-servicegateway voor de API die deze bewerking bevat.|  
|`headers`|Verzameling van [entiteiten met kopteksten.](#Header)|Kopteksten voor deze bewerking.|  
|`parameters`|Verzameling [Parameter](#Parameter) van parameterentiteiten.|Parameters die voor deze bewerking zijn gedefinieerd.|  
  
##  <a name="comment"></a><a name="Comment"></a>Commentaar  
 De `API` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Id`|getal|De ID van de opmerking.|  
|`CommentText`|tekenreeks|Het lichaam van de opmerking. Kan HTML bevatten.|  
|`DeveloperCompany`|tekenreeks|De bedrijfsnaam van de ontwikkelaar.|  
|`PostedOn`|DateTime|De datum en tijd waarop de opmerking is geplaatst.|  
  
##  <a name="issue"></a><a name="Issue"></a>Probleem  
 De `issue` entiteit heeft de volgende eigenschappen.  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Id`|tekenreeks|De unieke id voor het probleem.|  
|`ApiID`|tekenreeks|De ID voor de API waarvoor dit probleem is gemeld.|  
|`Title`|tekenreeks|Titel van de kwestie.|  
|`Description`|tekenreeks|Beschrijving van het probleem.|  
|`SubscriptionDeveloperName`|tekenreeks|Voornaam van de ontwikkelaar die het probleem heeft gemeld.|  
|`IssueState`|tekenreeks|De huidige stand van zaken. Mogelijke waarden worden voorgesteld, geopend, gesloten.|  
|`ReportedOn`|DateTime|De datum en tijd waarop het probleem is gemeld.|  
|`Comments`|Verzameling van [entiteiten voor opmerkingen.](#Comment)|Opmerkingen over deze kwestie.|  
|`Attachments`|Verzameling van [entiteiten voor bijlagen.](api-management-template-data-model-reference.md#Attachment)|Eventuele bijlagen bij het probleem.|  
|`Services`|Verzameling van [API-entiteiten.](#API)|De API's waarop de gebruiker het probleem heeft ingediend.|  
  
##  <a name="filtering"></a><a name="Filtering"></a>Filteren  
 De `filtering` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Pattern`|tekenreeks|De huidige zoekterm; of `null` als er geen zoekterm is.|  
|`Placeholder`|tekenreeks|De tekst die in het zoekvak moet worden weergegeven wanneer er geen zoekterm is opgegeven.|  
  
##  <a name="header"></a><a name="Header"></a>Header  
 In deze sectie `parameter` wordt de weergave beschreven.  
  
|Eigenschap|Type|Beschrijving|  
|--------------|-----------------|----------|  
|`name`|tekenreeks|Parameternaam.|  
|`description`|tekenreeks|Parameterbeschrijving.|  
|`value`|tekenreeks|Koptekstwaarde.|  
|`typeName`|tekenreeks|Gegevenstype koptekstwaarde.|  
|`options`|tekenreeks|Opties.|  
|`required`|booleaans|Of de koptekst vereist is.|  
|`readOnly`|booleaans|Of de koptekst alleen-lezen is.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a>HTTP-aanvraag  
 In deze sectie `request` wordt de weergave beschreven.  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`description`|tekenreeks|Beschrijving van de aanvraag voor de bewerking.|  
|`headers`|array [van](#Header) header-entiteiten.|Kopteksten aanvragen.|  
|`parameters`|matrix van [parameter](#Parameter)|Verzameling van parameters voor bewerkingsaanvragen.|  
|`representations`|array van [vertegenwoordiging](#Representation)|Verzameling van voorstellingen van bewerkingsaanvragen.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a>HTTP-antwoord  
 In deze sectie `response` wordt de weergave beschreven.  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`statusCode`|positief geheel getal|Statuscode voor bewerkingsrespons.|  
|`description`|tekenreeks|Beschrijving van de reactie van de bewerking.|  
|`representations`|array van [vertegenwoordiging](#Representation)|Verzameling van voorstellingen van de operatierespons.|  
  
##  <a name="operation"></a><a name="Operation"></a>Bewerking  
 De `operation` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`id`|tekenreeks|Resource-id. Identificeert de bewerking op unieke wijze binnen de huidige API Management-serviceinstantie. De waarde is een geldige relatieve `apis/{aid}/operations/{id}` `{aid}` URL in de `{id}` indeling van waar een API-id is en een bewerkings-id is. Deze eigenschap is alleen-lezen.|  
|`name`|tekenreeks|Naam van de bewerking. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`description`|tekenreeks|Beschrijving van de bewerking. Mag niet leeg zijn. Kan HTML-opmaaktags bevatten. De maximale lengte is 1000 tekens.|  
|`scheme`|tekenreeks|Beschrijft op welke protocollen de bewerkingen in deze API kunnen worden aangeroepen. Toegestane waarden `http`zijn `https`, `http` , `https`of beide en .|  
|`uriTemplate`|tekenreeks|Relatieve URL-sjabloon die de doelbron voor deze bewerking identificeert. Kan parameters bevatten. Voorbeeld: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|tekenreeks|De URL van de API-beheergateway die de API host.|  
|`httpMethod`|tekenreeks|Operatie HTTP-methode.|  
|`request`|[HTTP-aanvraag](#HTTPRequest)|Een entiteit met aanvraaggegevens.|  
|`responses`|array van [HTTP-respons](#HTTPResponse)|Array met [HTTP-antwoordentiteiten.](#HTTPResponse)|  
  
##  <a name="operation-menu"></a><a name="Menu"></a>Menu Bewerking  
 De `operation menu` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`ApiId`|tekenreeks|De ID van de huidige API.|  
|`CurrentOperationId`|tekenreeks|De ID van de huidige bewerking.|  
|`Action`|tekenreeks|Het menutype.|  
|`MenuItems`|Verzameling van itementiteiten van [het menu Bewerking.](#MenuItem)|De bewerkingen voor de huidige API.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a>Menu-item Bewerking  
 De `operation menu item` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Id`|tekenreeks|De ID van de operatie.|  
|`Title`|tekenreeks|De beschrijving van de operatie.|  
|`HttpMethod`|tekenreeks|De Http-methode van de bewerking.|  
  
##  <a name="paging"></a><a name="Paging"></a>Paging  
 De `paging` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Page`|getal|Het huidige paginanummer.|  
|`PageSize`|getal|De maximale resultaten die op één pagina moeten worden weergegeven.|  
|`TotalItemCount`|getal|Het aantal objecten dat moet worden weergegeven.|  
|`ShowAll`|booleaans|Of alle resultaten op één pagina moeten worden gesho.|  
|`PageCount`|getal|Het aantal pagina's met resultaten.|  
  
##  <a name="parameter"></a><a name="Parameter"></a>Parameter  
 In deze sectie `parameter` wordt de weergave beschreven.  
  
|Eigenschap|Type|Beschrijving|  
|--------------|-----------------|----------|  
|`name`|tekenreeks|Parameternaam.|  
|`description`|tekenreeks|Parameterbeschrijving.|  
|`value`|tekenreeks|Parameterwaarde.|  
|`options`|tekenreeksmatrix|Waarden gedefinieerd voor queryparameterwaarden.|  
|`required`|booleaans|Hiermee geeft u op of parameter vereist is of niet.|  
|`kind`|getal|Of deze parameter een padparameter (1) of een querytekenreeksparameter (2) is.|  
|`typeName`|tekenreeks|Parametertype.|  
  
##  <a name="product"></a><a name="Product"></a>Product  
 De `product` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Id`|tekenreeks|Resource-id. Identificeert het product op unieke wijze binnen de huidige API Management-serviceinstantie. De waarde is een geldige relatieve `products/{pid}` `{pid}` URL in de notatie van waar een product-id is. Deze eigenschap is alleen-lezen.|  
|`Title`|tekenreeks|De naam van het product. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`Description`|tekenreeks|Beschrijving van het product. Mag niet leeg zijn. Kan HTML-opmaaktags bevatten. De maximale lengte is 1000 tekens.|  
|`Terms`|tekenreeks|Productgebruiksvoorwaarden. Ontwikkelaars die zich op het product proberen te abonneren, worden gepresenteerd en moeten deze voorwaarden accepteren voordat ze het abonnementsproces kunnen voltooien.|  
|`ProductState`|getal|Hiermee geeft u op of het product wordt gepubliceerd of niet. Gepubliceerde producten zijn vindbaar door ontwikkelaars op de ontwikkelaarsportal. Niet-gepubliceerde producten zijn alleen zichtbaar voor beheerders.<br /><br /> De toegestane waarden voor de productstatus zijn:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|booleaans|Hiermee geeft u op of een gebruiker meerdere abonnementen tegelijk op dit product kan hebben.|  
|`MultipleSubscriptionsCount`|getal|Maximaal aantal abonnementen op dit product dat een gebruiker tegelijkertijd mag hebben.|  
  
##  <a name="provider"></a><a name="Provider"></a>Provider  
 De `provider` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Properties`|tekenreekswoordenboek|Eigenschappen voor deze verificatieprovider.|  
|`AuthenticationType`|tekenreeks|Het type provider. (Azure Active Directory, Facebook login, Google Account, Microsoft Account, Twitter).|  
|`Caption`|tekenreeks|De naam van de provider weergeven.|  
  
##  <a name="representation"></a><a name="Representation"></a>Vertegenwoordiging  
 In deze sectie `representation`wordt een .  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`contentType`|tekenreeks|Hiermee geeft u bijvoorbeeld een geregistreerd of `application/xml`aangepast inhoudstype op voor deze weergave .|  
|`sample`|tekenreeks|Een voorbeeld van de vertegenwoordiging.|  
  
##  <a name="subscription"></a><a name="Subscription"></a>Abonnement  
 De `subscription` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Id`|tekenreeks|Resource-id. Identificeert het abonnement op unieke wijze binnen het huidige API Management-serviceexemplaar. De waarde is een geldige relatieve `subscriptions/{sid}` `{sid}` URL in de indeling van waar een abonnements-id is. Deze eigenschap is alleen-lezen.|  
|`ProductId`|tekenreeks|De productbron-id van het geabonneerde product. De waarde is een geldige relatieve `products/{pid}` `{pid}` URL in de notatie van waar een product-id is.|  
|`ProductTitle`|tekenreeks|De naam van het product. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`ProductDescription`|tekenreeks|Beschrijving van het product. Mag niet leeg zijn. Kan HTML-opmaaktags bevatten. De maximale lengte is 1000 tekens.|  
|`ProductDetailsUrl`|tekenreeks|Relatieve URL naar de productdetails.|  
|`state`|tekenreeks|De staat van het abonnement. Mogelijke staten zijn:<br /><br /> - `0 - suspended`– het abonnement is geblokkeerd en de abonnee kan geen API's van het product bellen.<br /><br /> - `1 - active`– het abonnement actief is.<br /><br /> - `2 - expired`– het abonnement de vervaldatum heeft bereikt en is gedeactiveerd.<br /><br /> - `3 - submitted`– de abonnementsaanvraag is gedaan door de ontwikkelaar, maar is nog niet goedgekeurd of afgewezen.<br /><br /> - `4 - rejected`– het abonnementsverzoek is geweigerd door een beheerder.<br /><br /> - `5 - cancelled`– het abonnement is opgezegd door de ontwikkelaar of beheerder.|  
|`DisplayName`|tekenreeks|De naam van het abonnement weergeven.|  
|`CreatedDate`|Datetime|De datum waarop het abonnement is gemaakt, in `2014-06-24T16:25:00Z`ISO 8601-indeling: .|  
|`CanBeCancelled`|booleaans|Of het abonnement kan worden opgezegd door de huidige gebruiker.|  
|`IsAwaitingApproval`|booleaans|Of het abonnement in afwachting is van goedkeuring.|  
|`StartDate`|Datetime|De begindatum voor het abonnement, in ISO `2014-06-24T16:25:00Z`8601-indeling: .|  
|`ExpirationDate`|Datetime|De vervaldatum voor het abonnement, in ISO `2014-06-24T16:25:00Z`8601-indeling: .|  
|`NotificationDate`|Datetime|De aanmeldingsdatum voor het abonnement, in `2014-06-24T16:25:00Z`ISO 8601-indeling: .|  
|`primaryKey`|tekenreeks|De primaire abonnementssleutel. De maximale lengte is 256 tekens.|  
|`secondaryKey`|tekenreeks|De secundaire abonnementssleutel. De maximale lengte is 256 tekens.|  
|`CanBeRenewed`|booleaans|Of het abonnement kan worden verlengd door de huidige gebruiker.|  
|`HasExpired`|booleaans|Of het abonnement is verlopen.|  
|`IsRejected`|booleaans|Of het abonnementsverzoek is afgewezen.|  
|`CancelUrl`|tekenreeks|De relatieve url om het abonnement op te zeggen.|  
|`RenewUrl`|tekenreeks|De relatieve url om het abonnement te verlengen.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a>Overzicht van abonnementen  
 De `subscription summary` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Id`|tekenreeks|Resource-id. Identificeert het abonnement op unieke wijze binnen het huidige API Management-serviceexemplaar. De waarde is een geldige relatieve `subscriptions/{sid}` `{sid}` URL in de indeling van waar een abonnements-id is. Deze eigenschap is alleen-lezen.|  
|`DisplayName`|tekenreeks|De weergavenaam van het abonnement|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a>Gebruikersaccountgegevens  
 De `user account info` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`FirstName`|tekenreeks|Voornaam. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`LastName`|tekenreeks|Achternaam. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`Email`|tekenreeks|E-mailadres. Mag niet leeg zijn en moet uniek zijn binnen de service-instantie. De maximale lengte is 254 tekens.|  
|`Password`|tekenreeks|Wachtwoord van het gebruikersaccount.|  
|`NameIdentifier`|tekenreeks|Account-id, hetzelfde als de e-mail van de gebruiker.|  
|`ProviderName`|tekenreeks|Naam van de verificatieprovider.|  
|`IsBasicAccount`|booleaans|True als dit account is geregistreerd met e-mail en wachtwoord; false als het account is geregistreerd via een provider.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a>Aanmelden door gebruiker  
 De `user sign in` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`Email`|tekenreeks|E-mailadres. Mag niet leeg zijn en moet uniek zijn binnen de service-instantie. De maximale lengte is 254 tekens.|  
|`Password`|tekenreeks|Wachtwoord van het gebruikersaccount.|  
|`ReturnUrl`|tekenreeks|De URL van de pagina waarop de gebruiker op zich heeft aangemeld.|  
|`RememberMe`|booleaans|Of u de gegevens van de huidige gebruiker wilt opslaan.|  
|`RegistrationEnabled`|booleaans|Of registratie is ingeschakeld.|  
|`DelegationEnabled`|booleaans|Of gedelegeerd aanmelden is ingeschakeld.|  
|`DelegationUrl`|tekenreeks|De gedelegeerde aanmeldingsurl, indien ingeschakeld.|  
|`SsoSignUpUrl`|tekenreeks|Het enige teken op URL voor de gebruiker, indien aanwezig.|  
|`AuxServiceUrl`|tekenreeks|Als de huidige gebruiker een beheerder is, is dit een koppeling naar de service-instantie in de Azure-portal.|  
|`Providers`|Verzameling van entiteiten van [aanbieders](#Provider)|De verificatieproviders voor deze gebruiker.|  
|`UserRegistrationTerms`|tekenreeks|Voorwaarden waarmee een gebruiker moet instemmen voordat hij zich aanmeldt.|  
|`UserRegistrationTermsEnabled`|booleaans|Of termen zijn ingeschakeld.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a>Aanmelden door gebruiker  
 De `user sign up` entiteit heeft de volgende eigenschappen:  
  
|Eigenschap|Type|Beschrijving|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|booleaans|Waarde die [sign-up](api-management-page-controls.md#sign-up)wordt gebruikt door het aanmeldingsbesturingselement.|  
|`Password`|tekenreeks|Wachtwoord van het gebruikersaccount.|  
|`PasswordVerdictLevel`|getal|Waarde die [sign-up](api-management-page-controls.md#sign-up)wordt gebruikt door het aanmeldingsbesturingselement.|  
|`UserRegistrationTerms`|tekenreeks|Voorwaarden waarmee een gebruiker moet instemmen voordat hij zich aanmeldt.|  
|`UserRegistrationTermsOptions`|getal|Waarde die [sign-up](api-management-page-controls.md#sign-up)wordt gebruikt door het aanmeldingsbesturingselement.|  
|`ConsentAccepted`|booleaans|Waarde die [sign-up](api-management-page-controls.md#sign-up)wordt gebruikt door het aanmeldingsbesturingselement.|  
|`Email`|tekenreeks|E-mailadres. Mag niet leeg zijn en moet uniek zijn binnen de service-instantie. De maximale lengte is 254 tekens.|  
|`FirstName`|tekenreeks|Voornaam. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`LastName`|tekenreeks|Achternaam. Mag niet leeg zijn. De maximale lengte is 100 tekens.|  
|`UserData`|tekenreeks|Waarde die wordt gebruikt door [het besturingselement voor aanmelding.](api-management-page-controls.md#sign-up)|  
|`NameIdentifier`|tekenreeks|Waarde die [sign-up](api-management-page-controls.md#sign-up)wordt gebruikt door het aanmeldingsbesturingselement.|  
|`ProviderName`|tekenreeks|Naam van de verificatieprovider.|

## <a name="next-steps"></a>Volgende stappen
Zie [De API Management-ontwikkelaarsportal aanpassen met sjablonen](api-management-developer-portal-templates.md)voor meer informatie over het werken met sjablonen.

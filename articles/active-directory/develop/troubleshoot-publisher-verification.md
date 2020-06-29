---
title: Problemen met de verificatie van de uitgever oplossen-micro soft Identity-platform | Azure
description: Hierin wordt beschreven hoe u de verificatie van uitgevers (preview) voor micro soft Identity platform kunt oplossen door Microsoft Graph-Api's aan te roepen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: dcdce6ee6683c5770f97f5f3dc20e1c9b409ead0
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85477036"
---
# <a name="troubleshoot-publisher-verification-preview"></a>Problemen met verificatie van uitgever oplossen (preview)
Als u het proces niet kunt volt ooien of onverwachte problemen ondervindt met de verificatie van de [Uitgever (preview)](publisher-verification-overview.md), moet u eerst het volgende doen als u fouten ontvangt of een onverwacht gedrag ziet: 

1. Bekijk de [vereisten](publisher-verification-overview.md#requirements) en zorg ervoor dat aan hen is voldaan.

1. Lees de instructies voor het [markeren van een app als uitgever is geverifieerd](mark-app-as-publisher-verified.md) en controleer of alle stappen zijn uitgevoerd.

1. Bekijk de lijst met [veelvoorkomende problemen](#common-issues).

1. Reproduceer de aanvraag met [Graph Explorer](#making-microsoft-graph-api-calls) om aanvullende informatie te verzamelen en eventuele problemen in de gebruikers interface op te lossen.

## <a name="common-issues"></a>Algemene problemen
Hieronder vindt u enkele veelvoorkomende problemen die zich kunnen voordoen tijdens het proces. 

- **Ik weet niet wat mijn Microsoft Partner Network-ID is (MPN-ID) of ik ben niet wie de primaire contact persoon voor het account is** 
    1. Ga naar de [registratie pagina voor MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Meld u aan met een gebruikers account in de primaire Azure AD-Tenant van de organisatie 
    1. Als er al een MPN-account bestaat, wordt dit herkend en wordt u aan het account toegevoegd 
    1. Navigeer naar de [pagina Partner profiel](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) waar de contact persoon voor de MPN-id en het primaire account wordt weer gegeven

- **Ik weet niet wie mijn globale beheerder van Azure AD (ook wel bedrijfs beheerder of Tenant beheerder is), hoe vind ik ze? Wat is de beheerder van de app of een andere beheerdersrol?**
    1. Meld u aan bij de [Azure AD-Portal](https://aad.portal.azure.com) met behulp van een gebruikers account in de primaire Tenant van uw organisatie
    1. Navigeer naar [Role Management](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Klik op globale beheerder of de gewenste beheerdersrol
    1. De lijst met gebruikers die zijn toegewezen aan die rol wordt weer gegeven

- **Ik weet niet wie de beheerder (s) voor mijn MPN-account** Ga naar de [MPN-gebruikers beheer pagina](https://partner.microsoft.com/en-us/pcv/users) en filter de gebruikers lijst om te zien welke gebruikers zich in verschillende beheerders rollen bevinden.

- **Er wordt een fout bericht weer gegeven met de mede deling dat mijn MPN-ID ongeldig is of dat ik geen toegang heb.**
    1. Ga naar uw [partner profiel](https://partner.microsoft.com/en-us/pcv/accountsettings/connectedpartnerprofile) en controleer het volgende: 
        - De MPN-ID is juist. 
        - Er zijn geen fouten of acties die in behandeling zijn, en de verificatie status onder juridisch zakelijk profiel en partner informatie hebben beide de tekst "geautoriseerd" of "geslaagd".
    1. Ga naar de [pagina MPN Tenant beheer](https://partner.microsoft.com/en-us/dashboard/account/v3/tenantmanagement) en bevestig dat de Tenant waarin de app is geregistreerd en dat u zich aanmeldt met een gebruikers account, zich in de lijst met gekoppelde tenants bevindt.
    1. Ga naar de [pagina gebruikers beheer van MPN](https://partner.microsoft.com/en-us/pcv/users) en bevestig dat de gebruiker waarbij u zich aanmeldt, een globale beheerder, MPN beheerder of account beheerder is.

- **Wanneer ik me aanmeld in de Azure AD-Portal, zie ik geen geregistreerde apps. Waarom?** 
    Uw app-registraties zijn mogelijk gemaakt met een ander gebruikers account of een andere Tenant. Zorg ervoor dat u bent aangemeld met het juiste account in de Tenant waar de app-registraties zijn gemaakt.

- **Hoe kan ik weet wie de eigenaar van een app-registratie in azure AD is?** 
    Als u zich aanmeldt bij een Tenant waar de app is geregistreerd, gaat u naar de Blade app-registraties, klikt u op een app en klikt u vervolgens op eigen aars.

## <a name="making-microsoft-graph-api-calls"></a>Microsoft Graph-API-aanroepen maken 

Als u een probleem ondervindt, maar geen inzicht kunt krijgen in de redenen die zijn gebaseerd op wat u in de gebruikers interface ziet, kan het nuttig zijn om verdere problemen op te lossen met behulp van Microsoft Graph-aanroepen om dezelfde bewerkingen uit te voeren die u in de app-registratie Portal kunt uitvoeren. Tijdens de preview-fase zijn deze Api's alleen beschikbaar op het/Beta-eind punt van Microsoft Graph.  

De eenvoudigste manier om deze aanvragen te maken, is met [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). U kunt ook andere opties gebruiken, zoals het gebruik van [postman](https://www.postman.com/)of [het aanroepen van een webaanvraag](/powershell/module/microsoft.powershell.utility/invoke-webrequest?view=powershell-7)met behulp van Power shell.  

U kunt Microsoft Graph gebruiken om de geverifieerde Uitgever van uw app in te stellen en op te heffen en het resultaat na het uitvoeren van een van deze bewerkingen te controleren. Het resultaat kan worden weer gegeven op het [toepassings](/graph/api/resources/application?view=graph-rest-beta) object dat overeenkomt met de registratie van uw app en alle [service-principals](/graph/api/resources/serviceprincipal?view=graph-rest-beta) die zijn geïnstantieerd vanuit die app. Zie voor meer informatie over de relatie tussen deze objecten: [toepassings-en Service-Principal-objecten in azure Active Directory](app-objects-and-service-principals.md).  

Hier volgen enkele voor beelden van nuttige aanvragen:  

### <a name="set-verified-publisher"></a>Geverifieerde Uitgever instellen 

Aanvraag

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Antwoord 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* is uw MPN-id. 

### <a name="unset-verified-publisher"></a>Geverifieerde Uitgever uitschakelen 

Aanvraag:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Antwoord 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Geverifieerde Uitgever gegevens van de toepassing ophalen 
 
```
GET https://graph.microsoft.com/beta/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Geverifieerde Uitgever gegevens ophalen van de Service-Principal 
```
GET https://graph.microsoft.com/beta/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Fout referentie 

Hier volgt een lijst met mogelijke fout codes die u kunt ontvangen, hetzij bij het oplossen van problemen met Microsoft Graph of door lopen van het proces in de app-registratie Portal.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess     

De MPN-ID die u hebt gegeven ( <MPNID> ) bestaat niet of u hebt er geen toegang toe. Geef een geldige MPN-ID op en probeer het opnieuw. 

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound     

De MPN-ID die u hebt gegeven ( <MPNID> ) is niet geldig. Geef een geldige MPN-ID op en probeer het opnieuw. 

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid    

De MPN-ID die u hebt gegeven ( <MPNID> ) is niet geldig. Geef een geldige MPN-ID op en probeer het opnieuw. 

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted  

De MPN-ID ( <MPNID> ) die u hebt ingevoerd, heeft het hebben-proces nog niet voltooid. Voltooi dit proces in het partner centrum en probeer het opnieuw. 

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount  

De MPN-ID die u hebt gegeven ( <MPNID> ) is niet geldig. Geef een geldige MPN-ID op en probeer het opnieuw. 

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount    

De MPN-ID die u hebt gegeven ( <MPNID> ) is niet geldig. Geef een geldige MPN-ID op en probeer het opnieuw. 

### <a name="applicationnotfound"></a>ApplicationNotFound  

De doel toepassing ( <AppId> ) is niet gevonden. Geef een geldige toepassings-ID op en probeer het opnieuw. 

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed  

Deze functie wordt niet ondersteund in een Azure AD B2C Tenant. 

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed    

Deze functie wordt niet ondersteund in een door e-mail geverifieerde Tenant. 

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication   

Voor de doel toepassing ( <AppId> ) moet een uitgevers domein zijn ingesteld. Stel een uitgever domein in en probeer het opnieuw. 

### <a name="publisherdomainisnotdnsverified"></a>PublisherDomainIsNotDNSVerified  

Het Uitgever domein () van de doel toepassing <publisherDomain> is geen geverifieerd domein in deze Tenant. Verifieer een Tenant domein met behulp van DNS-verificatie en probeer het opnieuw. 

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch  

Het Uitgever domein () van de doel toepassing <publisherDomain> komt niet overeen met het domein dat wordt gebruikt om e-mail verificatie uit te voeren in Partner Center ( <pcDomain> ). Zorg ervoor dat deze domeinen overeenkomen en probeer het opnieuw. 

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher   

U bent niet gemachtigd om de eigenschap geverifieerde uitgever in te stellen voor de toepassing ( <AppId> ) 

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided  

De MPN-ID is niet in de aanvraag tekst of het type van de aanvraag inhoud is niet ' application/json '. 

### <a name="msanotsupported"></a>MSANotSupported  

Deze functie wordt niet ondersteund voor micro soft-consumenten accounts. Alleen toepassingen die zijn geregistreerd in azure AD door een Azure AD-gebruiker, worden ondersteund. 

## <a name="next-steps"></a>Volgende stappen

Als u alle voor gaande informatie hebt gecontroleerd en nog steeds een fout ontvangt van Microsoft Graph, moet u zoveel mogelijk van de volgende informatie verzamelen die betrekking heeft op de mislukte aanvraag en [contact opnemen met micro soft ondersteuning](developer-support-help-options.md#open-a-support-request).

- Tijdstempel 
- CorrelationId 
- ObjectID of UserPrincipalName van aangemelde gebruiker 
- ObjectId van doel toepassing
- AppId van doel toepassing
- TenantId waar de app is geregistreerd
- MPN-id
- REST-aanvraag wordt gedaan 
- De fout code en het bericht worden geretourneerd 

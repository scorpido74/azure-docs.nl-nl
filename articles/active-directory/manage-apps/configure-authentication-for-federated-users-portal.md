---
title: Automatische versnelling van aanmelding configureren met Behulp van Home Realm Discovery
description: Meer informatie over het configureren van Home Realm Discovery-beleid voor Azure Active Directory-verificatie voor federatieve gebruikers, inclusief automatische versnelling en domeinhints.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mimart
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60bfc964ffc394b3f79c9d279158003f383b7331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943431"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Azure Active Directory-aanmeldingsgedrag configureren voor een toepassing met behulp van een beleid voor Detectie van Thuisrealm

In dit artikel vindt u een inleiding tot het configureren van Azure Active Directory-verificatiegedrag voor federatieve gebruikers. Het omvat de configuratie van automatische acceleratie en authenticatie beperkingen voor gebruikers in federatieve domeinen.

## <a name="home-realm-discovery"></a>Home Realm Discovery
Home Realm Discovery (HRD) is het proces waarmee Azure Active Directory (Azure AD) kan bepalen waar een gebruiker zich moet verifiëren bij aanmeldingstijd.  Wanneer een gebruiker zich aanmeldt bij een Azure AD-tenant om toegang te krijgen tot een bron of bij de algemene aanmeldingspagina van Azure AD, typt hij een gebruikersnaam (UPN). Azure AD gebruikt dat om te ontdekken waar de gebruiker zich moet aanmelden. 

Mogelijk moet de gebruiker naar een van de volgende locaties worden gebracht om te worden geverifieerd:

- De huistenant van de gebruiker (kan dezelfde tenant zijn als de bron die de gebruiker probeert te openen). 

- Microsoft-account.  De gebruiker is een gast in de resourcetenant.

-  Een on-premises identiteitsprovider zoals Active Directory Federation Services (AD FS).

- Een andere identiteitsprovider die wordt gefedereerd met de Azure AD-tenant.

## <a name="auto-acceleration"></a>Automatische versnelling 
Sommige organisaties configureren domeinen in hun Azure Active Directory-tenant om te reageren op een andere IdP, zoals AD FS voor gebruikersverificatie.  

Wanneer een gebruiker zich aanmeldt bij een toepassing, wordt deze eerst een aanmeldingspagina van Azure AD gepresenteerd. Nadat ze hun UPN hebben getypt, als ze zich in een federatief domein bevinden, worden ze vervolgens naar de aanmeldingspagina van de IdP die dat domein bedient, meegenomen. Onder bepaalde omstandigheden willen beheerders gebruikers mogelijk naar de aanmeldingspagina leiden wanneer ze zich aanmelden bij specifieke toepassingen. 

Hierdoor kunnen gebruikers de oorspronkelijke Azure Active Directory-pagina overslaan. Dit proces wordt aangeduid als "sign-in auto-acceleratie."

In gevallen waarin de tenant wordt gefedereerd naar een andere IdP voor aanmelding, maakt automatische versnelling de aanmelding van de gebruiker gestroomlijnder.  U automatische versnelling configureren voor afzonderlijke toepassingen.

>[!NOTE]
>Als u een toepassing configureert voor automatische versnelling, kunnen gastgebruikers zich niet aanmelden. Als u een gebruiker rechtstreeks naar een gefedereerde IdP voor verificatie brengt, is er geen manier om terug te keren naar de aanmeldingspagina van Azure Active Directory. Gastgebruikers die mogelijk moeten worden doorverwezen naar andere tenants of een externe IdP, zoals een Microsoft-account, kunnen zich niet aanmelden bij die toepassing omdat ze de stap Home Realm Discovery overslaan.  

Er zijn twee manieren om automatische acceleratie te regelen naar een gefedereerde IdP:   

- Gebruik een domeinhint op verificatieaanvragen voor een toepassing. 
- Configureer een Home Realm Discovery-beleid om automatische versnelling in te schakelen.

### <a name="domain-hints"></a>Domeinhints    
Domeinhints zijn richtlijnen die zijn opgenomen in het verificatieverzoek van een toepassing. Ze kunnen worden gebruikt om de gebruiker te versnellen naar hun federatieve IdP aanmeldingspagina. Of ze kunnen worden gebruikt door een multi-tenant toepassing om de gebruiker te versnellen rechtstreeks naar de branded Azure AD aanmeldingspagina voor hun tenant.  

De toepassing 'largeapp.com' kan hun klanten bijvoorbeeld toegang geven tot de toepassing op een aangepaste URL 'contoso.largeapp.com'. De app kan ook een domeinhint bevatten om te contoso.com in het verificatieverzoek. 

De syntaxis van domeinhints is afhankelijk van het protocol dat wordt gebruikt en het is meestal geconfigureerd in de toepassing.

**WS-Federation**: whr=contoso.com in de querytekenreeks.

**SAML:** een SAML-verificatieverzoek dat een domeinhint bevat of een querytekenreeks whr=contoso.com.

**Open ID Connect:** een querytekenreeks domain_hint=contoso.com. 

Als een domeinhint is opgenomen in de verificatieaanvraag van de toepassing en de tenant wordt gefedereerd met dat domein, probeert Azure AD aanmelding om te leiden naar de IdP die voor dat domein is geconfigureerd. 

Als de domeinhint niet verwijst naar een geverifieerd federatief domein, wordt deze genegeerd en wordt de normale Home Realm Discovery aangeroepen.

Zie het [blog Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)voor meer informatie over automatisch accelereren met behulp van de domeinhints die worden ondersteund door Azure Active Directory.

>[!NOTE]
>Als een domeinhint is opgenomen in een verificatieverzoek, overschrijft de aanwezigheid automatisch versnelling die is ingesteld voor de toepassing in HRD-beleid.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Home Realm Discovery-beleid voor automatische versnelling
Sommige toepassingen bieden geen manier om het verificatieverzoek te configureren dat ze uitzenden. In deze gevallen is het niet mogelijk om domeinhints te gebruiken om automatische versnelling te regelen. Automatische versnelling kan worden geconfigureerd via beleid om hetzelfde gedrag te bereiken.  

## <a name="enable-direct-authentication-for-legacy-applications"></a>Directe verificatie inschakelen voor oudere toepassingen
Aanbevolen procedures zijn voor toepassingen om AAD-bibliotheken en interactieve aanmelding te gebruiken om gebruikers te verifiëren. De bibliotheken zorgen voor de gefedereerde gebruikersstromen.  Soms worden oudere toepassingen niet geschreven om federatie te begrijpen. Ze voeren geen detectie van thuisgebieden uit en werken niet samen met het juiste gefedereerde eindpunt om een gebruiker te verifiëren. Als u hiervoor kiest, u HRD-beleid gebruiken om specifieke oudere toepassingen in te schakelen die gebruikersnaam-/wachtwoordreferenties indienen om rechtstreeks te verifiëren met Azure Active Directory. Password Hash Sync moet zijn ingeschakeld. 

> [!IMPORTANT]
> Schakel alleen directe verificatie in als wachtwoordhashsynchronisatie is ingeschakeld en u weet dat het goed is om deze toepassing te verifiëren zonder beleid dat is geïmplementeerd door uw on-premises IdP. Als u Wachtwoordhashsynchronisatie uitschakelt of Adreslijstsynchronisatie met AD Connect uitschakelt om welke reden dan ook, moet u dit beleid verwijderen om te voorkomen dat directe verificatie mogelijk is met behulp van een verouderde wachtwoordhash.

## <a name="set-hrd-policy"></a>HRD-beleid instellen
Er zijn drie stappen om HRD-beleid in te stellen voor een toepassing voor federatieve aanmeldingsautomatische versnelling of directe cloudgebaseerde toepassingen:

1. Maak een HRD-beleid.

2. Zoek de serviceprincipal waaraan u het beleid wilt koppelen.

3. Voeg het beleid toe aan de serviceprincipal. 

Beleidsregels worden alleen van kracht voor een specifieke toepassing wanneer deze zijn gekoppeld aan een serviceprincipal. 

Er kan slechts één HRD-beleid tegelijk actief zijn op een serviceprincipal.  

U de Azure Active Directory PowerShell-cmdlets gebruiken om HRD-beleid te maken en te beheren.

Hieronder volgt een voorbeeld van HRD-beleidsdefinitie:
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":true
    }
   }
```

Het beleidstype is 'HomeRealmDiscoveryPolicy'.

**AccelerateToFederatedDomain** is optioneel. Als **AccelerateToFederatedDomain** onjuist is, heeft het beleid geen effect op automatische versnelling. Als **AccelerateToFederatedDomain** waar is en er slechts één geverifieerd en gefedereerd domein in de tenant is, worden gebruikers rechtstreeks naar de federatieve IdP gebracht om in te loggen. Als dit waar is en er meer dan één geverifieerd domein in de tenant is, moet **PreferredDomain** worden opgegeven.

**PreferredDomain** is optioneel. **PreferredDomain** moet een domein aangeven waarop u moet versnellen. Het kan worden weggelaten als de tenant slechts één federatief domein heeft.  Als het wordt weggelaten en er is meer dan één geverifieerd federatief domein, heeft het beleid geen effect.

 Als **PreferredDomain** is opgegeven, moet het overeenkomen met een geverifieerd, federatief domein voor de tenant. Alle gebruikers van de toepassing moeten zich bij dat domein kunnen aanmelden.

**AllowCloudPasswordValidation** is optioneel. Als **AllowCloudPasswordValidation** waar is, mag de toepassing een federatieve gebruiker verifiëren door gebruikersnaam/wachtwoordreferenties rechtstreeks te presenteren aan het eindpunt van het Azure Active Directory-token. Dit werkt alleen als Password Hash Sync is ingeschakeld.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioriteit en evaluatie van HRD-beleid
HRD-beleid kan worden gemaakt en vervolgens worden toegewezen aan specifieke organisaties en serviceprincipals. Dit betekent dat het mogelijk is voor meerdere beleidsregels toe te passen op een specifieke toepassing. Het HRD-beleid dat van kracht wordt, volgt de volgende regels:


- Als er een domeinhint aanwezig is in de verificatieaanvraag, wordt elk HRD-beleid genegeerd voor automatische versnelling. Het gedrag dat is opgegeven door de domeinhint wordt gebruikt.

- Als anders een beleid expliciet is toegewezen aan de serviceprincipal, wordt het afgedwongen. 

- Als er geen domeinhint is en er geen beleid expliciet is toegewezen aan de serviceprincipal, wordt een beleid dat expliciet is toegewezen aan de bovenliggende organisatie van de serviceprincipal afgedwongen. 

- Als er geen domeinhint is en er geen beleid is toegewezen aan de serviceprincipal of de organisatie, wordt het standaard GEDRAG VAN HRD gebruikt.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Zelfstudie voor het instellen van HRD-beleid op een toepassing 
We gebruiken Azure AD PowerShell-cmdlets om een aantal scenario's te doorlopen, waaronder:


- HrD-beleid instellen om automatisch te versnellen voor een toepassing in een tenant met één gefedereerd domein.

- HrD-beleid instellen om automatisch te versnellen voor een toepassing op een van de verschillende domeinen die zijn geverifieerd voor uw tenant.

- HrD-beleid instellen om een oudere toepassing in staat te stellen directe gebruikersnaam/wachtwoordverificatie uit te voeren naar Azure Active Directory voor een federatieve gebruiker.

- De toepassingen weergeven waarvoor een beleid is geconfigureerd.


### <a name="prerequisites"></a>Vereisten
In de volgende voorbeelden maakt, updatet, koppelt en verwijdert u beleidsregels voor principals van de toepassingsservice in Azure AD.

1.  Download om te beginnen het nieuwste Azure AD Power PowerShell-cmdlet-voorbeeld. 

2.  Nadat u de Azure AD PowerShell-cmdlets hebt gedownload, voert u de opdracht Verbinding uit om u aan te melden bij Azure AD met uw beheerdersaccount:

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  Voer de volgende opdracht uit om alle beleidsregels in uw organisatie te bekijken:

    ``` powershell
    Get-AzureADPolicy
    ```

Als er niets wordt geretourneerd, betekent dit dat er geen beleid is gemaakt in uw tenant.

### <a name="example-set-hrd-policy-for-an-application"></a>Voorbeeld: HRD-beleid instellen voor een toepassing 

In dit voorbeeld maakt u een beleid dat wanneer het aan een toepassing is toegewezen: 
- Versnelt gebruikers automatisch naar een AD FS-aanmeldingsscherm wanneer ze zich aanmelden bij een toepassing wanneer er één domein in uw tenant is. 
- Als u gebruikers automatisch versnelt naar een AD FS-aanmeldingsscherm, is er meer dan één federatief domein in uw tenant.
- Hiermee u niet-interactieve aanmelding van gebruikersnaam/wachtwoord rechtstreeks aanmelden bij Azure Active Directory voor federatieve gebruikers voor de toepassingen waaraan het beleid is toegewezen.

#### <a name="step-1-create-an-hrd-policy"></a>Stap 1: Een HRD-beleid maken

Met het volgende beleid worden gebruikers automatisch versneld naar een AD FS-aanmeldingsscherm wanneer ze zich aanmelden bij een toepassing wanneer er één domein in uw tenant is.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```
Met het volgende beleid worden gebruikers automatisch versneld naar een AD FS-aanmeldingsscherm dat er meer dan één federatief domein in uw tenant is. Als u meer dan één federatief domein hebt dat gebruikers voor toepassingen verifieert, moet u het domein opgeven dat automatisch moet worden versneld.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Voer de volgende opdracht uit om een beleid te maken om gebruikersnaam/wachtwoordverificatie in te schakelen voor federatieve gebruikers rechtstreeks met Azure Active Directory voor specifieke toepassingen:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```


Voer de volgende opdracht uit om uw nieuwe beleid te bekijken en de **ObjectID**te krijgen:

``` powershell
Get-AzureADPolicy
```


Als u het HRD-beleid wilt toepassen nadat u het hebt gemaakt, u het toewijzen aan meerdere aangevers van de toepassingsservice.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Stap 2: De serviceprincipal zoeken waaraan het beleid moet worden toegewezen  
U hebt de **ObjectID** nodig van de serviceprincipals waaraan u het beleid wilt toewijzen. Er zijn verschillende manieren om de **ObjectID** van serviceprincipals te vinden.    

U de portal gebruiken of [Microsoft Graph opvragen.](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) U ook naar het [Graph Explorer-hulpprogramma](https://developer.microsoft.com/graph/graph-explorer) gaan en u aanmelden bij uw Azure AD-account om alle serviceprincipals van uw organisatie te bekijken. 

Omdat u PowerShell gebruikt, u de volgende cmdlet gebruiken om de serviceprincipals en hun id's weer te geven.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Stap 3: Het beleid toewijzen aan uw serviceprincipal  
Nadat u de **ObjectID** van de serviceprincipal van de toepassing hebt waarvoor u automatische versnelling wilt configureren, voert u de volgende opdracht uit. Deze opdracht associeert het HRD-beleid dat u in stap 1 hebt gemaakt met de serviceprincipal die u in stap 2 hebt geplaatst.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

U deze opdracht herhalen voor elke serviceprincipal waaraan u het beleid wilt toevoegen.

In het geval dat een toepassing al een HomeRealmDiscovery-beleid heeft toegewezen, u geen tweede toepassing toevoegen.  Wijzig in dat geval de definitie van het home realm discovery-beleid dat aan de toepassing is toegewezen om extra parameters toe te voegen.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Stap 4: Controleer aan welke applicatieserviceprincipals uw HRD-beleid is toegewezen
Als u wilt controleren welke toepassingen het HRD-beleid hebben geconfigureerd, gebruikt u de cmdlet **Get-AzureADPolicyAppliedObject.** Geef het de **ObjectID** van het beleid dat u wilt controleren.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>Stap 5: Je bent klaar!
Probeer de toepassing om te controleren of het nieuwe beleid werkt.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Voorbeeld: de toepassingen weergeven waarvoor hrd-beleid is geconfigureerd

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Stap 1: alle beleidsregels weergeven die in uw organisatie zijn gemaakt 

``` powershell
Get-AzureADPolicy
```

Let op de **ObjectID** van het beleid waarvoor u toewijzingen wilt aanbieden.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Stap 2: De serviceprincipals weergeven waaraan het beleid is toegewezen  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-for-an-application"></a>Voorbeeld: Een HRD-beleid voor een toepassing verwijderen
#### <a name="step-1-get-the-objectid"></a>Stap 1: De ObjectID oppakken
Gebruik het vorige voorbeeld om de **ObjectID** van het beleid op te halen en die van de hoofdsom van de toepassingsservice waaruit u het wilt verwijderen. 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Stap 2: De beleidstoewijzing verwijderen van de principal van de toepassingsservice  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Stap 3: Verwijdering controleren door de serviceprincipals aan te geven waaraan het beleid is toegewezen 

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```
## <a name="next-steps"></a>Volgende stappen
- Zie [Verificatiescenario's voor Azure AD voor](../develop/authentication-scenarios.md)meer informatie over hoe verificatie werkt in Azure AD.
- Zie [Enkele aanmelding voor toepassingen in Azure Active Directory](what-is-single-sign-on.md)voor meer informatie over eenmalige aanmelding van gebruikers.
- Ga naar het [Microsoft-identiteitsplatform](../develop/v2-overview.md) voor een overzicht van alle ontwikkelaarsgerelateerde inhoud.

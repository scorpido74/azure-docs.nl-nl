---
title: Migreren vanuit de Azure Access Control Service | Microsoft Documenten
description: Meer informatie over de opties voor het verplaatsen van apps en services vanuit de Azure Access Control Service (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: 3168d36bf4c2d3c696173725f669b12dc168dcc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154998"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>How to: Migreren vanuit de Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS), een service van Azure Active Directory (Azure AD), wordt op 7 november 2018 uitgeschakeld. Toepassingen en services die momenteel Toegangsbeheer gebruiken, moeten tegen die tijd volledig zijn gemigreerd naar een ander verificatiemechanisme. In dit artikel worden aanbevelingen voor huidige klanten beschreven, omdat u van plan bent uw gebruik van toegangscontrole af te keuren. Als u momenteel geen Access Control gebruikt, hoeft u geen actie te ondernemen.

## <a name="overview"></a>Overzicht

Access Control is een cloudverificatieservice die een eenvoudige manier biedt om gebruikers te verifiëren en te autoriseren voor toegang tot uw webtoepassingen en -services. Hiermee kunnen veel functies van authenticatie en autorisatie worden meegewogen uit uw code. Access Control wordt voornamelijk gebruikt door ontwikkelaars en architecten van Microsoft .NET-clients, ASP.NET webtoepassingen en Webservices (Windows Communication Foundation) en Webservices (Windows Communication Foundation).

Use cases voor toegangsbeheer kunnen worden onderverdeeld in drie hoofdcategorieën:

- Authenticeren naar bepaalde Microsoft-cloudservices, waaronder Azure Service Bus en Dynamics CRM. Clienttoepassingen verkrijgen tokens van Access Control om te verifiëren voor deze services om verschillende acties uit te voeren.
- Verificatie toevoegen aan webtoepassingen, zowel aangepast als voorverpakt (zoals SharePoint). Door toegangsbeheer "passieve" verificatie te gebruiken, kunnen webtoepassingen aanmelding ondersteunen met een Microsoft-account (voorheen Live ID) en met accounts van Google, Facebook, Yahoo, Azure AD en Active Directory Federation Services (AD FS).
- Aangepaste webservices beveiligen met tokens die zijn uitgegeven door Access Control. Door gebruik te maken van "actieve" verificatie kunnen webservices ervoor zorgen dat ze alleen toegang verlenen tot bekende clients die zich hebben geverifieerd met Access Control.

Elk van deze use cases en hun aanbevolen migratiestrategieën worden besproken in de volgende secties.

> [!WARNING]
> In de meeste gevallen zijn belangrijke codewijzigingen vereist om bestaande apps en services te migreren naar nieuwere technologieën. We raden u aan onmiddellijk te beginnen met het plannen en uitvoeren van uw migratie om mogelijke uitval of downtime te voorkomen.

Toegangsbeheer heeft de volgende onderdelen:

- Een secure token service (STS), die verificatieverzoeken ontvangt en beveiligingstokens uitgeeft.
- De klassieke Azure-portal, waar u naamruimten voor toegangsbeheer maakt, verwijdert en uitschakelt.
- Een aparte Access Control-beheerportal, waar u naamruimten voor toegangsbeheer aanpast en configureert.
- Een beheerservice, die u gebruiken om de functies van de portals te automatiseren.
- Een token transformatie regel engine, die u gebruiken om complexe logica te definiëren om de uitvoerindeling van tokens die Access Control problemen te manipuleren.

Als u deze componenten wilt gebruiken, moet u een of meer naamruimten voor toegangsbeheer maken. Een *naamruimte* is een speciaal exemplaar van Access Control waarmee uw toepassingen en services communiceren. Een naamruimte is geïsoleerd van alle andere Access Control-klanten. Andere Access Control-klanten gebruiken hun eigen naamruimten. Een naamruimte in Toegangsbeheer heeft een speciale URL die er als volgt uitziet:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Alle communicatie met de STS en management operaties worden gedaan op deze URL. U gebruikt verschillende paden voor verschillende doeleinden. Als u wilt bepalen of uw toepassingen of services&lt;Toegangsbeheer gebruiken, controleert u of verkeer naar https:// naamruimte&gt;.accesscontrol.windows.net. Alle verkeer naar deze URL wordt verwerkt door Toegangscontrole en moet worden stopgezet. 

De uitzondering hierop is `https://accounts.accesscontrol.windows.net`het verkeer naar . Verkeer naar deze URL wordt al door een andere service afgehandeld en **wordt niet** beïnvloed door de afschaffing van het toegangscontrolebeheer. 

Zie [Toegangscontroleservice 2.0 (gearchiveerd)](https://msdn.microsoft.com/library/hh147631.aspx)voor meer informatie over toegangscontrole.

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Ontdek welke van uw apps worden beïnvloed

Volg de stappen in deze sectie om erachter te komen welke van uw apps worden beïnvloed door acs-pensioen.

### <a name="download-and-install-acs-powershell"></a>ACS PowerShell downloaden en installeren

1. Ga naar de PowerShell Gallery en download [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. De module installeren door

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Een lijst met alle mogelijke opdrachten krijgen door

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Voer het gewenste hulp aan voor een specifieke opdracht uit om hulp te krijgen bij een specifieke opdracht:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    waar `[Command-Name]` is de naam van de ACS-opdracht.

### <a name="list-your-acs-namespaces"></a>Uw ACS-naamruimten weergeven

1. Maak verbinding met ACS met de **Connect-AcsAccount-cmdlet.**
  
    Mogelijk moet u `Set-ExecutionPolicy -ExecutionPolicy Bypass` uitvoeren voordat u opdrachten uitvoeren en de beheerder van die abonnementen zijn om de opdrachten uit te voeren.

2. Vermeld uw beschikbare Azure-abonnementen met de cmdlet **Get-AcsSubscription.**
3. Vermeld uw ACS-naamruimten met de cmdlet **Get-AcsNamespace.**

### <a name="check-which-applications-will-be-impacted"></a>Controleer welke toepassingen worden beïnvloed

1. Gebruik de naamruimte van de vorige stap en ga naar`https://<namespace>.accesscontrol.windows.net`

    Als een van de naamruimten bijvoorbeeld contoso-test is, gaat u naar`https://contoso-test.accesscontrol.windows.net`

2. Selecteer **onder Vertrouwensrelaties**de optie Toepassingen van **relying party** om de lijst met apps te bekijken die worden beïnvloed door acs-pensionering.
3. Herhaal stap 1-2 voor alle andere ACS-naamruimte(s) die u hebt.

## <a name="retirement-schedule"></a>Pensioenschema

Vanaf november 2017 worden alle Access Control-componenten volledig ondersteund en operationeel. De enige beperking is dat u [geen nieuwe naamruimten voor toegangsbeheer maken via de klassieke Azure-portal.](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Hier is het schema voor het verwijderen van Access Control-onderdelen:

- **November 2017**: De Azure AD-beheerervaring in de klassieke Azure-portal [is in gebruik genomen.](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/) Op dit moment is namespace management voor Access Control `https://manage.windowsazure.com?restoreClassic=true`beschikbaar op een nieuwe, speciale URL: . Gebruik deze URl om uw bestaande naamruimten weer te geven, naamruimten in- en uit te schakelen en naamruimten te verwijderen, als u dat wilt.
- **2 april 2018**: De klassieke Azure-portal is volledig buiten gebruik gesteld, wat betekent dat het naamruimtebeheer van Access Control niet langer beschikbaar is via een URL. Op dit moment u de naamruimten voor toegangsbeheer niet uitschakelen of inschakelen, verwijderen of opsommen. De access control-beheerportal is echter volledig `https://\<namespace\>.accesscontrol.windows.net`functioneel en bevindt zich op . Alle andere onderdelen van Toegangscontrole blijven normaal werken.
- **7 november 2018**: Alle Access Control-componenten worden definitief afgesloten. Dit omvat de Access Control-beheerportal, de beheerservice, STS en de tokentransformatieregelengine. Op dit moment mislukken alle aanvragen \<die\>naar Access Control worden verzonden (op namespace .accesscontrol.windows.net). U had alle bestaande apps en services ruim voor deze tijd naar andere technologieën moeten hebben gemigreerd.

> [!NOTE]
> Een beleid schakelt naamruimten uit die gedurende een bepaalde periode geen token hebben aangevraagd. Vanaf begin september 2018 bedraagt deze periode momenteel 14 dagen van inactiviteit, maar dit zal worden verkort tot 7 dagen van inactiviteit in de komende weken. Als u naamruimten voor Access Control hebt die momenteel zijn uitgeschakeld, u [ACS PowerShell downloaden en installeren](#download-and-install-acs-powershell) om de naamruimte(en) opnieuw in te schakelen.

## <a name="migration-strategies"></a>Migratiestrategieën

In de volgende secties worden aanbevelingen op hoog niveau beschreven voor het migreren van Toegangscontrole naar andere Microsoft-technologieën.

### <a name="clients-of-microsoft-cloud-services"></a>Clients van Microsoft-cloudservices

Elke Microsoft-cloudservice die tokens accepteert die zijn uitgegeven door Access Control, ondersteunt nu ten minste één alternatieve vorm van verificatie. Het juiste verificatiemechanisme varieert voor elke service. Wij raden u aan om voor officiële begeleiding naar de specifieke documentatie voor elke service te verwijzen. Voor het gemak wordt hier elke set documentatie verstrekt:

| Service | Richtlijnen |
| ------- | -------- |
| Azure Service Bus | [Migreren naar gedeelde toegangshandtekeningen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [Migreren naar gedeelde toegangshandtekeningen](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Managed Cache | [Migreren naar Azure-cache voor Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Migreren naar de API's voor cognitieve services](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk-services | [Migreren naar de functie Logic Apps van Azure App Service](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Migreren naar Azure AD-verificatie](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [De Azure Backup-agent bijwerken](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-klanten

SharePoint 2013- en 2016-klanten en SharePoint Online-klanten gebruiken ACS al lang voor verificatiedoeleinden in cloud-, on-premises en hybride scenario's. Sommige SharePoint-functies en use cases worden beïnvloed door acs-pensionering, terwijl andere dat niet zullen doen. In de onderstaande tabel worden migratierichtlijnen samengevat voor enkele van de populairste SharePoint-functies die gebruikmaken van ACS:

| Functie | Richtlijnen |
| ------- | -------- |
| Gebruikers verifiëren vanuit Azure AD | Voorheen ondersteunde Azure AD geen SAML 1.1-tokens die door SharePoint vereist zijn voor verificatie en werd ACS gebruikt als tussenpersoon die SharePoint compatibel maakte met Azure AD-tokenindelingen. U [SharePoint nu rechtstreeks verbinden met Azure AD via de Azure AD App Gallery SharePoint on premises-app](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial). |
| [App-verificatie & server-naar-server-verificatie in SharePoint on-premises](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | Niet beïnvloed door acs pensionering; geen wijzigingen nodig. | 
| [Machtiging voor weinig vertrouwensrelaties voor SharePoint-invoegingen (gehoste provider en SharePoint-](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Niet beïnvloed door acs pensionering; geen wijzigingen nodig. |
| [Hybride zoeken in SharePoint-cloud](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | Niet beïnvloed door acs pensionering; geen wijzigingen nodig. |

### <a name="web-applications-that-use-passive-authentication"></a>Webtoepassingen die passieve verificatie gebruiken

Voor webtoepassingen die Toegangsbeheer gebruiken voor gebruikersverificatie, biedt Access Control de volgende functies en mogelijkheden voor ontwikkelaars en architecten van webtoepassingen:

- Diepe integratie met Windows Identity Foundation (WIF).
- Federatie met Google-, Facebook-, Yahoo-, Azure Active Directory- en AD FS-accounts en Microsoft-accounts.
- Ondersteuning voor de volgende verificatieprotocollen: OAuth 2.0 Draft 13, WS-Trust en Web Services Federation (WS-Federation).
- Ondersteuning voor de volgende tokenformaten: JSON Web Token (JWT), SAML 1.1, SAML 2.0 en Simple Web Token (SWT).
- Een home realm discovery-ervaring, geïntegreerd in WIF, waarmee gebruikers het type account kunnen kiezen dat ze gebruiken om in te loggen. Deze ervaring wordt gehost door de webapplicatie en is volledig aanpasbaar.
- Tokentransformatie waarmee de claims die door de webtoepassing van Access Control zijn ontvangen, uitgebreid kunnen worden aangepast, waaronder:
    - Doorgeef claims van identiteitsproviders.
    - Extra aangepaste claims toevoegen.
    - Eenvoudige als-dan logica om claims uit te geven onder bepaalde voorwaarden.

Helaas is er niet één service die al deze gelijkwaardige mogelijkheden biedt. U moet evalueren welke mogelijkheden van Toegangsbeheer u nodig hebt en vervolgens kiezen tussen het gebruik van [Azure Active Directory,](https://azure.microsoft.com/develop/identity/signin/) [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) of een andere cloudverificatieservice.

#### <a name="migrate-to-azure-active-directory"></a>Migreren naar Azure Active Directory

Een pad om te overwegen is het integreren van uw apps en services rechtstreeks met Azure AD. Azure AD is de cloudgebaseerde identiteitsprovider voor Microsoft-werk- of schoolaccounts. Azure AD is de identiteitsprovider voor Office 365, Azure en nog veel meer. Het biedt vergelijkbare federatieve verificatiemogelijkheden voor Toegangscontrole, maar biedt niet alle Access Control-functies. 

Het primaire voorbeeld is federatie met sociale identiteitsproviders, zoals Facebook, Google en Yahoo. Als uw gebruikers zich aanmelden met dit soort referenties, is Azure AD niet de oplossing voor u. 

Azure AD biedt ook niet noodzakelijkerwijs ondersteuning voor exact dezelfde verificatieprotocollen als Toegangscontrole. Hoewel zowel Access Control als Azure AD OAuth ondersteunen, zijn er bijvoorbeeld subtiele verschillen tussen elke implementatie. Voor verschillende implementaties moet u de code wijzigen als onderdeel van een migratie.

Azure AD biedt echter verschillende potentiële voordelen voor Access Control-klanten. Het ondersteunt native Microsoft werk- of schoolaccounts die worden gehost in de cloud, die vaak worden gebruikt door Access Control-klanten. 

Een Azure AD-tenant kan ook worden gefedereerd naar een of meer exemplaren van on-premises Active Directory via AD FS. Op deze manier kan uw app cloudgebruikers en gebruikers verifiëren die on-premises worden gehost. Het ondersteunt ook het WS-Federation-protocol, waardoor het relatief eenvoudig is om te integreren met een webtoepassing met behulp van WIF.

In de volgende tabel worden de functies van Toegangsbeheer die relevant zijn voor webtoepassingen vergeleken met de functies die beschikbaar zijn in Azure AD. 

Op hoog niveau *is Azure Active Directory waarschijnlijk de beste keuze voor uw migratie als u gebruikers alleen laat inloggen met hun Microsoft-werk- of schoolaccounts.*

| Mogelijkheid | Ondersteuning voor Toegangsbeheer | Azure AD-ondersteuning |
| ---------- | ----------- | ---------------- |
| **Typen rekeningen** | | |
| Microsoft-werk- of schoolaccounts | Ondersteund | Ondersteund |
| Accounts van Windows Server Active Directory en AD FS |- Ondersteund via federatie met een Azure AD-tenant <br />- Ondersteund via directe federatie met AD FS | Alleen ondersteund via federatie met een Azure AD-tenant | 
| Accounts van andere systemen voor identiteitsbeheer van ondernemingen |- Mogelijk via federatie met een Azure AD-tenant <br />- Ondersteund via directe federatie | Mogelijk via federatie met een Azure AD-tenant |
| Microsoft-accounts voor persoonlijk gebruik | Ondersteund | Ondersteund via het Azure AD v2.0 OAuth-protocol, maar niet via andere protocollen | 
| Facebook, Google, Yahoo-accounts | Ondersteund | Helemaal niet ondersteund |
| **Protocollen en SDK-compatibiliteit** | | |
| WIF | Ondersteund | Ondersteunde, maar beperkte instructies zijn beschikbaar |
| Webservices-federatie | Ondersteund | Ondersteund |
| OAuth 2.0 | Ondersteuning voor ontwerp 13 | Ondersteuning voor RFC 6749, de modernste specificatie |
| WS-Vertrouwen | Ondersteund | Niet ondersteund |
| **Token-indelingen** | | |
| JWT JWT | Ondersteund in bèta | Ondersteund |
| SAML 1.1 | Ondersteund | Preview |
| SAML 2.0 | Ondersteund | Ondersteund |
| Swt | Ondersteund | Niet ondersteund |
| **Aanpassingen** | | |
| Aanpasbare detectie van thuisgebieden/gebruikersinterface voor het kiezen van accountpicking | Downloadbare code die kan worden opgenomen in apps | Niet ondersteund |
| Aangepaste tokenondertekenende certificaten uploaden | Ondersteund | Ondersteund |
| Claims in tokens aanpassen |- Voer inputclaims door van identiteitsproviders<br />- Krijg toegangstoken van identiteitsprovider als claim<br />- Output claims uitgeven op basis van waarden van input claims<br />- Outputclaims met constante waarden afgeven |- Kan geen claims doorgeven van federatieve identiteitsaanbieders<br />- Kan geen toegangstoken krijgen van identiteitsprovider als claim<br />- Kan geen outputclaims indienen op basis van waarden van inputclaims<br />- Kan uitvoerclaims met constante waarden uitgeven<br />- Kan uitvoerclaims indienen op basis van eigenschappen van gebruikers die zijn gesynchroniseerd met Azure AD |
| **Automatisering** | | |
| Configuratie- en beheertaken automatiseren | Ondersteund via Access Control Management Service | Ondersteund met de Microsoft Graph API |

Als u besluit dat Azure AD het beste migratiepad is voor uw toepassingen en services, moet u op twee manieren op de hoogte zijn van twee manieren om uw app te integreren met Azure AD.

Als u WS-Federation of WIF wilt gebruiken om te integreren met Azure AD, raden we u aan de aanpak te volgen die is beschreven in [Federatie federatieve eenmalige aanmelding configureren voor een niet-galerietoepassing.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) Het artikel verwijst naar het configureren van Azure AD voor SAML-gebaseerde single sign-on, maar werkt ook voor het configureren van WS-Federation. Voor het volgen van deze aanpak is een Azure AD Premium-licentie vereist. Deze aanpak heeft twee voordelen:

- U krijgt de volledige flexibiliteit van azure AD-tokenaanpassing. U de claims die door Azure AD zijn uitgegeven aanpassen aan de claims die zijn uitgegeven door Access Control. Dit geldt met name voor de claim voor de gebruikersnaam of naam-id. Als u consistente gebruikersinstellingen voor uw gebruikers wilt blijven ontvangen nadat u technologieën hebt gewijzigd, moet u ervoor zorgen dat de door Azure AD uitgegeven gebruikers-id's overeenkomen met de gebruikers die zijn uitgegeven door Access Control.
- U een certificaat voor het ondertekenen van certificaten configureren dat specifiek is voor uw toepassing en met een levensduur die u beheert.

> [!NOTE]
> Voor deze aanpak is een Azure AD Premium-licentie vereist. Als u een klant voor toegangsbeheer bent en u een premiumlicentie nodig hebt voor het instellen van één aanmelding voor een toepassing, neemt u contact met ons op. We bieden u graag ontwikkelaarslicenties die u gebruiken.

Een alternatieve benadering is het volgen van [deze code steekproef](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), die iets verschillende instructies voor het opzetten van WS-Federation geeft. Deze code steekproef maakt geen gebruik van WIF, maar de ASP.NET 4,5 OWIN middleware. De instructies voor app-registratie zijn echter geldig voor apps die WIF gebruiken en vereisen geen Azure AD Premium-licentie. 

Als u deze benadering kiest, moet u inzicht krijgen [in de rollover van ondertekeningssleutels in Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover) Deze benadering maakt gebruik van de wereldwijde ondertekeningssleutel van Azure AD om tokens uit te geven. Standaard vernieuwt WIF de ondertekeningstoetsen niet automatisch. Wanneer Azure AD de globale ondertekeningssleutels roteert, moet uw WIF-implementatie voorbereid zijn om de wijzigingen te accepteren. Zie [Belangrijke informatie over het ondertekenen van sleutelrollover in Azure AD](https://msdn.microsoft.com/library/azure/dn641920.aspx)voor meer informatie.

Als u integreren met Azure AD via de OpenID Connect- of OAuth-protocollen, raden we u aan dit te doen. We hebben uitgebreide documentatie en richtlijnen over het integreren van Azure AD in uw webtoepassing beschikbaar in onze [Azure AD-ontwikkelaarshandleiding.](https://aka.ms/aaddev)

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migreren naar Azure Active Directory B2C

Het andere migratiepad dat u moet overwegen, is Azure AD B2C. Azure AD B2C is een cloudverificatieservice waarmee ontwikkelaars, net als Access Control, hun verificatie- en identiteitsbeheerlogica kunnen uitbesteden aan een cloudservice. Het is een betaalde dienst (met gratis en premium lagen) die is ontworpen voor toepassingen die gericht zijn op consumenten die mogelijk tot miljoenen actieve gebruikers hebben.

Net als Access Control is een van de meest aantrekkelijke functies van Azure AD B2C dat het veel verschillende typen accounts ondersteunt. Met Azure AD B2C u gebruikers aanmelden met hun Microsoft-account of Facebook-, Google-, LinkedIn-, GitHub- of Yahoo-accounts en meer. Azure AD B2C ondersteunt ook 'lokale accounts' of gebruikersnaam en wachtwoorden die gebruikers specifiek voor uw toepassing maken. Azure AD B2C biedt ook uitgebreide uitbreidbaarheid die u gebruiken om uw aanmeldingsstromen aan te passen. 

Azure AD B2C biedt echter geen ondersteuning voor de breedte van verificatieprotocollen en tokenindelingen die access control-clients nodig hebben. U Azure AD B2C ook niet gebruiken om tokens en query's te ontvangen voor aanvullende informatie over de gebruiker van de identiteitsprovider, Microsoft of anderszins.

In de volgende tabel worden de functies van Toegangsbeheer die relevant zijn voor webtoepassingen vergeleken met de functies die beschikbaar zijn in Azure AD B2C. Op hoog niveau *is Azure AD B2C waarschijnlijk de juiste keuze voor uw migratie als uw toepassing wordt geconfronteerd met de consument of als deze veel verschillende typen accounts ondersteunt.*

| Mogelijkheid | Ondersteuning voor Toegangsbeheer | Azure AD B2C-ondersteuning |
| ---------- | ----------- | ---------------- |
| **Typen rekeningen** | | |
| Microsoft-werk- of schoolaccounts | Ondersteund | Ondersteund via aangepast beleid  |
| Accounts van Windows Server Active Directory en AD FS | Ondersteund via directe federatie met AD FS | Ondersteund via SAML-federatie met behulp van aangepast beleid |
| Accounts van andere systemen voor identiteitsbeheer van ondernemingen | Ondersteund via directe federatie via WS-Federatie | Ondersteund via SAML-federatie met behulp van aangepast beleid |
| Microsoft-accounts voor persoonlijk gebruik | Ondersteund | Ondersteund | 
| Facebook, Google, Yahoo-accounts | Ondersteund | Facebook en Google ondersteund native, Yahoo ondersteund via OpenID Connect federatie met behulp van aangepaste beleid |
| **Protocollen en SDK-compatibiliteit** | | |
| Windows Identity Foundation (WIF) | Ondersteund | Niet ondersteund |
| Webservices-federatie | Ondersteund | Niet ondersteund |
| OAuth 2.0 | Ondersteuning voor ontwerp 13 | Ondersteuning voor RFC 6749, de modernste specificatie |
| WS-Vertrouwen | Ondersteund | Niet ondersteund |
| **Token-indelingen** | | |
| JWT JWT | Ondersteund in bèta | Ondersteund |
| SAML 1.1 | Ondersteund | Niet ondersteund |
| SAML 2.0 | Ondersteund | Niet ondersteund |
| Swt | Ondersteund | Niet ondersteund |
| **Aanpassingen** | | |
| Aanpasbare detectie van thuisgebieden/gebruikersinterface voor het kiezen van accountpicking | Downloadbare code die kan worden opgenomen in apps | Volledig aanpasbare gebruikersinterface via aangepaste CSS |
| Aangepaste tokenondertekenende certificaten uploaden | Ondersteund | Aangepaste ondertekeningssleutels, geen certificaten, ondersteund via aangepaste beleidsregels |
| Claims in tokens aanpassen |- Voer inputclaims door van identiteitsproviders<br />- Krijg toegangstoken van identiteitsprovider als claim<br />- Output claims uitgeven op basis van waarden van input claims<br />- Outputclaims met constante waarden afgeven |- Kan door claims van identiteitsaanbieders heen; aangepaste beleidsregels vereist voor sommige claims<br />- Kan geen toegangstoken krijgen van identiteitsprovider als claim<br />- Kan outputclaims indienen op basis van waarden van inputclaims via aangepast beleid<br />- Kan uitvoerclaims met constante waarden indienen via aangepast beleid |
| **Automatisering** | | |
| Configuratie- en beheertaken automatiseren | Ondersteund via Access Control Management Service |- Het maken van gebruikers toegestaan met behulp van de Microsoft Graph API<br />- Kan geen B2C-tenants, toepassingen of beleid programmatisch maken |

Als u besluit dat Azure AD B2C het beste migratiepad is voor uw toepassingen en services, begint u met de volgende bronnen:

- [Azure AD B2C-documentatie](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Aangepast Azure AD B2C-beleid](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C-prijzen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migreren naar Ping-identiteit of Auth0

In sommige gevallen zult u merken dat Azure AD en Azure AD B2C niet voldoende zijn om toegangsbeheer in uw webtoepassingen te vervangen zonder grote codewijzigingen aan te brengen. Enkele veelvoorkomende voorbeelden zijn:

- Webtoepassingen die WIF of WS-Federation gebruiken voor aanmelding bij aanbieders van sociale identiteit, zoals Google of Facebook.
- Webtoepassingen die rechtstreeks worden uitgevoerd naar een leverancier van bedrijfsidentiteiten via het WS-Federation-protocol.
- Webtoepassingen waarvoor het toegangstoken vereist is dat is uitgegeven door een aanbieder van sociale identiteit (zoals Google of Facebook) als een claim in de tokens die zijn uitgegeven door Toegangscontrole.
- Webtoepassingen met complexe regels voor tokentransformatie die Azure AD of Azure AD B2C niet kan reproduceren.
- Multi-tenant webapplicaties die ACS gebruiken om federatie centraal te beheren voor veel verschillende identiteitsproviders

In deze gevallen u overwegen uw webtoepassing te migreren naar een andere cloudverificatieservice. We raden u aan de volgende opties te verkennen. Elk van de volgende opties biedt mogelijkheden die vergelijkbaar zijn met Toegangscontrole:

|     |     |
| --- | --- |
| ![Deze afbeelding toont het Auth0-logo](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs) is een flexibele cloudidentiteitsservice die [migratiebegeleiding op hoog niveau](https://auth0.com/acs)heeft gecreëerd voor klanten van Access Control en bijna elke functie ondersteunt die ACS doet. |
| ![Deze afbeelding toont het ping-identiteitslogo](./media/active-directory-acs-migration/rsz-ping.png) | [Ping Identity](https://www.pingidentity.com) biedt twee oplossingen die vergelijkbaar zijn met ACS. PingOne is een cloud-identiteitsservice die veel van dezelfde functies als ACS ondersteunt, en PingFederate is een vergelijkbaar on-premises identiteitsproduct dat meer flexibiliteit biedt. Raadpleeg [de ACS-pensioenrichtlijnen van Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) voor meer informatie over het gebruik van deze producten. |

Ons doel bij het werken met Ping Identity en Auth0 is ervoor te zorgen dat alle Access Control-klanten een migratiepad hebben voor hun apps en services dat de hoeveelheid werk minimaliseert die nodig is om van Toegangscontrole te gaan.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Webservices die actieve verificatie gebruiken

Voor webservices die zijn beveiligd met tokens die zijn uitgegeven door Toegangscontrole, biedt Access Control de volgende functies en mogelijkheden:

- Mogelijkheid om een of meer *serviceidentiteiten* te registreren in de naamruimte voor toegangsbeheer. Serviceidentiteiten kunnen worden gebruikt om tokens aan te vragen.
- Ondersteuning voor de OAuth WRAP- en OAuth 2.0 Concept 13-protocollen voor het aanvragen van tokens, met behulp van de volgende typen referenties:
    - Een eenvoudig wachtwoord dat is gemaakt voor de service-identiteit
    - Een ondertekende SWT met behulp van een symmetrische toets of X509-certificaat
    - Een SAML-token uitgegeven door een vertrouwde identiteitsprovider (meestal een AD FS-exemplaar)
- Ondersteuning voor de volgende tokenformaten: JWT, SAML 1.1, SAML 2.0 en SWT.
- Eenvoudige regels voor tokentransformatie.

Serviceidentiteiten in Toegangsbeheer worden meestal gebruikt om server-naar-serververificatie te implementeren. 

#### <a name="migrate-to-azure-active-directory"></a>Migreren naar Azure Active Directory

Onze aanbeveling voor dit type verificatiestroom is om te migreren naar [Azure Active Directory.](https://azure.microsoft.com/develop/identity/signin/) Azure AD is de cloudgebaseerde identiteitsprovider voor Microsoft-werk- of schoolaccounts. Azure AD is de identiteitsprovider voor Office 365, Azure en nog veel meer. 

U Azure AD ook gebruiken voor server-naar-serververificatie met behulp van de Azure AD-implementatie van de OAuth-clientreferentiessubsidie. In de volgende tabel worden de mogelijkheden van Toegangsbeheer in server-naar-serververificatie vergeleken met de mogelijkheden die beschikbaar zijn in Azure AD.

| Mogelijkheid | Ondersteuning voor Toegangsbeheer | Azure AD-ondersteuning |
| ---------- | ----------- | ---------------- |
| Een webservice registreren | Een relying party maken in de Access Control-beheerportal | Een Azure AD-webtoepassing maken in de Azure-portal |
| Een klant registreren | Een service-identiteit maken in de beheerportal voor toegangsbeheer | Een andere Azure AD-webtoepassing maken in de Azure-portal |
| Gebruikt protocol |- OAuth WRAP protocol<br />- OAuth 2.0 Ontwerp 13 clientreferenties verlenen | Referenties voor OAuth 2.0-client verlenen |
| Clientverificatiemethoden |- Eenvoudig wachtwoord<br />- Ondertekend SWT<br />- SAML-token van een federatieve identiteitsprovider |- Eenvoudig wachtwoord<br />- Ondertekend JWT |
| Token-indelingen |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | Alleen JWT |
| Tokentransformatie |- Aangepaste claims toevoegen<br />- Eenvoudige als-dan claim uitgifte logica | Aangepaste claims toevoegen | 
| Configuratie- en beheertaken automatiseren | Ondersteund via Access Control Management Service | Ondersteund met de Microsoft Graph API |

Zie de volgende bronnen voor richtlijnen voor het implementeren van server-naar-serverscenario's:

- Service-to-Service-sectie van de [Azure AD-ontwikkelaarshandleiding](https://aka.ms/aaddev)
- [Daemon code voorbeeld met behulp van eenvoudige wachtwoord client referenties](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Voorbeeld van daemoncode met behulp van certificaatclientreferenties](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migreren naar Ping-identiteit of Auth0

In sommige gevallen u merken dat de Azure AD-clientreferenties en de OAuth-subsidieimplementatie niet voldoende zijn om Toegangsbeheer in uw architectuur te vervangen zonder grote codewijzigingen. Enkele veelvoorkomende voorbeelden zijn:

- Server-naar-server-verificatie met andere token-indelingen dan JWT's.
- Server-naar-server-verificatie met behulp van een invoertoken dat wordt geleverd door een externe identiteitsprovider.
- Server-naar-server-verificatie met tokentransformatieregels die Azure AD niet kan reproduceren.

In deze gevallen u overwegen uw webtoepassing te migreren naar een andere cloudverificatieservice. We raden u aan de volgende opties te verkennen. Elk van de volgende opties biedt mogelijkheden die vergelijkbaar zijn met Toegangscontrole:

|     |     |
| --- | --- |
| ![Deze afbeelding toont het Auth0-logo](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs) is een flexibele cloudidentiteitsservice die [migratiebegeleiding op hoog niveau](https://auth0.com/acs)heeft gecreëerd voor klanten van Access Control en bijna elke functie ondersteunt die ACS doet. |
| ![Deze afbeelding toont het ping-identiteitslogo](./media/active-directory-acs-migration/rsz-ping.png) | [Ping Identity](https://www.pingidentity.com) biedt twee oplossingen die vergelijkbaar zijn met ACS. PingOne is een cloud-identiteitsservice die veel van dezelfde functies als ACS ondersteunt, en PingFederate is een vergelijkbaar on-premises identiteitsproduct dat meer flexibiliteit biedt. Raadpleeg [de ACS-pensioenrichtlijnen van Ping](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) voor meer informatie over het gebruik van deze producten. |

Ons doel bij het werken met Ping Identity en Auth0 is ervoor te zorgen dat alle Access Control-klanten een migratiepad hebben voor hun apps en services dat de hoeveelheid werk minimaliseert die nodig is om van Toegangscontrole te gaan.

#### <a name="passthrough-authentication"></a>Passthrough-verificatie

Voor passthrough-verificatie met willekeurige tokentransformatie is er geen gelijkwaardige Microsoft-technologie met ACS. Als dat is wat uw klanten nodig hebben, auth0 misschien degene die de dichtstbijzijnde benadering oplossing biedt.

## <a name="questions-concerns-and-feedback"></a>Vragen, zorgen en feedback

We begrijpen dat veel Access Control-klanten na het lezen van dit artikel geen duidelijk migratiepad zullen vinden. Mogelijk hebt u wat hulp of begeleiding nodig bij het bepalen van het juiste plan. Als u uw migratiescenario's en vragen wilt bespreken, laat dan een reactie achter op deze pagina.

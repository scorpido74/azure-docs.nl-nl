---
title: Azure-identiteit & aanbevolen procedures voor toegang tot beveiliging | Microsoft Documenten
description: In dit artikel vindt u een reeks aanbevolen procedures voor identiteitsbeheer en toegangscontrole met behulp van ingebouwde Azure-mogelijkheden.
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: ffd9919092cdf2481767e58f10ba6525d56ca4a8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548459"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Aanbevolen procedures voor azure Identity Management en beveiliging van toegangsbeheer

In dit artikel bespreken we een verzameling aanbevolen procedures voor Azure-identiteitsbeheer en toegangscontrolebeveiliging. Deze best practices zijn afgeleid van onze ervaring met [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) en de ervaringen van klanten zoals uzelf.

Voor elke best practice leggen we uit:

* Wat de beste praktijk is
* Waarom u die best practice wilt inschakelen
* Wat kan het resultaat zijn als u de beste praktijken niet inschakelt
* Mogelijke alternatieven voor de beste praktijken
* Hoe u leren om de beste praktijken in te schakelen

Dit best practices artikel over azure-identiteitsbeheer en toegangscontrolebeveiliging is gebaseerd op een consensusadvies en Azure-platformmogelijkheden en functiesets, zoals deze bestaan op het moment dat dit artikel werd geschreven.

De bedoeling bij het schrijven van dit artikel is om een algemene routekaart te bieden naar een meer robuuste beveiligingshouding na implementatie geleid door onze "[5 stappen om uw identiteitsinfrastructuur](steps-secure-identity.md)" checklist te beveiligen, die u door enkele van onze kernfuncties en services leidt.

Meningen en technologieën veranderen in de loop van de tijd en dit artikel zal regelmatig worden bijgewerkt om deze veranderingen weer te geven.

Aanbevolen procedures voor azure-identiteitsbeheer en toegangscontrolebeveiliging die in dit artikel worden besproken, zijn onder meer:

* Identiteit behandelen als de primaire beveiligingsperimeter
* Identiteitsbeheer centraliseren
* Verbonden tenants beheren
* Eén aanmelding inschakelen
* Voorwaardelijke toegang inschakelen
* Plannen voor routinematige beveiligingsverbeteringen
* Wachtwoordbeheer inschakelen
* Multifactorverificatie afdwingen voor gebruikers
* Op rollen gebaseerd toegangsbeheer gebruiken
* Lagere blootstelling van geprivilegieerde accounts
* Locaties beheren waar resources zich bevinden
* Azure AD gebruiken voor opslagverificatie

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Identiteit behandelen als de primaire beveiligingsperimeter

Velen beschouwen identiteit als de primaire perimeter voor beveiliging. Dit is een verschuiving van de traditionele focus op netwerkbeveiliging. Netwerkperimeters worden steeds poreuzer en die perimeterverdediging kan niet zo effectief zijn als voor de explosie van [BYOD-apparaten](https://aka.ms/byodcg) en cloudtoepassingen.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) is de Azure-oplossing voor identiteits- en toegangsbeheer. Azure AD is een multitenant-, cloud- en identiteitsbeheerservice van Microsoft. Het combineert belangrijke directoryservices, toegangsbeheer voor toepassingen en identiteitsbeveiliging in één oplossing.

In de volgende secties worden best practices voor identiteits- en toegangsbeveiliging weergegeven met Azure AD.

**Aanbevolen procedures**: Beveiligingscontroles en detecties centreren rond gebruikers- en serviceidentiteiten.
**Detail:** Gebruik Azure AD om besturingselementen en identiteiten te verzamelen.

## <a name="centralize-identity-management"></a>Identiteitsbeheer centraliseren

In een [hybride identiteitsscenario](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) raden we u aan uw on-premises en cloudmappen te integreren. Integratie stelt uw IT-team in staat om accounts vanaf één locatie te beheren, ongeacht waar een account wordt gemaakt. Integratie helpt uw gebruikers ook productiever te zijn door een gemeenschappelijke identiteit te bieden voor toegang tot zowel cloud- als on-premises bronnen.

**Aanbevolen procedures**: Stel één Azure AD-exemplaar in. Consistentie en één gezaghebbende bron zullen de duidelijkheid vergroten en beveiligingsrisico's van menselijke fouten en configuratiecomplexiteit verminderen.
**Detail:** Wijs één Azure AD-map aan als de gezaghebbende bron voor zakelijke en organisatorische accounts.

**Aanbevolen procedures**: Integreer uw on-premises mappen met Azure AD.  
**Detail:** Gebruik [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) om uw on-premises directory te synchroniseren met uw cloudmap.

> [!Note]
> Er zijn [factoren die van invloed zijn op de prestaties van Azure AD Connect.](../../active-directory/hybrid/plan-connect-performance-factors.md) Zorg ervoor dat Azure AD Connect voldoende capaciteit heeft om te voorkomen dat slecht presterende systemen de beveiliging en productiviteit belemmeren. Grote of complexe organisaties (organisaties die meer dan 100.000 objecten inrichten) moeten de [aanbevelingen](../../active-directory/hybrid/whatis-hybrid-identity.md) volgen om hun Azure AD Connect-implementatie te optimaliseren.

**Aanbevolen procedures:** synchroniseer geen accounts met Azure AD met hoge bevoegdheden in uw bestaande Active Directory-instantie.
**Detail:** wijzig niet de [standaardConfiguratie van Azure AD Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) die deze accounts filtert. Deze configuratie beperkt het risico van tegenstanders die van cloud naar on-premises assets draaien (wat een groot incident kan veroorzaken).

**Aanbevolen procedures**: Schakel synchronisatie van wachtwoordhash in.  
**Detail:** Wachtwoordhashsynchronisatie is een functie die wordt gebruikt om gebruikerswachtwoordhashes te synchroniseren van een on-premises Active Directory-instantie naar een op een cloud gebaseerd Azure AD-exemplaar. Deze synchronisatie helpt om te beschermen tegen gelekte referenties die worden afgespeeld van eerdere aanvallen.

Zelfs als u besluit om federatie te gebruiken met Active Directory Federation Services (AD FS) of andere identiteitsproviders, u optioneel wachtwoordhashsynchronisatie instellen als back-up voor het geval uw on-premises servers uitvallen of tijdelijk niet meer beschikbaar zijn. Met deze synchronisatie kunnen gebruikers zich aanmelden bij de service met hetzelfde wachtwoord dat ze gebruiken om zich aan te melden bij hun on-premises Active Directory-exemplaar. Het stelt identiteitsbescherming ook in staat om gecompromitteerde referenties te detecteren door gesynchroniseerde wachtwoordhashes te vergelijken met wachtwoorden waarvan bekend is dat ze zijn gecompromitteerd, als een gebruiker hetzelfde e-mailadres en wachtwoord heeft gebruikt voor andere services die niet zijn verbonden met Azure AD.

Zie [Synchronisatie van wachtwoordhash implementeren met Azure AD Connect-synchronisatie](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)voor meer informatie.

**Aanbevolen procedures:** gebruik Azure AD voor nieuwe toepassingsontwikkeling voor verificatie.
**Detail:** Gebruik de juiste mogelijkheden om verificatie te ondersteunen:

  - Azure AD voor werknemers
  - [Azure AD B2B](../../active-directory/b2b/index.yml) voor gastgebruikers en externe partners
  - [Azure AD B2C](../../active-directory-b2c/index.yml) om te bepalen hoe klanten zich aanmelden, zich aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken

Organisaties die hun on-premises identiteit niet integreren met hun cloudidentiteit, kunnen meer overhead hebben bij het beheren van accounts. Deze overhead verhoogt de kans op fouten en inbreuken op de beveiliging.

> [!Note]
> U moet kiezen in welke mappen kritieke accounts zich bevinden en of het gebruikte beheerwerkstation wordt beheerd door nieuwe cloudservices of bestaande processen. Het gebruik van bestaande beheer- en identiteitsinrichtingsprocessen kan sommige risico's verminderen, maar kan ook het risico creëren dat een aanvaller een on-premises account in gevaar brengt en naar de cloud draait. U een andere strategie gebruiken voor verschillende rollen (bijvoorbeeld IT-beheerders versus beheerders van business unit). U hebt twee opties. De eerste optie is het maken van Azure AD-accounts die niet zijn gesynchroniseerd met uw on-premises Active Directory-exemplaar. Sluit je aan bij je beheerderswerkstation voor Azure AD, dat u beheren en patchen met Microsoft Intune. De tweede optie is het gebruik van bestaande beheerdersaccounts door te synchroniseren met uw on-premises Active Directory-exemplaar. Gebruik bestaande werkstations in uw Active Directory-domein voor beheer en beveiliging.

## <a name="manage-connected-tenants"></a>Verbonden tenants beheren
Uw beveiligingsorganisatie heeft zichtbaarheid nodig om risico's te beoordelen en te bepalen of het beleid van uw organisatie en eventuele wettelijke vereisten worden gevolgd. U moet ervoor zorgen dat uw beveiligingsorganisatie inzicht heeft in alle abonnementen die zijn verbonden met uw productieomgeving en netwerk (via [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) of [site-to-site VPN).](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) Een [globale beheerder/bedrijfsbeheerder](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) in Azure AD kan de toegang tot de functie [Gebruikerstoegangsbeheerder](../../role-based-access-control/built-in-roles.md#user-access-administrator) verhogen en alle abonnementen en beheerde groepen bekijken die zijn verbonden met uw omgeving.

Zie [verhoogde toegang om alle Azure-abonnementen en beheergroepen](../../role-based-access-control/elevate-access-global-admin.md) te beheren om ervoor te zorgen dat u en uw beveiligingsgroep alle abonnementen of beheergroepen kunnen bekijken die zijn verbonden met uw omgeving. U moet deze verhoogde toegang verwijderen nadat u risico's hebt beoordeeld.

## <a name="enable-single-sign-on"></a>Eén aanmelding inschakelen

In een mobile-first, cloud-first wereld, wilt u single sign-on (SSO) inschakelen op apparaten, apps en services van overal, zodat uw gebruikers productief kunnen zijn, waar en wanneer dan ook. Wanneer u meerdere identiteitsoplossingen moet beheren, wordt dit een administratief probleem, niet alleen voor IT, maar ook voor gebruikers die meerdere wachtwoorden moeten onthouden.

Door dezelfde identiteitsoplossing te gebruiken voor al uw apps en resources, u SSO bereiken. En uw gebruikers kunnen dezelfde set referenties gebruiken om zich aan te melden en toegang te krijgen tot de resources die ze nodig hebben, of de resources zich on-premises of in de cloud bevinden.

**Beste praktijken**: SSO inschakelen.  
**Detail:** Azure AD [breidt on-premises Active Directory uit](/azure/active-directory/connect/active-directory-aadconnect) naar de cloud. Gebruikers kunnen hun primaire werk- of schoolaccount gebruiken voor hun met een domein verbonden apparaten, bedrijfsbronnen en alle web- en SaaS-toepassingen die ze nodig hebben om hun werk gedaan te krijgen. Gebruikers hoeven niet meerdere sets gebruikersnamen en wachtwoorden te onthouden en hun toegang tot hun toepassing kan automatisch worden ingericht (of gedeprovisioneerd) op basis van hun lidmaatschap van hun organisatiegroep en hun status als werknemer. En u kunt deze toegang beheren voor galerie-apps of voor uw eigen on-premises apps die u hebt ontwikkeld en gepubliceerd via de [Azure AD-toepassingsproxy](/azure/active-directory/active-directory-application-proxy-get-started).

Gebruik SSO om gebruikers toegang te geven tot hun [SaaS-toepassingen](/azure/active-directory/active-directory-appssoaccess-whatis) op basis van hun werk- of schoolaccount in Azure AD. Dit geldt niet alleen voor Microsoft SaaS-apps, maar ook voor andere apps, zoals [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) en [Salesforce.](/azure/active-directory/active-directory-saas-salesforce-tutorial) U uw toepassing configureren om Azure AD te gebruiken als [een saml-gebaseerde identiteitsprovider.](/azure/active-directory/fundamentals-identity) Als beveiligingsbesturingselement geeft Azure AD geen token uit waarmee gebruikers zich bij de toepassing kunnen aanmelden, tenzij ze toegang hebben gekregen via Azure AD. U rechtstreeks toegang verlenen of via een groep waarvan gebruikers lid zijn.

Organisaties die geen gemeenschappelijke identiteit maken om SSO voor hun gebruikers en toepassingen in te stellen, worden meer blootgesteld aan scenario's waarin gebruikers meerdere wachtwoorden hebben. Deze scenario's vergroten de kans dat gebruikers wachtwoorden hergebruiken of zwakke wachtwoorden gebruiken.

## <a name="turn-on-conditional-access"></a>Voorwaardelijke toegang inschakelen

Gebruikers hebben overal toegang tot de bronnen van uw organisatie door verschillende apparaten en apps te gebruiken. Als IT-beheerder wilt u ervoor zorgen dat deze apparaten voldoen aan uw normen voor beveiliging en naleving. Alleen focussen op wie toegang heeft tot een resource is niet meer voldoende.

Om de veiligheid en productiviteit in evenwicht te brengen, moet u nadenken over hoe een resource wordt benaderd voordat u een beslissing nemen over toegangscontrole. Met voorwaardelijke toegang tot Azure AD u aan deze vereiste voldoen. Met Voorwaardelijke toegang u geautomatiseerde toegangscontrolebeslissingen nemen op basis van de voorwaarden voor toegang tot uw cloud-apps.

**Aanbevolen procedures**: Beheer en beheer de toegang tot bedrijfsbronnen.  
**Detail:** Configureer voorwaardelijke [toegang tot](/azure/active-directory/active-directory-conditional-access-azure-portal) Azure AD op basis van een groep, locatie en toepassingsgevoeligheid voor SaaS-apps en met Azure AD verbonden apps.

**Aanbevolen procedures**: Verouderde verificatieprotocollen blokkeren.
**Detail:** Aanvallers exploiteren zwakke punten in oudere protocollen elke dag, met name voor wachtwoord spray aanvallen. Voorwaardelijke toegang configureren om verouderde protocollen te blokkeren. Zie de video [Azure AD: Do's en Don'ts](https://www.youtube.com/watch?v=wGk0J4z90GI) voor meer informatie.

## <a name="plan-for-routine-security-improvements"></a>Plannen voor routinematige beveiligingsverbeteringen

Beveiliging is altijd in ontwikkeling en het is belangrijk om in te bouwen in uw cloud- en identiteitsbeheerframework een manier om regelmatig groei te laten zien en nieuwe manieren te ontdekken om uw omgeving te beveiligen.

Identity Secure Score is een set aanbevolen beveiligingsbesturingselementen die Microsoft publiceert en die werkt om u een numerieke score te bieden om uw beveiligingshouding objectief te meten en toekomstige beveiligingsverbeteringen te helpen plannen. U uw score ook bekijken in vergelijking met die in andere industrieën, evenals uw eigen trends in de tijd.

**Aanbevolen procedures**: Plan routinematige beveiligingsbeoordelingen en -verbeteringen op basis van aanbevolen procedures in uw branche.
**Detail:** Gebruik de functie Identity Secure Score om uw verbeteringen in de loop van de tijd te rangschikken.

## <a name="enable-password-management"></a>Wachtwoordbeheer inschakelen

Als u meerdere tenants hebt of als u gebruikers in staat wilt stellen hun eigen wachtwoorden opnieuw in te [stellen,](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)is het belangrijk dat u het juiste beveiligingsbeleid gebruikt om misbruik te voorkomen.

**Aanbevolen procedures:** Stel het opnieuw instellen van selfservicewachtwoorden (SSPR) in voor uw gebruikers.  
**Detail:** Gebruik de functie [voor het opnieuw instellen van azure AD-wachtwoorden.](/azure/active-directory-b2c/active-directory-b2c-reference-sspr)

**Best practice**: Monitor hoe of of SSPR echt wordt gebruikt.  
**Detail:** Controleer de gebruikers die zich registreren met behulp van het [rapport Registratieactiviteit registratieactiviteit voor azure AD-wachtwoord](/azure/active-directory/active-directory-passwords-get-insights)opnieuw instellen . De rapportagefunctie die Azure AD biedt, helpt u vragen te beantwoorden met vooraf gebouwde rapporten. Als u een licentie hebt, u ook aangepaste query's maken.

**Aanbevolen procedures:** Breid wachtwoordbeleid in de cloud uit naar uw on-premises infrastructuur.
**Detail:** Verbeter het wachtwoordbeleid in uw organisatie door dezelfde controles uit te voeren voor on-premises wachtwoordwijzigingen als voor wachtwoordwijzigingen in de cloud. Installeer [Azure AD-wachtwoordbeveiliging](/azure/active-directory/authentication/concept-password-ban-bad) voor Windows Server Active Directory-agents on-premises om geblokkeerde wachtwoordlijsten uit te breiden naar uw bestaande infrastructuur. Gebruikers en beheerders die on-premises wachtwoorden wijzigen, instellen of opnieuw instellen, moeten voldoen aan hetzelfde wachtwoordbeleid als gebruikers die alleen in de cloud zijn.

## <a name="enforce-multi-factor-verification-for-users"></a>Multifactorverificatie afdwingen voor gebruikers

We raden u aan verificatie in twee stappen voor al uw gebruikers te vereisen. Dit geldt ook voor beheerders en anderen in uw organisatie die een aanzienlijke impact kunnen hebben als hun account wordt gecompromitteerd (bijvoorbeeld financiële functionarissen).

Er zijn meerdere opties voor het vereisen van tweestapsverificatie. De beste optie voor u is afhankelijk van uw doelen, de Azure AD-editie die u uitvoert en uw licentieprogramma. Zie [Hoe u verificatie in twee stappen vereist voor een gebruiker](/azure/active-directory/authentication/howto-mfa-userstates) om de beste optie voor u te bepalen. Zie de prijspagina's [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) en [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) voor meer informatie over licenties en prijzen.

Hieronder volgen opties en voordelen voor het inschakelen van verificatie in twee stappen:

**Optie 1**: MFA inschakelen voor alle gebruikers en aanmeldingsmethoden met Azure AD Security Defaults **Benefit:** met deze optie u MFA eenvoudig en snel afdwingen voor alle gebruikers in uw omgeving met een strikt beleid om:

* Administratieve rekeningen en administratieve aanmeldingsmechanismen uitdagen
* Vereisen MFA uitdaging via Microsoft Authenticator voor alle gebruikers
* Verouderde verificatieprotocollen beperken.

Deze methode is beschikbaar voor alle licentieniveaus, maar kan niet worden gemengd met bestaande beleid voor voorwaardelijke toegang. Meer informatie vindt u in Azure AD Security Defaults

**Optie 2**: [Multi-Factor Authentication inschakelen door de gebruikersstatus te wijzigen](../../active-directory/authentication/howto-mfa-userstates.md).   
**Voordeel**: Dit is de traditionele methode voor het vereisen van tweestapsverificatie. Het werkt met zowel [Azure Multi-Factor Authentication in de cloud als Azure Multi-Factor Authentication Server.](/azure/active-directory/authentication/concept-mfa-whichversion) Met deze methode moeten gebruikers tweestapsverificatie uitvoeren elke keer dat ze zich aanmelden en beleid voor voorwaardelijke toegang overschrijven.

Zie Welke versie van Azure MFA geschikt [is voor mijn organisatie om](/azure/active-directory/authentication/concept-mfa-whichversion)te bepalen waar multi-factorauthenticatie moet worden ingeschakeld? .

**Optie 3**: [Multi-Factor Authentication inschakelen met beleid voor voorwaardelijke toegang](/azure/active-directory/authentication/howto-mfa-getstarted).
**Voordeel:** Met deze optie u vragen om verificatie in twee stappen onder specifieke voorwaarden met behulp van [voorwaardelijke toegang.](/azure/active-directory/active-directory-conditional-access-azure-portal) Specifieke voorwaarden kunnen gebruikersaanmelding zijn vanaf verschillende locaties, niet-vertrouwde apparaten of toepassingen die u als riskant beschouwt. Door specifieke voorwaarden te definiëren waarbij u verificatie in twee stappen nodig hebt, u voorkomen dat uw gebruikers voortdurend worden gevraagd, wat een onaangename gebruikerservaring kan zijn.

Dit is de meest flexibele manier om verificatie in twee stappen voor uw gebruikers mogelijk te maken. Het inschakelen van een beleid voor voorwaardelijke toegang werkt alleen voor Azure Multi-Factor Authentication in de cloud en is een premium functie van Azure AD. Meer informatie over deze methode vindt u in [Azure Multi-Factor Authentication implementeren](/azure/active-directory/authentication/howto-mfa-getstarted)in de cloud.

**Optie 4**: Multi-Factor Authentication inschakelen met beleid voor voorwaardelijke toegang door het gebruikers- en aanmeldingsrisico van [Azure AD-identiteitsbeveiliging](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa)te evalueren.   
**Voordeel:** Met deze optie u:

* Detecteer potentiële kwetsbaarheden die van invloed zijn op de identiteit van uw organisatie.
* Configureer geautomatiseerde antwoorden op gedetecteerde verdachte acties die gerelateerd zijn aan de identiteit van uw organisatie.
* Onderzoek verdachte incidenten en onderneem passende maatregelen om deze op te lossen.

Deze methode maakt gebruik van de risicobeoordeling van Azure AD Identity Protection om te bepalen of verificatie in twee stappen vereist is op basis van gebruikers- en aanmeldingsrisico's voor alle cloudtoepassingen. Voor deze methode is Azure Active Directory P2-licenties vereist. Meer informatie over deze methode vindt u in [Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview).

> [!Note]
> Optie 1, multifactorverificatie inschakelen door de gebruikersstatus te wijzigen, overschrijft beleid voor voorwaardelijke toegang. Omdat opties 2 en 3 gebruikmaken van beleid voor voorwaardelijke toegang, u optie 1 niet met deze opties gebruiken.

Organisaties die geen extra lagen van identiteitsbescherming toevoegen, zoals verificatie in twee stappen, zijn gevoeliger voor diefstalaanvallen met referenties. Een aanval op diefstal van referenties kan leiden tot een datalek.

## <a name="use-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer gebruiken

Toegangsbeheer voor cloudresources is essentieel voor elke organisatie die de cloud gebruikt. [Met RBAC (Role-based access control)](/azure/role-based-access-control/overview)u beheren wie toegang heeft tot Azure-bronnen, wat ze met die bronnen kunnen doen en tot welke gebieden ze toegang hebben.

Het aanwijzen van groepen of individuele rollen die verantwoordelijk zijn voor specifieke functies in Azure, helpt verwarring te voorkomen die kan leiden tot fouten in de menselijke en automatiseringsautomatisering die beveiligingsrisico's met zich meebrengen. Het beperken van de toegang op basis van de [noodzaak om beveiligingsprincipes te kennen](https://en.wikipedia.org/wiki/Need_to_know) en de minste [bevoegdheden](https://en.wikipedia.org/wiki/Principle_of_least_privilege) te kennen, is noodzakelijk voor organisaties die beveiligingsbeleid voor gegevenstoegang willen afdwingen.

Uw beveiligingsteam heeft inzicht nodig in uw Azure-bronnen om risico's te kunnen beoordelen en te herstellen. Als het beveiligingsteam operationele verantwoordelijkheden heeft, hebben ze extra machtigingen nodig om hun werk te kunnen doen.

U [RBAC](/azure/role-based-access-control/overview) gebruiken om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen op een bepaald bereik. Het bereik van een roltoewijzing kan een abonnement, een resourcegroep of een enkele resource zijn.

**Aanbevolen procedures**: Seis taken binnen uw team en geef alleen de hoeveelheid toegang aan gebruikers die ze nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen te geven in uw Azure-abonnement of -bronnen, staan alleen bepaalde acties op een bepaald bereik toe.
**Detail:** Gebruik [ingebouwde RBAC-rollen](/azure/role-based-access-control/built-in-roles) in Azure om bevoegdheden toe te wijzen aan gebruikers.

> [!Note]
> Specifieke machtigingen creëren onnodige complexiteit en verwarring, accumuleren in een "legacy" configuratie die moeilijk op te lossen zonder angst voor het breken van iets. Vermijd resourcespecifieke bevoegdheden. Gebruik in plaats daarvan beheergroepen voor bedrijfsbrede machtigingen en resourcegroepen voor machtigingen binnen abonnementen. Vermijd gebruikersspecifieke machtigingen. Wijs in plaats daarvan toegang toe aan groepen in Azure AD.

**Aanbevolen procedures:** Geef beveiligingsteams met Azure-verantwoordelijkheden toegang tot Azure-bronnen, zodat ze risico's kunnen beoordelen en herstellen.
**Detail**: Geef beveiligingsteams de Rol van RBAC [Security Reader](/azure/role-based-access-control/built-in-roles#security-reader) toe. U de hoofdbeheergroep of de segmentbeheergroep gebruiken, afhankelijk van de reikwijdte van de verantwoordelijkheden:

* **Root management groep** voor teams die verantwoordelijk zijn voor alle bedrijfsresources
* **Segment management groep** voor teams met een beperkt bereik (meestal vanwege regelgeving of andere organisatorische grenzen)

**Aanbevolen procedures**: Geef de juiste machtigingen aan beveiligingsteams die directe operationele verantwoordelijkheden hebben.
**Detail:** Bekijk de rbac-ingebouwde rollen voor de juiste roltoewijzing. Als de ingebouwde rollen niet voldoen aan de specifieke behoeften van uw organisatie, u [aangepaste rollen voor Azure-resources maken.](/azure/role-based-access-control/custom-roles) Net als bij ingebouwde rollen u aangepaste rollen toewijzen aan gebruikers, groepen en serviceprincipals bij abonnements-, resourcegroep- en resourcescopes.

**Aanbevolen procedures**: Verontrede Azure Security Center toegang tot beveiligingsrollen die dit nodig hebben. Met Security Center kunnen beveiligingsteams risico's snel identificeren en herstellen.
**Detail:** Voeg beveiligingsteams met deze behoeften toe aan de rol [RBAC-beveiligingsbeheer,](/azure/role-based-access-control/built-in-roles#security-admin) zodat ze beveiligingsbeleid kunnen bekijken, beveiligingsstatussen kunnen bekijken, beveiligingsbeleid kunnen bewerken, waarschuwingen en aanbevelingen kunnen bekijken en waarschuwingen en aanbevelingen kunnen negeren. U dit doen door gebruik te maken van de root management groep of de segment management groep, afhankelijk van de reikwijdte van de verantwoordelijkheden.

Organisaties die het beheer van gegevenstoegang niet afdwingen met behulp van mogelijkheden zoals RBAC, geven mogelijk meer bevoegdheden dan nodig is voor hun gebruikers. Dit kan leiden tot datacompromitterende gegevens doorgebruikers toegang te geven tot soorten gegevens (bijvoorbeeld een hoge bedrijfsimpact) die ze niet zouden moeten hebben.

## <a name="lower-exposure-of-privileged-accounts"></a>Lagere blootstelling van geprivilegieerde accounts

Het beveiligen van bevoorrechte toegang is een cruciale eerste stap voor de bescherming van bedrijfsmiddelen. Het minimaliseren van het aantal mensen dat toegang heeft tot beveiligde informatie of bronnen vermindert de kans dat een kwaadwillende gebruiker toegang krijgt of een geautoriseerde gebruiker die per ongeluk een gevoelige bron beïnvloedt.

Geprivilegieerde accounts zijn accounts die IT-systemen beheren en beheren. Cyberaanvallers richten zich op deze accounts om toegang te krijgen tot de gegevens en systemen van een organisatie. Om bevoorrechte toegang te beveiligen, moet u de accounts en systemen isoleren van het risico om te worden blootgesteld aan een kwaadwillende gebruiker.

We raden u aan een roadmap te ontwikkelen en te volgen om bevoorrechte toegang tegen cyberaanvallers te beveiligen. Voor informatie over het maken van een gedetailleerde roadmap voor het beveiligen van identiteiten en toegang die worden beheerd of gerapporteerd in Azure AD, Microsoft Azure, Office 365 en andere cloudservices, controleert [u Privileged Access beveiligen voor hybride en cloudimplementaties in Azure AD.](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

Het volgende bevat de aanbevolen procedures voor [het beveiligen van bevoorrechte toegang voor hybride en cloudimplementaties in Azure AD:](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Aanbevolen procedures**: Beheer, bedien en controleer de toegang tot bevoegde accounts.   
**Detail:** Azure [AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)inschakelen . Nadat u Privileged Identity Management hebt ingeschakeld, ontvangt u e-mailberichten met meldingen voor wijzigingen in de bevoegdheden voor bevoorrechte toegang. Deze meldingen bieden vroegtijdige waarschuwing wanneer extra gebruikers worden toegevoegd aan zeer bevoorrechte rollen in uw directory.

**Aanbevolen procedures**: Zorg ervoor dat alle kritieke beheeraccounts Azure AD-accounts worden beheerd.
**Detail:** verwijder consumentenaccounts uit kritieke beheerdersrollen (bijvoorbeeld Microsoft-accounts zoals hotmail.com, live.com en outlook.com).

**Aanbevolen procedures:** Zorg ervoor dat alle kritieke beheerdersrollen een apart account hebben voor administratieve taken om phishing en andere aanvallen te voorkomen om beheerdersrechten te compromitteren.
**Detail:** Maak een apart beheerdersaccount waaraan de bevoegdheden zijn toegewezen die nodig zijn om de beheertaken uit te voeren. Blokkeer het gebruik van deze administratieve accounts voor dagelijkse productiviteitstools zoals Microsoft Office 365-e-mail of willekeurig surfen op het web.

**Aanbevolen procedures**: Identificeer en categoriseer accounts in zeer bevoorrechte rollen.   
**Detail:** Bekijk na het inschakelen van Azure AD Privileged Identity Management de gebruikers die zich in de globale beheerder, bevoorrechte rolbeheerder en andere zeer bevoorrechte rollen bevinden. Verwijder accounts die niet meer nodig zijn in die rollen en categoriseer de resterende accounts die zijn toegewezen aan beheerdersrollen:

* Individueel toegewezen aan administratieve gebruikers en kan worden gebruikt voor niet-administratieve doeleinden (bijvoorbeeld persoonlijke e-mail)
* Individueel toegewezen aan administratieve gebruikers en alleen aangewezen voor administratieve doeleinden
* Gedeeld tussen meerdere gebruikers
* Voor noodtoegangsscenario's
* Voor geautomatiseerde scripts
* Voor externe gebruikers

**Aanbevolen procedures**: Implementeer "just in time" (JIT) toegang om de belichtingstijd van privileges verder te verlagen en uw zichtbaarheid in het gebruik van bevoorrechte accounts te vergroten.   
**Detail:** Met Azure AD Privileged Identity Management u:

* Beperk gebruikers tot het alleen overnemen van hun privileges JIT.
* Rolrollen toewijzen voor een verkorte duur met het vertrouwen dat de bevoegdheden automatisch worden ingetrokken.

**Aanbevolen procedures**: Definieer ten minste twee noodtoegangsaccounts.   
**Detail:** Met noodtoegangsaccounts kunnen organisaties privileged access beperken in een bestaande Azure Active Directory-omgeving. Deze accounts zijn zeer geprivilegieerd en worden niet toegewezen aan specifieke personen. Accounts voor noodtoegang zijn beperkt tot scenario's waarin normale beheerdersaccounts niet kunnen worden gebruikt. Organisaties moeten het gebruik van het noodaccount beperken tot slechts de benodigde hoeveelheid tijd.

Evalueer de accounts die zijn toegewezen of in aanmerking komen voor de globale beheerdersrol. Als u geen cloudaccounts ziet met het `*.onmicrosoft.com` domein (bedoeld voor noodtoegang), maakt u deze. Zie [Beheer van beheervan beheeraccounts voor noodtoegang in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)voor meer informatie.

**Beste praktijken**: Hebben een "break glass" proces in geval van nood.
**Detail:** Volg de stappen in [Het beveiligen van bevoorrechte toegang voor hybride en cloudimplementaties in Azure AD.](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Aanbevolen procedures**: Vereisen dat alle kritieke beheerdersaccounts wachtwoordloos (voorkeur) zijn of multifactorauthenticatie vereisen.
**Detail:** Gebruik de [Microsoft Authenticator-app](/azure/active-directory/authentication/howto-authentication-phone-sign-in) om u aan te melden bij een Azure AD-account zonder een wachtwoord te gebruiken. Net als [Bij Windows Hello voor Bedrijven](/windows/security/identity-protection/hello-for-business/hello-identity-verification)gebruikt de Microsoft Authenticator verificatie op basis van sleutels om een gebruikersreferentie in te schakelen die is gekoppeld aan een apparaat en biometrische verificatie of een pincode gebruikt.

Azure Multi-Factor Authentication vereisen bij aanmelding voor alle individuele gebruikers die permanent zijn toegewezen aan een of meer van de Azure AD-beheerdersrollen: Globale beheerder, beheerder van bevoorrechte rollen, Exchange Online-beheerder en SharePoint Online-beheerder. Schakel [Multi-Factor Authentication in voor uw beheerdersaccounts](/azure/active-directory/authentication/howto-mfa-userstates) en zorg ervoor dat beheerdersaccountgebruikers zich hebben geregistreerd.

**Aanbevolen procedures**: Voor kritieke beheerdersaccounts hebt u een beheerwerkstation waar productietaken niet zijn toegestaan (bijvoorbeeld browsen en e-mail). Dit beschermt uw beheerdersaccounts tegen aanvalsvectoren die browsen en e-mail gebruiken en verlaagt uw risico op een groot incident aanzienlijk.
**Detail:** Gebruik een beheerwerkstation. Kies een niveau van workstationbeveiliging:

- Zeer veilige productiviteitsapparaten bieden geavanceerde beveiliging voor browsen en andere productiviteitstaken.
- [Privileged Access Workstations (PAWs)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) bieden een speciaal besturingssysteem dat is beschermd tegen internetaanvallen en bedreigingsvectoren voor gevoelige taken.

**Aanbevolen procedures**: Beheeraccounts deprovisionen wanneer werknemers uw organisatie verlaten.
**Detail:** Heb een proces dat beheerdersaccounts uitschakelt of verwijdert wanneer werknemers uw organisatie verlaten.

**Aanbevolen procedures**: Test regelmatig beheerdersaccounts met behulp van huidige aanvalstechnieken.
**Detail:** Gebruik Office 365 Attack Simulator of een aanbod van derden om realistische aanvalsscenario's in uw organisatie uit te voeren. Dit kan u helpen kwetsbare gebruikers te vinden voordat een echte aanval plaatsvindt.

**Aanbevolen procedures**: Neem maatregelen om de meest gebruikte aangevallen technieken te beperken.  
**Detail:** [Microsoft-accounts identificeren in administratieve rollen die moeten worden overgeschakeld naar werk- of schoolaccounts](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Zorgen voor afzonderlijke gebruikersaccounts en e-maildoorsturen voor algemene beheerdersaccounts](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Ervoor zorgen dat de wachtwoorden van administratieve accounts onlangs zijn gewijzigd](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Synchronisatie van wachtwoordhash inschakelen](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Multi-Factor Authentication vereisen voor gebruikers in alle bevoorrechte rollen en blootgestelde gebruikers](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Uw Office 365-beveiligde score verkrijgen (als u Office 365 gebruikt)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[De beveiligings- en nalevingsrichtlijnen van Office 365 controleren (als u Office 365 gebruikt)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Office 365-activiteitscontrole configureren (als u Office 365 gebruikt)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Eigenaren van incident-/noodoplossingen instellen](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Beveiligde on-premises bevoorrechte administratieve accounts](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Als u geen bevoorrechte toegang beveiligt, u merken dat u te veel gebruikers in zeer bevoorrechte rollen hebt en kwetsbaarder bent voor aanvallen. Kwaadwillende actoren, waaronder cyberaanvallers, richten zich vaak op beheerdersaccounts en andere elementen van bevoorrechte toegang om toegang te krijgen tot gevoelige gegevens en systemen met behulp van diefstal van referenties.

## <a name="control-locations-where-resources-are-created"></a>Locaties beheren waar resources worden gemaakt

Het is erg belangrijk om cloudoperators in staat te stellen taken uit te voeren en tegelijkertijd te voorkomen dat ze conventies breken die nodig zijn om de resources van uw organisatie te beheren. Organisaties die de locaties willen beheren waar resources worden gemaakt, moeten deze locaties hard coderen.

U [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) gebruiken om beveiligingsbeleid te maken waarvan de definities de acties of resources beschrijven die specifiek worden geweigerd. U wijst deze beleidsdefinities toe op het gewenste bereik, zoals het abonnement, de resourcegroep of een afzonderlijke resource.

> [!NOTE]
> Beveiligingsbeleid is niet hetzelfde als RBAC. Ze gebruiken RBAC om gebruikers te autoriseren om deze bronnen te maken.
>
>

Organisaties die niet bepalen hoe resources worden gemaakt, zijn gevoeliger voor gebruikers die misbruik kunnen maken van de service door meer resources te maken dan ze nodig hebben. Het verharden van het proces voor het maken van resources is een belangrijke stap om een multitenant-scenario te beveiligen.

## <a name="actively-monitor-for-suspicious-activities"></a>Actief controleren op verdachte activiteiten

Een actief identiteitsbewakingssysteem kan snel verdacht gedrag detecteren en een waarschuwing activeren voor verder onderzoek. In de volgende tabel worden twee Azure AD-mogelijkheden weergegeven waarmee organisaties hun identiteit kunnen controleren:

**Beste praktijken**: Hebben een methode om te identificeren:

- Pogingen om in te loggen [zonder getraceerd te worden.](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources)
- [Brute kracht](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) aanvallen tegen een bepaalde rekening.
- Pogingen om in te loggen vanaf meerdere locaties.
- Aanmeldingen van [geïnfecteerde apparaten](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Verdachte IP-adressen.

**Detail:** Gebruik Azure AD [Premium-anomalierapporten](/azure/active-directory/active-directory-view-access-usage-reports). Beschikken over processen en procedures voor IT-beheerders om deze rapporten dagelijks of op aanvraag uit te voeren (meestal in een incidentresponsescenario).

**Aanbevolen procedures**: Zorg voor een actief monitoringsysteem dat u op de hoogte stelt van risico's en het risiconiveau (hoog, gemiddeld of laag) kan aanpassen aan uw bedrijfsbehoeften.   
**Detail:** Gebruik [Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection), die de huidige risico's op het eigen dashboard markeert en dagelijkse overzichtsmeldingen via e-mail verzendt. Om de identiteit van uw organisatie te beschermen, u op risico's gebaseerdbeleid configureren dat automatisch reageert op gedetecteerde problemen wanneer een bepaald risiconiveau is bereikt.

Organisaties die hun identiteitssystemen niet actief controleren, lopen het risico dat gebruikersreferenties worden aangetast. Zonder te weten dat verdachte activiteiten plaatsvinden via deze referenties, kunnen organisaties dit soort bedreigingen niet beperken.

## <a name="use-azure-ad-for-storage-authentication"></a>Azure AD gebruiken voor opslagverificatie
[Azure Storage](/azure/storage/common/storage-auth-aad) ondersteunt verificatie en autorisatie met Azure AD voor Blob-opslag en wachtrijopslag. Met Azure AD-verificatie u het op Azure-rollen gebaseerde toegangsbeheer gebruiken om specifieke machtigingen toe te kennen aan gebruikers, groepen en toepassingen tot aan het bereik van een afzonderlijke blobcontainer of -wachtrij.

We raden u aan Azure AD te gebruiken [voor het verifiëren van toegang tot opslag.](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

## <a name="next-step"></a>Volgende stap

Zie [aanbevolen procedures en patronen voor Azure-beveiliging](best-practices-and-patterns.md) voor meer aanbevolen procedures voor beveiliging die u gebruiken bij het ontwerpen, implementeren en beheren van uw cloudoplossingen met Azure.

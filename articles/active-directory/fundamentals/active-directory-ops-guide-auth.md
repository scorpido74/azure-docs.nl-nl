---
title: Naslag Gids voor verificatie beheer van Azure Active Directory
description: In deze hand leiding voor bewerkingen worden de controles en acties beschreven die u moet uitvoeren om het verificatie beheer te beveiligen
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 934fe8271796ed6196c9e50a0eddd5d7de3d8432
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511889"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Naslag Gids voor verificatie beheer van Azure Active Directory

In deze sectie van de [Naslag Gids voor Azure AD-bewerkingen](active-directory-ops-guide-intro.md) worden de controles en acties beschreven die u moet uitvoeren voor het beveiligen en beheren van referenties, het definiëren van de verificatie, het toewijzen van de toewijzing, het meten van het gebruik en het definiëren van toegangs beleid op basis van de beveiligings postuur van de onderneming.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de datum van publicatie, maar kunnen worden gewijzigd in de loop van de tijd. Organisaties moeten voortdurend hun identiteits methoden evalueren omdat micro soft-producten en-services zich in de loop van de tijd ontwikkelen.

## <a name="key-operational-processes"></a>Belangrijkste operationele processen

### <a name="assign-owners-to-key-tasks"></a>Eigen aren toewijzen aan belang rijke taken

Voor het beheren van Azure Active Directory is de continue uitvoering van belang rijke operationele taken en-processen vereist, die mogelijk geen deel uitmaken van een implementatie project. Het is nog belang rijk dat u deze taken instelt om uw omgeving te optimaliseren. De belangrijkste taken en de aanbevolen eigen aren zijn onder andere:

| Taak | Eigenaar |
| :- | :- |
| Configuratie van eenmalige aanmelding (SSO) in azure AD beheren | IAM Operations-team |
| Beleid voor voorwaardelijke toegang ontwerpen voor Azure AD-toepassingen | Team van InfoSec-architectuur |
| Aanmeld activiteit archiveren in een SIEM-systeem | InfoSec Operations-team |
| Risico gebeurtenissen in een SIEM-systeem archiveren | InfoSec Operations-team |
| Beveiligings rapporten sorteren en onderzoeken | InfoSec Operations-team |
| Sorteren en onderzoeken van risico gebeurtenissen | InfoSec Operations-team |
| Sorteren en onderzoek gebruikers die zijn gemarkeerd voor risico-en kwets bare rapporten van Azure AD Identity Protection | InfoSec Operations-team |

> [!NOTE]
> Voor Azure AD Identity Protection is een Azure AD Premium P2-licentie vereist. Zie de [Algemeen beschik bare functies van de Azure ad free en Azure AD Premium edities vergelijken](https://azure.microsoft.com/pricing/details/active-directory/)om de juiste licentie voor uw vereisten te vinden.

Wanneer u uw lijst bekijkt, moet u mogelijk een eigenaar toewijzen voor taken waarvoor een eigenaar ontbreekt of het eigendom van taken met eigen aars aanpassen die niet zijn afgestemd op de bovenstaande aanbevelingen.

#### <a name="owner-recommended-reading"></a>Door eigenaar aanbevolen lezen

- [Beheerdersrollen toewijzen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Referentiebeheer

### <a name="password-policies"></a>Wachtwoordbeleid

Het veilig beheren van wacht woorden is een van de meest essentiële onderdelen van identiteits-en toegangs beheer en vaak het grootste doel van aanvallen. Azure AD biedt ondersteuning voor verschillende functies die kunnen helpen voor komen dat een aanval slaagt.

Gebruik de onderstaande tabel om de aanbevolen oplossing te vinden voor het beperken van het probleem dat moet worden opgelost:

| Probleem | Aanbeveling |
| :- | :- |
| Geen mechanisme om te beveiligen tegen zwakke wacht woorden | [Selfservice voor wachtwoord herstel (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) en [wachtwoord beveiliging](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) voor Azure AD inschakelen |
| Geen mechanisme voor het detecteren van gelekte wacht woorden | [Wacht woord-hash-synchronisatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) inschakelen om inzichten te verkrijgen |
| AD FS gebruiken en kan niet worden verplaatst naar beheerde verificatie | Inschakelen [AD FS extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) en/of [Azure AD Smart-vergren deling](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| Wachtwoord beleid maakt gebruik van regels op basis van complexiteit, zoals lengte, meerdere teken sets of verval datum | Bekijk het voor deel van [Aanbevolen procedures van micro soft](https://aka.ms/passwordguidance) en schakel uw benadering uit voor wachtwoord beheer en implementeer [Azure AD-wachtwoord beveiliging](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad). |
| Gebruikers zijn niet geregistreerd voor het gebruik van multi-factor Authentication (MFA) | [Alle beveiligings gegevens van de gebruiker registreren](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) zodat deze kan worden gebruikt als mechanisme om de identiteit van de gebruiker en het bijbehorende wacht woord te verifiëren |
| Er worden geen wacht woorden ingetrokken op basis van het risico van gebruikers | Azure AD [Identity Protection-beleid voor gebruikers Risico's](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) implementeren om wachtwoord wijzigingen op gelekte referenties te forceren met behulp van SSPR |
| Er is geen slim vergrendelings mechanisme om kwaad aardige verificatie te beschermen tegen beschadigde Actors die afkomstig zijn van geïdentificeerde IP-adressen | Door de Cloud beheerde authenticatie met hash-synchronisatie of [Pass Through-verificatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) implementeren |

#### <a name="password-policies-recommended-reading"></a>Wachtwoord beleid wordt aanbevolen te lezen

- [Azure AD en AD FS best practices: verdediging tegen wachtwoord spray-Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Self-service voor wacht woord opnieuw instellen en wachtwoord beveiliging inschakelen

Gebruikers die hun wacht woord moeten wijzigen of opnieuw moeten instellen, zijn een van de grootste bronnen van het volume en de kosten van helpdesk oproepen. Naast de kosten is het wijzigen van het wacht woord als een hulp programma voor het beperken van een gebruikers risico een fundamentele stap bij het verbeteren van de beveiligings postuur van uw organisatie.

U kunt het beste Azure AD [self-service voor wachtwoord herstel](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR) en on-premises [wachtwoord beveiliging](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) implementeren om het volgende te bereiken:

- Bewijkend Help Desk-aanroepen.
- Het gebruik van tijdelijke wacht woorden vervangen.
- Vervang de bestaande selfservice oplossing voor wachtwoord beheer die afhankelijk is van een on-premises oplossing.
- [Elimineer zwakke wacht woorden](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) in uw organisatie.

> [!NOTE]
> Voor organisaties met een Azure AD Premium P2-abonnement, wordt het aanbevolen SSPR te implementeren en deze te gebruiken als onderdeel van een [gebruikers risico beleid voor identiteits beveiliging](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy).

### <a name="strong-credential-management"></a>Sterk referentie beheer

Wacht woorden zijn niet veilig genoeg om te voor komen dat ongeldige actors toegang krijgen tot uw omgeving. Elke gebruiker met een bevoegd account moet mini maal zijn ingeschakeld voor multi-factor Authentication (MFA). In het ideale geval moet u [gecombineerde registratie](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) inschakelen en vereisen dat alle gebruikers zich registreren voor MFA en SSPR met behulp van de [gecombineerde registratie-ervaring](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview). Uiteindelijk raden wij u aan een strategie te nemen om [flexibiliteit te bieden](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) om het risico van vergren deling door onvoorziene omstandigheden te verminderen.

![Stroom voor gecombineerde gebruikers ervaring](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Tolerantie van on-premises storings authenticatie

Naast de voor delen van eenvoud en het inschakelen van lekkende referentie detectie, hebben Azure AD Password Hash Sync (PHS) en Azure MFA gebruikers toegang tot SaaS-toepassingen en Office 365 ondanks lokale storingen als gevolg van Cyber aanvallen zoals [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/). Het is ook mogelijk om PHS in te scha kelen in combi natie met Federatie. Als u PHS inschakelt, kan de verificatie worden teruggevallen wanneer er geen Federatie services beschikbaar zijn.

Als uw on-premises organisatie geen strategie voor een storings tolerantie ondervindt of is die niet is geïntegreerd met Azure AD, moet u Azure AD PHS implementeren en een nood herstel plan definiëren dat PHS bevat. Als Azure AD PHS wordt ingeschakeld, kunnen gebruikers zich bij Azure AD verifiëren als uw on-premises Active Directory niet beschikbaar is.

![wachtwoord hash-synchronisatie stroom](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Zie [de juiste verificatie methode kiezen voor uw Azure Active Directory hybride identiteits oplossing](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)voor meer informatie over uw verificatie opties.

### <a name="programmatic-usage-of-credentials"></a>Programmatisch gebruik van referenties

Azure AD-scripts die gebruikmaken van Graph API beveiligde verificatie vereisen met Power shell of toepassingen. Slecht referentie beheer dat deze scripts en hulpprogram ma's uitvoert, verhogen het risico op referentie diefstal. Als u scripts of toepassingen gebruikt die afhankelijk zijn van wacht woorden of wachtwoord prompts, moet u eerst wacht woorden in configuratie bestanden of bron code controleren en vervolgens deze afhankelijkheden vervangen en Azure Managed-identiteiten, geïntegreerde Windows-verificatie of [certificaten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates) waar mogelijk gebruiken. Voor toepassingen waarbij de vorige oplossingen niet mogelijk zijn, kunt u het beste [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)gebruiken.

Als u vaststelt dat er service-principals zijn met wachtwoord referenties en u niet zeker weet hoe deze wachtwoord referenties worden beveiligd door scripts of toepassingen, neemt u contact op met de eigenaar van de toepassing voor meer informatie over gebruiks patronen.

Micro soft raadt u ook aan om contact op te nemen met eigen aren van toepassingen om gebruiks patronen te begrijpen als er service-principals met wachtwoord referenties zijn.

## <a name="authentication-experience"></a>Verificatie-ervaring

### <a name="on-premises-authentication"></a>On-premises verificatie

Federatieve verificatie met geïntegreerde Windows-verificatie (IWA) of naadloze eenmalige aanmelding (SSO) met wachtwoord hash-synchronisatie of Pass Through-verificatie is de beste gebruikers ervaring in het bedrijfs netwerk met line-of-Insight to on-premises domein controllers. Het minimaliseert de referentie prompt vermoeidheid en vermindert het risico van gebruikers die Prey zijn tegen phishing-aanvallen. Als u al gebruikmaakt van door de Cloud beheerde verificatie met PHS of PTA, maar gebruikers nog steeds hun wacht woord hoeven in te voeren wanneer ze on-premises verifiëren, moet u onmiddellijk [naadloze SSO implementeren](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso). Als u echter op dit moment federatieve bent met plannen die uiteindelijk moeten worden gemigreerd naar door de Cloud beheerde authenticatie, moet u naadloze SSO implementeren als onderdeel van het migratie project.

### <a name="device-trust-access-policies"></a>Toegangs beleid voor vertrouwens relaties van apparaten

Net als bij een gebruiker in uw organisatie is een apparaat een kern identiteit die u wilt beveiligen. U kunt de identiteit van een apparaat gebruiken om uw resources te beschermen op elk gewenst moment en vanaf elke locatie. Het verifiëren van het apparaat en de accounting voor het vertrouwens type verbetert uw beveiligings postuur en-bruikbaarheid door:

- Vermijd wrijving, bijvoorbeeld met MFA, wanneer het apparaat wordt vertrouwd
- Toegang vanaf niet-vertrouwde apparaten blok keren
- Voor Windows 10-apparaten biedt [u probleemloos eenmalige aanmelding bij on-premises bronnen](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso).

U kunt dit doel doen door de apparaat-id's te halen en te beheren in azure AD met behulp van een van de volgende methoden:

- Organisaties kunnen [Microsoft intune](https://docs.microsoft.com/intune/what-is-intune) gebruiken om het apparaat te beheren en nalevings beleid af te dwingen, de status van het apparaat te bevestigen en beleids regels voor voorwaardelijke toegang in te stellen op basis van het feit of het apparaat compatibel is. Microsoft Intune kunt iOS-apparaten, Mac-Bureau bladen (via JAMF-integratie), Windows-Bureau bladen (systeem eigen gebruik van Mobile Device Management voor Windows 10 en co-beheer met micro soft endpoint Configuration Manager) en mobiele Android-apparaten beheren.
- [Hybride Azure AD join](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains) biedt beheer met groeps beleid of micro soft Endpoint-Configuration Manager in een omgeving met Active Directory computers die lid zijn van een domein. Organisaties kunnen een beheerde omgeving implementeren via PHS of PTA met naadloze SSO. Door uw apparaten naar Azure AD te brengen, wordt de gebruikers productiviteit via SSO in uw Cloud en on-premises resources gemaximaliseerd, terwijl u de toegang tot uw Cloud en on-premises resources met [voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) tegelijk kunt beveiligen.

Als u Windows-apparaten die lid zijn van een domein die niet zijn geregistreerd in de Cloud, of Windows-apparaten die lid zijn van het domein die zijn geregistreerd in de Cloud, maar geen beleid voor voorwaardelijke toegang, moet u de niet-geregistreerde apparaten registreren en in beide gevallen [hybride Azure AD join gebruiken als een besturings element](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) in uw beleid voor voorwaardelijke toegang.

![Een scherm opname van de toekenning in het beleid voor voorwaardelijke toegang dat hybride apparaat vereist](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Als u apparaten beheert met MDM of Microsoft Intune, maar niet met behulp van apparaatbesturing in uw beleid voor voorwaardelijke toegang, raden we u aan om het gebruik van een [apparaat vereisen als compatibel te worden gemarkeerd](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant) als een besturings element in dat beleid.

![Een scherm opname van Grant in beleid voor voorwaardelijke toegang dat apparaatcompatibiliteit vereist](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Toegangs beleid voor vertrouwens relaties van apparaten aanbevolen lezen

- [Procedure: uw hybride Azure Active Directory deelname-implementatie plannen](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [Configuraties voor identiteit en Apparaattoegang](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello voor Bedrijven

In Windows 10 vervangt [Windows hello voor bedrijven](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) wacht woorden met een sterke twee ledige verificatie op pc's. Windows hello voor bedrijven biedt een meer gestroomlijnde MFA-ervaring voor gebruikers en vermindert uw afhankelijkheid van wacht woorden. Als u Windows 10-apparaten niet hebt uitgerold of slechts gedeeltelijk hebt geïmplementeerd, raden we u aan een upgrade naar Windows 10 uit te voeren en [Windows hello voor bedrijven](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) op alle apparaten in te scha kelen.

Als u meer wilt weten over verificatie met een wacht woord, kunt u [een wereld zonder wacht woorden zien met Azure Active Directory](https://aka.ms/passwordlessdoc).

## <a name="application-authentication-and-assignment"></a>Toepassings verificatie en-toewijzing

### <a name="single-sign-on-for-apps"></a>Eenmalige aanmelding voor apps

Het bieden van een gestandaardiseerd mechanisme voor eenmalige aanmelding bij de hele onderneming is essentieel voor de beste gebruikers ervaring, vermindering van het risico, de mogelijkheid om te rapporteren en te voor komen. Als u gebruikmaakt van toepassingen die ondersteuning bieden voor eenmalige aanmelding met Azure AD, maar die momenteel zijn geconfigureerd voor het gebruik van lokale accounts, moet u deze toepassingen opnieuw configureren voor het gebruik van eenmalige aanmelding met Azure AD. Als u toepassingen gebruikt die ondersteuning bieden voor eenmalige aanmelding met Azure AD, maar een andere ID-provider gebruiken, moet u deze toepassingen ook opnieuw configureren voor het gebruik van eenmalige aanmelding met Azure AD. Voor toepassingen die geen ondersteuning bieden voor Federatie protocollen, maar wel op formulieren gebaseerde verificatie, wordt u aangeraden om de toepassing te configureren voor het gebruik van [wachtwoord kluizen](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting) met Azure AD-toepassingsproxy.

![Aanmelding op basis van AppProxy-wacht woord](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Als u niet beschikt over een mechanisme voor het detecteren van niet-beheerde toepassingen in uw organisatie, kunt u het beste een detectie proces implementeren met behulp van een Cloud Access Security Broker-oplossing (CASB), zoals [Microsoft Cloud app Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security).

Ten slotte, als u een Azure AD-App-galerie hebt en toepassingen gebruikt die ondersteuning bieden voor eenmalige aanmelding met Azure AD, wordt u aangeraden [de toepassing in de app-galerie te vermelden](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing).

#### <a name="single-sign-on-recommended-reading"></a>Eenmalige aanmelding aanbevolen lezen

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migratie van AD FS-toepassingen naar Azure AD

Het [migreren van apps van AD FS naar Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) biedt extra mogelijkheden voor beveiliging, consistente beheer baarheid en een betere samenwerkings ervaring. Als u toepassingen hebt geconfigureerd in AD FS die SSO ondersteunen met Azure AD, moet u deze toepassingen opnieuw configureren voor het gebruik van eenmalige aanmelding met Azure AD. Als u toepassingen hebt geconfigureerd in AD FS met niet-veelgebruikte configuraties die niet worden ondersteund door Azure AD, neemt u contact op met de app-eigen aren om te begrijpen of de speciale configuratie een absolute vereiste is voor de toepassing. Als dat niet het geval is, moet u de toepassing opnieuw configureren voor het gebruik van eenmalige aanmelding met Azure AD.

![Azure AD als de primaire id-provider](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health voor ADFS](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs) kan worden gebruikt voor het verzamelen van configuratie details over elke toepassing die mogelijk kan worden gemigreerd naar Azure AD.

### <a name="assign-users-to-applications"></a>Gebruikers toewijzen aan toepassingen

[Het toewijzen van gebruikers aan toepassingen is het](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) meest geschikt voor het gebruik van groepen, omdat ze een grotere flexibiliteit en mogelijkheid bieden om op schaal te beheren. De voor delen van het gebruik van groepen zijn onder andere [op kenmerken gebaseerd dynamisch groepslid maatschap](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) en [overdracht naar app-eigen aren](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners). Daarom raden we u aan de volgende acties uit te voeren om het beheer op schaal te verbeteren als u al groepen gebruikt en beheert:

- Delegeer groeps beheer en bestuur aan eigen aars van toepassingen.
- Self-service toegang tot de toepassing toestaan.
- Definieer dynamische groepen als gebruikers kenmerken de toegang tot toepassingen consistent kunnen bepalen.
- Implementeer Attestation naar groepen die worden gebruikt voor toegang tot toepassingen met [Azure AD-toegangs beoordelingen](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

Als u daarentegen toepassingen vindt die aan afzonderlijke gebruikers zijn toegewezen, moet u ervoor zorgen dat [governance](https://docs.microsoft.com/azure/active-directory/governance/index) rond deze toepassingen wordt geïmplementeerd.

#### <a name="assign-users-to-applications-recommended-reading"></a>Gebruikers toewijzen aan toepassingen aanbevolen te lezen

- [Gebruikers en groepen toewijzen aan een toepassing in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Machtigingen voor app-registratie in Azure Active Directory delegeren](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Dynamische lidmaatschaps regels voor groepen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Toegangsbeleid

### <a name="named-locations"></a>Benoemde locaties

Met [benoemde locaties](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) in azure AD kunt u vertrouwde IP-adresbereiken labelen in uw organisatie. Azure AD maakt gebruik van benoemde locaties om:

- Voorkom fout-positieven in risico gebeurtenissen. Wanneer u zich aanmeldt vanaf een vertrouwde netwerk locatie, wordt het aanmeldings risico van een gebruiker verlaagd.
- Configureer [op locatie gebaseerde voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations).

![Benoemde locatie](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Gebruik de onderstaande tabel op basis van prioriteit voor de aanbevolen oplossing die het beste voldoet aan de behoeften van uw organisatie:

| **Prioriteit** | **Scenario** | **Aanbeveling** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Als u PHS of PTA gebruikt en er geen benoemde locaties zijn gedefinieerd | Benoemde locaties definiëren voor het verbeteren van de detectie van risico gebeurtenissen |
| 2 | Als u federatief bent en geen ' insideCorporateNetwork-claim gebruikt en er geen benoemde locaties zijn gedefinieerd | Benoemde locaties definiëren voor het verbeteren van de detectie van risico gebeurtenissen |
| 3 | Als u geen benoemde locaties gebruikt in beleids regels voor voorwaardelijke toegang en er geen risico-of apparaatbesturing is in beleids regels voor voorwaardelijke toegang | Het beleid voor voorwaardelijke toegang configureren voor het insluiten van benoemde locaties |
| 4 | Als u federatief bent en de claim ' insideCorporateNetwork ' gebruikt en er geen benoemde locaties zijn gedefinieerd | Benoemde locaties definiëren voor het verbeteren van de detectie van risico gebeurtenissen |
| 5 | Als u vertrouwde IP-adressen met MFA gebruikt in plaats van benoemde locaties en ze als vertrouwd te markeren | Benoemde locaties definiëren en markeren als vertrouwd ter verbetering van de detectie van risico gebeurtenissen |

### <a name="risk-based-access-policies"></a>Op risico gebaseerd toegangs beleid

Azure AD kan het risico berekenen voor elke aanmelding en elke gebruiker. Het gebruik van een risico als criterium in het toegangs beleid kan een betere gebruikers ervaring bieden, bijvoorbeeld minder verificatie vragen en betere beveiliging, bijvoorbeeld alleen gebruikers vragen wanneer ze nodig zijn, en de reactie en het herstel automatiseren.

![Beleid voor aanmeldings Risico's](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Als u al beschikt over Azure AD Premium P2-licenties die ondersteuning bieden voor het gebruik van Risico's in het toegangs beleid, maar deze niet worden gebruikt, kunt u het beste risico toevoegen aan uw beveiligings postuur.

#### <a name="risk-based-access-policies-recommended-reading"></a>Op risico gebaseerd toegangs beleid aanbevolen lezen

- [Procedure: het beleid voor aanmeldings Risico's configureren](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [Procedure: het beleid voor gebruikers Risico's configureren](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>Toegangs beleid voor client toepassing

Microsoft Intune Application Management (MAM) biedt de mogelijkheid om besturings elementen voor gegevens beveiliging te pushen, zoals opslag versleuteling, pincode, opruiming van externe opslag, enzovoort, aan compatibele client toepassingen zoals Outlook Mobile. Daarnaast kunt u beleid voor voorwaardelijke toegang maken om de [toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) tot Cloud Services, zoals Exchange Online, te beperken vanuit goedgekeurde of compatibele apps.

Als uw werk nemers MAM toepassingen zoals mobiele Office-apps installeren om toegang te krijgen tot bedrijfs resources zoals Exchange Online of share point online, en u ook BYOD (uw eigen apparaat meenemen), raden wij u aan toepassings MAM-beleid te implementeren om te beheren de configuratie van de toepassing in persoonlijke apparaten zonder MDM-inschrijving en vervolgens uw beleid voor voorwaardelijke toegang bijwerken zodat alleen toegang vanaf clients met MAM kan worden toegestaan.

![Beheer van machtigingen voor voorwaardelijke toegang](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Als werk nemers MAM toepassingen installeren op basis van bedrijfs resources en toegang is beperkt op apparaten die door intune worden beheerd, kunt u overwegen om het beleid voor toepassings MAM te implementeren voor het beheren van de toepassings configuratie voor persoonlijke apparaten en het beleid voor voorwaardelijke toegang bijwerken zodat alleen toegang via MAM-compatibele clients wordt toegestaan.

### <a name="conditional-access-implementation"></a>Implementatie van voorwaardelijke toegang

Voorwaardelijke toegang is een essentieel hulp programma voor het verbeteren van de beveiligings postuur van uw organisatie. Daarom is het belang rijk dat u deze aanbevolen procedures volgt:

- Zorg ervoor dat er ten minste één beleid is toegepast op alle SaaS-toepassingen
- Vermijd het combi neren van het filter **alle apps** met de **blok kering** om het risico van vergren deling te voor komen
- Vermijd het gebruik van **alle gebruikers** als filter en het per ongeluk toevoegen van **gasten**
- **Alle ' verouderde ' beleids regels migreren naar de Azure Portal**
- Alle criteria voor gebruikers, apparaten en toepassingen catchiseren
- Gebruik beleids regels voor voorwaardelijke toegang om [MFA te implementeren](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)in plaats van een **MFA per gebruiker** te gebruiken
- Beschikken over een kleine set kern beleidsregels die van toepassing kunnen zijn op meerdere toepassingen
- Lege uitzonderings groepen definiëren en toevoegen aan het beleid om een uitzonderings strategie te hebben
- Planning voor [afbreek glazen](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency) accounts zonder MFA-besturings elementen
- Zorg voor een consistente ervaring in Office 365-client toepassingen, bijvoorbeeld teams, OneDrive voor bedrijven, Outlook, enzovoort. door dezelfde set besturings elementen te implementeren voor services zoals Exchange Online en share point online
- Toewijzing aan beleid moet worden geïmplementeerd via groepen, niet voor personen
- Voer regel matig beoordelingen uit van de uitzonderings groepen die worden gebruikt in beleid om het aantal tijd gebruikers te beperken dat de beveiligings postuur. Als u Azure AD P2 hebt, kunt u toegangs beoordelingen gebruiken om het proces te automatiseren

#### <a name="conditional-access-recommended-reading"></a>Voorwaardelijke toegang aanbevolen lezen

- [Aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Configuraties voor identiteit en Apparaattoegang](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Verwijzing naar de Azure Active Directory-instellingen voor voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Algemeen beleid voor voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Toegang surface area

### <a name="legacy-authentication"></a>Verouderde verificatie

Sterke referenties zoals MFA kunnen geen apps beveiligen met behulp van verouderde verificatie protocollen, waardoor het de voorkeurs aanvals vector door kwaad aardige actors wordt. Het vergren delen van verouderde verificatie is van cruciaal belang voor het verbeteren van de toegangs beveiliging postuur.

Verouderde verificatie is een term die verwijst naar verificatie protocollen die worden gebruikt door apps, zoals:

- Oudere Office-clients die geen gebruik kunnen maakt van moderne verificatie (bijvoorbeeld Office 2010 client)
- Clients die gebruikmaken van e-mail protocollen zoals IMAP/SMTP/POP

Kwaadwillende personen geven de voor keur aan deze protocollen, bijna [100% van aanvallen met wachtwoord spray](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) gebruiken verouderde verificatie protocollen. Hackers gebruiken verouderde verificatie protocollen, omdat ze geen ondersteuning bieden voor interactieve aanmelding, wat nodig is voor extra beveiligings problemen zoals multi-factor Authentication en verificatie van apparaten.

Als verouderde verificatie veel in uw omgeving wordt gebruikt, moet u plannen om verouderde clients te migreren naar clients die [moderne verificatie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) zo snel mogelijk ondersteunen. Als u in hetzelfde token een aantal gebruikers hebt die al gebruikmaken van moderne verificatie, maar anderen die nog steeds gebruikmaken van verouderde verificatie, moet u de volgende stappen uitvoeren om verouderde verificatie-clients te vergren delen:

1. Gebruik [rapporten voor aanmeldings activiteiten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) om gebruikers te identificeren die nog steeds gebruikmaken van verouderde verificatie en herstel plannen:

   a. Voer een upgrade uit naar gebruikers die geschikt zijn voor authenticatie.
   
   b. Plan een cutover-tijds bestek om te vergren delen volgens onderstaande stappen.
   
   c. Identificeer welke oudere toepassingen een vaste afhankelijkheid hebben van verouderde verificatie. Zie stap 3 hieronder.

2. Schakel verouderde protocollen uit in de bron (bijvoorbeeld Exchange-postvak) voor gebruikers die geen verouderde verificatie gebruiken om meer bloot stelling te voor komen.
3. Gebruik voor de resterende accounts (in het ideale geval niet-menselijke identiteiten zoals service accounts) [voorwaardelijke toegang om verouderde protocollen na verificatie te beperken](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) .

#### <a name="legacy-authentication-recommended-reading"></a>Verouderde verificatie wordt aanbevolen te lezen

- [POP3-of IMAP4-toegang tot post vakken in Exchange server in-of uitschakelen](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Verleende toestemming

Bij een illegale toestemming verleent de aanvaller een Azure AD-geregistreerde toepassing die toegang tot gegevens, zoals contact gegevens, e-mail of documenten, aanvraagt. Gebruikers kunnen via phishing-aanvallen toestemming geven voor kwaad aardige toepassingen wanneer ze op schadelijke websites terechtkomen.

Hieronder vindt u een lijst met apps met machtigingen die u mogelijk wilt scrutinize voor micro soft-Cloud Services:

- Apps met een app of gedelegeerde \*. ReadWrite-machtigingen
- Apps met gedelegeerde machtigingen kunnen e-mail namens de gebruiker lezen, verzenden of beheren
- Apps waaraan het gebruik van de volgende machtigingen is verleend:

| Bron | Machtiging |
| :- | :- |
| Office 365 Exchange Online | Uitgebreide. AccessAsUser. all |
| | EWS. AccessAsUser. all |
| | Mail. Read |
| Microsoft Graph | Mail. Read |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Apps hebben volledige gebruikers imitatie verleend van de aangemelde gebruiker. Bijvoorbeeld:

|Bron | Machtiging |
| :- | :- |
| Azure AD-grafiek | Map. AccessAsUser. alle |
| Microsoft Graph | Map. AccessAsUser. alle |
| Azure REST API | user_impersonation |

Om dit scenario te voor komen, dient u te verwijzen naar [illegale toestemming subsidies in Office 365 te detecteren](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) en op te lossen om toepassingen te identificeren en te herstellen met illegale subsidies of toepassingen die meer subsidies hebben dan nodig zijn. Vervolgens [verwijdert u self-service samen](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) en stelt u beheer [procedures](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)in. Ten slotte kunt u regel matig beoordelingen van app-machtigingen plannen en deze verwijderen wanneer ze niet nodig zijn.

#### <a name="consent-grants-recommended-reading"></a>Toestemming toekenningen aanbevolen te lezen

- [Machtigingen voor Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Gebruikers-en groeps instellingen

Hieronder ziet u de gebruikers-en groeps instellingen die kunnen worden vergrendeld als er geen expliciete bedrijfs behoeften zijn:

#### <a name="user-settings"></a>Gebruikersinstellingen

- **Externe gebruikers** : externe samen werking kan op biologische wijze plaatsvinden in de onderneming met Services als Teams, Power bi, share point Online en Azure Information Protection. Als u expliciete beperkingen hebt voor het beheren van de door de gebruiker gestarte externe samen werking, wordt u aangeraden externe gebruikers in te scha kelen met behulp van het [beheer van rechten van Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) of een gecontroleerde bewerking, zoals via uw Help Desk. Als u geen organische externe samen werking voor services wilt toestaan, kunt u [leden blok keren om externe gebruikers volledig uit te nodigen](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations). U kunt ook specifieke domeinen in uitnodigingen voor externe gebruikers [toestaan of blok keren](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) .
- **App-registraties** : als app-registraties zijn ingeschakeld, kunnen eind gebruikers toepassingen zelf opheffen en toegang tot hun gegevens verlenen. Een typisch voor beeld van app-registratie is het inschakelen van Outlook-invoeg toepassingen of spraak assistenten, zoals Alexa en SIRI, om hun e-mail en agenda te lezen of e-mail berichten te verzenden. Als de klant besluit om de app-registratie uit te scha kelen, moeten de InfoSec-en IAM-teams betrokken zijn bij het beheer van uitzonde ringen (app-registraties die nodig zijn op basis van de bedrijfs vereisten), omdat de toepassingen moeten worden geregistreerd bij een beheerders account. en het is waarschijnlijk nodig om een proces te ontwerpen om het proces te operationeel maken.
- **Beheer Portal** : organisaties kunnen de Azure AD-Blade in de Azure Portal vergren delen, zodat niet-beheerders geen toegang hebben tot Azure AD-beheer in de Azure Portal en worden verward. Ga naar de gebruikers instellingen in de Azure AD-beheer Portal om de toegang te beperken:

![Beperkte toegang tot de beheer Portal](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Niet-beheerders hebben nog steeds toegang tot de Azure AD-beheer interfaces via opdracht regel en andere programmatische interfaces.

#### <a name="group-settings"></a>Groepsinstellingen

**Self-service groeps beheer/gebruikers kunnen beveiligings groepen/O365-groepen maken.** Als er geen self-service-initiatief is voor groepen in de Cloud, kunnen klanten ervoor kiezen deze uit te scha kelen totdat ze klaar zijn om deze mogelijkheid te gebruiken.

#### <a name="groups-recommended-reading"></a>Aanbevolen groepen lezen

- [Wat is Azure Active Directory B2B-samen werking?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Toepassingen integreren met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Apps, machtigingen en toestemming in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Groepen gebruiken voor het beheren van de toegang tot resources in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Toegangs beheer voor selfservice toepassingen instellen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Verkeer van onverwachte locaties

Aanvallers zijn afkomstig van verschillende onderdelen van de wereld. Beheer dit risico door gebruik te maken van beleids regels voor voorwaardelijke toegang met de locatie van de voor waarde. De [locatie voorwaarde](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) van een beleid voor voorwaardelijke toegang stelt u in staat om toegang te blok keren voor locaties van waaruit er geen zakelijke reden is om zich aan te melden.

![Een nieuwe benoemde locatie maken](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Gebruik, indien beschikbaar, een SIEM-oplossing (Security Information and Event Management) om de toegang tot verschillende regio's te analyseren en te bepalen. Als u geen SIEM-product gebruikt of geen verificatie gegevens opneemt vanuit Azure AD, raden we u aan [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) te gebruiken om de toegang tot verschillende regio's te identificeren.

## <a name="access-usage"></a>Toegang tot gebruik

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD-logboeken gearchiveerd en geïntegreerd met incident response plannen

U hebt toegang tot de aanmeldings activiteiten, audits en risico gebeurtenissen voor Azure AD van cruciaal belang voor het oplossen van problemen, gebruiks analyses en forensische onderzoek. Azure AD biedt toegang tot deze bronnen via REST Api's die een beperkte Bewaar periode hebben. Een SIEM-systeem (Security Information and Event Management) of gelijkwaardige archiverings technologie is essentieel voor lange termijn opslag van audits en ondersteuning. Als u lange termijn opslag van Azure AD-logboeken wilt inschakelen, moet u deze toevoegen aan uw bestaande SIEM-oplossing of gebruikmaken van [Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor). Archiveer logboeken die kunnen worden gebruikt als onderdeel van het antwoord plannen en onderzoeken van uw incident.

#### <a name="logs-recommended-reading"></a>Aanbevolen Lees logboeken

- [Naslag informatie over Azure Active Directory controle-API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Rapport API-referentie voor Azure Active Directory-aanmeld activiteiten](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Gegevens ophalen met de rapportage-API van Azure AD met certificaten](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph voor Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Naslag informatie voor Office 365 Management Activity API](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Het Azure Active Directory Power BI-inhouds pakket gebruiken](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Samenvatting

Er zijn 12 aspecten van een veilige identiteits infrastructuur. Deze lijst helpt u bij het verder beveiligen en beheren van referenties, het definiëren van de verificatie, het toewijzen van de toewijzing, het meten van het gebruik en het definiëren van toegangs beleid op basis van de beveiligings postuur van de onderneming.

- Eigen aren toewijzen aan belang rijke taken.
- Implementeer oplossingen om zwakke of gelekte wacht woorden te detecteren, wachtwoord beheer en-beveiliging te verbeteren en de gebruikers toegang tot bronnen verder te beveiligen.
- Beheer de identiteit van apparaten om uw resources op elk gewenst moment en vanaf elke locatie te beveiligen.
- Implementeer verificatie met een wacht woord.
- Geef een gestandaardiseerd mechanisme voor eenmalige aanmelding voor de hele organisatie op.
- Migreer apps van AD FS naar Azure AD om betere beveiliging en consistente beheer baarheid mogelijk te maken.
- Gebruikers toewijzen aan toepassingen door gebruik te maken van groepen om meer flexibiliteit en mogelijkheid te bieden om op schaal te beheren.
- Configureer op risico gebaseerd toegangs beleid.
- Verouderde verificatie protocollen vergren delen.
- Detecteer en herstel illegale toestemming subsidies.
- Gebruikers-en groeps instellingen vergren delen.
- Lange termijn opslag van Azure AD-logboeken inschakelen voor probleem oplossing, gebruiks analyse en forensische onderzoek.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [operationele controles en acties voor identiteits](active-directory-ops-guide-govern.md)beheer.

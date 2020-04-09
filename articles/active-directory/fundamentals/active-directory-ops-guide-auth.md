---
title: Naslaginformatieover zoeknaar naar beheerhandleiding voor Azure Active Directory Authentication-beheer
description: In deze handleiding voor bewerkingen worden de controles en acties beschreven die u moet uitvoeren om verificatiebeheer te beveiligen
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
ms.openlocfilehash: f25abb70a95f559cf0cc14efa6cf9f0e81ec9ec0
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876289"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Naslaginformatieover zoeknaar naar beheerhandleiding voor Azure Active Directory Authentication-beheer

In dit gedeelte van de [naslaggids voor Azure AD-bewerkingen](active-directory-ops-guide-intro.md) worden de controles en acties beschreven die u moet uitvoeren om referenties te beveiligen en te beheren, verificatie-ervaring te definiëren, toewijzing te delegeren, het gebruik te meten en toegangsbeleid te definiëren op basis van de beveiligingshouding van het bedrijf.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de publicatiedatum, maar kunnen in de loop van de tijd veranderen. Organisaties moeten hun identiteitspraktijken voortdurend evalueren terwijl Microsoft-producten en -services in de loop van de tijd evolueren.

## <a name="key-operational-processes"></a>Belangrijkste operationele processen

### <a name="assign-owners-to-key-tasks"></a>Eigenaren toewijzen aan belangrijke taken

Voor het beheren van Azure Active Directory is de continue uitvoering van belangrijke operationele taken en processen vereist, die mogelijk geen deel uitmaken van een implementatieproject. Het is nog steeds belangrijk dat u deze taken instelt om uw omgeving te optimaliseren. De belangrijkste taken en de aanbevolen eigenaren zijn:

| Taak | Eigenaar |
| :- | :- |
| Levenscyclus van eenmalige aanmeldingsconfiguratie (SSO) beheren in Azure AD | IAM Operations Team |
| Beleid voor voorwaardelijke toegang voor Azure AD-toepassingen ontwerpen | InfoSec-architectuurteam |
| Aanmeldingsactiviteit archiveren in een SIEM-systeem | InfoSec Operations Team |
| Risicogebeurtenissen archiveren in een SIEM-systeem | InfoSec Operations Team |
| Triage en beveiligingsrapporten onderzoeken | InfoSec Operations Team |
| Triage en risicogebeurtenissen onderzoeken | InfoSec Operations Team |
| Triage en onderzoek gebruikers die zijn gemarkeerd voor risico- en kwetsbaarheidsrapporten van Azure AD Identity Protection | InfoSec Operations Team |

> [!NOTE]
> Azure AD-identiteitsbeveiliging vereist een Azure AD Premium P2-licentie. Zie Algemeen beschikbare functies van [de Azure AD Free- en Azure AD Premium-edities vergelijken](https://azure.microsoft.com/pricing/details/active-directory/)voor de juiste licentie voor uw vereisten.

Als u uw lijst bekijkt, moet u mogelijk een eigenaar toewijzen voor taken die een eigenaar missen of het eigendom aanpassen voor taken met eigenaren die niet zijn afgestemd op de bovenstaande aanbevelingen.

#### <a name="owner-recommended-reading"></a>Eigenaar aanbevolen lezen

- [Beheerdersrollen toewijzen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>Referentiebeheer

### <a name="password-policies"></a>Wachtwoordbeleid

Het veilig beheren van wachtwoorden is een van de meest kritieke onderdelen van identiteits- en toegangsbeheer en vaak het grootste doelwit van aanvallen. Azure AD ondersteunt verschillende functies die kunnen voorkomen dat een aanval succesvol is.

Gebruik de onderstaande tabel om de aanbevolen oplossing te vinden om het probleem te verzachten dat moet worden aangepakt:

| Probleem | Aanbeveling |
| :- | :- |
| Geen mechanisme om te beschermen tegen zwakke wachtwoorden | Azure AD [selfservice password reset (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) en [wachtwoordbeveiliging inschakelen](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| Geen mechanisme om gelekte wachtwoorden te detecteren | Password [hash sync](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) (PHS) inschakelen om inzichten te verkrijgen |
| AD FS gebruiken en kan niet naar beheerde verificatie gaan | [AD FS Extranet Smart Lockout](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) en/of [Azure AD Smart Lockout inschakelen](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| Wachtwoordbeleid maakt gebruik van op complexiteit gebaseerde regels, zoals lengte, meerdere tekensets of vervaldatum | Heroverweeg ten gunste van [Microsoft Recommended Practices](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) en schakel uw benadering over naar wachtwoordbeheer en implementeer [Azure AD-wachtwoordbeveiliging.](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) |
| Gebruikers zijn niet geregistreerd om multi-factor authenticatie (MFA) te gebruiken | [Registreer alle beveiligingsgegevens van de gebruiker,](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy) zodat deze kunnen worden gebruikt als een mechanisme om de identiteit van de gebruiker te verifiëren, samen met hun wachtwoord |
| Er is geen intrekking van wachtwoorden op basis van gebruikersrisico | Gebruikersrisicobeleid voor Azure AD [Identity Protection implementeren](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy) om wachtwoordwijzigingen op gelekte referenties te forceren met SSPR |
| Er is geen slim lock-outmechanisme om kwaadaardige verificatie te beschermen tegen slechte actoren die afkomstig zijn van geïdentificeerde IP-adressen | Cloudbeheerverificatie implementeren met wachtwoordhashsynchronisatie of [pass-through-verificatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Wachtwoordbeleid aanbevolen lezen

- [Aanbevolen procedures azure AD en AD FS: Verdedigen tegen aanvallen met wachtwoordspray - Enterprise Mobility + Beveiliging](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Selfservice wachtwoordreset en wachtwoordbeveiliging inschakelen

Gebruikers die hun wachtwoorden moeten wijzigen of resetten, is een van de grootste bronnen van volume en kosten van helpdeskgesprekken. Naast de kosten is het wijzigen van het wachtwoord als hulpmiddel om een gebruikersrisico te beperken een fundamentele stap in het verbeteren van de beveiligingshouding van uw organisatie.

Ten minste wordt aanbevolen azure AD [self-service password reset](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) (SSPR) en on-premises [wachtwoordbeveiliging](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) te implementeren om te bereiken:

- Afbuigen helpdesk oproepen.
- Vervang het gebruik van tijdelijke wachtwoorden.
- Vervang een bestaande selfservice-wachtwoordbeheeroplossing die is gebaseerd op een on-premises oplossing.
- [Elimineer zwakke wachtwoorden](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) in uw organisatie.

> [!NOTE]
> Voor organisaties met een Azure AD Premium P2-abonnement wordt aanbevolen SSPR te implementeren en te gebruiken als onderdeel van een [gebruikersrisicobeleid voor identiteitsbescherming.](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="strong-credential-management"></a>Sterk referentiebeheer

Wachtwoorden zelf zijn niet veilig genoeg om te voorkomen dat slechte acteurs toegang krijgen tot uw omgeving. Ten minste moet elke gebruiker met een geprivilegieerd account zijn ingeschakeld voor multi-factor authenticatie (MFA). Idealiter moet u [gecombineerde registratie](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) inschakelen en vereisen dat alle gebruikers zich registreren voor MFA en SSPR met behulp van de gecombineerde [registratie-ervaring.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview) Uiteindelijk raden we u aan een strategie te volgen om veerkracht te [bieden](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) om het risico op uitsluiting als gevolg van onvoorziene omstandigheden te verminderen.

![Gecombineerde gebruikerservaringsstroom](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>On-premises tolerantie voor uitvalverificatie

Naast de voordelen van eenvoud en het inschakelen van gelekte referentiedetectie, bieden Azure AD Password Hash Sync (PHS) en Azure MFA gebruikers toegang tot SaaS-toepassingen en Office 365, ondanks on-premises uitval als gevolg van cyberaanvallen zoals [NotPetya.](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/) Het is ook mogelijk om PHS in te schakelen in samenwerking met federatie. PhS inschakelen maakt een terugval van verificatie mogelijk wanneer federatieservices niet beschikbaar zijn.

Als uw on-premises organisatie een onderbrekingstolerantiestrategie voor storingen mist of een strategie heeft die niet is geïntegreerd met Azure AD, moet u Azure AD PHS implementeren en een noodherstelplan definiëren dat PHS bevat. Als u Azure AD PHS inschakelt, kunnen gebruikers zich verifiëren tegen Azure AD als uw on-premises Active Directory niet beschikbaar is.

![wachtwoordhashsynchronisatiestroom](./media/active-directory-ops-guide/active-directory-ops-img5.png)

Zie [De juiste verificatiemethode kiezen voor uw hybride azure directory-identiteitsoplossing voor uw verificatieopties.](../hybrid/choose-ad-authn.md)

### <a name="programmatic-usage-of-credentials"></a>Programmatisch gebruik van referenties

Azure AD-scripts die PowerShell of toepassingen gebruiken met de Microsoft Graph API vereisen veilige verificatie. Slecht referentiebeheer bij het uitvoeren van deze scripts en hulpprogramma's verhoogt het risico op diefstal van referenties. Als u scripts of toepassingen gebruikt die afhankelijk zijn van hardgecodeerde wachtwoorden of wachtwoordprompts, moet u eerst wachtwoorden in config-bestanden of broncode controleren, deze afhankelijkheden vervangen en azure managed identities, geïntegreerde Windows-verificatie of [certificaten](../reports-monitoring/tutorial-access-api-with-certificates.md) gebruiken waar mogelijk. Voor toepassingen waar de vorige oplossingen niet mogelijk zijn, u Azure [Key Vault gebruiken.](https://azure.microsoft.com/services/key-vault/)

Als u vaststelt dat er serviceprincipals zijn met wachtwoordreferenties en u niet zeker weet hoe deze wachtwoordreferenties worden beveiligd door scripts of toepassingen, neemt u contact op met de eigenaar van de toepassing om gebruikspatronen beter te begrijpen.

Microsoft raadt u ook aan contact op te nemen met toepassingseigenaren om gebruikspatronen te begrijpen als er serviceprincipals zijn met wachtwoordreferenties.

## <a name="authentication-experience"></a>Verificatie-ervaring

### <a name="on-premises-authentication"></a>On-premises verificatie

Federatieve verificatie met geïntegreerde Windows-verificatie (IWA) of Naadloze Single Sign-On (SSO) beheerde verificatie met wachtwoordhashsynchronisatie of pass-through-verificatie is de beste gebruikerservaring wanneer u zich binnen het bedrijfsnetwerk bevindt met line-of-sight naar on-premises domeincontrollers. Het minimaliseert referentie prompt vermoeidheid en vermindert het risico van gebruikers ten prooi vallen aan phishing-aanvallen. Als u al cloudbeheerde verificatie met PHS of PTA gebruikt, maar gebruikers nog steeds hun wachtwoord moeten invoeren wanneer ze on-premises authenticeren, moet u [naadloze SSO](../hybrid/how-to-connect-sso.md)onmiddellijk implementeren. Aan de andere kant, als u momenteel federatief met plannen om uiteindelijk te migreren naar cloud-managed authenticatie, dan moet u implementeren Seamless SSO als onderdeel van het migratieproject.

### <a name="device-trust-access-policies"></a>Toegangsbeleid voor vertrouwensrelaties voor apparaten

Net als een gebruiker in uw organisatie is een apparaat een kernidentiteit die u wilt beschermen. U de identiteit van een apparaat gebruiken om uw resources op elk gewenst moment en vanaf elke locatie te beschermen.Het verifiëren van het apparaat en het verantwoorden van het vertrouwenstype verbetert uw beveiligingshouding en bruikbaarheid door:

- Het vermijden van frictie, bijvoorbeeld met MFA, wanneer het apparaat wordt vertrouwd
- Toegang blokkeren vanaf niet-vertrouwde apparaten
- Voor Windows 10-apparaten u on-premises resources naadloos aanmelden voor [on-premises resources.](../devices/azuread-join-sso.md)

U dit doel uitvoeren door apparaatidentiteiten te identiianden en te beheren in Azure AD met behulp van een van de volgende methoden:

- Organisaties kunnen [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune) gebruiken om het apparaat te beheren en nalevingsbeleid af te dwingen, de status van het apparaat te bevestigen en beleid voor voorwaardelijke toegang in te stellen op basis van de vraag of het apparaat compatibel is. Microsoft Intune kan iOS-apparaten, Mac-desktops (Via JAMF-integratie), Windows-desktops (native gebruik van Mobile Device Management voor Windows 10 en co-management met Microsoft Endpoint Configuration Manager) en mobiele Android-apparaten beheren.
- [Hybride Azure AD join](../devices/hybrid-azuread-join-managed-domains.md) biedt beheer met Groepsbeleid of Microsoft Endpoint Configuration Manager in een omgeving met Active Directory-computers die zijn verbonden met active directory-domeinen. Organisaties kunnen een beheerde omgeving implementeren via PHS of PTA met Seamless SSO. Als u uw apparaten naar Azure AD brengt, wordt de productiviteit van gebruikers via SSO in uw cloud en on-premises resources gemaximaliseerd, zodat u tegelijkertijd toegang krijgen tot uw cloud- en on-premises resources met [voorwaardelijke toegang.](../conditional-access/overview.md) 

Als u windows-apparaten met domeinverbonden hebt die niet zijn geregistreerd in de cloud of windows-apparaten met domeinverbonden die zijn geregistreerd in de cloud, maar zonder beleid voor voorwaardelijke toegang, moet u de niet-geregistreerde apparaten registreren en in beide gevallen [Hybride Azure AD-join gebruiken als besturingselement](../conditional-access/require-managed-devices.md) in uw beleid voor voorwaardelijke toegang.

![Een schermafbeelding van subsidie in het beleid voor voorwaardelijke toegang waarvoor hybride apparaat vereist is](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Als u apparaten beheert met MDM of Microsoft Intune, maar geen apparaatbesturingselementen gebruikt in uw beleid voor voorwaardelijke toegang, raden we u aan [het apparaat vereisen te](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) gebruiken dat als compatibel is gemarkeerd als besturingselement in dat beleid.

![Een schermafbeelding van subsidie in het beleid voor voorwaardelijke toegang dat apparaatnaleving vereist](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Toegangsbeleid voor vertrouwde vertrouwensrelaties aanbevolen voor lezen

- [How To: Uw hybride Azure Active Directory join-implementatie plannen](../devices/hybrid-azuread-join-plan.md)
- [Configuraties voor identiteit en apparaattoegang](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello voor Bedrijven

In Windows 10 vervangt [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) wachtwoorden door sterke tweestapsverificatie op pc's. Windows Hello for Business maakt een meer gestroomlijnde MFA-ervaring mogelijk voor gebruikers en vermindert uw afhankelijkheid van wachtwoorden. Als u nog niet bent begonnen met de uitrol van Windows 10-apparaten of deze slechts gedeeltelijk hebt geïmplementeerd, raden we u aan te upgraden naar Windows 10 en Windows Hello voor Bedrijven op alle apparaten [in te schakelen.](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

Zie [Een wereld zonder wachtwoorden met Azure Active Directory](../authentication/concept-authentication-passwordless.md)als u meer wilt weten over verificatie zonder wachtwoord.

## <a name="application-authentication-and-assignment"></a>Toepassingsverificatie en -toewijzing

### <a name="single-sign-on-for-apps"></a>Eén aanmelding voor apps

Het bieden van een gestandaardiseerd single sign-on mechanisme voor de hele onderneming is cruciaal voor de beste gebruikerservaring, vermindering van risico's, vermogen om te rapporteren en governance. Als u toepassingen gebruikt die SSO ondersteunen met Azure AD, maar momenteel zijn geconfigureerd om lokale accounts te gebruiken, moet u deze toepassingen opnieuw configureren om SSO te gebruiken met Azure AD. Als u toepassingen gebruikt die SSO ondersteunen met Azure AD, maar een andere identiteitsprovider gebruiken, moet u deze toepassingen ook opnieuw configureren om SSO met Azure AD te gebruiken. Voor toepassingen die federatieprotocollen niet ondersteunen, maar wel op formulieren gebaseerde verificatie ondersteunen, raden we u aan de toepassing te configureren om [wachtwoordkluising](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) te gebruiken met Azure AD Application Proxy.

![Aanmelding op basis van appProxy-wachtwoord](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Als u geen mechanisme hebt om onbeheerde toepassingen in uw organisatie te ontdekken, raden we u aan een detectieproces te implementeren met behulp van een CASB (Cloud Access Security Broker-oplossing), zoals [Microsoft Cloud App Security.](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)

Als u ten slotte een Azure AD-app-galerie hebt en toepassingen gebruikt die SSO ondersteunen met Azure AD, raden we u aan [de toepassing in de app-galerie aan te geven.](../azuread-dev/howto-app-gallery-listing.md)

#### <a name="single-sign-on-recommended-reading"></a>Aanbevolen lezing voor eenmalig aanmelden

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Migratie van AD FS-toepassingen naar Azure AD

[Als u apps migreert van AD FS naar Azure AD,](../manage-apps/migrate-adfs-apps-to-azure.md) u extra mogelijkheden bieden op het gebied van beveiliging, consistentere beheerbaarheid en een betere samenwerkingservaring. Als u toepassingen hebt geconfigureerd in AD FS die SSO ondersteunen met Azure AD, moet u deze toepassingen opnieuw configureren om SSO te gebruiken met Azure AD. Als u toepassingen hebt geconfigureerd in AD FS met ongebruikelijke configuraties die niet worden ondersteund door Azure AD, moet u contact opnemen met de app-eigenaren om te zien of de speciale configuratie een absolute vereiste van de toepassing is. Als dit niet nodig is, moet u de toepassing opnieuw configureren om SSO met Azure AD te gebruiken.

![Azure AD als primaire identiteitsprovider](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health for ADFS](../hybrid/how-to-connect-health-adfs.md) kan worden gebruikt om configuratiegegevens te verzamelen over elke toepassing die mogelijk kan worden gemigreerd naar Azure AD.

### <a name="assign-users-to-applications"></a>Gebruikers toewijzen aan toepassingen

[Het toewijzen van gebruikers aan toepassingen](../manage-apps/assign-user-or-group-access-portal.md) kan het beste in kaart worden gebracht met behulp van groepen, omdat ze meer flexibiliteit en het vermogen om op schaal te beheren mogelijk maken. De voordelen van het gebruik van groepen zijn [op basis van een attribuut gebaseerd dynamisch groepslidmaatschap](../users-groups-roles/groups-dynamic-membership.md) en delegatie naar [app-eigenaren.](../fundamentals/active-directory-accessmanagement-managing-group-owners.md) Als u daarom al groepen gebruikt en beheert, raden we u aan de volgende acties te ondernemen om het beheer op schaal te verbeteren:

- Groepsbeheer en -beheer delegeren aan toepassingseigenaren.
- Geef selfservicetoegang tot de toepassing.
- Definieer dynamische groepen als gebruikerskenmerken consistent de toegang tot toepassingen kunnen bepalen.
- Verating implementeren voor groepen die worden gebruikt voor toepassingstoegang met [Azure AD-toegangsbeoordelingen](../governance/access-reviews-overview.md).

Aan de andere kant, als u toepassingen vindt die toewijzing hebben aan individuele gebruikers, moet u [beheer](https://docs.microsoft.com/azure/active-directory/governance/index) rond die toepassingen implementeren.

#### <a name="assign-users-to-applications-recommended-reading"></a>Gebruikers toewijzen aan aanbevolen toepassingen lezen

- [Gebruikers en groepen toewijzen aan een toepassing in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [Machtigingen voor app-registratie delegeren in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Dynamische lidmaatschapsregels voor groepen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>Toegangsbeleidsregels

### <a name="named-locations"></a>Benoemde locaties

Met [benoemde locaties](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) in Azure AD u vertrouwde IP-adresbereiken in uw organisatie labelen. Azure AD maakt gebruik van benoemde locaties om:

- Voorkom valse positieven bij risicogebeurtenissen. Als u zich aanmeldt vanaf een vertrouwde netwerklocatie, wordt het aanmeldingsrisico van een gebruiker verlaagd.
- [Locatiegebaseerde voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)configureren .

![Benoemde locatie](./media/active-directory-ops-guide/active-directory-ops-img10.png)

Gebruik de onderstaande tabel op basis van prioriteit om de aanbevolen oplossing te vinden die het beste aan de behoeften van uw organisatie voldoet:

| **Prioriteit** | **Scenario** | **Aanbeveling** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Als u PHS of PTA gebruikt en de benoemde locaties niet zijn gedefinieerd | Benoemde locaties definiëren om de detectie van risicogebeurtenissen te verbeteren |
| 2 | Als u gefedereerd bent en geen claim 'insideCorporateNetwork' gebruikt en benoemde locaties niet zijn gedefinieerd | Benoemde locaties definiëren om de detectie van risicogebeurtenissen te verbeteren |
| 3 | Als u benoemde locaties niet gebruikt in het beleid voor voorwaardelijke toegang en er geen risico- of apparaatbesturingselementen zijn in het beleid voor voorwaardelijke toegang | Het beleid voor voorwaardelijke toegang configureren om benoemde locaties op te nemen |
| 4 | Als u gefedereerd bent en wel 'insideCorporateNetwork'-claim gebruikt en de benoemde locaties niet zijn gedefinieerd | Benoemde locaties definiëren om de detectie van risicogebeurtenissen te verbeteren |
| 5 | Als u vertrouwde IP-adressen met MFA gebruikt in plaats van benoemde locaties en ze markeert als vertrouwd | Definieer benoemde locaties en markeer ze als vertrouwd om de detectie van risicogebeurtenissen te verbeteren |

### <a name="risk-based-access-policies"></a>Op risico's gebaseerd toegangsbeleid

Azure AD kan het risico berekenen voor elke aanmelding en elke gebruiker. Het gebruik van risico's als criterium in toegangsbeleid kan een betere gebruikerservaring bieden, bijvoorbeeld minder verificatieprompts en betere beveiliging, bijvoorbeeld gebruikers alleen vragen wanneer ze nodig zijn, en de respons en herstel automatiseren.

![Aanmeldingsrisicobeleid](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Als u al eigenaar bent van Azure AD Premium P2-licenties die ondersteuning bieden voor risico's in toegangsbeleid, maar deze niet worden gebruikt, raden we u ten zeerste aan risico's toe te voegen aan uw beveiligingshouding.

#### <a name="risk-based-access-policies-recommended-reading"></a>Op risico's gebaseerd toegangsbeleid aanbevolen om te lezen

- [How To: Het aanmeldingsrisicobeleid configureren](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [How To: Het gebruikersrisicobeleid configureren](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Toegangsbeleid voor clienttoepassingen

Microsoft Intune Application Management (MAM) biedt de mogelijkheid om besturingselementen voor gegevensbeveiliging, zoals opslagversleuteling, pincode, opschonen van externe opslag, enz. Daarnaast kan beleid voor voorwaardelijke toegang worden gemaakt om [de toegang tot](../conditional-access/app-based-conditional-access.md) cloudservices zoals Exchange Online te beperken van goedgekeurde of compatibele apps.

Als uw medewerkers MAM-toepassingen installeren, zoals mobiele Office-apps, om toegang te krijgen tot bedrijfsbronnen zoals Exchange Online of SharePoint Online, en u ook BYOD ondersteunt (breng uw eigen apparaat mee), raden we u aan om mam-beleid voor toepassingen te implementeren om de toepassingsconfiguratie te beheren in apparaten die persoonlijk eigendom zijn zonder MDM-inschrijving en vervolgens uw voorwaardelijke toegangsbeleid bij te werken om alleen toegang van MAM-compatibele clients mogelijk te maken.

![Subsidiebeheer voorwaardelijke toegang](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Als werknemers MAM-toepassingen installeren tegen bedrijfsresources en de toegang is beperkt op Intune Managed-apparaten, moet u overwegen om mam-beleid voor toepassingen te implementeren om de toepassingsconfiguratie voor persoonlijke apparaten te beheren en voorwaardelijke toegangsbeleidsregels bij te werken om alleen toegang toe te staan van MAM-compatibele clients.

### <a name="conditional-access-implementation"></a>Implementatie van voorwaardelijke toegang

Conditional Access is een essentieel hulpmiddel voor het verbeteren van de beveiligingshouding van uw organisatie. Daarom is het belangrijk dat u deze best practices volgt:

- Zorg ervoor dat alle SaaS-toepassingen ten minste één beleid hebben toegepast
- Vermijd het combineren van het filter **Alle apps** met het **blokbesturingselement** om lock-outrisico's te voorkomen
- Vermijd het gebruik van de **Alle gebruikers** als een filter en per ongeluk het toevoegen van **gasten**
- **Alle 'legacy'-beleidsregels migreren naar de Azure-portal**
- Van alle criteria voor gebruikers, apparaten en toepassingen voldoen
- Beleid voor voorwaardelijke toegang gebruiken om MFA te [implementeren,](../conditional-access/plan-conditional-access.md)in plaats van een **MFA per gebruiker te** gebruiken
- Een kleine set kernbeleidsregels hebben die van toepassing kunnen zijn op meerdere toepassingen
- Lege uitzonderingsgroepen definiëren en toevoegen aan het beleid voor een uitzonderingsstrategie
- Plannen voor [break glass](../users-groups-roles/directory-admin-roles-secure.md#break-glass-what-to-do-in-an-emergency) accounts zonder MFA controles
- Zorg voor een consistente ervaring in Office 365-clienttoepassingen, bijvoorbeeld Teams, OneDrive voor Bedrijven, Outlook, enz.) door dezelfde set besturingselementen voor services zoals Exchange Online en Sharepoint Online te implementeren
- Toewijzing aan beleid moet worden uitgevoerd via groepen, niet individuen
- Doe regelmatig beoordelingen van de uitzonderingsgroepen die in het beleid worden gebruikt om de tijd te beperken dat gebruikers uit de beveiligingshouding vallen. Als u eigenaar bent van Azure AD P2, u toegangsbeoordelingen gebruiken om het proces te automatiseren

#### <a name="conditional-access-recommended-reading"></a>Aanbevolen lezen voor voorwaardelijke toegang

- [Aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [Configuraties voor identiteit en apparaattoegang](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Verwijzing naar de instellingen voor voorwaardelijke toegang in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [Algemeen beleid voor voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>Toegangsoppervlak

### <a name="legacy-authentication"></a>Verouderde verificatie

Sterke referenties zoals MFA kunnen apps niet beschermen met behulp van verouderde verificatieprotocollen, waardoor het de gewenste aanvalsvector is door kwaadwillende actoren. Het vergrendelen van legacy-verificatie is cruciaal om de status van toegangsbeveiliging te verbeteren.

Oudere verificatie is een term die verwijst naar verificatieprotocollen die worden gebruikt door apps zoals:

- Oudere Office-clients die geen moderne verificatie gebruiken (bijvoorbeeld Office 2010-client)
- Clients die e-mailprotocollen zoals IMAP/SMTP/POP gebruiken

Aanvallers geven sterk de voorkeur aan deze protocollen - in feite, bijna [100% van de wachtwoord spray aanvallen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) gebruik maken van legacy authenticatie protocollen! Hackers gebruiken verouderde verificatieprotocollen, omdat ze geen interactieve aanmelding ondersteunen, wat nodig is voor extra beveiligingsuitdagingen zoals multi-factor authenticatie en apparaatverificatie.

Als oudere verificatie op grote schaal wordt gebruikt in uw omgeving, moet u van plan zijn om oudere clients zo snel mogelijk te migreren naar clients die [moderne verificatie](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) ondersteunen. Als u bepaalde gebruikers al moderne verificatie gebruikt, maar anderen die nog steeds oudere verificatie gebruiken, moet u de volgende stappen ondernemen om oudere verificatieclients te vergrendelen:

1. [Rapporten met aanmeldingsactiviteit gebruiken](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) om gebruikers te identificeren die nog steeds verouderde verificatie gebruiken en herstel van plannen:

   a. Upgrade naar moderne verificatie-compatibele clients voor getroffen gebruikers.
   
   b. Plan een cut-over tijdsbestek te vergrendelen per stappen hieronder.
   
   c. Bepaal welke oudere toepassingen een harde afhankelijkheid hebben van verouderde verificatie. Zie stap 3 hieronder.

2. Schakel oudere protocollen bij de bron (bijvoorbeeld Exchange Mailbox) uit voor gebruikers die geen legacy auth gebruiken om meer blootstelling te voorkomen.
3. Voor de overige accounts (idealiter niet-menselijke identiteiten zoals serviceaccounts) gebruikt u voorwaardelijke toegang om oudere protocollen na verificatie [te beperken.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417)

#### <a name="legacy-authentication-recommended-reading"></a>Legacy-verificatie aanbevolen lezen

- [POP3- of IMAP4-toegang tot postvakken in Exchange Server in- of uitschakelen](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>Toestemmingssubsidies

Bij een aanval met illegale toestemmingsverlening maakt de aanvaller een door Azure AD geregistreerde toepassing die toegang vraagt tot gegevens zoals contactgegevens, e-mail of documenten. Gebruikers kunnen toestemming verlenen voor kwaadaardige toepassingen via phishing-aanvallen bij de landing op kwaadaardige websites.

Hieronder vindt u een lijst met apps met machtigingen die u mogelijk wilt onderzoeken voor Microsoft-cloudservices:

- Apps met app \*of gedelegeerd . LeesSchrijfmachtigingen
- Apps met gedelegeerde machtigingen kunnen e-mail namens de gebruiker lezen, verzenden of beheren
- Apps die de volgende machtigingen gebruiken:

| Resource | Machtiging |
| :- | :- |
| Office 365 Exchange Online | Eas. Accessasuser.All |
| | EWS. Accessasuser.All |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail.Read.Gedeeld |
| | Mail.ReadWrite Mail.ReadWrite |

- Apps verleend volledige gebruiker imitatie van de ingelogde gebruiker. Bijvoorbeeld:

|Resource | Machtiging |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

Om dit scenario te voorkomen, moet u verwijzen naar [het opsporen en herstellen van illegale toestemmingssubsidies in Office 365](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) om aanvragen met illegale subsidies of aanvragen die meer subsidies hebben te identificeren en vast te stellen dan nodig is. Verwijder vervolgens [de zelfbediening en](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent) [stel governanceprocedures vast](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow). Tot slot, plant regelmatig beoordelingen van app-machtigingen en verwijder ze wanneer ze niet nodig zijn.

#### <a name="consent-grants-recommended-reading"></a>Toestemming subsidies aanbevolen lezing

- [Microsoft Graph API-machtigingen](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Gebruikers- en groepsinstellingen

Hieronder vindt u de gebruikers- en groepsinstellingen die kunnen worden vergrendeld als er geen expliciete bedrijfsbehoefte is:

#### <a name="user-settings"></a>Gebruikersinstellingen

- **Externe gebruikers** - externe samenwerking kan organisch plaatsvinden in de onderneming met services zoals Teams, Power BI, Sharepoint Online en Azure Information Protection. Als u expliciete beperkingen hebt om door gebruikers geïnitieerde externe samenwerking te beheren, wordt u aangeraden externe gebruikers in te schakelen met [Azure AD Entitlement-beheer](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) of een gecontroleerde bewerking, zoals via uw helpdesk. Als u geen organische externe samenwerking voor services wilt toestaan, u [ervoor zorgen dat leden externe gebruikers volledig uitnodigen.](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations) U ook [specifieke domeinen toestaan of blokkeren](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list) in uitnodigingen van externe gebruikers.
- **App-registraties** - wanneer app-registraties zijn ingeschakeld, kunnen eindgebruikers zelf toepassingen aan boord nemen en toegang verlenen tot hun gegevens. Een typisch voorbeeld van app-registratie zijn gebruikers die Outlook-plug-ins inschakelen, of spraakassistenten zoals Alexa en Siri om hun e-mail en agenda te lezen of namens hen e-mails te verzenden. Als de klant besluit de registratie van apps uit te schakelen, moeten de InfoSec- en IAM-teams betrokken zijn bij het beheer van uitzonderingen (app-registraties die nodig zijn op basis van zakelijke vereisten), omdat ze de toepassingen moeten registreren bij een beheerdersaccount en waarschijnlijk een proces moeten ontwerpen om het proces te operationaliseren.
- **Beheerportal** - organisaties kunnen het Azure AD-blad vergrendelen in de Azure-portal, zodat niet-beheerders geen toegang hebben tot Azure AD-beheer in de Azure-portal en in de war raken. Ga naar de gebruikersinstellingen in de Azure AD-beheerportal om de toegang te beperken:

![Beperkte toegang voor beheerportalen](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> Niet-beheerders hebben nog steeds toegang tot de Azure AD-beheerinterfaces via de command-line en andere programmatische interfaces.

#### <a name="group-settings"></a>Groepsinstellingen

**Self-Service Group Management / Gebruikers kunnen beveiligingsgroepen / O365-groepen maken.** Als er geen huidig selfservice-initiatief is voor groepen in de cloud, kunnen klanten besluiten om het uit te schakelen totdat ze klaar zijn om deze mogelijkheid te gebruiken.

#### <a name="groups-recommended-reading"></a>Groepen aanbevolen lezing

- [Wat is Azure Active Directory B2B-samenwerking?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [Toepassingen integreren met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Apps, machtigingen en toestemming in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [Groepen gebruiken om toegang tot bronnen in Azure Active Directory te beheren](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [Selfservice-toepassingstoegangsbeheer instellen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>Verkeer vanaf onverwachte locaties

Aanvallers komen uit verschillende delen van de wereld. Beheer dit risico door gebruik te maken van beleid voor voorwaardelijke toegang met locatie als voorwaarde. Met de [locatievoorwaarde](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) van een beleid voor voorwaardelijke toegang u de toegang blokkeren voor locaties waar u zich niet aanmelden.

![Een nieuwe locatie met nieuwe naam maken](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Gebruik indien beschikbaar een SIEM-oplossing (Security Information and Event Management) om toegangspatronen in verschillende regio's te analyseren en te vinden. Als u geen SIEM-product gebruikt of als het geen verificatiegegevens van Azure AD inneemt, raden we u aan [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) te gebruiken om toegangspatronen in verschillende regio's te identificeren.

## <a name="access-usage"></a>Toegangsgebruik

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD-logboeken gearchiveerd en geïntegreerd met incidentresponseplannen

Toegang hebben tot aanmeldingsactiviteiten, audits en risicogebeurtenissen voor Azure AD is cruciaal voor het oplossen van problemen, gebruiksanalyses en forensisch onderzoek. Azure AD biedt toegang tot deze bronnen via REST API's met een beperkte bewaartermijn. Een SIEM-systeem (Security Information and Event Management), of gelijkwaardige archiveringstechnologie, is essentieel voor langdurige opslag van audits en ondersteuning. Als u langdurige opslag van Azure AD-logboeken wilt inschakelen, moet u deze toevoegen aan uw bestaande SIEM-oplossing of [Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor)gebruiken. Archieflogboeken die kunnen worden gebruikt als onderdeel van uw plannen en onderzoeken voor incidentrespons.

#### <a name="logs-recommended-reading"></a>Logboeken aanbevolen lezen

- [Naslaginformatie over Azure Active Directory-auditAPI](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [API-verwijzing naar api-verwijzing naar azure Active Directory-aanmeldingsrapport](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [Gegevens ophalen met de rapportage-API van Azure AD met certificaten](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Microsoft Graph voor Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Verwijzing naar api-activiteit voor Office 365-beheer](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [Azure Active Directory Power BI Content Pack gebruiken](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Samenvatting

Er zijn 12 aspecten aan een veilige infrastructuur van de Identiteit. Met deze lijst u referenties verder beveiligen en beheren, verificatie-ervaring definiëren, toewijzing delegeren, het gebruik meten en toegangsbeleid definiëren op basis van de beveiligingshouding van ondernemingen.

- Eigenaren toewijzen aan belangrijke taken.
- Implementeer oplossingen om zwakke of gelekte wachtwoorden te detecteren, wachtwoordbeheer en -bescherming te verbeteren en gebruikers toegang tot bronnen verder te beveiligen.
- Beheer de identiteit van apparaten om uw resources op elk gewenst moment en vanaf elke locatie te beschermen.
- Implementeer wachtwoordloze verificatie.
- Zorg voor een gestandaardiseerd eenmalig aanmeldingsmechanisme in de hele organisatie.
- Migreer apps van AD FS naar Azure AD om betere beveiliging en consistentere beheerbaarheid mogelijk te maken.
- Wijs gebruikers toe aan toepassingen door groepen te gebruiken om meer flexibiliteit en de mogelijkheid om op schaal te beheren mogelijk te maken.
- Op risico's gebaseerd toegangsbeleid configureren.
- Verouderde verificatieprotocollen vergrendelen.
- Illegale toestemmingssubsidies opsporen en saneren.
- Gebruikers- en groepsinstellingen vergrendelen.
- Langdurige opslag van Azure AD-logboeken inschakelen voor probleemoplossing, gebruiksanalyses en forensisch onderzoek.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [operationele controles en acties voor identity governance.](active-directory-ops-guide-govern.md)

---
title: Uw Azure AD-identiteitsinfrastructuur beveiligen
titleSuffix: Azure Active Directory
description: In dit document wordt een lijst met belangrijke acties beschreven die beheerders moeten implementeren om hen te helpen hun organisatie te beveiligen met Azure AD-mogelijkheden
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.openlocfilehash: e0db8edfdfa380697a1d8d7e262a7a84da2fb7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565533"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>Vijf stappen om uw identiteitsinfrastructuur te beveiligen

Als u dit document leest, bent u zich bewust van de betekenis van beveiliging. U draagt waarschijnlijk al de verantwoordelijkheid voor het beveiligen van uw organisatie. Als u anderen moet overtuigen van het belang van beveiliging, stuurt u ze om het nieuwste [Microsoft Security Intelligence-rapport](https://go.microsoft.com/fwlink/p/?linkid=2073747)te lezen.

Met dit document u een veiligere houding krijgen door gebruik te maken van de mogelijkheden van Azure Active Directory door een checklist in vijf stappen te gebruiken om uw organisatie in te enten tegen cyberaanvallen.

Met deze checklist u snel kritieke aanbevolen acties implementeren om uw organisatie onmiddellijk te beschermen door uit te leggen hoe u:

* Versterk je geloofsbrieven.
* Verminder het oppervlak van het aanvalsoppervlak.
* Automatiseer de reactie van bedreigingen.
* Maak gebruik van cloud intelligence.
* Schakel selfservice voor eindgebruikers in.

Zorg ervoor dat u bijhoudt welke functies en stappen volledig zijn tijdens het lezen van deze checklist.

> [!NOTE]
> Veel van de aanbevelingen in dit document zijn alleen van toepassing op toepassingen die zijn geconfigureerd om Azure Active Directory als identiteitsprovider te gebruiken. Het configureren van apps voor Single Sign-On garandeert de voordelen van referentiebeleid, bedreigingsdetectie, controle, logboekregistratie en andere functies die aan deze toepassingen worden toegevoegd. [Single sign-on via Azure Active Directory](../../active-directory/manage-apps/configure-single-sign-on-non-gallery-applications.md) is de basis- waarop al deze aanbevelingen zijn gebaseerd.

De aanbevelingen in dit document zijn afgestemd op de [Identity Secure Score](../../active-directory/fundamentals/identity-secure-score.md), een geautomatiseerde beoordeling van de identiteitsbeveiligingsconfiguratie van uw Azure AD-tenant. Organisaties kunnen de pagina Identity Secure Score in de Azure AD-portal gebruiken om hiaten in hun huidige beveiligingsconfiguratie te vinden om ervoor te zorgen dat ze de huidige [microsoft-aanbevolen procedures](identity-management-best-practices.md) voor beveiliging volgen. Als u elke aanbeveling implementeert op de pagina Secure Score, verhoogt u uw score en u uw voortgang bijhouden en u uw implementatie vergelijken met andere organisaties van vergelijkbare grootte of uw branche.

![Identiteitsbeveiligde score](./media/steps-secure-identity/azure-ad-sec-steps0.png)

> [!NOTE]
> Veel van de functies die hier worden beschreven, vereisen een Azure AD Premium-abonnement, terwijl sommige gratis zijn. Raadpleeg onze [Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory/) en [de azure AD Deployment-checklist](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-checklist-p2) voor meer informatie.

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>Voordat u begint: bevoegde accounts beveiligen met MFA

Voordat u begint met deze checklist, zorg ervoor dat je niet gecompromitteerd tijdens het lezen van deze checklist. U moet eerst uw bevoorrechte accounts beschermen.

Aanvallers die controle krijgen over bevoorrechte accounts kunnen enorme schade aanrichten, dus het is van cruciaal belang om deze accounts eerst te beschermen. Azure [Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) (MFA) inschakelen en vereisen voor alle beheerders in uw organisatie die [Azure AD Security Defaults](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) of [Conditional Access](../../active-directory/conditional-access/plan-conditional-access.md)gebruiken. Als u mfa nog niet hebt geïmplementeerd, doe het dan nu! Zo belangrijk is het.

Helemaal klaar? Laten we beginnen met de checklist.

## <a name="step-1---strengthen-your-credentials"></a>Stap 1 - Uw referenties versterken

De meeste inbreuken op de bedrijfsbeveiliging zijn afkomstig van een account dat is gecompromitteerd met een handvol methoden, zoals wachtwoordspray, replay van inbreuken of phishing. Lees meer over deze aanvallen in deze video (45 min):
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-uses-strong-authentication"></a>Zorg ervoor dat uw organisatie sterke verificatie gebruikt

Gezien de frequentie van wachtwoorden die worden geraden, gephished, gestolen met malware, of hergebruikt, is het van cruciaal belang om het wachtwoord te back's met een vorm van sterke referentie - meer informatie over [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md).

Als u het basisniveau van identiteitsbeveiliging eenvoudig wilt inschakelen, u de enablement met één klik gebruiken met [Azure AD-beveiligingsstandaarden.](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md) Beveiligingsstandaardinstellingen dwingen Azure MFA af voor alle gebruikers in een tenant en blokkeert aanmeldingen uit verouderde protocollen voor de hele tenant.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>Begin met het verbieden van vaak aangevallen wachtwoorden en schakel traditionele complexiteit en vervaldatumregels uit.

Veel organisaties gebruiken de traditionele complexiteit (waarvoor speciale tekens, getallen, hoofdletters en kleine letters vereist zijn) en regels voor het verlopen van wachtwoorden. [Uit onderzoek van Microsoft](https://aka.ms/passwordguidance) blijkt dat dit beleid ertoe leiden dat gebruikers wachtwoorden kiezen die gemakkelijker te raden zijn.

De dynamische [functie voor verboden wachtwoorden](../../active-directory/authentication/concept-password-ban-bad.md) van Azure AD maakt gebruik van huidig gedrag van aanvallers om te voorkomen dat gebruikers wachtwoorden instellen die gemakkelijk kunnen worden geraden. Deze mogelijkheid is altijd ingeschakeld wanneer gebruikers in de cloud worden gemaakt, maar is nu ook beschikbaar voor hybride organisaties wanneer ze [Azure AD-wachtwoordbeveiliging implementeren voor Windows Server Active Directory.](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) Azure AD-wachtwoordbeveiliging voorkomt dat gebruikers deze algemene wachtwoorden kiezen en kan worden uitgebreid om een wachtwoord te blokkeren dat aangepaste zoekwoorden bevat die u opgeeft. U bijvoorbeeld voorkomen dat uw gebruikers wachtwoorden kiezen die de productnamen van uw bedrijf of een lokaal sportteam bevatten.

Microsoft raadt aan om het volgende moderne wachtwoordbeleid aan te nemen op basis van [NIST-richtlijnen:](https://pages.nist.gov/800-63-3/sp800-63b.html)

1. Wachtwoorden vereisen hebben ten minste 8 tekens. Langer is niet per se beter, omdat ze ervoor zorgen dat gebruikers voorspelbare wachtwoorden kiezen, wachtwoorden opslaan in bestanden of ze opschrijven.
2. Schakel verloopregels uit, waardoor gebruikers gemakkelijk geraden wachtwoorden zoals **Spring2019!**
3. Schakel vereisten voor de samenstelling van tekens uit en voorkom dat gebruikers vaak aangevallen wachtwoorden kiezen, omdat gebruikers voorspelbare tekenvervangingen in wachtwoorden kiezen.

U [PowerShell](../../active-directory/authentication/concept-sspr-policy.md) gebruiken om te voorkomen dat wachtwoorden verlopen voor gebruikers als u rechtstreeks identiteiten in Azure AD maakt. Hybride organisaties moeten dit beleid implementeren met [beleidsinstellingen voor domeingroepen](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) of [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy).

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>Bescherm tegen gelekte referenties en voeg veerkracht toe tegen uitval

Als uw organisatie een hybride identiteitsoplossing met pass-through-verificatie of federatie gebruikt, moet u wachtwoordhashsynchronisatie inschakelen om de volgende twee redenen:

* Het [rapport Gebruikers met gelekte referenties](../../active-directory/reports-monitoring/concept-risk-events.md) in het Azure AD-beheer waarschuwt u voor gebruikersnaam- en wachtwoordparen, die zijn blootgesteld op het 'dark web'. Een ongelooflijke hoeveelheid wachtwoorden wordt gelekt via phishing, malware en hergebruik van wachtwoorden op sites van derden die later worden geschonden. Microsoft vindt veel van deze gelekte referenties en zal u vertellen, in dit rapport, als ze overeenkomen met referenties in uw organisatie - maar alleen als u [wachtwoord hash sync!](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
* In het geval van een on-premises storing (bijvoorbeeld bij een ransomware-aanval) u overschakelen naar het gebruik van [cloudverificatie met behulp van wachtwoordhashsynchronisatie.](choose-ad-authn.md) Met deze verificatiemethode voor back-upkunt u toegang blijven krijgen tot apps die zijn geconfigureerd voor verificatie met Azure Active Directory, waaronder Office 365. In dit geval hoeven IT-medewerkers geen persoonlijke e-mailaccounts aan te nemen om gegevens te delen totdat de on-premises storing is opgelost.

Meer informatie over hoe [wachtwoordhashsynchronisatie](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) werkt.

> [!NOTE]
> Als u wachtwoordhashsynchronisatie inschakelt en Azure AD Domain-services gebruikt, worden Kerberos (AES 256) hashes en optioneel NTLM (RC4, geen zout) hashes ook versleuteld en gesynchroniseerd met Azure AD.

### <a name="implement-ad-fs-extranet-smart-lockout"></a>Implementeren AD FS extranet smart lockout

Organisaties die toepassingen configureren om rechtstreeks te verifiëren voor Azure AD profiteren van [azure AD smart lockout](../../active-directory/authentication/concept-sspr-howitworks.md). Als u AD FS in Windows Server 2012R2 gebruikt, implementeert u AD FS [extranetuitsluitingsbeveiliging](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Als u AD FS op Windows Server 2016 gebruikt, implementeert u [extranet smart lockout](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). AD FS Smart Extranet-uitsluiting beschermt tegen brute force-aanvallen, die zich richten op AD FS en voorkomen dat gebruikers worden buitengesloten in Active Directory.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>Profiteer van intrinsiek veilige, gemakkelijker te gebruiken referenties

Met [Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)u wachtwoorden vervangen door sterke tweestapsverificatie op pc's en mobiele apparaten. Deze verificatie bestaat uit een nieuw type gebruikersreferentie dat veilig is gekoppeld aan een apparaat en een biometrische of pincode gebruikt.

## <a name="step-2---reduce-your-attack-surface"></a>Stap 2 - Verminder je aanvalsoppervlak

Gezien de alomtegenwoordigheid van wachtwoord compromis, het minimaliseren van de aanval oppervlak in uw organisatie is van cruciaal belang. Het elimineren van het gebruik van oudere, minder veilige protocollen, het beperken van toegangspunten en het uitoefenen van een grotere controle op de administratieve toegang tot resources kan helpen het aanvalsoppervlak te verminderen.

### <a name="block-legacy-authentication"></a>Verouderde verificatie blokkeren

Apps die hun eigen verouderde methoden gebruiken om te verifiëren met Azure AD en toegang te krijgen tot bedrijfsgegevens, vormen een ander risico voor organisaties. Voorbeelden van apps met legacy-verificatie zijn POP3-, IMAP4- of SMTP-clients. Verouderde verificatie-apps verifiëren namens de gebruiker en voorkomen dat Azure AD geavanceerde beveiligingsevaluaties uitbrengt. De alternatieve, moderne verificatie vermindert uw beveiligingsrisico, omdat het multi-factor authenticatie en voorwaardelijke toegang ondersteunt. We raden de volgende drie acties aan:

1. Blokkeer [oudere verificatie als u AD FS gebruikt](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12).
2. [Stel SharePoint Online en Exchange Online in om moderne verificatie te gebruiken.](../../active-directory/conditional-access/conditional-access-for-exo-and-spo.md)
3. Als u Azure AD Premium hebt, gebruikt u [beleid voor voorwaardelijke toegang](../../active-directory/conditional-access/overview.md) om verouderde verificatie te blokkeren, anders gebruikt u Azure AD Security [Defaults](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md).

### <a name="block-invalid-authentication-entry-points"></a>Ongeldige toegangspunten voor verificatie blokkeren

Met behulp van de veronderstel inbreuk mentaliteit, moet u de impact van gecompromitteerde gebruikersreferenties te verminderen wanneer ze gebeuren. Voor elke app in uw omgeving rekening houden met de geldige use cases: welke groepen, welke netwerken, welke apparaten en andere elementen zijn geautoriseerd - blokkeer dan de rest. Met [Azure AD Conditional Access](../../active-directory/conditional-access/overview.md)u bepalen hoe geautoriseerde gebruikers toegang krijgen tot hun apps en bronnen op basis van specifieke voorwaarden die u definieert.

### <a name="restrict-user-consent-operations"></a>Bewerkingen voor toestemming van gebruikers beperken

Het is belangrijk om inzicht te krijgen in de verschillende [Azure AD-toepassingstoestemmingservaringen,](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)de [soorten machtigingen en toestemming en](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)de implicaties daarvan voor de beveiligingshouding van uw organisatie. Standaard kunnen alle gebruikers in Azure AD toepassingen verlenen die gebruikmaken van het Microsoft-identiteitsplatform om toegang te krijgen tot de gegevens van uw organisatie. Hoewel gebruikers zelf toestemming kunnen geven, kunnen gebruikers eenvoudig nuttige toepassingen aanschaffen die integreren met Microsoft 365, Azure en andere services, maar het kan een risico vormen als ze niet zorgvuldig worden gebruikt en gecontroleerd.

Microsoft raadt [aan toekomstige toestemmingsbewerkingen voor gebruikers uit](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access#i-want-to-disable-all-future-user-consent-operations-to-any-application) te schakelen om uw oppervlakte te verkleinen en dit risico te beperken. Als de toestemming van de eindgebruiker is uitgeschakeld, worden eerdere toestemmingssubsidies nog steeds gehonoreerd, maar moeten alle toekomstige toestemmingsbewerkingen worden uitgevoerd door een beheerder. Toestemming van de beheerder kan door gebruikers worden aangevraagd via een geïntegreerde [werkstroom voor toestemming seintoestemming s.](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow) Voordat u de toestemming van eindgebruikers uitschakelt, gebruikt u onze [aanbevelingen](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-consent-requests) om deze wijziging in uw organisatie te plannen. Voor toepassingen die u alle gebruikers toegang wilt geven, u overwegen om namens alle gebruikers toestemming te [verlenen,](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)zodat gebruikers die nog geen individuele toestemming hebben gegeven, toegang hebben tot de app. Als u niet wilt dat deze toepassingen in alle scenario's beschikbaar zijn voor alle gebruikers, gebruikt u [toepassingstoewijzing](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups) en [voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) om de toegang van gebruikers tot apps te beperken.

Zorg ervoor dat gebruikers beheerdersgoedkeuring kunnen aanvragen voor nieuwe toepassingen om de wrijving van gebruikers te verminderen, het ondersteuningsvolume te minimaliseren en te voorkomen dat gebruikers zich aanmelden voor toepassingen met niet-Azure AD-referenties. Zodra u uw toestemmingsbewerkingen hebt geregeld, moeten beheerders regelmatig app- en toestemmingsmachtigingen controleren.


### <a name="implement-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management implementeren

Een andere impact van "assume breach" is de noodzaak om de kans te minimaliseren dat een gecompromitteerd account kan werken met een bevoorrechte rol.

[Azure AD Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md) helpt u accountrechten te minimaliseren door u te helpen:

* Gebruikers identificeren en beheren die zijn toegewezen aan beheerdersrollen.
* Begrijp ongebruikte of overmatige bevoegdheden die u moet verwijderen.
* Stel regels op om ervoor te zorgen dat bevoorrechte rollen worden beschermd door meervoudige verificatie.
* Stel regels op om ervoor te zorgen dat bevoorrechte rollen slechts lang genoeg worden toegekend om de bevoorrechte taak te volbrengen.

Schakel Azure AD PIM in, bekijk vervolgens de gebruikers aan wie beheerdersrollen zijn toegewezen en verwijder onnodige accounts in die rollen. Voor resterende bevoorrechte gebruikers, verplaats ze van permanent naar in aanmerking komend. Tot slot, stel een passend beleid om ervoor te zorgen wanneer ze toegang moeten krijgen tot die bevoorrechte rollen, kunnen ze dit veilig doen, met de nodige veranderingscontrole.

Volg als onderdeel van het implementeren van uw geprivilegieerde accountproces de [aanbevolen procedures om ten minste twee noodaccounts](../../active-directory/users-groups-roles/directory-admin-roles-secure.md) te maken om ervoor te zorgen dat u nog steeds toegang hebt tot Azure AD als u uzelf buitensluit.

## <a name="step-3---automate-threat-response"></a>Stap 3 - Automatiseer dreigingsrespons

Azure Active Directory heeft veel mogelijkheden die automatisch aanvallen onderscheppen, om de latentie tussen detectie en respons te verwijderen. U de kosten en risico's verminderen, wanneer u de tijd vermindert die criminelen gebruiken om zichzelf in uw omgeving in te sluiten. Hier zijn de concrete stappen die u nemen.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Gebruikersrisicobeveiligingsbeleid implementeren met Azure AD-identiteitsbeveiliging

Gebruikersrisico geeft aan hoe groot de kans is dat de identiteit van een gebruiker is aangetast en wordt berekend op basis van de [gebruikersrisicodetecties](../../active-directory/identity-protection/overview.md) die zijn gekoppeld aan de identiteit van een gebruiker. Een gebruikersrisicobeleid is een beleid voor voorwaardelijke toegang dat het risiconiveau evalueert voor een specifieke gebruiker of groep. Op basis van laag, gemiddeld, hoog risiconiveau kan een beleid worden geconfigureerd om toegang te blokkeren of een veilige wachtwoordwijziging vereisen met behulp van meervoudige verificatie. De aanbeveling van Microsoft is om een veilige wachtwoordwijziging voor gebruikers met een hoog risico te vereisen.

![Gebruikers voor wie wordt aangegeven dat ze risico lopen](./media/steps-secure-identity/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Aanmeldingsrisicobeleid implementeren met Azure AD-identiteitsbeveiliging

Aanmeldingsrisico is de waarschijnlijkheid dat iemand anders dan de eigenaar van het account probeert zich aan te melden met de identiteit. Een [aanmeldingsrisicobeleid](../../active-directory/identity-protection/overview.md) is een beleid voor voorwaardelijke toegang dat het risiconiveau voor een specifieke gebruiker of groep evalueert. Op basis van het risiconiveau (hoog/gemiddeld/laag) kan een beleid worden geconfigureerd om toegang te blokkeren of multi-factor authenticatie af te dwingen. Zorg ervoor dat u multi-factor authenticatie forceopmedium of boven risico aanmeldingen.

![Aanmelden bij anonieme IP's](./media/steps-secure-identity/azure-ad-sec-steps2.png)

## <a name="step-4---utilize-cloud-intelligence"></a>Stap 4 - Gebruik cloud intelligence

Het controleren en registreren van beveiligingsgerelateerde gebeurtenissen en gerelateerde waarschuwingen zijn essentiële onderdelen van een efficiënte beschermingsstrategie. Beveiligingslogboeken en -rapporten bieden u een elektronisch overzicht van verdachte activiteiten en helpen u patronen te detecteren die kunnen duiden op een poging tot of succesvolle externe penetratie van het netwerk en interne aanvallen. U controle gebruiken om de activiteiten van gebruikers te controleren, naleving van de regelgeving te documenteren, forensische analyses te doen en meer. Waarschuwingen bieden meldingen van beveiligingsgebeurtenissen.

### <a name="monitor-azure-ad"></a>Azure AD bewaken

Microsoft Azure-services en -functies bieden u configureerbare beveiligingscontrole- en logboekregistratieopties om hiaten in uw beveiligingsbeleid en -mechanismen te identificeren en deze hiaten op te vullen om inbreuken te voorkomen. U [Azure Logging en Auditing](log-audit.md) gebruiken en controlerapporten gebruiken in de Azure Active [Directory-portal.](../../active-directory/reports-monitoring/concept-audit-logs.md)

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>Azure AD Connect-status bewaken in hybride omgevingen

[Door AD FS te bewaken met Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) krijgt u meer inzicht in mogelijke problemen en zichtbaarheid van aanvallen op uw AD FS-infrastructuur. Azure AD Connect Health levert waarschuwingen met details, oplossingsstappen en koppelingen naar gerelateerde documentatie; gebruiksanalyses voor verschillende statistieken met betrekking tot verificatieverkeer; prestatiebewaking en rapportages.

![Azure AD Connect Health (Engelstalig)](./media/steps-secure-identity/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Azure AD-identiteitsbeveiliging-gebeurtenissen bewaken

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) is een hulpprogramma voor meldingen, bewaking en rapportage die u gebruiken om potentiële kwetsbaarheden op te sporen die van invloed zijn op de identiteit van uw organisatie. Het detecteert risicodetecties, zoals gelekte referenties, onmogelijke reizen en aanmeldingen van geïnfecteerde apparaten, anonieme IP-adressen, IP-adressen in verband met de verdachte activiteit en onbekende locaties. Schakel meldingswaarschuwingen in om e-mail te ontvangen van gebruikers die risico lopen en/of een wekelijkse samenvattingse-mail.

Azure AD Identity Protection biedt twee belangrijke rapporten die u dagelijks moet controleren:
1. Riskante aanmeldingsrapporten zullen aanmeldingsactiviteiten van gebruikers aan de oppervlakte brengen die u moet onderzoeken, de rechtmatige eigenaar heeft de aanmelding mogelijk niet uitgevoerd.
2. Risicovolle gebruikersrapporten zullen gebruikersaccounts aan de oppervlakte brengen die mogelijk zijn gecompromitteerd, zoals gelekte referenties die zijn gedetecteerd of de gebruiker die is aangemeld vanaf verschillende locaties waardoor een onmogelijke reisgebeurtenis wordt veroorzaakt.

![Gebruikers voor wie wordt aangegeven dat ze risico lopen](./media/steps-secure-identity/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>Apps en toestemminggegeven machtigingen controleren

Gebruikers kunnen worden misleid in het navigeren naar een gecompromitteerde website of apps die toegang krijgen tot hun profielinformatie en gebruikersgegevens, zoals hun e-mail. Een kwaadwillende acteur kan gebruik maken van de toestemming gegeven machtigingen ontvangen om hun mailbox inhoud te versleutelen en een losgeld te eisen om uw mailbox gegevens te herwinnen. [Beheerders moeten](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) de machtigingen van gebruikers controleren en controleren of de mogelijkheid van gebruikers om standaard toestemming te geven uitschakelen.

Naast het controleren van de machtigingen die door gebruikers worden gegeven, u [riskante of ongewenste OAuth-toepassingen in](https://docs.microsoft.com/cloud-app-security/investigate-risky-oauth) premium-omgevingen vinden.

## <a name="step-5---enable-end-user-self-service"></a>Stap 5 - Selfservice voor eindgebruikers inschakelen

U wilt de beveiliging zoveel mogelijk in evenwicht brengen met productiviteit. Langs dezelfde lijnen van het naderen van uw reis met de mentaliteit dat u een basis voor veiligheid op de lange termijn, u wrijving te verwijderen uit uw organisatie door empowerment van uw gebruikers, terwijl waakzaam blijven.

### <a name="implement-self-service-password-reset"></a>Self-service wachtwoord opnieuw instellen implementeren

De [selfservice wachtwoordreset (SSPR) van](../../active-directory/authentication/quickstart-sspr.md) Azure AD biedt een eenvoudige manier voor IT-beheerders om gebruikers in staat te stellen hun wachtwoorden of accounts opnieuw in te stellen of te ontgrendelen zonder tussenkomst van de helpdesk of beheerder. Het systeem bevat gedetailleerde rapportage die bijhoudt wanneer gebruikers hun wachtwoorden hebben gereset, samen met meldingen om u te waarschuwen voor misbruik of misbruik.

### <a name="implement-self-service-group-and-application-access"></a>Zelfservicegroep en toepassingstoegang implementeren

Azure AD biedt niet-beheerders de mogelijkheid om toegang tot bronnen te beheren met behulp van beveiligingsgroepen, Office 365-groepen, toepassingsrollen en toegangspakketcatalogi.  [Self-service group management](../../active-directory/users-groups-roles/groups-self-service-management.md) stelt groepseigenaren in staat om hun eigen groepen te beheren, zonder dat ze een administratieve rol hoeven te krijgen. Gebruikers kunnen ook Office 365-groepen maken en beheren zonder dat beheerders hun aanvragen moeten afhandelen en ongebruikte groepen automatisch verlopen.  [Azure AD-rechtenbeheer](../../active-directory/governance/entitlement-management-overview.md) maakt overdracht en zichtbaarheid verder mogelijk, met uitgebreide toegangsaanvraagworkflows en automatische vervaldatum.  U aan niet-beheerders de mogelijkheid delegeren om hun eigen toegangspakketten te configureren voor groepen, Teams, toepassingen en SharePoint Online-sites waarvan ze eigenaar zijn, met aangepaste beleidsregels voor wie toegang moet goedkeuren, inclusief het configureren van de managers en business partner sponsors als fiatteurs.

### <a name="implement-azure-ad-access-reviews"></a>Azure AD-toegangsbeoordelingen implementeren

Met [Azure AD-toegangsbeoordelingen](../../active-directory/governance/access-reviews-overview.md)u toegangspakket- en groepslidmaatschappen, toegang tot bedrijfstoepassingen en bevoorrechte roltoewijzingen beheren om ervoor te zorgen dat u een beveiligingsstandaard handhaaft.  Regelmatig toezicht door de gebruikers zelf, broneigenaren en andere revisoren zorgen ervoor dat gebruikers geen toegang behouden voor langere tijd wanneer ze het niet meer nodig hebben.

## <a name="summary"></a>Samenvatting

Er zijn veel aspecten aan een veilige identiteitsinfrastructuur, maar deze vijfstappenchecklist helpt u snel een veiligere en veilige identiteitsinfrastructuur te realiseren:

* Versterk je geloofsbrieven.
* Verminder het oppervlak van het aanvalsoppervlak.
* Automatiseer de reactie van bedreigingen.
* Maak gebruik van cloud intelligence.
* Maak meer voorspelbare en complete eindgebruikersbeveiliging mogelijk met zelfhulp.

We waarderen hoe serieus u Identity Security neemt en hopen dat dit document een nuttig stappenplan is voor een veiligere houding voor uw organisatie.

## <a name="next-steps"></a>Volgende stappen

Als u hulp nodig hebt bij het plannen en implementeren van de aanbevelingen, raadpleegt u de [azure AD-projectimplementatieplannen](https://aka.ms/deploymentplans) voor hulp.

Als u er zeker van bent dat al deze stappen volledig zijn, gebruikt u de [Identity Secure Score](../../active-directory/fundamentals/identity-secure-score.md)van Microsoft, die u op de hoogte houdt van de nieuwste best [practices](identity-management-best-practices.md) en beveiligingsbedreigingen.

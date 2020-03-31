---
title: Aanbevolen procedures voor toegang tot beveiligde beheerders - Azure AD | Microsoft Documenten
description: Zorg ervoor dat de beheerderstoegangs- en beheeraccounts van uw organisatie veilig zijn. Voor systeemarchitecten en IT-professionals die Azure AD, Azure en Microsoft Online Services configureren.
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 11/13/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c46facb2d43137175730bf04fea0efec9c1ecbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266271"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD

De beveiliging van de meeste of alle bedrijfsmiddelen in de moderne organisatie is afhankelijk van de integriteit van de bevoegde accounts die IT-systemen beheren en beheren. Kwaadwillende actoren, waaronder cyberaanvallers, richten zich vaak op beheerdersaccounts en andere elementen van bevoorrechte toegang om snel toegang te krijgen tot gevoelige gegevens en systemen met behulp van aanvallen op diefstal van referenties. Voor clouddiensten zijn preventie en respons de gezamenlijke verantwoordelijkheden van de cloudserviceprovider en de klant. Zie het [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report)voor meer informatie over de nieuwste bedreigingen voor eindpunten en de cloud. Dit artikel kan u helpen bij het ontwikkelen van een routekaart naar het dichten van de kloof tussen uw huidige plannen en de hier beschreven richtlijnen.

> [!NOTE]
> Microsoft streeft naar de hoogste niveaus van vertrouwen, transparantie, naleving van normen en naleving van de regelgeving. Meer informatie over hoe het wereldwijde incidentresponseteam van Microsoft de effecten van aanvallen op cloudservices vermindert en hoe beveiliging is ingebouwd in microsoft-bedrijfsproducten en cloudservices in [microsoft Trust Center - Beveiligings-](https://www.microsoft.com/trustcenter/security) en Microsoft-compliancedoelen bij Microsoft Trust Center - [Compliance](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Voor de meeste organisaties is de beveiliging van bedrijfsmiddelen afhankelijk van de integriteit van de bevoegde accounts die IT-systemen beheren en beheren. Cyberaanvallers richten zich op bevoorrechte toegang tot infrastructuursystemen (zoals Active Directory en Azure Active Directory) om toegang te krijgen tot de gevoelige gegevens van een organisatie. 

Traditionele benaderingen die zich richten op het beveiligen van de in- en uitgangen van een netwerk, omdat de primaire beveiligingsperimeter minder effectief is vanwege de toename van het gebruik van SaaS-apps en persoonlijke apparaten op het internet. De natuurlijke vervanging voor de netwerkbeveiligingsperimeter in een complexe moderne onderneming is het gebruik van verificatie- en autorisatie-elementen in de identiteitslaag van een organisatie.

Bevoorrechte administratieve accounts hebben effectief de controle over deze nieuwe 'beveiligingsperimeter'. Het is van cruciaal belang om bevoorrechte toegang te beschermen, ongeacht of de omgeving on-premises, cloud- of hybride on-premises en cloudgehoste services is. Om de administratieve toegang tegen bepaalde tegenstanders te beschermen, moet u een complete en doordachte benadering hanteren om de systemen van uw organisatie te isoleren van risico's. 

Voor het beveiligen van bevoorrechte toegang is wijzigingen nodig in

* Processen, administratieve praktijken en kennismanagement
* Technische componenten zoals hostdefenses, accountbeveiligingen en identiteitsbeheer

Dit document richt zich voornamelijk op het maken van een routekaart voor het beveiligen van identiteiten en toegang die worden beheerd of gerapporteerd in Azure AD, Microsoft Azure, Office 365 en andere cloudservices. Zie de richtlijnen voor on-premises en hybride bevoorrechte toegang die wordt beheerd vanuit Active Directory bij [Beveiligde Privileged Access,](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)voor organisaties die on-premises beheerdersaccounts hebben. 

> [!NOTE] 
> De richtlijnen in dit artikel hebben voornamelijk betrekking op functies van Azure Active Directory die zijn opgenomen in Azure Active Directory Premium-abonnementen P1 en P2. Azure Active Directory Premium P2 is opgenomen in de EMS E5-suite en Microsoft 365 E5-suite. Deze richtlijnen gaan ervan uit dat uw organisatie al Azure AD Premium P2-licenties heeft aangeschaft voor uw gebruikers. Als u deze licenties niet hebt, is een deel van de richtlijnen mogelijk niet van toepassing op uw organisatie. In dit artikel staat de term globale beheerder (of globale beheerder) ook synoniem met 'bedrijfsbeheerder' of 'tenantbeheerder'.

## <a name="develop-a-roadmap"></a>Een roadmap ontwikkelen 

Microsoft raadt u aan een roadmap te ontwikkelen en te volgen om bevoorrechte toegang tegen cyberaanvallers te beveiligen. U uw roadmap altijd aanpassen aan uw bestaande mogelijkheden en specifieke vereisten binnen uw organisatie. Elke fase van de roadmap moet de kosten en moeite voor tegenstanders verhogen om bevoorrechte toegang aan te vallen voor uw on-premises, cloud en hybride assets. Microsoft beveelt de volgende vier roadmap fasen: Deze aanbevolen roadmap schema's de meest effectieve en de snelste implementaties eerste, op basis van microsoft's ervaringen met cyber-aanval incident en reactie implementatie. De tijdlijnen voor deze routekaart zijn bij benadering.

![Fasen van de routekaart met tijdslijnen](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fase 1 (24-48 uur): Kritieke items die we u meteen aanraden te doen

* Fase 2 (2-4 weken): Verminder de meest gebruikte aanvalstechnieken

* Fase 3 (1-3 maanden): Vergroot zichtbaarheid en bouw volledige controle over de beheeractiviteit

* Fase 4 (zes maanden en daarna): Doorgaan met het bouwen van verdedigingen om uw beveiligingsplatform verder te verharden

Dit roadmap-framework is ontworpen om het gebruik van Microsoft-technologieën die u mogelijk al hebt geïmplementeerd, te maximaliseren. U ook profiteren van de belangrijkste huidige en toekomstige beveiligingstechnologieën en beveiligingstools integreren van andere leveranciers die u al hebt geïmplementeerd of overweegt te implementeren. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fase 1: Kritieke items die we u meteen aanraden te doen

![Eerste fase 1-items die u moet doen](./media/directory-admin-roles-secure/stage-one.png)

Fase 1 van de roadmap is gericht op kritieke taken die snel en eenvoudig te implementeren zijn. We raden u aan deze paar items meteen binnen de eerste 24-48 uur te doen om een basisniveau van veilige bevoorrechte toegang te garanderen. Deze fase van de beveiligde beveiligde toegangsroadmap bevat de volgende acties:

### <a name="general-preparation"></a>Algemene voorbereiding

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management inschakelen

Als u Azure AD Privileged Identity Management (PIM) nog niet hebt ingeschakeld, doet u dit in uw productietenant. Nadat u Privileged Identity Management hebt ingeschakeld, ontvangt u e-mailberichten met meldingen voor wijzigingen in de bevoegdheden voor bevoorrechte toegang. Deze meldingen bieden vroegtijdige waarschuwing wanneer extra gebruikers worden toegevoegd aan zeer bevoorrechte rollen in uw directory.

Azure AD Privileged Identity Management is opgenomen in Azure AD Premium P2 of EMS E5. Met deze oplossingen u de toegang tot toepassingen en resources in de on-premises omgeving en in de cloud beschermen. Als u nog geen Azure AD Premium P2 of EMS E5 hebt en meer functies wilt evalueren waarnaar in dit stappenplan wordt verwezen, meldt u zich aan voor de proefversie van Enterprise Mobility + Security gratis [90 dagen.](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial) Gebruik deze licentieproefversies om Azure AD Privileged Identity Management en Azure AD Identity Protection te proberen, om activiteiten te controleren met behulp van geavanceerde beveiligingsrapportages, controle- en waarschuwingen van Azure AD.

Nadat u Azure AD Privileged Identity Management hebt ingeschakeld:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een account dat een globale beheerder is van uw productietenant.

2. Als u de tenant wilt selecteren waar u Privileged Identity Management wilt gebruiken, selecteert u uw gebruikersnaam in de rechterbovenhoek van de Azure-portal.

3. Selecteer in het menu van de Azure-portal **Alle services** en filter de lijst voor Azure AD Privileged **Identity Management**.

4. Open Privileged Identity Management vanuit de lijst **Alle services** en maak deze vast aan uw dashboard.

De eerste persoon die Azure AD Privileged Identity Management in uw tenant gebruikt, krijgt automatisch de rollen **Beveiligingsbeheerder** en **Bevoorrechte rolbeheerders** toegewezen in de tenant. Alleen beheerders van bevoorrechte rollen kunnen de toewijzingen van azure AD-maprollen van gebruikers beheren. Na het toevoegen van Azure AD Privileged Identity Management krijgt u ook de beveiligingswizard te zien die u door de eerste detectie- en toewijzingservaring leidt. U de wizard op dit moment verlaten zonder extra wijzigingen aan te brengen. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Accounts identificeren en categoriseren die zich in zeer bevoorrechte rollen bevinden 

Nadat u Azure AD Privileged Identity Management hebt ingeschakeld, bekijkt u de gebruikers die zich in de directoryrollen bevinden, globale beheerder, bevoorrechte rolbeheerder, Exchange Online-beheerder en SharePoint Online-beheerder. Als u geen Azure AD PIM in uw tenant hebt, u de [PowerShell API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)gebruiken. Begin met de globale beheerrol omdat deze rol algemeen is: een gebruiker aan wie deze beheerdersrol is toegewezen, heeft dezelfde machtigingen voor alle cloudservices waarvoor uw organisatie is geabonneerd, ongeacht of deze rol is toegewezen in de Microsoft 365 beheercentrum, de Azure-portal of met de Azure AD-module voor Microsoft PowerShell. 

Verwijder accounts die niet langer nodig zijn in die rollen. Categoriseer vervolgens de resterende accounts die zijn toegewezen aan beheerdersrollen:

* Individueel toegewezen aan administratieve gebruikers, en kan ook worden gebruikt voor niet-administratieve doeleinden (bijvoorbeeld persoonlijke e-mail)
* Individueel toegewezen aan administratieve gebruikers en alleen aangewezen voor administratieve doeleinden
* Gedeeld tussen meerdere gebruikers
* Voor scenario's voor noodtoegang voor break-glass
* Voor geautomatiseerde scripts
* Voor externe gebruikers

#### <a name="define-at-least-two-emergency-access-accounts"></a>Definieer ten minste twee noodtoegangsaccounts 

Zorg ervoor dat u niet in een situatie terechtkomt waarin ze per ongeluk kunnen worden uitgesloten van het beheer van uw Azure AD-tenant vanwege het onvermogen om in te loggen of het account van een bestaande individuele gebruiker als beheerder te activeren. Als de organisatie bijvoorbeeld wordt gefedereerd naar een on-premises identiteitsprovider, is die identiteitsprovider mogelijk niet beschikbaar, zodat gebruikers zich niet on-premises kunnen aanmelden. U de impact van onbedoeld gebrek aan beheerderstoegang beperken door twee of meer noodtoegangsaccounts op te slaan in uw tenant.

Met noodtoegangsaccounts kunnen organisaties de bevoorrechte toegang binnen een bestaande Azure Active Directory-omgeving beperken. Deze accounts zijn zeer geprivilegieerd en worden niet toegewezen aan specifieke personen. Noodtoegangsaccounts zijn beperkt tot noodscenario's voor 'break glass'-scenario's waarbij normale administratieve accounts niet kunnen worden gebruikt. Organisaties moeten ervoor zorgen dat het gebruik van het noodaccount wordt controleren en verminderd tot alleen die tijd waarvoor het nodig is.

Evalueer de accounts die zijn toegewezen of in aanmerking komen voor de globale beheerdersrol. Als u geen cloudaccounts hebt gezien die het domein *.onmicrosoft.com gebruiken (bedoeld voor noodtoegang voor 'glas breken'), maakt u deze. Zie [Beheer van beheervan beheeraccounts voor noodtoegang in Azure AD](directory-emergency-access.md)voor meer informatie.

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Schakel meervoudige verificatie in en registreer alle andere zeer bevoorrechte niet-federatieve beheerdersaccounts van één gebruiker

Azure Multi-Factor Authentication (MFA) vereisen bij aanmelding voor alle individuele gebruikers die permanent zijn toegewezen aan een of meer van de Azure AD-beheerdersrollen: globale beheerder, beheerder van bevoorrechte rollen, Exchange Online-beheerder en SharePoint Online beheerder. Gebruik de handleiding om [Multi-factor Authentication (MFA) in](../authentication/howto-mfa-userstates.md) te schakelen voor [https://aka.ms/mfasetup](https://aka.ms/mfasetup)uw beheerdersaccounts en ervoor te zorgen dat al die gebruikers zich hebben geregistreerd bij . Meer informatie vindt u onder stap 2 en stap 3 van de gids [Bescherm toegang tot gegevens en services in Office 365.](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fase 2: De meest gebruikte aanvalstechnieken beperken

![Fase 2 Veelgebruikte aanvallen beperken](./media/directory-admin-roles-secure/stage-two.png)

Fase 2 van de routekaart richt zich op het verzachten van de meest gebruikte aanvalstechnieken van diefstal en misbruik van referenties en kan worden geïmplementeerd in ongeveer 2-4 weken. Deze fase van de beveiligde beveiligde toegangsroadmap bevat de volgende acties.

### <a name="general-preparation"></a>Algemene voorbereiding

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Een inventarisatie maken van services, eigenaren en beheerders

Met de toename van bring-your-own-device (BYOD) en work-from-home beleid en de groei van draadloze connectiviteit in bedrijven, is het van cruciaal belang dat u controleert wie verbinding maakt met uw netwerk. Een effectieve beveiligingscontrole onthult vaak apparaten, toepassingen en programma's die op uw netwerk worden uitgevoerd en die niet worden ondersteund door IT en dus mogelijk niet veilig zijn. Zie overzicht van [Azure-beveiligingsbeheer en -bewaking](../../security/fundamentals/management-monitoring-overview.md)voor meer informatie. Zorg ervoor dat u alle volgende taken opneemt in uw voorraadproces. 

* Identificeer de gebruikers met een administratieve rol en de services waar ze kunnen beheren.
* Gebruik Azure AD PIM om erachter te komen welke gebruikers in uw organisatie beheerderstoegang hebben tot Azure AD, inclusief extra rollen die verder gaan dan de functies in fase 1.
* Naast de rollen die in Azure AD zijn gedefinieerd, wordt Office 365 geleverd met een reeks beheerdersrollen die u toewijzen aan gebruikers in uw organisatie. Elke beheerrol wordt toegewezen aan algemene bedrijfsfuncties en geeft mensen in uw organisatie machtigingen om specifieke taken uit te voeren in het [Microsoft 365-beheercentrum.](https://admin.microsoft.com) Gebruik het Microsoft 365-beheercentrum om erachter te komen welke gebruikers in uw organisatie beheerderstoegang hebben tot Office 365, ook via rollen die niet worden beheerd in Azure AD. Zie Aanbevolen procedures voor Office 365 voor meer informatie [over office 365-beheerdersrollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) en [aanbevolen procedures voor beveiliging voor Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center).
* Voer de voorraad uit in andere services waarop uw organisatie vertrouwt, zoals Azure, Intune of Dynamics 365.
* Zorg ervoor dat uw beheerdersaccounts (accounts die worden gebruikt voor beheerdoeleinden, niet alleen de dagelijkse accounts van gebruikers) werke-mailadressen aan hen hebben gekoppeld en zich hebben geregistreerd voor Azure MFA of MFA on-premises hebben gebruikt.
* Vraag gebruikers naar hun zakelijke rechtvaardiging voor administratieve toegang.
* Verwijder beheerderstoegang voor personen en services die deze niet nodig hebben.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Microsoft-accounts identificeren in beheerdersrollen die moeten worden overgeschakeld naar werk- of schoolaccounts

Soms gebruiken de oorspronkelijke globale beheerders voor een organisatie hun bestaande Microsoft-accountreferenties opnieuw toen ze Azure AD begonnen te gebruiken. Deze Microsoft-accounts moeten worden vervangen door afzonderlijke cloudgebaseerde of gesynchroniseerde accounts. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Zorgen voor afzonderlijke gebruikersaccounts en e-maildoorsturen voor algemene beheerdersaccounts 

Wereldwijde beheerdersaccounts mogen geen persoonlijke e-mailadressen hebben, omdat persoonlijke e-mailaccounts regelmatig worden gephished door cyberaanvallers. Maak speciale accounts voor elke gebruiker met beheerdersbevoegdheden om internetrisico's (phishing-aanvallen, onbedoeld surfen op het web) te scheiden van beheerdersrechten. 

Als u dit nog niet hebt gedaan, maakt u afzonderlijke accounts voor gebruikers om globale beheertaken uit te voeren, om ervoor te zorgen dat ze niet per ongeluk e-mails openen of programma's uitvoeren die zijn gekoppeld aan hun beheerdersaccounts. Zorg ervoor dat deze accounts hun e-mail hebben doorgestuurd naar een werkende mailbox.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Ervoor zorgen dat de wachtwoorden van administratieve accounts onlangs zijn gewijzigd

Zorg ervoor dat alle gebruikers zich hebben aangemeld bij hun beheerdersaccounts en hun wachtwoorden ten minste eenmaal in de afgelopen 90 dagen hebben gewijzigd. Zorg er ook voor dat gedeelde accounts waarin meerdere gebruikers het wachtwoord kennen, onlangs hun wachtwoorden hebben gewijzigd.

#### <a name="turn-on-password-hash-synchronization"></a>Synchronisatie van wachtwoordhash inschakelen

Wachtwoordhashsynchronisatie is een functie die wordt gebruikt om hashes van gebruikerswachtwoordhashes te synchroniseren van een on-premises Active Directory-instantie naar een op een cloud gebaseerd Azure AD-exemplaar. Zelfs als u besluit om federatie te gebruiken met Active Directory Federation Services (AD FS) of andere identiteitsproviders, u optioneel wachtwoordhashsynchronisatie instellen als back-up voor het geval uw on-premises infrastructuur zoals AD- of ADFS-servers mislukt of tijdelijk niet beschikbaar. Hierdoor kunnen gebruikers zich aanmelden bij de service met hetzelfde wachtwoord dat ze gebruiken om zich aan te melden bij hun on-premises AD-exemplaar. Ook kan Identiteitsbescherming gecompromitteerde referenties detecteren door deze wachtwoordhashes te vergelijken met wachtwoorden waarvan bekend is dat ze zijn gecompromitteerd, als een gebruiker zijn e-mailadres en wachtwoord heeft gebruikt voor andere services die niet zijn verbonden met Azure AD.  Zie [Synchronisatie van wachtwoordhash implementeren met Azure AD Connect-synchronisatie](../hybrid/how-to-connect-password-hash-synchronization.md)voor meer informatie.

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Multi-factor authentication (MFA) vereisen voor gebruikers in alle bevoorrechte rollen en blootgestelde gebruikers

Azure AD raadt u aan multi-factor authenticatie (MFA) te vereisen voor al uw gebruikers, inclusief beheerders en alle andere gebruikers die een aanzienlijke impact zouden hebben als hun account wordt gecompromitteerd (bijvoorbeeld financiële functionarissen). Dit vermindert het risico op een aanval als gevolg van een gecompromitteerd wachtwoord.

Inschakelen:

* [MFA met beleid voor voorwaardelijke toegang](../authentication/howto-mfa-getstarted.md) voor alle gebruikers in uw organisatie.

Als u Windows Hello for Business gebruikt, kan aan de MFA-vereiste worden voldaan met de windows Hello-aanmeldingservaring. Zie [Windows Hello voor](https://docs.microsoft.com/windows/uwp/security/microsoft-passport)meer informatie. 

#### <a name="configure-identity-protection"></a>Identiteitsbeveiliging configureren 

Azure AD Identity Protection is een op algoritmen gebaseerde monitoring- en rapportagetool die u gebruiken om potentiële kwetsbaarheden op te sporen die van invloed zijn op de identiteit van uw organisatie. U geautomatiseerde antwoorden configureren op gedetecteerde verdachte activiteiten en passende maatregelen nemen om deze op te lossen. Zie [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) voor meer informatie.

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Uw Office 365-beveiligde score verkrijgen (als u Office 365 gebruikt)

Secure Score berekent welke Office 365-services u gebruikt (zoals OneDrive, SharePoint en Exchange) en bekijkt vervolgens uw instellingen en activiteiten en vergelijkt deze met een basislijn die door Microsoft is vastgesteld. Je krijgt een score op basis van hoe uitgelijnd je bent met de beste beveiligingsprocedures. Iedereen die beheerdersmachtigingen (globale beheerder of een aangepaste beheerdersrol) heeft voor een Office 365 Business Premium- of Enterprise-abonnement, heeft toegang tot de Beveiligde score op [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>De beveiligings- en nalevingsrichtlijnen van Office 365 controleren (als u Office 365 gebruikt)

Het [plan voor beveiliging en naleving](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) schetst de aanpak voor hoe een Office 365-klant Office 365 moet configureren en andere EMS-mogelijkheden moet benutten. Bekijk vervolgens stap 3-6 van hoe [u de toegang tot gegevens en services in Office 365 beveiligen](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) en de handleiding voor het controleren van beveiliging en naleving in Office [365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Office 365-activiteitscontrole configureren (als u Office 365 gebruikt)

U controleren hoe mensen in uw organisatie Office 365-services gebruiken, zodat u gebruikers identificeren die een beheerdersaccount hebben en die mogelijk geen Toegang tot Office 365 nodig hebben omdat ze zich niet bij die portals hebben aanmelden. Zie [Activiteitsrapporten in het Microsoft 365-beheercentrum](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)voor meer informatie.

#### <a name="establish-incidentemergency-response-plan-owners"></a>Eigenaren van incident-/noodoplossingen instellen

Het effectief uitvoeren van incidentrespons is een complexe onderneming. Daarom vereist het opzetten van een succesvolle incidentresponsemogelijkheid een aanzienlijke planning en middelen. Het is essentieel dat u voortdurend controleren op cyberaanvallen en procedures voor het prioriteren van de behandeling van incidenten vast te stellen. Effectieve methoden voor het verzamelen, analyseren en rapporteren van gegevens zijn essentieel om relaties op te bouwen en om communicatie tot stand te brengen met andere interne groepen en planeigenaren. Zie [Microsoft Security Response Center](https://technet.microsoft.com/security/dn440717)voor meer informatie. 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>Beveiligde on-premises bevoorrechte administratieve accounts, zo niet al gedaan

Als uw Azure Active Directory-tenant is gesynchroniseerd met on-premises Active Directory, volgt u de richtlijnen in [de beveiligingsupdate:](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)fase 1. Dit omvat het maken van afzonderlijke beheerdersaccounts voor gebruikers die on-premises beheertaken moeten uitvoeren, het implementeren van Privileged Access Workstations voor Active Directory-beheerders en het maken van unieke lokale beheerderswachtwoorden voor werkstations en Servers.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Aanvullende stappen voor organisaties die toegang tot Azure beheren

#### <a name="complete-an-inventory-of-subscriptions"></a>Een overzicht van abonnementen invullen

Gebruik de Enterprise-portal en de Azure-portal om de abonnementen in uw organisatie te identificeren die productietoepassingen hosten.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Microsoft-accounts verwijderen uit beheerdersrollen

Microsoft-accounts van andere programma's, zoals Xbox, Live en Outlook, mogen niet worden gebruikt als beheerdersaccounts voor organisatieabonnementen. Verwijder de beheerdersstatus van alle Microsoft-accounts en vervang chris@contoso.comdoor Azure Active Directory (bijvoorbeeld ) werk- of schoolaccounts.

#### <a name="monitor-azure-activity"></a>Azure-activiteit bewaken

Het Azure-activiteitenlogboek bevat een geschiedenis van gebeurtenissen op abonnementsniveau in Azure. Het biedt informatie over wie welke bronnen heeft gemaakt, bijgewerkt en verwijderd en wanneer deze gebeurtenissen hebben plaatsgevonden. Zie [Meldingen over belangrijke acties in uw Azure-abonnement controleren en ontvangen](../../azure-monitor/platform/quick-audit-notify-action-subscription.md)voor meer informatie.

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Aanvullende stappen voor organisaties die toegang tot andere cloud-apps beheren via Azure AD

#### <a name="configure-conditional-access-policies"></a>Beleid voor voorwaardelijke toegang configureren

Bereid beleid voor voorwaardelijke toegang voor on-premises en cloudgehoste toepassingen voor. Als gebruikers op de werkplek zijn aangesloten, krijgt u meer informatie van [On-premises voorwaardelijke toegang instellen met azure Active Directory-apparaatregistratie](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fase 3: Zichtbaarheid opbouwen en de volledige controle over de beheeractiviteit overnemen

![Fase 3 neemt de controle over de beheeractiviteit over](./media/directory-admin-roles-secure/stage-three.png)

Fase 3 bouwt voort op de oplossingen van fase 2 en is ontworpen om te worden geïmplementeerd in ongeveer 1-3 maanden. Deze fase van de beveiligde beveiligde toegangsroadmap bevat de volgende onderdelen.

### <a name="general-preparation"></a>Algemene voorbereiding

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Een toegangscontrole van gebruikers in beheerdersrollen voltooien

Meer zakelijke gebruikers krijgen bevoorrechte toegang via cloudservices, wat kan leiden tot een toenemend onbeheerd platform. Dit geldt ook voor gebruikers die globale beheerders worden voor Office 365, Azure-abonnementsbeheerders en gebruikers die beheerderstoegang hebben tot VM's of via SaaS-apps. In plaats daarvan moeten organisaties alle werknemers, met name beheerders, dagelijkse zakelijke transacties als onbevoegde gebruikers afhandelen en alleen beheerdersrechten overnemen als dat nodig is. Aangezien het aantal gebruikers in beheerdersrollen sinds de eerste goedkeuring kan zijn toegenomen, u toegangsbeoordelingen voltooien om elke gebruiker te identificeren en te bevestigen die in aanmerking komt om beheerdersrechten te activeren. 

Ga als volgt te werk:

* Bepaal welke gebruikers Azure AD-beheerders zijn, schakel on-demand, just-in-time beheerderstoegang en op rollen gebaseerde beveiligingsbesturingselementen in.
* Gebruikers converteren die geen duidelijke rechtvaardiging hebben voor beheerdersbevoorrechte toegang tot een andere rol (als er geen in aanmerking komende rol is, verwijdert u deze).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Doorgaan met de uitrol van sterkere verificatie voor alle gebruikers 

Vereisen dat C-suite-managers, managers op hoog niveau, kritisch IT- en beveiligingspersoneel en andere sterk blootgestelde gebruikers moderne, sterke verificatie hebben, zoals Azure MFA of Windows Hello. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Speciale werkstations gebruiken voor beheer voor Azure AD

Aanvallers kunnen proberen bevoorrechte accounts te targeten om toegang te krijgen tot de gegevens en systemen van een organisatie, zodat ze de integriteit en authenticiteit van gegevens kunnen verstoren, door middel van kwaadaardige code die de programmalogica wijzigt of de beheerder die een referentie invoert. Privileged Access Workstations (PAW's) beschikken over een speciaal besturingssysteem voor gevoelige taken dat is beveiligd tegen aanvallen via internet en dreigingsvectoren. Door deze gevoelige taken en accounts te scheiden van de werkstations en apparaten voor dagelijks gebruik, zorgt u voor een zeer goede beveiliging tegen phishingaanvallen, beveiligingsproblemen in toepassingen en besturingssystemen, verschillende imitatie-aanvallen en aanvallen waarbij wordt geprobeerd uw referenties te stelen, zoals het vastleggen van toetsaanslagen, pass-the-hash en pass-the-ticket. Door geprivilegieerde werkstations met toegang te implementeren, u het risico verkleinen dat beheerders beheerdersreferenties invoeren, behalve op een geharde desktopomgeving. Zie [Geprivilegieerde toegangsstations voor](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)meer informatie.

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Bekijk de aanbevelingen van het National Institute of Standards and Technology voor de afhandeling van incidenten 

Het National Institute of Standards and Technology (NIST) biedt richtlijnen voor incidentafhandeling, met name voor het analyseren van incidentgerelateerde gegevens en het bepalen van de juiste respons op elk incident. Zie [de handleiding voor computerbeveiliging (NIST) (SP 800-61, Revisie 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)voor meer informatie.

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Privileged Identity Management (PIM) voor JIT implementeren in extra administratieve rollen

Gebruik azure AD [Privileged Identity Management-mogelijkheden](../privileged-identity-management/pim-configure.md) voor Azure Active Directory. Tijdelijke activering van bevoorrechte rollen werkt door:

* Beheerdersrechten activeren om een specifieke taak uit te voeren
* MFA afdwingen tijdens het activeringsproces
* Waarschuwingen gebruiken om beheerders te informeren over out-of-band-wijzigingen
* Gebruikers in staat stellen om bepaalde bevoegdheden voor een vooraf geconfigureerde tijd te behouden
* Beveiligingsbeheerders toestaan alle bevoorrechte identiteiten te ontdekken, controlerapporten te bekijken en toegangsbeoordelingen te maken om elke gebruiker te identificeren die in aanmerking komt voor het activeren van beheerdersrechten

Als u Azure AD Privileged Identity Management al gebruikt, past u de termijnen voor tijdgebonden bevoegdheden zo nodig aan (bijvoorbeeld onderhoudsvensters).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>Blootstelling aan op wachtwoorden gebaseerde aanmeldingsprotocollen bepalen (als u Exchange Online gebruikt)

In het verleden gingen protocollen ervan uit dat combinaties van gebruikersnaam en wachtwoord waren ingebed in apparaten, e-mailaccounts, telefoons, enzovoort. Maar nu met het risico voor cyberaanvallen in de cloud, raden we u aan elke potentiële gebruiker te identificeren die, als hun referenties zijn gecompromitteerd, catastrofaal kan zijn voor de organisatie, en hen uit te sluiten van de mogelijkheid om in te loggen op hun e-mail via gebruikersnaam / wachtwoord door het implementeren van sterke authenticatievereisten en voorwaardelijke toegang. U verouderde verificatie blokkeren [met voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Controleer de details over [het blokkeren van basisverificatie](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) via Exchange online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Een beoordeling van rollensbeoordeling voor Office 365-rollen voltooien (als u Office 365 gebruikt)

Beoordelen of alle beheerdersgebruikers zich in de juiste rollen bevinden (verwijderen en opnieuw toewijzen volgens deze beoordeling).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Bekijk de aanpak voor het beheer van beveiligingsincidenten die in Office 365 wordt gebruikt en vergelijk met uw eigen organisatie

U dit rapport downloaden van [Security Incident Management in Microsoft Office 365.](https://www.microsoft.com/download/details.aspx?id=54302)

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Doorgaan met het beveiligen van on-premises bevoorrechte administratieve accounts

Als uw Azure Active Directory is verbonden met on-premises Active Directory, volgt u de richtlijnen in de [roadmap voor beveiligde toegang:](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access)fase 2. Dit omvat het implementeren van Privileged Access Workstations voor alle beheerders, waarbij MFA vereist is, just enough admin gebruikt voor DC-onderhoud, het aanvalsoppervlak van domeinen verlaagt en ATA implementeert voor aanvalsdetectie.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Aanvullende stappen voor organisaties die toegang tot Azure beheren

#### <a name="establish-integrated-monitoring"></a>Geïntegreerde monitoring opzetten

Het [Azure Security Center](../../security-center/security-center-intro.md) biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt blijven en werkt met een breed ecosysteem van beveiligingsoplossingen.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Inventariseren van uw bevoegde accounts binnen gehoste virtuele machines

In de meeste gevallen hoeft u gebruikers geen onbeperkte machtigingen te geven aan al uw Azure-abonnementen of -bronnen. U Azure AD-beheerrollen gebruiken om taken binnen uw organisatie te scheiden en alleen de hoeveelheid toegang te verlenen aan gebruikers die specifieke taken moeten uitvoeren. Gebruik bijvoorbeeld Azure AD-beheerdersrollen om de ene beheerder alleen VM's in een abonnement te laten beheren, terwijl een andere beheren SQL-databases binnen hetzelfde abonnement. Zie [Aan de slag met op rollen gebaseerd toegangsbeheer in de Azure-portal](../../role-based-access-control/overview.md)voor meer informatie.

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>Pim implementeren voor Ad-beheerdersrollen van Azure

Gebruik Privileged identity Management met Azure AD-beheerdersrollen om de toegang tot Azure-bronnen te beheren, te beheren en te bewaken. Het gebruik van PIM beschermt bevoorrechte accounts tegen cyberaanvallen door de blootstellingstijd van privileges te verlagen en uw zichtbaarheid in het gebruik ervan te vergroten door middel van rapporten en waarschuwingen. Zie [RBAC-toegang tot Azure-bronnen beheren met Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md)voor meer informatie.

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure-logboekintegraties gebruiken om relevante Azure-logboeken naar uw SIEM-systemen te verzenden 

Azure-logboekintegratie stelt u in staat om onbewerkte logboeken van uw Azure-resources te integreren in de bestaande SIEM-systemen (Security Information and Event Management) van uw organisatie. [Azure-logboekintegratie](../../security/fundamentals/azure-log-integration-overview.md) verzamelt Windows-gebeurtenissen uit Windows Event Viewer-logboeken en Azure-bronnen uit Azure Activity Logs, Azure Security Center-waarschuwingen en Azure Diagnostic-logboeken. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Aanvullende stappen voor organisaties die toegang tot andere cloud-apps beheren via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Gebruikersinrichting voor verbonden apps implementeren

Met Azure AD u het maken, onderhouden en verwijderen van gebruikersidentiteiten in cloudtoepassingen (SaaS) zoals Dropbox, Salesforce, ServiceNow, enzovoort automatiseren. Zie [Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor](../app-provisioning/user-provisioning.md)meer informatie met Azure AD .

#### <a name="integrate-information-protection"></a>Informatiebescherming integreren

Met MCAS u bestanden onderzoeken en beleid instellen op basis van classificatielabels voor Azure Information Protection, waardoor uw gegevens in de cloud beter zichtbaar en beter kunnen worden gebruikt. Bestanden scannen en classificeren in de cloud en Azure-labels voor informatiebescherming toepassen. Zie [Azure Information Protection-integratie](https://docs.microsoft.com/cloud-app-security/azip-integration)voor meer informatie.

#### <a name="configure-conditional-access"></a>Voorwaardelijke toegang configureren

Voorwaardelijke toegang configureren op basis van een groep, locatie en toepassingsgevoeligheid voor [SaaS-apps](https://azure.microsoft.com/overview/what-is-saas/) en azure AD-apps. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Activiteit in verbonden cloud-apps bewaken

Om ervoor te zorgen dat de toegang van gebruikers ook in verbonden toepassingen wordt beschermd, raden we u aan gebruik te maken van [Microsoft Cloud App Security.](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) Dit beveiligt de bedrijfstoegang tot cloud-apps, naast het beveiligen van uw beheerdersaccounts, door u in staat te stellen:

* Zichtbaarheid en beheer uitbreiden naar cloud-apps
* Beleid maken voor toegang, activiteiten en het delen van gegevens
* Identificeer automatisch risicovolle activiteiten, abnormaal gedrag en bedreigingen
* Voorkomen dat gegevenslekken
* Minimaliseer risico's en geautomatiseerde dreigingspreventie en beleidshandhaving

De SIEM-agent Cloud App Security integreert Cloud App Security met uw SIEM-server om gecentraliseerde bewaking van Office 365-waarschuwingen en -activiteiten mogelijk te maken. Het draait op uw server en haalt waarschuwingen en activiteiten van Cloud App Security en streamt ze naar de SIEM-server. Zie [SIEM-integratie](https://docs.microsoft.com/cloud-app-security/siem)voor meer informatie.

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fase 4: Doorgaan met het bouwen van verdedigingen naar een meer proactieve veiligheidshouding

![Fase 4 neemt een proactieve veiligheidshouding aan](./media/directory-admin-roles-secure/stage-four.png)

Fase 4 van de routekaart bouwt voort op de zichtbaarheid van fase 3 en is ontworpen om binnen zes maanden en daarna te worden geïmplementeerd. Het invullen van een roadmap helpt u bij het ontwikkelen van sterke bevoorrechte toegangsbeveiligingen tegen potentiële aanvallen die momenteel bekend en beschikbaar zijn. Helaas evolueren en verschuiven beveiligingsbedreigingen voortdurend, dus we raden u aan beveiliging te zien als een doorlopend proces dat gericht is op het verhogen van de kosten en het verminderen van het slagingspercentage van tegenstanders die zich richten op uw omgeving.

Het beveiligen van bevoorrechte toegang is een cruciale eerste stap om beveiligingsgaranties voor bedrijfsmiddelen in een moderne organisatie vast te stellen, maar het is niet het enige onderdeel van een volledig beveiligingsprogramma dat elementen zou bevatten, zoals beleid, bewerkingen, informatie beveiliging, servers, applicaties, pc's, apparaten, cloudfabric en andere componenten bieden doorlopende beveiligingsgaranties. 

Naast het beheren van uw accounts voor bevoorrechte toegang, raden we u aan om het volgende voortdurend te bekijken:

* Zorg ervoor dat beheerders hun dagelijkse werkzaamheden doen als onbevoegde gebruikers.
* Geef alleen bevoorrechte toegang wanneer dat nodig is en verwijder deze daarna (just-in-time).
* Controleactiviteiten met betrekking tot geprivilegieerde accounts behouden en controleren.

Zie [Microsoft Cloud IT-architectuurbronnen](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources)voor meer informatie over het bouwen van een complete beveiligingsroadmap. Neem voor meer informatie over het inschakelen van Microsoft-services om u te helpen bij een van deze onderwerpen contact op met uw Microsoft-vertegenwoordiger of [raadpleeg kritieke cyberverdedigingen bouwen om uw onderneming te beschermen.](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)

Deze laatste fase van de beveiligde privileged access roadmap bevat de volgende onderdelen.

### <a name="general-preparation"></a>Algemene voorbereiding

#### <a name="review-admin-roles-in-azure-active-directory"></a>Beheerdersrollen controleren in Azure Active Directory 

Bepaal of de huidige ingebouwde Azure AD-beheerrollen nog steeds up-to-date zijn en zorg ervoor dat gebruikers zich alleen in de rollen en delegaties bevinden die ze nodig hebben voor bijbehorende machtigingen. Met Azure AD u afzonderlijke beheerders aanwijzen om verschillende functies te kunnen uitvoeren. Zie [Beheerdersrollen toewijzen in Azure Active Directory](directory-assign-admin-roles.md)voor meer informatie.

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Gebruikers controleren die beheer hebben van Azure AD-apparaten

Zie [Hybride Azure Active Directory-samengevoegde apparaten configureren](../device-management-hybrid-azuread-joined-devices-setup.md)voor meer informatie.

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>Leden van [ingebouwde Office 365-beheerdersrollen bekijken](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)
Als u Office 365 gebruikt.
‎
#### <a name="validate-incident-response-plan"></a>Plan voor incidentrespons valideren

Om uw abonnement te verbeteren, raadt Microsoft u aan regelmatig te valideren of uw abonnement werkt zoals verwacht:

* Ga door uw bestaande routekaart om te zien wat er gemist werd
* Op basis van de postmortemanalyse, bestaande bestaande of definiëren van nieuwe best practices
* Zorg ervoor dat uw bijgewerkte incidentresponseplan en aanbevolen procedures worden verspreid over uw organisatie


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Aanvullende stappen voor organisaties die toegang tot Azure beheren 

Bepaal of u het [eigendom van een Azure-abonnement moet overdragen aan een ander account.](../../cost-management-billing/manage/billing-subscription-transfer.md)
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Break glass": wat te doen in geval van nood

![Accounts voor toegang tot noodgolfglas](./media/directory-admin-roles-secure/emergency.jpeg)

1. Informeer belangrijke managers en beveiligingsfunctionarissen met relevante informatie over het incident.

2. Bekijk je aanvalsplaybook. 

3. Krijg toegang tot de gebruikersnaam/wachtwoordcombinatie van uw 'break glass'-account om u aan te melden bij Azure AD. 

4. Hulp van Microsoft krijgen door [een Azure-ondersteuningsaanvraag te openen.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

5. Bekijk de [azure AD-aanmeldingsrapporten](../reports-monitoring/overview-reports.md). Er kan een vertraging zijn tussen een gebeurtenis die zich voordoet en wanneer deze in het rapport is opgenomen.

6. Voor hybride omgevingen moet u mogelijk tijdelijk overschakelen van federatieve verificatie naar wachtwoordhashsynchronisatie. Hiermee wordt de domeinfederatie teruggezet naar beheerde verificatie totdat de AD FS-server beschikbaar is.

7. Controleer e-mail op geprivilegieerde accounts.

8. Zorg ervoor dat u back-ups van relevante logboeken opslaat voor mogelijk forensisch en juridisch onderzoek.

Zie [Security Incident Management in Microsoft Office 365](https://aka.ms/Office365SIM)voor meer informatie over hoe Microsoft Office 365 omgaat met beveiligingsincidenten.

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>VEELGESTELDE VRAGEN: Veelgestelde vragen die we krijgen met betrekking tot het beveiligen van bevoorrechte toegang  

**V:** Wat moet ik doen als ik nog geen beveiligde toegangscomponenten heb geïmplementeerd?

**Antwoord:** Definieer ten minste twee break-glass-accounts, wijs MFA toe aan uw bevoegde beheerdersaccounts en scheid gebruikersaccounts van globale beheerdersaccounts.

**V:** Na een schending, wat is het belangrijkste probleem dat eerst moet worden aangepakt?

**Antwoord:** Zorg ervoor dat u de sterkste verificatie nodig hebt voor hoogopgeleide personen.

**V:** Wat gebeurt er als onze bevoorrechte beheerders zijn gedeactiveerd?

**Antwoord:** Maak een globaal beheerdersaccount dat altijd up-to-date wordt gehouden.

**V:** Wat gebeurt er als er nog maar één globale beheerder over is en deze niet kan worden bereikt? 

**Antwoord:** Gebruik een van uw break-glass accounts om direct bevoorrechte toegang te krijgen.

**V:** Hoe kan ik beheerders binnen mijn organisatie beveiligen?

**Antwoord:** Hebben admins altijd hun dagelijkse werkzaamheden doen als standaard "unprivileged" gebruikers.

**V:** Wat zijn de aanbevolen procedures voor het maken van beheerdersaccounts binnen Azure AD?

**Antwoord:** Bevoorrechte toegang reserveren voor specifieke beheertaken.

**V:** Welke tools bestaan er voor het verminderen van permanente beheerderstoegang?

**Antwoord:** Pim (Privileged Identity Management) en Azure AD-beheerrollen.

**V:** Wat is de positie van Microsoft bij het synchroniseren van beheerdersaccounts naar Azure AD?

**Antwoord:** Tier 0-beheerdersaccounts (inclusief accounts, groepen en andere elementen met directe of indirecte beheerbeheer over het AD-forest, domeinen of domeincontrollers en alle elementen) worden alleen gebruikt voor on-premises AD-accounts en worden doorgaans niet gesynchroniseerd voor Azure AD voor de cloud.

**V:** Hoe kunnen we voorkomen dat beheerders willekeurige beheerderstoegang toewijzen in de portal?

**Antwoord:** Gebruik niet-bevoorrechte accounts voor alle gebruikers en de meeste beheerders. Begin met het ontwikkelen van een voetafdruk van de organisatie om te bepalen welke paar beheerdersaccounts moeten worden geprivilegieerd. En monitor voor nieuw gecreëerde administratieve gebruikers.

## <a name="next-steps"></a>Volgende stappen

* [Microsoft Trust Center for Product Security](https://www.microsoft.com/trustcenter/security) – Beveiligingsfuncties van Microsoft-cloudproducten en -services

* [Microsoft Trust Center - Compliance](https://www.microsoft.com/trustcenter/compliance/complianceofferings) – Microsoft's uitgebreide reeks compliance-aanbiedingen voor cloudservices

* [Richtlijnen voor het uitvoeren van een risicobeoordeling](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - Beveiligings- en nalevingsvereisten voor Microsoft-cloudservices beheren

### <a name="other-microsoft-online-services"></a>Andere Microsoft Online Services

* [Microsoft Intune Security](https://www.microsoft.com/trustcenter/security/intune-security) – Intune biedt mogelijkheden voor beheer van mobiele apparaten, mobiel applicatiebeheer en pc-beheer vanuit de cloud.

* [Microsoft Dynamics 365 security](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 is de Cloud-gebaseerde oplossing voor microsoft-cloud die de mogelijkheden voor klantrelatiebeheer (CRM) en erp-mogelijkheden (Enterprise Resource Planning) ontkracht.

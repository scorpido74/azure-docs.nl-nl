---
title: Aanbevolen procedures voor beveiligde beheerders toegang-Azure AD | Microsoft Docs
description: Zorg ervoor dat de beheerders-en beheerders accounts van uw organisatie veilig zijn. Voor systeem architecten en IT-professionals die Azure AD, Azure en micro soft Online Services configureren.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063846"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>Uitgebreide toegang beveiligen voor hybride en Cloud implementaties in azure AD

De beveiliging van de meeste of alle bedrijfs assets in de moderne organisatie is afhankelijk van de integriteit van de bevoegde accounts die IT-systemen beheren en beheren. Kwaadwillende actoren met inbegrip van Cyber aanvallers richten vaak beheerders accounts en andere elementen van bevoegde toegang op om snel toegang te krijgen tot gevoelige gegevens en systemen met behulp van aanvallen op basis van referentie diefstal. Voor Cloud Services zijn de gezamenlijke verantwoordelijkheden van de Cloud serviceprovider en de klant van toepassing. Zie het [micro soft Security Intelligence-rapport](https://www.microsoft.com/security/operations/security-intelligence-report)voor meer informatie over de nieuwste bedreigingen voor eind punten en de Cloud. In dit artikel vindt u informatie over het ontwikkelen van een route kaart voor het sluiten van de hiaten tussen uw huidige plannen en de hier beschreven richt lijnen.

> [!NOTE]
> Micro soft wordt op de hoogte gebracht van de hoogste mate van vertrouwen, transparantie, normen conform en naleving van de regelgeving. Meer informatie over de manier waarop het micro soft Global incident response team de gevolgen van aanvallen op Cloud Services verkleint en hoe beveiliging is ingebouwd in micro soft-producten en Cloud Services in micro [soft vertrouwens centrum: beveiligings](https://www.microsoft.com/trustcenter/security) -en micro soft-nalevings doelen in [micro soft vertrouwens centrum-naleving](https://www.microsoft.com/trustcenter/compliance).

<!--## Risk management, incident response, and recovery preparation

A cyber-attack, if successful, can shut down operations not just for a few hours, but in some cases for days or even weeks. The collateral damage, such as legal ramifications, information leaks, and media coverage, could potentially continue for years. To ensure effective company-wide risk containment, cybersecurity and IT pros must align their response and recovery processes. To reduce the risk of business disruption due to a cyber-attack, industry experts recommend you do the following:

* As part of your risk management operations, establish a crisis management team for your organization that is responsible for managing all types of business disruptions.

* Compare your current risk mitigations, incident response, and recovery plan with industry best practices for managing a business disruption before, during, and after a cyber-attack.

* Develop and implement a roadmap for closing the gaps between your current plans and the best practices described in this document.


## Securing privileged access for hybrid and cloud deployments

does the article really start here?-->
Voor de meeste organisaties is de beveiliging van bedrijfs assets afhankelijk van de integriteit van de bevoegde accounts waarmee IT-systemen worden beheerd en beheerd. Cyber aanvallers richten zich op bevoorrechte toegang tot infrastructuur systemen (zoals Active Directory en Azure Active Directory) om toegang te krijgen tot gevoelige gegevens van een organisatie. 

Traditionele benaderingen die zich richten op het beveiligen van de ingangs-en uitgangs punten van een netwerk, omdat de primaire beveiligings verbinding minder effectief is vanwege de toename van het gebruik van SaaS-apps en persoonlijke apparaten op internet. De natuurlijke vervanging voor de netwerk beveiligings verbinding in een complexe moderne onderneming is de verificatie-en autorisatie controles in de laag van een organisatie.

Geprivilegieerde beheerders accounts zijn effectief in het beheer van dit nieuwe beveiligings netwerk. Het is van cruciaal belang om de toegang te beschermen, ongeacht of de omgeving on-premises, Cloud of hybride on-premises en in de Cloud gehoste services is. Voor het beveiligen van administratieve toegang tegen de vastgestelde aanvallers moet u een volledige en doordachte benadering nemen voor het isoleren van de systemen van uw organisatie tegen Risico's. 

Het beveiligen van bevoegde toegang vereist wijzigingen in

* Processen, beheer procedures en kennis beheer
* Technische onderdelen, zoals beveiligingen van de host, account bescherming en identiteits beheer

Dit document richt zich voornamelijk op het maken van een plan voor het beveiligen van identiteiten en toegang die worden beheerd of gerapporteerd in azure AD, Microsoft Azure, Office 365 en andere Cloud Services. Voor organisaties met on-premises beheerders accounts raadpleegt u de richt lijnen voor on-premises en hybride privileged Access die worden beheerd via Active Directory op het [beveiligen van bevoegde toegang](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access). 

> [!NOTE] 
> De richt lijnen in dit artikel zijn voornamelijk bedoeld voor functies van Azure Active Directory die zijn opgenomen in Azure Active Directory Premium plan P1 en P2. Azure Active Directory Premium P2 is opgenomen in EMS E5 Suite en Microsoft 365 E5 Suite. In deze richt lijnen wordt ervan uitgegaan dat uw organisatie al Azure AD Premium P2-licenties heeft gekocht voor uw gebruikers. Als u deze licenties niet hebt, is het mogelijk dat bepaalde instructies niet van toepassing zijn op uw organisatie. In dit artikel is de term globale beheerder (of globale beheerder) ook synoniemen als ' bedrijfs beheerder ' of ' Tenant beheerder '.

## <a name="develop-a-roadmap"></a>Een route kaart ontwikkelen 

Micro soft raadt u aan om een plan te ontwikkelen en te volgen om bevoegde toegang te beveiligen tegen Cyber aanvallers. U kunt uw plan altijd aanpassen aan uw bestaande mogelijkheden en specifieke vereisten binnen uw organisatie. Elke fase van het plan moet de kosten en moeilijkheid verhogen voor aanvallers om toegang te krijgen tot uw on-premises, Cloud-en hybride activa. Micro soft raadt de volgende vier plan stadia aan: met dit aanbevolen schema wordt het meest effectief en de snelste implementaties gepland, op basis van de ervaringen van micro soft met betrekking tot het incidenten van Cyber aanvallen en het implementeren van de reactie. De tijd lijnen voor dit schema zijn ongeveer.

![Fasen van het schema met tijd lijnen](./media/directory-admin-roles-secure/roadmap-timeline.png)

* Fase 1 (24-48 uur): essentiële items die wij u het beste meteen doen

* Fase 2 (2-4 weken): de meest gebruikte aanvals technieken beperken

* Fase 3 (1-3 maanden): Maak inzicht in en bouw volledig beheer van beheer activiteiten

* Fase 4 (zes maanden en langer): door gaan met het bouwen van verdedigings elementen voor een verdere beveiliging van uw beveiligings platform

Dit raam werk schema is ontworpen om het gebruik van micro soft-technologieën die u mogelijk al hebt geïmplementeerd, te maximaliseren. U kunt ook profiteren van belang rijke huidige en aanstaande beveiligings technologieën en integreer beveiligings hulpprogramma's van andere leveranciers die u al hebt geïmplementeerd of overweegt de implementatie uit te voeren. 

## <a name="stage-1-critical-items-that-we-recommend-you-do-right-away"></a>Fase 1: essentiële items die wij het beste direct doen

![Fase 1 essentiële items eerst uitvoeren](./media/directory-admin-roles-secure/stage-one.png)

Fase 1 van het schema is gericht op kritieke taken die snel en gemakkelijk kunnen worden geïmplementeerd. U wordt aangeraden deze enkele items direct binnen de eerste 24-48 uur uit te voeren om een basis niveau van beveiligde privileged Access te garanderen. Deze fase van de beveiligde route kaart voor uitgebreide toegang bevat de volgende acties:

### <a name="general-preparation"></a>Algemene voor bereiding

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management inschakelen

Als u Azure AD Privileged Identity Management (PIM) nog niet hebt ingeschakeld, doet u dat in uw productie Tenant. Nadat u Privileged Identity Management hebt ingeschakeld, ontvangt u een e-mail bericht met meldingen voor de gewijzigde rol van de toegang. Deze meldingen geven een vroegtijdige waarschuwing wanneer extra gebruikers worden toegevoegd aan rollen met zeer privileged in uw Directory.

Azure AD Privileged Identity Management is opgenomen in Azure AD Premium P2 of EMS E5. Deze oplossingen helpen u bij het beveiligen van de toegang tot toepassingen en bronnen in de on-premises omgeving en in de Cloud. Als u nog niet beschikt over Azure AD Premium P2 of EMS E5 en u meer van de functies in dit plan wilt evalueren, kunt u zich registreren voor de [Enterprise Mobility + Security gratis 90-daagse proef versie](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial). Gebruik deze licentie experimenten om Azure AD Privileged Identity Management en Azure AD Identity Protection te proberen om activiteiten te bewaken met behulp van geavanceerde beveiligings rapporten, controle en waarschuwingen van Azure AD.

Nadat u Azure AD Privileged Identity Management hebt ingeschakeld:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met een account dat een globale beheerder van uw productie Tenant is.

2. Als u de Tenant wilt selecteren waarvoor u Privileged Identity Management wilt gebruiken, selecteert u uw gebruikers naam in de rechter bovenhoek van de Azure Portal.

3. Selecteer in het menu Azure Portal **alle services** en filter de lijst voor **Azure AD privileged Identity Management**.

4. Open Privileged Identity Management in de lijst **alle services** en vastmaken aan uw dash board.

De eerste persoon die Azure AD Privileged Identity Management in uw Tenant moet gebruiken, krijgt automatisch de beheerders rollen **beveiligings beheerder** en **privileged Role** in de Tenant toegewezen. Alleen bevoegde beheerdersrol kunnen de toewijzingen van Azure AD-Directory functies van gebruikers beheren. Nadat u Azure AD Privileged Identity Management hebt toegevoegd, ziet u ook de wizard Beveiliging die u door de eerste detectie-en toewijzings ervaring leidt. U kunt de wizard afsluiten zonder verdere wijzigingen aan te brengen. 

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>Accounts identificeren en categoriseren die zich in rollen met een hoge bevoegdheden bevinden 

Nadat u Azure AD Privileged Identity Management hebt ingeschakeld, bekijkt u de gebruikers die zich in de Directory rollen globale beheerder, beheerdersrol, Exchange Online beheerder en share point online-beheerder bevinden. Als u geen Azure AD PIM in uw Tenant hebt, kunt u de [Power shell-API](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)gebruiken. Begin met de rol van globale beheerder omdat deze rol algemeen is: een gebruiker aan wie deze beheerdersrol is toegewezen, heeft dezelfde machtigingen voor alle Cloud Services waarvoor uw organisatie is geabonneerd, ongeacht of ze deze rol hebben toegewezen in de Microsoft 365 beheer centrum, de Azure Portal of met de Azure AD-module voor micro soft power shell. 

Verwijder alle accounts die niet meer nodig zijn voor deze rollen. Vervolgens categoriseert u de overige accounts die zijn toegewezen aan beheerders rollen:

* Individueel toegewezen aan gebruikers met beheerders rechten en kunnen ook worden gebruikt voor niet-administratieve doel einden (bijvoorbeeld persoonlijke e-mail berichten)
* Individueel toegewezen aan gebruikers met beheerders rechten en alleen voor administratieve doel einden worden aangewezen
* Gedeeld door meerdere gebruikers
* Voor scenario's voor nood gevallen
* Voor geautomatiseerde scripts
* Voor externe gebruikers

#### <a name="define-at-least-two-emergency-access-accounts"></a>Ten minste twee accounts voor toegang in nood gevallen definiëren 

Zorg ervoor dat u niet in een situatie komt waarvan u per ongeluk het beheer van uw Azure AD-Tenant zou kunnen afwegen, omdat u zich niet kunt aanmelden of een bestaand account van een individuele gebruiker als beheerder wilt activeren. Als de organisatie bijvoorbeeld federatief is voor een on-premises ID-provider, is deze id-provider mogelijk niet beschikbaar, zodat gebruikers zich niet on-premises kunnen aanmelden. U kunt de gevolgen van onopzettelijke beheerders toegang beperken door twee of meer accounts voor toegang tot nood gevallen in uw Tenant op te slaan.

Met accounts voor toegang in nood gevallen kunnen organisaties bevoegde toegang beperken tot een bestaande Azure Active Directory omgeving. Deze accounts zijn zeer privileged en worden niet toegewezen aan specifieke personen. Accounts voor toegang in nood gevallen zijn beperkt tot nood scenario's waarbij normale beheerders accounts niet kunnen worden gebruikt. Organisaties moeten ervoor zorgen dat het gebruik van het nood-account wordt beperkt tot alleen die tijd waarvoor het nodig is.

Evalueer de accounts die zijn toegewezen of die in aanmerking komen voor de rol van globale beheerder. Als u geen alleen-Cloud accounts hebt weer geven met behulp van het domein *. onmicrosoft.com (bedoeld voor ' Breek glazen ' nood toegang), maakt u deze. Zie [Beheer accounts voor nood toegang beheren in azure AD](directory-emergency-access.md)voor meer informatie.

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>Schakel multi-factor Authentication in en Registreer alle andere niet-productieve, niet-federatieve beheerders accounts voor één gebruiker.

Azure Multi-Factor Authentication (MFA) vereisen bij het aanmelden voor alle afzonderlijke gebruikers die permanent zijn toegewezen aan een of meer van de Azure AD-beheerders rollen: globale beheerder, bevoegde rol beheerder, Exchange Online-beheerder en share point Online beheerder. Gebruik de hand leiding om [multi-factor Authentication (MFA) in te scha kelen voor uw beheerders accounts](../authentication/howto-mfa-userstates.md) en ervoor te zorgen dat alle gebruikers zijn geregistreerd bij [https://aka.ms/mfasetup](https://aka.ms/mfasetup). Meer informatie vindt u in stap 2 en stap 3 van de hand leiding [beveiliging beveiligen tot gegevens en services in Office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e). 

## <a name="stage-2-mitigate-the-most-frequently-used-attack-techniques"></a>Fase 2: de meest gebruikte aanvals technieken beperken

![Fase 2 veelgebruikte aanvallen beperken](./media/directory-admin-roles-secure/stage-two.png)

Fase 2 van het schema is gericht op het beperken van de meest gebruikte aanvals technieken met referentie diefstal en-misbruik en kan in ongeveer 2-4 weken worden geïmplementeerd. Deze fase van de beveiligde route kaart voor verhoogde toegang bevat de volgende acties.

### <a name="general-preparation"></a>Algemene voor bereiding

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>Een inventarisatie uitvoeren van services, eigen aren en beheerders

Met de toename van uw eigen apparaat (BYOD) en het werk van thuis beleid en de groei van draadloze connectiviteit in bedrijven is het essentieel dat u controleert wie verbinding maakt met uw netwerk. Een efficiënte beveiligings controle weerhoudt vaak apparaten, toepassingen en Program ma's die worden uitgevoerd op uw netwerk en die niet worden ondersteund door IT, en kunnen daarom mogelijk niet worden beveiligd. Zie overzicht van Azure- [beveiligings beheer en-controle](../../security/fundamentals/management-monitoring-overview.md)voor meer informatie. Zorg ervoor dat u alle volgende taken opneemt in uw inventaris proces. 

* Identificeer de gebruikers met beheerders rollen en de services die ze kunnen beheren.
* Gebruik Azure AD PIM om erachter te komen welke gebruikers in uw organisatie beheerders toegang hebben tot Azure AD, met inbegrip van aanvullende rollen die worden vermeld in fase 1.
* Naast de functies die zijn gedefinieerd in azure AD, wordt Office 365 geleverd met een set beheerders rollen die u kunt toewijzen aan gebruikers in uw organisatie. Elke beheerdersrol wordt toegewezen aan algemene bedrijfs functies en geeft personen in uw organisatie machtigingen om specifieke taken uit te voeren in het [Microsoft 365-beheer centrum](https://admin.microsoft.com). Gebruik het Microsoft 365-beheer centrum om erachter te komen welke gebruikers in uw organisatie beheerders toegang hebben tot Office 365, met inbegrip van rollen die niet worden beheerd in azure AD. Zie [over office 365-beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) en [aanbevolen beveiligings procedures voor Office 365](https://docs.microsoft.com/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)voor meer informatie.
* Voer de inventarisatie uit in andere services waarvan uw organisatie afhankelijk is, zoals Azure, intune of Dynamics 365.
* Zorg ervoor dat uw beheerders accounts (accounts die worden gebruikt voor beheer doeleinden, niet alleen de dagelijkse accounts van gebruikers) werk hebben gekoppeld aan hun eigen e-mail adressen en zijn geregistreerd voor Azure MFA of gebruikmaken van MFA on-premises.
* Vraag gebruikers om hun zakelijke rechtvaardiging voor beheerders toegang.
* Verwijder beheerders toegang voor de personen en services die deze niet nodig hebben.

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>Micro soft-accounts in beheerders rollen identificeren die moeten worden overgeschakeld naar werk-of school accounts

Soms gebruiken de oorspronkelijke globale beheerders voor een organisatie hun bestaande Microsoft-account referenties opnieuw wanneer ze met Azure AD zijn begonnen. Deze micro soft-accounts moeten worden vervangen door afzonderlijke Cloud-of gesynchroniseerde accounts. 

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>Zorg ervoor dat afzonderlijke gebruikers accounts en e-mail berichten worden doorgestuurd voor globale beheerders accounts 

Globale beheerders accounts mogen geen persoonlijke e-mail adressen hebben, omdat persoonlijke e-mail accounts regel matig worden gephishd door Cyber aanvallers. Om Internet risico's (phishing-aanvallen, onbedoelde websurfen) te scheiden van Administrator bevoegdheden, kunt u speciale accounts maken voor elke gebruiker met beheerders bevoegdheden. 

Als u dit nog niet hebt gedaan, maakt u afzonderlijke accounts voor gebruikers om algemene beheer taken uit te voeren, om ervoor te zorgen dat ze niet per ongeluk e-mail berichten openen of Program ma's uitvoeren die zijn gekoppeld aan hun beheerders accounts. Zorg ervoor dat deze accounts hun e-mail adres hebben doorgestuurd naar een werk postvak.  

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>Controleren of de wacht woorden van beheerders accounts onlangs zijn gewijzigd

Zorg ervoor dat alle gebruikers zijn aangemeld bij hun beheerders accounts en wijzig hun wacht woord ten minste eenmaal in de afgelopen 90 dagen. Zorg er ook voor dat alle gedeelde accounts waarin meerdere gebruikers weten dat het wacht woord onlangs is gewijzigd.

#### <a name="turn-on-password-hash-synchronization"></a>Wachtwoord hash synchronisatie inschakelen

Synchronisatie van wacht woord-hashes is een functie die wordt gebruikt voor het synchroniseren van hashes van wacht woord-hashes van een on-premises Active Directory-exemplaar naar een Azure AD-exemplaar op basis van de Cloud. Zelfs als u besluit Federatie met Active Directory Federation Services (AD FS) of andere id-providers te gebruiken, kunt u optioneel een wachtwoord hash-synchronisatie instellen als back-up voor het geval uw on-premises infra structuur, zoals AD-of ADFS-servers, mislukt of wordt tijdelijk niet beschikbaar. Hierdoor kunnen gebruikers zich aanmelden bij de service met hetzelfde wacht woord dat ze gebruiken om zich aan te melden bij hun on-premises AD-exemplaar. Het is ook mogelijk om identiteits beveiliging te detecteren door de wacht woord-hashes te vergelijken met wacht woorden waarvan bekend is dat ze worden aangetast, als een gebruiker hetzelfde e-mail adres en wacht woord heeft gebruikt voor andere services die niet zijn verbonden met Azure AD.  Zie [wachtwoord hash synchronisatie implementeren met Azure AD Connect Sync](../hybrid/how-to-connect-password-hash-synchronization.md)voor meer informatie.

#### <a name="require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users"></a>Multi-factor Authentication (MFA) vereisen voor gebruikers in alle geprivilegieerde rollen en gebruikers die ze kunnen belichten

Azure AD raadt u aan om multi-factor Authentication (MFA) te vereisen voor al uw gebruikers, inclusief beheerders en alle andere gebruikers die aanzienlijke gevolgen zouden hebben als hun account is aangetast (bijvoorbeeld financiële functionarissen). Dit vermindert het risico van een aanval vanwege een wacht woord dat is aangetast.

Inschakelen:

* [MFA met beleids regels voor voorwaardelijke toegang](../authentication/howto-mfa-getstarted.md) voor alle gebruikers in uw organisatie.

Als u Windows hello voor bedrijven gebruikt, kan aan de MFA-vereiste worden voldaan door gebruik te maken van de aanmeld ervaring van Windows hello. Zie [Windows hello](https://docs.microsoft.com/windows/uwp/security/microsoft-passport)voor meer informatie. 

#### <a name="configure-identity-protection"></a>Identiteits beveiliging configureren 

Azure AD Identity Protection is een op algoritme gebaseerd hulp programma voor bewaking en rapportage dat u kunt gebruiken om mogelijke beveiligings problemen te detecteren die van invloed zijn op de identiteiten van uw organisatie. U kunt automatische reacties op gedetecteerde verdachte activiteiten configureren en de juiste actie ondernemen om ze op te lossen. Zie [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)voor meer informatie.

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>Uw Office 365 Secure score verkrijgen (als u Office 365 gebruikt)

Met beveiligde scores kunt u zien welke Office 365-Services u gebruikt (zoals OneDrive, share point en Exchange) en vervolgens naar uw instellingen en activiteiten en vergelijkt u deze met een basis lijn die is ingesteld door micro soft. U krijgt een score op basis van de beste beveiligings procedures. Iedereen met beheerders machtigingen (globale beheerder of een aangepaste beheerdersrol) voor een Office 365 Business Premium-of ENTER prise-abonnement kan toegang krijgen tot de beveiligde Score op [https://securescore.office.com](https://securescore.office.com/).

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>Raadpleeg de richt lijnen voor beveiliging en naleving van Office 365 (als u Office 365 gebruikt)

Het [plan voor beveiliging en naleving](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57) geeft een overzicht van de manier waarop een Office 365-klant Office 365 moet configureren en andere EMS-mogelijkheden kan gebruiken. Lees vervolgens stap 3-6 van de beveiliging van de [toegang tot gegevens en services in office 365](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e) en de hand leiding voor het [bewaken van de beveiliging en naleving in Office 365](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6).

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>Bewaking van Office 365-activiteiten configureren (als u Office 365 gebruikt)

U kunt bewaken hoe personen in uw organisatie Office 365-Services gebruiken, zodat u gebruikers kunt identificeren die een beheerders account hebben en die mogelijk geen toegang tot Office 365 nodig hebben om zich niet aan te melden bij deze portals. Zie [activiteiten rapporten in het Microsoft 365-beheer centrum](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)voor meer informatie.

#### <a name="establish-incidentemergency-response-plan-owners"></a>Eigen aren van incidenten/nood-antwoord plannen vastleggen

Effectief uitvoeren van incidenten is een complexe onderneming. Daarom is het maken van een geslaagde reactie op incidenten belang rijke planning en bronnen nodig. Het is essentieel dat u voortdurend bewaakt voor Cyber aanvallen en procedures instelt voor het afhandelen van incidenten. Efficiënte methoden voor het verzamelen, analyseren en rapporteren van gegevens zijn essentieel voor het bouwen van relaties en het tot stand brengen van communicatie met andere interne groepen en het plannen van eigen aars. Zie [micro soft Security Response Center](https://technet.microsoft.com/security/dn440717)(Engelstalig) voor meer informatie. 

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>On-premises geprivilegieerde beheerders accounts beveiligen als dat nog niet is gebeurd

Als uw Azure Active Directory-Tenant is gesynchroniseerd met on-premises Active Directory, volgt u de instructies in [beveiligings schema voor bevoegde toegang](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fase 1. Dit omvat het maken van afzonderlijke beheerders accounts voor gebruikers die on-premises beheer taken moeten uitvoeren, werk stations voor bevoegde toegang moeten implementeren voor Active Directory beheerders en unieke lokale beheerders wachtwoorden voor werk stations kunnen maken en Server.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Aanvullende stappen voor organisaties die de toegang tot Azure beheren

#### <a name="complete-an-inventory-of-subscriptions"></a>Een inventarisatie van abonnementen volt ooien

Gebruik de Enter prise Portal en de Azure Portal om de abonnementen in uw organisatie te identificeren die als host optreden voor productie toepassingen.

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>Micro soft-accounts uit beheerders rollen verwijderen

Micro soft-accounts uit andere Program ma's, zoals Xbox, Live en Outlook mogen niet worden gebruikt als beheerders account voor organisatie abonnementen. Verwijder de beheer status van alle micro soft-accounts en vervang door Azure Active Directory (bijvoorbeeld chris@contoso.com) werk-of school accounts.

#### <a name="monitor-azure-activity"></a>Azure-activiteit bewaken

Het Azure-activiteiten logboek bevat een geschiedenis van gebeurtenissen op abonnements niveau in Azure. Het biedt informatie over wie de resources heeft gemaakt, bijgewerkt en verwijderd, en wanneer deze gebeurtenissen zich hebben voorgedaan. Zie [controleren en meldingen ontvangen over belang rijke acties in uw Azure-abonnement](../../azure-monitor/platform/quick-audit-notify-action-subscription.md)voor meer informatie.

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Aanvullende stappen voor organisaties die toegang tot andere Cloud-apps beheren via Azure AD

#### <a name="configure-conditional-access-policies"></a>Beleid voor voorwaardelijke toegang configureren

Bereid het beleid voor voorwaardelijke toegang voor op on-premises en in de Cloud gehoste toepassingen. Als u apparaten aan de werk plek hebt toegevoegd, kunt u meer informatie krijgen [over het instellen van on-premises voorwaardelijke toegang met behulp van Azure Active Directory apparaatregistratie](../active-directory-device-registration-on-premises-setup.md).


## <a name="stage-3-build-visibility-and-take-full-control-of-admin-activity"></a>Fase 3: zicht baarheid opbouwen en volledig beheer over de activiteiten van de beheerder

![Fase 3 beheer activiteiten overnemen](./media/directory-admin-roles-secure/stage-three.png)

Fase 3 bouwt voort op de oplossingen in fase 2 en is ontworpen om te worden geïmplementeerd in ongeveer 1-3 maanden. Deze fase van de beveiligd-privileged Access-schema bevat de volgende onderdelen.

### <a name="general-preparation"></a>Algemene voor bereiding

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>Een toegangs beoordeling van gebruikers in beheerders rollen volt ooien

Meer zakelijke gebruikers krijgen uitgebreide toegang via Cloud Services, wat kan leiden tot een groter niet-beheerd platform. Dit omvat gebruikers die globale beheerders worden voor Office 365, Azure-abonnements beheerders en gebruikers met beheerders toegang tot Vm's of via SaaS-apps. In plaats daarvan moeten organisaties alle werk nemers, met name beheerders, dagelijkse zakelijke trans acties afhandelen als niet-gemachtigde gebruikers en alleen beheerders rechten hebben als dat nodig is. Omdat het aantal gebruikers in beheerders rollen sinds de eerste aanneming mogelijk is geworden, hebben volledige toegangs Beoordelingen voor het identificeren en bevestigen van elke gebruiker die in aanmerking komt voor het activeren van Administrator bevoegdheden. 

Ga als volgt te werk:

* Bepaal welke gebruikers Azure AD-beheerders zijn, schakel just-in-time-beheerders toegang en op rollen gebaseerde beveiligings controles in.
* Converteer gebruikers die geen duidelijke uitschakeling hebben over de toegang met beheerders rechten voor een andere rol (als er geen geschikte rol is, verwijdert u deze).

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>Implementatie van sterkere verificatie voor alle gebruikers voortzetten 

Beheer van C-Suite, managers op hoog niveau, kritiek IT-en beveiligings personeel en andere uiterst belichte gebruikers zijn verplicht om moderne, krachtige verificatie, zoals Azure MFA of Windows Hello, te hebben. 

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>Speciale werk stations gebruiken voor beheer voor Azure AD

Aanvallers kunnen proberen zich te richten op bevoegde accounts om toegang te krijgen tot de gegevens en systemen van een organisatie, zodat ze de integriteit en authenticiteit van gegevens kunnen verstoren door middel van schadelijke code die de programma logica wijzigt of de beheerder die een referentie invoert, ondervindt. Privileged Access workstations (Paw's) bieden een speciaal besturings systeem voor gevoelige taken die worden beschermd tegen aanvallen via internet en bedreigings vectoren. Het scheiden van deze gevoelige taken en accounts van de werk stations en apparaten die dagelijks worden gebruikt, biedt zeer sterke bescherming tegen phishing-aanvallen, problemen met toepassingen en besturings systemen, verschillende imitatie aanvallen en aanvallen met referentie diefstal, zoals toetsaanslag logboek registratie, Pass-the-hash en Pass-the-ticket. Door privileged Access workstations te implementeren, kunt u het risico dat beheerders beheerders referenties invoeren, beperken, behalve op een desktop omgeving die is gehard. Zie [privileged Access workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)(Engelstalig) voor meer informatie.

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>Bestudeer het National Institute of Standards and Technology aanbevelingen voor het afhandelen van incidenten 

Het National Institute of Standards and Technology (NIST) biedt richt lijnen voor de verwerking van incidenten, met name voor het analyseren van gegevens met betrekking tot incidenten en het bepalen van de juiste reactie op elk incident. Zie voor meer informatie [de hand leiding (het NIST) computer security incident handling (SP 800-61, revisie 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf).

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>Privileged Identity Management (PIM) implementeren voor JIT naar extra beheerders rollen

Gebruik [Azure AD privileged Identity Management](../privileged-identity-management/pim-configure.md) mogelijkheid voor Azure Active Directory. Beperkte activering van geprivilegieerde rollen werkt door het volgende in te scha kelen:

* Beheerders bevoegdheden activeren om een specifieke taak uit te voeren
* MFA afdwingen tijdens het activerings proces
* Waarschuwingen gebruiken om beheerders op de hoogte te stellen van out-of-band-wijzigingen
* Gebruikers in staat stellen bepaalde bevoegdheden te behouden voor een vooraf geconfigureerde tijd
* Beveiligings beheerders toestaan alle bevoorrechte identiteiten te detecteren, controle rapporten te bekijken en toegangs beoordelingen te maken voor het identificeren van elke gebruiker die in aanmerking komt voor het activeren van Administrator bevoegdheden

Als u Azure AD Privileged Identity Management al gebruikt, past u de tijds perioden aan voor tijdgebonden bevoegdheden (bijvoorbeeld onderhouds Vensters).

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>De bloot stelling aan aanmeldings protocollen op basis van wacht woorden bepalen (als u Exchange Online gebruikt)

In het verleden wordt aangenomen dat de combi Naties van gebruikers namen en wacht woorden zijn Inge sloten in apparaten, e-mail accounts, telefoons, enzovoort. Maar nu het risico voor Cyber aanvallen in de Cloud, raden we u aan om elke mogelijke gebruiker te identificeren die, als de referenties zijn aangetast, mogelijk onherstelbaar is voor de organisatie en deze niet kan aanmelden bij hun e-mail via gebruikers naam/wacht woord door sterke verificatie vereisten en voorwaardelijke toegang te implementeren. U kunt [verouderde verificatie blok keren met behulp van voorwaardelijke toegang](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication). Raadpleeg de details over [het blok keren van basis verificatie](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online) via Exchange Online. 

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>Beoordeling van functies volt ooien evaluatie voor Office 365-rollen (als u Office 365 gebruikt)

Bepaal of alle beheerders gebruikers de juiste rollen hebben (verwijderen en opnieuw toewijzen volgens deze evaluatie).

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>Bekijk de aanpak voor het beheer van beveiligings incidenten die worden gebruikt in Office 365 en vergelijk met uw eigen organisatie

U kunt dit rapport downloaden vanuit het [beheer van beveiligings incidenten in Microsoft Office 365](https://www.microsoft.com/download/details.aspx?id=54302).

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>Blijf on-premises geprivilegieerde beheerders accounts beveiligen

Als uw Azure Active Directory is verbonden met on-premises Active Directory, volgt u de richt lijnen in de [beveiligings schema voor bevoegde toegang](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access): fase 2. Dit omvat het implementeren van werk stations met verhoogde toegang voor alle beheerders, het vereisen van MFA, het gebruiken van voldoende beheerders voor het onderhoud van domein controllers, het verminderen van de kwets baarheid van domeinen, het implementeren van ATA voor detectie van aanvallen.

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Aanvullende stappen voor organisaties die de toegang tot Azure beheren

#### <a name="establish-integrated-monitoring"></a>Geïntegreerde bewaking tot stand brengen

De [Azure Security Center](../../security-center/security-center-intro.md) biedt geïntegreerde beveiligings bewaking en beleids beheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders niet worden opgemerkt en werkt met een uitgebreid ecosysteem van beveiligings oplossingen.

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>Uw geprivilegieerde accounts inventariseren in gehoste Virtual Machines

In de meeste gevallen hoeft u geen gebruikers onbeperkte machtigingen te geven voor al uw Azure-abonnementen of-resources. U kunt Azure AD-beheerders rollen gebruiken om taken binnen uw organisatie te scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers die specifieke taken moeten uitvoeren. Gebruik bijvoorbeeld Azure AD-beheerders rollen om één beheerder toe te staan om alleen Vm's in een abonnement te beheren, terwijl een andere SQL-data bases binnen hetzelfde abonnement kan beheren. Zie [aan de slag met Access Control op basis van rollen in de Azure Portal](../../role-based-access-control/overview.md)voor meer informatie.

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>PIM implementeren voor Azure AD-beheerders rollen

Gebruik privileged Identity Management met Azure AD-beheerders rollen voor het beheren, beheren en bewaken van de toegang tot Azure-resources. Het gebruik van PIM beschermt geprivilegieerde accounts van Cyber aanvallen door de blootstellings tijd van bevoegdheden te verlagen en uw zicht baarheid te verg Roten met rapporten en waarschuwingen. Zie RBAC-toegang tot [Azure-resources beheren met privileged Identity Management](../../role-based-access-control/pim-azure-resource.md)voor meer informatie.

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>Azure-logboek integratie gebruiken om relevante Azure-logboeken te verzenden naar uw SIEM-systemen 

Met Azure-logboek integratie kunt u onbewerkte logboeken van uw Azure-resources integreren in de bestaande Security Information and Event Management-systemen (SIEM) van uw organisatie. [Azure-logboek integratie](../../security/fundamentals/azure-log-integration-overview.md) verzamelt Windows-gebeurtenissen van Windows Logboeken-Logboeken en Azure-resources uit Azure-activiteiten logboeken, Azure Security Center waarschuwingen en Azure Diagnostische logboeken. 


### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>Aanvullende stappen voor organisaties die toegang tot andere Cloud-apps beheren via Azure AD

#### <a name="implement-user-provisioning-for-connected-apps"></a>Gebruikers inrichten voor verbonden Apps implementeren

Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikers identiteiten in Cloud toepassingen (SaaS), zoals Dropbox, Sales Force, ServiceNow, enzovoort automatiseren. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure AD automatiseren](../app-provisioning/user-provisioning.md)voor meer informatie.

#### <a name="integrate-information-protection"></a>Gegevens beveiliging integreren

Met MCAS kunt u bestanden onderzoeken en beleid instellen op basis van Azure Information Protection classificatie labels, waardoor uw gegevens in de Cloud beter zichtbaar zijn en kunnen worden beheerd. Bestanden in de Cloud scannen en classificeren en Azure Information Protection-labels Toep assen. Zie [Azure Information Protection-integratie](https://docs.microsoft.com/cloud-app-security/azip-integration)voor meer informatie.

#### <a name="configure-conditional-access"></a>Voorwaardelijke toegang configureren

Configureer voorwaardelijke toegang op basis van een groep, locatie en toepassings gevoeligheid voor [SaaS-apps](https://azure.microsoft.com/overview/what-is-saas/) en Azure AD Connected apps. 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>Activiteit in verbonden Cloud-apps bewaken

Om ervoor te zorgen dat de toegang van gebruikers wordt beschermd in verbonden toepassingen, raden we u aan [Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)te gebruiken. Dit beveiligt de bedrijfs toegang tot Cloud-apps, naast het beveiligen van uw beheerders accounts, door het volgende in te scha kelen:

* Zicht baarheid en beheer voor Cloud-apps uitbreiden
* Beleids regels maken voor toegang, activiteiten en het delen van gegevens
* Automatisch Risk ante activiteiten, abnormaal gedrag en bedreigingen identificeren
* Lekkage van gegevens voor komen
* Risico's en automatische bedreigings preventie en beleids afdwinging minimaliseren

De Cloud App Security SIEM-Agent integreert Cloud App Security met uw SIEM-server om gecentraliseerde bewaking van Office 365-waarschuwingen en-activiteiten mogelijk te maken. Het wordt uitgevoerd op uw server en er worden waarschuwingen en activiteiten opgehaald van Cloud App Security en deze worden naar de SIEM-server gestreamd. Zie [Siem-integratie](https://docs.microsoft.com/cloud-app-security/siem)voor meer informatie.

## <a name="stage-4-continue-building-defenses-to-a-more-proactive-security-posture"></a>Fase 4: door gaan met het bouwen van beveiliging in een proactieve beveiligings postuur

![Fase 4 een proactieve beveiligings postuur aannemen](./media/directory-admin-roles-secure/stage-four.png)

Fase 4 van het schema is gebaseerd op de zicht baarheid van fase 3 en is ontworpen om te worden geïmplementeerd in zes maanden en verder. Het volt ooien van een route kaart helpt u bij het ontwikkelen van krachtige privileged Access Protection tegen mogelijke aanvallen die momenteel bekend zijn en beschikbaar zijn. Helaas raden beveiligings bedreigingen voortdurend te werk en schuiven we u aan om de beveiliging te bekijken als een doorlopend proces dat gericht is op het verhogen van de kosten en het verminderen van het slagings percentage van aanvallers gericht op uw omgeving.

Het beveiligen van bevoegde toegang is een kritieke eerste stap bij het instellen van beveiligings garanties voor zakelijke assets in een moderne organisatie, maar dit is niet het enige deel van een volledig beveiligings programma dat elementen zou kunnen bevatten, zoals beleid, bewerkingen, informatie beveiliging, servers, toepassingen, Pc's, apparaten, Cloud infrastructuur en andere onderdelen bieden voortdurende beveiligings garanties. 

Naast het beheren van uw privileged Access-accounts, raden we u aan het volgende voortdurend te controleren:

* Zorg ervoor dat beheerders hun dagelijkse bedrijfs activiteiten doen als onbevoegde gebruikers.
* Ken alleen uitgebreide toegang toe wanneer dit nodig is en verwijder deze achteraf (just-in-time).
* Behoud en controleer de controle activiteit met betrekking tot geprivilegieerde accounts.

Zie voor meer informatie over het bouwen van een volledig beveiligings schema [micro soft Cloud IT-architectuur bronnen](https://docs.microsoft.com/office365/enterprise/microsoft-cloud-it-architecture-resources). Neem contact op met uw micro soft-vertegenwoordiger of Zie [essentiële Cyber-verdediging bouwen om uw bedrijf te beschermen](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)voor meer informatie over het aanbrengen van micro soft-Services om u te helpen bij een van deze onderwerpen.

Deze laatste voortdurende fase van het beveiligde privileged Access-schema bevat de volgende onderdelen.

### <a name="general-preparation"></a>Algemene voor bereiding

#### <a name="review-admin-roles-in-azure-active-directory"></a>Beheerders rollen controleren in Azure Active Directory 

Bepaal of de huidige ingebouwde Azure AD-beheerders rollen nog steeds up-to-date zijn en zorg ervoor dat gebruikers zich alleen in de rollen en delegaties bevinden die ze nodig hebben voor de bijbehorende machtigingen. Met Azure AD kunt u afzonderlijke beheerders aanwijzen om verschillende functies te leveren. Zie [beheerders rollen toewijzen in azure Active Directory](directory-assign-admin-roles.md)voor meer informatie.

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>Gebruikers met een beheer van aan Azure AD gekoppelde apparaten bekijken

Zie [Configure hybrid Azure Active Directory joind devices](../device-management-hybrid-azuread-joined-devices-setup.md)(Engelstalig) voor meer informatie.

#### <a name="review-members-of-built-in-office-365-admin-roleshttpssupportofficecomarticleabout-office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d"></a>Leden van [ingebouwde Office 365-beheerders rollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) controleren
Als u Office 365 gebruikt.
‎
#### <a name="validate-incident-response-plan"></a>Reactie plan voor incidenten valideren

Om uw abonnement te verbeteren, raadt micro soft u aan regel matig te controleren of uw plan op de verwachte wijze werkt:

* Door loop uw bestaande weg kaart om te zien wat er is gemist
* Op basis van de analyse van postmortem kunt u bestaande best practices wijzigen of nieuwe aanbevolen procedures definiëren
* Zorg ervoor dat uw bijgewerkte reactie plan voor incidenten en aanbevolen procedures worden gedistribueerd in uw hele organisatie


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>Aanvullende stappen voor organisaties die de toegang tot Azure beheren 

Bepaal of u [het eigendom van een Azure-abonnement naar een ander account moet overdragen](../../cost-management-billing/manage/billing-subscription-transfer.md).
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>"Glas aflichten": wat u in een nood geval moet doen

![Accounts voor toegang tot nood gevallen](./media/directory-admin-roles-secure/emergency.jpeg)

1. Meld belang rijke managers en beveiligings ambtenaren met relevante informatie over het incident.

2. Bekijk de Playbook van uw aanval. 

3. Meld u aan met de combi natie van gebruikers naam en wacht woord voor afbreek glas om u aan te melden bij Azure AD. 

4. Krijg hulp van micro soft door [een ondersteunings aanvraag voor Azure te openen](../../azure-portal/supportability/how-to-create-azure-support-request.md).

5. Bekijk de [aanmeldings rapporten van Azure AD](../reports-monitoring/overview-reports.md). Er kan een vertraging optreden tussen een gebeurtenis die optreedt en wanneer deze in het rapport wordt opgenomen.

6. Als federatieve en uw AD FS-server niet beschikbaar zijn voor hybride omgevingen, moet u mogelijk tijdelijk overschakelen van Federated Authentication naar het gebruik van wachtwoord-hash-synchronisatie. Hiermee wordt de domein Federatie teruggezet naar beheerde verificatie totdat de AD FS-server beschikbaar is.

7. Controleer e-mail op bevoegde accounts.

8. Zorg ervoor dat u back-ups van relevante logboeken opslaat voor mogelijke forensische en juridisch onderzoek.

Voor meer informatie over hoe Microsoft Office 365 beveiligings incidenten afhandelt, raadpleegt u [Security Incident Management in Microsoft Office 365](https://aka.ms/Office365SIM).

## <a name="faq-common-questions-we-receive-regarding-securing-privileged-access"></a>Veelgestelde vragen: veelgestelde vraag over het beveiligen van bevoegde toegang  

**V:** Wat moet ik doen als ik nog geen beveiligde toegangs onderdelen heb geïmplementeerd?

**Antwoord:** Definieer ten minste twee account voor een afbreek punt, wijs MFA toe aan uw bevoegde beheerders accounts en scheid gebruikers accounts van globale beheerders accounts.

**V:** Wat is het belangrijkste probleem dat eerst moet worden opgelost na een schending?

**Antwoord:** Zorg ervoor dat u de krach tigste verificatie voor Maxi maal beschik bare individuen nodig hebt.

**V:** Wat gebeurt er als onze geprivilegieerde beheerders zijn gedeactiveerd?

**Antwoord:** Maak een globaal beheerders account dat altijd up-to-date blijft.

**V:** Wat gebeurt er als er slechts één globale beheerder is en deze niet kan worden bereikt? 

**Antwoord:** Gebruik een van uw afbreek glazen om direct privileged Access te verkrijgen.

**V:** Hoe kan ik beheerders in mijn organisatie beveiligen?

**Antwoord:** Laat beheerders altijd hun dagelijkse bedrijfs activiteiten doen als standaard gebruikers zonder privileged.

**V:** Wat zijn de aanbevolen procedures voor het maken van beheerders accounts in azure AD?

**Antwoord:** Reserveer uitgebreide toegang voor specifieke beheerders taken.

**V:** Welke hulpprogram ma's bestaan er voor het verminderen van permanente beheerders toegang?

**Antwoord:** Privileged Identity Management (PIM) en Azure AD-beheerders rollen.

**V:** Wat is de positie van micro soft bij het synchroniseren van beheerders accounts met Azure AD?

**Antwoord:** Laag 0-beheerders accounts (inclusief accounts, groepen en andere assets die directe of indirecte administratieve controle hebben over het AD-forest, domeinen of domein controllers en alle assets) worden alleen gebruikt voor on-premises AD-accounts en worden normaal gesp roken niet gesynchroniseerd voor Azure AD voor de Cloud.

**V:** Hoe kunnen beheerders ervoor zorgen dat er geen wille keurige beheerders toegang worden toegewezen in de portal?

**Antwoord:** Gebruik niet-privilegede accounts voor alle gebruikers en de meeste beheerders. Begin met het ontwikkelen van een footprint van de organisatie om te bepalen welke beheerders accounts privileged moeten zijn. En controleren op nieuw gemaakte gebruikers met beheerders rechten.

## <a name="next-steps"></a>Volgende stappen

* [Micro soft vertrouwens centrum voor product beveiliging](https://www.microsoft.com/trustcenter/security) – beveiligings functies van micro soft-Cloud producten en-services

* [Micro soft vertrouwens centrum-naleving](https://www.microsoft.com/trustcenter/compliance/complianceofferings) : de uitgebreide set nalevings aanbiedingen van micro soft voor Cloud Services

* [Richt lijnen voor het uitvoeren van een risico analyse](https://www.microsoft.com/trustcenter/guidance/risk-assessment) -vereisten voor beveiliging en naleving beheren voor micro soft-Cloud Services

### <a name="other-microsoft-online-services"></a>Andere online services van micro soft

* [Microsoft intune beveiliging](https://www.microsoft.com/trustcenter/security/intune-security) : intune biedt mogelijkheden voor Mobile Device Management, Mobile Application Management en PC-beheer vanuit de Cloud.

* [Micro soft dynamics 365 Security](https://www.microsoft.com/trustcenter/security/dynamics365-security) : Dynamics 365 is de micro soft-Cloud oplossing voor het samen delen van CRM-en Enter prise resource planning (ERP)-mogelijkheden.

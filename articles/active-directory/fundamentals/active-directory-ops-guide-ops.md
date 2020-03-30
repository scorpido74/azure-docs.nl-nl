---
title: Naslaginformatie over algemene bewerkingen van Azure Active Directory
description: Deze handleiding voor bewerkingen beschrijft de controles en acties die u moet uitvoeren om algemene bewerkingen te beveiligen
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
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422952"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Naslaginformatie over algemene bewerkingen van Azure Active Directory

In dit gedeelte van de [naslaggids voor Azure AD-bewerkingen](active-directory-ops-guide-intro.md) worden de controles en acties beschreven die u moet uitvoeren om de algemene bewerkingen van Azure Active Directory (Azure AD) te optimaliseren.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de publicatiedatum, maar kunnen in de loop van de tijd veranderen. Organisaties moeten hun operationele praktijken voortdurend evalueren naarmate Microsoft-producten en -services in de loop van de tijd evolueren.

## <a name="key-operational-processes"></a>Belangrijkste operationele processen

### <a name="assign-owners-to-key-tasks"></a>Eigenaren toewijzen aan belangrijke taken

Voor het beheren van Azure Active Directory is de continue uitvoering van belangrijke operationele taken en processen vereist, die mogelijk geen deel uitmaken van een implementatieproject. Het is nog steeds belangrijk dat u deze taken instelt om uw omgeving te optimaliseren. De belangrijkste taken en de aanbevolen eigenaren zijn:

| Taak | Eigenaar |
| :- | :- |
| Verbeteringen op de score voor identiteitsveilige identiteit stimuleren | InfoSec Operations Team |
| Azure AD Connect-servers onderhouden | IAM Operations Team |
| IdFix-rapporten regelmatig uitvoeren en triage | IAM Operations Team |
| Triage Azure AD Connect-statuswaarschuwingen voor synchronisatie en AD FS | IAM Operations Team |
| Als u Azure AD Connect Health niet gebruikt, heeft de klant een gelijkwaardig proces en hulpprogramma's om aangepaste infrastructuur te controleren | IAM Operations Team |
| Als u AD FS niet gebruikt, heeft de klant een gelijkwaardig proces en tools om aangepaste infrastructuur te controleren | IAM Operations Team |
| Hybride logboeken bewaken: Azure AD App Proxy Connectors | IAM Operations Team |
| Hybride logboeken controleren: verificatieagents voor passthrough | IAM Operations Team |
| Hybride logboeken controleren: Password Writeback-service | IAM Operations Team |
| Hybride logboeken controleren: on-premises wachtwoordbeveiligingsgateway | IAM Operations Team |
| Hybride logboeken bewaken: Azure MFA NPS-extensie (indien van toepassing) | IAM Operations Team |

Als u uw lijst bekijkt, moet u mogelijk een eigenaar toewijzen voor taken die een eigenaar missen of het eigendom aanpassen voor taken met eigenaren die niet zijn afgestemd op de bovenstaande aanbevelingen.

#### <a name="owners-recommended-reading"></a>Eigenaren aanbevolen lezen

- [Beheerdersrollen toewijzen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governance in Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Hybride beheer

### <a name="recent-versions-of-on-premises-components"></a>Recente versies van on-premises componenten

Het hebben van de meest up-to-date versies van on-premises componenten biedt de klant de nieuwste beveiligingsupdates, prestatieverbeteringen en functionaliteit die kunnen helpen om de omgeving verder te vereenvoudigen. De meeste componenten hebben een automatische upgrade-instelling, die het upgradeproces automatiseert.

Deze componenten omvatten:

- Azure AD Connect
- Proxyconnectors voor Azure AD-toepassing
- Azure AD Pass-through-verificatieagents
- Azure AD Connect-statusagents

Tenzij er een is vastgesteld, moet u een proces definiëren om deze componenten te upgraden en waar mogelijk afhankelijk te zijn van de automatische upgradefunctie. Als u onderdelen vindt die zes of meer maanden achterlopen, moet u zo snel mogelijk upgraden.

#### <a name="hybrid-management-recommended-reading"></a>Hybride beheer aanbevolen lezen

- [Azure AD Connect: automatische upgrade](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Informatie over Azure AD-toepassingsproxyconnectors | Automatische updates](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Basislijn azure AD Connect-statuswaarschuwing

Organisaties moeten [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) implementeren voor het bewaken en rapporteren van Azure AD Connect en AD FS. Azure AD Connect en AD FS zijn kritieke onderdelen die levenscyclusbeheer en verificatie kunnen doorbreken en dus tot uitval kunnen leiden. Azure AD Connect Health helpt bij het bewaken en verkrijgen van inzicht in uw on-premises identiteitsinfrastructuur, zodat de betrouwbaarheid van uw omgeving wordt gewaarborgd.

![Azure AD Connect Heath-architectuur](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Als u de gezondheid van uw omgeving controleert, moet u onmiddellijk alle waarschuwingen met hoge ernst aanpakken, gevolgd door waarschuwingen met een lagere ernst.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Aanbevolen lezen van Azure AD Connect Health

- [De Azure AD Connect Health-agent installeren](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>On-premises agenten logboeken

Voor sommige services voor identiteits- en toegangsbeheer zijn on-premises agents vereist om hybride scenario's mogelijk te maken. Voorbeelden hiervan zijn wachtwoordreset, pass-through authentication (PTA), Azure AD Application Proxy en Azure MFA NPS-extensie. Het is essentieel dat het operations team baseline en de status van deze componenten te controleren door het archiveren en analyseren van de component agent logs met behulp van oplossingen zoals System Center Operations Manager of SIEM. Het is net zo belangrijk dat uw Infosec Operations-team of helpdesk begrijpt hoe u fouten oplossen.

#### <a name="on-premises-agents-logs-recommended-reading"></a>On-premises agenten logs aanbevolen lezen

- [Problemen met toepassingsproxy oplossen](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Probleemoplossing voor het opnieuw instellen van het wachtwoord van selfservice: Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Informatie over Azure AD-toepassingsproxyconnectors](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: problemen met pass-through-verificatie oplossen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Foutcodes voor de Azure MFA NPS-extensie oplossen](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Beheer van on-premises agenten

Het aannemen van best practices kan helpen bij de optimale werking van on-premises agenten. Houd rekening met de volgende aanbevolen procedures:

- Meerdere Azure AD-toepassingsproxyconnectors per connectorgroep worden aanbevolen om een naadloze taakverdeling en hoge beschikbaarheid te bieden door te voorkomen dat er één storingspunt optreedt bij de toegang tot de proxytoepassingen. Als u momenteel slechts één connector in een connectorgroep hebt die toepassingen in productie verwerkt, moet u ten minste twee connectors voor redundantie implementeren.
- Het maken en gebruiken van een proxyconnectorgroep voor het opsporen van debuggen kan handig zijn voor het oplossen van probleemscenario's en voor het ontsteken van nieuwe on-premises toepassingen. We raden ook aan om netwerktools zoals Message Analyzer en Fiddler in de connectormachines te installeren.
- Meerdere pass-through verificatieagents worden aanbevolen om naadloze load balancing en hoge beschikbaarheid te bieden door single point of failure tijdens de verificatiestroom te vermijden. Zorg ervoor dat u ten minste twee verificatieagents voor omleidingsverificatie implementeert voor redundantie.

#### <a name="on-premises-agents-management-recommended-reading"></a>On-premises agenten management aanbevolen lezen

- [Informatie over Azure AD-toepassingsproxyconnectors](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD Pass-through-verificatie - snel aan de slag](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Management op schaal

### <a name="identity-secure-score"></a>Identity Secure Score

De [identity secure score](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) biedt een kwantificeerbare maatstaf voor de beveiligingshouding van uw organisatie. Het is essentieel om voortdurend te controleren en de bevindingen te behandelen gerapporteerd en streven naar de hoogst mogelijke score. De score helpt bij het volgende:

- Objectief meten van het beveiligingspostuur van uw identiteit
- Plannen van verbeteringen aan de identiteitsbeveiliging
- Evalueren van het succes van uw verbeteringen

![Beveiligingsscore](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Als uw organisatie momenteel geen programma heeft om wijzigingen in identity secure score te controleren, wordt u aangeraden een plan te implementeren en eigenaren toe te wijzen om verbeteringsacties te controleren en te stimuleren. Organisaties moeten verbeteracties met een scoreimpact hoger dan 30 zo snel mogelijk herstellen.

### <a name="notifications"></a>Meldingen

Microsoft stuurt e-mailcommunicatie naar beheerders om verschillende wijzigingen in de service, benodigde configuratie-updates en fouten die beheerdersmoeten ingrijpen, op de hoogte te stellen. Het is belangrijk dat klanten de e-mailadressen van de melding zo instellen dat meldingen worden verzonden naar de juiste teamleden die alle meldingen kunnen bevestigen en reageren. We raden u aan meerdere geadresseerden toe te voegen aan het [Office 365 Message Center](https://docs.microsoft.com/office365/admin/manage/message-center) en te verzoeken dat meldingen (inclusief Azure AD Connect Health-meldingen) naar een distributielijst of gedeeld postvak worden verzonden. Als u slechts één globaal beheerdersaccount met een e-mailadres hebt, moet u ten minste twee accounts met e-mailgeschikt configureren.

Er zijn twee 'From'-adressen die <o365mc@email2.microsoft.com>worden gebruikt door Azure AD: , die Office 365 Message Center-meldingen verzendt; en <azure-noreply@microsoft.com>, die kennisgevingen stuurt met betrekking tot:

- [Azure AD Access-recensies](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health (Engelstalig)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Certificaatmeldingen voor het verlopen van bedrijfsapps](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Meldingen van enterprise-appprovisioningsservice

Raadpleeg de volgende tabel om te leren welk type meldingen wordt verzonden en waar u deze controleren:

| Meldingsbron | Wat wordt verzonden | Waar te controleren |
|:-|:-|:-|
| Technisch contact | Synchronisatiefouten | Azure-portal - eigenschappenblad |
| Berichtencentrum van Office 365 | Incident- en degradatiemeldingen van Identity Services en O365 backendservices | Office-portal |
| Wekelijkse samenvatting van identiteitsbescherming | Digest voor identiteitsbescherming | Azure AD-identiteitsbeveiliging-blad |
| Azure AD Connect Health (Engelstalig) | Waarschuwingsmeldingen | Azure-portal - Azure AD Connect-statusblad |
| Meldingen voor bedrijfstoepassingen | Meldingen wanneer certificaten op het punt staan te verlopen en inprovisioneringsfouten | Azure-portal - Enterprise Application blade (elke app heeft zijn eigen e-mailadres instelling) |

#### <a name="notifications-recommended-reading"></a>Meldingen aanbevolen lezen

- [Het adres, de technische contactpersoon en meer van uw organisatie wijzigen - Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Operationeel oppervlak

### <a name="ad-fs-lockdown"></a>AD FS-afsluiting

Organisaties die toepassingen configureren om rechtstreeks te verifiëren voor Azure AD profiteren van [azure AD smart lockout](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Als u AD FS gebruikt in Windows Server 2012 R2, implementeert u AD FS [extranetuitsluitingsbeveiliging](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Als u AD FS op Windows Server 2016 of hoger gebruikt, implementeert u [extranet smart lockout](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). We raden u ten minste aan extranetlock-out in te schakelen om het risico van brute force-aanvallen tegen on-premises Active Directory te beperken. Als u echter AD FS in Windows 2016 of hoger hebt, moet u ook extranetsmart lockout inschakelen die zal helpen om [aanvallen met wachtwoordspray](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) te beperken.

Als AD FS alleen wordt gebruikt voor Azure AD-federatie, zijn er enkele eindpunten die kunnen worden uitgeschakeld om het oppervlak van de aanval te minimaliseren. Als AD FS bijvoorbeeld alleen wordt gebruikt voor Azure AD, moet u ws-vertrouwenseindpunten uitschakelen, anders dan de eindpunten die zijn ingeschakeld voor **gebruikersnaamgemengd** en **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Toegang tot machines met on-premises identiteitscomponenten

Organisaties moeten de toegang tot de machines met on-premises hybride componenten op dezelfde manier vergrendelen als uw on-premises domein. Een back-upoperator of Hyper-V-beheerder mag zich bijvoorbeeld niet kunnen aanmelden bij de Azure AD Connect Server om regels te wijzigen.

Het Active Directory-beheerlaagmodel is ontworpen om identiteitssystemen te beschermen met behulp van een set bufferzones tussen volledige controle over de omgeving (Tier 0) en de risicovolle werkstationelementen die aanvallers vaak compromitteren. ![Diagram van de drie lagen van het lagenmodel](./media/active-directory-ops-guide/active-directory-ops-img18.png)

Het [niveaumodel](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) bestaat uit drie niveaus en bevat alleen administratieve accounts, geen standaardgebruikersaccounts.

- **Niveau 0** - Directe controle van bedrijfsidentiteiten in de omgeving. Laag 0 bevat accounts, groepen en andere activa die directe of indirecte controle hebben over het beheer van Active Directory-forest, -domeinen of -domeincontrollers en alle activa daarvan. De beveiliging van alle activa op laag 0 is even gevoelig omdat ze elkaar in stand houden.
- **Tier 1** - Controle van bedrijfsservers en -toepassingen. De activa op laag 1 zijn serverbesturingssystemen, cloudservices en bedrijfstoepassingen. De administratoraccounts op laag 1 hebben controle over het beheer van een groot deel van de bedrijfswaarde die op deze activa wordt gehost. Een veelgebruikt voorbeeld is de rol van de serverbeheerders. Zij onderhouden deze besturingssystemen en kunnen invloed uitoefenen op alle bedrijfsservices.
- **Tier 2** - Controle van werkstations en apparaten van gebruikers. De administratoraccounts op laag 2 hebben controle over het beheer van een groot deel van de bedrijfswaarde die op werkstations en apparaten van gebruikers wordt gehost. Voorbeelden zijn de medewerkers van de helpdesk en computerondersteuning. Zij hebben immers invloed op de integriteit van nagenoeg alle gebruikersgegevens.

Sluit de toegang tot on-premises identiteitscomponenten zoals Azure AD Connect, AD FS en SQL-services op dezelfde manier af als voor domeincontrollers.

## <a name="summary"></a>Samenvatting

Er zijn zeven aspecten aan een veilige infrastructuur van de Identiteit. Met deze lijst u de acties vinden die u moet uitvoeren om de bewerkingen voor Azure Active Directory (Azure AD) te optimaliseren.

- Eigenaren toewijzen aan belangrijke taken.
- Automatiseer het upgradeproces voor on-premises hybride componenten.
- Azure AD Connect-status implementeren voor het bewaken en rapporteren van Azure AD Connect en AD FS.
- Controleer de status van on-premises hybride componenten door de logboeken van de componentagent te archiveren en te analyseren met Behulp van System Center Operations Manager of een SIEM-oplossing.
- Implementeer beveiligingsverbeteringen door uw beveiligingshouding te meten met Identity Secure Score.
- Sluit AD FS af.
- Sluit de toegang tot machines met on-premises identiteitscomponenten af.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Azure AD-implementatieplannen](active-directory-deployment-plans.md) voor implementatiedetails over eventuele mogelijkheden die u nog niet hebt geïmplementeerd.

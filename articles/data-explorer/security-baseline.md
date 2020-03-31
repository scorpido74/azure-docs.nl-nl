---
title: Azure-beveiligingsbasislijn voor Gegevensverkenner
description: Azure-beveiligingsbasislijn voor Gegevensverkenner
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289717"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Azure-beveiligingsbasislijn voor Gegevensverkenner

De Azure Security Baseline for Data Explorer bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze service is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen**: Azure Data Explorer ondersteunt het implementeren van een cluster in een subnet in uw virtuele netwerk. Met deze mogelijkheid u nsg-regels (Network Security Group) afdwingen voor uw Azure Data Explorer-clusterverkeer, uw on-premises netwerk verbinden met het subnet van azure Data Explorer-cluster en uw gegevensverbindingsbronnen (Gebeurtenishub en gebeurtenisraster) beveiligen met serviceeindpunten.

Uw Azure Data Explorer-cluster implementeren in een virtueel netwerk:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NICS controleren en registreren

**Richtlijnen**: NsG-stroomlogboeken (Network Security Group) inschakelen en logboeken naar een opslagaccount verzenden voor verkeerscontrole.

NSG-stroomlogboeken inschakelen:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Inzicht in netwerkbeveiliging van Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beschermen

**Richtsnoeren**: Niet van toepassing; Aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie weigeren met bekende kwaadaardige IP-adressen

**Richtlijnen:** Schakel Azure DDoS Protection Standard in op het virtuele netwerk dat uw Data Explorer-clusters beschermt voor bescherming tegen DDoS-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.

DDoS-beveiliging configureren:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Begrijp azure security center integrated threat intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen:** Schakel stroomlogboeken in op de netwerkbeveiligingsgroepen (NSG) die worden gebruikt om uw Azure Data Explorer-cluster te beschermen en stuur logboeken naar een opslagaccount voor verkeerscontrole.

NSG-stroomlogboeken inschakelen:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen implementeren

**Richtsnoeren**: Niet van toepassing; Dit besturingselement gebeurt op endpoint of firewall.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Verkeer naar uw webtoepassingen beheren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen:** Gebruik Virtual Network Service Tags om netwerktoegangsbesturingselementen te definiëren voor netwerkbeveiligingsgroepen of Azure Firewalls die zijn gekoppeld aan uw Azure Data Explorer-clusters. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of bestemmingsveld van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

Servicetags begrijpen en gebruiken:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Configuratievereisten voor servicetags voor Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** Klant voor het definiëren en implementeren van standaardbeveiligingsconfiguraties voor netwerkbronnen met Azure Policy.

De klant kan azure-blauwdrukken ook gebruiken om azure-implementaties op grote schaal te vereenvoudigen door belangrijke omgevingsartefacten, zoals ARM-sjablonen, RBAC-besturingselementen en beleidsregels, in één blauwdrukdefinitie te verpakken. Pas de blauwdruk eenvoudig toe op nieuwe abonnementen en omgevingen en stem de besturing en het beheer af via versiebeheer.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een Azure Blueprint maken:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor documentverkeer

**Richtlijnen:** Gebruik tags voor netwerkbeveiligingsgroepen (NSG) en andere bronnen met betrekking tot netwerkbeveiliging en verkeersstroom voor uw Data Explorer-clusters. Voor afzonderlijke NSG-regels gebruikt u het veld 'Beschrijving' om de bedrijfsbehoefte en/of -duur (enz.) op te geven voor regels die verkeer van/naar een netwerk toestaan.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkbronconfiguraties te controleren en wijzigingen te detecteren

**Richtlijnen:** Gebruik Azure Policy om configuratie voor netwerkbronnen te valideren (en/of te herstellen).

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Beheer goedgekeurde tijdsynchronisatiebronnen

**Richtlijnen**: Microsoft onderhoudt tijdbronnen voor Azure-resources, klanten kunnen tijdsynchronisatie bijwerken voor compute-implementaties die eigendom zijn van de klant.

Tijdsynchronisatie configureren voor Azure-rekenbronnen:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Beheer van centrale beveiligingslogboeken configureren

**Richtlijnen**: Azure Data Explorer gebruikt diagnostische logboeken voor inzichten over innamesuccessen en -fouten. U bedrijfslogboeken exporteren naar Azure Storage, Event Hub of Log Analytics om de opnamestatus te controleren.

Hoe u de opnamebewerkingen van Azure Data Explorer controleren:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Gegevens over het innemen en querybewaking in Azure Data Explorer:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure Resources

**Richtlijnen**: Schakel diagnostische instellingen voor Azure Data Explorer in voor toegang en logboekregistratie voor servicespecifieke bewerkingen en logboekregistratie. Azure Activity-logboeken binnen Azure Monitor, die logboekregistratie op hoog niveau over de bron bevatten, zijn standaard ingeschakeld.

Hoe u de opnamebewerkingen van Azure Data Explorer controleren:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Platformlogboeken en -statistieken verzamelen met Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Overzicht van Azure-platformlogboeken:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Beveiligingslogboeken verzamelen van het besturingssysteem

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen:** Stel binnen Azure Monitor de bewaartermijn van uw Log Analytics Workspace in volgens de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage Accounts voor opslag op lange termijn/archiverings.

Logboekretentieparameters instellen voor Logboekanalysewerkruimten:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen**: Analyseer en monitor logboeken op afwijkend gedrag en bekijk regelmatig resultaten. Nadat u diagnostische instellingen voor Azure Data Explorer hebt ingeschakeld, gebruikt u de Logboekanalysewerkruimte van Azure Monitor om logboeken te controleren en query's uit te voeren op logboekgegevens.

Inzicht in log Analytics Workspace:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtsnoeren**: Niet van toepassing; Azure Data Explorer heeft deze mogelijkheid niet.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtsnoeren**: Niet van toepassing; Azure Data Explorer verwerkt geen anti-malware logboekregistratie.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtlijnen:** niet van toepassing: DNS-query is geen functie van Azure Data Explorer.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Beveiligingsbeheer: Identiteits- en toegangscontrole](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van administratieve rekeningen

**Richtlijnen:** In Azure Data Explorer definiëren beveiligingsrollen welke beveiligingsprincipals (gebruikers en toepassingen) machtigingen hebben om te werken op een beveiligde bron, zoals een database of een tabel, en welke bewerkingen zijn toegestaan.  U Kusto-query gebruiken om beginselen op te sommen in de beheerdersrol voor de Azure Data Explorer-clusters en -databases.
Beheer van beveiligingsrollen in Azure Data Explorer met Kusto-query:https://docs.microsoft.com/azure/kusto/management/security-roles



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

**Richtlijnen**: Azure AD heeft niet het concept van standaardwachtwoorden. Andere Azure-bronnen waarvoor een wachtwoord vereist is, dwingt een wachtwoord te maken met complexiteitsvereisten en een minimale wachtwoordlengte, die verschilt afhankelijk van de service. U bent verantwoordelijk voor toepassingen van derden en marktplaatsservices die standaardwachtwoorden kunnen gebruiken.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Zorgen voor het gebruik van speciale administratieve rekeningen

**Richtlijnen**: Klant maakt standaard operationele procedures rond het gebruik van speciale administratieve accounts. Gebruik Azure Security Center Identity and Access Management om het aantal beheerdersaccounts te controleren.

Klanten kunnen ook een Just-In-Time / Just-Enough-Access inschakelen met Azure AD Privileged Identity Management Privileged Roles voor Microsoft Services en Azure ARM. 

Wat is Azure AD Privileged Identity Management?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik single sign-on (SSO) met Azure Active Directory

**Richtlijnen:** Waar mogelijk kan de klant SSO gebruiken met Azure Active Directory (Azure AD) in plaats van individuele zelfstandige referenties per service te configureren. Gebruik aanbevelingen voor azure security center-identiteits- en toegangsbeheer.

SSO begrijpen met Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Gebruik Multifactor-verificatie voor alle Azure Active Directory-gebaseerde toegang.

**Richtlijnen:** Schakel Azure Active Directory (Azure AD) multi-factor authentication (MFA) in en volg aanbevelingen voor Azure Security Center Identity and Access Management.

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Identiteit en toegang bewaken binnen Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Gebruik van speciale machines (geprivilegieerde werkstations) voor alle administratieve taken

**Richtlijnen:** Gebruik PAWs (gedrenreiënte stations) met multi-factor authenticatie (MFA) geconfigureerd om in te loggen en Azure-bronnen te configureren.

Meer informatie over geprivilegieerde werkstations voor toegang:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten op administratieve accounts

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) beveiligingsrapporten voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden. Azure Security Center gebruiken om identiteits- en toegangsactiviteiten te controleren

Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Hoe u de identiteit van gebruikers controleren en activiteiten openen in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Azure Resource beheren vanaf alleen goedgekeurde locaties

**Richtlijnen**: De klant gebruikt locaties met voorwaardelijke toegang om alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's toe te staan.

Benoemde locaties configureren in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-utilize-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen**: Azure Active Directory (Azure AD) is de voorkeursmethode voor het verifiëren naar Azure Data Explorer. Het ondersteunt een aantal verificatiescenario's:

Gebruikersverificatie (interactieve aanmelding): wordt gebruikt om menselijke principals te verifiëren.

Toepassingsverificatie (niet-interactieve aanmelding): wordt gebruikt om services en toepassingen te verifiëren die moeten worden uitgevoerd/geverifieerd zonder dat er menselijke gebruikers aanwezig zijn.

Overzicht van toegangsbeheer van Azure Data Explorer:https://docs.microsoft.com/azure/kusto/management/access-control

Authenticeren met Azure Active Directory:https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Gebruikerstoegang regelmatig bekijken en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te ontdekken. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. De toegang van de gebruiker kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

How-To Authenticate met Azure AD voor Azure Data Explorer Access:https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Azure AD-rapportage:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure Identity Access-recensies gebruiken:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen:** U Azure Active Directory (Azure AD) Aanmelden voor logboeken activiteit, audit en risicogebeurtenis gebruiken voor monitoring waarmee u integreren met een SIEM -hulpprogramma voor beveiligingsgegevens en gebeurtenisbeheer (Azure AD).

 U dit proces stroomlijnen door diagnostische instellingen voor Azure Active Directory-gebruikersaccounts te maken, de controlelogboeken en aanmeldingslogboeken naar een Logboekanalysewerkruimte te verzenden. Klant om gewenste waarschuwingen te configureren binnen Log Analytics Workspace.

Azure-activiteitslogboeken integreren in Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing bij afwijking van het inloggedrag van uw account

**Richtlijnen:** Gebruik de functie Risicodetecties en identiteitsbeveiliging van Azure Active Directory (Azure AD) om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Bovendien u gegevens opnemen in Azure Sentinel voor verder onderzoek.

Azure AD-riskante aanmeldingen weergeven:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Hoe u risicobeleid voor identiteitsbescherming configureert en inschakelt:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** In ondersteuningsscenario's waarin Microsoft toegang nodig heeft tot klantgegevens, biedt Customer Lockbox een interface voor klanten om verzoeken om toegang tot klantgegevens te controleren en goed te keuren of af te wijzen.

Inzicht in klantlockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Beveiligingsbeheer: Gegevensbescherming](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

**Richtlijnen:** Gebruik tags om te helpen bij het bijhouden van Azure-bronnen die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen**: Implementeer afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Azure Data Explorer-clusters moeten worden gescheiden van andere bronnen door virtueel netwerk/subnet, op de juiste manier worden getagd en beveiligd binnen een NETWERKbeveiligingsgroep (NSG) of Azure Firewall. Clusters van Gegevensverkenners die gevoelige gegevens opslaan of verwerken, moeten voldoende geïsoleerd zijn.

Extra Azure-abonnementen maken:https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Uw Azure Data Explorer-cluster implementeren in een virtueel netwerk:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Een NSG maken met een Beveiligingsconfig:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Ongeautoriseerde overdracht van gevoelige informatie controleren en blokkeren

**Richtsnoeren**: Niet van toepassing; Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen**: Azure Data Explorer-cluster onderhandelt standaard over TLS 1.2. Zorg ervoor dat clients die verbinding maken met uw Azure-bronnen, kunnen onderhandelen over TLS 1.2 of hoger.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>Een hulpprogramma voor actieve detectie gebruiken om gevoelige gegevens te identificeren</div>

**Richtlijnen**: Functies voor gegevensidentificatie, classificatie en verliespreventie zijn nog niet beschikbaar voor Azure Data Explorer. Implementeer oplossingen van derden indien nodig voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Azure RBAC gebruiken om de toegang tot bronnen te beheren</div>

**Richtlijnen:** met Azure Data Explorer u de toegang tot databases en tabellen beheren met behulp van een RBAC-model (Role-based Access Control). Onder dit model worden principals (gebruikers, groepen en apps) toegewezen aan rollen. Opdrachtgevers hebben toegang tot bronnen op basis van de toegewezen rollen.

Lijst met rollen en machtigingen en instructies voor het configureren van RBAC voor Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik host-based Data Loss Prevention om toegangscontrole af te dwingen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

Microsoft beheert de onderliggende infrastructuur voor Azure Data Explorer en heeft strenge controles geïmplementeerd om verlies of blootstelling van klantgegevens te voorkomen.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen:** Azure Disk Encryption helpt uw gegevens te beschermen en te beschermen om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Het biedt volumeversleuteling voor het besturingssysteem en gegevensschijven van uw virtuele clustermachines. Het integreert ook met Azure Key Vault, waarmee we de schijfversleutelingssleutels en -geheimen kunnen beheren en ervoor kunnen zorgen dat alle gegevens op de VM-schijven in rust worden versleuteld terwijl ze in Azure Storage zijn.

Versleuteling in rust inschakelen voor Azure Data Explorer-clusters:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen:** Gebruik Azure Monitor met het Azure Activity Log om waarschuwingen te maken voor wanneer wijzigingen op resourceniveau plaatsvinden in uw Azure Data Explorer-clusters.

Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtlijnen:** Volg aanbevelingen van Azure Security Center voor het beveiligen van uw Azure Data Explorer-bronnen.

Microsoft voert ook kwetsbaarheidsbeheer uit op de onderliggende systemen die Azure Data Explorer ondersteunen.

Informatie over aanbevelingen van Azure Security Center:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Oplossing voor het patchbeheer van geautomatiseerd besturingssysteem implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Software patchmanagementoplossing van derden implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk Back-to-back Vulnerability Scans

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden.

**Richtlijnen:** gebruik de standaardrisicoclassificaties (Secure Score) van Azure Security Center.
Informatie over azure security center secure score:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik Azure Resource Graph om alle bronnen (zoals compute, storage, netwerk, poorten en protocollen, enz.) binnen uw abonnement(en) op te vragen/te ontdekken. Zorg voor de juiste (lees)machtigingen in uw tenant en opsommen van alle Azure-abonnementen en resources binnen uw abonnementen.

Hoewel klassieke Azure-bronnen kunnen worden ontdekt via Resource Graph, is het ten zeerste aan te raden om azure resource manager-resources in de toekomst te maken en te gebruiken.

Query's maken met Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weergeven:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC begrijpen:https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-bronnen met metagegevens om ze logisch te ordenen in een taxonomie.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ongeautoriseerde Azure-bronnen verwijderen

**Richtlijnen**: U geschikte naamgevingsconventies, tags, beheergroepen of afzonderlijke abonnementen gebruiken, waar nodig, om assets te organiseren en bij te houden. U Azure Resource Graph gebruiken om voorraad regelmatig te verzoenen en ervoor te zorgen dat ongeautoriseerde bronnen tijdig uit het abonnement worden verwijderd. 

Extra Azure-abonnementen maken:https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Query's maken met Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Voorraad bijhouden van goedgekeurde Azure-resources en softwaretitels.

**Richtlijnen:** U moet een voorraad van goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen maken volgens uw organisatiebehoeften.  


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor voor niet-goedgekeurde Azure-bronnen

**Richtlijnen:** U Azure-beleid gebruiken om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

    - Niet toegestane brontypen

    - Toegestane brontypen

U de door het beleid gegenereerde gebeurtenissen volgen met behulp van de 

Activiteitslogboeken die kunnen worden gecontroleerd met Azure Monitor.

Bovendien u de Azure Resource Graph gebruiken om bronnen binnen het abonnement(en) op te vragen/te ontdekken.

Zelfstudie: Beleid maken en beheren om naleving af te dwingen:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Snelstart: Voer uw eerste Resourcegraph-query uit met Azure Resource Graph Explorer:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Waarschuwingen voor activiteitenlogboeken maken, weergeven en beheren met Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor voor niet-goedgekeurde softwaretoepassingen binnen Compute Resources

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources en Azure als geheel.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="68-utilize-only-approved-applications"></a>6.8: Alleen goedgekeurde aanvragen gebruiken

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="69-utilize-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** U Azure-beleid gebruiken om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

    - Niet toegestane brontypen

    - Toegestane brontypen

Zelfstudie: Beleid maken en beheren om naleving af te dwingen:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure-beleidsvoorbeelden:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: De mogelijkheid van gebruikers beperken om via scripts met Azure Resources Manager te communiceren

**Richtlijnen:** Gebruik de voorwaardelijke toegang van Azure om de interactie van gebruikers met Azure Resource Manager te beperken door 'Bloktoegang' voor de Microsoft Azure Management-app te configureren. Dit voorkomt het maken en wijzigen van resources binnen uw Azure-abonnementen. 

Beheer de toegang tot Azure-beheer met voorwaardelijke toegang:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Gebruikers de mogelijkheid beperken om scripts uit te voeren binnen compute resources

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Beveiligingsbeheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Beveiligde configuraties instellen voor alle Azure-bronnen

**Richtlijnen:** Gebruik Azure Policy-aliassen om aangepaste beleidsregels te maken om de configuratie van uw Azure-resources te controleren of af te dwingen. U ook ingebouwde Azure-beleidsdefinities gebruiken.

Azure Resource Manager heeft ook de mogelijkheid om de sjabloon te exporteren in JavaScript Object Notatie (JSON), die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan / hoger zijn dan de beveiligingsvereisten voor uw organisatie.

U ook aanbevelingen van Azure Security Center gebruiken als een veilige configuratiebasislijn voor uw Azure-bronnen.

Beschikbare Azure-beleidsaliassen weergeven:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Zelfstudie: Beleid maken en beheren om naleving af te dwingen:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Export met één en meerdere resources naar een sjabloon in Azure-portal:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Beveiligingsaanbevelingen - een naslaggids:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Veilige configuraties instellen voor uw besturingssysteem

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Veilige configuraties behouden voor alle Azure-bronnen

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen voor uw Azure-bronnen af te dwingen.  U oplossingen zoals Change Tracking, Policy compliance dashboard of een aangepaste oplossing gebruiken om eenvoudig beveiligingswijzigingen in uw omgeving te identificeren.

Inzicht in Azure-beleidseffecten:https://docs.microsoft.com/azure/governance/policy/concepts/effects

Beleid maken en beheren om naleving af te dwingen:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Bijhouden van wijzigingen in uw omgeving met de oplossing Voor het bijhouden van wijzigingen:https://docs.microsoft.com/azure/automation/change-tracking

Ontvang nalevingsgegevens van Azure-bronnen:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Veilige configuraties voor besturingssystemen behouden

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen**: Gebruik Azure Repos om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleidsregels, Azure Resource Manager-sjablonen, gewenste statusconfiguratiescripts enz. Als u toegang wilt krijgen tot de resources die u beheert in Azure DevOps, u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligingsgroepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als deze zijn geïntegreerd met TFS.

Code opslaan in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Informatie over machtigingen en groepen in Azure DevOps:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen**: Standaardbeveiligingsconfiguraties voor Azure-resources definiëren en implementeren met Azure Policy. Gebruik Azure Policy-aliassen om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure-bronnen te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities met betrekking tot uw specifieke resources.  Bovendien u Azure Automation gebruiken om configuratiewijzigingen te implementeren.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Aliassen gebruiken:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Hulpprogramma's voor systeemconfiguratiebeheer implementeren voor besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure Services

**Richtlijnen:** Gebruik Azure Policy-aliassen om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Gebruik Azure-beleid [audit], [weigeren] en [implementeren als deze niet bestaan] om configuraties voor uw Azure-resources automatisch af te dwingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="711-securely-manage-azure-secrets"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Azure Disk Encryption biedt volumeversleuteling voor de os- en gegevensschijven van uw azure Data Explorer-clustervirtuelemachines. Het integreert ook met Azure Key Vault waarmee u de schijfversleutelingssleutels en -geheimen beheren en ervoor zorgen dat alle gegevens op de VM-schijven in rust worden versleuteld terwijl u in Azure Storage bent.

Uw cluster beveiligen in Azure Data Explorer:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen**: Gebruik Beheerde identiteiten om Azure-services een automatisch beheerde identiteit te bieden in Azure AD. Met Managed Identities u zich verifiëren voor elke service die Azure AD-verificatie ondersteunt, inclusief Key Vault, zonder referenties in uw code. Beheerde identiteiten configureren:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Beheerde identiteiten configureren voor uw Azure Data Explorer-cluster:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling aan referenties elimineren

**Richtlijnen**: Implementeer credential scanner om referenties binnen code te identificeren. Credential Scanner stimuleert ook het verplaatsen van ontdekte referenties naar veiligere locaties zoals Azure Key Vault. 

Credential Scanner instellen:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: Gebruik centraal beheerde anti-malware software

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Data Explorer), maar wordt niet uitgevoerd op inhoud van klanten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-resources

**Richtlijnen**: Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Data Explorer), maar wordt niet uitgevoerd op inhoud van klanten.

Scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-bronnen, zoals Azure Data Explorer, Data Lake Storage, Blob Storage, Azure Database voor PostgreSQL, enz. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt, maar wordt niet uitgevoerd op inhoud van klanten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorgen voor regelmatige geautomatiseerde back-ups

**Richtlijnen:** De gegevens in uw Microsoft Azure-opslagaccount die worden gebruikt door uw Data Explorer-cluster, worden altijd gerepliceerd om duurzaamheid en hoge beschikbaarheid te garanderen. Azure Storage kopieert uw gegevens zodat deze worden beschermd tegen geplande en ongeplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk- of stroomuitval en enorme natuurrampen. U ervoor kiezen om uw gegevens te repliceren binnen hetzelfde datacenter, in zonale datacenters binnen dezelfde regio of in geografisch gescheiden regio's.

Inzicht in redundantie en serviceniveauovereenkomsten voor Azure Storage:https://docs.microsoft.com/azure/storage/common/storage-redundancy

Gegevens exporteren naar opslag :https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en back-upmaken van beheerde sleutels van de klant

**Richtlijnen:** Azure Data Explorer versleutelt alle gegevens in een opslagaccount in rust. Standaard worden gegevens versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels u door de klant beheerde sleutels leveren die u gebruiken voor gegevensversleuteling. Door de klant beheerde sleutels moeten worden opgeslagen in een Azure Key Vault. 

Klantbeheersleutels configureren met C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Klantbeheersleutels configureren met de sjabloon Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Een back-up maken van Azure Key Vault-certificaten:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** test periodiek het herstel van gegevens van uw Azure Key Vault-geheimen.

Azure Key Vault-certificaten herstellen:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Klantbeheersleutels configureren met C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Klantbeheersleutels configureren met de sjabloon Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorgen voor bescherming van back-ups en beheerde sleutels van klanten

**Richtlijnen:** Klant om Soft-Delete in Key Vault in te schakelen om sleutels te beschermen tegen onbedoelde of kwaadwillige verwijdering.  U ook zuiveringsbeveiliging inschakelen, zodat als een kluis of een object in de verwijderde status, deze niet kan worden verwijderd totdat de bewaartermijn is verstreken.  

Soft-Delete- en zuiveringsbeveiliging inschakelen in Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Klantbeheersleutels configureren met C# :https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Klantbeheersleutels configureren met de sjabloon Azure Resource Manager:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Beveiligingscontrole: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)voor meer informatie.*

### <a name="101-create-incident-response-guide"></a>10.1: Gids voor incidentrespons maken

**Richtlijnen**: Bouw een incidentresponseguide voor uw organisatie. Zorg ervoor dat er geschreven incidentresponseplannen zijn die alle rollen van personeel definiëren, evenals fasen van incidentafhandeling/beheer, van detectie tot beoordeling na incidenten.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Procedure voor het scoren van incidenten en prioritering maken

**Richtlijnen**: Security Center wijst een ernst toe aan elke waarschuwing om u te helpen prioriteiten te stellen welke waarschuwingen eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen:** Voer oefeningen uit om de incidentresponsemogelijkheden van uw systemen te testen op een regelmatige cadans om uw Azure-resources te beschermen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken &nbsp;en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat uw gegevens zijn geopend door een onwettige of onbevoegde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.
    

De beveiligingscontactpersoon azure security center instellen:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

**Richtlijnen:** exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue export om risico's voor Azure-resources te identificeren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatiseer de reactie op beveiligingswaarschuwingen

**Richtlijnen:** Gebruik de functie Werkstroomautomatisering in Azure Security Center om automatisch reacties te activeren via 'Logic Apps' over beveiligingswaarschuwingen en aanbevelingen om uw Azure-bronnen te beschermen.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Security Control: Penetration Tests en Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Voer regelmatig penetratietests uit van uw Azure-bronnen en zorg ervoor dat alle kritieke beveiligingsbevindingen binnen 60 dagen worden verholpen

**Richtlijnen:** Volg de Microsoft Rules of Engagement om ervoor te zorgen https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1dat uw penetratietests niet in strijd zijn met het Microsoft-beleid: .

Meer informatie over de strategie en uitvoering van Red Teaming en live site penetratietesten van microsoft op basis van door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen vindt u hier:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

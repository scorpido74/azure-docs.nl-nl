---
title: Azure-beveiligingsbasislijn voor Azure SQL-database
description: Azure-beveiligingsbasislijn voor Azure SQL-database
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 35d4d44f0b9f1b210f38a034575b589c7211d55c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246667"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure-beveiligingsbasislijn voor Azure SQL-database

De Azure Security Baseline voor Azure SQL Database bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze services is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen:** U Azure Private Link inschakelen om toegang te verlenen tot Azure PaaS Services (bijvoorbeeld SQL Database) en Azure-gehoste klant-/partnerservices via een privéeindpunt in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. Als u verkeer azure SQL Database wilt laten bereiken, gebruikt u de SQL-servicetags om uitgaand verkeer via netwerkbeveiligingsgroepen toe te staan.


Met virtuele netwerkregels kan Azure SQL Database alleen communicatie accepteren die wordt verzonden vanuit geselecteerde subnetten binnen een virtueel netwerk.


Private Link instellen voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database


Eindpunten en regels voor databaseservers gebruiken voor virtuele netwerkservice:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

**Richtlijnen:** Gebruik Azure Security Center en herstel de aanbevelingen voor netwerkbeveiliging voor het subnet waaraan uw Azure SQL Database Server is geïmplementeerd. Schakel voor Azure Virtual Machines (VM) die verbinding maken met uw Azure SQL Database Server-exemplaar, NSG-stroomlogboeken (Network Security Group) in voor de NSG's die deze VM's beschermen en logboeken naar een Azure Storage Account verzenden voor verkeerscontrole. U ook NSG-stroomlogboeken naar een log-analysewerkruimte verzenden en Traffic Analytics gebruiken om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en netwerkverkeerde configuraties te lokaliseren.


NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Inzicht in netwerkbeveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Inzicht in netwerkbeveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor Azure Apps Service of compute resources die webtoepassingen hosten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

**Richtlijnen:** Schakel DDoS Protection Standard in op de virtuele netwerken die zijn gekoppeld aan uw SQL Server-exemplaren voor beveiligingen tegen gedistribueerde denial-of-service-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.


DDoS-beveiliging configureren:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Begrijp azure security center integrated threat intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen:** Schakel voor Azure Virtual Machines (VM's) die verbinding maken met uw Azure SQL Database-exemplaar netwerkbeveiligingsgroep (NSG)-stroomlogboeken in voor de NSGs die deze VM's beschermen en logboeken naar een Azure Storage Account verzenden voor verkeerscontrole. Schakel Network Watcher-pakketopname in indien nodig voor het onderzoeken van afwijkende activiteiten.


NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Zo schakel je Network Watcher in:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Schakel Advanced Threat Protection (ATP) in voor Azure SQL Database.  Gebruikers ontvangen een waarschuwing over verdachte databaseactiviteiten, potentiële kwetsbaarheden en SQL-injectieaanvallen, evenals afwijkende databasetoegang en querypatronen. Advanced Threat Protection integreert ook waarschuwingen met Azure Security Center.

Geavanceerde bedreigingsbeveiliging voor Azure SQL-database begrijpen en gebruiken:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor Azure Apps Service of compute resources die webtoepassingen hosten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen:** gebruik virtuele netwerkservicetags om netwerktoegangsbesturingselementen te definiëren voor netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of bestemmingsveld van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.


Wanneer serviceeindpunten voor Azure SQL Database worden gebruikt, zijn uitgaande naar Azure SQL Database Public IP-adressen vereist: Network Security Groups (NSG's) moeten worden geopend voor Azure SQL Database IP's om connectiviteit mogelijk te maken. U dit doen door NSG-servicetags voor Azure SQL Database te gebruiken.


Servicetags begrijpen met Service-eindpunten voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations


Servicetags begrijpen en gebruiken:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** Netwerkbeveiligingsconfiguraties definiëren en implementeren voor uw Azure SQL Database-server-exemplaren met Azure Policy. U de naamruimte 'Microsoft.Sql' gebruiken om aangepaste beleidsdefinities te definiëren of een van de ingebouwde beleidsdefinities gebruiken die zijn ontworpen voor Azure SQL Database-servernetwerkbeveiliging. Een voorbeeld van een van toepassing ingebouwd netwerkbeveiligingsbeleid voor Azure SQL Database-server zou zijn: "SQL Server moet een eindpunt voor virtuele netwerkservice gebruiken".
 

Gebruik Azure Blueprints om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Azure Resource Management-sjablonen, RBAC (Role-based access control) en beleidsregels, in één blauwdrukdefinitie. Pas de blauwdruk eenvoudig toe op nieuwe abonnementen en omgevingen en stem de besturing en het beheer af via versiebeheer.


Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Een Azure Blueprint maken:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

**Richtlijnen**: Gebruik tags voor netwerkbeveiligingsgroepen (NSG) en andere bronnen met betrekking tot netwerkbeveiliging en verkeersstroom. Voor afzonderlijke NSG-regels gebruikt u het veld 'Beschrijving' om de bedrijfsbehoefte en/of -duur (enz.) op te geven voor regels die verkeer van/naar een netwerk toestaan.


Gebruik een van de ingebouwde Azure-beleidsdefinities met betrekking tot tags, zoals 'Tag en de waarde ervan vereisen' om ervoor te zorgen dat alle resources met tags worden gemaakt en om u op de hoogte te stellen van bestaande niet-gelabelde bronnen.


U Azure PowerShell of Azure CLI gebruiken om acties op te zoeken of uit te voeren op basis van hun tags.


Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik Azure Activity Log om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren voor netwerkbronnen die verband houden met uw Azure SQL Database-server-exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen plaatsvinden in kritieke netwerkbronnen.


Azure Activity Log-gebeurtenissen weergeven en ophalen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Waarschuwingen maken in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Gebruik goedgekeurde tijdsynchronisatiebronnen

**Richtlijnen:** Microsoft onderhoudt tijdsbronnen voor Azure-bronnen. U de synchronisatie van de tijd bijwerken voor uw compute-implementaties.



Tijdsynchronisatie configureren voor Azure-rekenbronnen:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboeken configureren

**Richtlijnen:** Schakel controle voor Azure SQL Database in om databasegebeurtenissen bij te houden en deze te schrijven naar een controlelogboek in uw Azure Storage Account, Log Analytics-werkruimte of Gebeurtenishubs.


Daarnaast u Azure SQL Diagnostics-telemetrie streamen naar Azure SQL Analytics, een cloudoplossing die de prestaties van Azure SQL-databases, elastische pools en beheerde exemplaren op schaal en in meerdere abonnementen bewaakt. Het kan u helpen bij het verzamelen en visualiseren van Azure SQL Database prestatiestatistieken en het heeft ingebouwde informatie voor het oplossen van prestaties.

Controle instellen voor uw Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing


Statistieken en bronlogboeken verzamelen met Azure Monitor:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging


Diagnostische gegevens streamen naar Azure SQL Analytics:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#configure-the-streaming-export-of-diagnostic-telemetry

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure-resources

**Richtlijnen:** Schakel controle in op uw Azure SQL Database-serverinstantie en kies een opslaglocatie voor de controlelogboeken (Azure Storage, Log Analytics of Event Hub).


Controle inschakelen voor Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze benchmark is bedoeld voor compute resources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen:** Wanneer u uw Azure SQL Database-logboeken opslaat in een Log Analytics Workspace, stelt u de bewaarperiode voor logboeken in volgens de nalevingsvoorschriften van uw organisatie.



Parameters voor logboekbehoud instellen:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen**: Analyseer en monitor logboeken op afwijkend gedrag en bekijk regelmatig resultaten. Gebruik de geavanceerde bedreigingsbeveiliging van Azure Security Center om te waarschuwen voor ongebruikelijke activiteiten die verband houden met uw Azure SQL Database-exemplaar. U ook waarschuwingen configureren op basis van metrische waarden of Azure Activity Log-vermeldingen die betrekking hebben op uw Azure SQL Database-exemplaren.


Inzicht in geavanceerde bedreigingsbeveiliging en waarschuwingen voor Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


Aangepaste waarschuwingen voor Azure SQL Database configureren:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtlijnen:** Gebruik Azure Security Center Advanced Threat Protection voor Azure SQL-databases voor het bewaken en waarschuwen van afwijkende activiteiten. Geavanceerde gegevensbeveiliging inschakelen voor uw SQL-databases. Advanced Data Security omvat functionaliteit voor het ontdekken en classificeren van gevoelige gegevens, het opsporen en beperken van potentiële databasekwetsbaarheden en het detecteren van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw database.



Inzicht in geavanceerde bedreigingsbeveiliging en waarschuwingen voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview



Geavanceerde gegevensbeveiliging inschakelen voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Waarschuwingen beheren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtsnoeren**: Niet van toepassing; voor Azure SQL Server wordt de anti-malwareoplossing beheerd door Microsoft op het onderliggende platform.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtsnoeren**: Niet van toepassing; DNS-logboekregistratie is niet van toepassing op Azure SQL Server.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

**Richtsnoeren**: Niet van toepassing; controle van de opdrachtregel is niet van toepassing op Azure SQL Server.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Beveiligingsbeheer: Identiteits- en toegangscontrole](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van de administratieve rekeningen

**Richtlijnen**: Azure Active Directory (AAD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en opvraagbaar zijn. Gebruik de AAD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van administratieve groepen.


Een maprol in Azure AD met PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Leden van een directoryrol in Azure AD krijgen met PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

**Richtlijnen:** Azure Active Directory heeft niet het concept van standaardwachtwoorden. Bij het inrichten van een Azure SQL Database-exemplaar wordt aanbevolen dat u ervoor kiest om verificatie te integreren met Azure Active Directory.


Azure Active Directory-verificatie configureren en beheren met Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Gebruik maken van speciale administratieve rekeningen

**Richtlijnen**: Maak beleid en procedures rond het gebruik van specifieke administratieve accounts. Gebruik Azure Security Center Identity and Access Management om het aantal beheerdersaccounts te controleren.



Informatie over identiteit en toegang van azure security center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik eenmalige aanmelding (SSO) met Azure Active Directory

**Richtsnoeren**: Niet van toepassing; u Azure Active Directory Authentication configureren om te integreren met Azure SQL Server, maar eenmalige aanmelding wordt niet ondersteund.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

**Richtlijnen:** Schakel Azure Active Directory (AAD) Multi-Factor Authentication (MFA) in en volg aanbevelingen voor Azure Security Center Identity and Access Management.


Mfa inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Identiteit en toegang bewaken binnen Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

**Richtlijnen:** Gebruik een Paw (Privileged Access Workstation) met Multi-Factor Authentication MFA geconfigureerd om in te loggen en Azure-bronnen te configureren.


Meer informatie over geprivilegieerde werkstations voor toegang:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Mfa inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten vanuit administratieve accounts

**Richtlijnen:** Gebruik Azure Active Directory-beveiligingsrapporten voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden.



Gebruik Advanced Threat Protection voor Azure SQL Database om afwijkende activiteiten te detecteren die ongebruikelijke en mogelijk schadelijke pogingen wijzen om databases te openen of te exploiteren.



Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk



Hoe u de identiteit van gebruikers controleren en activiteiten openen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access



Bekijk geavanceerde dreigingsbescherming en potentiële waarschuwingen:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources beheren vanaf alleen goedgekeurde locaties

**Richtlijnen:** Gebruik locaties met voorwaardelijke toegang om portal- en Azure Resource Management-toegang toe te staan vanuit alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's.


Benoemde locaties configureren in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen:** maak een AZURE Active Directory(AAD)-beheerder voor uw Azure SQL Database-server-exemplaren.


Azure Active Directory-verificatie configureren en beheren met Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Een AAD-exemplaar maken en configureren:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (AAD) biedt logboeken om verouderde accounts te ontdekken. Gebruik daarnaast Azure Identity Access-toegangsbeoordelingen om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. De toegang van gebruikers kan regelmatig worden beoordeeld om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.


Azure Identity Access-recensies gebruiken:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen**: Configureer Azure Active Directory (AAD)-verificatie met Azure SQL en maak diagnostische instellingen voor Azure Active Directory-gebruikersaccounts, het verzenden van de controlelogboeken en aanmeldingslogboeken naar een log Analytics-werkruimte. De gewenste waarschuwingen configureren binnen de werkruimte Log Analytics.


Azure Active Directory-verificatie configureren en beheren met Azure SQL:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure


Azure-activiteitslogboeken integreren in Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing over afwijking van het inloggedrag van het account

**Richtlijnen:** Gebruik Azure Active Directory (AAD) Identiteitsbeveiliging en risicodetecties om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Bovendien u gegevens opnemen in Azure Sentinel voor verder onderzoek.


Aanmeldingen voor AD-risico's weergeven:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Hoe u risicobeleid voor identiteitsbescherming configureert en inschakelt:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen:** In ondersteuningsscenario's waarin Microsoft toegang nodig heeft tot klantgegevens, biedt Azure Customer Lockbox een interface voor klanten om verzoeken voor toegang tot klantgegevens te controleren en goed te keuren of af te wijzen.


Inzicht in Klant Lockbox:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure Security Center-bewaking:** niet van toepassing

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

**Richtlijnen**: Implementeer afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door Vnet/Subnet, op de juiste manier worden getagd en beveiligd binnen een NSG- of Azure Firewall. Bronnen die gevoelige gegevens opslaan of verwerken, moeten worden geïsoleerd. Privékoppeling gebruiken; Azure SQL Server implementeren in uw Vnet en privé verbinding maken met privéeindpunten.



Extra Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Managementgroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create



Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



Private Link instellen voor Azure SQL Database:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

**Richtlijnen:** Voor Azure SQL-databases die gevoelige informatie opslaan of verwerken, markeert u de database en gerelateerde bronnen als gevoelig met tags. Configureer Private Link in combinatie met Network Security Group Service Tags op uw Azure SQL Database-exemplaren om exfiltratie van gevoelige informatie te voorkomen.



Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.



Private Link en NSG configureren om gegevensexfiltratie op uw Azure SQL Database-exemplaren te voorkomen:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview



Inzicht in de bescherming van klantgegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen:** Azure SQL Database beveiligt uw gegevens door gegevens in beweging te versleutelen met Transport Layer Security. SQL Server dwingt te allen tijde encryptie (SSL/TLS) af voor alle verbindingen. Dit zorgt ervoor dat alle gegevens worden versleuteld "onderweg" tussen de client en de server, ongeacht de instelling van Encrypt of TrustServerCertificate in de verbindingstekenreeks.



Inzicht in Azure SQL-versleuteling tijdens transit:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

**Richtlijnen:** Gebruik de functie voor het ontdekken en classificeren van Azure SQL Database-gegevens. Gegevensdetectie en -classificatie bieden geavanceerde mogelijkheden die zijn ingebouwd in &amp; Azure SQL Database voor het ontdekken, classificeren en labelen van de bescherming van de gevoelige gegevens in uw databases.



Gegevensdetectie en -classificatie gebruiken voor Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

**Richtlijnen:** Gebruik Azure Active Directory (AAD) voor het verifiëren en beheren van toegang tot Azure SQL Database-exemplaren.


Azure SQL Server integreren met Azure Active Directory voor verificatie:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication


Toegang beheren in Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

**Richtlijnen:** Microsoft beheert de onderliggende infrastructuur voor Azure SQL Database en heeft strenge controles geïmplementeerd om het verlies of de blootstelling van klantgegevens te voorkomen.

Inzicht in de bescherming van klantgegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen**: Transparent data encryption (TDE) helpt Azure SQL Database, Azure SQL managed instance en Azure Data Warehouse te beschermen tegen de dreiging van kwaadaardige offline activiteit door gegevens in rust te versleutelen. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht. Standaard wordt TDE ingeschakeld voor alle nieuw geïmplementeerde Azure SQL-databases. De TDE-versleutelingssleutel kan worden beheerd door Microsoft of de klant.


Hoe u transparante gegevensversleuteling beheert en uw eigen versleutelingssleutels gebruikt:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen:** Gebruik Azure Monitor met het Azure Activity Log om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in productie-exemplaren van Azure SQL-databases en andere kritieke of gerelateerde bronnen.


Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtlijnen:** Schakel Geavanceerde gegevensbeveiliging voor Azure SQL Database in en volg aanbevelingen van Azure Security Center voor het uitvoeren van kwetsbaarheidsbeoordelingen op uw Azure SQL Servers.



Kwetsbaarheidsbeoordelingen uitvoeren op uw Azure SQL-databases:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment



Geavanceerde gegevensbeveiliging inschakelen:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security



Aanbevelingen voor het beoordelen van azure security center-kwetsbaarheden implementeren:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementatie van een oplossing voor het patchbeheer van geautomatiseerd besturingssysteem

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Geautomatiseerde softwarepatchbeheeroplossing van derden implementeren

**Richtsnoeren**: Niet van toepassing; deze benchmark is bedoeld voor compute resources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk back-to-back kwetsbaarheid scans

**Richtlijnen**: Schakel periodieke terugkerende scans in voor uw Azure SQL Database-exemplaren; hiermee wordt een kwetsbaarheidsbeoordeling geconfigureerd om één keer per week automatisch een scan in uw database uit te voeren. Er wordt een overzicht van het scanresultaat verzonden naar het door u optezijn e-mailadres(en). Vergelijk de resultaten om te controleren of kwetsbaarheden zijn verholpen.



Een rapport voor het beoordelen van kwetsbaarheden exporteren in Azure Security Center:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implement-vulnerability-assessment

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden

**Richtlijnen:** gebruik de standaardrisicoclassificaties (Secure Score) van Azure Security Center.

Informatie over azure security center secure score:https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik Azure Resource Graph om alle bronnen (inclusief Azure SQL Server-exemplaren) in uw abonnement(en) op te vragen en te ontdekken.  Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant beschikt en dat u alle Azure-abonnementen en resources binnen uw abonnementen opsommen.


Hoewel klassieke Azure-bronnen kunnen worden ontdekt via Azure Resource Graph, is het ten zeerste aan te raden om azure resource beheerbronnen in de toekomst te maken en te gebruiken.


Query's maken met Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Uw Azure-abonnementen weergeven:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Azure RBAC begrijpen:https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-bronnen met metagegevens om ze logisch te ordenen in een taxonomie.



Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ongeautoriseerde Azure-bronnen verwijderen

**Richtlijnen**: Gebruik tagging, beheergroepen en afzonderlijke abonnementen, waar nodig, om assets te organiseren en bij te houden. Verzoen de voorraad regelmatig en zorg ervoor dat ongeautoriseerde bronnen tijdig uit het abonnement worden verwijderd.



Extra Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Managementgroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create



Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Een inventaris bijhouden van goedgekeurde Azure-resources en softwaretitels

**Richtlijnen**: Lijst met goedgekeurde Azure-resources en goedgekeurde software voor uw rekenbronnen definiëren

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor voor niet-goedgekeurde Azure-bronnen

**Richtlijnen:** Gebruik Azure Policy om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen
- Toegestane brontypen

Gebruik Azure Resource Graph om bronnen binnen uw abonnement(en) te zoeken/ontdekken. Controleer of alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor voor niet-goedgekeurde softwaretoepassingen binnen compute resources

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="68-use-only-approved-applications"></a>6.8: Gebruik alleen goedgekeurde aanvragen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** Gebruik Azure Policy om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen
- Toegestane brontypen

Gebruik Azure Resource Graph om bronnen binnen uw abonnement(en) te zoeken/ontdekken. Controleer of alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek brontype weigeren met Azure-beleid:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor toepassingen die op compute resources worden uitgevoerd.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: De mogelijkheid van gebruikers beperken om via scripts met Azure Resources Manager te communiceren

**Richtlijnen:** Gebruik voorwaardelijke toegang voor Azure om de interactie van gebruikers met Azure Resource Manager te beperken door 'Bloktoegang' voor de Microsoft Azure Management-app te configureren.


Voorwaardelijke toegang configureren om toegang tot Azure Resource Manager te blokkeren:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Beperk de mogelijkheid van gebruikers om scripts uit te voeren binnen compute resources

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor App Service of compute resources die desktop- of webtoepassingen hosten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Beveiligingsbeheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties instellen voor alle Azure-resources

**Richtlijnen**: Gebruik azure policy of Azure Security Center-aanbevelingen voor Azure SQL Servers/Databases om beveiligingsconfiguraties voor alle Azure-bronnen te behouden.


Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen voor uw Azure-bronnen af te dwingen.



Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



Inzicht in Azure-beleidseffecten:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen:** Als u aangepaste Azure-beleidsdefinities gebruikt, gebruikt u Azure DevOps of Azure Repos om uw code veilig op te slaan en te beheren.



Code opslaan in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Azure Repos-documentatie:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.SQL' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.



Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Systeemconfiguratiebeheertools implementeren voor besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-services

**Richtlijnen:** Maak gebruik van Azure Security Center om basislijnscans uit te voeren voor uw Azure SQL Servers en Databases.



Aanbevelingen herstellen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen**: Gebruik Azure Key Vault om versleutelingssleutels op te slaan voor Azure SQL Database Transparent Data Encryption (TDE).



Gevoelige gegevens die in Azure SQL Server worden opgeslagen, beveiligen en de coderingssleutels opslaan in Azure Key Vault:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen**: Gebruik Beheerde identiteiten om Azure-services een automatisch beheerde identiteit te bieden in Azure Active Directory (AAD). Met Managed Identities u zich verifiëren voor elke service die AAD-verificatie ondersteunt, inclusief Azure Key Vault, zonder referenties in uw code.


Zelfstudie: Gebruik een beheerde identiteit met Windows VM-systeem om toegang te krijgen tot Azure SQL:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql


Beheerde identiteiten configureren:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling aan referenties elimineren

**Richtlijnen:** Implementeer credential scanner om referenties binnen uw code te identificeren. Credential Scanner stimuleert ook het verplaatsen van ontdekte referenties naar veiligere locaties zoals Azure Key Vault. 

Credential Scanner instellen:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Gebruik centraal beheerde anti-malwaresoftware

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources. Microsoft verwerkt anti-malware voor onderliggend platform.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-resources

**Richtlijnen**: Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op inhoud van klanten.


Scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-bronnen, zoals App Service, Data Lake Storage, Blob Storage, Azure SQL Server, enz. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.


Microsoft Antimalware voor Azure Cloud Services en virtuele machines begrijpen:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources. Microsoft verwerkt anti-malware voor onderliggend platform.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

**Richtlijnen:** Om uw bedrijf te beschermen tegen gegevensverlies, maakt Azure SQL Database automatisch wekelijks volledige databaseback-ups, elke 12 uur verschillende databaseback-ups en elke 5 - 10 minuten back-ups van transactielogboeken. De back-ups worden gedurende ten minste 7 dagen opgeslagen in RA-GRS-opslag voor alle servicelagen. Alle servicelagen, behalve de configureerbare bewaarperiode voor basisondersteuning voor point-in-time herstel, tot 35 dagen.


Om aan verschillende nalevingsvereisten te voldoen, u verschillende bewaartermijnen selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. Het opslagverbruik is afhankelijk van de geselecteerde frequentie van back-ups en de bewaarperiode(en).


Inzicht in back-ups en bedrijfscontinuïteit met Azure SQL Server:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

**Richtlijnen:** Azure SQL Database maakt automatisch de databaseback-ups die tussen 7 en 35 dagen worden bewaard en maakt gebruik van Azure read-access geo-redundantstorage (RA-GRS) om ervoor te zorgen dat deze worden bewaard, zelfs als het datacenter niet beschikbaar is. Deze back-ups worden automatisch gemaakt. Schakel indien nodig georedundante back-ups op lange termijn in voor uw Azure SQL-databases.


Als u door de klant beheerde sleutels gebruikt voor transparante gegevensversleuteling, moet u ervoor zorgen dat er een back-up van uw sleutels wordt gemaakt.


Back-ups in Azure SQL Server begrijpen:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database


Een back-up maken van sleutelkluissleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Zorg ervoor dat u periodiek gegevensherstel van inhoud binnen Azure Backup uitvoeren. Test indien nodig de herstelinhoud in een geïsoleerde VLAN. Test de restauratie van back-ups van door de klant beheerde sleutels.


Sleutelkluissleutels herstellen in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


Azure SQL Database-back-ups herstellen met point-in-time restore:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

**Richtlijnen:** Schakel soft delete in Azure Key Vault in om sleutels te beschermen tegen onbedoelde of kwaadwillige verwijdering.


Soft delete inschakelen in Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Beveiligingscontrole: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een gids voor incidentrespons maken

**Richtlijnen**: Zorg ervoor dat er schriftelijke incidentresponseplannen zijn die de rollen van het personeel en de fasen van incidentafhandeling/-beheer definieert.



Werkstroomautomatiseringen configureren binnen Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een incidentscore- en prioriteringsprocedure maken

**Richtlijnen**: Security Center kent een ernst toe aan waarschuwingen, zodat u prioriteit geven aan de volgorde waarin u elke waarschuwing bijwoont, zodat wanneer een resource wordt gecompromitteerd, u er meteen bij komen. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing.
Beveiligingswaarschuwingen in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen**: Voer oefeningen uit om de incidentresponsmogelijkheden van uw systemen op een regelmatige cadans te testen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.



U verwijzen naar de publicatie van NIST: Gids voor test-, trainings- en trainingsprogramma's voor IT-plannen en -mogelijkheden:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat uw gegevens zijn geopend door een onwettige of onbevoegde partij.



De beveiligingscontactpersoon azure security center instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

**Richtlijnen:** exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue exporteren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen naar Sentinel te streamen.


Continue export configureren:

https://docs.microsoft.com/azure/security-center/continuous-export


Waarschuwingen streamen naar Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatiseer de reactie op beveiligingswaarschuwingen

**Richtlijnen:** Gebruik de functie Workflowautomatisering in Azure Security Center om automatisch reacties te activeren via 'Logic Apps' over beveiligingswaarschuwingen en aanbevelingen.



Workflowautomatisering en logische apps configureren:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Security Control: Penetration Tests en Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Voer regelmatig penetratietests uit van uw Azure-resources en zorg voor herstel van alle kritieke beveiligingsbevindingen binnen 60 dagen

**Richtlijnen:** Volg de Microsoft Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



Meer informatie over de strategie en uitvoering van Red Teaming en live site penetratietesten van microsoft op basis van door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen vindt u hier:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

---
title: Azure-beveiligingsbasislijn voor Cosmos DB
description: Azure-beveiligingsbasislijn voor Cosmos DB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd2706b6a6880d7c9454619e1315b2b5b5404561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244270"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Azure-beveiligingsbasislijn voor Cosmos DB

De Azure Security Baseline for Cosmos DB bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze service is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen:** Door Azure Private Link te gebruiken, u verbinding maken met een Azure Cosmos-account via een privéeindpunt. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U ook het risico op gegevensexfiltratie verminderen door een strikte set uitgaande regels voor een netwerkbeveiligingsgroep (NSG) te configureren en die NSG te associëren met uw subnet.

U serviceeindpunten ook gebruiken om uw Azure Cosmos-account te beveiligen. Door een Service Endpoint in te schakelen, u Azure Cosmos-accounts configureren om alleen toegang toe te staan vanaf een specifiek subnet van een Virtueel Azure-netwerk. Zodra het Azure Cosmos DB Service Endpoint is ingeschakeld, u de toegang tot een Azure Cosmos-account beperken met verbindingen vanaf een subnet in een virtueel netwerk.

U de gegevens die zijn opgeslagen in uw Azure Cosmos-account ook beveiligen met BEHULP van IP-firewalls. Azure Cosmos DB ondersteunt IP-gebaseerde toegangscontroles voor inkomende firewallondersteuning. U een IP-firewall instellen op het Azure Cosmos-account met behulp van de Azure-portal, Azure Resource Manager-sjablonen of via azure CLI of Azure PowerShell.

Overzicht van Azure Private Link:https://docs.microsoft.com/azure/private-link/private-link-overview

Een privéeindpunt configureren voor Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Een netwerkbeveiligingsgroep maken met een beveiligingsconfig:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

IP-firewall configureren in Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

**Richtlijnen:** Gebruik Azure Security Center en volg aanbevelingen voor netwerkbeveiliging om netwerkbronnen met betrekking tot uw Azure Cosmos-account te beveiligen.

Wanneer virtuele machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Azure Cosmos-account, u een netwerkbeveiligingsgroep (NSG) gebruiken om het risico op gegevensexfiltratie te verminderen. Schakel NSG-stroomlogboeken in en stuur logboeken naar een Azure Storage Account voor verkeersaudits. U ook NSG-stroomlogboeken naar een log-analysewerkruimte verzenden en Traffic Analytics gebruiken om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en netwerkverkeerde configuraties te lokaliseren.

Inzicht in netwerkbeveiliging van Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

NSG-stroomlogboeken inschakelen:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

**Richtlijnen:** Gebruik de functie Cross-Origin Resource Sharing (CORS) om een webtoepassing die onder één domein wordt uitgevoerd, toegang te geven tot bronnen in een ander domein. Webbrowsers implementeren een beveiligingsbeperking die bekend staat als beleid van dezelfde oorsprong, waardoor een webpagina geen API's in een ander domein kan aanroepen. CORS biedt echter een veilige manier om het oorsprongsdomein api's in een ander domein te laten aanroepen. Nadat u de CORS-ondersteuning voor uw Azure Cosmos-account hebt ingeschakeld, worden alleen geverifieerde aanvragen geëvalueerd om te bepalen of ze zijn toegestaan volgens de regels die u hebt opgegeven.

Cross-Origin Resource Sharing configureren:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

**Richtlijnen**: Gebruik Advanced Threat Protection (ATP) voor Azure Cosmos DB . ATP voor Azure Cosmos DB biedt een extra laag beveiligingsinformatie die ongebruikelijke en mogelijk schadelijke pogingen detecteert om Azure Cosmos-accounts te openen of te exploiteren. Met deze beveiligingslaag u bedreigingen aanpakken en integreren met centrale beveiligingsbewakingssystemen.

DDoS Protection Standard inschakelen op de virtuele netwerken die zijn gekoppeld aan uw Azure Cosmos DB-exemplaren om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.

Azure Cosmos DB Advanced Threat Protection configureren:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

DDoS-beveiliging configureren:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Begrijp azure security center integrated threat intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen**: NsG-stroomlogboeken (Network Security Group) inschakelen en logboeken naar een opslagaccount verzenden voor verkeerscontrole. U NSG-stroomlogboeken naar een log-analysewerkruimte verzenden en Traffic Analytics gebruiken om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en netwerkverkeerde configuraties te lokaliseren.

NSG-stroomlogboeken inschakelen:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen**: Gebruik Advanced Threat Protection (ATP) voor Azure Cosmos DB. ATP voor Azure Cosmos DB biedt een extra laag beveiligingsinformatie die ongebruikelijke en mogelijk schadelijke pogingen detecteert om Azure Cosmos-accounts te openen of te exploiteren. Met deze beveiligingslaag u bedreigingen aanpakken en integreren met centrale beveiligingsbewakingssystemen. 

Cosmos DB Advanced Threat Protection configureren:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtsnoeren**: Niet van toepassing; aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen:** Voor resources die toegang nodig hebben tot uw Azure Cosmos-account, gebruikt u servicetags voor virtuele netwerken om netwerktoegangsbesturingselementen op netwerkbeveiligingsgroepen of Azure Firewall te definiëren. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld AzureCosmosDB) op te geven in het juiste bron- of doelveld van een regel, u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

Voor meer informatie over het gebruik van servicetags:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties voor netwerkbronnen definiëren en implementeren met Azure Policy. Gebruik Azure Policy-aliassen in de naamruimten 'Microsoft.DocumentDB' en 'Microsoft.Network' om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure Cosmos DB-exemplaren te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities voor Azure Cosmos DB, zoals:

- Geavanceerde bedreigingsbeveiliging implementeren voor DB-accounts in de kosmos

- Cosmos DB moet een eindpunt voor virtuele netwerkservice gebruiken

U Azure Blueprints ook gebruiken om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Azure Resource Manager-sjablonen, rbac (Role-based access control) en beleidsregels in één blauwdrukdefinitie. U de blauwdruk eenvoudig toepassen op nieuwe abonnementen, omgevingen en het verfijnen van besturing en beheer via versiebeheer.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een Azure Blueprint maken:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

**Richtlijnen:** Gebruik tags voor netwerkbronnen die zijn gekoppeld aan uw Azure Cosmos DB-implementatie om ze logisch te ordenen in een taxonomie.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik Azure Activity Log om netwerkbronconfiguraties te bewaken en wijzigingen op te sporen voor netwerkbronnen die verband houden met uw Azure Cosmos DB-exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen plaatsvinden in kritieke netwerkbronnen. 

Azure Activity Log-gebeurtenissen weergeven en ophalen:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Gebruik goedgekeurde tijdsynchronisatiebronnen

**Richtlijnen**: Microsoft onderhoudt de tijdbron die wordt gebruikt voor Azure-resources, zoals Azure Cosmos DB, voor tijdstempels in de logboeken.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboeken configureren

**Richtlijnen:** Inname logboeken via Azure Monitor om beveiligingsgegevens gegenereerd door Azure Cosmos DB te verzamelen. Gebruik in de Azure Monitor log analytics-werkruimte(s) om analyses op te vragen en uit te voeren en opslagaccounts te gebruiken voor opslag op lange termijn/archiefopslag. U ook gegevens aan boord inschakelen en aan boord inschakelen bij Azure Sentinel of een SIEM (Security Incident and Event Management) van derden. 

Diagnostische logboeken inschakelen voor Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/logging

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure-resources

**Richtlijnen**: Schakel diagnostische instellingen voor Azure Cosmos DB in en stuur de logboeken naar een Log Analytics-werkruimte of opslagaccount. Diagnostische instellingen in Azure Cosmos DB worden gebruikt om bronlogboeken te verzamelen. Deze logboeken worden vastgelegd per aanvraag en ze worden ook wel aangeduid als "data plane logs". Enkele voorbeelden van de bewerkingen van het gegevensvlak zijn verwijderen, invoegen en lezen. U ook diagnostische instellingen voor Azure Activity Log inschakelen en deze naar dezelfde Log Analytics Workspace verzenden.

Diagnostische instellingen voor Azure Cosmos DB inschakelen:https://docs.microsoft.com/azure/cosmos-db/logging

Diagnostische instellingen voor Azure Activity Log inschakelen:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen:** Stel in Azure Monitor de bewaarperiode voor Logboekanalyse-werkruimten in die zijn gekoppeld aan uw Azure Cosmos DB-exemplaren volgens de nalevingsvoorschriften van uw organisatie.

Parameters voor logboekbehoud instellen:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen:** U query's uitvoeren in Log Analytics een werkruimte om termen te zoeken, trends te identificeren, patronen te analyseren en vele andere inzichten te bieden op basis van de Azure Cosmos DB-logboeken die u hebt verzameld.

Query's uitvoeren voor Azure Cosmos DB in Logboekanalysewerkruimten:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtlijnen:** Schakel in Azure Security Center Advanced Threat Protection voor Azure Cosmos DB in om afwijkende activiteiten in beveiligingslogboeken en gebeurtenissen te controleren. Schakel diagnostische instellingen in Azure Cosmos DB in en stuur logboeken naar een Log Analytics-werkruimte.

 

U ook aan boord van uw Log Analytics-werkruimte naar Azure Sentinel gaan, omdat deze een SOAR-oplossing (Security Orchestration Automated Response) biedt. Hierdoor kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligingsproblemen op te lossen. Bovendien u aangepaste logboekwaarschuwingen maken in uw Log Analytics-werkruimte met Azure Monitor.

Lijst met waarschuwingen voor bedreigingsbescherming voor Azure Cosmos DB:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Logboekwaarschuwingen maken, weergeven en beheren met Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtsnoeren**: Niet van toepassing; Azure Cosmos DB verwerkt of produceert geen anti-malware gerelateerde logboeken.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtsnoeren**: Niet van toepassing; Azure Cosmos DB verwerkt of produceert geen DNS-gerelateerde logboeken.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Beveiligingsbeheer: Identiteits- en toegangscontrole](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van de administratieve rekeningen

**Richtlijnen:** U het deelvenster Identiteits- en toegangsbeheer (IAM) in de Azure-portal gebruiken om RBAC (Role-based Access Control) te configureren en voorraad te onderhouden op Azure Cosmos DB-resources. De rollen worden toegepast op gebruikers, groepen, serviceprincipals en beheerde identiteiten in Active Directory. U ingebouwde rollen of aangepaste rollen voor personen en groepen gebruiken.

Azure Cosmos DB biedt ingebouwde RBAC voor veelvoorkomende beheerscenario's in Azure Cosmos DB. Een persoon met een profiel in Azure Active Directory (AD) kan deze RBAC-rollen toewijzen aan gebruikers, groepen, serviceprincipals of beheerde identiteiten om resources en bewerkingen op Azure Cosmos DB-resources toe te kennen of te weigeren.

U de Azure AD PowerShell-module ook gebruiken om adhocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen. 

Bovendien kunnen sommige acties in Azure Cosmos DB worden beheerd met Azure Active Directory en accountspecifieke hoofdsleutels.  Gebruik de accountinstelling 'disableKeyBasedMetadataWriteAccess' om de toegang tot de sleutel te beheren.

Inzicht in op rollen gebaseerde toegangscontrole in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Bouw uw eigen aangepaste rollen met Azure Cosmos DB Actions (Microsoft.DocumentDB namespace):https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Een nieuwe rol maken in Azure Active Directory:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Een maprol in Azure Active Directory met PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Leden van een directoryrol krijgen in Azure Active Directory met PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Beperk alleen de toegang van gebruikers tot gegevensbewerkingen:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

**Richtlijnen:** Het concept van standaard- of lege wachtwoorden bestaat niet met betrekking tot Azure AD of Azure Cosmos DB. In plaats daarvan gebruikt Azure Cosmos DB twee soorten sleutels om gebruikers te verifiëren en toegang te bieden tot de gegevens en bronnen. mastersleutels en resourcetokens. De sleutels kunnen op elk gewenst moment worden geregenereerd.

Inzicht in veilige toegang tot gegevens in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Azure Cosmos DB-sleutels regenereren:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Programmatisch toegang krijgen tot sleutels met Azure Active Directory:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Gebruik maken van speciale administratieve rekeningen

**Richtsnoeren**: Niet van toepassing; Azure Cosmos DB ondersteunt geen beheerdersaccounts.  Alle toegang is geïntegreerd met Azure Active Directory en Azure role-based access control (RBAC).



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik eenmalige aanmelding (SSO) met Azure Active Directory

**Richtlijnen**: Azure Cosmos DB gebruikt twee soorten sleutels om gebruikers te autoriseren en ondersteunt geen Enkele Aanmelding (SSO) op het niveau van het gegevensvlak. Toegang tot het besturingsvlak voor Cosmos DB is beschikbaar via REST API en ondersteunt SSO. Als u wilt verifiëren, stelt u de kopautorisatie voor uw aanvragen in op een JSON-webtoken dat u in azure Active Directory hebt verkregen.

Informatie over Azure Database for Cosmos DB REST API:https://docs.microsoft.com/rest/api/cosmos-db/

SSO begrijpen met Azure Active Directory:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

**Richtlijnen:** Schakel Azure Active Directory Multi-Factor Authentication in en volg aanbevelingen voor Azure Security Center Identity and Access Management.

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken binnen Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

**Richtlijnen:** Gebruik Paw (Privileged Access Workstations) met Multi-Factor Authentication geconfigureerd om in te loggen en Azure-resources te configureren.

Meer informatie over geprivilegieerde werkstations voor toegang:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten vanuit administratieve accounts

**Richtlijnen**: Gebruik Advanced Threat Protection (ATP) voor Azure Cosmos DB. ATP voor Azure Cosmos DB biedt een extra laag beveiligingsinformatie die ongebruikelijke en mogelijk schadelijke pogingen detecteert om Azure Cosmos-accounts te openen of te exploiteren. Met deze beveiligingslaag u bedreigingen aanpakken en integreren met centrale beveiligingsbewakingssystemen. 

Bovendien u Azure Active Directory (AD) Privileged Identity Management (PIM) gebruiken voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden.

Gebruik Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven.

Privileged Identity Management (PIM) implementeren:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Inzicht in Azure AD-risicodetecties:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources beheren vanaf alleen goedgekeurde locaties

**Richtlijnen**: Configureer de locatievoorwaarde van een beleid voor voorwaardelijke toegang en beheer uw benoemde locaties. Met benoemde locaties u logische groeperingen van IP-adresbereiken of landen en regio's maken. U de toegang tot gevoelige bronnen, zoals uw Azure Cosmos DB-exemplaren, beperken tot de geconfigureerde locaties met de naam.

Benoemde locaties configureren in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen:** Gebruik Azure Active Directory (AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens door sterke versleuteling te gebruiken voor gegevens in rust en onderweg. Azure AD zouten, hashes en slaat gebruikersreferenties ook veilig op.

Een Azure Active Directory-exemplaar maken en configureren:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Azure Active Directory-verificatie configureren en beheren met Azure SQL:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory biedt logboeken om verouderde accounts te ontdekken. Daarnaast u Azure Identity Access Reviews gebruiken om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. De toegang van de gebruiker kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Azure Identity Access-recensies gebruiken:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen:** U diagnostische instellingen maken voor Azure Active Directory-gebruikersaccounts, de controlelogboeken en aanmeldingslogboeken verzenden naar een werkruimte log-analyse waar u de gewenste waarschuwingen configureren.

Azure-activiteitslogboeken integreren in Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing over afwijking van het inloggedrag van het account

**Richtlijnen**: Gebruik Advanced Threat Protection (ATP) voor Azure Cosmos DB. ATP voor Azure Cosmos DB biedt een extra laag beveiligingsinformatie die ongebruikelijke en mogelijk schadelijke pogingen detecteert om Azure Cosmos-accounts te openen of te exploiteren. Met deze beveiligingslaag u bedreigingen aanpakken en integreren met centrale beveiligingsbewakingssystemen. 

U ook de functie Azure AD-identiteitsbeveiliging en risicodetecties gebruiken om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Bovendien u logboeken opnemen bij Azure Sentinel voor verder onderzoek.

Riskante aanmeldingen voor Azure Active Directory weergeven:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Hoe u risicobeleid voor identiteitsbescherming configureert en inschakelt:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen**: Momenteel niet beschikbaar; Customer Lockbox wordt nog niet ondersteund voor Azure Database voor Cosmos DB.

Lijst met door klanten ondersteunde services:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Beveiligingsbeheer: Gegevensbescherming](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

**Richtlijnen:** Gebruik tags om te helpen bij het bijhouden van Azure Cosmos DB-exemplaren die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen**: Implementeer afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Azure Cosmos DB-exemplaren worden gescheiden door virtueel netwerk/subnet, op de juiste manier gelabeld en beveiligd binnen een netwerkbeveiligingsgroep (NSG) of Azure Firewall. Azure Cosmos DB-exemplaren die gevoelige gegevens opslaan, moeten worden geïsoleerd. Door Azure Private Link te gebruiken, u verbinding maken met een Azure Cosmos DB-instantieaccount via een privéeindpunt. Het privéeindpunt is een set privé-IP-adressen in een subnet binnen uw virtuele netwerk. U de toegang tot de geselecteerde privé-IP-adressen vervolgens beperken. 

Extra Azure-abonnementen maken:https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken:https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een privéeindpunt configureren voor Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Een netwerkbeveiligingsgroep maken met een beveiligingsconfig:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

**Richtlijnen:** U Geavanceerde bedreigingsbeveiliging voor Azure Cosmos DB implementeren, die afwijkende activiteiten detecteert die ongebruikelijke en mogelijk schadelijke pogingen om databases te openen of te exploiteren. Het kan momenteel de volgende waarschuwingen activeren:

- Toegang vanaf ongebruikelijke locaties

- Ongebruikelijke gegevensextractie

Maak bovendien bij het gebruik van virtuele machines toegang tot uw Azure Cosmos DB-exemplaren, gebruik van Private Link, Firewall, netwerkbeveiligingsgroepen en servicetags om de mogelijkheid van gegevensexfiltratie te beperken. Microsoft beheert de onderliggende infrastructuur voor Azure Cosmos DB en heeft strenge controles geïmplementeerd om het verlies of de blootstelling van klantgegevens te voorkomen.

Cosmos DB Advanced Threat Protection configureren:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen**: Alle verbindingen met Azure Cosmos DB ondersteunen HTTPS. Azure Cosmos DB ondersteunt ook TLS1.2. Het is mogelijk om een minimale TLS-versie server-kant af te dwingen. Neem hiervoor contact [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)op met .

Overzicht van Cosmos DB Security:https://docs.microsoft.com/azure/cosmos-db/database-security

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

**Richtlijnen:** Automatische functies voor gegevensidentificatie, classificatie en verliespreventie zijn nog niet beschikbaar voor Azure Cosmos DB. U echter de Azure Cognitive Search-integratie gebruiken voor classificatie en gegevensanalyse. U ook een oplossing van derden implementeren indien vereist voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Azure Cosmos DB-gegevens indexeren met Azure Cognitive Search: https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp;bc=/azure/cosmos-db/breadcrumb/toc.json

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

**Richtlijnen**: Azure Cosmos DB biedt ingebouwde role-based access control (RBAC) voor veelvoorkomende beheerscenario's in Azure Cosmos DB. Een persoon die een profiel heeft in Azure Active Directory, kan deze RBAC-rollen toewijzen aan gebruikers, groepen, serviceprincipals of beheerde identiteiten om toegang te verlenen of te weigeren tot bronnen en bewerkingen op Azure Cosmos DB-bronnen. Roltoewijzingen zijn alleen toegankelijk voor beheervlak, waaronder toegang tot Azure Cosmos-accounts, databases, containers en aanbiedingen (doorvoer).

RBAC implementeren in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

Microsoft beheert de onderliggende infrastructuur voor Cosmos DB en heeft strenge controles geïmplementeerd om het verlies of de blootstelling van klantgegevens te voorkomen.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen**: Alle gebruikersgegevens die zijn opgeslagen in Cosmos DB, worden standaard in rust versleuteld. Er zijn geen besturingselementen om het uit te schakelen. Azure Cosmos DB maakt gebruik van AES-256-versleuteling in alle regio's waar het account wordt uitgevoerd.

Microsoft beheert standaard de sleutels die worden gebruikt om de gegevens in uw Azure Cosmos-account te versleutelen. U er optioneel voor kiezen om een tweede laag versleuteling toe te voegen met uw eigen sleutels.

Versleuteling in rust begrijpen met Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Inzicht in sleutelbeheer voor versleuteling in rust met Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Sleutels configureren voor uw Azure Cosmos DB-account:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen:** Gebruik Azure Monitor met het Azure Activity Log om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in productie-exemplaren van Azure Cosmos DB.

Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtlijnen:** Volg aanbevelingen van Azure Security Center voor uw Azure Cosmos DB-exemplaren. 

Microsoft voert systeempatching en kwetsbaarheidsbeheer uit op de onderliggende hosts die uw Azure Cosmos DB-exemplaren ondersteunen. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Ondersteunde functies beschikbaar in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementatie van een oplossing voor het patchbeheer van geautomatiseerd besturingssysteem

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Geautomatiseerde softwarepatchbeheeroplossing van derden implementeren

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk back-to-back kwetsbaarheid scans

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik de Azure-portal of Azure Resource Graph om alle bronnen te ontdekken (niet beperkt tot Azure Cosmos DB, maar ook bronnen zoals compute, andere opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement(en) te ontdekken.  Zorg ervoor dat u over de juiste machtigingen in uw tenant beschikt en dat u alle Azure-abonnementen en resources binnen uw abonnementen opsommen.

Hoewel klassieke Azure-bronnen kunnen worden ontdekt via Resource Graph, is het ten zeerste aan te raden om azure resource manager-resources in de toekomst te maken en te gebruiken.

Query's maken met Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weergeven:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Inzicht in Azure-toegangsbeheer op basis van rollen:https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op uw Azure Cosmos DB-exemplaren en gerelateerde bronnen met metagegevens om ze logisch te ordenen in een taxonomie.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Welke Azure Cosmos DB-bronnen ondersteunen tags:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ongeautoriseerde Azure-bronnen verwijderen

**Richtlijnen**: Gebruik tagging, beheergroepen en afzonderlijke abonnementen, waar nodig, om assets te ordenen en bij te houden, inclusief maar niet beperkt tot Azure Cosmos DB-resources. Verzoen de voorraad regelmatig en zorg ervoor dat ongeautoriseerde bronnen tijdig uit het abonnement worden verwijderd.

Extra Azure-abonnementen maken:https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Een inventaris bijhouden van goedgekeurde Azure-resources en softwaretitels

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor compute resources en Azure als geheel.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor voor niet-goedgekeurde Azure-bronnen

**Richtlijnen:** Gebruik Azure-beleid om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen

- Toegestane brontypen

Gebruik bovendien de Azure Resource Graph om bronnen binnen het abonnement(en) op te vragen/te ontdekken.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor voor niet-goedgekeurde softwaretoepassingen binnen compute resources

**Richtsnoeren**: Niet van toepassing; deze basislijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor compute resources en Azure als geheel.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="68-use-only-approved-applications"></a>6.8: Gebruik alleen goedgekeurde aanvragen

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** Gebruik Azure Policy om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen 

- Toegestane brontypen

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek brontype weigeren met Azure-beleid:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: De mogelijkheid van gebruikers beperken om via scripts met AzureResources Manager te communiceren

**Richtlijnen:** Gebruik de voorwaardelijke toegang van Azure om de interactie van gebruikers met Azure Resource Manager te beperken door 'Bloktoegang' voor de Microsoft Azure Management-app te configureren. Dit kan voorkomen dat de creatie en wijzigingen in resources binnen een hoge beveiligingsomgeving.

Voorwaardelijke toegang configureren om toegang tot Azure Resource Manager te blokkeren:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Beperk de mogelijkheid van gebruikers om scripts uit te voeren binnen compute resources

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Beveiligingsbeheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties instellen voor alle Azure-resources

**Richtlijnen:** Standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Cosmos DB-exemplaren met Azure Policy. Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.DocumentDB' om aangepaste beleidsregels te maken om de configuratie van uw Cosmos DB-exemplaren te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities voor Azure Cosmos DB, zoals:

- Geavanceerde bedreigingsbeveiliging implementeren voor DB-accounts in de kosmos

- Cosmos DB moet een eindpunt voor virtuele netwerkservice gebruiken

Beschikbare Azure-beleidsaliassen weergeven:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen voor uw Azure-bronnen af te dwingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Inzicht in Azure-beleidseffecten:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen:** Als u aangepaste Azure-beleidsdefinities gebruikt voor uw Cosmos DB of gerelateerde bronnen, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

Azure Repos-documentatie: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devopshttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.DocumentDB' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Systeemconfiguratiebeheertools implementeren voor besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-services

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.DocumentDB' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Gebruik Azure-beleid [audit], [weigeren], en [implementeren als deze niet bestaan] om configuraties voor uw Azure Cosmos DB-exemplaren en gerelateerde bronnen automatisch af te dwingen. 

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om het geheim beheer van Azure Cosmos DB te vereenvoudigen en te beveiligen voor Azure Cosmos DB-toepassingen. Zorg ervoor dat Key Vault Soft Delete is ingeschakeld.

Hoe te integreren met Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een key vault maken:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Key Vault-verificatie voorzien van een beheerde identiteit:https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om het geheim beheer van Azure Cosmos DB te vereenvoudigen en te beveiligen voor Azure Cosmos DB-toepassingen.

Beheerde identiteiten gebruiken om Azure-services een automatisch beheerde identiteit te bieden in Azure Active Directory (AD). Met Managed Identities u zich verifiëren voor elke service die Azure AD-verificatie ondersteunt, inclusief Key Vault, zonder referenties in uw code.

Beheerde identiteiten configureren:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Hoe te integreren met Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling aan referenties elimineren

**Richtlijnen**: Implementeer credential scanner om referenties binnen code te identificeren. Credential Scanner stimuleert ook het verplaatsen van ontdekte referenties naar veiligere locaties zoals Azure Key Vault.

Credential Scanner instellen:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Gebruik centraal beheerde anti-malwaresoftware

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources. Microsoft Antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op inhoud van klanten.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-resources

**Richtlijnen**: Microsoft Antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op inhoud van klanten.

Het is uw verantwoordelijkheid om bestanden die worden geüpload naar niet-compute Azure-bronnen, waaronder Azure Cosmos DB, vooraf te scannen. Microsoft heeft geen toegang tot klantgegevens en kan daarom namens u geen anti-malwarescans van klantinhoud uitvoeren.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtsnoeren**: Niet van toepassing; benchmark is bedoeld voor compute resources. Microsoft Antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt, maar wordt niet uitgevoerd op inhoud van klanten.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

**Richtlijnen**: Azure Cosmos DB maakt elke vier uur momentopnamen van uw gegevens. Alle back-ups worden afzonderlijk opgeslagen in een opslagservice en deze back-ups worden wereldwijd gerepliceerd voor tolerantie tegen regionale rampen. Op een gegeven moment blijven alleen de laatste twee momentopnamen behouden. Als de container of database echter wordt verwijderd, blijven in Azure Cosmos DB de bestaande momentopnamen van een opgegeven container of database gedurende 30 dagen behouden. Neem contact op met Azure Support om te herstellen vanuit een back-up.

Inzicht in geautomatiseerde back-ups van Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

**Richtlijnen:** Azure Cosmos DB maakt automatisch regelmatig back-ups van uw gegevens. Als de database of container wordt verwijderd, u een ondersteuningsticket indienen of Azure-ondersteuning bellen om de gegevens van automatische online back-ups te herstellen. Azure-ondersteuning is alleen beschikbaar voor geselecteerde abonnementen, zoals Standaard, Ontwikkelaar en abonnementen die hoger zijn dan deze. Als u een specifieke momentopname van de back-up wilt herstellen, vereist Azure Cosmos DB dat de gegevens beschikbaar zijn voor de duur van de back-upcyclus voor die momentopname. 

Als u Key Vault gebruikt om referenties op te slaan voor uw Cosmos DB-exemplaren, moet u ervoor zorgen dat u regelmatig geautomatiseerde back-ups van uw sleutels maakt.

Informatie over geautomatiseerde back-ups van Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Gegevens herstellen in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Een back-up maken van Key Vault Keys:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Als de database of container wordt verwijderd, u een ondersteuningsticket indienen of Azure-ondersteuning bellen om de gegevens van automatische online back-ups te herstellen. Azure-ondersteuning is alleen beschikbaar voor geselecteerde abonnementen, zoals Standaard, Ontwikkelaar en abonnementen die hoger zijn dan deze. Als u een specifieke momentopname van de back-up wilt herstellen, vereist Azure Cosmos DB dat de gegevens beschikbaar zijn voor de duur van de back-upcyclus voor die momentopname.

Test het herstel van uw geheimen die zijn opgeslagen in Azure Key Vault met PowerShell. Met de cmdlet Restore-AzureKeyVaultKey maakt u een sleutel in de opgegeven sleutelkluis. Deze sleutel is een replica van de back-upsleutel in het invoerbestand en heeft dezelfde naam als de oorspronkelijke sleutel.

Informatie over geautomatiseerde back-ups van Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Gegevens herstellen in Azure Cosmos DB:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Azure Key Vault Secrets herstellen:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

**Richtlijnen**: Omdat alle gebruikersgegevens die zijn opgeslagen in Cosmos DB in rust en in transport zijn versleuteld, hoeft u geen actie te ondernemen. Een andere manier om dit te zetten is dat encryptie in rust is "op" standaard. Er zijn geen besturingselementen om deze uit of aan te zetten. Azure Cosmos DB maakt gebruik van AES-256-versleuteling in alle regio's waar het account wordt uitgevoerd.

Schakel Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of kwaadwillige verwijdering.

Informatie over gegevensversleuteling in Azure Cosmos DB:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Soft-Delete inschakelen in Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Beveiligingscontrole: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een gids voor incidentrespons maken

**Richtlijnen**: Bouw een incidentresponseguide voor uw organisatie. Zorg ervoor dat er geschreven incidentresponseplannen zijn die alle rollen van personeel definiëren, evenals fasen van incidentafhandeling/beheer, van detectie tot beoordeling na incidenten.

U ook gebruikmaken van NIST's Computer Security Incident Handling Guide om te helpen bij het maken van uw eigen incident response plan:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Werkstroomautomatiseringen configureren binnen Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Richtlijnen voor het bouwen van uw eigen beveiligingsincidentresponsproces:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De anatomie van een incident door het Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een incidentscore- en prioriteringsprocedure maken

**Richtlijnen**: Security Center wijst een ernst toe aan elke waarschuwing om u te helpen prioriteiten te stellen welke waarschuwingen eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker het Security Center is in het vinden of de analyses die worden gebruikt om de waarschuwing uit te geven, evenals het betrouwbaarheidsniveau dat er sprake was van kwade opzet achter de activiteit die tot de waarschuwing heeft geleid.

Bovendien, duidelijk markeren abonnementen (voor bijvoorbeeld. productie, niet-prod) en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en te categoriseren.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen**: Voer oefeningen uit om de incidentresponsmogelijkheden van uw systemen op een regelmatige cadans te testen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.

Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat de gegevens van de klant zijn geraadpleegd door een onwettige of onbevoegde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

De beveiligingscontactpersoon azure security center instellen:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

**Richtlijnen:** exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue exporteren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen sentinel te streamen.

Continue export configureren:https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatiseer de reactie op beveiligingswaarschuwingen

**Richtlijnen:** Gebruik de functie Workflowautomatisering in Azure Security Center om automatisch reacties te activeren via 'Logic Apps' over beveiligingswaarschuwingen en aanbevelingen.

Workflowautomatisering en logische apps configureren:https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Security Control: Penetration Tests en Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Voer regelmatig penetratietests uit van uw Azure-resources en zorg voor herstel van alle kritieke beveiligingsbevindingen binnen 60 dagen

**Richtlijnen:** Volg de Microsoft Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Meer informatie over de strategie en uitvoering van Red Teaming en live site penetratietesten van Microsoft met microsoft beheerde cloudinfrastructuur, services en toepassingen vindt u hier:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

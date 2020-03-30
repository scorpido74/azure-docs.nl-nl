---
title: Azure-beveiligingsbasislijn voor Azure-database voor MariaDB
description: Azure-beveiligingsbasislijn voor Azure-database voor MariaDB
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d9cff47c61fc08305427b25f949884b1ef089fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289665"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azure-beveiligingsbasislijn voor Azure-database voor MariaDB

De Azure Security Baseline voor Azure Database voor MariaDB bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze service is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen**: Private Link voor Azure Database configureren voor MariaDB met privéeindpunten. Met Private Link u verbinding maken met verschillende PaaS-services in Azure via een privéeindpunt. Azure Private Link brengt in wezen Azure-services binnen uw privé-virtuele netwerk (VNet). Verkeer tussen uw virtuele netwerk en MariaDB-instantie reist het Microsoft-backbonenetwerk.

U ook eindpunten van de Virtual Network Service gebruiken om netwerktoegang tot uw Azure Database voor MariaDB-implementaties te beschermen en te beperken. Virtuele netwerkregels zijn een beveiligingsfunctie voor firewalls die bepaalt of uw Azure Database voor MariaDB communicatie accepteert die wordt verzonden vanuit bepaalde subnetten in virtuele netwerken.

U uw Azure Database voor MariaDB ook beveiligen met firewallregels. De serverfirewall voorkomt alle toegang tot uw databaseserver totdat u opgeeft welke computers toestemming hebben. U configureert de firewall door firewallregels te maken die bereiken opgeven van acceptabele IP-adressen. U firewallregels maken op serverniveau.

Private Link voor Azure Database configureren voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

VNet-serviceeindpunten en VNet-regels maken en beheren in Azure Database voor MariaDB-server:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Azure Database configureren voor MariaDB-firewallregels:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

**Richtlijnen:** Wanneer uw Azure Database voor MariaDB-server is beveiligd tot een privéeindpunt, u virtuele machines implementeren in hetzelfde virtuele netwerk. U een netwerkbeveiligingsgroep (NSG) gebruiken om het risico op gegevensexfiltratie te verminderen. Schakel NSG-stroomlogboeken in en stuur logboeken naar een opslagaccount voor verkeerscontrole. U ook NSG-stroomlogboeken naar een log-analysewerkruimte verzenden en Traffic Analytics gebruiken om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en netwerkverkeerde configuraties te lokaliseren.

Private Link voor Azure Database configureren voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

NSG-stroomlogboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal Traffic Analytics inschakelen en gebruiken:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

**Richtlijnen**: Gebruik geavanceerde bedreigingsbeveiliging voor Azure-database voor MariaDB. Advanced Threat Protection detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen om databases te openen of te exploiteren aangeven.

DDoS Protection Standard inschakelen op de virtuele netwerken die zijn gekoppeld aan uw Azure Database voor MariaDB-exemplaren om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.

Geavanceerde bedreigingsbeveiliging voor Azure-database configureren voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

DDoS-beveiliging configureren:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen:** Wanneer uw Azure Database voor MariaDB-server is beveiligd tot een privéeindpunt, u virtuele machines implementeren in hetzelfde virtuele netwerk. U vervolgens een netwerkbeveiligingsgroep (NSG) configureren om het risico op gegevensexfiltratie te verminderen. Schakel NSG-stroomlogboeken in en stuur logboeken naar een opslagaccount voor verkeerscontrole. U ook NSG-stroomlogboeken naar een log-analysewerkruimte verzenden en Traffic Analytics gebruiken om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en netwerkverkeerde configuraties te lokaliseren.

NSG-stroomlogboeken inschakelen: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal Traffic Analytics inschakelen en gebruiken:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen**: Gebruik geavanceerde bedreigingsbeveiliging voor Azure-database voor MariaDB. Advanced Threat Protection detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen om databases te openen of te exploiteren aangeven.
Geavanceerde bedreigingsbeveiliging voor Azure-database configureren voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen:** Voor resources die toegang nodig hebben tot uw Azure Database voor MariaDB-exemplaren, gebruikt u virtuele netwerkservicetags om netwerktoegangsbesturingselementen te definiëren op netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag op te geven (bijvoorbeeld SQL. WestUs) in het juiste bron- of bestemmingsveld van een regel u het verkeer voor de desbetreffende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.
Opmerking: Azure Database voor MariaDB maakt gebruik van de servicetag 'Microsoft.Sql'.

Voor meer informatie over https://docs.microsoft.com/azure/virtual-network/service-tags-overview het gebruik van servicetags: Inzicht in het gebruik van servicetags voor Azure Database voor MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkinstellingen en netwerkbronnen die zijn gekoppeld aan uw Azure Database voor MariaDB-exemplaren met Azure Policy. Gebruik Azure Policy-aliassen in de naamruimten 'Microsoft.DBforMariaDB' en 'Microsoft.Network' om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure Database voor MariaDB-instanties te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities met betrekking tot netwerken of uw Azure Database voor MariaDB-instanties, zoals:

- DDoS Protection Standard moet ingeschakeld zijn

- Privéeindpunt moet worden ingeschakeld voor MariaDB-servers

- MariaDB-server moet een eindpunt voor virtuele netwerkservice gebruiken

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Voorbeelden van Azure-beleid voor netwerken:https://docs.microsoft.com/azure/governance/policy/samples/#network

Een Azure Blueprint maken:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

**Richtlijnen:** Gebruik tags voor bronnen met betrekking tot netwerkbeveiliging en verkeersstroom voor uw MariaDB-instanties om metagegevens en logische organisatie te bieden.

Gebruik een van de ingebouwde Azure-beleidsdefinities met betrekking tot tags, zoals 'Tag en de waarde ervan vereisen' om ervoor te zorgen dat alle resources met tags worden gemaakt en om u op de hoogte te stellen van bestaande niet-gelabelde bronnen.

U Azure PowerShell of Azure CLI gebruiken om acties op te zoeken of uit te voeren op basis van resources op basis van hun tags.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik Azure Activity Log om netwerkbronconfiguraties te bewaken en wijzigingen op te sporen voor netwerkbronnen met betrekking tot uw Azure Database voor MariaDB-exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen plaatsvinden in kritieke netwerkbronnen.
Azure Activity Log-gebeurtenissen weergeven https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view en ophalen: waarschuwingen maken in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Gebruik goedgekeurde tijdsynchronisatiebronnen

**Richtlijnen**: Microsoft onderhoudt de tijdbron die wordt gebruikt voor Azure-resources, zoals Azure Database voor MariaDB voor tijdstempels in de logboeken.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboeken configureren

**Richtlijnen:** Schakel diagnostische instellingen en serverlogboeken en innamelogboeken in om beveiligingsgegevens die door uw Azure Database worden gegenereerd voor MariaDB-exemplaren te verzamelen. Gebruik in Azure Monitor Log Analytics Workspace(s) om analyses op te vragen en uit te voeren en Azure Storage Accounts te gebruiken voor opslag op lange termijn/archiverings. U ook gegevens aan boord inschakelen en aan boord maken voor Azure Sentinel of een SIEM van derden.
Serverlogboeken voor Azure Database voor MariaDB configureren en openen:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Controlelogboeken configureren en openen voor Azure https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Database voor MariaDB: Azure Sentinel aan boord nemen:https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Azure Security Center-bewaking:** niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure-resources

**Richtlijnen:** Schakel diagnostische instellingen in uw Azure Database in voor MariaDB-instanties voor toegang tot controle-, beveiligings- en diagnostische logboeken. Zorg ervoor dat u specifiek het MariaDB Audit log inschakelt. Activiteitenlogboeken, die automatisch beschikbaar zijn, bevatten gebeurtenisbron, datum, gebruiker, tijdstempel, bronadressen, bestemmingsadressen en andere nuttige elementen. U ook diagnostische instellingen voor Azure Activity Log inschakelen en de logboeken naar dezelfde log Analytics-werkruimte of opslagaccount verzenden.

Serverlogboeken voor Azure Database voor MariaDB https://docs.microsoft.com/azure/mariadb/concepts-server-logs configureren en openen: controlelogboeken configureren https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal en openen voor Azure Database voor MariaDB: Diagnostische instellingen configureren voor het Azure-activiteitenlogboek:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Azure Security Center-bewaking:** niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen:** Stel in Azure Monitor de bewaartermijn in op basis van de nalevingsvoorschriften van uw organisatie voor het bewaren van uw Azure Database voor MariaDB-logboeken. Gebruik Azure Storage Accounts voor opslag op lange termijn/archiverings.
Logboekbewaarparameters instellen voor Logboekanalysewerkruimten: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period bronlogboeken opslaan in een Azure Storage-account:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen:** Analyseer en monitor logboeken van uw MariaDB-exemplaren voor afwijkend gedrag. Gebruik de Log Analytics Workspace van Azure Monitor om logboeken te controleren en query's uit te voeren op logboekgegevens. U ook gegevens aan boord inschakelen en aan boord maken voor Azure Sentinel of een SIEM van derden.

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Voor meer informatie over de Log Analytics Workspace:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtlijnen**: Geavanceerde dreigingsbescherming voor MariaDB inschakelen. Geavanceerde bedreigingsbeveiliging voor Azure Database voor MariaDB detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen wijzen om databases te openen of te exploiteren.

Daarnaast u serverlogboeken en diagnostische instellingen voor MariaDB inschakelen en logboeken naar een Log Analytics Workspace verzenden. Aan boord van uw Log Analytics Workspace naar Azure Sentinel omdat het een SOAR-oplossing (Security Orchestration Automated Response) biedt. Hierdoor kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligingsproblemen op te lossen.

Hoe geavanceerde bescherming tegen bedreigingen voor MariaDB in te schakelen:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Serverlogboeken voor MariDB configureren en openen:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Controlelogboeken configureren en openen voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtsnoeren**: N/A; MariaDB verwerkt of produceert geen anti-malware gerelateerde logs.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtsnoeren**: N/A; MariaDB verwerkt of produceert geen DNS-gerelateerde logboeken.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

**Richtsnoeren**: N/A; benchmark is bedoeld voor compute resources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Beveiligingsbeheer: Identiteits- en toegangscontrole](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van de administratieve rekeningen

**Richtlijnen:** Houd een voorraad bij van de gebruikersaccounts die beheerderstoegang hebben tot het beheervlak (Azure portal/Azure Resource Manager) van uw MariaDB-exemplaren. Bovendien u een inventaris bijhouden van de administratieve accounts die toegang hebben tot het gegevensvlak van uw MariaDB-exemplaren. (Bij het maken van de MariaDB-server geeft u referenties op voor een beheerdersgebruiker. Deze beheerder kan worden gebruikt om extra MariaDB-gebruikers te maken.)

Inzicht in toegangsbeheer voor MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Inzicht in ingebouwde RBAC-rollen voor Azure-abonnementen:https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

**Richtlijnen:** Azure Active Directory heeft niet het concept van standaardwachtwoorden.

Bij het maken van de MariaDB-bron zelf dwingt Azure de creatie van een administratieve gebruiker met een sterk wachtwoord. Zodra het MariaDB-exemplaar is gemaakt, u echter het eerste serverbeheerdersaccount gebruiken dat u hebt gemaakt om extra gebruikers te maken en er beheerderstoegang toe te verlenen. Wanneer u deze accounts maakt, moet u voor elk account een ander, sterk wachtwoord configureren.

Extra accounts maken voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Gebruik maken van speciale administratieve rekeningen

**Richtlijnen**: Maak standaard werkprocedures rond het gebruik van speciale administratieve accounts die toegang hebben tot uw MariaDB-exemplaren. Gebruik Azure Security Center Identity and Access Management om het aantal beheerdersaccounts te controleren.

Informatie over identiteit en toegang van azure security center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik eenmalige aanmelding (SSO) met Azure Active Directory

**Richtlijnen**: Toegang tot gegevensvlak tot MariaDB wordt gecontroleerd door identiteiten die zijn opgeslagen in de database en ondersteunt sso niet. Control plane access voor MariaDB is beschikbaar via REST API en ondersteunt SSO. Als u wilt verifiëren, stelt u de kopautorisatie voor uw aanvragen in op een JSON-webtoken dat u in azure Active Directory hebt verkregen.

Informatie over Azure Database voor MariaDB REST API:https://docs.microsoft.com/rest/api/mariadb/

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

**Richtlijnen**: Schakel Azure AD MFA in en volg aanbevelingen voor Azure Security Center Identity and Access Management.

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken binnen Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

**Richtlijnen:** Gebruik PAWs (gedrenke stations met bevoorrechte toegang) met MFA geconfigureerd om in te loggen en Azure-bronnen te configureren.

Meer informatie over geprivilegieerde werkstations voor toegang:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten vanuit administratieve accounts

**Richtlijnen**: Schakel Geavanceerde bedreigingsbescherming voor MariaDB in om waarschuwingen voor verdachte activiteiten te genereren.

Bovendien u Azure AD Privileged Identity Management (PIM) gebruiken voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden. Gebruik Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven.

Geavanceerde bedreigingsbeveiliging instellen voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Privileged Identity Management (PIM) implementeren:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Inzicht in Azure AD-risicodetecties:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources beheren vanaf alleen goedgekeurde locaties

**Richtlijnen:** Gebruik locaties met voorwaardelijke toegang om toegang te verlenen vanuit alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's om de toegang tot Azure-bronnen zoals MariaDB te beperken.

Benoemde locaties configureren in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen:** Gebruik Azure Active Directory (AAD) als het centrale verificatie- en autorisatiesysteem. AAD beschermt gegevens door sterke versleuteling te gebruiken voor gegevens in rust en onderweg. AAD zouten, hashes en slaat ook veilig gebruikersreferenties op.

Azure AD-verificatie kan niet worden gebruikt voor directe toegang tot het MariaDB-gegevensvlak, maar Azure AD-referenties kunnen worden gebruikt voor beheer op beheervlakniveau (bijvoorbeeld de Azure-portal) om MariaDB-beheerdersaccounts te beheren.

Hoe het beheerderswachtwoord voor MariaDB bij te werken:https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Bekijk de Azure Active Directory-logboeken om verouderde accounts te ontdekken die mogelijk zijn met mariadb-beheerdersrollen. Gebruik bovendien Azure Identity Access Reviews om groepslidmaatschappen efficiënt te beheren, toegang tot bedrijfstoepassingen die kunnen worden gebruikt om toegang te krijgen tot MariaDB en roltoewijzingen. Gebruikerstoegang moet regelmatig worden beoordeeld, zoals elke 90 dagen om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Informatie over Azure AD-rapportage:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure Identity Access-recensies gebruiken:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen**: Schakel diagnostische instellingen voor MariaDB en Azure Active Directory in en verzend alle logboeken naar een Logboekanalysewerkruimte. De gewenste waarschuwingen (zoals mislukte verificatiepogingen) configureren in Log Analytics Workspace.

Serverlogboeken voor MariaDB configureren en openen:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Controlelogboeken configureren en openen voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Azure-activiteitslogboeken integreren in Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-bewaking:** niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing over afwijking van het inloggedrag van het account

**Richtlijnen**: Geavanceerde dreigingsbescherming voor MariaDB inschakelen. Geavanceerde bedreigingsbeveiliging voor Azure Database voor MariaDB detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen wijzen om databases te openen of te exploiteren.

Gebruik de functies Voor identiteitsbescherming en risicodetectie van Azure Active Directory om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties. U geautomatiseerde reacties via Azure Sentinel inschakelen om de beveiligingsreacties van uw organisatie te implementeren.

Hoe geavanceerde bescherming tegen bedreigingen voor MariaDB in te schakelen:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Hoe u risicobeleid voor identiteitsbescherming configureert en inschakelt:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure AD-riskante aanmeldingen weergeven:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking:** niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtsnoeren**: Niet van toepassing; Customer Lockbox wordt nog niet ondersteund voor Azure Database voor MariaDB.

Lijst met door klanten ondersteunde services:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Beveiligingsbeheer: Gegevensbescherming](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

**Richtlijnen:** Gebruik tags om te helpen bij het bijhouden van Azure Database voor MariaDB-exemplaren of gerelateerde bronnen die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen**: Implementeer afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Gebruik een combinatie van Private Link, Service Endpoints en/of MariaDB firewallregels om de netwerktoegang tot uw MariaDB-instanties te isoleren en te beperken.

Extra Azure-abonnementen maken:https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:https://docs.microsoft.com/azure/governance/management-groups/create

Private Link voor Azure Database configureren voor MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Serviceeindpunten configureren voor Azure Database voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Firewallregels voor Azure Database voor MariaDB configureren:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Azure Security Center-bewaking:** niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

**Richtlijnen:** Wanneer u Azure VM's gebruikt om toegang te krijgen tot MariaDB-exemplaren, maakt u gebruik van Private Link, MariaDB-netwerkconfiguraties, netwerkbeveiligingsgroepen en servicetags om de mogelijkheid van gegevensexfiltratie te beperken.

Microsoft beheert de onderliggende infrastructuur voor MariaDB en heeft strenge controles geïmplementeerd om het verlies of de blootstelling van klantgegevens te voorkomen.

Gegevensexfiltratie voor Azure Database voor MariaDB beperken:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen**: Azure Database voor MariaDB ondersteunt het verbinden van uw Azure Database voor MariaDB-server met clienttoepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld. Zorg er in de Azure-portal voor dat 'SSL-verbinding afdwingen' is ingeschakeld voor al uw MariaDB-exemplaren.

Versleuteling configureren tijdens het transport voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Azure Security Center-bewaking:** niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

**Richtlijnen**: Functies voor gegevensidentificatie, classificatie en verliespreventie zijn nog niet beschikbaar voor Azure Database voor MariaDB. Implementeer oplossingen van derden indien nodig voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

**Richtlijnen**: Gebruik Azure AD RBAC om de toegang tot de Azure Database voor het MariaDB-beheervlak (Azure portal/Azure Resource Manager) te beheren. Gebruik SQL-query's om gebruikers te maken en gebruikersmachtigingen te configureren voor toegang tot gegevensvlak (in de database zelf).

RBAC configureren in Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Gebruikerstoegang configureren met SQL voor MariaDB:https://docs.microsoft.com/azure/mariadb/howto-create-users

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

**Richtsnoeren**: N/A; deze richtlijn is bedoeld voor rekenresources.

Microsoft beheert de onderliggende infrastructuur voor MariaDB en heeft strenge controles geïmplementeerd om het verlies of de blootstelling van klantgegevens te voorkomen.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen**: De Azure Database for MariaDB-service maakt gebruik van de fips 140-2 gevalideerde cryptografische module voor opslagversleuteling van gegevens in rust. Gegevens, inclusief back-ups, worden versleuteld op schijf, met uitzondering van tijdelijke bestanden die zijn gemaakt tijdens het uitvoeren van query's. De service maakt gebruik van de AES 256-bits cipher die is opgenomen in Azure-opslagversleuteling en de sleutels worden beheerd door het systeem. Opslagversleuteling is altijd actief en kan niet worden uitgeschakeld.

Begrijp encryptie at-rest voor MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-security

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen:** Gebruik Azure Monitor met het Azure-activiteitenlogboek om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in productie-exemplaren van Azure Database voor MariaDB en andere kritieke of verwante bronnen.

Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtlijnen**: Momenteel niet beschikbaar; Azure Security Center ondersteunt nog geen kwetsbaarheidsbeoordeling voor Azure Database voor MariaDB-server.


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementatie van een oplossing voor het patchbeheer van geautomatiseerd besturingssysteem

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Geautomatiseerde softwarepatchbeheeroplossing van derden implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk back-to-back kwetsbaarheid scans

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden

**Richtlijnen**: Microsoft voert kwetsbaarheidsbeheer uit op de onderliggende systemen die Azure Database voor MariaDB-server ondersteunen.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik Azure Resource Graph om alle bronnen (inclusief Azure Database voor MariaDB-server) in uw abonnement(en) op te vragen en te ontdekken. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant beschikt en dat u alle Azure-abonnementen en resources binnen uw abonnementen opsommen.

Query's maken met Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weergeven:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC begrijpen:https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure Database voor MariaDB-server en andere gerelateerde bronnen die metagegevens geven om ze logisch te ordenen in een taxonomie.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ongeautoriseerde Azure-bronnen verwijderen

**Richtlijnen:** Gebruik tagging, beheergroepen en afzonderlijke abonnementen, waar nodig, om Azure Database voor MariaDB-server en gerelateerde bronnen te organiseren en bij te houden. Verzoen de voorraad regelmatig en zorg ervoor dat ongeautoriseerde bronnen tijdig uit het abonnement worden verwijderd.

Extra Azure-abonnementen maken:https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Een inventaris bijhouden van goedgekeurde Azure-resources en softwaretitels

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources en Azure als geheel.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

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

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources en Azure als geheel.



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="68-use-only-approved-applications"></a>6.8: Gebruik alleen goedgekeurde aanvragen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** Gebruik Azure-beleid om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen

- Toegestane brontypen

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek brontype weigeren met Azure-beleid:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11: <div>De mogelijkheid van gebruikers beperken om via scripts met Azure Resources Manager te communiceren</div>

**Richtlijnen:** Gebruik de voorwaardelijke toegang van Azure om de interactie van gebruikers met Azure Resource Manager te beperken door 'Bloktoegang' voor de Microsoft Azure Management-app te configureren. Dit kan voorkomen dat resources worden gemaakt en gewijzigd in resources binnen een hoogbeveiligde omgeving, zoals Azure Database voor MariaDB-server met gevoelige informatie.

Voorwaardelijke toegang configureren om toegang tot Azure Resource Manager te blokkeren:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Beperk de mogelijkheid van gebruikers om scripts uit te voeren binnen compute resources

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Beveiligingsbeheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties instellen voor alle Azure-resources

**Richtlijnen:** Standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Azure Database voor MariaDB-exemplaren met Azure Policy. Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.DBforMariaDB' om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure Database voor MariaDB-servers te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities met betrekking tot uw Azure Database voor MariaDB-servers, zoals:

- Georedundante back-up moet worden ingeschakeld voor Azure Database voor MariaDB

Beschikbare Azure-beleidsaliassen weergeven:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen voor uw Azure-bronnen af te dwingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Inzicht in Azure-beleidseffecten:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen:** Als u aangepaste Azure-beleidsdefinities gebruikt voor uw Azure Database voor MariaDB-servers en gerelateerde resources, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

Code opslaan in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos-documentatie:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.DBforMariaDB' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Systeemconfiguratiebeheertools implementeren voor besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-services

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.DBforMariaDB' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Gebruik Azure-beleid [audit], [weigeren], en [implementeren als deze niet bestaan] om configuraties voor uw Azure Database voor MariaDB-exemplaren en gerelateerde bronnen automatisch af te dwingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om Azure Database voor geheim beheer van mariaDB-servers te vereenvoudigen en te beveiligen voor Azure Virtual Machines of webtoepassingen die worden gebruikt voor het beheer van uw Azure Database voor MariaDB-servers. Zorg ervoor dat Key Vault Soft Delete is ingeschakeld.

Hoe te integreren met Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een key vault maken:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Key Vault-verificatie voorzien van een beheerde identiteit:https://docs.microsoft.com/azure/key-vault/managed-identity 



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Azure Database voor MariaDB-server biedt momenteel geen ondersteuning voor Azure Active Directory-verificatie voor toegang tot databases.  Tijdens het maken van de Azure Database voor MariaDB-server geeft u referenties op voor een beheerdersgebruiker. Deze beheerder kan worden gebruikt om extra MariaDB-gebruikers te maken.  

Voor Azure Virtual Machines of webtoepassingen die worden uitgevoerd op Azure App Service die wordt gebruikt om toegang te krijgen tot uw Azure Database voor MariaDB-server, gebruikt u Managed Service Identity in combinatie met Azure Key Vault om referenties voor Azure Database voor MariaDB op te slaan en op te halen Server.  Zorg ervoor dat Key Vault Soft Delete is ingeschakeld.

Beheerde identiteiten gebruiken om Azure-services een automatisch beheerde identiteit te bieden in Azure Active Directory (AD). Met Managed Identities u zich verifiëren voor elke service die Azure AD-verificatie ondersteunt, inclusief Key Vault, zonder referenties in uw code. Beheerde identiteiten configureren: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vmHow integreren met Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling aan referenties elimineren

**Richtlijnen**: Implementeer credential scanner om referenties binnen code te identificeren. Credential Scanner stimuleert ook het verplaatsen van ontdekte referenties naar veiligere locaties zoals Azure Key Vault. 

Credential Scanner instellen:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Gebruik centraal beheerde anti-malwaresoftware

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op inhoud van klanten.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-resources

**Richtlijnen**: Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Database voor MariaDB-server), maar wordt niet uitgevoerd op inhoud van klanten.

Scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-bronnen, zoals App Service, Data Lake Storage, Blob Storage, Azure Database voor MariaDB-server, enz. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Database voor MariaDB-server), maar wordt niet uitgevoerd op inhoud van klanten.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

**Richtlijnen:** Azure Database voor MariaDB maakt volledige, differentiële en transactielogboekback-ups.  Azure Database voor MariaDB maakt automatisch serverback-ups en slaat deze op in lokaal redundante of georedundante opslag van de gebruiker. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen. Back-ups en herstel zijn een essentieel onderdeel van elke bedrijfscontinuïteitsstrategie omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.  De standaardbewaarperiode voor back-ups is zeven dagen. U het optioneel tot 35 dagen configureren. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Begrijp back-ups voor MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Begrijp mariadb initiële configuratie:https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

**Richtlijnen:** Azure Database voor MariaDB maakt automatisch serverback-ups en slaat deze op in lokaal redundante of georedundante opslag van de gebruiker. Back-ups kunnen worden gebruikt om de status van de server naar een bepaald tijdstip te herstellen.  Back-ups en herstel zijn een essentieel onderdeel van elke bedrijfscontinuïteitsstrategie omdat ze uw gegevens beschermen tegen onbedoelde beschadiging of verwijdering.

Als u Key Vault gebruikt voor gegevensversleuteling aan de clientzijde voor gegevens die zijn opgeslagen in uw MariaDB-server, moet u ervoor zorgen dat u regelmatig geautomatiseerde back-ups van uw sleutels maakt.

Begrijp back-ups voor MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup

Een back-up maken van Key Vault Keys:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Voer in Azure Database for MariaDB een herstel uit van de back-ups van de oorspronkelijke server voor het periodiek testen van back-ups. Er zijn twee soorten herstel beschikbaar: Point-in-time restore en Geo-restore. Point-in-time restore is beschikbaar met een back-up redundantieoptie en maakt een nieuwe server in dezelfde regio als de oorspronkelijke server. Geo-restore is alleen beschikbaar als u uw server hebt geconfigureerd voor georedundante opslag en u uw server naar een andere regio herstellen.

De geschatte hersteltijd is afhankelijk van verschillende factoren, waaronder de databasegrootte, de grootte van het transactielogboek, de netwerkbandbreedte en het totale aantal databases dat tegelijkertijd in dezelfde regio herstelt. De hersteltijd is meestal minder dan 12 uur.

Begrijp back-up en herstel in Azure Database voor MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

**Richtlijnen:** Azure Database voor MariaDB maakt volledige, differentiële en transactielogboekback-ups. Met deze back-ups u een server herstellen naar elk point-in-time binnen uw geconfigureerde back-upbewaarperiode. De standaardbewaarperiode voor back-ups is zeven dagen. U het optioneel tot 35 dagen configureren. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Begrijp back-up en herstel in Azure Database voor MariaDB:https://docs.microsoft.com/azure/mariadb/concepts-backup


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Beveiligingscontrole: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een gids voor incidentrespons maken

**Richtlijnen**: Bouw een incidentresponseguide voor uw organisatie. Zorg ervoor dat er geschreven incidentresponseplannen zijn die alle rollen van personeel definiëren, evenals fasen van incidentafhandeling/beheer, van detectie tot beoordeling na incidenten.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een incidentscore- en prioriteringsprocedure maken

**Richtlijnen**: Security Center wijst een ernst toe aan elke waarschuwing om u te helpen prioriteiten te stellen welke waarschuwingen eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

Gebruik tags om uw Azure-bronnen te ordenen:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen:** Voer oefeningen uit om de incidentresponsemogelijkheden van uw systemen te testen op een regelmatige cadans om uw Azure-resources te beschermen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat uw gegevens zijn geopend door een onwettige of onbevoegde partij. Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.
    

    How to set the Azure Security Center Security Contact: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

**Richtlijnen:** exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue export om risico's voor Azure-resources te identificeren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatiseer de reactie op beveiligingswaarschuwingen

**Richtlijnen:** Gebruik de functie Werkstroomautomatisering in Azure Security Center om automatisch reacties te activeren via 'Logic Apps' over beveiligingswaarschuwingen en aanbevelingen om uw Azure-bronnen te beschermen.
    

Workflowautomatisering en logische apps configureren:https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Security Control: Penetration Tests en Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Voer regelmatig penetratietests uit van uw Azure-resources en zorg voor herstel van alle kritieke beveiligingsbevindingen binnen 60 dagen

**Richtlijnen:** Volg de Microsoft Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Meer informatie over de strategie en uitvoering van Red Teaming en live site penetratietesten van Microsoft met microsoft beheerde cloudinfrastructuur, services en toepassingen vindt u hier:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

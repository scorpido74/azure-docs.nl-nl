---
title: Azure-beveiligingsbasislijn voor HDInsight
description: Azure-beveiligingsbasislijn voor HDInsight
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 93a5bcd77bb4f42d9099cc1ddb1b5c3130c19059
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010134"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azure-beveiligingsbasislijn voor HDInsight

De Azure Security Baseline voor HDInsight bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze service is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen**: Perimeterbeveiliging in Azure HDInsight wordt bereikt via virtuele netwerken. Een bedrijfsbeheerder kan een cluster binnen een virtueel netwerk maken en een netwerkbeveiligingsgroep (NSG) gebruiken om de toegang tot het virtuele netwerk te beperken. Alleen de toegestane IP-adressen in de regels van de binnenkomende netwerkbeveiligingsgroep kunnen communiceren met het Azure HDInsight-cluster. Deze configuratie biedt perimeterbeveiliging. Alle clusters die in een virtueel netwerk worden geïmplementeerd, hebben ook een privéeindpunt dat wordt opgelost naar een privé-IP-adres in het virtuele netwerk voor privé-HTTP-toegang tot de clustergateways.

Als u het risico op gegevensverlies via exfiltratie wilt verminderen, beperkt u uitgaand netwerkverkeer voor Azure HDInsight-clusters met Azure Firewall.

Azure HDInsight implementeren in een virtueel netwerk en beveiligen met een netwerkbeveiligingsgroep:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Uitgaand verkeer voor Azure HDInsight-clusters beperken met Azure Firewall:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

**Richtlijnen:** Gebruik Azure Security Center en herstel de aanbevelingen voor netwerkbeveiliging voor de virtuele netwerk-, subnet- en netwerkbeveiligingsgroep die wordt gebruikt om uw Azure HDInsight-cluster te beveiligen. Schakel nsg-stroomlogboeken (Network Security Group) in en stuur logboeken naar een Azure Storage Account naar verkeerscontrole. U ook NSG-stroomlogboeken naar een Azure Log Analytics Workspace verzenden en Azure Traffic Analytics gebruiken om inzicht te bieden in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Azure Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en foutieve netwerkconfiguraties te lokaliseren.

NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Inzicht in netwerkbeveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

**Richtsnoeren**: Niet van toepassing; benchmark is bedoeld voor Azure Apps Service of compute resources die webtoepassingen hosten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

**Richtlijnen:** Voor beveiligingen tegen DDoS-aanvallen schakelt u Azure DDoS-standaardbeveiliging in op het virtuele netwerk waar uw Azure HDInsight is geïmplementeerd. Gebruik geïntegreerde bedreigingsinformatie van Azure Security Center om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.

DDoS-beveiliging configureren:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Begrijp azure security center integrated threat intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen:** Nsg-logboeken (Network Security Group) inschakelen voor de NSG die is gekoppeld aan het subnet dat wordt gebruikt om uw Azure HDInsight-cluster te beschermen. Neem de NSG-stroomlogboeken op in een Azure Storage-account om stroomrecords te genereren. Schakel Azure Network Watcher-pakketopname in indien nodig voor het onderzoeken van afwijkende activiteiten.

NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Zo schakel je Network Watcher in:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen**: Vereiste kan worden voldaan aan Azure-beveiligingscontrole-id 1.1; Implementeer Azure HDInsight-cluster in een virtueel netwerk en beveilig met een NETWERKbeveiligingsgroep (NSG).

Er zijn verschillende afhankelijkheden voor Azure HDInsight waarvoor binnenkomend verkeer nodig is. Het binnenkomende beheerverkeer kan niet via een firewall-apparaat worden verzonden. De bronadressen voor vereist beheerverkeer zijn bekend en gepubliceerd. Maak regels voor netwerkbeveiliging met deze informatie om verkeer van alleen vertrouwde locaties toe te staan en binnenkomend verkeer naar de clusters te beveiligen.

Als u het risico op gegevensverlies via exfiltratie wilt verminderen, beperkt u uitgaand netwerkverkeer voor Azure HDInsight-clusters met Azure Firewall.

HDInsight implementeren binnen een virtueel netwerk en beveiligd met een netwerkbeveiligingsgroep:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

HdInsight-afhankelijkheden en firewallgebruik begrijpen:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

IP-adressen van HDInsight-beheer:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtsnoeren**: Niet van toepassing; benchmark is bedoeld voor Azure Apps Service of compute resources die webtoepassingen hosten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen:** Gebruik virtuele netwerkservicetags om netwerktoegangsbesturingselementen te definiëren voor netwerkbeveiligingsgroepen (NSG) die zijn gekoppeld aan het subnet waarin uw Azure HDInsight-cluster is geïmplementeerd. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of bestemmingsveld van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

Servicetags voor Azure HDInsight begrijpen en gebruiken:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkbronnen die gerelateerd zijn aan uw Azure HDInsight-cluster. Gebruik Azure Policy-aliassen in de naamruimten 'Microsoft.HDInsight' en 'Microsoft.Network' om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure HDInsight-cluster te controleren of af te dwingen.

U Azure Blueprints ook gebruiken om azure-implementaties op grote schaal te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Azure Resource Manager-sjablonen, RBAC-besturingselementen en beleidsregels, in één blauwdrukdefinitie. Pas de blauwdruk eenvoudig toe op nieuwe abonnementen en omgevingen en stem de besturing en het beheer af via versiebeheer.

Beschikbare Azure-beleidsaliassen weergeven:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een Azure Blueprint maken:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

**Richtlijnen:** Gebruik tags voor de netwerkbeveiligingsgroep (NSG's) en andere bronnen met betrekking tot netwerkbeveiliging en verkeersstroom die zijn gekoppeld aan uw Azure HDInsight-cluster. Voor afzonderlijke NSG-regels gebruikt u het veld 'Beschrijving' om de bedrijfsbehoefte en/of -duur (enz.) op te geven voor regels die verkeer van/naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure-beleidsdefinities met betrekking tot tags, zoals 'Tag en de waarde ervan vereisen' om ervoor te zorgen dat alle resources met tags worden gemaakt en om u op de hoogte te stellen van bestaande niet-gelabelde bronnen.

U Azure PowerShell of Azure command-line interface (CLI) gebruiken om acties op te zoeken of uit te voeren op basis van resources op basis van hun tags.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een virtueel netwerk maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een Beveiligingsconfig:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik Azure Activity Log om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren voor netwerkbronnen die verband houden met uw Azure HDInsight-implementaties. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen plaatsvinden in kritieke netwerkbronnen.

Azure Activity Log-gebeurtenissen weergeven en ophalen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Gebruik goedgekeurde tijdsynchronisatiebronnen

**Richtlijnen**: Microsoft onderhoudt tijdsbronnen voor Azure HDInsight-clustercomponenten, u tijdsynchronisatie bijwerken voor uw compute-implementaties.

Tijdsynchronisatie configureren voor Azure-rekenbronnen:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboeken configureren

**Richtlijnen:** U uw Azure HDInsight-cluster aan boord nemen van Azure Monitor om beveiligingsgegevens die door het cluster worden gegenereerd, te verzamelen. Maak gebruik van aangepaste query's om bedreigingen in de omgeving te detecteren en erop te reageren. 

Een Azure HDInsight-cluster aan boord maken van Azure HDInsight naar Azure Monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Aangepaste query's maken voor een Azure HDInsight-cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure-resources

**Richtlijnen**: Azure Monitor inschakelen voor het HDInsight-cluster, doorsturen naar een Log Analytics-werkruimte. Hiermee worden relevante clusterinformatie en OS-statistieken voor alle Azure HDInsight-clusterknooppunten bijgedeeld.

Logboekregistratie inschakelen voor HDInsight Cluster:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

HdInsight-logboeken opvragen:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtlijnen**: Azure HDInsight-cluster ingebruikmaken voor Azure Monitor. Controleer of de gebruikte log analytics-werkruimte de bewaartermijn voor logboeken heeft ingesteld volgens de nalevingsvoorschriften van uw organisatie.

Een Azure HDInsight-cluster aan boord maken van Azure-monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

De bewaarperiode van logboekanalysewerkruimte configureren:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen**: Azure HDInsight-cluster ingebruikmaken voor Azure Monitor. Controleer of de gebruikte Azure Log Analytics-werkruimte de bewaartermijn voor logboeken heeft ingesteld volgens de nalevingsvoorschriften van uw organisatie.

Een Azure HDInsight-cluster aan boord maken van Azure-monitor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

De bewaarperiode van logboekanalysewerkruimte configureren:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen:** Gebruik Azure Log Analytics-werkruimtequery's om Azure HDInsight-logboeken op te vragen:

Aangepaste query's maken voor Azure HDInsight-clusters:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtlijnen:** Gebruik azure log analytics-werkruimte voor het bewaken en waarschuwen van afwijkende activiteiten in beveiligingslogboeken en gebeurtenissen die verband houden met uw Azure HDInsight-cluster.

Waarschuwingen beheren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Hoe u logboekgegevens waarschuwen voor logboekanalyse:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtlijnen**: Azure HDInsight wordt geleverd met Clamscan vooraf geïnstalleerd en ingeschakeld voor de clusterknooppuntafbeeldingen, maar u moet de software beheren en alle logboeken die Clamscan produceert handmatig samenvoegen/controleren.

Begrijp Clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtlijnen**: Implementeer een oplossing van derden voor dns-logging.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

**Richtlijnen**: Handmatig consolelogboekregistratie configureren op basis van een knooppunt.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Beveiligingsbeheer: Identiteits- en toegangscontrole](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van de administratieve rekeningen

**Richtlijnen:** houd het lokale beheerdersaccount bij dat is gemaakt tijdens de clusterinrichting van het Azure HDInsight-cluster en alle andere accounts die u maakt. Als Azure AD-integratie wordt gebruikt, heeft Azure AD bovendien ingebouwde rollen die expliciet moeten worden toegewezen en daarom queryable zijn. Gebruik de Azure AD PowerShell-module om adhocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

Daarnaast u aanbevelingen voor Azure Security Center Identity and Access Management gebruiken.

Een maprol in Azure AD met PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Leden van een directoryrol in Azure AD krijgen met PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Identiteit en toegang controleren met Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

**Richtlijnen:** Azure vereist dat u bij het inrichten van een cluster nieuwe wachtwoorden maakt voor de webportal en De Toegang tot Secure Shell (SSH). Er zijn geen standaardwachtwoorden om te wijzigen, maar u verschillende wachtwoorden opgeven voor SSH- en webportaltoegang.

Wachtwoorden instellen bij het inrichten van een Azure HDInsight-cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Gebruik maken van speciale administratieve rekeningen

**Richtlijnen**: Integreer verificatie voor Azure HDInsight-cluster met Azure Active Directory. Maak beleid en procedures rond het gebruik van specifieke administratieve accounts.

Daarnaast u aanbevelingen voor Azure Security Center Identity and Access Management gebruiken.

Azure HDInsight-verificatie integreren met Azure Active Directory:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Identiteit en toegang controleren met Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik eenmalige aanmelding (SSO) met Azure Active Directory

**Richtlijnen:** Gebruik Azure HDInsight ID Broker om u aan te melden bij ESP-clusters (Enterprise Security Package) met behulp van Multi-Factor Authentication, zonder wachtwoorden te verstrekken. Als u zich al hebt aangemeld bij andere Azure-services, zoals de Azure-portal, u zich aanmelden bij uw Azure HDInsight-cluster met één SSO-ervaring (sign-on).

Azure HDInsight ID Broker inschakelen:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

**Richtlijnen**: Schakel Azure AD MFA in en volg aanbevelingen voor Azure Security Center Identity and Access Management. Azure HDInsight-clusters met het enterprise securitypakket geconfigureerd, kunnen worden verbonden met een domein, zodat domeingebruikers hun domeinreferenties kunnen gebruiken om te verifiëren met de clusters en big data-taken uit te voeren. Wanneer u authenticeren met multi-factor authentication (MFA) ingeschakeld, worden gebruikers uitgedaagd om een tweede verificatiefactor te bieden.

Mfa inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken binnen Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

**Richtlijnen:** Gebruik PAWs (privileged access workstations) met multi-factor authenticatie (MFA) geconfigureerd om in te loggen en configureren van uw Azure HDInsight clusters en gerelateerde resources.

Meer informatie over geprivilegieerde werkstations voor toegang:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Mfa inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten vanuit administratieve accounts

**Richtlijnen**: Azure HDInsight-clusters met het enterprise securitypakket geconfigureerd, kunnen worden verbonden met een domein, zodat domeingebruikers hun domeinreferenties kunnen gebruiken om te verifiëren. U Azure Active Directory (AAD)-beveiligingsrapporten gebruiken voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten plaatsvinden in de AAD-omgeving. Gebruik Azure Security Center om identiteits- en toegangsactiviteiten te controleren.

Aad-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Hoe u de identiteit van gebruikers controleren en activiteiten openen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources beheren vanaf alleen goedgekeurde locaties

**Richtlijnen**: Azure HDInsight-clusters met het enterprise securitypakket geconfigureerd, kunnen worden verbonden met een domein, zodat domeingebruikers hun domeinreferenties kunnen gebruiken om te verifiëren. Gebruik locaties met voorwaardelijke toegang om alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's toe te staan.

Benoemde locaties configureren in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen:** Gebruik Azure Active Directory (AAD) als het centrale verificatie- en autorisatiesysteem. AAD beschermt gegevens door sterke versleuteling te gebruiken voor gegevens in rust en onderweg. AAD zouten, hashes en slaat ook veilig gebruikersreferenties op.

Azure HDInsight-clusters met Enterprise Security Package (ESP) geconfigureerd kunnen worden aangesloten op een domein, zodat domeingebruikers hun domeinreferenties kunnen gebruiken om te verifiëren met de clusters.

Een AAD-exemplaar maken en configureren:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Enterprise Security Package configureren met Azure Active Directory Domain Services in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Gebruik Azure Active Directory (AAD)-verificatie met uw Azure HDInsight-cluster. AAD biedt logboeken om verouderde accounts te ontdekken. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. De toegang van de gebruiker kan regelmatig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

Azure Identity Access-recensies gebruiken:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen:** Gebruik azure Active Directory (AAD) Aanmeldings- en controlelogboeken om te controleren op pogingen om toegang te krijgen tot gedeactiveerde accounts; deze logboeken kunnen worden geïntegreerd in elke SIEM/monitoringtool van derden.

U dit proces stroomlijnen door diagnostische instellingen voor AAD-gebruikersaccounts te maken, de controlelogboeken en aanmeldingslogboeken naar een Azure Log Analytics-werkruimte te verzenden. De gewenste waarschuwingen configureren binnen de Azure Log Analytics-werkruimte.

Azure-activiteitslogboeken integreren in Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing over afwijking van het inloggedrag van het account

**Richtlijnen**: Azure HDInsight-clusters met Enterprise Security Package (ESP) geconfigureerd kunnen worden aangesloten op een domein, zodat domeingebruikers hun domeinreferenties kunnen gebruiken om te verifiëren met de clusters.  Gebruik de functie Azure Active Directory (AAD) Risicodetecties en Identiteitsbeveiliging om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Bovendien u gegevens opnemen in Azure Sentinel voor verder onderzoek.

Aad-riskante aanmeldingen bekijken:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Hoe u risicobeleid voor identiteitsbescherming configureert en inschakelt:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen**: Niet beschikbaar; Customer Lockbox wordt nog niet ondersteund voor Azure HDInsight.

Lijst met door klanten ondersteunde services:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Beveiligingsbeheer: Gegevensbescherming](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

**Richtlijnen:** Gebruik tags op resources die betrekking hebben op uw Azure HDInsight-implementaties om te helpen bij het bijhouden van Azure-bronnen die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen**: Implementeer afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Azure HDInsight-clusters en bijbehorende opslagaccounts moeten worden gescheiden door een virtueel netwerk/subnet, op de juiste manier worden getagd en beveiligd binnen een NETWERKbeveiligingsgroep (NSG) of Azure Firewall. Clustergegevens moeten worden opgenomen in een beveiligd Azure Storage-account of Azure Data Lake Storage (Gen1 of Gen2).

Kies opslagopties voor uw Azure HDInsight-cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Azure Data Lake Storage beveiligen:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Azure-opslagaccounts beveiligen:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

**Richtlijnen:** Voor Azure HDInsight-clusters die gevoelige informatie opslaan of verwerken, markeert u het cluster en de bijbehorende bronnen als gevoelig met behulp van tags. Als u het risico op gegevensverlies via exfiltratie wilt verminderen, beperkt u uitgaand netwerkverkeer voor Azure HDInsight-clusters met Azure Firewall.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Uitgaand verkeer voor Azure HDInsight-clusters beperken met Azure Firewall:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Inzicht in de bescherming van klantgegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen**: Versleutel alle gevoelige informatie tijdens het transport. Zorg ervoor dat clients die verbinding maken met uw Azure HDInsight-cluster- of clustergegevensopslag (Azure Storage Accounts of Azure Data Lake Storage Gen1/Gen2) kunnen onderhandelen over TLS 1.2 of hoger. Microsoft Azure-bronnen onderhandelen standaard over TLS 1.2. 

Informatie over Azure Data Lake Storage-versleuteling onderweg:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Informatie over Azure Storage Account-versleuteling onderweg:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

**Richtlijnen**: Functies voor gegevensidentificatie, classificatie en verliespreventie zijn nog niet beschikbaar voor Azure Storage- of compute resources. Implementeer oplossingen van derden indien nodig voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

**Richtlijnen:** Met Azure HDInsight Enterprise Security Package (ESP) u Apache Ranger gebruiken om fijnkorrelig toegangsbeheer en gegevensverduisteringsbeleid te maken en te beheren voor uw gegevens die zijn opgeslagen in bestanden, mappen, databases, tabellen en rijen/kolommen. De hadoop-beheerder kan op rollen gebaseerde toegangscontrole (RBAC) configureren om Apache Hive, HBase, Kafka en Spark te beveiligen met behulp van deze plug-ins in Apache Ranger.

Als u RBAC-beleidsregels configureert met Apache Ranger, u machtigingen koppelen aan een rol in de organisatie. Deze abstractielaag maakt het gemakkelijker om ervoor te zorgen dat mensen alleen de machtigingen hebben die nodig zijn om hun werkverantwoordelijkheden uit te voeren.

Enterprise Security Package-configuraties met Azure Active Directory Domain Services in HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Overzicht van bedrijfsbeveiliging in Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

**Richtlijnen:** Voor Azure HDInsight-clusters die gevoelige informatie opslaan of verwerken, markeert u het cluster en de bijbehorende bronnen als gevoelig met behulp van tags. Gegevensidentificatie, classificatie en verliespreventiezijn nog niet beschikbaar voor Azure Storage- of compute resources. Implementeer oplossingen van derden indien nodig voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen:** Als u Azure SQL Database gebruikt om apache hive- en Apache Oozie-metagegevens op te slaan, moet u ervoor zorgen dat SQL-gegevens te allen tijde versleuteld blijven. Voor Azure Storage Accounts and Data Lake Storage (Gen1 of Gen2) wordt aanbevolen om Microsoft toe te staan uw versleutelingssleutels te beheren, maar u hebt de mogelijkheid om uw eigen sleutels te beheren.

Versleutelingssleutels voor Azure Storage-accounts beheren:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

Azure Data Lake Storage maken met door klanten beheerde versleutelingssleutels:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Versleuteling voor Azure SQL Database begrijpen:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

Transparent Data Encryption for SQL Database configureren met door de klant beheerde sleutels:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen**: Diagnostische instellingen configureren voor Azure Storage Accounts die zijn gekoppeld aan Azure HDInsight-clusters om alle CRUD-bewerkingen te controleren en te registreren tegen clustergegevens. Controle inschakelen voor opslagaccounts of Gegevenslake-opslag die zijn gekoppeld aan het Azure HDInsight-cluster.

Extra logboekregistratie/controle inschakelen voor een Azure Storage-account:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Extra logboekregistratie/controle inschakelen voor Azure Data Lake Storage:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtlijnen**: Implementeer een oplossing voor kwetsbaarheidsbeheer van derden.

Als u een Rapid7-, Qualys- of een ander abonnement op het kwetsbaarheidsbeheerplatform hebt, u mogelijk scriptacties gebruiken om beveiligingsbeoordelingsagents op uw Azure HDInsight-clusterknooppunten te installeren en de knooppunten te beheren via de desbetreffende portal.

Rapid7-agent handmatig installeren:

https://insightvm.help.rapid7.com/v1.0/docs/agent-installation-on-linux

Hoe qualys agent handmatig te installeren:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

Scriptacties gebruiken:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementatie van een oplossing voor het patchbeheer van geautomatiseerd besturingssysteem

**Richtlijnen:** Automatische systeemupdates zijn ingeschakeld voor clusterknooppuntafbeeldingen, maar u moet clusterknooppunten periodiek opnieuw opstarten om ervoor te zorgen dat updates worden toegepast.

Microsoft voor het onderhouden en bijwerken van azure HDInsight-knooppuntafbeeldingen.

Het patchschema voor besturingssysteem configureren voor HDInsight-clusters:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Geautomatiseerde softwarepatchbeheeroplossing van derden implementeren

**Richtlijnen:** Gebruik scriptacties of andere mechanismen om uw Azure HDInsight-clusters te patchen. Nieuw gemaakte clusters hebben altijd de nieuwste beschikbare updates, inclusief de meest recente beveiligingspatches.

Het patchschema voor het besturingssysteem configureren voor Azure HDInsight-clusters op basis van Linux:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

Scriptacties gebruiken:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk back-to-back kwetsbaarheid scans

**Richtlijnen**: Implementeer een oplossing voor kwetsbaarheidsbeheer van derden die de mogelijkheid heeft om kwetsbaarheidsscans in de loop van de tijd te vergelijken. Als u een Rapid7- of Qualys-abonnement hebt, u de portal van die leverancier gebruiken om back-to-back-kwetsbaarheidsscans te bekijken en te vergelijken.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden

**Richtlijnen:** Gebruik een gemeenschappelijk risicoscoreprogramma (bijvoorbeeld Common Vulnerability Scoring System) of de standaardrisicobeoordelingen van uw scantool van derden.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik Azure Resource Graph om alle bronnen (zoals compute, storage, netwerk, poorten en protocollen, enz.), inclusief Azure HDInsight-clusters, binnen uw abonnement(en) op te vragen/te ontdekken.  Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant beschikt en dat u alle Azure-abonnementen en resources binnen uw abonnementen opsommen.

Hoewel klassieke Azure-bronnen kunnen worden ontdekt via Resource Graph, is het ten zeerste aan te raden om azure resource manager-resources in de toekomst te maken en te gebruiken.

Query's maken met Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weergeven:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC begrijpen:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

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

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor voor niet-goedgekeurde Azure-bronnen

**Richtlijnen:** Gebruik Azure-beleid om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen

- Toegestane brontypen

Gebruik Azure Resource Graph om bronnen binnen uw abonnement(en) te zoeken/ontdekken. Controleer of alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor voor niet-goedgekeurde softwaretoepassingen binnen compute resources

**Richtlijnen:** Implementeer een oplossing van derden om clusterknooppunten voor niet-goedgekeurde softwaretoepassingen te controleren.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

**Richtlijnen:** Gebruik Azure Resource Graph om alle bronnen (zoals compute, storage, netwerk, poorten en protocollen, enz.), inclusief Azure HDInsight-clusters, binnen uw abonnement(en) op te vragen/te ontdekken.  Verwijder niet-goedgekeurde Azure-bronnen die u ontdekt. Implementeer voor Azure HDInsight-clusterknooppunten een oplossing van derden om niet-goedgekeurde software te verwijderen of te waarschuwen.

Query's maken met Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="68-use-only-approved-applications"></a>6.8: Gebruik alleen goedgekeurde aanvragen

**Richtlijnen:** Implementeer voor Azure HDInsight-clusterknooppunten een oplossing van derden om te voorkomen dat ongeautoriseerde software wordt uitgevoerd.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** Gebruik Azure-beleid om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen

- Toegestane brontypen

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek brontype weigeren met Azure-beleid:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtlijnen:** Implementeer voor Azure HDInsight-clusterknooppunten een oplossing van derden om te voorkomen dat ongeautoriseerde bestandstypen worden uitgevoerd.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: De mogelijkheid van gebruikers beperken om via scripts met Azure Resources Manager te communiceren

**Richtlijnen:** Gebruik voorwaardelijke toegang voor Azure om de interactie van gebruikers met Azure Resource Manager te beperken door 'Bloktoegang' voor de Microsoft Azure Management-app te configureren.

Voorwaardelijke toegang configureren om toegang tot Azure Resource Manager te blokkeren:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Beperk de mogelijkheid van gebruikers om scripts uit te voeren binnen compute resources

**Richtsnoeren**: Niet van toepassing; Dit is niet van toepassing op Azure HDInsight omdat gebruikers (niet-beheerders) van het cluster geen toegang tot de afzonderlijke knooppunten nodig hebben om taken uit te voeren. De clusterbeheerder heeft roottoegang tot alle clusterknooppunten.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtsnoeren**: Niet van toepassing; benchmark is bedoeld voor Azure Apps Service of compute resources die webtoepassingen hosten.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Beveiligingsbeheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties instellen voor alle Azure-resources

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.HDInsight' om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw HDInsight-cluster te controleren of af te dwingen.

Beschikbare Azure-beleidsaliassen weergeven:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

**Richtlijnen**: Azure HDInsight Operating System Images beheerd en onderhouden door Microsoft. Klant verantwoordelijk voor het implementeren van beveiligde configuraties voor het besturingssysteem van uw clusterknooppunten. 


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om veilige instellingen af te dwingen voor uw Azure HDInsight-clusters en gerelateerde bronnen.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Inzicht in Azure-beleidseffecten:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

**Richtlijnen**: Azure HDInsight Operating System Images beheerd en onderhouden door Microsoft. Klant verantwoordelijk voor het implementeren van de statusconfiguratie op OS-niveau.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen:** Als u aangepaste Azure-beleidsdefinities gebruikt, gebruikt u Azure DevOps of Azure Repos om uw code veilig op te slaan en te beheren.

Code opslaan in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos-documentatie:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtsnoeren**: Niet van toepassing; aangepaste afbeeldingen die niet van toepassing zijn op Azure HDInsight.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.HDInsight' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Systeemconfiguratiebeheertools implementeren voor besturingssystemen

**Richtlijnen:** Implementeer een oplossing van derden om de gewenste status voor uw clusterknooppuntbesturingssystemen te behouden.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-services

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.HDInsight' om aangepaste beleidsregels te maken om de configuratie van uw HDInsight-cluster te controleren of af te dwingen.

Beschikbare Azure-beleidsaliassen weergeven:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtlijnen:** Implementeer een oplossing van derden om de status van uw clusterknooppuntbesturingssystemen te controleren.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen**: Azure HDInsight bevat BYOK-ondersteuning (Bring Your Own Key) voor Apache Kafka. Met deze mogelijkheid u de sleutels bezitten en beheren die worden gebruikt om gegevens in rust te versleutelen.

Alle beheerde schijven in Azure HDInsight zijn beveiligd met Azure Storage Service Encryption (SSE). Standaard worden de gegevens op die schijven versleuteld met door Microsoft beheerde sleutels. Als u BYOK inschakelt, geeft u de versleutelingssleutel op voor Azure HDInsight om deze te gebruiken en te beheren met Azure Key Vault.

Key Vault kan ook worden gebruikt met Azure HDInsight-implementaties om sleutels voor clusteropslag (Azure Storage Accounts en Azure Data Lake Storage) te beheren.

Hoe breng je je eigen sleutel voor Apache Kafka mee op Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Versleutelingssleutels voor Azure Storage-accounts beheren:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen**: Beheerde identiteiten kunnen worden gebruikt in Azure HDInsight om uw clusters toegang te geven tot Azure Active Directory-domeinservices, toegang tot Azure Key Vault of toegang tot bestanden in Azure Data Lake Storage Gen2.

Beheerde identiteiten begrijpen met Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling aan referenties elimineren

**Richtlijnen:** Als u code gebruikt die betrekking heeft op uw Azure HDInsight-implementatie, u referentiescanner implementeren om referenties binnen code te identificeren. Credential Scanner stimuleert ook het verplaatsen van ontdekte referenties naar veiligere locaties zoals Azure Key Vault. 

Credential Scanner instellen:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Gebruik centraal beheerde anti-malwaresoftware

**Richtlijnen**: Azure HDInsight wordt geleverd met Clamscan vooraf geïnstalleerd en ingeschakeld voor de clusterknooppuntafbeeldingen, maar u moet de software beheren en alle logboeken die Clamscan produceert handmatig samenvoegen/controleren.

Informatie over Clamscan voor Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-resources

**Richtlijnen:** Microsoft Antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt, maar wordt niet uitgevoerd op inhoud van klanten.

Scan vooraf bestanden die worden geüpload naar Azure-bronnen die verband houden met uw Azure HDInsight-clusterimplementatie, zoals Data Lake Storage, Blob Storage, enz. Microsoft heeft in deze gevallen geen toegang tot klantgegevens.

Microsoft Antimalware voor Azure Cloud Services en virtuele machines begrijpen:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtlijnen**: Azure HDInsight wordt geleverd met Clamscan vooraf geïnstalleerd en ingeschakeld voor de clusterknooppuntafbeeldingen. Clamscan voert automatisch motor- en definitie-updates uit, maar aggregatie en beheer van logboeken moeten handmatig worden uitgevoerd.

Informatie over Clamscan voor Azure Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

**Richtlijnen:** Wanneer u een Azure Storage-account gebruikt voor het HDInsight-clustergegevensarchief, kiest u de juiste redundantieoptie (LRS, ZRS, GRS, RA-GRS).  Wanneer u een Azure SQL Database gebruikt voor het Azure HDInsight-clustergegevensarchief, configureert u Active Geo-replicatie.

Opslagredundantie configureren voor Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Redundantie configureren voor Azure SQL-databases:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

**Richtlijnen:** Wanneer u een Azure Storage-account gebruikt voor het Azure HDInsight-clustergegevensarchief, kiest u de juiste redundantieoptie (LRS, ZRS, GRS, RA-GRS). Als u Azure Key Vault gebruikt voor een deel van uw Azure HDInsight-implementatie, moet u ervoor zorgen dat er een back-up van uw sleutels wordt gemaakt.

Kies opslagopties voor uw Azure HDInsight-cluster:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Opslagredundantie configureren voor Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Een back-up maken van Key Vault-sleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Als Azure Key Vault wordt gebruikt met uw Azure HDInsight-implementatie, test u het herstel van back-ups van door klanten beheerde sleutels.

Hoe breng je je eigen sleutel voor Apache Kafka mee op Azure HDInsight:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Sleutelkluissleutels herstellen in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

**Richtlijnen:** Als Azure Key Vault wordt gebruikt met uw Azure HDInsight-implementatie, schakelt u Soft-Delete in Key Vault in om sleutels te beschermen tegen onbedoelde of kwaadwillige verwijdering.

Soft-Delete inschakelen in Azure Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

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

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen**: Voer oefeningen uit om de incidentresponsmogelijkheden van uw systemen op een regelmatige cadans te testen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig. Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat uw gegevens zijn geopend door een onwettige of onbevoegde partij.

De beveiligingscontactpersoon azure security center instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

**Richtlijnen:** exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue exporteren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen sentinel te streamen.

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

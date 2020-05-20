---
title: Azure-beveiligings basislijn voor Azure Databricks
description: Azure-beveiligings basislijn voor Azure Databricks
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 23057723d46ed5d9203741ab2eb1a15edb5bd510
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681695"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Azure-beveiligings basislijn voor Azure Databricks

De Azure-beveiligings basislijn voor Azure Databricks bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie het [overzicht van Azure Security-basis lijnen](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Hulp**: implementeer Azure Databricks in uw eigen Azure Virtual Network (VNet). De standaard implementatie van Azure Databricks is een volledig beheerde service in Azure: alle gegevens vlak resources, inclusief een VNet waaraan alle clusters worden gekoppeld, worden geïmplementeerd in een vergrendelde resource groep. Als u netwerk aanpassing nodig hebt, kunt u echter Azure Databricks data spot-resources implementeren in uw eigen virtuele netwerk (VNet-injectie), zodat u aangepaste netwerk configuraties implementeert. U kunt uw eigen netwerk beveiligings groep (NSG) Toep assen met aangepaste regels voor specifieke beperkingen voor uitgaand verkeer.

Daarnaast kunt u NSG-regels configureren om beperkingen voor uitgaand verkeer op te geven voor het subnet waarop uw Azure Databricks-exemplaar is geïmplementeerd. Azure Firewall kunnen worden geconfigureerd voor door VNET geïnjecteerde werk ruimten.

* [Azure Databricks implementeren in uw eigen Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Nsg's beheren](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en NIC'S bewaken en vastleggen

**Hulp**: implementeer Azure Databricks in uw eigen Azure Virtual Network (VNet). Een netwerk beveiligings groep (NSG) wordt niet automatisch gemaakt. U moet een basislijn NSG maken met alleen de standaard regels voor Azure. Wanneer u een werk ruimte implementeert, worden de regels die vereist zijn voor Databricks toegevoegd. U kunt ook beginnen met een lege NSG en de juiste regels worden automatisch toegevoegd. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor verkeers controles. U kunt ook stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

* [Azure Databricks implementeren in uw eigen Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG-stroom logboeken inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Traffic Analytics inschakelen en gebruiken](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Network Watcher inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel Azure DDoS Protection Standard in op de virtuele Azure-netwerken die zijn gekoppeld aan uw Azure Databricks-instanties voor beveiliging tegen gedistribueerde Denial-of-service-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte open bare IP-adressen te weigeren.

* [Azure DDoS Protection Standard beheren met de Azure Portal](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Meer informatie over bedreigingen beveiliging voor Azure-netwerklaag in Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Hulp**: implementeer Azure Databricks in uw eigen Azure Virtual Network (VNet). Een netwerk beveiligings groep (NSG) wordt niet automatisch gemaakt. U moet een basislijn NSG maken met alleen de standaard regels voor Azure. Wanneer u een werk ruimte implementeert, worden de regels die vereist zijn voor Databricks toegevoegd. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor verkeers controle. U kunt ook stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

* [Azure Databricks implementeren in uw eigen Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [NSG-stroom logboeken inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Traffic Analytics inschakelen en gebruiken](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Network Watcher inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Hulp**: Implementeer een IDP/IP-compatibel netwerk virtueel apparaat (NVA) van Azure Marketplace om een virtuele netwerk-geïntegreerde werk ruimte te maken waarin alle Azure Databricks clusters één IP-adres voor uitgaand verkeer hebben. Het ene IP-adres kan worden gebruikt als een extra beveiligingslaag met andere Azure-Services en-toepassingen die toegang toestaan op basis van specifieke IP-adressen. U kunt een Azure-firewall of andere hulpprogram ma's van derden, zoals NVA, gebruiken voor het beheren van binnenkomend en uitgaand verkeer.

Als inbraak detectie en/of preventie op basis van Payload-inspectie geen vereiste is, kan Azure Firewall met bedreigings informatie worden gebruikt. Azure Firewall op bedreigingen gebaseerd filteren kan verkeer van en naar bekende schadelijke IP-adressen en domeinen Signa lering en weigeren. De IP-adressen en domeinen zijn afkomstig van de micro soft Threat Intelligence-feed.

* [Een enkel openbaar IP-adres toewijzen aan in VNET opgenomen werkruimten met behulp van Azure Firewall](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [Een NVA maken](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik service tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen die zijn gekoppeld aan de subnetten waaraan uw Azure Databricks-exemplaar is gekoppeld. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen. Service tags worden niet ondersteund met niet-geïnjecteerde VNET-werk ruimten.

* [Informatie over service Tags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: netwerk beveiligings configuraties voor uw Azure Databricks-instanties definiëren en implementeren met Azure Policy. U kunt Azure Policy aliassen in de naam ruimte ' micro soft. Databricks ' gebruiken om aangepaste beleids definities te definiëren. Beleids regels zijn niet van toepassing op resources binnen de beheerde resource groep.

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure resource management-sjablonen, op rollen gebaseerd toegangs beheer (RBAC) en beleids regels, in één definitie van de blauw druk. Pas de blauw druk toe op nieuwe abonnementen en omgevingen en Verfijn de controle en het beheer via versies.

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy aliassen en definitie structuur begrijpen](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Een Azure Blueprint maken](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor nsg's en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom die zijn gekoppeld aan uw Azure Databricks-exemplaar. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

* [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure Databricks exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

* [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Waarschuwingen maken in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: micro soft onderhoudt tijd bronnen voor Azure-resources, maar u hebt de mogelijkheid om de tijd synchronisatie-instellingen voor uw reken resources te beheren. Omdat Azure Databricks een PaaS-service is, hebt u geen rechtstreekse toegang tot de virtuele machines in een Azure Databricks-cluster en kunt u geen tijd synchronisatie-instellingen instellen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door Azure Databricks. In Azure Monitor kunt u een query uitvoeren op de Log Analytics-werk ruimte die is geconfigureerd om uw Databricks-en Diagnostische logboeken te ontvangen. Gebruik Azure Storage accounts voor langdurige logboek opslag of event hubs voor het exporteren van gegevens naar andere systemen. U kunt ook gegevens naar Azure Sentinel of een beveiligings incident en gebeurtenis beheer van derden (SIEM) inschakelen en op de trein zetten.

Opmerking: voor Azure Databricks Diagnostische logboeken is het Azure Databricks Premium plan vereist

* [Diagnostische instellingen configureren](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in en verzend de logboeken naar een log Analytics-werk ruimte, een Azure Event hub of een Azure-opslag account voor archivering. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het controle vlak voor uw Azure-resources.

Voor controle logboek registratie biedt Azure Databricks uitgebreide end-to-end Diagnostische logboeken van activiteiten die worden uitgevoerd door Azure Databricks gebruikers, zodat uw onderneming gedetailleerde Azure Databricks gebruiks patronen kan bewaken.

Opmerking: voor Azure Databricks Diagnostische logboeken is het Azure Databricks Premium plan vereist

* [Diagnostische instellingen inschakelen voor Azure-activiteiten logboek](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Diagnostische instellingen inschakelen voor Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Hulp**: Azure Databricks biedt uitgebreide end-to-end Diagnostische logboeken van activiteiten die worden uitgevoerd door Azure Databricks gebruikers, zodat uw onderneming gedetailleerde Azure Databricks gebruiks patronen kan bewaken.

Opmerking: Azure Databricks Diagnostische logboeken vereist het Azure Databricks Premium plan. Logboek registratie van het besturings systeem is niet beschikbaar.

* [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Diagnostische instellingen inschakelen voor Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: Diagnostische instellingen inschakelen voor Azure Databricks. Als u ervoor kiest om logboeken op te slaan in een Log Analytics-werk ruimte, moet u uw Bewaar periode voor Log Analytics werkruimte instellen volgens de nalevings voorschriften van uw organisatie. Gebruik Azure Storage-accounts voor lange termijn/archiverings opslag. Aan de beveiliging gerelateerde activiteiten worden bijgehouden in Databricks-controle Logboeken.

Opmerking: voor Azure Databricks Diagnostische logboeken is het Azure Databricks Premium plan vereist

* [Diagnostische instellingen inschakelen in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Para meters voor het bewaren van Logboeken instellen voor Log Analytics-werk ruimten](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Diagnostische instellingen voor Azure Databricks inschakelen en logboeken naar een log Analytics werkruimte verzenden. Gebruik de werk ruimte Log Analytics om uw Azure Databricks logboeken te analyseren en te controleren op afwijkend gedrag en regel matig de resultaten te controleren.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

Opmerking: voor Azure Databricks Diagnostische logboeken is het Azure Databricks Premium plan vereist

* [Diagnostische instellingen inschakelen in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Query's uitvoeren op Azure Databricks logboeken die naar Log Analytics werk ruimte worden verzonden](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Hulp**bij het configureren van diagnostische instellingen voor uw Azure Databricks-exemplaar en het verzenden van logboeken naar log Analytics werk ruimte. Configureer in uw Log Analytics-werk ruimte de waarschuwingen die moeten worden uitgevoerd wanneer een vooraf gedefinieerde set voor waarden plaatsvindt.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

Opmerking: voor Azure Databricks Diagnostische logboeken is het Azure Databricks Premium plan vereist

* [Azure Databricks logboeken naar log Analytics werk ruimte verzenden](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery))

* [Waarschuwingen configureren in Log Analytics werk ruimte](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure Databricks geen door de gebruiker toegankelijke logboeken met betrekking tot DNS verwerkt of produceert.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteits-en toegangs beheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: u kunt Azure Databricks scim-api's gebruiken voor het beheren van gebruikers in een Azure Databricks-werk ruimte en het verlenen van beheerders bevoegdheden aan aangewezen gebruikers. U kunt ze ook beheren via de Azure Databricks-gebruikers interface.

* [De SCIM-Api's gebruiken](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Gebruikers toevoegen aan en verwijderen uit Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Databricks gebruikt Azure Active Directory (AD) om toegang te bieden tot de Azure Portal en de Azure Databricks-beheer console. Azure AD heeft niet het concept van standaard wachtwoorden, maar u bent zelf verantwoordelijk voor het wijzigen of niet toestaan van standaard wachtwoorden voor aangepaste of externe toepassingen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: u kunt Azure Databricks scim-api's gebruiken om gebruikers met beheerders bevoegdheden toe te voegen in een Azure Databricks. U kunt ze ook beheren via de Azure Databricks-gebruikers interface.

* [De SCIM-Api's gebruiken](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Gebruikers toevoegen en verwijderen in Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Hulp**: Azure Databricks wordt automatisch ingesteld voor het gebruik van Azure Active Directory eenmalige aanmelding om gebruikers te verifiëren. Gebruikers buiten uw organisatie moeten het uitnodigings proces volt ooien en aan uw Active Directory-Tenant toevoegen voordat ze zich kunnen aanmelden bij Azure Databricks via eenmalige aanmelding. U kunt SCIM implementeren voor het automatiseren van het inrichten en het ongedaan maken van de inrichting van gebruikers uit werk ruimten.

* [De SCIM-Api's gebruiken](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Meer informatie over eenmalige aanmelding voor Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Gebruikers uitnodigen voor uw Azure AD-Tenant](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: gebruik multi-factor Authentication voor alle toegang op basis van Azure Active Directory.

**Richt lijnen**: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

* [MFA inschakelen in azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Identiteit en toegang bewaken in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met MFA dat is geconfigureerd om Azure-resources aan te melden en te configureren.

* [Meer informatie over privileged Access workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [MFA inschakelen in azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken. Daarnaast kunt u gebruikmaken van de Azure Databricks Diagnostische logboeken.

* [Azure AD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

* [Benoemde locaties configureren in azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: Azure Databricks wordt automatisch ingesteld voor het gebruik van Azure Active Directory eenmalige aanmelding om gebruikers te verifiëren. Gebruikers buiten uw organisatie moeten het uitnodigings proces volt ooien en aan uw Active Directory-Tenant toevoegen voordat ze zich kunnen aanmelden bij Azure Databricks via eenmalige aanmelding.

* [Meer informatie over eenmalige aanmelding voor Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Gebruikers uitnodigen voor uw Azure AD-Tenant](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Richt lijnen**: Azure AD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. U kunt ook SCIM-Api's en Azure Databricks Diagnostische logboeken implementeren om de gebruikers toegang te controleren. U kunt ook SCIM-Api's en Azure Databricks diagnostische Logboeken gebruiken om de gebruikers toegang te controleren.

Daarnaast kunt u de gebruikers toegang regel matig bekijken en beheren in de beheer console van Azure Databricks.

* [Azure AD-rapportage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Beoordelingen over Azure Identity Access gebruiken](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Gebruikers toegang beheren in de Azure Databricks-beheer console](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Richt lijnen**: u hebt toegang tot de Azure AD-aanmeldings activiteit, controle en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met elk Siem/bewakings programma. Daarnaast kunt u Azure Databricks diagnostische Logboeken gebruiken om de activiteit van de gebruikers toegang te controleren.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure Active Directory gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte.

* [SCIM-Api's gebruiken](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure-activiteiten logboeken integreren in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Richt lijnen**: Azure AD-functies voor risico-en identiteits beveiliging gebruiken om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek. Daarnaast kunt u Azure Databricks diagnostische Logboeken gebruiken om de activiteit van de gebruikers toegang te controleren.

* [Risk ante aanmeldingen voor Azure AD weer geven](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Risico beleid voor identiteits beveiliging configureren en inschakelen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: als er ondersteunings tickets zijn geopend, vragen technici van klanten service en-ondersteuning om toestemming voor toegang tot relevante klant gegevens.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: momenteel niet beschikbaar

## <a name="data-protection"></a>Gegevensbescherming

*Zie [beveiligings beheer: gegevens beveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het volgen van Azure Databricks instanties die gevoelige informatie verwerken.

* [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. De standaard implementatie van Azure Databricks is een volledig beheerde service die binnen een eigen virtueel netwerk wordt geïmplementeerd. Als u netwerk aanpassing nodig hebt, kunt u Azure Databricks implementeren in uw eigen virtuele netwerk. Het is een best practice om afzonderlijke Azure Databricks-werk ruimten te maken voor verschillende zakelijke teams of afdelingen.

* [Azure Databricks implementeren in uw eigen Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Beheergroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Een Virtual Network maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens bewaken en blok keren.

**Hulp**: Volg de Databricks ' exfiltration Protection-architectuur om de kans op gegevens exfiltration te verminderen.

* [Beveiliging van gegevens exfiltration met Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

Micro soft beheert de onderliggende infra structuur voor Azure Databricks en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

* [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: momenteel niet beschikbaar

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: micro soft onderhandelt standaard TLS 1,2 bij het beheer van uw Azure Databricks-exemplaar via de Azure Portal of Azure Databricks-console. Databricks Web-App ondersteunt TLS 1,3.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: momenteel niet beschikbaar; de functies voor gegevens identificatie, classificatie en verlies preventie zijn momenteel niet beschikbaar voor Azure Databricks. Codeer Azure Databricks instanties en gerelateerde resources die gevoelige informatie kunnen verwerken als dusdanig en implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Het Databricks-platform is alleen Compute en alle gegevens worden opgeslagen op andere Azure Data Services. Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

* [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: momenteel niet beschikbaar

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: in azure Databricks kunt u toegangs beheer lijsten (acl's) gebruiken om machtigingen te configureren voor toegang tot gegevens tabellen, clusters, groepen, taken en werkruimte objecten zoals notebooks, experimenten en mappen. Alle gebruikers met beheerders rechten kunnen toegangs beheer lijsten beheren, evenals gebruikers aan wie gedelegeerde machtigingen zijn verleend voor het beheren van toegangs beheer lijsten. U kunt Azure RBAC gebruiken voor het instellen van machtigingen voor de Azure Databricks-werk ruimte.

Opmerking: het toegangs beheer voor tabellen, clusters, groepen, taken en werk ruimten is alleen beschikbaar in het Azure Databricks Premium plan

* [Toegangs beheer in Azure Databricks beheren](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

Micro soft beheert de onderliggende infra structuur voor Azure Databricks en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

* [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: een Azure Databricks-werk ruimte bestaat uit een beheer vlak dat wordt gehost in een Azure Databricks beheerd virtueel netwerk en een gegevensvlak dat is geïmplementeerd in een door de klant beheerd virtueel netwerk. In het besturings vlak worden de notitie blokken van alle gebruikers en de bijbehorende notitieblok resultaten opgeslagen in een Data Base. Standaard worden alle notitie blokken en resultaten op rest versleuteld met een andere versleutelings sleutel.

Databricks File System (DBFS) is een gedistribueerd bestands systeem dat is gekoppeld aan een Azure Databricks-werk ruimte en beschikbaar is op Azure Databricks-clusters. DBFS wordt geïmplementeerd als een opslag account in de beheerde resource groep van uw Azure Databricks-werk ruimte. Het opslag account wordt standaard versleuteld met door micro soft beheerde sleutels. Uw gegevens worden opgeslagen in de Azure-gegevens services waarvan u de eigenaar bent, en u hebt de mogelijkheid om deze te versleutelen. Het gebruik van DBFS voor het opslaan van productie gegevens wordt niet aanbevolen

Opmerking: deze functies zijn niet beschikbaar voor alle Azure Databricks-abonnementen. Neem contact op met uw micro soft-of Databricks-account om toegang aan te vragen.

* [Door de klant beheerde sleutels inschakelen voor Azure Databricks-notebooks](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Door de klant beheerde sleutels inschakelen voor Azure Databricks bestands systeem](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden doorgevoerd in essentiële Azure Databricks werk ruimten.

* [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: Implementeer een oplossing voor het beheer van beveiligings problemen van derden.

Als u een abonnement voor het beheer platform voor beveiligings problemen hebt, kunt u Azure Databricks initialisatie scripts gebruiken voor het installeren van agents voor beveiligings evaluaties op uw Azure Databricks cluster knooppunten en het beheren van de knoop punten via de desbetreffende Portal. Houd er rekening mee dat elke oplossing van derden anders werkt.

* [Rapid7 agent hand matig installeren](https://insightagent.help.rapid7.com/docs/install)

* [Qualys agent hand matig installeren](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks initialisatie scripts](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Hulp**: micro soft bewaart de Azure Databricks basis installatie kopieën van cluster knooppunten, maar u moet er wel voor zorgen dat uw cluster knooppunten nog steeds worden bijgewerkt. Om een onderhouds update toe te voegen aan een bestaand cluster dat wordt uitgevoerd, moet u het cluster opnieuw opstarten.

* [Informatie over updates voor runtime-onderhoud voor Azure Databricks](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: micro soft bewaart de Azure Databricks basis installatie kopieën van cluster knooppunten, maar u moet er wel voor zorgen dat alle toepassingen van derden die u installeert, worden bijgewerkt.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: Implementeer een oplossing voor het beheer van beveiligings problemen van derden die de mogelijkheid biedt om beveiligings controles in de loop van de tijd te vergelijken. Als u een abonnement voor het beheer van beveiligings problemen hebt, kunt u de portal van die leverancier gebruiken om scans van back-to-back te bekijken en te vergelijken. Houd er rekening mee dat elke oplossing van derden anders werkt.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: gebruik een proces voor risico classificatie om prioriteit te geven aan het herstel van ontdekte beveiligings problemen.

**Richt lijnen**: gebruik een gemeen schappelijke risico Score programma (bijvoorbeeld een gemeen schappelijk score systeem voor beveiligings problemen) of de standaard risico classificaties van het hulp programma van derden.

**Azure Security Center bewaking**: n.v.t.

**Verantwoordelijkheid**: klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en). Zorg ervoor dat de juiste (Lees-) machtigingen in uw Tenant bestaan en Inventariseer alle Azure-abonnementen, evenals de resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

* [Query's maken met Azure resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Uw Azure-abonnementen weer geven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Meer informatie over Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

* [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Daarnaast kunt u met Azure Policy beperkingen opleggen aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane brontypen
- Toegestane brontypen

* [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Beheergroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: behoud de inventaris van goedgekeurde Azure-resources en software titels.

**Hulp**: Definieer goedgekeurde Azure-resources en goedgekeurde software voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: Azure**Policy gebruiken om beperkingen te geven aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane brontypen
- Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Query's maken met Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; Azure Databricks is een PaaS-service, klanten hebben geen directe toegang tot de virtuele machines in een Azure Databricks cluster.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.), inclusief Azure Databricks exemplaren, binnen uw abonnement (en). Verwijder alle niet-goedgekeurde Azure-resources die u ontdekt. Implementeer voor Azure Databricks cluster knooppunten een oplossing van derden om niet-goedgekeurde software te verwijderen of te waarschuwen.

* [Query's maken met Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; Azure Databricks een PaaS-service is, hebt u geen rechtstreekse toegang tot de virtuele machines in een Azure Databricks cluster.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik**Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane brontypen
- Toegestane brontypen

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing; Azure Databricks een PaaS-service is, hebt u geen rechtstreekse toegang tot de virtuele machines in een Azure Databricks cluster.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken</div>

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

* [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; Dit is niet van toepassing op Azure Databricks als gebruikers (niet-beheerders) van het cluster geen toegang nodig hebben tot de afzonderlijke knoop punten om taken uit te voeren. De Cluster beheerder heeft standaard toegang tot alle cluster knooppunten.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; Bench Mark is bedoeld voor Azure app service-of COMPUTE-resources die webtoepassingen hosten.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw Azure Databricks-instanties definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Databricks ' om aangepaste beleids regels te maken om de configuratie van uw Azure Databricks instanties te controleren of af te dwingen. Houd er rekening mee dat beleids regels die worden toegepast, niet werken voor de beheerde resource groep Databricks.

* [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; Azure Databricks een PaaS-service is, hebt u geen rechtstreekse toegang tot de virtuele machines in een Azure Databricks cluster.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: standaard beveiligings configuraties voor uw Azure Databricks-instanties definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Databricks ' om aangepaste beleids regels te maken om de configuratie van uw Azure Databricks instanties te controleren of af te dwingen. Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy effecten begrijpen](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Hulp**: installatie kopieën van het besturings systeem Azure Databricks beheerd en beheerd door micro soft. U bent verantwoordelijk voor het implementeren van de status configuratie op besturingssysteem niveau.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste definities van Azure-beleid gebruikt, kunt u Azure DevOps of Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren.

* [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Documentatie voor Azure opslag plaatsen](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: als u aangepaste installatie kopieën gebruikt voor uw Azure Databricks cluster knooppunten, moet u uw aangepaste basis installatie kopie pushen naar een docker-REGI ster, zoals Azure container Registry (ACR).

* [Containers aanpassen met Databricks-container Services](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [Azure Container Registry begrijpen](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Richt lijnen**: standaard beveiligings configuraties voor uw Azure Databricks-instanties definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Databricks ' om aangepaste beleids regels te maken om de configuratie van uw Azure Databricks instanties te controleren of af te dwingen.

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; Azure Databricks een PaaS-service is, hebt u geen rechtstreekse toegang tot de virtuele machines in een Azure Databricks cluster.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Richt lijnen**: standaard beveiligings configuraties voor uw Azure Databricks-instanties definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Databricks ' om aangepaste beleids regels te maken om de configuratie van uw Azure Databricks instanties te controleren of af te dwingen.

* [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: Implementeer een oplossing van derden om de status van uw Azure Databricks cluster knooppunt besturings systemen te bewaken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Azure Key Vault met een Azure Databricks geheim bereik om geheimen veilig te beheren en te gebruiken.

* [Azure Key Vault gebruiken met Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Hulp**: momenteel niet beschikbaar; Beheerde identiteiten zijn momenteel niet beschikbaar voor Azure Databricks

* [Services die beheerde identiteiten voor Azure-resources ondersteunen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: momenteel niet beschikbaar

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

* [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: als u een abonnement voor het beheer platform voor beveiligings problemen hebt, kunt u Azure Databricks initialisatie scripts gebruiken voor het installeren van agents voor beveiligings evaluaties op uw Azure Databricks cluster knooppunten en het beheren van de knoop punten via de desbetreffende Portal. Houd er rekening mee dat elke oplossing van derden anders werkt.

* [Rapid7 agent hand matig installeren](https://insightagent.help.rapid7.com/docs/install)

* [Qualys agent hand matig installeren](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks initialisatie scripts](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure app service), maar wordt niet uitgevoerd voor uw inhoud.

Scan vooraf bestanden die worden geüpload naar uw Azure Databricks cluster knooppunten of gerelateerde bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: als u een abonnement voor het beheer platform voor beveiligings problemen hebt, kunt u Azure Databricks initialisatie scripts gebruiken voor het installeren van agents voor beveiligings evaluaties op uw Azure Databricks cluster knooppunten en het beheren van de knoop punten via de desbetreffende Portal. Houd er rekening mee dat elke oplossing van derden anders werkt.

* [Rapid7 agent hand matig installeren](https://insightagent.help.rapid7.com/docs/install)

* [Qualys agent hand matig installeren](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks initialisatie scripts](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: voor uw Azure Databricks gegevens bronnen moet u ervoor zorgen dat u een geschikt niveau voor gegevens redundantie hebt geconfigureerd voor uw use-case. Als u bijvoorbeeld een Azure Storage-account gebruikt voor uw Azure Databricks gegevens archief, kiest u de juiste optie voor redundantie (LRS, ZRS, GRS, RA-GRS).

* [Gegevens bronnen voor Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Regionale nood herstel voor Azure Databricks clusters](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Hulp**: een back-up maken van een door de klant beheerde sleutels die betrekking hebben op uw Azure Databricks-implementaties binnen Azure Key Vault. U kunt ook REST API en CLI gebruiken om een dagelijkse back-up van Databricks-configuraties te maken. U kunt ook REST API/CLI gebruiken om een dagelijkse back-up van Databricks-configuraties te maken.

* [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: het herstellen van een back-up van door de klant beheerde sleutels die betrekking hebben op uw Azure Databricks-implementaties testen.

* [Sleutel kluis sleutels herstellen in azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat zacht verwijderen is ingeschakeld in Key Vault om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

* [Zacht verwijderen inschakelen in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten maken voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle werk rollen definiëren, evenals fasen van incident handling/management van detectie tot een beoordeling van het incident.

* [Werk stroom automatisering configureren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [U kunt ook gebruikmaken van de richt lijnen voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

* [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de klant gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

* [De Azure Security Center Security-contact persoon instellen](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de Sentinel van waarschuwingen te streamen.

* [Continue export configureren](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Waarschuwingen streamen naar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

* [Werk stroom automatisering en Logic Apps configureren](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herstel van alle essentiële beveiligings resultaten binnen 60 dagen.

**Richt lijnen**: * [Volg de regels van micro soft om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests met door micro soft beheerde Cloud infrastructuur,-services en-toepassingen.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

---
title: Azure-beveiligings basislijn voor batch
description: Azure-beveiligings basislijn voor batch
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1eb24871817f365efe58b8e687563727df74493c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400973"
---
# <a name="azure-security-baseline-for-batch"></a>Azure-beveiligings basislijn voor batch

De Azure Security Baseline voor batch bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview.md), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](../security/benchmarks/security-baselines-overview.md)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](../security/benchmarks/security-control-network-security.md)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Hulp**: Azure batch groep (en) implementeren in een virtueel netwerk. U kunt de groep inrichten in een subnet van een virtueel Azure-netwerk als u wilt toestaan dat groeps Compute-knoop punten veilig met andere virtuele machines kunnen communiceren of met een on-premises netwerk. Daarnaast kunt u met de implementatie van uw groep binnen een virtueel netwerk de netwerk beveiligings groep (NSG) beheren die wordt gebruikt voor het beveiligen van de afzonderlijke knoop punten netwerk interfaces (NIC), evenals het subnet. Configureer de NSG om alleen verkeer toe te staan van vertrouwde IP (s)/locations op internet.

Een Azure Batch groep maken binnen een Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en NIC'S bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en herstel aanbevelingen voor netwerk beveiliging met betrekking tot het virtuele netwerk/netwerk beveiligings groep (NSG) die is gekoppeld aan de batch-pool. Schakel stroom Logboeken in op de NSG die wordt gebruikt voor het beveiligen van uw batch-pool en verzend logboeken naar een Azure Storage account voor verkeers controle. U kunt ook NSG-stroom logboeken naar een Azure Log Analytics-werk ruimte verzenden en Azure Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Azure Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerkloze configuraties te lokaliseren.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Informatie over netwerk beveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel Azure DDoS (Distributed Denial of service) standaard beveiliging in op het virtuele netwerk dat uw Azure batch-groep beschermt tegen beveiliging tegen DDoS-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte Internet-IP-adressen te weigeren.

DDoS-beveiliging configureren:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Meer informatie over Azure Security Center geïntegreerde bedreigings informatie:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Hulp**: Schakel stroom Logboeken in voor de netwerk beveiligings groep (NSG) die wordt gebruikt voor het beveiligen van uw Azure batch groep en verzend logboeken naar een Azure Storage account voor verkeers controle.

NSG-stroom logboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen implementeren

**Richt lijnen**: als dat nodig is voor nalevings doeleinden, selecteert u een virtueel netwerk apparaat in de Azure Marketplace dat ondersteuning biedt voor indringings detectie systemen (id's) en functionaliteit voor het indringing van systemen (IPS) met Payload-inspectie mogelijkheden.

Als inbraak detectie en/of preventie op basis van Payload-inspectie geen vereiste is, kan Azure Firewall met bedreigings informatie worden gebruikt. Azure Firewall op bedreigingen gebaseerd filteren kan verkeer van en naar bekende schadelijke IP-adressen en domeinen Signa lering en weigeren. De IP-adressen en domeinen zijn afkomstig van de Microsoft Bedreigingsinformatie-feed.

Implementeer Azure Firewall met een openbaar IP-adres in hetzelfde virtuele netwerk als uw Azure Batch pool-knoop punten. Configureer Network Address Translation (NAT) regels tussen vertrouwde locaties op internet en de privé-IP-adressen van uw afzonderlijke groeps knooppunten. Configureer op het Azure Firewall onder Threat Intelligence de waarschuwing en weigeren om te blok keren dat verkeer naar/van bekende schadelijke IP-adressen en domeinen moet worden gewaarschuwd en geblokkeerd. De IP-adressen en domeinen zijn afkomstig uit de micro soft Threat Intelligence-feed en alleen de hoogste betrouwbaarheids records worden opgenomen. 

Een Azure Batch groep maken binnen een Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Azure Firewall implementeren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: verkeer naar uw webtoepassingen beheren

**Richt lijnen**: niet van toepassing, Bench Mark is bedoeld voor webtoepassingen die worden uitgevoerd op Azure app service-of IaaS-instanties.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: gebruik Tags voor het virtuele netwerk om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure-firewalls die zijn gekoppeld aan uw Azure batch-groep (en). U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

Service Tags begrijpen en gebruiken:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen die zijn gekoppeld aan uw Azure batch groep (en) met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' Microsoft.Batch ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Batch groepen te controleren of af te dwingen.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk service groepen (nsg's) en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom die zijn gekoppeld aan uw Azure batch-Pools. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een virtueel netwerk maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische Hulpprogram Ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk bronnen die betrekking hebben op uw Azure batch groepen. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden.

Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Waarschuwingen maken in Azure Monitor: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: micro soft biedt standaard tijd synchronisatie voor Azure batch. Als u echter specifieke tijd synchronisatie vereisten hebt, kunt u deze wijzigingen implementeren.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: onboard Azure batch account voor het Azure monitor van geaggregeerde beveiligings gegevens die door de cluster apparaten worden gegenereerd. Gebruik aangepaste query's om bedreigingen in de omgeving te detecteren en erop te reageren.  Gebruik voor Azure Batch bewaking op resource niveau de batch-Api's om de status van uw resources, inclusief taken, taken, knoop punten en Pools, te controleren of er query's op uit te zoeken.

Een Azure Batch-account onboarden naar Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: Bewaak voor Azure batch controle op account niveau elk batch-account met behulp van de functies van Azure monitor. Azure Monitor verzamelt metrische gegevens en eventueel Diagnostische logboeken voor resources die zijn afgestemd op het niveau van een batch-account, zoals Pools, Jobs en taken. Verzamel en gebruik deze gegevens hand matig of programmatisch om activiteiten in uw batch-account te bewaken en problemen te onderzoeken.

Gebruik voor Azure Batch bewaking op resource niveau de Azure Batch Api's om de status van uw resources, inclusief taken, taken, knoop punten en Pools, te controleren of er query's op uit te zoeken.

Azure Batch bewaking en logboek registratie op account niveau configureren:

https://docs.microsoft.com/azure/batch/monitoring-overview

Meer informatie over batch-bewaking op resource niveau:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="24-collect-security-logs-from-operating-system"></a>2,4: beveiligings logboeken van het besturings systeem verzamelen

**Hulp**: Azure monitor verzamelt metrische gegevens en Diagnostische logboeken voor resources in uw Azure batch-account. Verzamel en gebruik deze gegevens op verschillende manieren om uw Azure Batch-account te controleren en problemen op te sporen. U kunt ook metrische waarschuwingen configureren zodat u meldingen ontvangt wanneer een metriek een opgegeven waarde bereikt.

Als dat nodig is, kunt u verbinding maken met de knoop punten van uw afzonderlijke groep via een beveiligde shell (SSH) of Remote Desktop Protocol (RDP) om toegang te krijgen tot de logboeken van het lokale besturings systeem.

Diagnostische logboeken van uw Azure Batch-account verzamelen:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Extern verbinding maken met de knoop punten van uw Azure Batch-groep:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Hulp**: Azure batch account voor onboarding naar Azure monitor. Zorg ervoor dat de gebruikte Azure Log Analytics-werk ruimte de Bewaar periode voor logboek registratie heeft ingesteld volgens de nalevings voorschriften van uw organisatie

Azure Batch bewaking en logboek registratie configureren:

https://docs.microsoft.com/azure/batch/monitoring-overview

De Bewaar periode voor Azure Log Analytics Workspace configureren:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Maak Azure batch metrische waarschuwingen die worden geactiveerd wanneer de waarde van een opgegeven metriek een gegeven drempel overschrijdt.

Azure Batch metrische waarschuwingen configureren:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Hulp**: Maak Azure batch metrische waarschuwingen die worden geactiveerd wanneer de waarde van een opgegeven metriek een gegeven drempel overschrijdt.

Azure Batch metrische waarschuwingen configureren:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Hulp**: gebruik Windows Defender op uw afzonderlijke batch knooppunten in het geval van Windows-besturings systemen, of geef uw eigen oplossing voor anti-malware op als u Linux gebruikt.

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: oplossingen van derden voor DNS-logboek registratie implementeren

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Hulp**: hand matig de console logboek registratie en Power shell transcriptie configureren per knoop punt.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteit en Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: inventaris van beheerders accounts onderhouden

**Hulp**: behoud de record van het lokale beheerders account dat is gemaakt tijdens het inrichten van de Azure batch groep, evenals andere accounts die u maakt. Als Azure Active Directory-integratie (AAD) wordt gebruikt, heeft AAD Daarnaast ingebouwde rollen die expliciet moeten worden toegewezen en daarom kunnen query's worden uitgevoerd. Gebruik de AAD Power shell-module om ad hoc query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen.

Daarnaast kunt u de aanbevelingen van Azure Security Center identiteits-en toegangs beheer gebruiken.

Een directory-rol verkrijgen in AAD met Power shell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Leden van een directory-rol in AAD ophalen met Power shell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Identiteit en toegang controleren met Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Richt lijnen**: bij het inrichten van een Azure batch groep krijgt u de mogelijkheid om lokale computer accounts te maken. Er zijn geen standaard wachtwoorden om te wijzigen, maar u kunt ook verschillende wacht woorden opgeven voor toegang tot beveiligde shell (SSH) en Remote Desktop Protocol (RDP). Nadat Azure Batch groep is geconfigureerd, kunt u een wille keurige gebruiker genereren voor afzonderlijke knoop punten in de Azure Portal of via Azure Resource Manager API.

Een gebruiker toevoegen aan een specifiek reken knooppunt:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3,3: controleren of er specifieke beheerders accounts worden gebruikt

**Richt lijnen**: authenticatie voor Azure batch toepassingen integreren met Azure Active Directory. Beleids regels en procedures voor het gebruik van specifieke beheerders accounts maken.

Daarnaast kunt u de aanbevelingen van Azure Security Center identiteits-en toegangs beheer gebruiken.

Batch-toepassingen verifiëren met Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Identiteit en toegang controleren met Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige Sign-On (SSO) gebruiken met Azure Active Directory

**Richt lijnen**: niet van toepassing, terwijl Azure batch Azure AD-verificatie ondersteunt, wordt eenmalige aanmelding niet ondersteund.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle toegang op basis van Azure Active Directory.

**Hulp**: Integreer verificatie voor Azure batch-toepassingen met Azure Active Directory (Aad). Schakel AAD multi-factor Authentication (MFA) in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

 

MFA inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met multi-factor Authentication (MFA) geconfigureerd om u aan te melden en uw Azure batch-resources te configureren.

Meer informatie over privileged Access workstations:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

MFA inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Richt lijnen**: als u verificatie hebt geïntegreerd voor Azure batch toepassingen met Azure Active Directory (Aad), gebruikt u Azure Active Directory beveiligings rapporten voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure Security Center om identiteits-en toegangs activiteiten te bewaken.

Azure AD-gebruikers identificeren die zijn gemarkeerd voor Risk ante activiteiten:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Identiteits-en toegangs activiteiten van gebruikers controleren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: Azure-resource alleen beheren vanaf goedgekeurde locaties

**Richt lijnen**: als u verificatie hebt geïntegreerd voor Azure batch toepassingen met Azure Active Directory, kunt u voorwaardelijke toegang benoemde locaties gebruiken om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

Benoemde locaties configureren in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Aad) als centrale verificatie-en autorisatie systeem en integreer verificatie voor Azure batch toepassingen met Aad. AAD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en tijdens de overdracht. AAD bevat ook zouten, hashes en veilige gebruikers referenties.

Een AAD-exemplaar maken en configureren:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Batch-toepassingen verifiëren met AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (Aad) biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen, toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. De toegang van gebruikers kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Azure Identity Access revisies gebruiken:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Hulp**: Diagnostische instellingen voor Azure Active Directory gebruikers accounts maken, de audit logboeken en aanmeldings logboeken verzenden naar een Azure log Analytics-werk ruimte. Gewenste waarschuwingen configureren in azure Log Analytics-werk ruimte.

Azure-activiteiten logboeken integreren in Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Azure Active Directory (Aad) risico detecties en identiteits beschermings functie om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. Daarnaast kunt u gegevens opnemen in azure Sentinel voor verder onderzoek.

Het weer geven van de Risk ante aanmeldingen voor AAD:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Risico beleid voor identiteits beveiliging configureren en inschakelen:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure-Sentinel onboarden:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3,13: <div>Micro soft toegang bieden tot relevante klant gegevens tijdens ondersteunings scenario's<br></div>

**Richt lijnen**: niet beschikbaar; Klanten-lockbox nog niet ondersteund voor Azure Batch.
 
Lijst met door Klanten-lockbox ondersteunde services: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbescherming

*Zie [beveiligings beheer: gegevens beveiliging](../security/benchmarks/security-control-data-protection.md)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Azure Batch groepen moeten worden gescheiden door het virtuele netwerk/subnet, op de juiste wijze worden gelabeld en beveiligd met een netwerk beveiligings groepen (NSG). Azure Batch gegevens moeten zijn opgenomen in een beveiligd Azure Storage-account.

Een Azure Batch groep maken binnen een Virtual Network:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Azure Storage accounts beveiligen:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens bewaken en blok keren.

**Richt lijnen**: voor Azure Storage accounts die zijn gekoppeld aan uw Azure batch groep (en) die gevoelige informatie bevatten, markeert u deze als gevoelige Tags en beveiligt u deze met behulp van de best practices van Azure.

De functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Azure Storage accounts beveiligen:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Hulp**: alle gevoelige gegevens in de overdracht versleutelen. Microsoft Azure resources onderhandelen standaard TLS 1,2. Zorg ervoor dat clients die verbinding maken met uw Azure Batch Pools of gegevens archieven (Azure Storage-accounts) kunnen onderhandelen over TLS 1,2 of hoger.

Zorg ervoor dat HTTPS vereist is om toegang te krijgen tot het opslag account dat uw Azure Batch gegevens bevat.

Meer informatie over Azure Storage account versleuteling in transit:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: voor Azure Storage accounts die zijn gekoppeld aan uw Azure batch groep (en) die gevoelige informatie bevatten, markeert u deze als gevoelige Tags en beveiligt u deze met behulp van de best practices van Azure.

De functies voor gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Azure Storage accounts beveiligen:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: gebruik Azure RBAC (op rollen gebaseerd toegangs beheer) voor het beheren van de toegang tot het beheer vlak van Azure-resources, inclusief batch-account, batch-pool (s) en opslag accounts.

Meer informatie over Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

Azure RBAC configureren:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: de functies gegevens identificatie, classificatie en verlies preventie zijn nog niet beschikbaar voor Azure Storage of reken resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

Informatie over de beveiliging van klant gegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: voor opslag accounts die zijn gekoppeld aan uw Azure batch-account, wordt aanbevolen micro soft toe te staan versleutelings sleutels te beheren, maar u hebt de mogelijkheid om zo nodig uw eigen sleutels te beheren.

Versleutelings sleutels voor Azure Storage accounts beheren:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in essentiële Azure-resources met betrekking tot of gekoppeld aan uw Azure batch accounts/groepen.

Diagnostische instellingen configureren voor opslag accounts die zijn gekoppeld aan Azure Batch groep om alle ruwe bewerkingen te controleren en te registreren op groeps gegevens.

Waarschuwingen voor Azure-activiteiten logboek gebeurtenissen maken:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Aanvullende logboek registratie/controle inschakelen voor een Azure Storage account:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische Hulpprogram Ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: voor knoop punten van Azure batch-groep bent u verantwoordelijk voor het beheer van de oplossing voor beveiligings problemen.

Als u een abonnement op Rapid7, Qualys of een ander platform voor beveiligings problemen hebt, kunt u de agents voor beveiligings evaluaties op batch-pool knooppunten hand matig installeren en knoop punten beheren via de desbetreffende Portal.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: micro soft om installatie kopieën van het basis Azure batch groeps knooppunt te onderhouden en bij te werken. Zorg ervoor dat het besturings systeem van Azure Batch pool-knoop punten wordt bijgewerkt voor de duur van de levens duur van het cluster, waardoor het mogelijk is om automatische updates in te scha kelen, de knoop punten te bewaken of periodiek opnieuw op te starten.


**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: geautomatiseerde oplossing voor Third Party Software patch beheer implementeren

**Hulp**: Zorg ervoor dat Azure batch toepassingen van derden worden bijgewerkt gedurende de levens duur van het cluster, waardoor het mogelijk is om automatische updates in te scha kelen, de knoop punten te bewaken of periodieke opnieuw op te starten.


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: als u een abonnement op Rapid7, Qualys of een ander platform voor beveiligings problemen hebt, kunt u de portal van die leverancier gebruiken om scans van back-to-back te bekijken en te vergelijken.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: gebruik een proces voor risico classificatie om prioriteit te geven aan het herstel van ontdekte beveiligings problemen.

**Richt lijnen**: gebruik een gemeen schappelijke risico Score programma (bijvoorbeeld een gemeen schappelijk score systeem voor beveiligings problemen) of de standaard risico classificaties van het hulp programma van derden.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, enzovoort) binnen uw abonnement (en). Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager (ARM)-resources te maken en te gebruiken.

Query's maken met Azure resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weer geven:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Meer informatie over Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om assets te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Aanvullende Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: behoud de inventaris van goedgekeurde Azure-resources en software titels.

**Hulp**: een lijst met goedgekeurde Azure-resources en goedgekeurde software voor reken bronnen definiëren


**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik**Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen uw abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**voor het implementeren van een oplossing van derden voor het bewaken van cluster knooppunten voor niet-goedgekeurde software toepassingen voor Azure batch groeps knooppunten.


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**voor het implementeren van een oplossing van derden voor het bewaken van cluster knooppunten voor niet-goedgekeurde software toepassingen voor Azure batch groeps knooppunten.


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: voor de knoop punten van een Azure batch-groep implementeert u een oplossing van derden om te voor komen dat onbevoegde software wordt uitgevoerd.


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik**Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:

- Niet toegestane resourcetypen
- Toegestane brontypen

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek resource type weigeren met Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: voor Azure batch groeps knooppunten implementeert u een oplossing van derden om te voor komen dat niet-geautoriseerde bestands typen worden uitgevoerd.


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>De mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken</div>

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing,

Dit is niet van toepassing op Azure Batch, omdat gebruikers (niet-beheerders) van de Azure Batch groepen geen toegang nodig hebben tot de afzonderlijke knoop punten om taken uit te voeren. De Cluster beheerder heeft al hoofd toegang tot alle knoop punten.


**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing, Bench Mark is bedoeld voor webtoepassingen die worden uitgevoerd op Azure app service-of IaaS-instanties.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](../security/benchmarks/security-control-secure-configuration.md)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' Microsoft.Bat' om aangepaste beleids regels te maken om de configuratie van uw Azure batch-accounts en-groepen te controleren of af te dwingen.

Beschik bare Azure Policy aliassen weer geven:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: veilige configuraties maken voor uw besturings systeem

**Richt lijnen**: veilige configuraties instellen voor het besturings systeem van uw batch-pool knooppunten.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: veilige configuraties voor alle Azure-resources onderhouden

**Richt lijnen**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor de Azure-resources die zijn gerelateerd aan uw batch-account en Pools (zoals virtuele netwerken, subnetten, Azure-firewalls, Azure Storage accounts, enzovoort). U kunt Azure Policy aliassen van de volgende naam ruimten gebruiken om aangepast beleid te maken:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy effecten begrijpen: https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: beveiligde configuraties voor besturings systemen onderhouden

**Hulp**: de installatie kopieën van het besturings systeem Azure batch worden beheerd en onderhouden door micro soft. U bent verantwoordelijk voor het implementeren van de status configuratie op besturingssysteem niveau.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Richt lijnen**: als u aangepaste Azure Policy definities gebruikt voor uw Azure batch accounts, Pools of gerelateerde resources, gebruikt u Azure opslag plaatsen om uw code veilig op te slaan en te beheren.

Code opslaan in azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Documentatie voor Azure opslag plaatsen:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: als u aangepaste installatie kopieën gebruikt voor uw Azure batch groepen, gebruikt u Azure RBAC (op rollen gebaseerd toegangs beheer) om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën.

Meer informatie over Azure RBAC:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Azure RBAC configureren:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Hulpprogram Ma's voor het beheer van systeem configuratie implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities om Azure batch-gerelateerde resource configuraties te Signa lering, te controleren en af te dwingen.  Gebruik Azure Policy aliassen in de naam ruimte ' Microsoft.Bat' om aangepaste beleids regels te maken voor uw Azure Batch-accounts en-groepen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

Azure Policy configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Hulpprogram Ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Hulp**: Implementeer een oplossing van derden om de gewenste status te behouden voor de besturings systemen van uw Azure batch-groeps knooppunten.


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' Microsoft.Bat' om aangepaste beleids regels te maken om de configuratie van uw Azure batch exemplaar te controleren of af te dwingen. U kunt ook ingebouwde beleids regels gebruiken die specifiek zijn gemaakt voor Azure Batch of de resources die door Azure Batch worden gebruikt, zoals:

- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep

-Opslag accounts moeten een service-eind punt van een virtueel netwerk gebruiken

- Diagnostische logboeken in Batch-accounts moeten worden ingeschakeld

Beschik bare Azure Policy aliassen weer geven: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy configureren en beheren: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Hulp**: Implementeer een oplossing van derden om de status van de besturings systemen van uw Azure batch-groeps knooppunten te bewaken.


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="711-securely-manage-azure-secrets"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: Azure Key Vault kunnen worden gebruikt met Azure batch-implementaties voor het beheren van sleutels voor pool opslag binnen Azure Storage accounts.

Integratie met door Azure beheerde identiteiten:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een Key Vault maken:

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

Verificatie bij Key Vault:

https://docs.microsoft.com/azure/key-vault/general/authentication

Een Key Vault toegangs beleid toewijzen:

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: niet beschikbaar, Managed Service Identity niet ondersteund door Azure batch


**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault. 

Referentie scanner instellen: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](../security/benchmarks/security-control-malware-defense.md)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Hulp**: gebruik Windows Defender op uw afzonderlijke Azure batch pool-knoop punten in het geval van Windows-besturings systemen, of geef uw eigen oplossing voor anti-malware op als u Linux gebruikt.


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: micro soft antimalware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure batch), maar wordt niet uitgevoerd op de inhoud van de klant.

Scan vooraf bestanden die worden geüpload naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, enzovoort. Micro soft heeft geen toegang tot klant gegevens in deze instanties.

Meer informatie over micro soft antimalware voor Azure Cloud Services en Virtual Machines:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Hulp**: gebruik Windows Defender op uw afzonderlijke Azure batch pool-knoop punten in het geval van Windows-besturings systemen en zorg ervoor dat automatische updates is ingeschakeld. Geef uw eigen oplossing voor anti-malware op als u Linux gebruikt.


**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](../security/benchmarks/security-control-data-recovery.md)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Richt lijnen**: als u een Azure Storage-account gebruikt voor het gegevens archief van de Azure batch groep, kiest u de juiste optie voor redundantie (LRS, ZRS, GRS, RA-GRS). 

Opslag redundantie configureren voor Azure Storage accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Richt lijnen**: als u een Azure Storage-account gebruikt voor het gegevens archief van de Azure batch groep, kiest u de juiste optie voor redundantie (LRS, ZRS, GRS, RA-GRS).  Als u Azure Key Vault voor elk onderdeel van uw Azure Batch-implementatie gebruikt, moet u ervoor zorgen dat er een back-up van uw sleutels wordt gemaakt.

Opslag redundantie configureren voor Azure Storage accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Hoe kan ik een back-up maken van sleutel kluis sleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: als u uw eigen sleutels beheert voor Azure Storage accounts of andere bronnen die betrekking hebben op uw Azure batch-implementatie, kunt u het herstellen van back-upsleutels periodiek testen.

Hoe kan ik een back-up maken van sleutel kluis sleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Een door de klant beheerde sleutel herstellen met Power shell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: als Azure Key Vault wordt gebruikt om sleutels te bewaren die betrekking hebben op Azure batch pool-opslag accounts, schakelt u Soft-Delete in azure Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

Het inschakelen van zacht verwijderen in Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-incident-response-guide"></a>10,1: respons gids voor incidenten maken

**Richt lijnen**: Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die rollen van personeel en fasen van incident handling/Management definiëren.

Werk stroom automatisering configureren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: het maken van scores en prioriteits procedures voor incidenten

**Hulp**: Security Center wijst een Ernst toe aan waarschuwingen, zodat u de volg orde van de instructies voor elke waarschuwing kunt bepalen, zodat u meteen aan de voor waarde krijgt wanneer een bron is aangetast. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren &nbsp; voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij.

De Azure Security Center Security-contact persoon instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

Continue export configureren:

https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

Werk stroom automatisering en Logic Apps configureren:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgen ervoor dat alle essentiële beveiligings resultaten binnen 60 dagen worden hersteld.

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

U vindt hier meer informatie over de strategie van micro soft en de uitvoering van Red Teaming en live site indringings tests ten opzichte van micro soft Managed Cloud Infrastructure, services en toepassingen, hier: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligings basislijnen](../security/benchmarks/security-baselines-overview.md)

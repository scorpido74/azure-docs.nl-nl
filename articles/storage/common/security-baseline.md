---
title: Azure-beveiligings basislijn voor Azure Storage
description: Azure-beveiligings basislijn voor Azure Storage
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: cd7e7df5c789743cf6bd84c6150fd901490bdedd
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84751637"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure-beveiligings basislijn voor Azure Storage

De Azure-beveiligings basislijn voor Azure Storage bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Hulp**: Configureer de firewall van uw opslag account door de toegang te beperken tot clients van specifieke open bare IP-adresbereiken, virtuele netwerken (VNets) te selecteren in azure of naar specifieke Azure-resources. U kunt ook privé-eind punten configureren, zodat het verkeer naar de opslag service van uw bedrijf uitsluitend via particuliere netwerken wordt verplaatst.

Opmerking: klassieke opslag accounts bieden geen ondersteuning voor firewalls en virtuele netwerken.

- [De Azure Storage firewall configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Privé-eind punten voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1,2: Vnet-, subnet-en NIC-configuratie en-verkeer bewaken en vastleggen

**Hulp**: Azure Storage biedt een gelaagd beveiligings model. U kunt de toegang tot uw opslag account beperken tot aanvragen die afkomstig zijn van opgegeven IP-adressen, IP-adresbereiken of uit een lijst met subnetten in een Azure Virtual Network (VNet). U kunt Azure Security Center gebruiken en aanbevelingen voor netwerk beveiliging volgen om uw netwerk bronnen in azure te beveiligen. Schakel ook NSG-stroom Logboeken in voor virtuele netwerken/subnet die zijn geconfigureerd voor de opslag accounts via de firewall van het opslag account en logboeken verzenden naar een opslag account voor verkeers controle. 

Als u privé-eind punten aan uw opslag account hebt gekoppeld, kunt u geen NSG-regels (netwerk beveiligings groep) voor subnetten configureren. 

- [Azure Storage firewalls en virtuele netwerken configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [NSG-stroom logboeken inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Informatie over netwerk beveiliging die wordt verschaft door Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Meer informatie over privé-eind punten voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: niet van toepassing; aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: geavanceerde beveiliging tegen bedreigingen inschakelen voor uw Azure Storage-account. Advanced Threat Protection voor Azure Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van opslag accounts worden gedetecteerd. Azure Security Center geïntegreerde waarschuwingen zijn gebaseerd op activiteiten waarvoor netwerk communicatie is gekoppeld aan een IP-adres dat is omgezet, ongeacht of het IP-adres een bekend riskant IP-adres is (bijvoorbeeld een bekend cryptominer) of een IP-adres dat niet eerder is herkend als riskant. Beveiligings waarschuwingen worden geactiveerd wanneer afwijkingen in de activiteit optreden. 

- [Geavanceerde beveiliging tegen bedreigingen inschakelen](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Hulp**: bij het vastleggen van Network Watcher-pakketten kunt u opname sessies maken om verkeer tussen het opslag account en een virtuele machine bij te houden. Er worden filters voor de opname sessie gegeven om ervoor te zorgen dat u alleen het gewenste verkeer vastlegt. Met pakket opname kunt u netwerk afwijkingen, zowel reactief als proactief, vaststellen. Andere gebruiken zijn onder andere het verzamelen van netwerk statistieken, het verkrijgen van informatie over inbreuken op het netwerk, het opsporen van fouten in client-server communicatie en nog veel meer. Als u pakket opnames op afstand wilt activeren, vereenvoudigt u de belasting van het hand matig uitvoeren van een pakket opname op een gewenste virtuele machine, waardoor kost bare tijd wordt bespaard. 

- [Pakket opnames beheren met Azure Network Watcher met behulp van de portal](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-manage-portal)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen implementeren

**Richt lijnen**: Advanced threat protection voor Azure Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van opslag accounts worden gedetecteerd. Beveiligings waarschuwingen worden geactiveerd wanneer afwijkingen in de activiteit optreden. Deze beveiligings waarschuwingen zijn geïntegreerd met Azure Security Center en worden ook via e-mail verzonden naar abonnements beheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

- [Geavanceerde bedreigings beveiliging voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: verkeer naar uw webtoepassingen beheren

**Richt lijnen**: niet van toepassing; aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Richt lijnen**: voor bron in virtuele netwerken die toegang nodig hebben tot uw opslag account, gebruikt u Virtual Network Service Tags voor het geconfigureerde virtuele netwerk om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld opslag) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen. 

Gebruik Virtual Network service-eindpunt beleid als het bereik van netwerk toegang moet worden toegewezen aan specifieke opslag accounts.

- [Voor meer informatie over het gebruik van service Tags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Voor meer informatie over het beleid voor service-eind punten van virtuele netwerken voor Azure Storage](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: Service

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk bronnen die zijn gekoppeld aan uw Azure Storage-Account met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. Storage ' en ' micro soft. Network ' om aangepaste beleids regels te maken voor het controleren of afdwingen van de netwerk configuratie van de resources van uw opslag account. 

U kunt ook gebruikmaken van ingebouwde beleids definities met betrekking tot het opslag account, zoals: opslag accounts moeten een service-eind punt van een virtueel netwerk gebruiken 

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Voor beelden Azure Policy voor opslag](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage)

- [Azure Policy voor beelden voor netwerk](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Een Azure Blueprint maken](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Hulp**: Labels gebruiken voor netwerk beveiligings groepen (NSG) en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan. Gebruik een van de ingebouwde Azure Policy definities die betrekking hebben op labelen, zoals ' tag vereisen en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources. U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren. 

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Een Virtual Network maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Een NSG maken met een beveiligings configuratie](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische Hulpprogram Ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: gebruik Azure Policy om configuratie wijzigingen voor netwerk bronnen te registreren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in kritieke netwerk bronnen plaatsvinden. 

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Waarschuwingen maken in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-resource"></a>2,1: goedgekeurde tijd synchronisatie Resource gebruiken

**Richt lijnen**: niet van toepassing; Micro soft onderhoudt tijd bronnen voor Azure Storage accounts.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken via Azure monitor voor het verzamelen van beveiligings gegevens die worden gegenereerd door eind punten, netwerk bronnen en andere beveiligings systemen. In Azure Monitor kunt u Log Analytics werk ruimte (n) gebruiken om een query uit te voeren en te analyseren en Azure Storage-accounts te gebruiken voor lange termijn/archief opslag, eventueel met beveiligings functies zoals onveranderlijke opslag en afgedwongen Bewaar perioden.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: Azure Opslaganalyse biedt logboeken voor blobs, wacht rijen en tabellen. U kunt de Azure Portal gebruiken om te configureren welke logboeken voor uw account worden vastgelegd. 

- [Bewaking configureren voor uw Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-monitoring-for-a-storage-account)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: wanneer u beveiligings logboeken opslaat in het Azure Storage-account of log Analytics-werk ruimte, kunt u het Bewaar beleid instellen op basis van de vereisten van uw organisatie. 

- [Bewaar beleid configureren voor logboeken van Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

- [De Bewaar periode voor gegevens wijzigen in Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: als u de Azure Storage-logboeken wilt bekijken, zijn er de gebruikelijke opties zoals query's via de log Analytics-aanbieding en een unieke optie om de logboek bestanden rechtstreeks weer te geven. In Azure Storage worden de Logboeken opgeslagen in blobs die rechtstreeks toegankelijk moeten zijn op http://accountname.blob.core.windows.net/ $logs (de map voor logboek registratie is standaard verborgen, zodat u rechtstreeks naar een andere moet navigeren. Het wordt niet weer gegeven in lijst opdrachten.) 

Schakel ook geavanceerde beveiliging tegen bedreigingen in voor uw Azure Storage-account. Advanced Threat Protection voor Azure Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van opslag accounts worden gedetecteerd. Beveiligings waarschuwingen worden geactiveerd wanneer afwijkingen in de activiteit optreden. Deze beveiligings waarschuwingen zijn geïntegreerd met Azure Security Center en worden ook via e-mail verzonden naar abonnements beheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

- [Gegevens registreren en controleren](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored)

- [Geavanceerde beveiliging tegen bedreigingen inschakelen](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Richt lijnen**: schakel in azure Security Center geavanceerde beveiliging tegen bedreigingen in voor het opslag account. Schakel de diagnostische instellingen voor het opslag account in en verzend logboeken naar een Log Analytics-werk ruimte. Onboarding van uw Log Analytics-werk ruimte naar Azure-Sentinel, omdat dit een via-oplossing (Security Orchestration Automated Response) biedt. Hiermee kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligings problemen op te lossen. 

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Waarschuwingen beheren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Een waarschuwing over logboek gegevens van log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Azure Storage-analyselogboeken](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Hulp**: Azure Security Center gebruiken en bedreigings beveiliging inschakelen voor Azure Storage voor het detecteren van malware-uploads naar Azure Storage met behulp van hash-reputatie analyse en verdachte toegang vanaf een actieve Tor-afsluit knooppunt (een anoniem-proxy). 

- [Geavanceerde bedreigings beveiliging voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: de oplossing Azure DNS Analytics (preview) in azure monitor verzamelt inzicht in de DNS-infra structuur voor beveiliging, prestaties en bewerkingen. Dit biedt momenteel geen ondersteuning voor Azure Storage-accounts u kunt de DNS-registratie oplossing van derden echter gebruiken. 

- [Inzichten over uw DNS-infra structuur verzamelen met de preview-oplossing van DNS-analyse](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; Bench Mark is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteit en Access Control](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: inventaris van beheerders accounts onderhouden

**Hulp**: Azure AD heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen. 

- [Een directory-rol verkrijgen in azure AD met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: Azure Storage accounts of Azure Active Directory het concept van standaard-of lege wacht woorden. Azure Storage implementeert een model voor toegangs beheer dat ondersteuning biedt voor op rollen gebaseerd toegangs beheer (RBAC) van Azure, evenals gedeelde sleutel en gedeelde toegangs handtekeningen (SAS). Een kenmerk van gedeelde sleutel en SAS-verificatie is dat er geen identiteit aan de oproepende functie is gekoppeld en daarom niet kan worden gemachtigd om op basis van een machtiging voor beveiliging-principal een autorisatie uit te voeren. 

- [Toegang tot gegevens in Azure Storage autoriseren](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Informatie over beveiligings-principals en toegangs beheer voor Azure Storage account](https://docs.microsoft.com/azure/storage/common/storage-introduction)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts die toegang hebben tot uw opslag account. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken. 

U kunt ook Just-in-time/alleen-voldoende toegang inschakelen met behulp van Azure AD Privileged Identity Management geprivilegieerde rollen voor micro soft-Services en Azure ARM. 

- [Inzicht in Azure Security Center identiteit en toegang](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Overzicht van Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik waar mogelijk Azure Active Directory-SSO in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer. 

- [Informatie over eenmalige aanmelding met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Toegang tot gegevens in Azure Storage autoriseren](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Toegang verlenen tot blobs en wacht rijen met behulp van Azure Active Directory](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle toegang op basis van Azure Active Directory.

**Hulp**bij het inschakelen van Azure Active Directory multi-factor Authentication en het volgen van Azure Security Center identiteits-en toegangs beheer aanbevelingen voor het beveiligen van de resources van uw opslag account. 

- [MFA inschakelen in azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Identiteit en toegang bewaken in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met MFA dat is geconfigureerd voor aanmelding bij en configureren van opslag account resources. 

- [Meer informatie over privileged Access workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [MFA inschakelen in azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: verzend waarschuwingen voor Azure Security Center risico detectie naar Azure monitor en configureer aangepaste waarschuwingen/meldingen met behulp van actie groepen. Geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure Storage account om waarschuwingen te genereren voor verdachte activiteiten. Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag. 

- [Geavanceerde bedreigings beveiliging instellen voor Azure Storage-account](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

- [Meer informatie over Azure AD-risico detectie](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

- [Actie groepen configureren voor aangepaste waarschuwingen en meldingen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: Azure-resource alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's. 

- [Benoemde locaties configureren in azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD biedt op rollen gebaseerd toegangs beheer (RBAC) voor nauw keurige controle over de toegang van een client tot bronnen in een opslag account.  Gebruik indien mogelijk Azure AD-referenties als een beveiligings best practice, in plaats van de account sleutel te gebruiken, die eenvoudiger kan worden aangetast. Wanneer het ontwerp van uw toepassing gedeelde toegangs handtekeningen vereist voor toegang tot Blobopslag, moet u Azure AD-referenties gebruiken om een gebruikers delegering van Shared Access signatures (SAS) te maken wanneer dat mogelijk is voor een superieure beveiliging.

- [Een Azure AD-exemplaar maken en configureren](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [De resource provider van Azure Storage gebruiken om toegang te krijgen tot beheer resources](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Toegang tot Azure Blob en wachtrij gegevens configureren met RBAC in Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Toegang tot gegevens in Azure Storage autoriseren](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Richt lijnen**: raadpleeg de Azure Active Directory-Logboeken om verouderde accounts te detecteren, die kunnen bestaan uit beheerders rollen voor het opslag account. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen efficiënt te beheren, toegang te krijgen tot bedrijfs toepassingen die kunnen worden gebruikt voor toegang tot de resources van het opslag account en roltoewijzingen. Gebruikers toegang moet regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.  

U kunt ook een Shared Access Signature (SAS) gebruiken om beveiligde gedelegeerde toegang tot resources in uw opslag account te bieden zonder de beveiliging van uw gegevens in gevaar te brengen. U kunt bepalen welke resources de client mag openen, welke machtigingen ze hebben op deze resources en hoe lang de SAS geldig is, onder andere para meters.

Lees ook anonieme lees toegang tot containers en blobs. De standaardinstelling is dat een container en alle bijbehorende blobs alleen toegankelijk zijn voor een gebruiker die over de juiste machtigingen beschikt. U kunt Azure Monitor gebruiken om te waarschuwen voor anonieme toegang voor opslag accounts met behulp van anonieme verificatie.

Een efficiënte manier om het risico van onvermoede gebruikers accounts te beperken, is het beperken van de duur van toegang die u verleent aan gebruikers. Time-Limited SAS-Uri's zijn een efficiënte manier om gebruikers toegang tot een opslag account automatisch te laten verlopen. Daarnaast is het draaien van opslag account sleutels regel matig een manier om ervoor te zorgen dat onverwachte toegang via de sleutels van het opslag account een beperkte duur is.

- [Meer informatie over Azure AD-rapportage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Toegang op het niveau van Azure Storage-account weer geven en wijzigen](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

- [Anonieme leestoegang tot containers en blobs beheren](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)

- [Een Storage-account bewaken in de Azure-portal](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

- [Toegangs sleutels voor opslag accounts beheren](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Hulp**: met Opslaganalyse kunt u gedetailleerde informatie over geslaagde en mislukte aanvragen in een opslag service vastleggen. Alle logboeken worden opgeslagen in blok-blobs in een container met de naam $logs, die automatisch wordt gemaakt wanneer Opslaganalyse is ingeschakeld voor een opslag account.

Diagnostische instellingen voor Azure Active Directory gebruikers accounts maken, de audit logboeken en aanmeldings logboeken verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste waarschuwingen configureren in Log Analytics werk ruimte. Als u verificatie fouten wilt controleren op Azure Storage accounts, kunt u waarschuwingen maken om u te waarschuwen wanneer bepaalde drempel waarden zijn bereikt voor metrische gegevens van de opslag resource. Daarnaast kunt u met behulp van Azure Monitor op anonieme toegang waarschuwen voor opslag accounts met behulp van anonieme verificatie.

- [Azure Storage-analyselogboeken](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Azure-activiteiten logboeken integreren in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Metrische waarschuwingen voor Azure Storage accounts configureren](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik de functies voor risico-en identiteits beveiliging van Azure Active Directory om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op de resources van uw opslag account. Schakel automatische antwoorden via Azure Sentinel in om de beveiligings reacties van uw organisatie te implementeren. 

- [Risk ante aanmeldingen voor Azure AD weer geven](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: in ondersteunings Scenario's waarin micro soft toegang moet krijgen tot klant gegevens, biedt klanten-lockbox (Preview voor het opslag account) een interface waarmee klanten aanvragen voor gegevens toegang van klanten kunnen controleren en goed keuren of afwijzen. Micro soft vereist geen toegang tot de geheimen van uw organisatie die zijn opgeslagen in het opslag account.

- [Klanten-lockbox begrijpen](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken bij het volgen van opslag account bronnen voor het opslaan of verwerken van gevoelige informatie. 

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: isolatie implementeren met behulp van afzonderlijke abonnementen, beheer groepen en opslag accounts voor afzonderlijke beveiligings domeinen, zoals omgeving, gegevens gevoeligheid.  U kunt uw opslag account beperken om het toegangs niveau te bepalen voor uw opslag accounts die uw toepassingen en bedrijfs omgevingen vereisen, op basis van het type en de subset van netwerken die worden gebruikt. Wanneer netwerk regels zijn geconfigureerd, hebben alleen toepassingen die gegevens aanvragen via de opgegeven set netwerken toegang tot een opslag account. U kunt de toegang tot Azure Storage beheren via Azure AD RBAC. U kunt ook privé-eind punten configureren om de beveiliging te verbeteren als verkeer tussen uw virtuele netwerk en de service gaat over het micro soft backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen. 

- [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Beheergroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Storage firewalls en virtuele netwerken configureren](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [Service-eind punten Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens bewaken en blok keren.

**Richt lijnen**: voor opslag account bronnen die gevoelige informatie opslaan of verwerken, markeert u de resources als gevoelig met behulp van tags. Om het risico van gegevens verlies via exfiltration te verminderen, beperkt u het uitgaande netwerk verkeer voor Azure Storage accounts met behulp van Azure Firewall. 

Daarnaast kunt u het virtuele netwerk service-eindpunt beleid gebruiken om uitgaand virtueel netwerk verkeer te filteren op Azure Storage accounts via service-eind punten en alleen gegevens exfiltration toe te staan op specifieke Azure Storage accounts.

- [Azure Storage firewalls en virtuele netwerken configureren](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

- [Beleid voor service-eind punten voor virtuele netwerken voor Azure Storage](https://docs.microsoft.com/azure/private-link/create-private-endpoint-storage-portal)

- [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: u kunt het gebruik van HTTPS afdwingen door de vereiste beveiligde overdracht voor het opslag account in te scha kelen. Als u deze optie hebt ingeschakeld, worden verbindingen die gebruikmaken van HTTP geweigerd. Daarnaast kunt u Azure Security Center en Azure Policy gebruiken om een veilige overdracht af te dwingen voor uw opslag account.

- [Veilige overdracht vereisen in Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)

- [Azure-beveiligings beleid bewaakt door Security Center](https://docs.microsoft.com/azure/security-center/security-center-policy-definitions)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: gegevens identificatie functies zijn nog niet beschikbaar voor Azure Storage account en gerelateerde resources. Implementeer oplossingen van derden, indien nodig voor nalevings doeleinden. 

- [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via op rollen gebaseerd toegangs beheer (RBAC). Azure Storage definieert een set ingebouwde RBAC-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot BLOB-of wachtrij gegevens. 

- [RBAC-rollen toewijzen voor Azure Storage account](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal)

- [De resource provider van Azure Storage gebruiken om toegang te krijgen tot beheer resources](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Toegang tot Azure Blob en wachtrij gegevens configureren met RBAC in Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Een AAD-exemplaar maken en configureren](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Toegang tot gegevens in Azure Storage autoriseren](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: Azure Storage versleuteling is ingeschakeld voor alle opslag accounts en kan niet worden uitgeschakeld. Azure Storage versleutelt uw gegevens automatisch wanneer deze in de cloud worden bewaard. Wanneer u gegevens uit Azure Storage leest, worden deze door Azure Storage ontsleuteld voordat ze worden geretourneerd. Met Azure Storage versleuteling kunt u uw gegevens op rest beveiligen zonder dat u code hoeft te wijzigen of code aan toepassingen hoeft toe te voegen. 

- [Meer informatie over Azure Storage versleuteling in rust](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in de resources van het opslag account. U kunt ook Azure Storage logboek registratie inschakelen om bij te houden hoe elke aanvraag voor Azure Storage is geautoriseerd. De logboeken geven aan of een aanvraag anoniem is gemaakt door gebruik te maken van een OAuth 2,0-token, met behulp van gedeelde sleutel of door gebruik te maken van een Shared Access Signature (SAS). Daarnaast kunt u met behulp van Azure Monitor op anonieme toegang waarschuwen voor opslag accounts met behulp van anonieme verificatie.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Azure Storage-analyselogboeken](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Metrische waarschuwingen voor Azure Storage accounts configureren](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische Hulpprogram Ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Volg aanbevelingen van Azure Security Center om de configuratie van uw opslag accounts continu te controleren en te controleren. 

- [Aanbevelingen voor beveiliging: een referentie gids](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: geautomatiseerde oplossing voor Third Party Software patch beheer implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; Micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor opslag accounts.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: gebruik een proces voor risico classificatie om prioriteit te geven aan het herstel van ontdekte beveiligings problemen.

**Richt lijnen**: gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center. 

- [Azure Security Center beveiligde Score begrijpen](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken om alle resources (inclusief opslag accounts) in uw abonnement (en) te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen. 

- [Query's maken met Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Uw Azure-abonnementen weer geven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Meer informatie over Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op opslag account resources die meta gegevens geven om ze logisch in een taxonomie te organiseren. 

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, indien van toepassing, om opslag accounts en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement. 

Gebruik Advanced Threat Protection voor Azure Storage ook om niet-geautoriseerde Azure-resources te detecteren. 

- [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Beheergroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Geavanceerde bedreigings beveiliging voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: behoud de inventaris van goedgekeurde Azure-resources en software titels.

**Richt lijnen**: u moet een inventaris van goedgekeurde Azure-resources maken conform uw organisatie behoeften. 


**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: gebruik**Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities: 

 - Niet toegestane brontypen 
 - Toegestane brontypen 

Daarnaast gebruikt u de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen. Dit kan helpen bij omgevingen met hoge beveiliging, zoals die met opslag accounts. 

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Query's maken met Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: klanten kunnen voor komen dat resources worden gemaakt of gebruikt met Azure policy zoals vereist door het bedrijfs beleid van de klant. 

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik**Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities: 

- Niet toegestane brontypen 
- Toegestane brontypen 

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management. Dit kan ertoe leiden dat het maken en wijzigen van resources binnen een omgeving met hoge beveiliging, zoals die met opslag accounts, wordt voor komen. 

- [Voorwaardelijke toegang configureren om toegang tot ARM te blok keren](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of reken bronnen.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Storage ' om aangepaste beleids regels te maken om de configuratie van uw opslag account-exemplaren te controleren of af te dwingen. U kunt ook ingebouwde Azure Policy definities voor Azure Storage account gebruiken, zoals: 

Onbeperkte netwerktoegang tot opslagaccounts controleren  
Advanced Threat Protection implementeren voor opslagaccounts  
Opslagaccounts moeten worden gemigreerd naar nieuwe Azure Resource Manager-resources  
Beveiligde overdracht naar opslagaccounts moet zijn ingeschakeld  

Gebruik aanbevelingen van Azure Security Center als een veilige configuratie basislijn voor uw opslag accounts. 

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: veilige configuraties maken voor uw besturings systeem

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: veilige configuraties voor alle Azure-resources onderhouden

**Hulp**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor de resources van uw opslag account. 

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Wat zijn Azure Policy effecten?](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: beveiligde configuraties voor besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure opslag plaatsen gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels, Azure Resource Manager sjablonen, gewenste status configuratie scripts, enzovoort. Als u toegang wilt krijgen tot de resources die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps, of Active Directory als dit is geïntegreerd met TFS.

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Over machtigingen en groepen in azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: Hulpprogram Ma's voor het beheer van systeem configuratie implementeren

**Hulp**: maakt gebruik van Azure Policy om systeem configuraties voor opslag accounts te signasen, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen. 

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: Hulpprogram Ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: Maak gebruik van Azure Security Center om basislijn scans uit te voeren voor de resources van uw Azure Storage-account. 

- [Aanbevelingen herstellen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-securely-manage-azure-secrets"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: Azure Storage worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. U kunt door micro soft beheerde sleutels gebruiken voor de versleuteling van het opslag account of versleuteling beheren met hun eigen sleutels. Als u door de klant verschafte sleutels gebruikt, kunt u gebruikmaken van Azure Key Vault om de sleutels veilig op te slaan. 

U kunt ook de sleutels van het opslag account regel matig draaien om de impact van het verlies of de openbaar making van de sleutels van het opslag account te beperken.

- [Azure Storage-versleuteling voor inactieve gegevens](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [Toegangs sleutels voor opslag accounts beheren](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: toegang verlenen tot blobs en wacht rijen binnen Azure Storage Accounts met Azure Active Directory en beheerde identiteiten. Azure Blob-en Queue Storage ondersteunen Azure Active Directory-verificatie (Azure AD) met beheerde identiteiten voor Azure-resources. Beheerde identiteiten voor Azure-resources kunnen toegang verlenen tot Blob-en wachtrij gegevens met behulp van Azure AD-referenties van toepassingen die worden uitgevoerd in azure virtual machines (Vm's), functie-apps, schaal sets voor virtuele machines en andere services. Door beheerde identiteiten voor Azure-resources te gebruiken in combi natie met Azure AD-verificatie kunt u voor komen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd. 

- [Toegang verlenen tot Azure Blob-en wachtrij gegevens met behulp van een beheerde identiteit](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault. 

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft verzorgt anti-malware voor het onderliggende platform.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Hulp**: gebruik beveiliging tegen bedreigingen voor Azure Storage voor het detecteren van malware-uploads naar Azure Storage met behulp van hash-reputatie analyse en verdachte toegang vanaf een actieve Tor-afsluit knooppunt (een anoniem-proxy). 

U kunt ook alle inhoud voor schadelijke software scannen voordat u deze uploadt naar niet-reken resources van Azure, zoals App Service, Data Lake Storage, Blob Storage, enzovoort om te voldoen aan de vereisten van uw organisatie.

- [Geavanceerde bedreigings beveiliging voor Azure Storage configureren](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor reken resources. Micro soft verzorgt anti-malware voor het onderliggende platform.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: de gegevens in uw Microsoft Azure Storage-account worden altijd automatisch gerepliceerd om duurzaamheid en hoge Beschik baarheid te garanderen. Azure Storage kopieert uw gegevens, zodat deze worden beveiligd tegen geplande en niet-geplande gebeurtenissen, waaronder tijdelijke hardwarestoringen, netwerk-of energie storingen en enorme natuur rampen. U kunt ervoor kiezen om uw gegevens te repliceren binnen hetzelfde Data Center, op zonegebonden-data centrums binnen dezelfde regio of in geografisch gescheiden regio's. 

U kunt Azure Automation ook inschakelen om regel matige moment opnamen van de blobs te maken.

- [Informatie over Azure Storage redundantie en service level agreements](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [Een moment opname van een BLOB maken](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Overzicht van Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Richt lijnen**: voor het maken van back-ups van services die worden ondersteund door het opslag account, zijn er meerdere methoden beschikbaar, met inbegrip van het gebruik van azcopy of hulpprogram ma's van derden. Onveranderbare opslag voor Azure Blob-opslag stelt gebruikers in staat om bedrijfskritische gegevens objecten op te slaan in een WORM (eenmaal schrijven, gelezen). Met deze status worden de gegevens niet-kan worden gewist en niet kunnen worden gewijzigd voor een door de gebruiker opgegeven interval.

- [Aan de slag met AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

- [Onveranderbaarheid-beleid instellen en beheren voor Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

Door de klant beheerde/beschik bare sleutels kunnen worden ondersteund in Azure Key Vault met behulp van Azure CLI of Power shell. 

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: regel matig gegevens herstel van uw Key Vault-certificaten, sleutels, beheerde opslag accounts en geheimen uitvoeren met de volgende Power shell-opdrachten: 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Key Vault certificaten herstellen](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Key Vault sleutels herstellen](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Key Vault beheerde opslag accounts herstellen](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault geheimen herstellen](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [AzCopy is een opdracht regel programma dat u kunt gebruiken voor het kopiëren van blobs, bestanden en tabel gegevens naar of van een opslag account](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

Opmerking: als u gegevens wilt kopiëren van en naar de Azure Table Storage-service, installeert u vervolgens AzCopy versie 7,3.


**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: als u door de klant beheerde sleutels wilt inschakelen voor een opslag account, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet de instellingen voorlopig verwijderen en niet wissen in de sleutel kluis inschakelen. Met de functie voor voorlopig verwijderen van Key Vault kunt u verwijderde kluizen en kluis objecten, zoals sleutels, geheimen en certificaten, herstellen. Als er een back-up wordt gemaakt van gegevens van een opslag account naar Azure Storage blobs, schakelt u de optie zacht verwijderen in om uw gegevens op te slaan en te herstellen wanneer blobs of BLOB-moment opnamen worden verwijderd Behandel uw back-ups als gevoelige gegevens en pas de relevante besturings elementen voor toegang en gegevens bescherming toe als onderdeel van deze basis lijn. Daarnaast kunt u voor betere beveiliging essentiële gegevens objecten van het bedrijf opslaan in een WORM (Write Once, Read Many).

- [De tijdelijke verwijdering van Azure Key Vault gebruiken](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

- [Voorlopig verwijderen voor Azure Storage-blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Bedrijfs kritieke blobgegevens opslaan met onveranderlijke opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10,1: respons gids voor incidenten maken

**Hulp**: een antwoord gids voor incidenten maken voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle werk rollen definiëren, evenals fasen van incident handling/management van detectie tot een beoordeling van het incident.

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: het maken van scores en prioriteits procedures voor incidenten

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid. 

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [Tags gebruiken om uw Azure-resources te organiseren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

- [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Waarschuwingen streamen naar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

- [Werk stroom automatisering en Logic Apps configureren](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11,1: regel matig indringings tests van uw Azure-resources

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

---
title: Azure-beveiligings basislijn voor Virtual Machine Scale Sets
description: De Virtual Machine Scale Sets Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 89da86d753f746774737c248ee318c7a377dd7a0
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007094"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>Azure-beveiligings basislijn voor Virtual Machine Scale Sets

De Azure-beveiligings basislijn voor Virtual Machine Scale Sets bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie [overzicht van Azure Security-basis lijnen](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Richt lijnen**: wanneer u een virtuele machine van Azure (VM) maakt, moet u een virtueel netwerk maken of een bestaand virtueel netwerk gebruiken en de virtuele machine met een subnet configureren. Zorg ervoor dat voor alle geïmplementeerde subnetten een netwerk beveiligings groep is toegepast met netwerk toegangs beheer die specifiek is voor uw toepassingen vertrouwde poorten en bronnen.

Als u een specifieke use-case voor een gecentraliseerde firewall hebt, kunt Azure Firewall ook worden gebruikt om aan deze vereisten te voldoen.

* [Netwerken voor virtuele-machineschaalsets in Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [Een Virtual Network maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Een NSG maken met een beveiligings configuratie](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Azure Firewall implementeren en configureren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en netwerk interfaces bewaken en vastleggen

**Hulp**: gebruik de Azure Security Center voor het identificeren en volgen van aanbevelingen voor netwerk beveiliging voor het beveiligen van uw Azure virtual machine-resources (VM) in Azure. Schakel logboeken voor NSG-stroom in en verzend logboeken naar een opslag account voor de verkeers controle voor de Vm's voor ongebruikelijke activiteiten.

* [NSG-stroom logboeken inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: als u virtuele-machine schaal sets (VMSS) gebruikt om webtoepassingen te hosten, gebruikt u een netwerk beveiligings groep (NSG) op het VMSS-subnet om te beperken welk netwerk verkeer, poorten en protocollen mogen communiceren. Volg een minimale geprivilegieerde netwerk benadering bij het configureren van uw Nsg's zodat alleen het vereiste verkeer naar uw toepassing wordt toegestaan.

U kunt ook Azure Web Application firewall (WAF) voor essentiële webtoepassingen implementeren voor extra inspectie van inkomend verkeer. Schakel de diagnostische instelling voor WAF-en opname Logboeken in een opslag account, Event hub of Log Analytics werk ruimte in.

* [Netwerken voor virtuele-machineschaalsets in Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [Een toepassings gateway met een Web Application firewall maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-web-application-firewall-portal)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Richt lijnen**: DDoS-standaard beveiliging (Distributed Denial of service) op de virtuele netwerken inschakelen om tegen DDoS-aanvallen te beveiligen. Met Azure Security Center geïntegreerde bedreigings informatie kunt u communicatie met bekende schadelijke IP-adressen bewaken. Configureer Azure Firewall op elk van uw Virtual Network segmenten, met bedreigings informatie ingeschakeld en geconfigureerd voor ' waarschuwen en weigeren ' voor schadelijk netwerk verkeer.

U kunt de just-in-time-netwerk toegang van Azure Security Center gebruiken om de bloot stelling van Windows Virtual Machines aan de goedgekeurde IP-adressen te beperken gedurende een beperkte periode. U kunt ook Azure Security Center adaptieve netwerk beveiliging gebruiken om NSG-configuraties aan te bevelen die poorten en bron-Ip's beperken op basis van daad werkelijk verkeer en bedreigings informatie.

* [DDoS-beveiliging configureren](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Azure Firewall implementeren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

* [Meer informatie over Azure Security Center adaptieve netwerk beveiliging](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

* [Meer informatie over Azure Security Center just-in-time-netwerk Access Control](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Richt lijnen**: u kunt NSG-stroom logboeken vastleggen in een opslag account om stroom records te genereren voor uw Azure-virtual machines. Bij het onderzoeken van afwijkende activiteiten kunt u Network Watcher pakket vastleggen inschakelen, zodat het netwerk verkeer kan worden gecontroleerd op ongebruikelijke en onverwachte activiteiten.

* [NSG-stroom logboeken inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Network Watcher inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: door pakket opnames te combi neren die worden verstrekt door Network Watcher en een open-source-id-hulp programma, kunt u de detectie van het netwerk binnendringen voor een breed scala aan bedreigingen. U kunt ook Azure Firewall op de Virtual Network-segmenten implementeren als dat nodig is, met bedreigings informatie die is ingeschakeld en die is geconfigureerd voor waarschuwing en weigeren voor schadelijk netwerk verkeer.

* [Detectie van binnendringing van het netwerk met Network Watcher en open source-hulpprogram ma's uitvoeren](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools)

* [Azure Firewall implementeren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Waarschuwingen configureren met Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: als u virtuele-machine schaal sets (VMSS) gebruikt om webtoepassingen te hosten, kunt u Azure-toepassing Gateway implementeren voor webtoepassingen waarvoor https/SSL is ingeschakeld voor vertrouwde certificaten. Met Azure-toepassing gateway stuurt u het webverkeer van uw toepassing naar specifieke bronnen door listeners toe te wijzen aan poorten, regels te maken en resources toe te voegen aan een back-end-groep zoals VMSS enzovoort.

* [Application Gateway implementeren](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [Application Gateway configureren voor het gebruik van HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [Een schaalset maken die verwijst naar een toepassingsgateway](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

* [De taak verdeling van laag 7 met Azure Web Application-gateways begrijpen](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall die zijn geconfigureerd voor uw virtuele Azure-machines. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld ApiManagement) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

* [Service Tags begrijpen en gebruiken](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties voor Azure virtual machine Scale sets definiëren en implementeren met behulp van Azure Policy. U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure VM-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, roltoewijzingen en Azure Policy toewijzingen, in één blauw druk te definiëren. U kunt de blauw druk Toep assen op abonnementen en resource beheer inschakelen met behulp van de blauw druk-versie.

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Meer informatie over sjablonen voor virtuele-machine schaal sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-start)

* [Voor beelden Azure Policy voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [Een Azure Blueprint maken](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: u kunt labels gebruiken voor netwerk beveiligings groepen (NSG) en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom die is geconfigureerd voor uw virtuele Windows-machines. Gebruik voor afzonderlijke NSG-regels het veld Beschrijving om de bedrijfs behoeften en/of de duur op te geven voor regels die verkeer naar/van een netwerk toestaan.

* [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Een Virtual Network maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Een NSG maken met een beveiligings configuratie](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: gebruik het Azure-activiteiten logboek voor het bewaken van wijzigingen in de netwerk bron configuraties die zijn gerelateerd aan de schaalset voor virtuele Azure-machines. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer wijzigingen in essentiële netwerk instellingen of bronnen plaatsvinden.

Gebruik Azure Policy om configuraties te valideren (en/of te herstellen) voor netwerk bronnen die betrekking hebben op schaal sets voor virtuele machines.

* [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Waarschuwingen maken in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Voor beelden Azure Policy voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/#network)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Hulp**: micro soft onderhoudt tijd bronnen voor Azure-resources, maar u hebt de mogelijkheid om de tijd synchronisatie-instellingen voor uw virtual machines te beheren.

* [Tijd synchronisatie configureren voor Azure Windows Compute-resources](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

* [Tijd synchronisatie configureren voor Azure Linux-reken resources](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: activiteiten logboeken kunnen worden gebruikt voor het controleren van bewerkingen en acties die worden uitgevoerd op resources van de schaalset van virtuele machines. Het activiteiten logboek bevat alle schrijf bewerkingen (PUT, POST, DELETE) voor uw resources, behalve Lees bewerkingen (GET). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

U kunt logboek gegevens die zijn geproduceerd vanuit Azure-activiteiten Logboeken of virtuele-machine bronnen in-en uitschakelen voor Azure Sentinel of een SIEM van derden voor centraal beveiligings logboek beheer.

Gebruik Azure Security Center om bewaking van beveiligings logboeken voor Azure Virtual Machines te bieden. Gezien het volume van de gegevens dat het beveiligings logboek genereert, wordt het niet standaard opgeslagen.

Als uw organisatie de gegevens van het beveiligings logboek van de virtuele machine wil behouden, kan deze worden opgeslagen in een Log Analytics-werk ruimte op de gewenste gegevensverzamelings tier die in Azure Security Center is geconfigureerd.

* [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Gegevensverzameling in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Virtuele machines in azure controleren](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken kunnen worden gebruikt voor het controleren van bewerkingen en acties die worden uitgevoerd op resources van de schaalset van virtuele machines. Het activiteiten logboek bevat alle schrijf bewerkingen (PUT, POST, DELETE) voor uw resources, behalve Lees bewerkingen (GET). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Schakel de verzameling diagnostische gegevens van het gast besturingssysteem in door de diagnostische uitbrei ding op uw Virtual Machines (VM) te implementeren. U kunt de diagnostische extensie gebruiken om diagnostische gegevens te verzamelen, zoals toepassings Logboeken of prestatie meter items van een virtuele machine van Azure.

Voor geavanceerde zicht baarheid van de toepassingen en services die worden ondersteund door de Schaalset voor virtuele Azure-machines kunt u zowel Azure Monitor voor VM's als Application Insights inschakelen. Met Application Insights kunt u uw toepassing bewaken en telemetrie vastleggen, zoals HTTP-aanvragen, uitzonde ringen enzovoort, zodat u problemen tussen de Vm's en uw toepassing kunt correleren.

* [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [Activiteiten logboek gebeurtenissen van Azure bekijken en ophalen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Virtuele machines in azure controleren](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Overzicht van Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Hulp**: gebruik Azure Security Center om bewaking van beveiligings logboeken voor Azure virtual machines te bieden. Gezien het volume van de gegevens dat het beveiligings logboek genereert, wordt het niet standaard opgeslagen.

Als uw organisatie de gegevens van het beveiligings logboek van de virtuele machine wil behouden, kan deze worden opgeslagen in een Log Analytics-werk ruimte op de gewenste gegevensverzamelings tier die in Azure Security Center is geconfigureerd.

* [Gegevensverzameling in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Virtuele machines in azure controleren](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: Zorg ervoor dat voor opslag accounts of log Analytics-werk ruimten die worden gebruikt voor het opslaan van de logboeken van de virtuele machine, de Bewaar periode voor logboek registratie is ingesteld volgens de nalevings voorschriften van uw organisatie.

* [Virtuele machines in azure controleren](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [De Bewaar periode van Log Analytics Workspace configureren](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Richt lijnen**: Logboeken analyseren en bewaken voor afwijkend gedrag en regel matig resultaten bekijken. Gebruik Azure Monitor om logboeken te controleren en query's uit te voeren op logboek gegevens.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden om uw logboeken te controleren en te controleren.

* [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Log Analytics-werk ruimte begrijpen](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Aangepaste query's uitvoeren in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Azure Security Center geconfigureerd met een log Analytics-werk ruimte voor bewaking en waarschuwingen over afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen voor uw Azure-virtual machines zijn gevonden.

U kunt ook gegevens in-of uitschakelen voor Azure Sentinel of een SIEM van derden om waarschuwingen in te stellen voor afwijkende activiteiten.

* [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Waarschuwingen beheren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Een waarschuwing over logboek gegevens van log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: u kunt micro soft anti-malware voor Azure Cloud Services gebruiken en virtual machines en uw virtuele Windows-machines zo configureren dat gebeurtenissen worden geregistreerd in een Azure Storage-account. Configureer een Log Analytics-werk ruimte om de gebeurtenissen van de opslag accounts op te nemen en maak waar nodig waarschuwingen. Volg de aanbevelingen in Azure Security Center: ' COMPUTE &amp; apps '. Voor virtuele Linux-machines hebt u een hulp programma van derden nodig voor de detectie van beveiligings problemen met malware.

* [Micro soft anti-malware configureren voor Cloud Services en Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Bewaking op gast niveau inschakelen voor Virtual Machines](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

* [Instructies voor het voorbereiden van Linux-servers naar Azure Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-linux-computer)

* [Volgende koppeling bevat de aanbevolen beveiligings richtlijnen van micro soft, die kunnen dienen als een lijst met criteria voor de geselecteerde beveiligings software](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: Implementeer een oplossing van derden van Azure Marketplace voor de DNS-registratie oplossing conform uw organisatie.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Hulp**: de Azure Security Center biedt bewaking van beveiligings logboeken voor Azure virtual machines (VM). Security Center richt micro soft monitoring agent in op alle ondersteunde virtuele machines van Azure en nieuwe die worden gemaakt als automatische inrichting is ingeschakeld, of u kunt de agent hand matig installeren. De agent maakt de gebeurtenis 4688 voor het maken van processen en het veld CommandLine in gebeurtenis 4688 mogelijk. Nieuwe processen die op de virtuele machine worden gemaakt, worden vastgelegd door EventLog en gecontroleerd door de detectie services van Security Center.

Voor virtuele Linux-machines kunt u de console logboek registratie hand matig configureren per knoop punt en syslogs gebruiken om de gegevens op te slaan. U kunt ook de werk ruimte Log Analytics van Azure Monitor gebruiken om logboeken te controleren en query's uit te voeren op syslog-gegevens van virtuele Azure-machines.

* [Gegevensverzameling in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [Aangepaste query's uitvoeren in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Syslog-gegevensbronnen in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteits-en toegangs beheer](/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Richt lijnen**: Hoewel Azure Active Directory de aanbevolen methode is om gebruikers toegang te beheren, kunnen Azure virtual machines lokale accounts hebben. Zowel lokale als domein accounts moeten worden gecontroleerd en beheerd, normaal gesp roken met een minimale footprint. Gebruik Azure Privileged Identity Management bovendien voor beheerders accounts die worden gebruikt voor toegang tot de resources van de virtuele machine.

* [Informatie voor lokale accounts is beschikbaar op](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

* [Informatie over privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: de virtuele-machine schaalset en Azure Active Directory van Azure bevatten niet het concept standaard wachtwoord. Klant die verantwoordelijk is voor toepassingen van derden en Marketplace-services die standaard wachtwoorden gebruiken.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts die toegang hebben tot uw virtuele machines. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken. Beheerders accounts die worden gebruikt voor toegang tot resources van Azure virtual machine kunnen ook worden beheerd door Azure Privileged Identity Management (PIM). Azure Privileged Identity Management biedt verschillende opties, zoals just-in-time-uitbrei ding, waarbij Multi-Factor Authentication vereist voordat een rol en overdrachts opties worden aangenomen, zodat de machtigingen alleen beschikbaar zijn voor specifieke tijds frames en een goed keurder vereist is.

* [Inzicht in Azure Security Center identiteit en toegang](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Informatie over privileged Identity Manager](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: gebruik Azure Active Directory eenmalige aanmelding (SSO)

**Richt lijnen**: gebruik waar mogelijk SSO met Azure Active Directory in plaats van afzonderlijke zelfstandige referenties per service te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

* [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

* [Identiteit en toegang bewaken in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Schakel Azure AD MFA in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

* [MFA inschakelen in azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Identiteit en toegang bewaken in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: beveiligde, door Azure beheerde werk stations gebruiken voor beheer taken

**Richt lijnen**: gebruik paw's (privileged Access workstations) met MFA dat is geconfigureerd om Azure-resources aan te melden en te configureren.

* [Meer informatie over privileged Access workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [MFA inschakelen in azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: Logboeken en waarschuwingen voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure AD PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd. Gebruik Azure AD-risico detecties om waarschuwingen en rapporten weer te geven over Risk ante gebruikers gedrag. De klant kan eventueel Azure Security Center waarschuwingen over risico detectie in Azure Monitor opnemen en aangepaste waarschuwingen/meldingen configureren met actie groepen.

* [Privileged Identity Management implementeren (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Meer informatie over Azure Security Center risico detecties (verdachte activiteiten)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [Azure-activiteiten logboeken integreren in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Actie groepen configureren voor aangepaste waarschuwingen en meldingen](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik Azure Active Directory beleid voor voorwaardelijke toegang en benoemde locaties om alleen toegang toe te staan vanaf specifieke logische groepen met IP-adresbereiken of landen/regio's.

* [Benoemde locaties configureren in azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (Azure AD) als centrale verificatie-en autorisatie systeem. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties. U kunt beheerde identiteiten gebruiken om te verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code. Uw code die wordt uitgevoerd op een virtuele machine, kan de beheerde identiteit gebruiken om toegangs tokens aan te vragen voor services die ondersteuning bieden voor Azure AD-verificatie.

* [Een Azure AD-exemplaar maken en configureren](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

* [Overzicht van beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Richt lijnen**: Azure AD biedt logboeken waarmee u verlopen accounts kunt detecteren. Daarnaast kunt u met behulp van Azure Active Directory-Beoordelingen voor identiteits toegang de groepslid maatschappen, de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze beheren. De toegang van de gebruiker kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. Wanneer u virtuele machines van Azure gebruikt, moet u de lokale beveiligings groepen en gebruikers controleren om ervoor te zorgen dat er geen onverwachte accounts zijn die inbreuk kunnen maken op het systeem.

* [Beoordelingen over Azure Identity Access gebruiken](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde referenties

**Richt lijnen**: Diagnostische instellingen configureren voor Azure Active Directory om de audit logboeken en aanmeldings logboeken te verzenden naar een log Analytics-werk ruimte. Gebruik Azure Monitor ook om logboeken te controleren en query's uit te voeren op logboek gegevens van virtuele Azure-machines.

* [Log Analytics-werk ruimte begrijpen](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [Azure-activiteiten logboeken integreren in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Aangepaste query's uitvoeren in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Virtuele machines in azure controleren](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van het account

**Hulp**: gebruik de functies voor risico-en identiteits beveiliging van Azure Active Directory om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op de resources van uw opslag account. Schakel automatische antwoorden via Azure Sentinel in om de beveiligings reacties van uw organisatie te implementeren.

* [Risk ante aanmeldingen voor Azure AD weer geven](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Risico beleid voor identiteits beveiliging configureren en inschakelen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Richt lijnen**: in ondersteunings Scenario's waarbij micro soft toegang nodig heeft tot klant gegevens (zoals tijdens een ondersteunings aanvraag), gebruikt u klanten-lockbox voor virtuele machines van Azure om aanvragen voor toegang tot klant gegevens te controleren en goed te keuren of af te wijzen.

* [Wat is Klanten-lockbox?](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [beveiligings beheer: gegevens beveiliging](/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om virtuele Azure-machines te volgen die gevoelige informatie opslaan of verwerken.

* [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Resources moeten worden gescheiden door het virtuele netwerk/subnet, op de juiste wijze worden gelabeld en beveiligd in een netwerk beveiligings groep (NSG) of door een Azure Firewall. Voor Virtual Machines het opslaan of verwerken van gevoelige gegevens, implementeert u beleid en procedure (s) om ze uit te scha kelen wanneer ze niet worden gebruikt.

* [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Beheergroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Een Virtual Network maken](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Een NSG maken met een beveiligings configuratie](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Azure Firewall implementeren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Waarschuwing of waarschuwing configureren en weigeren met Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Hulp**bij het implementeren van een oplossing van derden op netwerk verbindingen die controle bieden op niet-geautoriseerde overdracht van gevoelige informatie en die overdrachten blokkeert tijdens het melden van informatie over de beveiliging van uw mede werkers.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig voor de bescherming tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

* [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: gegevens in transit naar, van en tussen virtual machines (VM) met Windows worden op een aantal manieren versleuteld, afhankelijk van de aard van de verbinding, bijvoorbeeld wanneer verbinding wordt gemaakt met een virtuele machine in een RDP-of SSH-sessie.

Micro soft maakt gebruik van het Transport Layer Security (TLS)-protocol voor het beveiligen van gegevens wanneer het onderweg is tussen de Cloud Services en klanten.

* [In-transit versleuteling in Vm's](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Richt lijnen**: gebruik een actief detectie hulpprogramma van derden om alle gevoelige informatie te identificeren die is opgeslagen, verwerkt of verzonden door de technologie systemen van de organisatie, met inbegrip van degenen die zich op locatie of op een externe service provider bevinden en werk de gevoelige informatie-inventaris van de organisatie bij.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Richt lijnen**: met behulp van Azure RBAC (op rollen gebaseerd toegangs beheer) kunt u de taken binnen uw team scheiden en alleen de hoeveelheid toegang verlenen aan gebruikers op uw virtuele machine (VM) die ze nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen voor de virtuele machine te geven, kunt u alleen bepaalde acties toestaan. U kunt toegangs beheer voor de virtuele machine configureren in het Azure Portal, met behulp van de Azure CLI of Azure PowerShell.

* [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

* [Ingebouwde Azure-rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Hulp**: Implementeer een hulp programma van derden, zoals een geautomatiseerde oplossing voor het voor komen van gegevens verlies op basis van een host, om toegangs controles af te dwingen om het risico van gegevens schendingen te beperken.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Hulp**: virtuele schijven op virtual machines (VM) worden versleuteld met behulp van versleuteling aan server zijde of Azure Disk Encryption (ADE). Azure Disk Encryption maakt gebruik van de DM-cryptografie functie van Linux om beheerde schijven te versleutelen met door de klant beheerde sleutels in de gast-VM. Versleuteling aan de server zijde met door de klant beheerde sleutels wordt verbeterd op ADE door u in staat te stellen alle typen besturings systemen en installatie kopieën voor uw virtuele machines te gebruiken door gegevens in de opslag service te versleutelen.

* [Azure Disk Encryption voor Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken wanneer wijzigingen worden aangebracht in schaal sets en gerelateerde resources van virtuele machines.

* [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Logboekregistratie van Azure Opslaganalyse](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [beveiligings beheer: beveiligingslek beheer](/azure/security/benchmarks/security-control-vulnerability-management)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Volg aanbevelingen van Azure Security Center over het uitvoeren van evaluatie van beveiligings problemen op uw Azure virtual machines. Gebruik Azure-beveiliging aanbevolen of een oplossing van derden voor het uitvoeren van evaluatie van beveiligings problemen voor uw virtuele machines.

* [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Hulp**: Schakel automatische upgrades van besturings systemen in voor ondersteunde besturingssysteem versies of voor aangepaste installatie kopieën die zijn opgeslagen in een galerie met gedeelde afbeeldingen.

* [Automatische upgrades van besturings systemen voor virtuele-machine schaal sets in azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: Implementeer een oplossing voor geautomatiseerd patch beheer voor software titels van derden

**Richt lijnen**: Azure virtual machine Scale sets (VMSS) kan gebruikmaken van automatische upgrades van installatie kopieën van besturings systemen. U kunt de Azure desired state Configuration (DSC)-extensie gebruiken voor de onderliggende virtuele machines in de VMSS. DSC wordt gebruikt voor het configureren van de virtuele machines zodra deze online zijn, zodat ze de gewenste software uitvoeren.

* [Virtual Machine Scale Sets gebruiken met de Azure DSC-uitbrei ding](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-dsc)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: scan resultaten met consistente intervallen exporteren en de resultaten vergelijken om te controleren of beveiligings problemen zijn opgelost. Bij het gebruik van aanbevelingen voor beveiligings beheer die worden voorgesteld door Azure Security Center, kan de klant in de portal van de geselecteerde oplossing draaien om historische scan gegevens weer te geven.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Richt lijnen**: gebruik de standaard risico classificaties (beveiligde Score) van Azure Security Center.

* [Azure Security Center beveiligde Score begrijpen](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en asset-management

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Richt lijnen**: gebruik Azure resource Graph om alle resources (inclusief virtuele machines) in uw abonnementen te doorzoeken en te detecteren. Zorg ervoor dat u de juiste machtigingen (lezen) hebt in uw Tenant en dat u alle Azure-abonnementen kunt inventariseren, evenals de resources in uw abonnementen.

* [Query's maken met Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Uw Azure-abonnementen weer geven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Meer informatie over Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch te organiseren op basis van een taxonomie.

* [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om virtual machines schaal sets en gerelateerde resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

* [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Beheergroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: de inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Richt lijnen**: een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor reken resources maken conform onze organisatorische behoeften.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp: Azure**Policy gebruiken om beperkingen te geven aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

Daarnaast gebruikt u de resource grafiek van Azure voor het opvragen/detecteren van resources binnen een of meer abonnementen. Dit kan helpen bij omgevingen met hoge beveiliging, zoals die met opslag accounts.

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Query's maken met Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van werk belastingen en resources. Maak gebruik van Azure virtual machine Inventory om het verzamelen van informatie over alle software op Virtual Machines te automatiseren. Opmerking: de software naam, versie, uitgever en vernieuwings tijd zijn beschikbaar via de Azure Portal. Om toegang te krijgen tot de installatie datum en andere informatie, is de klant verplicht om diagnostische gegevens op gast niveau in te scha kelen en de Windows-gebeurtenis logboeken naar een Log Analytics-werk ruimte te brengen.

Momenteel zijn besturings elementen voor adaptieve toepassingen niet beschikbaar voor Virtual Machine Scale Sets.

* [Een inleiding tot Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Azure VM-inventaris inschakelen](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: Azure Automation biedt volledige controle tijdens de implementatie, bewerkingen en het buiten gebruik stellen van werk belastingen en resources. U kunt Wijzigingen bijhouden gebruiken om alle software te identificeren die op Virtual Machines is geïnstalleerd. U kunt uw eigen proces implementeren of de configuratie van Azure Automation status gebruiken voor het verwijderen van onbevoegde software.

* [Een inleiding tot Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

* [Wijzigingen in uw omgeving bijhouden met de Wijzigingen bijhouden oplossing](https://docs.microsoft.com/azure/automation/change-tracking)

* [Overzicht van Azure Automation status configuratie](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Hulp**: momenteel zijn besturings elementen voor adaptieve toepassingen niet beschikbaar voor Virtual Machine Scale sets. Gebruik software van derden om het gebruik te beheren voor alleen goedgekeurde toepassingen.

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: Azure**Policy gebruiken om beperkingen te geven aan het type resources dat kan worden gemaakt in klant abonnement (en) met de volgende ingebouwde beleids definities:
- Niet toegestane resourcetypen
- Toegestane brontypen

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Hulp**: momenteel zijn besturings elementen voor adaptieve toepassingen niet beschikbaar voor Virtual Machine Scale sets. Implementeer een oplossing van derden als deze niet voldoet aan de vereisten van uw organisatie.

* [Azure Security Center adaptieve toepassings besturings elementen gebruiken](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen**: gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

* [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: afhankelijk van het type scripts kunt u specifieke configuraties van het besturings systeem of bronnen van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure Compute-resources te beperken.

* [De uitvoering van Power shell-scripts beheren in Windows-omgevingen](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: toepassingen met een hoog risico die zijn geïmplementeerd in uw Azure-omgeving, kunnen worden geïsoleerd met virtuele netwerken, subnetten, abonnementen, beheer groepen, enzovoort en voldoende beveiligd met een Azure firewall, Web Application firewall (WAF) of netwerk beveiligings groep (NSG).

* [Virtuele netwerken en virtuele machines in azure](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Overzicht van Azure Firewall](https://docs.microsoft.com/azure/firewall/overview)

* [Overzicht van Web Application firewall](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [Overzicht van netwerkbeveiliging](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Overzicht van Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [Uw resources organiseren met Azure-beheergroepen](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [Handleiding voor beslissingen over abonnementen](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Hulp**: gebruik Azure Policy of Azure Security Center om beveiligings configuraties voor alle Azure-resources te onderhouden. Azure Resource Manager heeft ook de mogelijkheid om de sjabloon in JavaScript Object Notation (JSON) te exporteren, die moet worden gecontroleerd om ervoor te zorgen dat de configuraties voldoen aan de beveiligings vereisten voor uw bedrijf of deze overschrijden.

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Informatie over het downloaden van de VM-sjabloon](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: gebruik Azure Security Center aanbeveling [beveiligings configuraties op uw virtual machines herstellen] voor het onderhouden van beveiligings configuraties op alle reken resources.

* [Azure Security Center aanbevelingen bewaken](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [Azure Security Center aanbevelingen herstellen](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Hulp**: gebruik Azure Resource Manager sjablonen en Azure-beleid om Azure-resources die zijn gekoppeld aan de virtual machines schaal sets, veilig te configureren. Azure Resource Manager sjablonen zijn JSON-bestanden die worden gebruikt voor het implementeren van de virtuele machine, samen met Azure-resources en aangepaste sjablonen moeten worden behouden. Micro soft voert het onderhoud uit op basis van deze sjablonen. Gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

* [Informatie over het maken van Azure Resource Manager sjablonen](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Wat zijn Azure Policy effecten?](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: er zijn verschillende opties voor het onderhouden van een veilige configuratie voor Azure virtual machines (VM) voor implementatie:

1. Azure Resource Manager sjablonen: Dit zijn JSON-bestanden die worden gebruikt voor het implementeren van een virtuele machine vanuit de Azure Portal, en aangepaste sjablonen moeten worden behouden. Micro soft voert het onderhoud uit op basis van deze sjablonen.

2. Aangepaste virtuele harde schijf (VHD): in sommige gevallen is het vereist dat er aangepaste VHD-bestanden worden gebruikt, zoals bij het verwerken van complexe omgevingen die niet op een andere manier kunnen worden beheerd.

3. Azure Automation status configuratie: zodra het basis besturingssysteem is geïmplementeerd, kan dit worden gebruikt voor een gedetailleerdere controle van de instellingen en afgedwongen via het Automation-Framework.

In de meeste gevallen kunnen de micro soft-sjablonen voor virtuele machines in combi natie met de Azure Automation desired state Configuration worden geholpen bij het verg Roten en onderhouden van de beveiligings vereisten.

* [Informatie over het downloaden van de VM-sjabloon](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [Informatie over het maken van ARM-sjablonen](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [Een aangepaste VM-VHD uploaden naar Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure-beleids regels, Azure Resource Manager sjablonen, gewenste status configuratie scripts, enzovoort.  Als u toegang wilt krijgen tot de resources die u beheert in azure DevOps, zoals uw code, builds en het bijhouden van wijzigingen, moet u machtigingen hebben voor deze specifieke resources. De meeste machtigingen worden verleend via ingebouwde beveiligings groepen, zoals beschreven in machtigingen en toegang. U kunt machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps of Active Directory als dit is geïntegreerd met TFS.

* [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Over machtigingen en groepen in azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: als u aangepaste installatie kopieën gebruikt (bijvoorbeeld virtuele harde schijf), gebruikt u Azure RBAC (op rollen gebaseerd toegangs beheer) om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de installatie kopieën.

* [Meer informatie over Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [Azure RBAC configureren](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Hulp**: maakt gebruik van Azure Policy om systeem configuraties voor uw virtuele machines te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

* [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor configuratie beheer voor besturings systemen implementeren

**Hulp**: de configuratie van de Azure Automation-status is een configuratie beheer service voor de desired state Configuration-knoop punten in elke Cloud of een on-premises Data Center. Hiermee kan de schaal baarheid van duizenden computers snel en eenvoudig worden uitgebreid vanaf een centrale, veilige locatie. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven.

* [Onboarding van machines voor beheer door Azure Automation status configuratie](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-resources implementeren

**Hulp**: Maak gebruik van Azure Security Center voor het uitvoeren van basislijn scans voor uw virtuele machines in Azure. Aanvullende methoden voor automatische configuratie zijn onder andere het gebruik van Azure Automation status configuratie.

* [Aanbevelingen herstellen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

* [Aan de slag met de configuratie van de Azure Automation-status](https://docs.microsoft.com/azure/automation/automation-dsc-getting-started)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Hulp**: de configuratie van de Azure Automation-status is een configuratie beheer service voor de desired state Configuration-knoop punten in elke Cloud of een on-premises Data Center. Hiermee kan de schaal baarheid van duizenden computers snel en eenvoudig worden uitgebreid vanaf een centrale, veilige locatie. U kunt eenvoudig computers onboarden, de declaratieve configuraties toewijzen en rapporten weer geven met de naleving van elke computer die u hebt opgegeven.

* [Onboarding van machines voor beheer door Azure Automation status configuratie](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Hulp**: gebruik Managed Service Identity in combi natie met Azure Key Vault om het geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen.

* [Integratie met door Azure beheerde identiteiten](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [Een Key Vault maken](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [Key Vault verificatie bieden met een beheerde identiteit](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten voor het leveren van Azure-Services met een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

* [Beheerde identiteiten configureren](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

* [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Hulp**bij het gebruik van micro soft antimalware voor virtuele Azure Windows-machines om uw resources voortdurend te controleren en te beschermen. U hebt een hulp programma van derden nodig voor beveiliging tegen schadelijke software in azure Linux virtual machine.

* [Micro soft antimalware configureren voor Cloud Services en Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Richt lijnen**: niet van toepassing op virtuele machines van Azure omdat het een reken resource is.

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: niet van toepassing

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: wanneer de implementatie voor virtuele Windows-machines wordt geïmplementeerd, worden standaard automatisch de nieuwste hand tekening, platform en engine-updates geïnstalleerd door micro soft antimalware voor Azure. Volg de aanbevelingen in Azure Security Center: "COMPUTE &amp; apps" om ervoor te zorgen dat alle eind punten up-to-date zijn met de nieuwste hand tekeningen. Het Windows-besturings systeem kan verder worden beveiligd met extra beveiliging om het risico van aanvallen op virussen of malware te beperken met behulp van de micro soft Defender Advanced Threat Protection-Service die kan worden geïntegreerd met Azure Security Center.

U hebt een hulp programma van derden nodig voor beveiliging tegen schadelijke software in azure Linux virtual machine.

* [Micro soft antimalware implementeren voor Azure Cloud Services en Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Microsoft Defender Advanced Threat Protection](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [Micro soft antimalware configureren voor Cloud Services en Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [beveiligings beheer: gegevens herstel](/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: zorg voor regel matige automatische back-ups

**Hulp**bij het maken van een moment opname van het exemplaar van de Azure virtual machine Scale set of de beheerde schijf die is gekoppeld aan het exemplaar met behulp van Power shell of rest-api's. U kunt Azure Automation ook gebruiken om de back-upscripts met regel matige tussen pozen uit te voeren.

* [Een moment opname maken van een instantie van een schaalset voor virtuele machines en beheerde schijven](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Inleiding tot Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van door de klant beheerde sleutels

**Richt lijnen**: Maak moment opnamen van uw virtuele Azure-machines of de beheerde schijven die zijn gekoppeld aan deze instanties met behulp van Power shell of rest-api's. Maak een back-up van door de klant beheerde sleutels binnen Azure Key Vault.

Schakel Azure Backup en doel-Azure-Virtual Machines (VM) in, evenals de gewenste frequentie en retentie perioden. Dit omvat de volledige back-up van de systeem status. Als u gebruikmaakt van Azure Disk Encryption, wordt de back-up van door de klant beheerde sleutels automatisch door Azure VM backup verwerkt.

* [Back-ups maken op virtuele machines van Azure die versleuteling gebruiken](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Overzicht van Azure VM backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [Een moment opname maken van een instantie van een schaalset voor virtuele machines en beheerde schijven](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat het gegevens herstel van beheerde schijven periodiek kan worden uitgevoerd binnen Azure backup. Test zo nodig inhoud terug naar een geïsoleerd virtueel netwerk of abonnement. Klant om het herstel van back-ups van door de klant beheerde sleutels te testen.

Als u gebruikmaakt van Azure Disk Encryption, kunt u de schaal sets van virtuele machines met de versleutelings sleutels van de schijf herstellen. Wanneer u schijf versleuteling gebruikt, kunt u de Azure VM herstellen met de versleutelings sleutels voor de schijf.

* [Back-ups maken op virtuele machines van Azure die versleuteling gebruiken](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Een schijf herstellen en een herstelde VM maken in Azure](https://docs.microsoft.com/azure/backup/tutorial-restore-disk)

* [Sleutel kluis sleutels herstellen in azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Schijf versleuteling inschakelen voor Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: Verwijder beveiliging voor beheerde schijven met behulp van vergren delingen. Schakel de beveiliging voor voorlopig verwijderen en opschonen in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

* [Resources vergrendelen om onverwachte wijzigingen te voorkomen](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)

* [Overzicht van de beveiliging van zacht verwijderen Azure Key Vault en opschonen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten maken voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle werk rollen definiëren, evenals fasen van incident handling/management van detectie tot een beoordeling van het incident.

* [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [De verwerkings handleiding voor het computer beveiligings incident van het NIST gebruiken om u te helpen bij het maken van uw eigen reactie plan voor incidenten](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) met behulp van tags en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren, met name voor de verwerking van gevoelige gegevens. Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

* [Beveiligingswaarschuwingen in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [Labels gebruiken om uw Azure-resources te organiseren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem te testen op een reguliere uitgebracht om uw Azure-resources te beschermen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

* [Publicatie van het NIST-hand leiding voor test-, trainings-en oefen Programma's voor IT-plannen en-mogelijkheden](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

* [De Azure Security Center Security-contact persoon instellen](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de functie continue export om Risico's voor Azure-resources te identificeren. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

* [Continue export configureren](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Waarschuwingen streamen naar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen voor het beveiligen van uw Azure-resources.

* [Werk stroom automatisering en Logic Apps configureren](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

* [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](/azure/security/benchmarks/security-baselines-overview)

---
title: Azure-beveiligings basislijn voor Azure Functions
description: Azure-beveiligings basislijn voor Azure Functions
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 040eeda3edc8aa1165915a157cb7e1bdd1594740
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796497"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure-beveiligings basislijn voor Azure Functions

De Azure-beveiligings basislijn voor Azure Functions bevat aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

De basis lijn voor deze service wordt opgehaald uit de [Azure Security Bench Mark-versie 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen biedt over hoe u uw cloud oplossingen kunt beveiligen in azure met onze richt lijnen voor best practices.

Zie het [overzicht van Azure Security-basis lijnen](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [beveiligings beheer: netwerk beveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: Beveilig bronnen met behulp van netwerk beveiligings groepen of Azure Firewall op de Virtual Network

**Hulp**: integreer uw Azure functions-apps met een virtueel Azure-netwerk. Functie-apps die worden uitgevoerd in het Premium-abonnement hebben dezelfde hosting mogelijkheden als web-apps in Azure App Service, waaronder de functie VNet-integratie.  Met virtuele Azure-netwerken kunt u veel van uw Azure-resources, zoals Azure Functions, in een niet-Internet routeerbaar netwerk plaatsen.

- [Functies integreren met een Azure-Virtual Network](https://docs.microsoft.com/azure/azure-functions/functions-create-vnet)

- [Meer informatie over Vnet-integratie voor Azure Functions en Azure App Service](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: de configuratie en het verkeer van Vnets, subnetten en Nic's bewaken en vastleggen

**Hulp**: gebruik Azure Security Center en volg aanbevelingen voor netwerk beveiliging om netwerk bronnen en netwerk configuraties te beveiligen die betrekking hebben op uw Azure functions-apps.

Als u netwerk beveiligings groepen (Nsg's) met uw Azure Functions-implementatie gebruikt, schakelt u NSG-stroom Logboeken in en verzendt u logboeken naar een Azure Storage account voor verkeers controles. U kunt ook NSG-stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [NSG-stroom logboeken inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Traffic Analytics inschakelen en gebruiken](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="13-protect-critical-web-applications"></a>1,3: essentiële webtoepassingen beveiligen

**Richt lijnen**: als u uw Azure function-eind punten in productie volledig wilt beveiligen, kunt u overwegen om een van de volgende functie-beveiligings opties op app-niveau te implementeren:
- Schakel App Service verificatie/autorisatie in voor uw functie-app,
- Azure API Management (APIM) gebruiken om aanvragen te verifiëren, of
- Implementeer uw functie-app in een Azure App Service Environment.

Zorg er bovendien voor dat externe fout opsporing is uitgeschakeld voor uw productie Azure Functions. Daarnaast mogen door middel van cross-Origin Resource Sharing (CORS) niet alle domeinen toegang hebben tot uw Azure-functie-app. Stel alleen vereiste domeinen in staat te communiceren met uw Azure-functie-app.

Overweeg om Azure Web Application firewall (WAF) te implementeren als onderdeel van de netwerk configuratie voor extra inspectie van binnenkomend verkeer. Schakel de diagnostische instelling voor WAF-en opname Logboeken in een opslag account, Event hub of Log Analytics werk ruimte in. 

- [Azure function-eind punten in productie beveiligen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Azure WAF implementeren](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: communicatie met bekende schadelijke IP-adressen weigeren

**Hulp**: Schakel DDoS Protection standaard in op de virtuele netwerken die zijn gekoppeld aan uw functions-apps om te beschermen tegen DDoS-aanvallen. Gebruik Azure Security Center geïntegreerde bedreigings informatie om communicatie met bekende of ongebruikte open bare IP-adressen te weigeren.
Daarnaast kunt u een front-end-gateway, zoals Azure Web Application firewall, configureren om alle inkomende aanvragen te verifiëren en schadelijk verkeer te filteren. Met de firewall van Azure Web Application kunt u uw Azure-functie-apps beveiligen door inkomend webverkeer te inspecteren om SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blok keren. De introductie van een WAF vereist een App Service Environment of het gebruik van privé-eind punten (preview-versie). Zorg ervoor dat privé-eind punten niet meer in (preview) zijn voordat u ze gebruikt met productie werkbelastingen.

- [Netwerkopties van Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions Premium-abonnement](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Inleiding tot Azure App Service Environments](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Netwerkoverwegingen voor een App Service-omgeving](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [DDoS-beveiliging configureren](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Azure Firewall implementeren](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Meer informatie over Azure Security Center geïntegreerde bedreigings informatie](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Meer informatie over Azure Security Center adaptieve netwerk beveiliging](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Meer informatie over Azure Security Center just-in-time-netwerk Access Control](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Privé-eind punten gebruiken voor Azure Functions](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: netwerk pakketten en stroom logboeken vastleggen

**Richt lijnen**: als u netwerk beveiligings groepen (nsg's) met uw Azure functions-implementatie gebruikt, schakelt u stroom logboeken van de netwerk beveiligings groep in en verzendt u logboeken naar een opslag account voor verkeers controle. U kunt ook stroom logboeken naar een Log Analytics-werk ruimte verzenden en Traffic Analytics gebruiken om inzicht te krijgen in de verkeers stroom in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [NSG-stroom logboeken inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Traffic Analytics inschakelen en gebruiken](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Network Watcher inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: op netwerk gebaseerde inbreuk detectie/indringings systemen (ID'S/IP-adressen) implementeren

**Richt lijnen**: Configureer een front-end-gateway, zoals Azure Web Application firewall, om alle inkomende aanvragen te verifiëren en schadelijk verkeer te filteren. Met de firewall van Azure Web Application kunt u uw functie-apps beveiligen door inkomend webverkeer te controleren om SQL-injecties, cross-site scripting, uploads van malware en DDoS-aanvallen te blok keren. De introductie van een WAF vereist een App Service Environment of het gebruik van privé-eind punten (preview-versie). Zorg ervoor dat privé-eind punten niet meer in (preview) zijn voordat u ze gebruikt met productie werkbelastingen.

Het is ook mogelijk dat er meerdere Marketplace-opties zijn, zoals de Barracuda WAF voor Azure die beschikbaar zijn op de Azure Marketplace, die ID'S/IPS-functies bevatten.

- [Netwerkopties van Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions Premium-abonnement](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Inleiding tot Azure App Service Environments](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Netwerkoverwegingen voor een App Service-omgeving](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Meer informatie over Azure Web Application firewall](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [Privé-eind punten gebruiken voor Azure Functions](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

- [Meer informatie over Barracuda WAF Cloud service](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="17-manage-traffic-to-web-applications"></a>1,7: verkeer naar webtoepassingen beheren

**Richt lijnen**: Configureer een front-end-gateway voor uw netwerk, zoals de firewall van Azure-webtoepassingen met end-to-end TLS-code ring. De introductie van een WAF vereist een App Service Environment of het gebruik van privé-eind punten (preview-versie). Zorg ervoor dat privé-eind punten niet meer in (preview) zijn voordat u ze gebruikt met productie werkbelastingen.

- [Netwerkopties van Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions Premium-abonnement](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Inleiding tot Azure App Service Environments](https://docs.microsoft.com/azure/app-service/environment/intro)

- [Netwerkoverwegingen voor een App Service-omgeving](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Meer informatie over Azure Web Application firewall](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [End-to-end TLS configureren met behulp van Application Gateway met de portal](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

- [Privé-eind punten gebruiken voor Azure Functions](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: de complexiteit en administratieve overhead van netwerk beveiligings regels minimaliseren

**Hulp**: gebruik Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen of Azure firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld AzureAppService) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Voor meer informatie over het gebruik van service Tags](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Hulp**: Definieer en implementeer standaard beveiligings configuraties voor netwerk instellingen met betrekking tot uw Azure functions. Gebruik Azure Policy aliassen in de naam ruimten ' micro soft. Web ' en ' micro soft. Network ' om aangepaste beleids regels te maken om de netwerk configuratie van uw Azure Functions te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities voor Azure Functions, zoals:
- CORS mag niet elke resource toestaan om toegang te krijgen tot uw functie-apps
- functie-app mag alleen toegankelijk zijn via HTTPS
- De meest recente versie van TLS moet worden gebruikt in uw functie-app

U kunt ook Azure-blauw drukken gebruiken om grootschalige Azure-implementaties te vereenvoudigen door sleutel omgevings artefacten, zoals Azure Resource Manager sjablonen, op rollen gebaseerd toegangs beheer (RBAC) en beleids regels in één blauw druk-definitie. U kunt de blauw druk eenvoudig Toep assen op nieuwe abonnementen, omgevingen en het beheer en de verwerkings mogelijkheden van versies.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Een Azure Blueprint maken](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="110-document-traffic-configuration-rules"></a>1,10: configuratie regels voor het document verkeer

**Richt lijnen**: als u netwerk beveiligings groepen (nsg's) met uw Azure functions-implementatie gebruikt, gebruikt u labels voor de nsg's en andere bronnen die betrekking hebben op netwerk beveiliging en verkeers stroom. Voor afzonderlijke NSG-regels gebruikt u het veld Beschrijving voor het opgeven van de bedrijfs behoefte en/of-duur (etc.) voor alle regels die verkeer van of naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure-beleids definities die betrekking hebben op labelen, zoals ' vereist label en de waarde ', om ervoor te zorgen dat alle resources met tags worden gemaakt en u op de hoogte moet zijn van bestaande niet-gelabelde resources.

U kunt Azure PowerShell of Azure CLI gebruiken om op basis van hun labels acties op resources te zoeken of uit te voeren.

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om netwerk resource configuraties te bewaken en wijzigingen te detecteren voor netwerk instellingen en-bronnen die betrekking hebben op uw Azure functions-implementaties. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen in essentiële netwerk instellingen of-resources plaatsvinden. 

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Waarschuwingen maken in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie voor meer informatie [beveiligings beheer: logboek registratie en controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: goedgekeurde tijd synchronisatie bronnen gebruiken

**Richt lijnen**: micro soft onderhoudt de tijd bron die wordt gebruikt voor Azure-resources, zoals Azure functions voor tijds tempels in de logboeken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Richt lijnen**: voor het bijhouden van de controle op vliegtuig controles, schakelt u Diagnostische instellingen voor Azure-activiteiten logboek in en verzendt u de logboeken naar een log Analytics-werk ruimte, Azure Event hub of Azure Storage-account voor archivering. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het controle vlak voor uw Azure-resources.

Azure Functions biedt ook ingebouwde integratie met Azure-toepassing inzichten om functies te bewaken. Application Insights verzamelt logboek-, prestatie-en fout gegevens. Het detecteert automatisch prestatie afwijkingen en bevat krachtige analyse hulpprogramma's waarmee u problemen kunt vaststellen en inzicht krijgt in de manier waarop uw functies worden gebruikt.

Als u in uw Azure function-app ingebouwde beveiligings-en controle logboek registratie hebt, schakelt u de diagnostische instelling ' FunctionAppLogs ' in en verzendt u de logboeken naar een Log Analytics-werk ruimte, Azure Event Hub of Azure Storage-account voor archivering. 

Optioneel kunt u gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden. 

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure Functions met Azure-toepassing inzichten instellen](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Diagnostische instellingen inschakelen (door de gebruiker gegenereerde logboeken) voor Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Richt lijnen**: voor het bijhouden van de controle op vliegtuig controles, schakelt u Diagnostische instellingen voor Azure-activiteiten logboek in en verzendt u de logboeken naar een log Analytics-werk ruimte, Azure Event hub of Azure Storage-account voor archivering. Met Azure-activiteiten logboek gegevens kunt u de ' What, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op het niveau van het controle vlak voor uw Azure-resources.

Als u in uw Azure function-app ingebouwde beveiligings-en controle logboek registratie hebt, schakelt u de diagnostische instelling ' FunctionAppLogs ' in en verzendt u de logboeken naar een Log Analytics-werk ruimte, Azure Event Hub of Azure Storage-account voor archivering. 

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Diagnostische instellingen inschakelen (door de gebruiker gegenereerde logboeken) voor Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: beveiligings logboeken verzamelen van besturings systemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="25-configure-security-log-storage-retention"></a>2,5: Bewaar beveiliging van het beveiligings logboek configureren

**Richt lijnen**: stel in azure monitor de Bewaar periode voor logboek registratie In voor log Analytics werk ruimten die zijn gekoppeld aan uw Azure functions-apps volgens de nalevings voorschriften van uw organisatie.

- [Para meters voor het bewaren van Logboeken instellen](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="26-monitor-and-review-logs"></a>2,6: Logboeken bewaken en controleren

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in, evenals de diagnostische instellingen voor uw Azure functions-app en verzend de logboeken naar een log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens.

Schakel Application Insights in voor uw Azure Functions-apps om logboek-, prestatie-en fout gegevens te verzamelen. U kunt de telemetriegegevens weer geven die zijn verzameld door Application Insights binnen het Azure Portal.

Als u in uw Azure function-app ingebouwde beveiligings-en controle logboek registratie hebt, schakelt u de diagnostische instelling ' FunctionAppLogs ' in en verzendt u de logboeken naar een Log Analytics-werk ruimte, Azure Event Hub of Azure Storage-account voor archivering. 

Optioneel kunt u gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden. 

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Diagnostische instellingen inschakelen voor Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Azure Functions met Azure-toepassing inzichten instellen en de telemetriegegevens weer geven](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteit

**Hulp**: Schakel Diagnostische instellingen voor Azure-activiteiten logboek in, evenals de diagnostische instellingen voor uw Azure functions-app en verzend de logboeken naar een log Analytics-werk ruimte. Query's uitvoeren in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de verzamelde gegevens. U kunt waarschuwingen maken op basis van uw Log Analytics werkruimte query's.

Schakel Application Insights in voor uw Azure Functions-apps om logboek-, prestatie-en fout gegevens te verzamelen. U kunt de telemetriegegevens weer geven die zijn verzameld door Application Insights en waarschuwingen maken binnen de Azure Portal.

Optioneel kunt u gegevens in-en inschakelen voor Azure Sentinel of een SIEM van derden. 

- [Diagnostische instellingen voor Azure-activiteiten logboek inschakelen](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Diagnostische instellingen inschakelen voor Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Application Insights inschakelen voor Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#enable-application-insights-integration)

- [Waarschuwingen maken in azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="28-centralize-anti-malware-logging"></a>2,8: registratie van anti-malware centraliseren

**Richt lijnen**: niet van toepassing; Azure Functions-apps kunnen geen anti-malware-gerelateerde logboeken verwerken of maken.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-query logboek registratie inschakelen

**Richt lijnen**: niet van toepassing; Azure Functions-apps verwerken geen door de gebruiker toegankelijke logboeken die betrekking hebben op DNS.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2,10: controle logboek registratie op opdracht regel inschakelen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [beveiligings beheer: identiteits-en toegangs beheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: een inventaris van beheerders accounts onderhouden

**Hulp**: Azure Active Directory (AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en waarop query's kunnen worden doorzocht. Gebruik de Azure AD Power shell-module om ad hoc-query's uit te voeren om accounts te detecteren die lid zijn van beheer groepen. 

- [Een directory-rol verkrijgen in azure AD met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Leden van een directory-rol in azure AD ophalen met Power shell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="32-change-default-passwords-where-applicable"></a>3,2: standaard wachtwoorden wijzigen indien van toepassing

**Hulp**: de toegang tot Azure functions beheren wordt geregeld via Azure Active Directory (AD). Azure AD heeft niet het concept van standaard wachtwoorden.

Toegang tot het gegevens vlak kan op verschillende manieren worden beheerd, waaronder autorisatie sleutels, netwerk beperkingen en het valideren van een AAD-identiteit. Autorisatie sleutels worden gebruikt door de clients die verbinding maken met uw Azure Functions HTTP-eind punten en kunnen op elk gewenst moment opnieuw worden gegenereerd. Deze sleutels worden standaard voor nieuwe HTTP-eind punten gegenereerd.

Er zijn meerdere implementatie methoden beschikbaar voor het gebruik van apps, waarvan sommige een set van gegenereerde referenties kunnen gebruiken. Controleer de implementatie methoden die voor uw toepassing worden gebruikt.

- [Een HTTP-eind punt beveiligen](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Autorisatie sleutels verkrijgen en opnieuw genereren](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#obtaining-keys)

- [Implementatie technologieën in Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: speciale beheerders accounts gebruiken

**Richt lijnen**: Maak standaard procedures voor het gebruik van specifieke beheerders accounts. Gebruik Azure Security Center identiteits-en toegangs beheer om het aantal beheerders accounts te bewaken.

Om u te helpen bij het bijhouden van specifieke beheerders accounts, kunt u ook aanbevelingen van Azure Security Center of het ingebouwde Azure-beleid gebruiken, zoals: er moet meer dan één eigenaar worden toegewezen aan uw abonnement afgeschafte accounts met eigenaars machtigingen moeten worden verwijderd uit uw abonnement externe accounts met eigenaars machtigingen moeten worden verwijderd

- [Azure Security Center gebruiken om identiteit en toegang te bewaken (preview)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Azure Policy gebruiken](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: gebruik waar mogelijk Azure Active Directory-SSO in plaats van afzonderlijke zelfstandige referenties voor gegevens toegang tot uw functie-app te configureren. Gebruik Azure Security Center aanbevelingen voor identiteits-en toegangs beheer. Implementeer eenmalige aanmelding voor uw Azure Functions-apps met behulp van de functie voor App Service verificatie/autorisatie.

- [Meer informatie over verificatie en autorisatie in Azure Functions](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

- [Informatie over eenmalige aanmelding met Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Hulp**: Schakel Azure Active Directory (AD) multi-factor Authentication (MFA) in en volg Azure Security Center aanbevelingen voor identiteits-en toegangs beheer.

- [MFA inschakelen in azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Identiteit en toegang bewaken in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: gebruik speciale machines (privileged Access workstations) voor alle beheer taken

**Hulp**: gebruik paw (privileged Access workstations) met multi-factor Authentication (MFA) die zijn geconfigureerd voor aanmelding bij en configureren van Azure-resources.

- [Meer informatie over privileged Access workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [MFA inschakelen in azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: logboek en waarschuwing voor verdachte activiteiten van beheerders accounts

**Hulp**: gebruik Azure Active Directory (AD) PRIVILEGED Identity Management (PIM) voor het genereren van Logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving worden uitgevoerd.

Daarnaast kunt u met Azure AD-risico detectie waarschuwingen en rapporten bekijken over Risk ante gebruikers gedrag.

- [Privileged Identity Management implementeren (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Meer informatie over Azure AD-risico detectie](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: Azure-resources alleen beheren vanaf goedgekeurde locaties

**Hulp**: gebruik benoemde locaties voor voorwaardelijke toegang om alleen toegang toe te staan tot de Azure Portal vanuit specifieke logische groepen met IP-adresbereiken of landen/regio's.

- [Benoemde locaties configureren in azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="39-use-azure-active-directory"></a>3,9: Azure Active Directory gebruiken

**Hulp**: gebruik Azure Active Directory (AD) als centraal verificatie-en autorisatie systeem voor uw Azure functions-apps. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

- [Uw Azure Functions-app configureren voor het gebruik van Azure AD-aanmelding](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Een AAD-exemplaar maken en configureren](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: regel matig gebruikers toegang controleren en afstemmen

**Hulp**: Azure Active Directory (AD) bevat logboeken waarmee u verouderde accounts kunt detecteren. Daarnaast kunt u Azure Identity Access revisies gebruiken om groepslid maatschappen en de toegang tot bedrijfs toepassingen en roltoewijzingen op efficiënte wijze te beheren. Gebruikers toegang kan regel matig worden gecontroleerd om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

- [Meer informatie over Azure AD-rapportage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Beoordelingen over Azure Identity Access gebruiken](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: controle pogingen om toegang te krijgen tot gedeactiveerde accounts

**Hulp**: gebruik Azure Active Directory (AD) als centraal verificatie-en autorisatie systeem voor uw Azure function-apps. Azure AD beveiligt gegevens door gebruik te maken van sterke versleuteling voor gegevens in rust en onderweg. Azure AD bevat ook zouten, hashes en veilige gebruikers referenties.

U hebt toegang tot de Azure AD-aanmeldings activiteit, de controle-en risico gebeurtenis logboek bronnen, waarmee u kunt integreren met Azure Sentinel of een SIEM van derden.

U kunt dit proces stroom lijnen door Diagnostische instellingen voor Azure AD-gebruikers accounts te maken en de audit logboeken en aanmeldings logboeken te verzenden naar een Log Analytics-werk ruimte. U kunt de gewenste logboek waarschuwingen configureren in Log Analytics.

- [Uw Azure Functions-app configureren voor het gebruik van Azure AD-aanmelding](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Azure-activiteiten logboeken integreren in Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure-Sentinel aan de trein](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: waarschuwing voor de afwijking van het aanmeldings gedrag van accounts

**Hulp**: gebruik Azure Active Directory (AD) als centraal verificatie-en autorisatie systeem voor uw Azure functions-apps. Voor de afwijking van het gedrag van de account aanmelding op het besturings vlak (de Azure Portal), gebruikt u de functies Azure Active Directory (AD) identiteits beveiliging en risico detectie om automatische antwoorden te configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten. U kunt ook gegevens opnemen in azure Sentinel voor verder onderzoek.

- [Risk ante aanmeldingen voor Azure AD weer geven](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Risico beleid voor identiteits beveiliging configureren en inschakelen](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: micro soft biedt toegang tot relevante klant gegevens tijdens ondersteunings scenario's

**Hulp**: momenteel niet beschikbaar; Klanten-lockbox wordt momenteel niet ondersteund voor Azure Functions.

- [Lijst met door Klanten-lockbox ondersteunde services](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-protection"></a>Gegevensbescherming

*Zie [beveiligings beheer: gegevens beveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: een inventaris van gevoelige informatie onderhouden

**Hulp**: Tags gebruiken om Azure-resources te helpen bij het bijhouden of verwerken van gevoelige informatie.

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: systemen isoleren die gevoelige informatie opslaan of verwerken

**Richt lijnen**: afzonderlijke abonnementen en/of beheer groepen implementeren voor ontwikkeling, testen en productie. Azure function-apps moeten worden gescheiden door de/subnet van het virtuele netwerk (VNet) en moeten op de juiste wijze worden gelabeld.

U kunt ook privé-eind punten gebruiken om netwerk isolatie uit te voeren. Een persoonlijk Azure-eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service (bijvoorbeeld: Azure Functions HTTPs-eind punt van de app) met een persoonlijke Azure-koppeling. Privé-eind punt gebruikt een privé-IP-adres uit uw VNet, waardoor de service effectief in uw VNet wordt gezet. Persoonlijke eind punten zijn in (preview) voor functie-apps die worden uitgevoerd in het Premium-abonnement. Zorg ervoor dat privé-eind punten niet meer in (preview) zijn voordat u ze gebruikt met productie werkbelastingen.

- [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Beheergroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Netwerkopties van Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions Premium-abonnement](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Persoonlijke eind punt begrijpen](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

- [Privé-eind punten gebruiken voor Azure Functions](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: niet-geautoriseerde overdracht van gevoelige gegevens controleren en blok keren

**Richt lijnen**: Implementeer een geautomatiseerd hulp programma op netwerk verbindingen die worden bewaakt voor niet-geautoriseerde overdracht van gevoelige informatie en blokkeert deze overdrachten tijdens het melden van informatie over de beveiliging van uw mede werkers. 

Micro soft beheert de onderliggende infra structuur voor Azure Functions en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: niet van toepassing

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: alle gevoelige gegevens in de overdracht versleutelen

**Richt lijnen**: Schakel In de Azure portal voor uw Azure function-apps onder ' platform functies: netwerken: SSL ' de instelling ' alleen https ' in en stel de minimale TLS-versie in op 1,2.

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: een actief detectie hulpprogramma gebruiken om gevoelige gegevens te identificeren

**Hulp**: momenteel niet beschikbaar; de functies voor gegevens identificatie, classificatie en verlies preventie zijn momenteel niet beschikbaar voor Azure Functions. Code functie-apps die mogelijk gevoelige informatie verwerken als zodanig en het implementeren van oplossingen van derden, indien nodig voor nalevings doeleinden.

Voor het onderliggende platform dat door micro soft wordt beheerd, behandelt micro soft alle inhoud van de klant als gevoelig en gaat u naar een fantastische lengte om te beschermen tegen verlies en bloot stelling van klant gegevens. Om ervoor te zorgen dat klant gegevens binnen Azure veilig blijven, heeft micro soft een reeks robuuste besturings elementen en mogelijkheden voor gegevens bescherming geïmplementeerd en onderhouden.

- [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: Azure RBAC gebruiken om de toegang tot resources te beheren

**Hulp**: gebruik Azure Active Directory (AD) op rollen gebaseerd toegangs beheer (RBAC) voor het beheren van de toegang tot het Azure function Control-vlak (de Azure Portal). 

- [RBAC configureren in azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: voor komen dat gegevens verlies op basis van host wordt gebruikt voor het afdwingen van toegangs beheer

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

Micro soft beheert de onderliggende infra structuur voor Azure Functions en heeft strikte controles geïmplementeerd om verlies of bloot stelling van klant gegevens te voor komen.

- [Informatie over beveiliging van klanten in azure](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: gevoelige informatie op rest versleutelen

**Richt lijnen**: wanneer u een functie-app maakt, moet u een algemeen Azure Storage-account maken of koppelen dat ondersteuning biedt voor blob-, wachtrij-en tabel opslag. Dit komt omdat functies afhankelijk zijn van Azure Storage voor bewerkingen, zoals het beheren van triggers en de uitvoering van logboek functies. Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels leveren voor versleuteling van BLOB-en bestands gegevens. Deze sleutels moeten aanwezig zijn in Azure Key Vault voor de functie-app om toegang te krijgen tot het opslag account.

- [Informatie over de opslag van Azure Functions](https://docs.microsoft.com/azure/azure-functions/storage-considerations)

- [Meer informatie over Azure Storage-versleuteling voor Data-at-rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: wijzigingen in essentiële Azure-resources vastleggen en waarschuwen

**Hulp**: gebruik Azure monitor met het Azure-activiteiten logboek om waarschuwingen te maken voor wanneer er wijzigingen worden aangebracht in de productie-apps van Azure en op andere kritieke of verwante resources.

- [Waarschuwingen maken voor gebeurtenissen in het Azure-activiteiten logboek](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="vulnerability-management"></a>Beveiligings beheer

*Zie [beveiligings beheer: beveiligingslek beheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)voor meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Richt lijnen**: Stel een DevSecOps-praktijk in om ervoor te zorgen dat uw Azure functions-toepassingen veilig zijn en zo veilig mogelijk blijven, gedurende de loop tijd van hun levens cyclus. DevSecOps maakt gebruik van het beveiligings team van uw organisatie en hun mogelijkheden in uw DevOps-procedures om de verantwoordelijkheid van iedereen op het team te beveiligen.

Volg daarnaast aanbevelingen van Azure Security Center om uw Azure function-apps te beveiligen.

- [Continue beveiligings validatie toevoegen aan uw CI/CD-pijp lijn](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Aanbevelingen voor de evaluatie van Azure Security Center-beveiligings problemen implementeren](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5,2: een geautomatiseerde oplossing voor patch beheer voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: Implementeer een geautomatiseerde oplossing voor software patch beheer van derden

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: micro soft voert beveiligings beheer uit op de onderliggende systemen die ondersteuning bieden voor Azure functions. u kunt echter ook de ernst van de aanbevelingen in azure Security Center gebruiken en de beveiligde Score om het risico binnen uw omgeving te meten. Uw beveiligde Score is gebaseerd op het aantal Security Center aanbevelingen dat u hebt verminderd. Denk na over de ernst van de aanbevelingen om eerst de prioriteit te bepalen.

- [Naslag Gids voor beveiligings aanbevelingen](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

## <a name="inventory-and-asset-management"></a>Inventarisatie en het beheer van middelen

*Zie voor meer informatie [beveiligings beheer: inventarisatie en activa beheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: Azure Asset Discovery gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en).  Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Uw Azure-abonnementen weer geven](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Meer informatie over Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Richt lijnen**: Gebruik labels, beheer groepen en afzonderlijke abonnementen, waar nodig, om Azure-resources te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

Daarnaast kunt u met Azure Policy beperkingen opleggen aan het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities: niet toegestaan resource typen toegestane resource typen

- [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [Beheergroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create)

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: een inventaris van goedgekeurde Azure-resources en software titels onderhouden

**Hulp**: Definieer goedgekeurde Azure-resources en goedgekeurde software voor reken resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en). 

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en).  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd. 

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Query's maken met Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp: gebruik**Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in klant abonnementen met behulp van de volgende ingebouwde beleids definities: niet toegestaan resource typen toegestane resource typen

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="610-implement-approved-application-list"></a>6,10: lijst met goedgekeurde toepassingen implementeren

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager via scripts beperken

**Hulp**bij het configureren van voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om te communiceren met Azure Resource Manager door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Richt lijnen**: voor Azure function-apps met een gevoelige of hoge risico kunt u afzonderlijke abonnementen en/of beheer groepen implementeren om isolatie te bieden.

Implementeer Azure function-apps met een hoog risico in hun eigen Virtual Network (VNet). De beveiliging van de verbinding in Azure Functions wordt bereikt via VNets. Functies die worden uitgevoerd in het Premium-abonnement of App Service Environment (ASE) kunnen worden geïntegreerd met VNets. Kies de beste architectuur voor uw use-case.

- [Netwerkopties van Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions Premium-abonnement](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [Netwerkoverwegingen voor een App Service-omgeving](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Een externe ASE maken](https://docs.microsoft.com/azure/app-service/environment/create-external-ase)

Een interne ASE maken:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Een NSG maken met een beveiligings configuratie](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [beveiligings beheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: veilige configuraties instellen voor alle Azure-resources

**Richt lijnen**: standaard beveiligings configuraties voor uw Azure function-app definiëren en implementeren met Azure Policy. Gebruik Azure Policy aliassen in de naam ruimte ' micro soft. Web ' om aangepaste beleids regels te maken om de configuratie van uw Azure Functions apps te controleren of af te dwingen. U kunt ook gebruik maken van ingebouwde beleids definities zoals:
- De beheerde identiteit moet worden gebruikt in uw functie-app
- Fout opsporing op afstand moet worden uitgeschakeld voor functie-apps
- functie-app mag alleen toegankelijk zijn via HTTPS

- [Beschik bare Azure Policy aliassen weer geven](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: veilige configuraties van besturings systemen instellen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: gebruik Azure Policy [deny] en [implementeren indien niet aanwezig] voor het afdwingen van beveiligde instellingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy effecten begrijpen](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: veilige configuraties van besturings systemen onderhouden

**Richt lijnen**: niet van toepassing; Hoewel het mogelijk is om on-premises functies te implementeren, is deze richt lijn bedoeld voor IaaS Compute-resources. Bij het implementeren van on-premises functies bent u verantwoordelijk voor de veilige configuratie van uw omgeving.

- [Meer informatie over on-premises functies](https://docs.microsoft.com/azure/azure-functions/functions-runtime-install)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: arm-sjablonen en aangepaste Azure-beleids definities veilig opslaan en beheren in broncode beheer.

- [Wat is een infra structuur als code](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

- [Ontwerp beleid als code werk stromen](https://docs.microsoft.com/azure/governance/policy/concepts/policy-as-code)

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: aangepaste installatie kopieën van een besturings systeem veilig opslaan

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: hulpprogram ma's voor het beheer van systeem configuratie implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Web ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Ontwikkel bovendien een proces en pijp lijn voor het beheren van beleids uitzonderingen.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: hulpprogram ma's voor het beheer van systeem configuratie implementeren voor besturings systemen

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: geautomatiseerde configuratie bewaking voor Azure-Services implementeren

**Hulp**: gebruik ingebouwde Azure Policy definities en Azure Policy aliassen in de naam ruimte ' micro soft. Web ' om aangepaste beleids regels te maken om systeem configuraties te Signa lering, te controleren en af te dwingen. Gebruik Azure Policy [audit], [deny] en [implementeren indien niet aanwezig] om automatisch configuraties af te dwingen voor uw Azure-resources.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: geautomatiseerde configuratie bewaking voor besturings systemen implementeren

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor IaaS Compute-resources.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7,11: Azure-geheimen veilig beheren

**Richt lijnen**: gebruik beheerde identiteiten in combi natie met Azure Key Vault om geheim beheer voor uw Cloud toepassingen te vereenvoudigen en te beveiligen. Met beheerde identiteiten kan uw functie-app worden geverifieerd bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Een Key Vault maken](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Beheerde identiteiten gebruiken voor App Service en Azure Functions](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

- [Key Vault verificatie bieden met een beheerde identiteit](https://docs.microsoft.com/azure/key-vault/managed-identity)

- [Gebruik Key Vault verwijzingen voor App Service en Azure Functions](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: identiteiten veilig en automatisch beheren

**Richt lijnen**: gebruik beheerde identiteiten om uw Azure-functie-app te voorzien van een automatisch beheerde identiteit in azure AD. Met beheerde identiteiten kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie, met inbegrip van Key Vault, zonder enige referenties in uw code.

- [Beheerde identiteiten gebruiken voor App Service en Azure Functions](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Richt lijnen**: referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault. 

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="malware-defense"></a>Bescherming tegen malware

*Zie [beveiligings beheer: verdediging tegen malware](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: centraal beheerde anti-malware-software gebruiken

**Richt lijnen**: niet van toepassing; deze richt lijn is bedoeld voor IaaS Compute-resources.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Functions), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: micro soft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: scan bestanden die moeten worden geüpload naar niet-reken resources van Azure

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor niet-reken resources die zijn ontworpen om gegevens op te slaan.


**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: controleren of anti-malware-software en hand tekeningen zijn bijgewerkt

**Richt lijnen**: niet van toepassing; deze aanbeveling is bedoeld voor niet-reken resources die zijn ontworpen om gegevens op te slaan.

Micro soft anti-malware is ingeschakeld op de onderliggende host die ondersteuning biedt voor Azure-Services (bijvoorbeeld Azure Functions), maar wordt niet uitgevoerd op de inhoud van de klant.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: niet van toepassing

## <a name="data-recovery"></a>Gegevens herstellen

*Zie [beveiligings beheer: gegevens herstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

**Hulp**: gebruik de functie voor het maken en terugzetten van back-ups om regel matige back-ups van uw app te plannen. Functie-apps die worden uitgevoerd in het Premium-abonnement hebben dezelfde hosting mogelijkheden als web-apps in Azure App Service, die de functie back-up en herstellen bevatten.

Maak ook gebruik van een broncode beheer oplossing zoals Azure opslag plaatsen en Azure DevOps om uw code veilig op te slaan en te beheren. Azure DevOps Services maakt gebruik van veel van de functies van Azure Storage om te zorgen voor de beschik baarheid van gegevens in het geval van hardwarestoringen, onderbreking van de service of de regionale nood situatie. Daarnaast volgt het Azure DevOps-team procedures voor het beveiligen van gegevens tegen onbedoelde of schadelijke verwijdering.

- [Back-up maken van uw app in Azure](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Inzicht in de beschik baarheid van gegevens in azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

**Hulp**: gebruik de functie voor het maken en terugzetten van back-ups om regel matige back-ups van uw app te plannen. Functie-apps die worden uitgevoerd in het Premium-abonnement hebben dezelfde hosting mogelijkheden als web-apps in Azure App Service, die de functie back-up en herstellen bevatten. Back-ups van door de klant beheerde sleutels binnen Azure Key Vault.

Maak ook gebruik van een broncode beheer oplossing zoals Azure opslag plaatsen en Azure DevOps om uw code veilig op te slaan en te beheren. Azure DevOps Services maakt gebruik van veel van de functies van Azure Storage om te zorgen voor de beschik baarheid van gegevens in het geval van hardwarestoringen, onderbreking van de service of de regionale nood situatie. Daarnaast volgt het Azure DevOps-team procedures voor het beveiligen van gegevens tegen onbedoelde of schadelijke verwijdering.

- [Back-up maken van uw app in Azure](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Inzicht in de beschik baarheid van gegevens in azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Documentatie voor Azure opslag plaatsen](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richt lijnen**: Zorg ervoor dat u de functie voor het maken en terugzetten van back-ups periodiek kunt herstellen. Als u een andere offline locatie gebruikt om een back-up te maken van uw code, zorgt u er dan voor dat u de volledige herstel bewerkingen uitvoert. Herstel van back-ups van door de klant beheerde sleutels testen.

- [Een app in azure herstellen vanuit een back-up](https://docs.microsoft.com/azure/app-service/web-sites-restore)

- [Een app in azure herstellen vanuit een moment opname](https://docs.microsoft.com/azure/app-service/app-service-web-restore-snapshots)

- [Sleutel kluis sleutels herstellen in azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

**Richt lijnen**: back-ups van de functie back-up en herstellen gebruiken een Azure Storage-account in uw abonnement. Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels voor versleuteling van opslag gegevens leveren.

Als u door de klant beheerde sleutels gebruikt, zorgt u ervoor dat de functie voor zacht verwijderen in Key Vault is ingeschakeld om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

- [Versleuteling-at-rest in Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [Zacht verwijderen inschakelen in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

## <a name="incident-response"></a>Reageren op incidenten

*Zie voor meer informatie [beveiligings beheer: reactie op incidenten](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: een antwoord gids voor incidenten maken voor uw organisatie. Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle werk rollen definiëren, evenals fasen van incident handling/management van detectie tot een beoordeling van het incident.

- [Werk stroom automatisering configureren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij.  Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Waarschuwingen streamen naar Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren op beveiligings waarschuwingen en aanbevelingen met Logic apps.

- [Werk stroom automatisering en Logic Apps configureren](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Indringings tests en rode team oefeningen

*Zie voor meer informatie [Security Control: Indringings tests en Red team-oefeningen](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de micro soft-regels om ervoor te zorgen dat de indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="next-steps"></a>Volgende stappen

- Zie de [Azure Security-Bench Mark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

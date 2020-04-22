---
title: Azure-beveiligingsbasislijn voor batch
description: Azure-beveiligingsbasislijn voor batch
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8bda712523faa90f32a12fe7eecce27dccf3a55c
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756634"
---
# <a name="azure-security-baseline-for-batch"></a>Azure-beveiligingsbasislijn voor batch

De Azure Security Baseline voor Batch bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze service is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen**: Azure Batch pool(s) implementeren binnen virtueel netwerk. Als u de groepcomputenodes veilig wilt laten communiceren met andere virtuele machines of met een on-premises netwerk, u de groep inrichten in een subnet van een virtueel Azure-netwerk. Het implementeren van uw pool binnen een virtueel netwerk geeft u ook controle over de netwerkbeveiligingsgroep (NSG) die wordt gebruikt om de netwerkinterfaces (NIC) van de afzonderlijke knooppunten te beveiligen, evenals het subnet. Configureer de NSG om verkeer toe te staan vanaf alleen vertrouwde IP(s)/locaties op internet.

Een Azure Batch Pool maken binnen een virtueel netwerk:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NICS controleren en registreren

**Richtlijnen:** Gebruik Azure Security Center en herstel aanbevelingen voor netwerkbeveiliging met betrekking tot de virtuele netwerk- en netwerkbeveiligingsgroep (NSG) die is gekoppeld aan uw batchgroep. Schakel stroomlogboeken in op de NSG die wordt gebruikt om uw batchgroep te beschermen en stuur logboeken naar een Azure Storage Account voor verkeerscontrole. U ook NSG-stroomlogboeken naar een Azure Log Analytics-werkruimte verzenden en Azure Traffic Analytics gebruiken om inzicht te bieden in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Azure Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en foutieve netwerkconfiguraties te lokaliseren.

NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Inzicht in netwerkbeveiliging van Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webtoepassingen beschermen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie weigeren met bekende kwaadaardige IP-adressen

**Richtlijnen**: Azure DDoS (distributed denial-of-service) Standaardbeveiliging inschakelen op het virtuele netwerk dat uw Azure Batch-groep beschermt voor bescherming tegen DDoS-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.

DDoS-beveiliging configureren:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Begrijp azure security center integrated threat intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen:** Schakel stroomlogboeken in op de netwerkbeveiligingsgroep (NSG) die wordt gebruikt om uw Azure Batch-groep te beschermen en stuur logboeken naar een Azure Storage Account voor verkeerscontrole.

NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen implementeren

**Richtlijnen:** Selecteer indien nodig voor nalevingsdoeleinden een virtueel netwerktoestel in azure marketplace dat de functionaliteit van inbraakdetectiesystemen (IDS) en inbraakpreventiesystemen (IPS) ondersteunt met payload-inspectiemogelijkheden.

Als inbraakdetectie en/of preventie op basis van payload-inspectie geen vereiste is, kan Azure Firewall met bedreigingsinformatie worden gebruikt. Filtering op basis van Azure Firewall-bedreigingsinformatie kan verkeer van en naar bekende kwaadaardige IP-adressen en domeinen waarschuwen en weigeren. De IP-adressen en domeinen zijn afkomstig uit de Microsoft Threat Intelligence-feed.

Azure Firewall implementeren met een openbaar IP-adres in hetzelfde virtuele netwerk als uw Azure Batch Pool-knooppunten. Configureer NAT-regels (Network Address Translation) tussen vertrouwde locaties op internet en de privé-IP-adressen van uw afzonderlijke poolknooppunten. Configureer op de Azure Firewall onder Threat Intelligence 'Waarschuwen en weigeren' om te blokkeren om verkeer van/naar bekende kwaadaardige IP-adressen en domeinen te waarschuwen en te blokkeren. De IP-adressen en domeinen zijn afkomstig uit de Microsoft Threat Intelligence-feed en alleen records met het hoogste vertrouwen zijn opgenomen. 

Een Azure Batch Pool maken binnen een virtueel netwerk:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Azure Firewall implementeren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: Verkeer naar uw webtoepassingen beheren

**Richtlijnen**: Benchmark is niet van toepassing en is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service- of IaaS-exemplaren.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen:** gebruik virtuele netwerkservicetags om netwerktoegangsbesturingselementen te definiëren voor netwerkbeveiligingsgroepen of Azure Firewalls die zijn gekoppeld aan uw Azure Batch-groep(s). U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of bestemmingsveld van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

Servicetags begrijpen en gebruiken:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkresources die zijn gekoppeld aan uw Azure Batch-groep(s) met Azure Policy. Gebruik Azure Policy-aliassen in de naamruimten 'Microsoft.Batch' en 'Microsoft.Network' om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure Batch-groepen te controleren of af te dwingen.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Configuratieregels voor documentverkeer

**Richtlijnen:** Gebruik tags voor netwerkservicegroepen (NSG's) en andere bronnen met betrekking tot netwerkbeveiliging en verkeersstroom die zijn gekoppeld aan uw Azure-batchgroepen. Voor afzonderlijke NSG-regels gebruikt u het veld 'Beschrijving' om de bedrijfsbehoefte en/of -duur (enz.) op te geven voor regels die verkeer van/naar een netwerk toestaan.

Gebruik een van de ingebouwde Azure-beleidsdefinities met betrekking tot tags, zoals 'Tag en de waarde ervan vereisen' om ervoor te zorgen dat alle resources met tags worden gemaakt en om u op de hoogte te stellen van bestaande niet-gelabelde bronnen.

U Azure PowerShell of Azure CLI gebruiken om acties op te zoeken of uit te voeren op basis van hun tags.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een virtueel netwerk maken:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Geautomatiseerde hulpprogramma's gebruiken om netwerkbronconfiguraties te controleren en wijzigingen te detecteren

**Richtlijnen:** Gebruik Azure Activity Log om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren voor netwerkbronnen die verband houden met uw Azure Batch-groepen. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen plaatsvinden in kritieke netwerkbronnen.

Azure Activity Log-gebeurtenissen weergeven en ophalen:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Waarschuwingen maken in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Beheer goedgekeurde tijdsynchronisatiebronnen

**Richtlijnen**: Voor Azure Batch biedt Microsoft standaard tijdsynchronisatie. Als u echter specifieke vereisten voor tijdsynchronisatie hebt, u deze wijzigingen implementeren.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Beheer van centrale beveiligingslogboeken configureren

**Richtlijnen:** Onboard Azure Batch-account naar Azure Monitor om beveiligingsgegevens die door de clusterapparaten worden gegenereerd, te verzamelen. Maak gebruik van aangepaste query's om bedreigingen in de omgeving te detecteren en erop te reageren.  Gebruik voor azure batch-monitoring op resourceniveau de Batch-API's om de status van uw resources te controleren of op te vragen, inclusief taken, taken, knooppunten en groepen.

Een Azure Batch-account aan boord maken van Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure Resources

**Richtlijnen:** Controleer voor Azure Batch-accountniveaubewaking elk Batch-account met behulp van functies van Azure Monitor. Azure Monitor verzamelt statistieken en optioneel diagnostische logboeken voor resources die worden verdeeld op het niveau van een Batch-account, zoals pools, taken en taken. Verzamel en gebruik deze gegevens handmatig of programmatisch om activiteiten in uw Batch-account te controleren en problemen te diagnosticeren.

Gebruik voor Azure Batch-bronniveaubewaking de Azure Batch API's om de status van uw resources te controleren of op te vragen, inclusief taken, taken, knooppunten en groepen.

Azure Batch-controle en -logboekregistratie op accountniveau configureren:

https://docs.microsoft.com/azure/batch/monitoring-overview

Inzicht in batchresourceniveaubewaking:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Beveiligingslogboeken verzamelen van het besturingssysteem

**Richtlijnen:** Azure Monitor verzamelt statistieken en diagnostische logboeken voor resources in uw Azure Batch-account. Verzamel en gebruik deze gegevens op verschillende manieren om uw Azure Batch-account te controleren en problemen te diagnosticeren. U ook metrische waarschuwingen configureren, zodat u meldingen ontvangt wanneer een statistiek een opgegeven waarde bereikt.

Indien nodig maakt u misschien verbinding met uw afzonderlijke poolknooppunten via Secured Shell (SSH) of Remote Desktop Protocol (RDP) om toegang te krijgen tot lokale logboeken van het besturingssysteem.

Diagnostische logboeken verzamelen van uw Azure Batch-account:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Op afstand verbinding maken met uw Azure Batch-poolknooppunten:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen:** Azure Batch-account aan boord voor Azure Monitor. Controleer of de gebruikte Azure Log Analytics-werkruimte de bewaartermijn van het logboek heeft ingesteld op basis van de nalevingsvoorschriften van uw organisatie

Azure Batch-controle en -logboekregistratie configureren:

https://docs.microsoft.com/azure/batch/monitoring-overview

De bewaarperiode van Azure Log Analytics-werkruimteconfigureren configureren:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen:** Azure Batch metrische waarschuwingen maken die leiden tot wanneer de waarde van een opgegeven statistiek een bepaalde drempel overschrijdt.

Metrische waarschuwingen voor Azure Batch configureren:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtlijnen:** Azure Batch metrische waarschuwingen maken die leiden tot wanneer de waarde van een opgegeven statistiek een bepaalde drempel overschrijdt.

Metrische waarschuwingen voor Azure Batch configureren:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtlijnen:** Gebruik Windows Defender op uw afzonderlijke batchknooppunten in het geval van Windows-besturingssystemen, of bied uw eigen anti-malwareoplossing als u Linux gebruikt.

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtlijnen**: Implementeer oplossing van derden voor dns-logging

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

**Richtlijnen**: Handmatig consolelogboekregistratie en PowerShell-transcriptie per knooppunt configureren.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Beveiligingsbeheer: Identiteits- en toegangscontrole](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van administratieve rekeningen

**Richtlijnen:** houd het lokale beheerdersaccount bij dat is gemaakt tijdens de inrichting van de Azure Batch-groep en alle andere accounts die u maakt. Als azure Active Directory (AAD)-integratie wordt gebruikt, heeft AAD bovendien ingebouwde rollen die expliciet moeten worden toegewezen en daarom opvraagbaar zijn. Gebruik de AAD PowerShell-module om adhocquery's uit te voeren om accounts te ontdekken die lid zijn van administratieve groepen.

Daarnaast u aanbevelingen voor Azure Security Center Identity and Access Management gebruiken.

Een maprol in AAD met PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Leden van een directoryrol krijgen in AAD met PowerShell:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Identiteit en toegang controleren met Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

**Richtlijnen:** Bij het inrichten van een Azure Batch-groep krijgt u de optie om lokale machineaccounts te maken. Er zijn geen standaardwachtwoorden om te wijzigen, maar u verschillende wachtwoorden opgeven voor Toegang tot Secured Shell (SSH) en Extern bureaublad-protocol (RDP). Nadat Azure Batch Pool is geconfigureerd, u een willekeurige gebruiker genereren voor afzonderlijke knooppunten binnen de Azure-portal of via Azure Resource Manager API.

Een gebruiker toevoegen aan een specifiek compute node:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: Zorgen voor het gebruik van speciale administratieve rekeningen

**Richtlijnen**: Integratie van verificatie voor Azure Batch-toepassingen met Azure Active Directory. Maak beleid en procedures rond het gebruik van specifieke administratieve accounts.

Daarnaast u aanbevelingen voor Azure Security Center Identity and Access Management gebruiken.

Batch-toepassingen verifiëren met Azure Active Directory:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Identiteit en toegang controleren met Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik single sign-on (SSO) met Azure Active Directory

**Richtlijnen**: Niet van toepassing, Azure Batch ondersteunt Azure AD-verificatie, maar eenmalige aanmelding wordt niet ondersteund.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Gebruik Multifactor-verificatie voor alle Azure Active Directory-gebaseerde toegang.

**Richtlijnen**: Verificatie voor Azure Batch-toepassingen integreren met Azure Active Directory (AAD). Schakel AAD multi-factor authentication (MFA) in en volg aanbevelingen voor Azure Security Center Identity and Access Management.

 

Mfa inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken binnen Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated Machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

**Richtlijnen:** Gebruik PAWs (gedrenft toegangswerkstations) met multi-factor authenticatie (MFA) geconfigureerd om in te loggen en configureren van uw Azure Batch-bronnen.

Meer informatie over geprivilegieerde werkstations voor toegang:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Mfa inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten van administratieve accounts

**Richtlijnen:** Als u geïntegreerde verificatie voor Azure Batch Applications hebt met Azure Active Directory (AAD), gebruikt u Azure Active Directory-beveiligingsrapporten voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden. Gebruik Azure Security Center om identiteits- en toegangsactiviteiten te controleren.

Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Hoe u de identiteit van gebruikers controleren en activiteiten openen in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Azure Resource beheren vanaf alleen goedgekeurde locaties

**Richtlijnen:** Als u geïntegreerde verificatie voor Azure Batch-toepassingen hebt met Azure Active Directory, u locaties met voorwaardelijke toegang gebruiken om toegang toe te staan vanuit alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's.

Benoemde locaties configureren in Azure:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen:** Gebruik Azure Active Directory (AAD) als het centrale verificatie- en autorisatiesysteem en integreer verificatie voor Azure Batch-toepassingen met AAD. AAD beschermt gegevens door sterke versleuteling te gebruiken voor gegevens in rust en onderweg. AAD zouten, hashes en slaat ook veilig gebruikersreferenties op.

Een AAD-exemplaar maken en configureren:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Batchtoepassingen verifiëren met AAD:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Gebruikerstoegang regelmatig bekijken en afstemmen

**Richtlijnen:** Azure Active Directory(AAD) biedt logboeken om verouderde accounts te ontdekken. Daarnaast u Azure Identity Access Reviews gebruiken om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. De toegang van gebruikers kan regelmatig worden beoordeeld om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Azure Identity Access-recensies gebruiken:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen**: Maak diagnostische instellingen voor Azure Active Directory-gebruikersaccounts, verzend de controlelogboeken en aanmeldingslogboeken naar een Azure Log Analytics-werkruimte. De gewenste waarschuwingen configureren binnen de Azure Log Analytics-werkruimte.

Azure-activiteitslogboeken integreren in Azure Monitor:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing bij afwijking van het inloggedrag van uw account

**Richtlijnen:** Gebruik de functie Azure Active Directory (AAD) Risicodetecties en Identiteitsbeveiliging om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. Bovendien u gegevens opnemen in Azure Sentinel voor verder onderzoek.

Aad-riskante aanmeldingen bekijken:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Hoe u risicobeleid voor identiteitsbescherming configureert en inschakelt:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel aan boord:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's<br></div>

**Richtlijnen**: Niet beschikbaar; Customer Lockbox wordt nog niet ondersteund voor Azure Batch.
 
Lijst met door klanten ondersteunde services:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



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

**Richtlijnen**: Implementeer afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Azure Batch Pools moeten worden gescheiden door virtueel netwerk/subnet, op de juiste manier worden getagd en beveiligd met een NETWERK beveiligingsgroepen (NSG). Azure Batch-gegevens moeten worden opgenomen in een beveiligd Azure-opslagaccount.

Een Azure Batch Pool maken binnen een virtueel netwerk:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Azure-opslagaccounts beveiligen:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie.

**Richtlijnen:** voor Azure Storage Accounts die zijn gekoppeld aan uw Azure Batch Pool(s) die gevoelige informatie bevatten, markeert u deze als gevoelig met tags en beveiligze met azure-aanbevolenprocedures.

Gegevensidentificatie, classificatie en verliespreventiezijn nog niet beschikbaar voor Azure Storage- of compute resources. Implementeer oplossingen van derden indien nodig voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Azure-opslagaccounts beveiligen:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen**: Versleutel alle gevoelige informatie tijdens het transport. Microsoft Azure-bronnen onderhandelen standaard over TLS 1.2. Zorg ervoor dat clients die verbinding maken met uw Azure Batch Pools of gegevensopslag (Azure Storage Accounts) kunnen onderhandelen over TLS 1.2 of hoger.

Controleer of HTTPS vereist is voor toegang tot het opslagaccount met uw Azure Batch-gegevens.

Informatie over Azure Storage Account Encryption in Transit:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

**Richtlijnen:** voor Azure Storage Accounts die zijn gekoppeld aan uw Azure Batch Pool(s) die gevoelige informatie bevatten, markeert u deze als gevoelig met behulp van tags en beveiligze met azure-aanbevolenprocedures.

Gegevensidentificatie, classificatie en verliespreventiezijn nog niet beschikbaar voor Azure Storage- of compute resources. Implementeer oplossingen van derden indien nodig voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Azure-opslagaccounts beveiligen:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

**Richtlijnen:** gebruik RBAC (Azure Active Directory(AAD) Role-based access control (RBAC) om de toegang tot het beheervlak van Azure-bronnen te beheren, waaronder Batch-account, Batch Pool(s) en Opslagaccounts.

Azure RBAC begrijpen:

https://docs.microsoft.com/azure/role-based-access-control/overview

RBAC configureren in Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik host-based Data Loss Prevention om toegangscontrole af te dwingen

**Richtlijnen**: Functies voor gegevensidentificatie, classificatie en verliespreventie zijn nog niet beschikbaar voor Azure Storage- of compute resources. Implementeer oplossingen van derden indien nodig voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen:** Voor opslagaccounts die zijn gekoppeld aan uw Azure Batch-account, wordt aanbevolen om Microsoft toe te staan versleutelingssleutels te beheren, maar u hebt de optie om uw eigen sleutels te beheren indien nodig.

Versleutelingssleutels voor Azure Storage-accounts beheren:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen:** Gebruik Azure Monitor met het Azure Activity Log om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in kritieke Azure-resources die zijn gerelateerd aan of zijn gekoppeld aan uw Azure Batch-accounts/-groepen.

Configureer diagnostische instellingen voor opslagaccounts die zijn gekoppeld aan Azure Batch Pool om alle CRUD-bewerkingen te controleren en te registreren tegen poolgegevens.

Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Extra logboekregistratie/controle inschakelen voor een Azure Storage-account:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtlijnen:** Voor Azure Batch Pool-knooppunten bent u verantwoordelijk voor het beheer van de oplossing voor het beheer van kwetsbaarheden.

Als u optioneel een Rapid7-, Qualys- of een ander abonnement op het kwetsbaarheidsbeheerplatform hebt, u handmatig beveiligingsbeoordelingsagents installeren op batchpoolknooppunten en knooppunten beheren via de desbetreffende portal.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Oplossing voor het patchbeheer van geautomatiseerd besturingssysteem implementeren

**Richtlijnen:** Microsoft voor het onderhouden en bijwerken van azure batchpoolknooppuntafbeeldingen. Zorg ervoor dat het besturingssysteem van Azure Batch Pool-knooppunten gepatcht blijft gedurende de duur van de clusterlevensduur, waarvoor mogelijk automatische updates moeten worden inschakelt, de knooppunten moeten worden bewaakt of periodieke reboots moeten worden uitgevoerd.


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Software patchmanagementoplossing van derden implementeren

**Richtlijnen:** Ervoor zorgen dat de toepassingen van Azure Batch Pool-knooppunten gepatcht blijven gedurende de duur van de clusterlevensduur, waarvoor mogelijk automatische updates moeten worden innodig, de knooppunten moeten worden bewaakt of periodieke reboots moeten worden uitgevoerd.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk Back-to-back Vulnerability Scans

**Richtlijnen:** Als u een Rapid7- of Qualys- of een ander abonnement op het kwetsbaarheidsbeheerplatform hebt, u de portal van die leverancier gebruiken om back-to-back-kwetsbaarheidsscans te bekijken en te vergelijken.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden.

**Richtlijnen:** Gebruik een gemeenschappelijk risicoscoreprogramma (bijvoorbeeld Common Vulnerability Scoring System) of de standaardrisicobeoordelingen van uw scantool van derden.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik Azure Resource Graph om alle bronnen (zoals rekenkracht, opslag, netwerk, enz.) binnen uw abonnement(en) op te vragen/te ontdekken. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant beschikt en dat u alle Azure-abonnementen en resources binnen uw abonnementen opsommen.

Hoewel klassieke Azure-bronnen kunnen worden ontdekt via Resource Graph, is het ten zeerste aan te raden om in de toekomst Azure Resource Manager-resources (ARM) te maken en te gebruiken.

Query's maken met Azure Resource Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weergeven:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC begrijpen:

https://docs.microsoft.com/azure/role-based-access-control/overview

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

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Voorraad bijhouden van goedgekeurde Azure-resources en softwaretitels.

**Richtlijnen**: Lijst met goedgekeurde Azure-resources en goedgekeurde software voor rekenbronnen definiëren


**Azure Security Center-bewaking:** niet van toepassing

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

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor voor niet-goedgekeurde softwaretoepassingen binnen Compute Resources

**Richtlijnen:** Implementeer voor Azure Batch Pool-knooppunten een oplossing van derden om clusterknooppunten voor niet-goedgekeurde softwaretoepassingen te controleren.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

**Richtlijnen:** Implementeer voor Azure Batch Pool-knooppunten een oplossing van derden om clusterknooppunten voor niet-goedgekeurde softwaretoepassingen te controleren.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="68-use-only-approved-applications"></a>6.8: Gebruik alleen goedgekeurde aanvragen

**Richtlijnen:** Implementeer voor Azure Batch Pool-knooppunten een oplossing van derden om te voorkomen dat ongeautoriseerde software wordt uitgevoerd.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure Services gebruiken

**Richtlijnen:** Gebruik Azure-beleid om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen
- Toegestane brontypen

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek brontype weigeren met Azure-beleid:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtlijnen:** Implementeer voor Azure Batch Pool-knooppunten een oplossing van derden om te voorkomen dat ongeautoriseerde bestandstypen worden uitgevoerd.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>De mogelijkheid van gebruikers beperken om te communiceren met Azure Resource Manager via scripts</div>

**Richtlijnen:** Gebruik voorwaardelijke toegang voor Azure om de interactie van gebruikers met Azure Resource Manager te beperken door 'Bloktoegang' voor de Microsoft Azure Management-app te configureren.

Voorwaardelijke toegang configureren om toegang tot Azure Resource Manager te blokkeren:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Gebruikers de mogelijkheid beperken om scripts uit te voeren binnen compute resources

**Richtsnoeren**: Niet van toepassing,

Dit is niet van toepassing op Azure Batch, omdat gebruikers (niet-beheerders) van de Azure Batch-groepen geen toegang nodig hebben tot de afzonderlijke knooppunten om taken uit te voeren. De clusterbeheerder heeft al roottoegang tot alle knooppunten.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen**: Benchmark is niet van toepassing en is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service- of IaaS-exemplaren.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Beveiligingsbeheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Beveiligde configuraties instellen voor alle Azure-bronnen

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.Batch' om aangepaste beleidsregels te maken om de configuratie van uw Azure Batch-accounts en -groepen te controleren of af te dwingen.

Beschikbare Azure Policy-aliassen weergeven:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Veilige configuraties instellen voor uw besturingssysteem

**Richtlijnen:** Stel veilige configuraties vast voor het besturingssysteem van uw Batch Pool-knooppunten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Veilige configuraties behouden voor alle Azure-bronnen

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen af te dwingen voor de Azure-resources met betrekking tot uw Batch-account en -groepen (zoals virtuele netwerken, subnetten, Azure Firewalls, Azure Storage Accounts, enz.). U Azure Policy-aliassen uit de volgende naamruimten gebruiken om aangepaste beleidsregels te maken:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Inzicht in Azure-beleidseffecten:https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Veilige configuraties voor besturingssystemen behouden

**Richtlijnen:** Azure Batch Pool Operating System Images beheerd en onderhouden door Microsoft. U bent verantwoordelijk voor de implementatie van de statusconfiguratie op OS-niveau.

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen:** Als u aangepaste Azure-beleidsdefinities gebruikt voor uw Azure Batch-accounts, pools of gerelateerde resources, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

Code opslaan in Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos-documentatie:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtlijnen:** Als u aangepaste afbeeldingen gebruikt voor uw Azure Batch-groepen, gebruikt u RBAC (Role-based access control) om ervoor te zorgen dat alleen geautoriseerde gebruikers toegang hebben tot de afbeeldingen.

RBAC in Azure begrijpen:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

RBAC configureren in Azure:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen:** Gebruik ingebouwde Azure-beleidsdefinities om Azure Batch-gerelateerde resourceconfiguraties te waarschuwen, te controleren en af te dwingen.  Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.Batch' om aangepaste beleidsregels te maken voor uw Azure Batch-accounts en -groepen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Hulpprogramma's voor systeemconfiguratiebeheer implementeren voor besturingssystemen

**Richtlijnen:** Implementeer een oplossing van derden om de gewenste status voor de besturingssystemen van uw Azure Batch Pool-knooppunten te behouden.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure Services

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.Batch' om aangepaste beleidsregels te maken om de configuratie van uw Azure Batch-exemplaar te controleren of af te dwingen. U ook alle ingebouwde beleidsregels gebruiken die specifiek zijn gemaakt voor Azure Batch of de bronnen die worden gebruikt door Azure Batch, zoals:

- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep

-Opslagaccounts moeten een eindpunt voor virtuele netwerkservice gebruiken

- Diagnostische logboeken in Batch-accounts moeten zijn ingeschakeld

Beschikbare Azure Policy-aliassen weergeven:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtlijnen:** Implementeer een oplossing van derden om de status van de besturingssystemen van uw Azure Batch Pool-knooppunten te controleren.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="711-securely-manage-azure-secrets"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Azure Key Vault kan worden gebruikt met Azure Batch-implementaties om sleutels voor poolopslag binnen Azure Storage-accounts te beheren.

Hoe te integreren met Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een Azure Key Vault maken:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Key Vault-verificatie voorzien van een beheerde identiteit:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen**: Niet beschikbaar, Managed Service Identity wordt niet ondersteund door Azure Batch


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling aan referenties elimineren

**Richtlijnen**: Implementeer credential scanner om referenties binnen code te identificeren. Credential Scanner stimuleert ook het verplaatsen van ontdekte referenties naar veiligere locaties zoals Azure Key Vault. 

Credential Scanner instellen:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Gebruik centraal beheerde anti-malware software

**Richtlijnen:** Gebruik Windows Defender op uw afzonderlijke Azure Batch-poolknooppunten in het geval van Windows-besturingssystemen of bied uw eigen anti-malwareoplossing als u Linux gebruikt.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-resources

**Richtlijnen**: Microsoft Antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Batch), maar wordt niet uitgevoerd op inhoud van klanten.

Scan vooraf bestanden die worden geüpload naar niet-compute Azure-bronnen, zoals App Service, Data Lake Storage, Blob Storage, enz. Microsoft heeft in deze gevallen geen toegang tot klantgegevens.

Microsoft Antimalware voor Azure Cloud Services en virtuele machines begrijpen:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtlijnen:** Gebruik Windows Defender op uw afzonderlijke Azure Batch-poolknooppunten in het geval van Windows-besturingssystemen en zorg ervoor dat automatische update is ingeschakeld. Bied uw eigen anti-malware oplossing als u Linux gebruikt.


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorgen voor regelmatige geautomatiseerde back-ups

**Richtlijnen:** Wanneer u een Azure Storage-account gebruikt voor het Azure Batch Pool-gegevensarchief, kiest u de juiste redundantieoptie (LRS, ZRS, GRS, RA-GRS). 

Opslagredundantie configureren voor Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en back-upmaken van beheerde sleutels van de klant

**Richtlijnen:** Wanneer u een Azure Storage-account gebruikt voor het Azure Batch Pool-gegevensarchief, kiest u de juiste redundantieoptie (LRS, ZRS, GRS, RA-GRS).  Als u Azure Key Vault gebruikt voor een deel van uw Azure Batch-implementatie, moet u ervoor zorgen dat er een back-up van uw sleutels wordt gemaakt.

Opslagredundantie configureren voor Azure Storage Accounts:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Een back-up maken van sleutelkluissleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Als u uw eigen sleutels voor Azure Storage Accounts of een andere bron met betrekking tot uw Azure Batch-implementatie beheert, test u periodiek het herstel van back-upsleutels.

Een back-up maken van sleutelkluissleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Een door de klant beheerde sleutel herstellen met PowerShell:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorgen voor bescherming van back-ups en beheerde sleutels van klanten

**Richtlijnen:** Als Azure Key Vault wordt gebruikt om sleutels te bevatten die verband houden met Opslagaccounts van Azure Batch Pool, schakelt u Soft-Delete in Azure Key Vault in om sleutels te beschermen tegen onbedoelde of kwaadwillige verwijdering.

Soft Delete inschakelen in Azure Key Vault:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Beveiligingscontrole: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)voor meer informatie.*

### <a name="101-create-incident-response-guide"></a>10.1: Gids voor incidentrespons maken

**Richtlijnen**: Zorg ervoor dat er schriftelijke incidentresponseplannen zijn die de rollen van het personeel definiëren, evenals fasen van incidentafhandeling/-beheer.

Werkstroomautomatiseringen configureren binnen Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Procedure voor het scoren van incidenten en prioritering maken

**Richtlijnen**: Security Center kent een ernst toe aan waarschuwingen, zodat u prioriteit geven aan de volgorde waarin u elke waarschuwing bijwoont, zodat wanneer een resource wordt gecompromitteerd, u er meteen bij komen. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing.

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen**: Voer oefeningen uit om de incidentresponsmogelijkheden van uw systemen op een regelmatige cadans te testen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.

Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken &nbsp;en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat de gegevens van uw gegevens zijn geopend door een onwettige of onbevoegde partij.

De beveiligingscontactpersoon azure security center instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

**Richtlijnen:** exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue exporteren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Voer regelmatig penetratietests uit van uw Azure-bronnen en zorg ervoor dat alle kritieke beveiligingsbevindingen binnen 60 dagen worden verholpen.

**Richtlijnen:** Volg de Microsoft Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Meer informatie over de strategie en uitvoering van Red Teaming en live site penetratietesten van microsoft op basis van door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen vindt u hier: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

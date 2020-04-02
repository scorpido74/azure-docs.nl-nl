---
title: Azure Security Baseline voor Azure Container Registry
description: Azure Security Baseline voor Azure Container Registry
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9225cfd9793a84f371387d6450a3dfa80ba74de3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547527"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure Security Baseline voor Azure Container Registry

De Azure Security Baseline voor Azure Container Registry bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze service is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen:** Azure Virtual Network biedt veilige, privénetwerken voor uw Azure- en on-premises resources. Door de toegang tot uw privé-Azure-containerregister te beperken vanuit een virtueel Azure-netwerk, zorgt u ervoor dat alleen resources in het virtuele netwerk toegang krijgen tot het register. Voor cross-premises scenario's u ook firewallregels configureren om alleen registertoegang toe te staan vanaf specifieke IP-adressen. Configureer firewalltoegangsregels en servicetags van achter een firewall om toegang te krijgen tot uw containerregister.

Toegang tot een Azure-containerregister beperken met behulp van een Virtueel Azure-netwerk of firewallregels:https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

Regels configureren om toegang te krijgen tot een Azure-containerregister achter een firewall:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

**Richtlijnen:** Gebruik Azure Security Center en herstel aanbevelingen voor netwerkbeveiliging om uw netwerkbronnen in Azure te beschermen. Schakel NSG-stroomlogboeken in en stuur logboeken naar een opslagaccount voor verkeerscontrole.

NSG-stroomlogboeken inschakelen:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Bescherm uw netwerkbronnen:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

**Richtlijnen**: Niet van toepassing. Benchmark is bedoeld voor Azure App Service of compute resources die webtoepassingen hosten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

**Richtlijnen:** Schakel DDoS-standaardbeveiliging in op uw virtuele netwerken voor bescherming tegen DDoS-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.  Implementeer Azure Firewall op elk van de netwerkgrenzen van de organisatie met Threat Intelligence ingeschakeld en geconfigureerd om te 'Waarschuwen en weigeren' voor kwaadaardig netwerkverkeer.

U Azure Security Center Just In Time-netwerktoegang gebruiken om NSG's te configureren om de blootstelling van eindpunten naar goedgekeurde IP-adressen gedurende een beperkte periode te beperken. Gebruik ook Azure Security Center Adaptive Network Hardening om NSG-configuraties aan te bevelen die poorten en bron-IP's beperken op basis van werkelijke verkeers- en bedreigingsinformatie.

DDoS-beveiliging configureren:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Firewall implementeren:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Begrijp azure security center integrated threat intelligence:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Inzicht in Azure Security Center Adaptive Network Hardening:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure Security Center Just In Time Network Access Control:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen:** NSG-stroomlogboeken (Network Security Group) inschakelen voor de NSG die is gekoppeld aan het subnet dat wordt gebruikt om uw Azure-containerregister te beschermen. U de NSG-stroomlogboeken opnemen in een Azure Storage-account om stroomrecords te genereren. Schakel Azure Network Watcher-pakketopname in indien nodig voor het onderzoeken van afwijkende activiteiten.

NSG-stroomlogboeken inschakelen:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Zo schakel je Network Watcher in:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Selecteer een aanbieding uit de Azure Marketplace die IDS/IPS-functionaliteit ondersteunt met payload-inspectiemogelijkheden. Als inbraakdetectie en/of preventie op basis van payload-inspectie geen vereiste is, kan Azure Firewall met Threat Intelligence worden gebruikt. Azure Firewall Threat intelligence-gebaseerde filtering kan verkeer van en naar bekende kwaadaardige IP-adressen en domeinen waarschuwen en weigeren. De IP-adressen en domeinen zijn afkomstig uit de Microsoft Threat Intelligence-feed.

Implementeer de firewalloplossing van uw keuze op elk van de netwerkgrenzen van uw organisatie om kwaadaardig verkeer te detecteren en/of te ontkennen.

Azure Marketplace:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Azure Firewall implementeren:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Waarschuwingen configureren met Azure Firewall:https://docs.microsoft.com/azure/firewall/threat-intel


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtlijnen**: Niet van toepassing. Benchmark is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of compute resources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen:** Voor resources die toegang nodig hebben tot uw containerregister, gebruikt u virtuele netwerkservicetags voor de Azure Container Registry-service om netwerktoegangsbesturingselementen voor netwerkbeveiligingsgroepen of Azure Firewall te definiëren. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag 'AzureContainerRegistry' op te geven in het juiste bron- of bestemmingsveld van een regel, u het verkeer voor de bijbehorende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

Toegang toestaan via servicetag:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkresources die zijn gekoppeld aan uw Azure-containerregisters met Azure-beleid. Gebruik Azure Policy-aliassen in de naamruimten 'Microsoft.ContainerRegistry' en 'Microsoft.Network' om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw containerregisters te controleren of af te dwingen. 

U Azure Blueprints gebruiken om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Azure Resource Manager-sjablonen, RBAC-besturingselementen en beleidsregels, in één blauwdrukdefinitie. Pas de blauwdruk eenvoudig toe op nieuwe abonnementen en stem de besturing en het beheer af via versiebeheer.

Controleer de naleving van Azure-containerregisters met Azure-beleid:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Een Azure Blueprint maken:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

**Richtlijnen:** De klant kan Azure Blueprints gebruiken om grootschalige Azure-implementaties te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Azure Resource Manager-sjablonen, RBAC-besturingselementen en beleidsregels, in één blauwdrukdefinitie. Pas de blauwdruk eenvoudig toe op nieuwe abonnementen en stem de besturing en het beheer af via versiebeheer.

Een Azure Blueprint maken:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik Azure Activity Log om netwerkbronconfiguraties te bewaken en wijzigingen op te sporen voor netwerkbronnen die verband houden met uw containerregisters. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen plaatsvinden in kritieke netwerkbronnen.

Azure Activity Log-gebeurtenissen weergeven en ophalen:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Gebruik goedgekeurde tijdsynchronisatiebronnen

**Richtlijnen**: Microsoft onderhoudt tijdbronnen voor Azure-resources, maar u hebt de optie om de tijdsynchronisatie-instellingen voor uw rekenbronnen te beheren.

Tijdsynchronisatie configureren voor Azure-rekenbronnen:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboeken configureren

**Richtlijnen:** Inname logboeken via Azure Monitor om beveiligingsgegevens die zijn gegenereerd door een Azure-containerregister te verzamelen. Gebruik in Azure Monitor Log Analytics Workspace(s) om analyses op te vragen en uit te voeren en Azure Storage Accounts te gebruiken voor opslag op lange termijn/archiverings.

Azure Container Registry-logboeken voor diagnostische evaluatie en controle:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure-resources

**Richtlijnen:** Azure Monitor verzamelt bronlogboeken (voorheen diagnostische logboeken genoemd) voor door gebruikers gestuurde gebeurtenissen in uw register. Verzamel en gebruik deze gegevens om registerverificatiegebeurtenissen te controleren en een volledig activiteitenspoor te bieden over registerartefacten zoals pull- en pushgebeurtenissen, zodat u beveiligingsproblemen met uw register diagnosticeren.

Azure Container Registry-logboeken voor diagnostische evaluatie en controle:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtlijnen**: Niet van toepassing. Benchmark is bedoeld voor compute resources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen:** Stel binnen Azure Monitor de bewaartermijn van uw Log Analytics Workspace in volgens de nalevingsvoorschriften van uw organisatie. Gebruik Azure Storage Accounts voor opslag op lange termijn/archiverings.

Logboekretentieparameters instellen voor Logboekanalysewerkruimten:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen**: Analyseer en monitor Azure Container Registry-logboeken op afwijkend gedrag en bekijk regelmatig resultaten. Gebruik de Log Analytics Workspace van Azure Monitor om logboeken te controleren en query's uit te voeren op logboekgegevens.

Azure Container Registry-logboeken voor diagnostische evaluatie en controle:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Inzicht in logboekanalysewerkruimte:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtlijnen:** Gebruik azure log analytics-werkruimte voor het bewaken en waarschuwen van afwijkende activiteiten in beveiligingslogboeken en gebeurtenissen die verband houden met uw Azure-containerregister.

Azure Container Registry-logboeken voor diagnostische evaluatie en controle:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Hoe u logboekgegevens waarschuwen voor logboekanalyse:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtlijnen**: Niet van toepassing. Azure Container Registry verwerkt of produceert geen anti-malware gerelateerde logboeken.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtlijnen**: Niet van toepassing. Azure Container Registry is een eindpunt en start geen communicatie en de service query's geen DNS.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

**Richtlijnen**: Niet van toepassing. Benchmark is bedoeld voor compute resources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Beveiligingsbeheer: Identiteits- en toegangscontrole](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van de administratieve rekeningen

**Richtlijnen**: Azure Active Directory (Azure AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en opvraagbaar zijn. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

Houd voor elk Azure-containerregister bij of het ingebouwde beheerdersaccount is ingeschakeld of uitgeschakeld. Schakel het account uit wanneer het niet in gebruik is.

Een maprol in Azure AD met PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Leden van een directoryrol in Azure AD krijgen met PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Container Registry-beheerdersaccount:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

**Richtlijnen**: Azure Active Directory (Azure AD) heeft niet het concept van standaardwachtwoorden. Andere Azure-bronnen waarvoor een wachtwoord vereist, dwingen een wachtwoord te maken met complexiteitsvereisten en een minimale wachtwoordlengte, die verschillen afhankelijk van de service. U bent verantwoordelijk voor toepassingen van derden en Marketplace-services die standaardwachtwoorden kunnen gebruiken.

Als het standaardbeheerdersaccount van een Azure-containerregister is ingeschakeld, worden complexe wachtwoorden automatisch gemaakt en moeten ze worden geroteerd. Schakel het account uit wanneer het niet in gebruik is.

Azure Container Registry-beheerdersaccount:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Gebruik maken van speciale administratieve rekeningen

**Richtlijnen**: Maak standaard werkprocedures rond het gebruik van specifieke administratieve accounts. Gebruik Azure Security Center Identity and Access Management om het aantal beheerdersaccounts te controleren.

Maak ook procedures om het ingebouwde beheerdersaccount van een containerregister in te schakelen. Schakel het account uit wanneer het niet in gebruik is.

Informatie over identiteit en toegang van azure security center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Container Registry-beheerdersaccount:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik eenmalige aanmelding (SSO) met Azure Active Directory

**Richtlijnen:** Gebruik waar mogelijk Azure Active Directory SSO in plaats van individuele zelfstandige referenties per service te configureren. Gebruik aanbevelingen voor azure security center-identiteits- en toegangsbeheer.

Voor individuele toegang tot het containerregister gebruikt u afzonderlijke aanmelding die is geïntegreerd met Azure Active Directory.

SSO begrijpen met Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

Individuele aanmelding bij een containerregister:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

**Richtlijnen:** Schakel Azure Active Directory (Azure AD) multi-factor authentication (MFA) in en volg aanbevelingen voor Azure Security Center Identity and Access Management.

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken binnen Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

**Richtlijnen:** Gebruik PAWs (gedrenke stations met bevoorrechte toegang) met MFA geconfigureerd om in te loggen en Azure-bronnen te configureren.

Meer informatie over geprivilegieerde werkstations voor toegang:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure Security Center-bewaking:** N/A

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten vanuit administratieve accounts

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) beveiligingsrapporten voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden. Gebruik Azure Security Center om identiteits- en toegangsactiviteiten te controleren.

Azure AD-gebruikers identificeren die zijn gemarkeerd voor riskante activiteiten:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

De identiteit en toegangsactiviteit van gebruikers in Azure Security Center controleren:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources beheren vanaf alleen goedgekeurde locaties

**Richtlijnen**: Gebruik locaties met voorwaardelijke toegang om alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's toegang te verlenen.

Benoemde locaties configureren in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens door sterke versleuteling te gebruiken voor gegevens in rust en onderweg. Azure AD zouten, hashes en slaat gebruikersreferenties ook veilig op.

Een Azure AD-exemplaar maken en configureren:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (Azure AD) biedt logboeken om verouderde accounts te ontdekken. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden beoordeeld om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Inzicht in Azure AD-rapportage:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure-identiteitstoegangsbeoordelingen gebruiken:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen:** u hebt toegang tot azure active directory (Azure AD) Aanmeldingsactiviteit, Controle en Risicogebeurtenislogboekbronnen, waarmee u integreren met een SIEM (Security Information and Event Management) /Monitoring tool.

U dit proces stroomlijnen door diagnostische instellingen voor Azure Active Directory-gebruikersaccounts te maken en de controlelogboeken en aanmeldingslogboeken naar een Logboekanalysewerkruimte te verzenden. U de gewenste waarschuwingen configureren binnen Log Analytics Workspace.

Azure-activiteitslogboeken integreren in Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing over afwijking van het inloggedrag van het account

**Richtlijnen:** Gebruik azure active directory (Azure AD) functies voor risico- en identiteitsbeveiliging om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. 

Azure AD-riskante aanmeldingen weergeven:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Hoe u risicobeleid voor identiteitsbescherming configureert en inschakelt:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen**: Niet beschikbaar; Customer Lockbox wordt momenteel niet ondersteund voor Azure Container Registry.

Lijst met door klanten ondersteunde services:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Beveiligingsbeheer: Gegevensbescherming](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

**Richtlijnen:** gebruik resourcetags om te helpen bij het bijhouden van Azure-containerregisters die gevoelige informatie opslaan of verwerken.

Tag en versiecontainerafbeeldingen of andere artefacten in een register en vergrendel afbeeldingen of opslagplaatsen om te helpen bij het bijhouden van afbeeldingen die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Aanbevelingen voor het taggen en versien van containerafbeeldingen:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Een containerafbeelding vergrendelen in een Azure-containerregister:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen**: Implementeer afzonderlijke containerregisters, abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Resources die gevoelige gegevens opslaan of verwerken, moeten voldoende geïsoleerd zijn.

Resources moeten worden gescheiden door een virtueel netwerk of subnet, op de juiste manier worden getagd en beveiligd door een NETWERKbeveiligingsgroep (NSG) of Azure Firewall.

Extra Azure-abonnementen maken:https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken:https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Toegang tot een Azure-containerregister beperken met behulp van een Virtueel Azure-netwerk of firewallregels:https://docs.microsoft.com/azure/container-registry/container-registry-vnet

Een NSG maken met een beveiligingsconfig:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure Firewall implementeren:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Waarschuwing of waarschuwing configureren en weigeren met Azure Firewall:

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

**Richtlijnen:** Implementeer een geautomatiseerd hulpprogramma op netwerkperimeters die controleert op ongeautoriseerde overdracht van gevoelige informatie en dergelijke overdrachten blokkeert en tegelijkertijd informatiebeveiligingsprofessionals waarschuwt.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen:** zorg ervoor dat clients die verbinding maken met uw Azure Container Registry kunnen onderhandelen over TLS 1.2 of hoger. Microsoft Azure-resources onderhandelen standaard over TLS 1.2.

Volg de aanbevelingen van azure security center voor versleuteling in rust en versleuteling tijdens het transport, indien van toepassing.

Versleuteling tijdens het transport met Azure begrijpen:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

**Richtlijnen**: Functies voor gegevensidentificatie, classificatie en verliespreventie zijn nog niet beschikbaar voor Azure Container Registry. Implementeer oplossingen van derden indien nodig voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

**Richtlijnen:** Gebruik Azure Active Directory (Azure AD) RBAC om de toegang tot gegevens en bronnen in een Azure-containerregister te beheren. 

RBAC configureren in Azure:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Rollen en machtigingen voor Azure Container Registry:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

**Richtlijnen:** Indien vereist voor naleving van compute resources, implementeert u een hulpprogramma van derden, zoals een geautomatiseerde oplossing voor het voorkomen van gegevensverlies op basis van een host, om toegangscontroles tot gegevens af te dwingen, zelfs wanneer gegevens van een systeem worden gekopieerd.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen:** Gebruik versleuteling in rust op alle Azure-bronnen. Standaard worden alle gegevens in een Azure-containerregister in rust versleuteld met door Microsoft beheerde sleutels.

Versleuteling in rust in Azure begrijpen:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Door de klant beheerde sleutels in Azure Container Registry:https://aka.ms/acr/cmk



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen:** Azure Monitor verzamelt bronlogboeken (voorheen diagnostische logboeken genoemd) voor door gebruikers gestuurde gebeurtenissen in uw register. Verzamel en gebruik deze gegevens om registerverificatiegebeurtenissen te controleren en een volledig activiteitenspoor te bieden over registerartefacten zoals pull- en pull-gebeurtenissen, zodat u operationele problemen met uw register diagnosticeren.

Azure Container Registry-logboeken voor diagnostische evaluatie en controle:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtlijnen:** Volg aanbevelingen van Azure Security Center voor het uitvoeren van kwetsbaarheidsbeoordelingen op uw containerafbeeldingen. Implementeer optioneel oplossingen van derden vanuit Azure Marketplace om beoordelingen van imagekwetsbaarheid uit te voeren.

Aanbevelingen voor het beoordelen van azure security center-kwetsbaarheden implementeren:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Azure Container Registry-integratie met Security Center (Voorbeeld):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementatie van een oplossing voor het patchbeheer van geautomatiseerd besturingssysteem

**Richtlijnen:** Microsoft voert patchbeheer uit op de onderliggende systemen die Azure Container Registry ondersteunen.

Automatiseer containerimage-updates wanneer updates voor basisafbeeldingen van het besturingssysteem en andere patches worden gedetecteerd.

Informatie over basisimage-updates voor Azure Container Registry-taken:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Geautomatiseerde softwarepatchbeheeroplossing van derden implementeren

**Richtlijnen:** U een oplossing van derden gebruiken om toepassingsafbeeldingen te patchen.  U ook Azure Container Registry-taken uitvoeren om updates voor toepassingsafbeeldingen in een containerregister te automatiseren op basis van beveiligingspatches of andere updates in basisafbeeldingen.

Informatie over basisafbeeldingsupdates voor ACR-taken:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk back-to-back kwetsbaarheid scans

**Richtlijnen**: Integreer Azure Container Registry (ACR) met Azure Security Center om het periodiek scannen van containerafbeeldingen op kwetsbaarheden mogelijk te maken. Implementeer optioneel oplossingen van derden vanuit Azure Marketplace om periodieke scans van het beveiligingslek in afbeeldingen uit te voeren.

Azure Container Registry-integratie met Security Center (Voorbeeld):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden

**Richtlijnen**: Integreer Azure Container Registry (ACR) met Azure Security Center om periodiek scannen van containerafbeeldingen op kwetsbaarheden mogelijk te maken en risico's te classificeren. Implementeer optioneel oplossingen van derden vanuit Azure Marketplace om periodieke scans van beeldkwetsbaarheden en risicoclassificatie uit te voeren.

Azure Container Registry-integratie met Security Center (Voorbeeld):https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure Security Center-bewaking:** N/A

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik Azure Resource Graph om alle bronnen (zoals compute, storage, netwerk, poorten en protocollen, enz.) binnen uw abonnement(en) op te vragen/te ontdekken.  Zorg voor de juiste (lees)machtigingen in uw tenant en opsommen van alle Azure-abonnementen en resources binnen uw abonnementen.

Hoewel klassieke Azure-bronnen kunnen worden ontdekt via Resource Graph, is het ten zeerste aan te raden om azure resource manager-resources in de toekomst te maken en te gebruiken.

Query's maken met Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weergeven:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Azure RBAC begrijpen:https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Azure Container Registry onderhoudt metagegevens, waaronder tags en manifesten voor afbeeldingen in een register. Volg aanbevolen procedures voor het taggen van artefacten.

Over registers, opslagplaatsen en afbeeldingen:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Aanbevelingen voor het taggen en versien van containerafbeeldingen:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ongeautoriseerde Azure-bronnen verwijderen

**Richtlijnen:** Azure Container Registry onderhoudt metagegevens, waaronder tags en manifesten voor afbeeldingen in een register. Volg aanbevolen procedures voor het taggen van artefacten.

Over registers, opslagplaatsen en afbeeldingen:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

Aanbevelingen voor het taggen en versien van containerafbeeldingen:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Een inventaris bijhouden van goedgekeurde Azure-resources en softwaretitels

**Richtlijnen:** U moet een voorraad van goedgekeurde Azure-resources maken volgens uw organisatiebehoeften.  

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor voor niet-goedgekeurde Azure-bronnen

**Richtlijnen:** Gebruik Azure Policy om beperkingen op te leggen aan het type resources dat in uw abonnement(en) kan worden gemaakt.

Gebruik Azure Resource Graph om bronnen binnen hun abonnement(en) te zoeken/ontdekken.  Controleer of alle Azure-resources die in de omgeving aanwezig zijn, zijn goedgekeurd.

Controleer de naleving van Azure-containerregisters met Azure-beleid:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor voor niet-goedgekeurde softwaretoepassingen binnen compute resources

**Richtlijnen**: Analyseer en monitor Azure Container Registry-logboeken op afwijkend gedrag en bekijk regelmatig resultaten. Gebruik de Log Analytics Workspace van Azure Monitor om logboeken te controleren en query's uit te voeren op logboekgegevens.

Azure Container Registry-logboeken voor diagnostische evaluatie en controle:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

Inzicht in logboekanalysewerkruimte:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

**Richtlijnen:** Azure Automation biedt volledige controle tijdens implementatie, bewerkingen en ontmanteling van workloads en resources.  U uw eigen oplossing implementeren voor het verwijderen van ongeautoriseerde Azure-bronnen. Een inleiding tot Azure Automation:https://docs.microsoft.com/azure/automation/automation-intro


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="68-use-only-approved-applications"></a>6.8: Gebruik alleen goedgekeurde aanvragen

**Richtlijnen**: Niet van toepassing. Benchmark is ontworpen voor compute resources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** Maak gebruik van Azure Policy om te beperken welke services u in uw omgeving inrichten.

Controleer de naleving van Azure-containerregisters met Azure-beleid:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek brontype weigeren met Azure-beleid:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtlijnen**: Niet van toepassing. Benchmark is ontworpen voor compute resources.



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: De mogelijkheid van gebruikers beperken om via scripts met AzureResources Manager te communiceren

**Richtlijnen:** Gebruik besturingssysteemspecifieke configuraties of bronnen van derden om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure-compute resources te beperken.

Voorwaardelijke toegang configureren om toegang tot Azure Resources Manager te blokkeren:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Beperk de mogelijkheid van gebruikers om scripts uit te voeren binnen compute resources

**Richtlijnen:** Gebruik beveiligingssysteemspecifieke configuraties of bronnen van derden om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure-compute resources te beperken.

Bijvoorbeeld hoe u powershell-scriptuitvoering in Windows-omgevingen beheert:https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtlijnen**: Software die vereist is voor bedrijfsactiviteiten, maar een hoger risico voor de organisatie kan opleveren, moet worden geïsoleerd binnen zijn eigen virtuele machine en/of virtueel netwerk en voldoende zijn beveiligd met een Azure Firewall- of Netwerkbeveiligingsgroep.

Een virtueel netwerk maken:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Een NSG maken met een beveiligingsconfig:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Beveiligingsbeheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties instellen voor alle Azure-resources

**Richtlijnen:** Gebruik Azure Policy of Azure Security Center om beveiligingsconfiguraties voor alle Azure-bronnen te onderhouden.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Controleer de naleving van Azure-containerregisters met Azure-beleid:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

**Richtlijnen:** Gebruik de aanbeveling van het Azure Security Center "Verhelpen van kwetsbaarheden in beveiligingsconfiguraties op uw virtuele machines" om beveiligingsconfiguraties op alle rekenbronnen te behouden.

Aanbevelingen voor Azure Security Center controleren:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Aanbevelingen voor Azure Security Center herstellen:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen voor uw Azure-bronnen af te dwingen.

Controleer de naleving van Azure-containerregisters met Azure-beleid:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Inzicht in Azure-beleidseffecten:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

**Richtlijnen**: Niet van toepassing. Benchmark is bedoeld voor compute resources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen:** Als u aangepaste Azure-beleidsdefinities gebruikt, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

Code opslaan in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos-documentatie:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtlijnen**: Niet van toepassing. Benchmark is van toepassing op compute resources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen:** Gebruik Azure Policy om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.

Controleer de naleving van Azure-containerregisters met Azure-beleid:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Systeemconfiguratiebeheertools implementeren voor besturingssystemen

**Richtlijnen**: Niet van toepassing. Benchmark is van toepassing op compute resources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-services

**Richtlijnen:** Gebruik Azure Security Center om basislijnscans uit te voeren voor uw Azure Resources.

Gebruik Azure Policy om beperkingen op te leggen aan het type resources dat in uw abonnement(en) kan worden gemaakt.

Aanbevelingen herstellen in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Controleer de naleving van Azure-containerregisters met Azure-beleid:https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtlijnen**: Niet van toepassing. Benchmark is van toepassing op compute resources.


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om geheim beheer voor uw cloudtoepassingen te vereenvoudigen en te beveiligen.

Hoe te integreren met Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een key vault maken:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Key Vault-verificatie voorzien van een beheerde identiteit:https://docs.microsoft.com/azure/key-vault/managed-identity

Gebruik een door Azure beheerde identiteit in Azure Container Registry-taken:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen**: Gebruik Beheerde identiteiten om Azure-services een automatisch beheerde identiteit te bieden in Azure AD. Met Managed Identities u zich verifiëren voor elke service die Azure AD-verificatie ondersteunt, inclusief Key Vault, zonder referenties in uw code.

Beheerde identiteiten configureren:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Gebruik een beheerde identiteit om te verifiëren naar een Azure-containerregister:https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


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

**Richtlijnen:** Gebruik Microsoft Antimalware voor Azure Cloud Services en Virtuele Machines om uw resources continu te bewaken en te verdedigen. Voor Linux, gebruik maken van derden antimalware oplossing.

Microsoft Antimalware configureren voor Cloud Services en virtuele machines:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-resources

**Richtlijnen**: Microsoft Antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Container Registry), maar wordt niet uitgevoerd op inhoud van klanten.

Scan vooraf bestanden die worden geüpload naar niet-compute Azure-bronnen, zoals App Service, Data Lake Storage, Blob Storage, enz.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtlijnen**: Niet van toepassing. Benchmark is bedoeld voor compute resources. Microsoft verwerkt anti-malware voor onderliggend platform.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

**Richtlijnen:** De gegevens in uw Microsoft Azure-containerregister worden altijd automatisch gerepliceerd om duurzaamheid en hoge beschikbaarheid te garanderen. Azure Container Registry kopieert uw gegevens zodat deze zijn beveiligd tegen geplande en ongeplande gebeurtenissen

Geo-repliceren van een containerregister om registerreplica's in meerdere Azure-regio's te behouden. 

Geo-replicatie in Azure Container Registry:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

**Richtlijnen**: Maakt optioneel een back-up van containerafbeeldingen door van het ene register naar het andere te importeren.

Maak een back-up van door de klant beheerde sleutels in Azure Key Vault met Azure-opdrachtregelhulpprogramma's of SDK's.

Containerafbeeldingen importeren in een containerregister:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Een back-up maken van sleutelkluissleutels in Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Test het herstel van back-ups van door klanten beheerde sleutels in Azure Key Vault met Azure-opdrachtregelhulpprogramma's of SDK's.

Azure Key Vault-sleutels herstellen in Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

**Richtlijnen:** U Soft-Delete in Azure Key Vault inschakelen om sleutels te beschermen tegen onbedoelde of kwaadwillige verwijdering.

Soft-Delete inschakelen in Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Beveiligingscontrole: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een gids voor incidentrespons maken

**Richtlijnen**: Bouw een incidentresponseguide voor uw organisatie. Zorg ervoor dat er geschreven incidentresponseplannen zijn die alle rollen van personeel definiëren, evenals fasen van incidentafhandeling/beheer, van detectie tot beoordeling na incidenten.

Werkstroomautomatiseringen configureren binnen Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Richtlijnen voor het bouwen van uw eigen beveiligingsincidentresponsproces:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De anatomie van een incident door het Microsoft Security Response Center:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De klant kan ook gebruikmaken van NIST's Computer Security Incident Handling Guide om te helpen bij het maken van hun eigen incident response plan:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een incidentscore- en prioriteringsprocedure maken

**Richtlijnen:** Azure Security Center wijst een ernst toe aan elke waarschuwing om u te helpen prioriteiten te stellen welke waarschuwingen eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing.

Bovendien, duidelijk markeren abonnementen (voor bijvoorbeeld. productie, niet-prod) en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en te categoriseren.


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen**: Voer oefeningen uit om de incidentresponsmogelijkheden van uw systemen op een regelmatige cadans te testen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.

Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat de gegevens van de klant zijn geraadpleegd door een onwettige of onbevoegde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

De beveiligingscontactpersoon azure security center instellen:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure Security Center-bewaking:** niet van toepassing

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

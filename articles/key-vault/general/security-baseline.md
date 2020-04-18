---
title: Azure Security Baseline voor Key Vault
description: Azure Security Baseline voor Key Vault
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 46fa0160dd8b37e89cdd77ba8acdae294fddbefe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616898"
---
# <a name="azure-security-baseline-for-key-vault"></a>Azure Security Baseline voor Key Vault

De Azure Security Baseline for Key Vault bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze service is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen**: Integreer Azure Key Vault met Azure Private Link. 

Azure Private Link Service stelt u in staat om toegang te krijgen tot Azure Services (bijvoorbeeld Azure Key Vault) en Azure-gehoste client-/partnerservices via een privéeindpunt in uw virtuele netwerk.

Een Azure Private Endpoint is een netwerkinterface die u privé en veilig verbindt met een service die wordt aangedreven door Azure Private Link. Het privéeindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service effectief in uw VNet wordt opgenomen. Al het verkeer naar de service kan worden doorgestuurd via het privéeindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute- of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U verbinding maken met een instantie van een Azure-bron, zodat u het hoogste niveau van granulariteit in toegangscontrole hebt.

Key Vault integreren met Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

**Richtlijnen:** Gebruik Azure Security Center en volg aanbevelingen voor netwerkbeveiliging om uw key vault-geconfigureerde resources in Azure te beveiligen. 

Voor meer informatie over de netwerkbeveiliging van Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

**Richtlijnen:** Schakel Azure DDoS Protection Standard in op de Azure Virtual Networks die zijn gekoppeld aan uw Key Vault-exemplaren voor bescherming tegen gedistribueerde denial-of-service-aanvallen. Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.

 
Azure DDoS Protection Standard beheren met de Azure-portal:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Detectie van bedreigingen voor de Azure-servicelaag in Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen:** Azure Key Vault maakt geen gebruik van netwerkbeveiligingsgroepen (NSG) en stroomlogboeken voor Azure Key Vault worden niet vastgelegd. Gebruik in plaats daarvan Azure Private Link om uw Azure Key Vault-exemplaren te beveiligen en diagnostische instellingen in te schakelen om statistieken op te nemen en gebeurtenissen te controleren.

Key Vault integreren met Azure Private Link:

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure Key Vault-logboekregistratie:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** aan deze vereiste kan worden voldaan door geavanceerde bedreigingsbeveiliging (ATP) voor Azure Key Vault te configureren. ATP biedt een extra laag beveiligingsinformatie. Deze tool detecteert mogelijk schadelijke pogingen om toegang te krijgen tot Azure Key Vault-accounts of deze te exploiteren.

Wanneer Azure Security Center afwijkende activiteit detecteert, worden waarschuwingen weergegeven. Het e-mailt ook de abonnementsbeheerder met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en herstellen van de geïdentificeerde bedreigingen.

Geavanceerde bedreigingsbeveiliging instellen voor Azure Key Vault:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen:** Voor resources die toegang nodig hebben tot uw Azure Key Vault-exemplaren, gebruikt u Azure-servicetags voor de Azure Key Vault om netwerktoegangsbesturingselementen te definiëren in netwerkbeveiligingsgroepen of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of bestemmingsveld van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

Overzicht van Azure-servicetags:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkresources die zijn gekoppeld aan uw Azure Key Vault-exemplaren met Azure Policy. Gebruik Azure Policy-aliassen in de naamruimten 'Microsoft.KeyVault' en 'Microsoft.Network' om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure Key Vault-exemplaren te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities met betrekking tot Azure Key Vault, zoals:

Key Vault moet een eindpunt voor virtuele netwerkservice gebruiken

Zelfstudie: Beleid maken en beheren om naleving af te dwingen:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure-beleidsvoorbeelden:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

Snelstart: een blauwdruk definiëren en toewijzen in de portal:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

**Richtlijnen:** Gebruik tags voor bronnen met betrekking tot netwerkbeveiliging en verkeersstroom voor uw Azure Key Vault-exemplaren om metagegevens en logische organisatie te bieden.

Gebruik een van de ingebouwde Azure-beleidsdefinities met betrekking tot tags, zoals 'Tag en de waarde ervan vereisen' om ervoor te zorgen dat alle resources met tags worden gemaakt en om u op de hoogte te stellen van bestaande niet-gelabelde bronnen.

U Azure PowerShell of Azure CLI gebruiken om acties op te zoeken of uit te voeren op basis van bronnen op basis van hun tags.

Gebruik tags om uw Azure-bronnen te ordenen:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik Azure Activity Log om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren voor netwerkbronnen die verband houden met uw Azure Key Vault-exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen plaatsvinden in kritieke netwerkbronnen.

Azure Activity Log-gebeurtenissen weergeven en ophalen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen voor activiteitenlogboeken maken, weergeven en beheren met Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Gebruik goedgekeurde tijdsynchronisatiebronnen

**Richtsnoeren**: Niet van toepassing; Microsoft onderhoudt de tijdsbron die wordt gebruikt voor Azure-bronnen, zoals Azure Key Vault, voor tijdstempels in de logboeken.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboeken configureren

**Richtlijnen:** Inname van logboeken via Azure Monitor om beveiligingsgegevens die zijn gegenereerd door Azure Key Vault te verzamelen. Gebruik in Azure Monitor azure log analytics om analyses op te vragen en uit te voeren en Azure Storage Accounts te gebruiken voor opslag op lange termijn/archiveringsopslag. U ook gegevens aan boord inschakelen en aan boord maken voor Azure Sentinel of een SIEM van derden. 

Azure Key Vault-logboekregistratie:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

Snelstart: Azure Sentinel aan boord:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure-resources

**Richtlijnen:** Schakel diagnostische instellingen in op uw Azure Key Vault-exemplaren voor toegang tot controle-, beveiligings- en diagnostische logboeken. Activiteitenlogboeken, die automatisch beschikbaar zijn, bevatten gebeurtenisbron, datum, gebruiker, tijdstempel, bronadressen, bestemmingsadressen en andere nuttige elementen.

Azure Key Vault-logboekregistratie:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen:** Stel in Azure Monitor de bewaartermijn in op basis van de nalevingsvoorschriften van uw organisatie voor de werkruimte Log Analytics die wordt gebruikt om uw Azure Key Vault-logboeken bij te houden. Gebruik Azure Storage Accounts voor opslag op lange termijn/archiverings.

Wijzig de bewaarperiode voor gegevens:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen:** Analyseer en monitor logboeken op afwijkend gedrag en bekijk regelmatig de resultaten van uw door Azure Key Vault beveiligde bronnen. Gebruik de log analytics-werkruimte van Azure Monitor om logboeken te controleren en query's uit te voeren op logboekgegevens. U ook gegevens aan boord inschakelen en aan boord maken voor Azure Sentinel of een SIEM van derden. 

Snelstart: Azure Sentinel aan boord:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Aan de slag met Log Analytics in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aan de slag met logboekquery's in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtlijnen:** Schakel in Azure Security Center advanced threat protection (ATP) in voor Key Vault. Schakel diagnostische instellingen in Azure Key Vault in en stuur logboeken naar een Log Analytics-werkruimte. Aan boord van uw Log Analytics-werkruimte naar Azure Sentinel, omdat het een SOAR-oplossing (Security Orchestration Automated Response) biedt. Hierdoor kunnen playbooks (geautomatiseerde oplossingen) worden gemaakt en gebruikt om beveiligingsproblemen op te lossen.

Snelstart: Azure Sentinel aan boord:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Reageren op gebeurtenissen met Azure Monitor Alerts:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtsnoeren**: Niet van toepassing; Azure Key Vault verwerkt of produceert geen anti-malware gerelateerde logboeken.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtsnoeren**: Niet van toepassing; Azure Key Vault verwerkt of produceert geen DNS-gerelateerde logboeken.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Beveiligingsbeheer: Identiteits- en toegangscontrole](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van de administratieve rekeningen

**Richtlijnen:** houd een voorraad bij van uw Azure Active Directory-geregistreerde toepassingen, evenals alle gebruikersaccounts die toegang hebben tot uw Azure Key Vault-sleutels, -geheimen en -certificaten. U de Azure-portal of PowerShell gebruiken om Key Vault-toegang op te vragen en te verzoenen. Als u de toegang in PowerShell wilt weergeven, gebruikt u de volgende opdracht:

(Get-azResource -Resourceid [KeyVaultResourceID]). Eigenschappen.Toegangsbeleid

Een toepassing registreren met Azure Active Directory:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

Beveiligde toegang tot een sleutelkluis:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

**Richtsnoeren**: Niet van toepassing; Azure Key Vault heeft niet het concept van standaardwachtwoorden, omdat verificatie wordt geleverd door Active Directory en is beveiligd met op rollen gebaseerd toegangsbeheer.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Gebruik maken van speciale administratieve rekeningen

**Richtlijnen:** Maak standaard operationele procedures rond het gebruik van speciale beheerdersaccounts die toegang hebben tot uw Azure Key Vault-exemplaren. Gebruik Azure Security Center Identity and Access Management (momenteel in preview) om het aantal actieve beheerdersaccounts te controleren.

Identiteit en toegang controleren (voorbeeld):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik eenmalige aanmelding (SSO) met Azure Active Directory

**Richtlijnen:** Gebruik een Azure-serviceprincipal in combinatie met de AppId, TenantID en ClientSecret om uw toepassing naadloos te verifiëren en het token op te halen dat wordt gebruikt om toegang te krijgen tot uw Azure Key Vault-geheimen.

Service-to-service-verificatie naar Azure Key Vault met .NET:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

**Richtlijnen:** Schakel Azure Active Directory Multi-Factor Authentication in en volg aanbevelingen voor Azure Security Center Identity and Access Management (momenteel in preview) om uw resources met gebeurtenishub te beschermen.

Een implementatie van Azure Multi-Factor Authentication in de cloud plannen:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang controleren (voorbeeld):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

**Richtlijnen:** Gebruik een Paw (Privileged Access Workstation) met Azure Multi-Factor Authentication (MFA) geconfigureerd om in te loggen en key vault-bronnen te configureren. 

Werkstations met toegang:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

Een implementatie van Azure Multi-Factor Authentication in de cloud plannen:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten vanuit administratieve accounts

**Richtlijnen:** Gebruik Azure Active Directory (AAD) Privileged Identity Management (PIM) voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden. Gebruik AAD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag te bekijken. Stuur azure security center-risicodetectiewaarschuwingen voor extra logboekregistratie naar Azure Monitor en configureer aangepaste waarschuwingen/meldingen met actiegroepen.

Schakel advanced threat protection (ATP) voor Azure Key Vault in om waarschuwingen voor verdachte activiteiten te genereren.

Azure AD Privileged Identity Management (PIM) implementeren:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Geavanceerde bedreigingsbeveiliging instellen voor Azure Key Vault (voorbeeld):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Waarschuwingen voor Azure Key Vault (voorbeeld:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Risicodetecties van Azure Active Directory:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Actiegroepen maken en beheren in de Azure-portal:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources beheren vanaf alleen goedgekeurde locaties

**Richtlijnen**: Configureer de locatievoorwaarde van een beleid voor voorwaardelijke toegang en beheer uw benoemde locaties. Met benoemde locaties u logische groeperingen van IP-adresbereiken of landen en regio's maken. U de toegang tot gevoelige bronnen, zoals uw Key Vault-geheimen, beperken tot de geconfigureerde locaties met de naam.

Wat is de locatievoorwaarde in voorwaardelijke toegang tot Azure Active Directory?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen:** Gebruik Azure Active Directory (AAD) als het centrale verificatie- en autorisatiesysteem voor Azure-bronnen, zoals Key Vault. Hierdoor kan RBAC (Role-based access control) gevoelige bronnen beheren.

 

Snelstart: een nieuwe tenant maken in Azure Active Directory:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Aad-logboeken (Azure Active Directory) bekijken om verouderde accounts te ontdekken met azure key vault-beheerrollen. Gebruik daarnaast AAD-toegangsbeoordelingen om groepslidmaatschappen efficiënt te beheren, toegang tot bedrijfstoepassingen die kunnen worden gebruikt om toegang te krijgen tot Azure Key Vault en roltoewijzingen. De toegang van de gebruiker moet regelmatig worden beoordeeld, zoals elke 90 dagen om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Azure Active Directory-rapporten en bewakingsdocumentatie:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Wat zijn Azure AD Access-toegangsbeoordelingen?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen**: Schakel diagnostische instellingen in voor Azure Key Vault en Azure Active Directory en verzend alle logboeken naar een Log Analytics-werkruimte. Configureer gewenste waarschuwingen (zoals pogingen om toegang te krijgen tot uitgeschakelde geheimen) binnen Log Analytics.

Azure AD-logboeken integreren met Azure Monitor-logboeken:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Migreren vanuit de oude Key Vault-oplossing:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing over afwijking van het inloggedrag van het account

**Richtlijnen:** Gebruik de functies Voor identiteitsbescherming en risicodetectie van Azure Active Directory om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot uw azure key vault-beveiligde bronnen. U moet geautomatiseerde antwoorden via Azure Sentinel inschakelen om de beveiligingsreacties van uw organisatie te implementeren. 

Riskantaanmeldingsrapport in de Azure Active Directory-portal:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

How To: Risicobeleid configureren en inschakelen:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtsnoeren**: Niet van toepassing; Customer Lockbox wordt niet ondersteund voor Azure Key Vault.

Ondersteunde services en scenario's in algemene beschikbaarheid:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Beveiligingsbeheer: Gegevensbescherming](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

**Richtlijnen:** Gebruik tags om te helpen bij het bijhouden van Azure-bronnen die gevoelige informatie opslaan of verwerken op azure key vault-bronnen. 

Gebruik tags om uw Azure-bronnen te ordenen:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen:** U de toegang tot Azure Key Vault beveiligen door gebruik te maken van eindpunten voor virtuele netwerkservices die zijn geconfigureerd om de toegang tot specifieke subnetten te beperken.

Nadat firewallregels van kracht zijn, u alleen azure key vault-gegevensvlakbewerkingen uitvoeren wanneer uw aanvraag afkomstig is van toegestane subnetten of IP-adresbereiken. Dit geldt ook voor Azure Key Vault-toegang in de Azure-portal. Hoewel u vanaf de Azure-portal naar een sleutelkluis bladeren, u mogelijk geen sleutels, geheimen of certificaten vermelden als uw clientmachine niet op de toegestane lijst staat. Dit geldt ook voor de Azure Key Vault Picker en andere Azure-services. Mogelijk u lijsten met key vaults zien, maar geen lijstsleutels, als firewallregels verhinderen dat uw clientmachine dit doet.

Azure Key Vault-firewalls en virtuele netwerken configureren:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Eindpunten voor virtuele netwerkservices voor Azure Key Vault:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

**Richtlijnen:** Alle gegevens die zijn opgeslagen in Azure Key Vault worden als gevoelig beschouwd. Gebruik toegangsbesturingselementen voor toegang tot Azure Key Vault-gegevensvlakom de toegang tot Azure Key Vault-geheimen te beheren. U ook de ingebouwde firewall van Key Vault gebruiken om de toegang op de netwerklaag te beheren. Als u de toegang tot Azure Key Vault wilt controleren, schakelt u Diagnostische instellingen voor sleutelskast in en verzendt u logboeken naar een Azure Storage-account of logboekanalysewerkruimte.

Beveiligde toegang tot een sleutelkluis:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Azure Key Vault-firewalls en virtuele netwerken configureren:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure Key Vault-logboekregistratie:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen:** Al het verkeer naar Azure Key Vault voor toegang tot verificatie, beheer en gegevensvlak, wordt versleuteld en gaat via HTTPS: poort 443. (Er zal echter af en toe HTTP [poort 80] verkeer voor CRL.) 

Toegang tot Azure Key Vault achter een firewall:https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

**Richtsnoeren**: Niet van toepassing; alle gegevens in Azure Key Vault (geheimen, sleutels en certificaten) worden als gevoelig beschouwd.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

**Richtlijnen:** Beveiligde toegang tot het beheer- en gegevensvlak van uw Azure Key Vault-exemplaren.

Beveiligde toegang tot een sleutelkluis:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

**Richtlijnen:** Microsoft beheert de onderliggende infrastructuur voor Azure Key Vault en heeft strenge controles geïmplementeerd om het verlies of de blootstelling van klantgegevens te voorkomen.

Wat is Azure Sleutelkluis?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure-klantgegevensbescherming:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen:** Alle beheerde objecten (sleutel, certificaten en geheimen) worden in rust versleuteld in Azure Key Vault.

Ondersteunende documentatie:

- [Coderingsmodel en sleutelbeheertabel](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen:** Gebruik de Azure Key Vault Analytics-oplossing in Azure Monitor om de logboeken van azure key vault-controlegebeurtenissen te bekijken.

Azure Key Vault Analytics-oplossing in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtlijnen**: Microsoft voert kwetsbaarheidsbeheer uit op de onderliggende systemen die Azure Key Vault ondersteunen.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementatie van een oplossing voor het patchbeheer van geautomatiseerd besturingssysteem

**Richtsnoeren**: N/A; Microsoft voert patchbeheer uit op de onderliggende systemen die Key Vault ondersteunen.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Geautomatiseerde softwarepatchbeheeroplossing van derden implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk back-to-back kwetsbaarheid scans

**Richtlijnen:** Microsoft voert kwetsbaarheidsbeheer uit op de onderliggende systemen die Key Vault ondersteunen.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden

**Richtlijnen:** gebruik de standaardrisicoclassificaties (Secure Score) van Azure Security Center.

Verbeter uw beveiligde score in Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik Azure Resource Graph om alle bronnen (inclusief Azure Key Vault-exemplaren) in uw abonnement op te vragen en te ontdekken. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant beschikt en dat u alle Azure-abonnementen en resources binnen uw abonnementen opsommen.

Snelstart: Voer uw eerste Resourcegraph-query uit met Azure Resource Graph Explorer:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Ontvang abonnementen waartoe de lopende rekening toegang heeft.:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Wat is op rollen gebaseerd toegangsbeheer (RBAC) voor Azure-resources?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure Key Vault-bronnen met metagegevens om ze logisch te ordenen in een taxonomie.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ongeautoriseerde Azure-bronnen verwijderen

**Richtlijnen:** gebruik tagging, beheergroepen en afzonderlijke abonnementen, waar nodig, om Azure Key Vault-exemplaren en gerelateerde bronnen te organiseren en bij te houden. Verzoen de voorraad regelmatig en zorg ervoor dat ongeautoriseerde bronnen tijdig uit het abonnement worden verwijderd.

Maak een extra Azure-abonnement:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Beheergroepen maken voor resourceorganisatie en -beheer:

https://docs.microsoft.com/azure/governance/management-groups/create

Gebruik tags om uw Azure-bronnen te ordenen:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Een inventaris bijhouden van goedgekeurde Azure-resources en softwaretitels

**Richtlijnen**: Lijst met goedgekeurde Azure-resources en goedgekeurde software voor uw rekenbronnen definiëren

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor voor niet-goedgekeurde Azure-bronnen

**Richtlijnen:** Gebruik Azure-beleid om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen

- Toegestane brontypen

Gebruik bovendien de Azure Resource Graph om bronnen binnen het abonnement(en) op te vragen/te ontdekken.

Zelfstudie: Beleid maken en beheren om naleving af te dwingen:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Snelstart: Voer uw eerste Resourcegraph-query uit met Azure Resource Graph Explorer:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor voor niet-goedgekeurde softwaretoepassingen binnen compute resources

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor Azure als geheel en voor rekenbronnen.


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

Zelfstudie: Beleid maken en beheren om naleving af te dwingen:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure-beleidsvoorbeelden:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: De mogelijkheid van gebruikers beperken om via scripts met AzureResources Manager te communiceren

**Richtlijnen:** Gebruik de voorwaardelijke toegang van Azure om de interactie van gebruikers met Azure Resource Manager (ARM) te beperken door 'Bloktoegang' voor de Microsoft Azure Management-app te configureren. Dit kan voorkomen dat resources worden gemaakt en gewijzigd in resources binnen een hoogbeveiligde omgeving, zoals resources met key vault-configuratie.

Beheer de toegang tot Azure-beheer met voorwaardelijke toegang:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


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

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.KeyVault' om aangepaste beleidsregels te maken om de configuratie van uw Azure Key Vault-exemplaren te controleren of af te dwingen. U ook ingebouwde Azure-beleidsdefinities gebruiken voor Azure Key Vault, zoals:

Key Vault-objecten moeten kunnen worden hersteld

Diagnostische instellingen voor Key Vault implementeren in de werkruimte Log Analytics

Diagnostische logboeken in Key Vault moeten zijn ingeschakeld

Key Vault moet een eindpunt voor virtuele netwerkservice gebruiken

Diagnostische instellingen voor key vault implementeren in gebeurtenishub

Gebruik aanbevelingen van Azure Security Center als een veilige configuratiebasislijn voor uw Azure Key Vault-exemplaren.

Beschikbare Azure-beleidsaliassen weergeven:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Zelfstudie: Beleid maken en beheren om naleving af te dwingen:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen af te dwingen voor uw azure key vault-bronnen. 

Zelfstudie: Beleid maken en beheren om naleving af te dwingen:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Inzicht in Azure-beleidseffecten: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen:** Als u aangepaste Azure-beleidsdefinities gebruikt voor de ingeschakelde resources van Azure Key Vault, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

Code opslaan in Azure DevOps: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure Repos-documentatie: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.KeyVault' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Systeemconfiguratiebeheertools implementeren voor besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-services

**Richtlijnen:** Azure Security Center gebruiken om basislijnscans uit te voeren voor uw azure key vault-beveiligde bronnen 

  

Aanbevelingen herstellen in Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtsnoeren**: Niet van toepassing; deze benchmark is bedoeld voor compute resources.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om geheim beheer voor uw cloudtoepassingen te vereenvoudigen en te beveiligen. Controleer of Azure Key Vault soft-delete is ingeschakeld.

Hoe te integreren met Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een key vault maken:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Key Vault-verificatie voorzien van een beheerde identiteit: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om geheim beheer voor uw cloudtoepassingen te vereenvoudigen en te beveiligen. 

  

Hoe te integreren met Azure Managed Identities: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

Een key vault maken: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

Key Vault-verificatie voorzien van een beheerde identiteit:  
https://docs.microsoft.com/azure/key-vault/managed-identity

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

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources. Microsoft verwerkt anti-malware voor onderliggend platform.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-resources

**Richtlijnen**: Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Key Vault), maar wordt niet uitgevoerd op inhoud van de klant.

Scan vooraf alle inhoud die wordt geüpload of verzonden naar niet-compute Azure-bronnen, zoals Azure Key Vault. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.

Microsoft Antimalware voor Azure Cloud Services en virtuele machines begrijpen:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources. Microsoft verwerkt anti-malware voor onderliggend platform.


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

**Richtlijnen:** Zorg voor regelmatige geautomatiseerde back-ups van uw Key Vault-certificaten, sleutels, beheerde opslagaccounts en geheimen, met de volgende PowerShell-opdrachten:

- Backup-AzKeyVaultCertificaat

- Back-up-AzKeyVaultKeyKeyKey

- Backup-azkeyvaultManagedStorageAccount

- Back-up-AzKeyVaultSecret

Optioneel u uw Key Vault-back-ups opslaan in Azure Backup.

Een back-up maken van Key Vault-certificaten:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Een back-up maken van Key Vault Keys:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Een back-up maken van key vault managed storage-accounts:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Een back-up maken van Key Vault Secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure Backup inschakelen:https://docs.microsoft.com/azure/backup



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

**Richtlijnen:** Maak back-ups van uw Key Vault-certificaten, sleutels, beheerde opslagaccounts en geheimen, met de volgende PowerShell-opdrachten:

- Backup-AzKeyVaultCertificaat

- Back-up-AzKeyVaultKeyKeyKey

- Backup-azkeyvaultManagedStorageAccount

- Back-up-AzKeyVaultSecret

Optioneel u uw Key Vault-back-ups opslaan in Azure Backup.

Een back-up maken van Key Vault-certificaten:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

Een back-up maken van Key Vault Keys:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

Een back-up maken van key vault managed storage-accounts:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

Een back-up maken van Key Vault Secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure Backup inschakelen:https://docs.microsoft.com/azure/backup



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen:** Voer periodiek gegevensherstel uit van uw Key Vault-certificaten, sleutels, beheerde opslagaccounts en geheimen, met de volgende PowerShell-opdrachten:

- Certificaat restore-azkeyvault

- Herstel-azkeyvaultkey

- Account voor beheerde opslag van azkeyvaults herstellen

- Restore-AzKeyVaultSecret

Key Vault-certificaten herstellen:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

Key Vault Keys herstellen:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

Key Vault Managed Storage Accounts herstellen:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

Key Vault Secrets herstellen:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

**Richtlijnen:** Zorg ervoor dat soft-delete is ingeschakeld voor Azure Key Vault. Soft-delete maakt het mogelijk om verwijderde sleutelkluizen en kluisobjecten zoals sleutels, geheimen en certificaten te herstellen. 

Hoe gebruik je De Soft Delete van Azure Key Vault: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Beveiligingscontrole: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een gids voor incidentrespons maken

**Richtlijnen**: Bouw een incidentresponseguide voor uw organisatie. Zorg ervoor dat er geschreven incidentresponseplannen zijn die alle rollen van personeel definiëren, evenals fasen van incidentafhandeling/beheer, van detectie tot beoordeling na incidenten. Deze processen moeten zich richten op het beschermen van gevoelige systemen, zoals die met Key Vault-geheimen.

Werkstroomautomatiseringen configureren binnen Azure Security Center: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

Richtlijnen voor het bouwen van uw eigen beveiligingsincidentresponsproces:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De anatomie van een incident door het Microsoft Security Response Center:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

De klant kan ook gebruikmaken van NIST's Computer Security Incident Handling Guide om te helpen bij het maken van hun eigen incident response plan: 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een incidentscore- en prioriteringsprocedure maken

**Richtlijnen**: Security Center wijst een ernst toe aan elke waarschuwing om u te helpen prioriteiten te stellen welke waarschuwingen eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing. Bovendien, duidelijk markeren abonnementen (voor bijvoorbeeld. productie, niet-prod) en maak een naamgevingssysteem om Azure-bronnen duidelijk te identificeren en te categoriseren, met name die welke gevoelige gegevens verwerken, zoals Azure Key Vault-geheimen.


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen:** Voer oefeningen uit om de incidentresponsemogelijkheden van uw systemen te testen op een regelmatige cadans om uw Azure Key Vault-exemplaren en gerelateerde resources te beschermen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.

Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat uw gegevens zijn geopend door een onwettige of onbevoegde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

De beveiligingscontactpersoon azure security center instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

**Richtlijnen:** exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue export om risico's voor azure key vault-resources te identificeren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren.  U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. 

 

Continue export configureren: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Waarschuwingen streamen naar Azure Sentinel: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatiseer de reactie op beveiligingswaarschuwingen

**Richtlijnen:** Gebruik de functie Workflowautomatisering in Azure Security Center om automatisch reacties te activeren via 'Logic Apps' over beveiligingswaarschuwingen en aanbevelingen om uw met Azure Key Vault beveiligde bronnen te beschermen. 

 

Workflowautomatisering en logische apps configureren: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Security Control: Penetration Tests en Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Voer regelmatig penetratietests uit van uw Azure-resources en zorg voor herstel van alle kritieke beveiligingsbevindingen binnen 60 dagen

**Richtlijnen:** U mag geen pentests uitvoeren op de Azure Key Vault-service rechtstreeks, maar het wordt aangemoedigd om uw Azure-bronnen te testen die Key Vault gebruiken om de veiligheid van de geheimen te garanderen.

U moet de Microsoft-regels van betrokkenheid volgen om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Meer informatie over de strategie en uitvoering van Red Teaming en live site penetratietesten van Microsoft met microsoft beheerde cloudinfrastructuur, services en toepassingen vindt u hier: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)

---
title: Azure-beveiligings basislijn voor virtuele WAN
description: De basis lijn voor virtuele WAN-beveiliging biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328692"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>Azure-beveiligings basislijn voor virtuele WAN

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) toegepast op Microsoft Azure virtuele WAN. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op basis van de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op virtuele WAN. **Besturings elementen** die niet van toepassing zijn op virtuele WAN, zijn uitgesloten.

Zie het [volledige bestand met de baseline-toewijzing van virtuele WAN-beveiliging](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor meer informatie over hoe virtuele WAN helemaal is toegewezen aan de Azure Security-Bench Mark.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](/azure/security/benchmarks/security-controls-v2-network-security)(Engelstalig) voor meer informatie.*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: beveiliging voor intern verkeer implementeren

**Hulp** : Azure Virtual WAN biedt geen ondersteuning voor het rechtstreeks implementeren in een virtueel netwerk. U kunt echter nog steeds regels voor netwerk beveiligings groepen Toep assen op spoke-resources, de beveiliging van Azure Firewall gebruiken of aangepaste route tabellen maken om privé verkeer te voor komen of toe te staan.

- [Aangepaste routerings scenario's](scenario-any-to-any.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="ns-2-connect-private-networks-together"></a>NS-2: particuliere netwerken met elkaar verbinden 

**Hulp** : gebruik Azure ExpressRoute of Azure Virtual Private Network (VPN) om particuliere verbindingen te maken tussen Azure-data centers en on-premises infra structuur in een co-locatie omgeving. ExpressRoute-verbindingen gaan niet via het open bare Internet, bieden meer betrouw baarheid, hogere snelheden en lagere latenties dan typische Internet verbindingen. 

Voor punt-naar-site-VPN en site-naar-site-VPN kunt u on-premises apparaten of netwerken verbinden met een virtueel netwerk met behulp van een combi natie van deze VPN-opties en ExpressRoute. Gebruik Virtual Network peering om twee of meer virtuele netwerken in azure met elkaar te verbinden. Netwerk verkeer tussen gekoppelde virtuele netwerken is privé en wordt opgeslagen in het Azure-backbone-netwerk.

- [ExpressRoute in virtuele WAN](virtual-wan-expressroute-portal.md)

- [Overzicht van site-naar-site-VPN](virtual-wan-site-to-site-portal.md)

- [Site-VPN-overzicht](virtual-wan-point-to-site-portal.md)

- [Private Link](howto-private-link.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : gedeeld

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3: toegang tot privé-netwerk tot stand brengen met Azure-Services

**Hulp** : persoonlijke Azure-koppeling gebruiken om persoonlijke toegang tot Azure Virtual WAN vanuit uw virtuele netwerken mogelijk te maken zonder het Internet te passeren. Persoonlijke toegang is een andere ingrijpende meting naast verificatie en verkeers beveiliging die wordt geboden door Azure-Services.

- [Persoonlijke Azure-koppeling](../private-link/private-link-overview.md)

- [VPN-verbinding met virtueel WAN](howto-private-link.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

**Richt lijnen** : Beveilig uw virtuele WAN-bronnen van Azure tegen aanvallen van externe netwerken, waaronder DDoS-aanvallen (Distributed Denial of service), toepassingsspecifieke aanvallen, en ongevraagd en mogelijk schadelijk Internet verkeer. 

Gebruik Azure Firewall om toepassingen en services te beveiligen tegen potentieel schadelijk verkeer van Internet en andere externe locaties. Kies Azure DDoS Protection voor uw assets tegen aanvallen op uw virtuele Azure-netwerken. Gebruik Azure Security Center om onjuiste configuratie Risico's te detecteren die betrekking hebben op uw netwerkgerelateerde bronnen.

- [Documentatie over Azure Firewall](/azure/firewall)

- [Azure DDoS Protection Standard beheren met de Azure Portal](/azure/virtual-network/manage-ddos-protection) 

- [Aanbevelingen voor Azure Security Center](../security-center/recommendations-reference.md#recs-network)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : gedeeld

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: inbraak detectie/inbraak preventie systemen (ID'S/IP'S) implementeren

**Richt lijnen** : de beste manier om detectie-of preventie systemen in azure Virtual WAN te implementeren, is het gebruik van een virtueel netwerk apparaat in de spoke-netwerken die zijn gekoppeld aan de virtuele hub.  Meer informatie is beschikbaar in de routerings scenario's op de koppelingen waarnaar wordt verwezen. 

- [Scenario - Verkeer routeren via een NVA](scenario-route-through-nva.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Hulp** : Azure Virtual Network Service-Tags gebruiken om netwerk toegangs beheer te definiëren voor Azure-netwerk beveiligings groepen of Azure firewall geconfigureerd voor uw virtuele WAN-resources. 

Gebruik service tags in plaats van specifieke IP-adressen bij het maken van beveiligings regels. Door de naam van de service label (bijvoorbeeld: {VirtualWAN: Virtual Network}) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7: secure Domain Name Service (DNS)

**Hulp** : Azure Virtual WAN biedt aangepaste DNS-servers voor punt-naar-site-VPN-gateways. 

Volg de aanbevolen procedures voor DNS-beveiliging om te beperken tegen veelvoorkomende aanvallen zoals Dangling DNS, DNS versterkt aanvallen, vergiftiging en spoofing van DNS, enzovoort.

Als Azure DNS als gezaghebbende DNS-service wordt gebruikt, moet u ervoor zorgen dat DNS-zones en-records worden beveiligd tegen onbedoelde of schadelijke wijzigingen met behulp van Azure op rollen gebaseerd toegangs beheer (Azure RBAC) en resource vergrendelingen.

- [Overzicht van Azure DNS](../dns/dns-overview.md) 

- [Implementatie handleiding voor beveiligde Domain Name System (DNS)](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [Dangling DNS-vermeldingen voor komen en de overname van subdomeinen voor komen](../security/fundamentals/subdomain-takeover.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Bench Mark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management)(Engelstalig) voor meer informatie.*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>Chat-1: Azure Active Directory standaardiseren als het centrale identiteits-en verificatie systeem

**Hulp** : de punt-naar-site VPN van Azure Virtual WAN is geïntegreerd met Azure Active Directory (Azure AD), de standaard service voor identiteits-en toegangs beheer van Azure. U moet Azure AD standaardiseren om het identiteits-en toegangs beheer van uw organisatie in te bepalen:

- Microsoft Cloud resources, zoals de Azure Portal, Azure Storage, Azure Virtual Machines (Linux en Windows), Azure Key Vault, PaaS en SaaS-toepassingen.
- De resources van uw organisatie, zoals toepassingen op Azure of uw bedrijfs netwerk resources.

Beveilig Azure AD met hoge prioriteit in de Cloud beveiligings procedure van uw organisatie. Evalueer uw identiteits-en beveiligings postuur met de functie beveiligings Score van Azure Security Center om te meten hoe nauw keurig uw configuratie overeenkomt met best practice aanbevelingen. Implementeer de best practice aanbevelingen van micro soft voor verbeteringen in uw beveiligings postuur.

Azure AD biedt ook ondersteuning voor externe identiteiten, waarmee gebruikers zonder Microsoft-account zich kunnen aanmelden bij hun toepassingen en resources met hun externe identiteit. Bekijk informatie over het gebruik van Azure AD in punt-naar-site-VPN-scenario's op de koppelingen waarnaar wordt verwezen.

- [Een Azure Active Directory-Tenant (AD) maken voor P2S OpenVPN-protocol verbindingen](openvpn-azure-ad-tenant-multi-app.md)

- [Azure Active Directory-verificatie voor gebruikers-VPN configureren](virtual-wan-point-to-site-azure-ad.md)

- [Tenancy in Azure Active Directory](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [Een Azure AD-exemplaar maken en configureren](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Externe ID-providers voor de toepassing gebruiken](/azure/active-directory/b2b/identity-providers)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : gedeeld

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>Chat-4: gebruik sterke verificatie-instellingen voor alle toegang op basis van Azure Active Directory

**Hulp** : het punt-naar-site-VPN van Azure Virtual WAN is geïntegreerd met Azure Active Directory (Azure AD), dat ondersteuning biedt voor krachtige verificatie methoden met meervoudige verificatie en een krachtige methode zonder wacht woord.

- Multi-factor Authentication: Schakel Azure AD multi-factor Authentication in en volg de aanbevelingen voor identiteits-en toegangs beheer van Azure Security Center voor de aanbevolen procedures voor het instellen van multi-factor Authentication. Multi-factor Authentication kan worden afgedwongen voor alle, Selecteer gebruikers of op het niveau per gebruiker op basis van aanmeldings voorwaarden en risico factoren.

- Verificatie met wacht woord: er zijn drie verificatie opties met een wacht woord beschikbaar. Dit zijn onder andere, Windows hello voor bedrijven, Microsoft Authenticator-apps en on-premises verificatie methoden zoals Smart Cards.

Voor beheerders en bevoegde gebruikers zorgt u ervoor dat het hoogste niveau van de methode voor sterke verificatie wordt gebruikt, gevolgd door een implementatie van het juiste beleid voor sterke authenticatie naar andere gebruikers.

- [MFA inschakelen in P2S VPN voor virtuele WAN](openvpn-azure-ad-mfa.md)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : gedeeld

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Chat-6: toegang tot Azure-bronnen beperken op basis van voor waarden

**Richt lijnen** : de punt-naar-site VPN van Azure Virtual WAN ondersteunt voorwaardelijke toegang van Azure AD voor een meer gedetailleerd toegangs beheer op basis van door de gebruiker gedefinieerde voor waarden. Gebruikers aanmeldingen van bepaalde IP-bereiken moeten bijvoorbeeld multi-factor Authentication gebruiken voor het aanmelden. Gedetailleerd beheer beleid voor verificatie sessies kan ook worden gebruikt voor verschillende use cases.

- [Algemeen beleid voor voorwaardelijke toegang](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Verificatie sessie beheer met voorwaardelijke toegang configureren](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [Voorwaardelijke toegang voor virtuele WAN P2S-VPN](openvpn-azure-ad-mfa.md#conditional)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="im-7-eliminate-unintended-credential-exposure"></a>Chat-7: onbedoelde referentie blootstelling elimineren

**Richt lijnen** : referentie scanner implementeren om referenties in code te identificeren. Referentie scanner stimuleert ook het verplaatsen van gedetecteerde referenties naar veiliger locaties, zoals Azure Key Vault.

Voor GitHub kunt u de systeem eigen functie voor het scannen van geheime gegevens gebruiken om referenties of een andere vorm van geheimen binnen de code te identificeren.

- [Referentie scanner instellen](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub Secret Scanning](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

## <a name="privileged-access"></a>Privileged Access

*Zie [Azure Security Bench Mark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access)(Engelstalig) voor meer informatie.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

**Hulp** : Azure Virtual WAN maakt gebruik van Azure-functies voor toegangs beheer op basis van rollen (Azure RBAC) voor het isoleren van toegang tot bedrijfskritische systemen door te beperken welke accounts bevoegde toegang krijgen tot de abonnementen en beheer groepen waarin ze zich bevinden.

Beperk ook de toegang tot de beheer-, identiteits-en beveiligings systemen met beheerders toegang tot uw essentiële bedrijfs toegang, zoals Active Directory-domein controllers, beveiligings hulpprogramma's en Systeembeheer Programma's met agents die zijn geïnstalleerd op essentiële bedrijfs systemen. Aanvallers die deze beheer-en beveiligings systemen misbruiken, kunnen ze onmiddellijk weaponize om bedrijfs kritieke activa te beschadigen.

Alle typen besturings elementen voor toegang moeten worden afgestemd op uw bedrijfs segmentatie strategie om een consistent toegangs beheer te garanderen.

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access) 

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: bevoorrechte toegang gebruiken werk stations

**Richt lijnen** : beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. Gebruik zeer beveiligde werk stations van gebruikers of Azure Bastion voor beheer taken. Gebruik Azure Active Directory (Azure AD), micro soft Defender Advanced Threat Protection (ATP) of Microsoft Intune voor het implementeren van een beveiligd en beheerd gebruikers werkstation voor beheer taken. De beveiligde werk stations kunnen centraal worden beheerd voor het afdwingen van beveiligde configuratie, waaronder sterke authenticatie, software-en hardware-basis lijnen, beperkte logische en netwerk toegang.

- [Meer informatie over privileged Access workstations](../active-directory/devices/concept-azure-managed-workstation.md)

- [Een privileged Access-werk station implementeren](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection)voor meer informatie.*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: gevoelige gegevens tijdens de overdracht versleutelen

**Hulp** : versleuteling is essentieel voor verkeer op externe en open bare netwerken.

- Met behulp van toegangs beheer, gegevens doorvoer moet worden beveiligd tegen buiten-band-aanvallen (zoals het vastleggen van verkeer) met versleuteling om ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

- Zorg ervoor dat er een HTTP-verkeer is, dat clients die verbinding maken met uw Azure-resources, TLS v 1.2 of hoger kunnen onderhandelen.-

- Voor extern beheer gebruikt u SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. De verouderde SSL/TLS/SSH-versies, protocollen en zwakke versleuteling moeten worden uitgeschakeld.-

- Bij de onderliggende infra structuur biedt Azure gegevens in transit versleuteling standaard voor gegevens verkeer tussen Azure-data centers.

Over het algemeen bieden we versleuteling voor de beveiligde micro soft-backbone en mogelijkheden voor het versleutelen van verkeer in uw site-naar-site-VPN, site-naar-site-VPN via Azure ExpressRoute en punt-naar-site-gebruikers-VPN.

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem)

- [Dubbele versleuteling voor Azure-gegevens in transit](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : gedeeld

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Bench Mark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)voor meer informatie.*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: controleren of het beveiligings team toegang heeft tot de inventaris en meta gegevens van de Asset

**Richt lijnen** : Tags Toep assen op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

Azure Virtual WAN biedt ook ondersteuning voor Azure Resource Manager op basis van resource-implementaties en Azure resource Graph-query's. 

- [Voor meer informatie over het labelen van assets raadpleegt u de hand leiding resource naamgeving en Tags toevoegen](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

**Hulp** : gebruik Azure Policy om services te beperken die in uw omgeving kunnen worden ingericht. Zoek en analyseer resources met Azure resource Graph binnen uw abonnementen en gebruik Azure Monitor om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md)

- [Het weigeren van een specifiek resource type met Azure Policy](../governance/policy/samples/built-in-policies.md#general)

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

**Richt lijnen** : gebruik de voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te geven om met Azure-resources te communiceren te beperken door ' toegang blok keren ' te configureren voor de App ' Microsoft Azure-beheer '.

- [Voorwaardelijke toegang configureren om de toegang tot Azure Resource Manager te blok keren](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

## <a name="logging-and-threat-detection"></a>Logboek registratie en detectie van bedreigingen

*Zie [Azure Security Bench Mark: Logging and Threat Detection](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)(Engelstalig) voor meer informatie.*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: detectie van bedreigingen inschakelen voor Azure-identiteits-en toegangs beheer

**Hulp** : Azure Active Directory (Azure AD) biedt de volgende gebruikers logboeken die kunnen worden weer gegeven in azure AD Reporting of geïntegreerd met Azure monitor, Azure Sentinel, Siem of controle hulpprogramma's voor meer geavanceerde bewakings-en analyse cases. Deze zijn:

- Aanmelden: het aanmeld rapport bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers.
- Auditlogboeken: traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. Voor beelden van audit logboeken zijn wijzigingen die zijn aangebracht in resources binnen Azure AD, zoals het toevoegen of verwijderen van gebruikers, apps, groepen, rollen en beleid.
- Risk ante aanmelding: een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is.
- Gebruikers voor wie wordt aangegeven dat ze risico lopen - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast.

Gebruik Azure Security Center om waarschuwingen te maken voor bepaalde verdachte activiteiten, zoals een buitensporig aantal mislukte verificatie pogingen, waaronder afgeschafte accounts in het abonnement. Naast de basis bewaking van beveiligings hygiëne kunt u met behulp van de bedreigings beveiligings module van Security Center uitgebreidere beveiligings waarschuwingen verzamelen van afzonderlijke Azure Compute-resources (virtuele machines, containers, app service), gegevens bronnen (SQL data base en opslag) en Azure-service lagen. Met deze functie kunt u inzicht krijgen in de account afwijkingen binnen de afzonderlijke resources.

- [Rapporten met controle activiteiten in de Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Azure Identity Protection inschakelen](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : gedeeld

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: logboek registratie inschakelen voor Azure-netwerk activiteiten

**Richt lijnen** : gebruik hulpprogram ma's voor het vastleggen van VPN-pakketten voor het vastleggen van netwerk pakketten tussen uw Azure Virtual WAN-resources. Dit kan helpen bij het fout opsporings proces dat is gerelateerd aan uw hybride netwerk. Hoewel u geen netwerk beveiligings groep kunt implementeren op virtuele WAN, kunt u deze implementeren op uw spoke-virtuele netwerk resources.

Schakel logboeken stroom voor netwerk beveiligings groep in de netwerk beveiligings groepen in voor het controleren van verkeer.

Schakel de functie Azure Traffic Analytics in om stroom logboeken te verwerken die in het opslag account worden bewaard en verzend deze vervolgens naar een Log Analytics-werk ruimte. Traffic Analytics biedt extra inzicht in de verkeers stroom voor uw Azure-netwerken. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren en HOTS pots te identificeren, beveiligings dreigingen te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren.

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Traffic Analytics inschakelen en gebruiken](../network-watcher/traffic-analytics.md) 

Virtuele WAN produceert of verwerkt geen DNS-query logboeken die moeten worden ingeschakeld.

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: logboek registratie inschakelen voor Azure-resources

**Richt lijnen** : activiteiten logboeken van Azure, die automatisch worden ingeschakeld, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw virtuele WAN-resources van Azure, met uitzonde ring van Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden tijdens het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd. Virtuele WAN produceert echter geen Azure-resource Logboeken.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 
- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : gedeeld

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: beveiligings logboek beheer en-analyse centraliseren

**Richt lijnen** : opslag en analyse van logboek registratie centraliseren om correlatie in te scha kelen. Zorg ervoor dat u voor elke logboek bron een gegevens eigenaar hebt toegewezen, toegangs richtlijnen, opslag locatie, welke hulpprogram ma's worden gebruikt voor het verwerken en openen van de gegevens en vereisten voor gegevens behoud. Zorg ervoor dat u Azure-activiteiten logboeken integreert in uw centrale systeem voor logboek registratie. 

Opname logboeken via Azure Monitor voor het verzamelen van beveiligings gegevens die zijn gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te gebruiken en gebruik Azure Storage-accounts voor lange termijn-en archiverings opslag. Daarnaast kunt u gegevens inschakelen en vrijgeven aan Azure Sentinel of een SIEM van derden.

Veel organisaties kiezen voor het gebruik van Azure Sentinel voor ' hot ' gegevens die regel matig worden gebruikt en die worden Azure Storage voor ' koude ' gegevens die minder vaak worden gebruikt.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Azure-Sentinel onboarden](../sentinel/quickstart-onboard.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : gedeeld

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response)(Engelstalig) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: voor bereiding-respons proces van incidenten bijwerken voor Azure

**Hulp** : Zorg ervoor dat uw organisatie processen heeft om te reageren op beveiligings incidenten, dat deze processen voor Azure zijn bijgewerkt en dat ze regel matig de voor bereidingen spelen. Zorg ervoor dat de service aanbieding is opgenomen in het reactie proces van het incident, zoals van toepassing.

- [Implementeer beveiliging in de bedrijfs omgeving](https://aka.ms/AzSec4) 
- [Naslag Gids voor respons op incidenten](https://aka.ms/IRRG)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: voor bereiding-incident melding instellen

**Richt lijnen** : contact gegevens voor beveiligings incidenten instellen in azure Security Center. Deze contact gegevens worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. U hebt ook opties voor het aanpassen van incident waarschuwingen en meldingen in verschillende Azure-Services op basis van de behoeften van uw incident respons.

Contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen volledig zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md) 

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Hulp** : Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook eenvoudig abonnementen markeren (bijvoorbeeld productie, niet-productie) en een naamgevings systeem maken om Azure-resources duidelijk te identificeren en te categoriseren.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : klant

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detectie en analyse: een incident onderzoeken

**Hulp** : een antwoord gids voor incidenten maken voor uw organisatie. Voer oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Identificeer zwakke punten en tussen ruimten en wijzig zo nodig het schema.

Zorg ervoor dat er schriftelijke incidenten abonnementen zijn die alle werk rollen definiëren, evenals fasen van incident handling/management van detectie tot een beoordeling van het incident.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detectie en analyse: de prioriteit van incidenten bepalen

**Hulp** : Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke bedoelingen zijn achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook eenvoudig abonnementen markeren (zoals productie, niet-productie) en een naamgevings systeem maken om Azure-resources duidelijk te identificeren en te categoriseren.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : gedeeld

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: insluiting, uitroeiing en herstel: de verwerking van incidenten automatiseren

**Hulp** : gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Azure Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : klant

## <a name="posture-and-vulnerability-management"></a>Postuur en beveiligings beheer

*Zie voor meer informatie de [Azure Security Bench Mark: postuur en het beveiligings beleid](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: regel matige aanvals simulatie uitvoeren

**Richt lijnen** : u kunt aan de hand van uw vereisten indringings tests of rode team activiteiten uitvoeren op uw Azure-resources en ervoor zorgen dat alle essentiële beveiligings resultaten worden hersteld.

Volg de Microsoft Cloud regels voor het testen van de indringings fase om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft, het uitvoeren van de implementatie van Red Teaming en live site penetratie op basis van door micro soft beheerde Cloud infrastructuur,-services en-toepassingen.

- [Indringings tests in azure](../security/fundamentals/pen-testing.md)

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking** : niet van toepassing

**Verantwoordelijkheid** : klant

## <a name="endpoint-security"></a>Endpoint Security

*Zie [Azure Security Bench Mark: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security)(Engelstalig) voor meer informatie.*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: eindpunt detectie en-antwoord gebruiken (EDR)

**Richt lijnen** : klanten mogen geen instellingen voor het configureren van het eind punt detecteren en antwoorden. De Virtual Machines die in de Azure Virtual WAN-aanbieding wordt gebruikt, maken echter gebruik van deze mogelijkheden. Meer informatie over deze algemene mogelijkheden vindt u op de koppelingen waarnaar wordt verwezen. 

- [Overzicht van micro soft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Micro soft Defender ATP-service voor Windows-servers](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Micro soft Defender ATP-service voor niet-Windows-servers](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure Security Center bewaking** : Ja

**Verantwoordelijkheid** : gedeeld

## <a name="governance-and-strategy"></a>Governance en strategie

*Zie [Azure Security Bench Mark: governance en strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy)voor meer informatie.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: de strategie voor Asset Management en gegevens bescherming definiëren 

**Richt lijnen** : Zorg ervoor dat u een duidelijke strategie documenteert en communiceert voor continue bewaking en bescherming van systemen en gegevens. Volg prioriteiten voor detectie, beoordeling, beveiliging en bewaking van bedrijfs kritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

- Standaard gegevens classificatie in overeenstemming met de zakelijke Risico's
- Zicht baarheid van beveiligings organisaties in Risico's en activa-inventaris 
- Goed keuring van beveiligings organisaties van Azure-Services voor gebruik 
- Beveiliging van assets via hun levens cyclus
- Vereiste strategie voor toegangs beheer in overeenstemming met organisatie gegevens classificatie
- Gebruik van functies voor gegevens bescherming van Azure native en derden
- Gegevens versleutelings vereisten voor in-transit-en rest-use cases
- Juiste cryptografische normen

Bekijk aanvullende informatie die beschikbaar is op de koppelingen waarnaar wordt verwezen.

- [Aanbeveling van Azure-beveiligings architectuur-opslag, gegevens en versleuteling](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Basis beginselen van Azure-beveiliging-Azure-gegevens beveiliging,-versleuteling en-opslag](../security/fundamentals/encryption-overview.md)

- [Cloud-acceptatie Framework-aanbevolen procedures voor Azure Data Security en versleuteling](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: ondernemings segmentatie strategie definiëren 

**Richt lijnen** : Stel een bedrijfs strategie in om de toegang tot assets te segmenteren met een combi natie van identiteits-, netwerk-, toepassings-, abonnements-, beheer groep-en andere besturings elementen. Houd de nood zaak van beveiligings scheiding zorgvuldig bij met de nood zaak om de dagelijkse werking van de systemen in te scha kelen die met elkaar moeten communiceren en toegang hebben tot gegevens.

Zorg ervoor dat de segmentatie strategie consistent wordt geïmplementeerd in alle controle typen, waaronder netwerk beveiliging, identiteits-en toegangs modellen, en toepassings machtigingen of-modellen en de besturings elementen voor Human processen.

- [Richt lijnen voor segmentatie strategie in azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Richt lijnen voor segmentatie strategie in azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Netwerk segmentatie uitlijnen met strategie voor bedrijfs segmentatie](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: beveiligings strategie voor postuur-beheer definiëren

**Hulp** : regel matig Risico's voor uw afzonderlijke assets en de omgeving waar ze worden gehost. Volg prioriteiten voor hoogwaardige en zeer belichte aanvallen, zoals gepubliceerde toepassingen, netwerk binnenkomend en uitgevende punten, gebruikers-en beheerders eindpunten, enzovoort.

- [Azure Security Bench Mark-postuur en beveiligings beheer](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: organisatie rollen, verantwoordelijkheden en accountabilities uitlijnen

**Richt lijnen** : Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligings organisatie documenteert en communiceert. Volg prioriteiten voor uw inspanningen op basis van een duidelijke verantwoordelijkheid voor beveiligings beslissingen, het trainen van iedereen op het gedeelde verantwoordelijkheids model en het opleveren van technische teams op technologie om de cloud te beveiligen.

- [Best Practice voor Azure-beveiliging 1: personen: teams trainen in Cloud Security traject](https://aka.ms/AzSec1)

- [Aanbevolen beveiligings procedure voor Azure-gebruikers: teams in de Cloud-beveiligings technologie trainen](https://aka.ms/AzSec2)

- [Best Practice voor Azure-beveiliging 3-proces: verantwoordelijkheid toewijzen voor Cloud beveiligings beslissingen](https://aka.ms/AzSec3)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="gs-5-define-network-security-strategy"></a>GS-5: netwerk beveiligings strategie definiëren

**Richt lijnen** : Stel een Azure-netwerk beveiligings benadering in als onderdeel van de algehele strategie voor het toegangs beheer van uw organisatie. Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

- Gecentraliseerd netwerk beheer en beveiligings verantwoordelijkheid
- Segment model voor virtuele netwerken dat is afgestemd op de strategie voor bedrijfs segmentatie
- Herstel strategie in verschillende scenario's voor bedreigingen en aanvallen
- Internet-en ingangs-en uitgangs strategie
- Hybride Cloud en on-premises interconnectiviteit-strategie
- Up-to-date netwerk beveiligings artefacten (zoals netwerk diagrammen, referentie netwerk architectuur)

Bekijk aanvullende informatie die beschikbaar is op de koppelingen waarnaar wordt verwezen.

- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](https://aka.ms/AzSec11)

- [Azure Security Bench Mark-netwerk beveiliging](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Overzicht van Azure-netwerk beveiliging](../security/fundamentals/network-overview.md)

- [Strategie voor bedrijfs netwerk architectuur](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: identiteits-en bevoorrechte toegangs strategie definiëren

**Richt lijnen** : Azure Identity and privileged Access benaderingen instellen als onderdeel van de algehele strategie voor het toegangs beheer van uw organisatie. Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

- Een gecentraliseerd identiteits-en verificatie systeem en de interconnectiviteit daarvan met andere interne en externe identiteits systemen
- Sterke authenticatie methoden in verschillende use-cases en-voor waarden
- Beveiliging van gebruikers met hoge bevoegdheden
- Bewaking en verwerking van afwijkende gebruikers activiteiten  
- Beoordelings-en afstemmings proces voor gebruikers-id en-toegang

Bekijk aanvullende informatie die beschikbaar is op de koppelingen waarnaar wordt verwezen.

- [Azure Security Bench Mark-Identity Management](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security-benchmark-privileged Access](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](https://aka.ms/AzSec11)

- [Overzicht van Azure Identity Management-beveiliging](../security/fundamentals/identity-management-overview.md)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: vastleggen van de logboek registratie en de reactie op bedreigingen

**Richt lijnen** : Stel een strategie voor logboek registratie en reactie op bedreigingen in om bedreigingen snel te detecteren en op te lossen terwijl aan nalevings vereisten wordt voldaan. Volg prioriteiten om analisten te voorzien van waarschuwingen met hoge kwaliteit en naadloze ervaring, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en hand matige stappen. 

Deze strategie dient gedocumenteerde richt lijnen, beleid en standaarden voor de volgende elementen te omvatten: 

- De rol en verantwoordelijkheden van de organisatie voor beveiligings bewerkingen (SecOps)
- Een goed gedefinieerd respons proces voor incidenten dat wordt afgestemd op het NIST of een ander branche raamwerk
- Vastleggen en bewaren in Logboeken voor ondersteuning van detectie van bedreigingen, reacties op incidenten en nalevings behoeften
- Gecentraliseerde zicht baarheid van en correlatie gegevens over bedreigingen, met behulp van SIEM, systeem eigen Azure-functies en andere bronnen: communicatie-en meldings abonnement met uw klanten, leveranciers en open bare partijen
- Gebruik van Azure native en platforms van derden voor het verwerken van incidenten, zoals logboek registratie en detectie van bedreigingen, forensische, herstel en uitroeiing van aanvallen
- Processen voor het verwerken van incidenten en activiteiten na incidenten, zoals geleerde lessen en bewijs behoud

Bekijk aanvullende informatie die beschikbaar is op de koppelingen waarnaar wordt verwezen.
- [Azure Security Bench Mark-logboek registratie en detectie van bedreigingen](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Bench Mark-incident respons](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Aanbevolen procedure voor Azure-beveiliging 4-proces. Reactie processen voor incidenten bijwerken voor Cloud](https://aka.ms/AzSec4)

- [Hand leiding Azure-acceptatie raamwerk, logboek registratie en rapportage](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure Security Center bewaking** : momenteel niet beschikbaar

**Verantwoordelijkheid** : klant

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Security Bench Mark v2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](/azure/security/benchmarks/security-baselines-overview)

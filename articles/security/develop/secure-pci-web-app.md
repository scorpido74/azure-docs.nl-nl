---
title: Beveiligde web-app voor PCI DSS | Microsoft Documenten
description: Deze voorbeeld-app implementeert best practices voor beveiliging die uw toepassing en de beveiligingshouding van uw organisatie verbeteren wanneer u zich ontwikkelt op Azure.
keywords: nb
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992612"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Een veilige infrastructuur voor een PCI-app ontwikkelen

## <a name="overview"></a>Overzicht

Deze beveiligde infrastructuur voor een PCI-app (Payment Card Industry) biedt richtlijnen voor de implementatie van een Platform voor de industrie van betaalkaarten als een serviceomgeving (PaaS) die geschikt is voor het verzamelen, opslaan en ophalen van kaarthoudergegevens. Deze oplossing automatiseert de implementatie en configuratie van Azure-resources voor een gemeenschappelijke referentiearchitectuur, toont manieren waarop klanten kunnen voldoen aan specifieke beveiligings- en nalevingsvereisten en dient als basis voor klanten om te bouwen en hun eigen oplossingen op Azure configureren. De oplossing implementeert een subset van vereisten die vergelijkbaar zijn met de databeveiligingsstandaarden voor betaalkaartindustrie (PCI DSS 3.2).

In dit voorbeeld wordt automatisch een PaaS-referentiearchitectuur voor webtoepassingen geïmplementeerd met vooraf geconfigureerde beveiligingsbesturingselementen om klanten te helpen naleving te bereiken die vergelijkbaar is met pci DSS 3.2-vereisten. De oplossing bestaat uit Azure Resource Manager-sjablonen en PowerShell-scripts die de implementatie en configuratie van resources begeleiden.

Volg de in dit artikel beschreven stappen achtereenvolgens om ervoor te zorgen dat de toepassingsonderdelen correct zijn geconfigureerd. De database, Azure App Service, Azure Key Vault-instantie en azure application gateway-instantie zijn van elkaar afhankelijk.

De implementatiescripts stellen de infrastructuur in. Nadat u de implementatiescripts hebt uitgevoerd, moet u een handmatige configuratie uitvoeren in de Azure-portal om de componenten en services aan elkaar te koppelen.

Dit voorbeeld is gericht op ervaren ontwikkelaars op Azure die binnen de detailhandel werken en een retail-app willen bouwen met een beveiligde Azure-infrastructuur.

> [!NOTE]
> Deze architectuur is bedoeld om te dienen als een basis voor klanten aan te passen aan hun specifieke eisen en mag niet worden gebruikt als-is in een productie-omgeving.

Het implementeren van een toepassing in deze omgeving zonder wijziging is niet voldoende om volledig te voldoen aan de vereisten van PCI DSS 3.2. Houd rekening met het volgende:

- Deze architectuur biedt een basislijn waarmee klanten Azure op een pci DSS 3.2-compatibele manier kunnen gebruiken.
- Klanten zijn verantwoordelijk voor het uitvoeren van de juiste beveiligings- en nalevingsbeoordeling van elke oplossing die met deze architectuur is gebouwd, omdat de vereisten kunnen variëren op basis van de specifieke kenmerken van de implementatie van elke klant.

Bij het ontwikkelen en implementeren van deze app leert u hoe u:

- Maak een Azure Key Vault-exemplaar en sla er geheimen uit op.
- Implementeer Azure Database voor Azure SQL, stel beveiligde gegevens in en vernieuw de toegang tot azure.
- Implementeer de Azure-web-app met app-serviceomgeving die is geïsoleerd met front-end firewall aEcess.
- Een Azure Application Gateway-instantie maken en configureren met een firewall die gebruikmaakt van [OWASP Top 10 Ruleset](https://coreruleset.org/).
- Schakel versleuteling van gegevens in transit en in rust in met behulp van Azure-services.
- Het Azure-beleid en de blauwe afdrukken instellen om de nalevingen te evalueren

Nadat u deze app hebt ontwikkeld en geïmplementeerd, hebt u de volgende voorbeeldweb-app ingesteld, samen met de configuratie- en beveiligingsmaatregelen die worden beschreven.

## <a name="architecture-diagram-and-components"></a>Architectuurdiagram en -componenten
De app is een typische n-tier applicatie met drie lagen. De front-end, back-end en database laag met monitoring en secret-management componenten geïntegreerd worden hier weergegeven:

![App-architectuur](./media/secure-pci-web-app/architecture.png)

De architectuur bestaat uit deze componenten:

- [App-serviceomgeving v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Biedt de App Service Environment en load balancer voor onze applicatiearchitectuur.
- [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/). Biedt de gateway en firewall voor onze applicatiearchitectuur.
- [Toepassingsinzichten](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Biedt een uitbreidbare APM-service (Application Performance Management) op meerdere platforms.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). Slaat de geheimen van onze app op en versleutelt en beheert het maken van toegangsbeleid eromheen.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Biedt cloudgebaseerde identiteits- en toegangsbeheerservice, meld u aan en toegangsbronnen.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Biedt de service om het domein te hosten.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Schaalt uw applicaties en creëert een hoge beschikbaarheid voor uw diensten
- [Azure-opslag](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Biedt oplossing voor moderne scenario's voor gegevensopslag.
- [Azure Web App](https://docs.microsoft.com/azure/app-service/overview/).  Biedt een OP HTTP-gebaseerde service voor het hosten van webtoepassingen.
- [Azure Database voor AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). Slaat de gegevens van onze app veilig op.
- [Azure-blauwdrukken](https://docs.microsoft.com/azure/governance/blueprints/overview/). Biedt specificaties en naleving van bepaalde normen en eisen.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Azure-beleid](https://docs.microsoft.com/azure/governance/policy/overview). Evalueert uw resources op niet-naleving van toegewezen beleidsregels.

## <a name="threat-model"></a>Bedreigingsmodel
Bedreigingsmodellering is het proces van het identificeren van potentiële beveiligingsbedreigingen voor uw bedrijf en toepassing en vervolgens ervoor te zorgen dat er een goed mitigatieplan is.

In dit voorbeeld wordt de [Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) gebruikt om bedreigingsmodellering voor de beveiligde voorbeeld-app te implementeren. Door de componenten en de gegevensstromen in kaart te brengen, u problemen en bedreigingen al vroeg in het ontwikkelingsproces identificeren. Dit bespaart later tijd en geld.

Dit is het bedreigingsmodel voor de voorbeeld-app:

![Bedreigingsmodel](./media/secure-pci-web-app/threat-model.png)

Sommige voorbeeldbedreigingen en potentiële kwetsbaarheden die de hulpprogramma voor bedreigingsmodellering genereert, worden weergegeven in de volgende schermafbeelding. Het dreigingsmodel geeft een overzicht van het blootgestelde aanvalsoppervlak en vraagt de ontwikkelaars na te denken over hoe de problemen kunnen worden beperkt.

![Uitvoer van bedreigingsmodellen](./media/secure-web-app/threat-model-output.png)

Sql-injectie in de vorige uitvoer van het bedreigingsmodel wordt bijvoorbeeld beperkt door gebruikersinvoer te ontsmetten en opgeslagen functies in Azure Database voor PostgreSQL te gebruiken. Deze beperking voorkomt willekeurige uitvoering van query's tijdens het lezen en schrijven van gegevens.

Ontwikkelaars verbeteren de algehele beveiliging van het systeem door het beperken van elk van de bedreigingen in het bedreigingsmodel output.

## <a name="deployment"></a>Implementatie
### <a name="prerequisites"></a>Vereisten
Als u de toepassing operationeel wilt krijgen, moet u de volgende hulpprogramma's installeren:

- Een codeeditor om de toepassingscode te wijzigen en te bekijken. [Visual Studio Code](https://code.visualstudio.com/) is een open-source optie.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) op uw ontwikkelcomputer.
- [Git](https://git-scm.com/) op uw systeem. Git wordt gebruikt om de broncode lokaal te klonen.
- [jq](https://stedolan.github.io/jq/), een UNIX-tool voor het op een gebruiksvriendelijke manier opvragen van JSON.

Dit voorbeeld bestaat uit JSON-configuratiebestanden en PowerShell-scripts die worden beheerd door de API-service van Azure Resource Manager om resources binnen Azure te implementeren. Gedetailleerde implementatie-instructies zijn [hier](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)beschikbaar.

#### <a name="quickstart"></a>Snelstartgids

1.  Kloon of download [deze](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub repository naar uw lokale werkstation.
2.  Bekijk 0-Setup-AdministrativeAccountAndPermission.md en voer de meegeleverde opdrachten uit.
3.  Implementeer een testoplossing met Contoso-monstergegevens of pilot een initiële productieomgeving.

    Met dit script worden Azure-resources geïmplementeerd voor een demonstratie van een webwinkel met Behulp van Contoso-voorbeeldgegevens.

In dit voorbeeld voert u het implementatiescript uit waarmee de web-app wordt geïmplementeerd op App Service en de resources worden gemaakt.

Er zijn veel manieren om apps op Azure te implementeren, waaronder:

- Azure Resource Manager-sjablonen
- PowerShell
- Azure-CLI
- Azure Portal
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Richtlijnen en aanbevelingen

### <a name="network"></a>Netwerk
De architectuur definieert een privé virtueel netwerk met een adresruimte van 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Netwerkbeveiligingsgroepenhttps://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) bevatten Toegangscontrolelijsten (ACL's) die verkeer binnen een virtueel netwerk toestaan of weigeren. Netwerkbeveiligingsgroepen kunnen worden gebruikt om verkeer op subnet- of individueel VM-niveau te beveiligen. De volgende netwerkbeveiligingsgroepen bestaan:

- 1 netwerkbeveiligingsgroep voor Application Gateway
- 1 netwerkbeveiligingsgroep voor app-serviceomgeving
- 1 netwerkbeveiligingsgroep voor Azure SQL Database
- 1 netwerkbeveiligingsgroep voor bastionhost

Elk van de netwerkbeveiligingsgroepen heeft specifieke poorten en protocollen geopend, zodat de oplossing veilig en correct kan werken. Bovendien zijn de volgende configuraties ingeschakeld voor elke netwerkbeveiligingsgroep:

- Diagnostische logboekenhttps://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) en gebeurtenissen( zijn ingeschakeld en opgeslagen in een opslagaccount
- Azure Monitor-logboeken zijn verbonden met de diagnose van de netwerkbeveiligingsgroep.https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG Config
NSG config for App Service Environment moet worden geconfigureerd, zoals weergegeven in de afbeelding hieronder.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Elk subnet is gekoppeld aan de bijbehorende netwerkbeveiligingsgroep.

### <a name="config"></a>Configureren
Subnetten zijn geconfigureerd zoals weergegeven in de afbeelding hieronder.
 ![Configureren](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
Het Domain Name System (DNS) is verantwoordelijk voor het vertalen (of oplossen) van een website of servicenaam naar het IP-adres. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) is een hostingservice voor DNS-domeinen die naamomzetting biedt met behulp van Azure-infrastructuur. Door domeinen in Azure te hosten, kunnen gebruikers DNS-records beheren met dezelfde referenties, API's, hulpprogramma's en facturering als andere Azure-services. Azure DNS ondersteunt ook privé DNS-domeinen.

### <a name="protect-data"></a>Gegevens beveiligen
Om gegevens in de cloud te beschermen, moet u rekening houden met de mogelijke status waarin uw gegevens kunnen optreden en welke besturingselementen beschikbaar zijn voor die status. Aanbevolen procedures voor Azure-gegevensbeveiliging en -versleuteling hebben betrekking op de volgende gegevensstatussen:

- In rust: Dit omvat alle informatieopslagobjecten, containers en typen die statisch op fysieke media bestaan, of het nu magnetische of optische schijf is.
- Onderweg: wanneer gegevens worden overgedragen tussen onderdelen, locaties of programma's, is deze onderweg. Voorbeelden hiervan zijn overdracht via het netwerk, over een servicebus (van on-premises naar cloud en vice versa, inclusief hybride verbindingen zoals ExpressRoute), of tijdens een invoer-/uitvoerproces.

### <a name="azure-storage"></a>Azure Storage
Om te voldoen aan versleutelde gegevens tijdens rustvereisten, gebruikt [alle Azure Storage](https://azure.microsoft.com/services/storage/) Azure Key Vault om de controle te behouden over sleutels die toegang hebben tot en versleutelen van de gegevens. Dit helpt bij het beschermen en beveiligen van kaarthouders ter ondersteuning van organisatorische beveiligingsverplichtingen en nalevingsvereisten die zijn gedefinieerd door PCI DSS 3.2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption maakt gebruik van de BitLocker-functie van Windows om volumeversleuteling voor gegevensschijven te bieden. De oplossing integreert met Azure Key Vault om de schijfversleutelingssleutels te beheren en te beheren.

### <a name="azure-sql-database"></a>Azure SQL Database
De instantie Azure SQL Database gebruikt de volgende databasebeveiligingsmaatregelen:

- [Met Active Directory-verificatie en -autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) kan identiteitsbeheer van databasegebruikers en andere Microsoft-services op één centrale locatie worden beheerd.
- [SQL-databasecontrole](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) houdt databasegebeurtenissen bij en schrijft deze naar een controlelogboek in een Azure-opslagaccount.
- Azure SQL Database is geconfigureerd voor het gebruik van [transparante gegevensversleuteling,](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)die realtime versleuteling en decryptie van de database, bijbehorende back-ups en transactielogboekbestanden uitvoert om informatie in rust te beschermen. Transparante gegevensversleuteling biedt de zekerheid dat opgeslagen gegevens niet onbevoegd zijn geweest.
- [Firewallregels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhinderen alle toegang tot databaseservers totdat de juiste machtigingen zijn verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
- [SQL Threat Detection](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) maakt detectie en respons op potentiële bedreigingen mogelijk wanneer deze zich voordoen door beveiligingswaarschuwingen te verstrekken voor verdachte databaseactiviteiten, potentiële kwetsbaarheden, SQL-injectieaanvallen en afwijkende databasetoegangspatronen.
- [Versleutelde kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit worden weergegeven als plaintext in het databasesysteem. Na het inschakelen van gegevensversleuteling hebben alleen clienttoepassingen of toepassingsservers met toegang tot de sleutels toegang tot plaintext-gegevens.
- [SQL Database dynamic data masking](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) beperkt de blootstelling aan gevoelige gegevens door de gegevens te maskeren aan niet-bevoorrechte gebruikers of toepassingen. Dynamische gegevensmaskering kan automatisch potentieel gevoelige gegevens ontdekken en de juiste maskers voorstellen die moeten worden toegepast. Dit helpt om de toegang tot gegevens te identificeren en te beperken, zodat de database niet wordt afgesloten via ongeautoriseerde toegang. Klanten zijn verantwoordelijk voor het aanpassen van dynamische instellingen voor het maskeren van gegevens om zich aan hun databaseschema te houden.

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden om toegang tot kaarthoudergegevens in de Azure-omgeving te beheren:

- Azure Active Directory is de multi-tenant cloud- en identiteitsbeheerservice van Microsoft. Alle gebruikers voor deze oplossing worden gemaakt in Azure Active Directory, inclusief gebruikers die toegang hebben tot de Azure SQL Database.
- Verificatie naar de toepassing wordt uitgevoerd met Azure Active Directory. Raadpleeg [Toepassingen integreren met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration) voor meer informatie. Bovendien gebruikt de versleuteling van de databasekolom Azure Active Directory om de toepassing te verifiëren voor Azure SQL Database. Zie voor meer informatie [hoe u gevoelige gegevens in Azure SQL Database beveiligen.](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)
- Azure-toegangsbeheer op basis van azure-rollen stelt beheerders in staat om fijnmazige toegangsmachtigingen te definiëren om alleen de hoeveelheid toegang te verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte toestemming te geven voor Azure-bronnen, kunnen beheerders alleen bepaalde acties toestaan voor toegang tot kaarthoudergegevens. Abonnementstoegang is beperkt tot de abonnementsbeheerder.
- Azure Active Directory Privileged Identity Management stelt klanten in staat om het aantal gebruikers dat toegang heeft tot bepaalde informatie, zoals kaarthoudergegevens, te minimaliseren. Beheerders kunnen Azure Active Directory Privileged Identity Management gebruiken om bevoorrechte identiteiten en hun toegang tot bronnen te ontdekken, te beperken en te controleren. Deze functionaliteit kan ook worden gebruikt om on-demand, just-in-time administratieve toegang af te dwingen wanneer dat nodig is.
- Azure Active Directory Identity Protection detecteert potentiële kwetsbaarheden die de identiteit van een organisatie beïnvloeden, configureert geautomatiseerde reacties op gedetecteerde verdachte acties met betrekking tot de identiteit van een organisatie en onderzoekt verdachte incidenten om passende maatregelen te nemen om deze op te lossen.

### <a name="secrets-management"></a>Geheimen beheer
De oplossing maakt gebruik van Azure Key Vault voor het beheer van sleutels en geheimen. Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met de volgende Azure Key Vault-mogelijkheden kunnen klanten dergelijke gegevens beveiligen en openen:

- Geavanceerde toegangsbeleidsregels worden geconfigureerd op basis van behoefte.
- Key Vault-toegangsbeleid wordt gedefinieerd met minimaal vereiste machtigingen voor sleutels en geheimen.
- Alle sleutels en geheimen in Key Vault hebben vervaldatums.
- Alle sleutels in Key Vault worden beschermd door gespecialiseerde hardwarebeveiligingsmodules. Het belangrijkste type is een HSM Protected 2048-bit RSA Key.
- Met Key Vault u sleutels en geheimen versleutelen (zoals verificatiesleutels, opslagaccountsleutels, gegevensversleutelingssleutels,. PFX-bestanden en wachtwoorden met behulp van sleutels die worden beschermd door hardwarebeveiligingsmodules (HSM's)
- Gebruik RBAC om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen op een bepaald bereik.
- Gebruik Key Vault om uw TLS-certificaten te beheren met automatische verlenging.
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een bewaartermijn van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereiste bewerkingen.

### <a name="azure-security-center"></a>Azure Security Center
Met Azure Security Center kunnen klanten beveiligingsbeleid centraal toepassen en beheren voor workloads, de blootstelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Daarnaast heeft Azure Security Center toegang tot bestaande configuraties van Azure-services om configuratie- en serviceaanbevelingen te bieden om de beveiligingshouding te verbeteren en gegevens te beschermen.

Azure Security Center maakt gebruik van verschillende detectiemogelijkheden om klanten te waarschuwen voor mogelijke aanvallen op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Azure Security Center heeft een reeks vooraf gedefinieerde beveiligingswaarschuwingen, die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Met aangepaste waarschuwingsregels in Azure Security Center kunnen klanten nieuwe beveiligingswaarschuwingen definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

Azure Security Center biedt geprioriteerd beveiligingswaarschuwingen en -incidenten, waardoor het voor klanten eenvoudiger wordt om potentiële beveiligingsproblemen te ontdekken en aan te pakken. Een threat intelligence rapport wordt gegenereerd voor elke gedetecteerde bedreiging om incident response teams te helpen bij het onderzoeken en herstellen van bedreigingen.

### <a name="azure-application-gateway"></a>Azure Application Gateway
De architectuur vermindert het risico op beveiligingsproblemen met behulp van een Azure Application Gateway met een firewall voor webtoepassingen geconfigureerd en de OWASP-regelset is ingeschakeld. Aanvullende mogelijkheden zijn:

- End-to-end-SSL
- TLS v1.0 en v1.1 uitschakelen
- TLSv1.2 inschakelen
- Firewall voor webtoepassingen (preventiemodus)
- Preventiemodus met OWASP 3.0-regelset
- Diagnostische logboekregistratie inschakelen
- Aangepaste statussondes
- Azure Security Center en Azure Advisor bieden extra bescherming en meldingen. Azure Security Center biedt ook een reputatiesysteem.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking
Azure-services registreren op grote schaal systeem- en gebruikersactiviteit, evenals systeemstatus:

- [Activiteitslogboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) geven inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteitslogboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van het optreden en de status van een bewerking.
- [Diagnostische logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) bevatten alle logboeken die door elke resource worden uitgestraald. Deze logboeken omvatten Windows-gebeurtenissysteemlogboeken, Azure Storage-logboeken, Key Vault-controlelogboeken en toepassingsgatewaytoegang en firewalllogboeken. Alle diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure-opslagaccount voor archivering. De retentie is tot 730 dagen gebruikersconfigureerbaar om te voldoen aan organisatiespecifieke bewaarvereisten.

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
Deze logboeken worden geconsolideerd in [Azure Monitor-logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboardrapportage. Eenmaal verzameld, worden de gegevens ingedeeld in afzonderlijke tabellen voor elk gegevenstype binnen Log Analytics-werkruimten, waarmee alle gegevens samen kunnen worden geanalyseerd, ongeacht de oorspronkelijke bron. Bovendien integreert Azure Security Center met Azure Monitor-logboeken waarmee klanten Kusto-query's kunnen gebruiken om toegang te krijgen tot hun beveiligingsgebeurtenisgegevens en deze te combineren met gegevens van andere services.

De volgende [Azure-bewakingsoplossingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) zijn opgenomen als onderdeel van deze architectuur:

- [Active Directory Assessment](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): De Active Directory Health Check-oplossing beoordeelt het risico en de status van serveromgevingen op een regelmatig interval en biedt een geprioritteerde lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde serverinfrastructuur.
- [SQL-beoordeling](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): De SQL Health Check-oplossing beoordeelt het risico en de status van serveromgevingen op een regelmatig interval en biedt klanten een geprioriteerd lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde serverinfrastructuur.
- [Agentgezondheid:](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)De oplossing agentstatus rapporteert hoeveel agents worden geïmplementeerd en hun geografische distributie, evenals hoeveel agents niet reageren en het aantal agents dat operationele gegevens indient.
- [Activiteitslogboekanalyse:](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)De oplossing Activity Log Analytics helpt bij het analyseren van de Azure-activiteitslogboeken voor alle Azure-abonnementen voor een klant.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers prestaties bij te houden, de beveiliging te onderhouden en trends te identificeren door organisaties in staat te stellen te controleren, waarschuwingen te maken en gegevens te archiveren, waaronder het bijhouden van API-aanroepen in hun Azure-bronnen.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) is een uitbreidbare Application Performance Management-service voor webontwikkelaars op meerdere platforms. Application Insights detecteert prestatieafwijkingen en klanten kunnen deze gebruiken om de live webapplicatie te monitoren. Het bevat krachtige analysetools om klanten te helpen problemen te diagnosticeren en te begrijpen wat gebruikers daadwerkelijk met hun app doen. Het is ontworpen om klanten te helpen de prestaties en bruikbaarheid voortdurend te verbeteren.

### <a name="azure-key-vault"></a>Azure Key Vault
Maak een kluis voor de organisatie waarin sleutels kunnen worden opgeslagen en houd de verantwoordelijkheid voor operationele taken zoals hieronder:

- Gegevens die zijn opgeslagen in Key Vault zijn onder andere:

   - Toepassingsinzichtsleutel
   - Toegangssleutel voor gegevensopslag
   - Verbindingsreeks
   - Naam van gegevenstabel
   - Gebruikersreferenties

- Geavanceerd toegangsbeleid is geconfigureerd op basis van behoefte
- Key Vault-toegangsbeleid wordt gedefinieerd met minimaal vereiste machtigingen voor sleutels en geheimen
- Alle sleutels en geheimen in Key Vault hebben vervaldatums
- Alle toetsen in Key Vault worden beschermd door HSM [Sleuteltype = HSM Protected 2048-bit RSA-toets]
- Alle gebruikers/identiteiten krijgen minimaal vereiste machtigingen met behulp van RBAC (Role-Based Access Control)
- Toepassingen delen geen Key Vault tenzij ze elkaar vertrouwen en ze toegang nodig hebben tot dezelfde geheimen tijdens runtime
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een bewaartermijn van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereiste bewerkingen

### <a name="vpn-and-expressroute"></a>VPN en ExpressRoute
Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om veilig een verbinding tot stand te brengen met de resources die worden geïmplementeerd als onderdeel van deze Referentiearchitectuur voor PaaS-webtoepassingen. Door een VPN of ExpressRoute op de juiste manier in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Door een beveiligde VPN-tunnel met Azure te implementeren, kan een virtuele privéverbinding worden gemaakt tussen een on-premises netwerk en een Azure Virtual Network. Deze verbinding vindt plaats via het internet en stelt klanten in staat om veilig "tunnel" informatie binnen een versleutelde verbinding tussen het netwerk van de klant en Azure. Site-to-Site VPN is een veilige, volwassen technologie die al tientallen jaren wordt geïmplementeerd door ondernemingen van elke omvang. De IPsec-tunnelmodus wordt in deze optie gebruikt als versleutelingsmechanisme.

Omdat verkeer binnen de VPN-tunnel het internet doorkruist met een site-to-site VPN, biedt Microsoft een andere, nog veiligere verbindingsoptie. Azure ExpressRoute is een speciale WAN-koppeling tussen Azure en een on-premises locatie of een Exchange-hostingprovider. Aangezien ExpressRoute-verbindingen niet via internet verlopen, bieden deze verbindingen meer betrouwbaarheid, hogere snelheden, lagere latencies en hogere beveiliging dan typische verbindingen via internet. Aangezien dit een directe verbinding van de telecomprovider van de klant is, reizen de gegevens bovendien niet via internet en worden ze er dus niet aan blootgesteld.

Aanbevolen procedures voor het implementeren van een beveiligd hybride netwerk dat een on-premises netwerk uitbreidt naar Azure zijn [beschikbaar.](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)

## <a name="cost-considerations"></a>Kostenoverwegingen
Als u nog geen Azure-account hebt, u een gratis account maken. Ga naar de [gratis accountpagina](https://azure.microsoft.com/free/) om aan de slag te gaan, zie wat u doen met een gratis Azure-account en ontdek welke producten 12 maanden gratis zijn.

Als u de resources in de voorbeeld-app wilt implementeren met de beveiligingsfuncties, moet u betalen voor een aantal premiumfuncties. Aangezien de app schaalt en de gratis lagen en proefversies die door Azure worden aangeboden, moeten worden geüpgraded om te voldoen aan de vereisten voor toepassingen, kunnen uw kosten stijgen. Gebruik de [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/) om uw kosten te schatten.

## <a name="next-steps"></a>Volgende stappen
De volgende artikelen kunnen u helpen bij het ontwerpen, ontwikkelen en implementeren van beveiligde toepassingen.

- [Ontwerp](secure-design.md)
- [Ontwikkelen](secure-develop.md)
- [Implementeren](secure-deploy.md)

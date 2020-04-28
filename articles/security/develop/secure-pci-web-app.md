---
title: Beveiligde web-app voor PCI DSS | Microsoft Docs
description: Deze voor beeld-app implementeert aanbevolen beveiligings procedures voor het verbeteren van uw toepassing en de beveiligings postuur van uw organisatie bij het ontwikkelen op Azure.
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
ms.openlocfilehash: 4ab3697824ff4a47e7b8f281b531cae610ffdc3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187576"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>Een beveiligde infra structuur ontwikkelen voor een PCI-app

## <a name="overview"></a>Overzicht

Deze beveiligde infra structuur voor een PCI-app (Payment Card Industry) biedt richt lijnen voor de implementatie van een PaaS-omgeving (Payment Card Industry platform as a Service) die geschikt is voor het verzamelen, opslaan en ophalen van gegevens van de kaart houder. Met deze oplossing automatiseert u de implementatie en configuratie van Azure-resources voor een algemene referentie architectuur, waarbij u kunt zien hoe klanten aan specifieke vereisten voor beveiliging en naleving voldoen en als basis voor klanten worden gebruikt om hun eigen oplossingen op Azure te bouwen en te configureren. De oplossing implementeert een subset van vereisten die vergelijkbaar zijn met de beveiliging van de gegevens van de betalings kaart (PCI DSS 3,2).

In dit voor beeld wordt automatisch een referentie architectuur voor een PaaS-webtoepassing geïmplementeerd met vooraf geconfigureerde beveiligings controles om ervoor te zorgen dat klanten voldoen aan de naleving van PCI DSS 3,2-vereisten. De oplossing bestaat uit Azure Resource Manager sjablonen en Power shell-scripts die de implementatie en configuratie van resources begeleiden.

Volg de stappen in dit artikel sequentieel om te controleren of de toepassings onderdelen correct zijn geconfigureerd. De data base, Azure App Service, Azure Key Vault instantie en Azure-toepassing gateway-exemplaar, zijn afhankelijk van elkaar.

De implementatie scripts instellen de infra structuur. Nadat u de implementatie scripts hebt uitgevoerd, moet u een aantal hand matige configuratie in het Azure Portal uitvoeren om de onderdelen en services samen te koppelen.

Dit voor beeld is gericht op ervaren ontwikkel aars in azure die in de zakelijke branche werken en een retail-app willen bouwen met een veilige Azure-infra structuur.

> [!NOTE]
> Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.

Het implementeren van een toepassing in deze omgeving zonder wijziging is niet voldoende om volledig te voldoen aan de vereisten van PCI DSS 3,2. Houd rekening met het volgende:

- Deze architectuur biedt een basis lijn om klanten te helpen bij het gebruik van Azure in een PCI DSS 3,2-bestendige manier.
- Klanten zijn verantwoordelijk voor het uitvoeren van de juiste beveiligings-en compatibiliteits beoordeling van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

Bij het ontwikkelen en implementeren van deze app leert u het volgende:

- Maak een Azure Key Vault-exemplaar en sla het op van de geheimen.
- Azure Data Base voor Azure SQL implementeren, beveiligde gegevens instellen en toegang verlenen.
- Implementeer de Azure-web-app met App Service omgeving die speciaal is geïsoleerd met toegang voor de front-end-firewall.
- Een Azure-toepassing gateway-exemplaar maken en configureren met een firewall die gebruikmaakt van de [Rule OWASP Top 10](https://coreruleset.org/).
- Versleuteling van gegevens in door Voer en op rest inschakelen met behulp van Azure-Services.
- Stel Azure Policy en Azure-blauw drukken in om de naleving te evalueren.

Nadat u deze app hebt ontwikkeld en geïmplementeerd, hebt u de volgende voor beeld-web-app ingesteld, samen met de configuratie-en beveiligings maatregelen die worden beschreven.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen
De app is een typische toepassing met n-tier met drie lagen. De front-end, de back-end en de data base-laag met de bewakings-en geheim beheer onderdelen die zijn geïntegreerd, worden hier weer gegeven:

![App-architectuur](./media/secure-pci-web-app/architecture.png)

De architectuur bestaat uit de volgende onderdelen:

- [App service Environment v2](https://docs.microsoft.com/azure/app-service/environment/intro/). Biedt de App Service Environment en load balancer voor onze toepassings architectuur.
- [Azure-toepassing gateway](https://docs.microsoft.com/azure/application-gateway/). Biedt de gateway en Firewall voor onze toepassings architectuur.
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Biedt een uitbreid bare service voor het beheer van toepassings prestaties op meerdere platforms.
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/). De geheimen van onze app worden opgeslagen en versleuteld en het maken van toegangs beleid wordt beheerd.
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/). Biedt service voor identiteits-en toegangs beheer op basis van de Cloud, Meld u aan en toegang tot bronnen.
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview). Biedt de service om het domein te hosten.
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/). Schaalt uw toepassingen en maakt hoge Beschik baarheid voor uw services
- [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction/). Biedt een oplossing voor moderne scenario's voor gegevens opslag.
- [Azure-web-app](https://docs.microsoft.com/azure/app-service/overview/).  Voorziet in een HTTP-gebaseerde service voor het hosten van webtoepassingen.
- [Azure data base for SQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/). De gegevens van onze app veilig opslaan.
- [Azure-blauw drukken](https://docs.microsoft.com/azure/governance/blueprints/overview/). Voorziet in specificaties en naleving van bepaalde normen en vereisten.
- [Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview). Evalueert uw resources op niet-naleving met toegewezen beleids regels.

## <a name="threat-model"></a>Bedreigings model
Bedreigings modellering is het proces van het identificeren van mogelijke beveiligings Risico's voor uw bedrijf en toepassing en zorgt ervoor dat er een goed risico op de juiste wijze wordt uitgevoerd.

In dit voor beeld wordt de [Microsoft Threat Modeling tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) gebruikt voor het implementeren van bedreigings modellen voor de beveiligde voor beeld-app. Door de componenten en de gegevens stromen te ontwikkelen, kunt u problemen en bedreigingen vroegtijdig in het ontwikkelings proces identificeren. Dit bespaart tijd en geld later.

Dit is het bedreigings model voor de voor beeld-app:

![Bedreigings model](./media/secure-pci-web-app/threat-model.png)

Enkele voor beelden van bedreigingen en mogelijke beveiligings problemen die het hulp programma voor het maken van dreigingen worden gegenereerd, worden weer gegeven in de volgende scherm afbeelding. Het bedreigings model biedt een overzicht van de beschik bare kwets baarheid en vraagt de ontwikkel aars om te zien hoe ze de problemen kunnen oplossen.

![Bedreigings model uitvoer](./media/secure-web-app/threat-model-output.png)

SQL-injectie in de voor gaande bedreigings model uitvoer wordt bijvoorbeeld verholpen door het opschonen van gebruikers invoer en het gebruik van opgeslagen functies in Azure Database for PostgreSQL. Deze oplossing voor komt een wille keurige uitvoering van query's tijdens het lezen en schrijven van gegevens.

Ontwikkel aars verbeteren de algehele beveiliging van het systeem door elk van de bedreigingen in de uitvoer van het bedreigings model te verminderen.

## <a name="deployment"></a>Implementatie
### <a name="prerequisites"></a>Vereisten
Als u de toepassing wilt uitvoeren, moet u deze hulpprogram ma's installeren:

- Een code-editor om de toepassings code te wijzigen en weer te geven. [Visual Studio code](https://code.visualstudio.com/) is een open-source optie.
- [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) op uw ontwikkel computer.
- [Git](https://git-scm.com/) op uw systeem. Git wordt gebruikt om de bron code lokaal te klonen.
- [JQ](https://stedolan.github.io/jq/), een UNIX-hulp programma voor het uitvoeren van QUERY'S in JSON op een gebruiks vriendelijke manier.

Dit voor beeld bestaat uit JSON-configuratie bestanden en Power shell-scripts die worden verwerkt door de API-service van Azure Resource Manager om resources te implementeren in Azure. Gedetailleerde implementatie-instructies zijn [hier](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM)beschikbaar.

#### <a name="quickstart"></a>Snelstartgids

1.  Kloon of down load [deze](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) github-opslag plaats naar uw lokale werk station.
2.  Bekijk 0-Setup-AdministrativeAccountAndPermission.md en voer de meegeleverde opdrachten uit.
3.  Implementeer een test oplossing met de voorbeeld gegevens van Contoso of test een eerste productie omgeving.

    Met dit script worden Azure-resources geïmplementeerd voor een demonstratie van een webstore met behulp van contoso-voorbeeld gegevens.

In dit voor beeld voert u het implementatie script uit dat de web-app implementeert op App Service en maakt u de resources.

Er zijn veel manieren om apps te implementeren op Azure, waaronder:

- Azure Resource Manager-sjablonen
- PowerShell
- Azure CLI
- Azure Portal
- Azure DevOps

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="network"></a>Netwerk
De architectuur definieert een persoonlijke Virtual Network met een adres ruimte van 10.200.0.0/16.
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen
Netwerk beveiligings groepen (https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) bevatten Access Control-lijsten (acl's) waarmee verkeer binnen een virtueel netwerk wordt toegestaan of geweigerd. Netwerk beveiligings groepen kunnen worden gebruikt voor het beveiligen van verkeer op een subnet of een afzonderlijk VM-niveau. De volgende netwerk beveiligings groepen bestaan:

- 1 netwerk beveiligings groep voor Application Gateway
- 1 netwerk beveiligings groep voor App Service Environment
- 1 netwerk beveiligings groep voor Azure SQL Database
- 1 netwerk beveiligings groep voor Bastion-host

Voor elk van de netwerk beveiligings groepen zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke netwerk beveiligings groep:

- Diagnostische logboeken enhttps://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) gebeurtenissen (worden ingeschakeld en opgeslagen in een opslag account
- Azure Monitor-logboeken zijn verbonden met de diagnostische gegevens van de netwerk beveiligings groep (https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG-configuratie
NSG config voor App Service Environment moet worden geconfigureerd zoals wordt weer gegeven in de onderstaande afbeelding.

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

Elk subnet is gekoppeld aan de bijbehorende netwerk beveiligings groep.

### <a name="config"></a>Configureren
Subnetten worden geconfigureerd zoals wordt weer gegeven in de onderstaande afbeelding.
 ![Configureren](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
De Domain Name System (DNS) is verantwoordelijk voor het vertalen van een website-of service naam naar het IP-adres. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) is een hosting service voor DNS-domeinen die naam omzetting biedt met behulp van Azure-infra structuur. Door domeinen in azure te hosten, kunnen gebruikers DNS-records beheren met dezelfde referenties, Api's, hulpprogram ma's en facturering als andere Azure-Services. Azure DNS biedt ook ondersteuning voor privé-DNS-domeinen.

### <a name="protect-data"></a>Gegevens beveiligen
Als u gegevens in de Cloud wilt beveiligen, moet u rekening houden met de mogelijke statussen waarin uw gegevens zich kunnen voordoen en welke besturings elementen beschikbaar zijn voor die status. Aanbevolen procedures voor Azure-gegevens beveiliging en-versleuteling hebben betrekking op de volgende gegevens statussen:

- In rust: Dit omvat alle opslag objecten, containers en typen van informatie die statisch aanwezig zijn op fysieke media, hetzij magnetische als optische schijf.
- Onderweg: wanneer gegevens worden overgedragen tussen onderdelen, locaties of Program ma's, is het onderweg. Voor beelden zijn overdracht via het netwerk, over een service bus (van on-premises naar de Cloud en vice versa, waaronder hybride verbindingen zoals ExpressRoute) of tijdens een invoer-en uitvoer proces.

### <a name="azure-storage"></a>Azure Storage
Om te voldoen aan de versleutelde gegevens op rest-vereisten, gebruikt alle [Azure Storage](https://azure.microsoft.com/services/storage/) Azure Key Vault om de controle te houden over de sleutels die de gegevens openen en versleutelen. Dit helpt de gegevens van de kaart houder te beschermen en te beschermen ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd door PCI DSS 3,2.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

### <a name="azure-sql-database"></a>Azure SQL Database
Het Azure SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:

- Met [Active Directory verificatie en autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) kan identiteits beheer van database gebruikers en andere micro soft-Services op één centrale locatie worden beheerd.
- Met [SQL database controle](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
- Azure SQL Database is geconfigureerd voor het gebruik van [transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), waarmee in realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden worden uitgevoerd om informatie te beveiligen. Transparent Data Encryption biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
- [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
- Met de [detectie van SQL-bedreigingen](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started) kunnen mogelijke dreigingen worden gedetecteerd en gereageerd als ze optreden door beveiligings waarschuwingen te bieden voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende database toegangs patronen.
- [Versleutelde kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Na het inschakelen van gegevens versleuteling, hebben alleen client toepassingen of toepassings servers met toegang tot de sleutels toegang tot tekst zonder opmaak.
- [SQL database dynamische gegevens maskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) beperkt de bloot stelling van gevoelige gegevens door de gegevens te maskeren voor niet-gemachtigde gebruikers of toepassingen. Met dynamische gegevens maskering kunnen mogelijk gevoelige gegevens automatisch worden gedetecteerd en worden de juiste maskers voorgesteld om te worden toegepast. Zo kunt u de toegang tot gegevens identificeren en verminderen, zodat de data base niet wordt afgesloten via onbevoegde toegang. Klanten zijn verantwoordelijk voor het aanpassen van instellingen voor dynamische gegevens maskering om te voldoen aan hun database schema.

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden voor het beheren van toegang tot gegevens van de kaart houder in de Azure-omgeving:

- Azure Active Directory is de multi tenant-Cloud Directory en identiteits beheer service van micro soft. Alle gebruikers voor deze oplossing worden gemaakt in Azure Active Directory, met inbegrip van gebruikers die toegang hebben tot de Azure SQL Database.
- Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure Active Directory. Raadpleeg [Toepassingen integreren met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration) voor meer informatie. Daarnaast maakt de database kolom versleuteling gebruik van Azure Active Directory om de toepassing te verifiëren voor Azure SQL Database. Zie voor meer informatie [hoe u gevoelige gegevens in Azure SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Met op rollen gebaseerd toegangs beheer van Azure kunnen beheerders verfijnde toegangs machtigingen definiëren om alleen de hoeveelheid toegang te verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte machtiging voor Azure-resources geven, kunnen beheerders alleen bepaalde acties toestaan om toegang te krijgen tot gegevens van de kaart houder. Abonnements toegang is beperkt tot de abonnements beheerder.
- Met Azure Active Directory Privileged Identity Management kunnen klanten het aantal gebruikers dat toegang heeft tot bepaalde gegevens, zoals gegevens van de kaart houder, minimaliseren. Beheerders kunnen Azure Active Directory Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag als dat nodig is.
- Azure Active Directory Identity Protection detecteert potentiële beveiligings problemen die van invloed zijn op de identiteit van een organisatie, configureert automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteit van een organisatie en onderzoekt verdachte incidenten om deze te verhelpen.

### <a name="secrets-management"></a>Geheimen beheren
De oplossing maakt gebruik van Azure Key Vault voor het beheer van sleutels en geheimen. Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met de volgende Azure Key Vault mogelijkheden kunnen klanten dergelijke gegevens beveiligen en benaderen:

- Geavanceerd toegangs beleid wordt geconfigureerd op basis van behoefte.
- Key Vault toegangs beleid wordt gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen.
- Alle sleutels en geheimen in Key Vault hebben verloop datums.
- Alle sleutels in Key Vault worden beveiligd door gespecialiseerde hardware security modules. Het sleutel type is een met HSM beschermde 2048-bits RSA-sleutel.
- Met Key Vault kunt u sleutels en geheimen versleutelen (zoals verificatie sleutels, sleutels voor opslag accounts, sleutels voor gegevens versleuteling,. PFX-bestanden en wacht woorden) met behulp van sleutels die worden beveiligd met Hardware Security modules (Hsm's)
- Gebruik RBAC om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen bij een bepaald bereik.
- Gebruik Key Vault om uw TLS-certificaten te beheren met automatische verlenging.
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn.

### <a name="azure-security-center"></a>Azure Security Center
Met Azure Security Center kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Daarnaast Azure Security Center de bestaande configuraties van Azure-Services openen om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beveiligen van gegevens.

Azure Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die zijn gericht op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Azure Security Center heeft een reeks vooraf gedefinieerde beveiligings waarschuwingen, die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Met aangepaste waarschuwings regels in Azure Security Center kunnen klanten nieuwe beveiligings waarschuwingen definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

Azure Security Center biedt beveiligings waarschuwingen en-incidenten met prioriteit, waardoor klanten eenvoudiger mogelijke beveiligings problemen kunnen detecteren en oplossen. Er wordt een Threat Intelligence-rapport voor elke gedetecteerde bedreiging gegenereerd om de incidenten te helpen bij het onderzoeken en oplossen van bedreigingen.

### <a name="azure-application-gateway"></a>Azure Application Gateway
De architectuur vermindert het risico op beveiligings problemen met behulp van een Azure-toepassing gateway waarvoor een Web Application Firewall is geconfigureerd en de ruleset OWASP ingeschakeld. Aanvullende mogelijkheden zijn onder andere:

- End-to-end-SSL
- TLS v 1.0 en v 1.1 uitschakelen
- TLSv 1.2 inschakelen
- Web Application firewall (preventie modus)
- Preventie modus met OWASP 3,0 ruleSet
- Diagnostische logboekregistratie inschakelen
- Aangepaste status tests
- Azure Security Center en Azure Advisor bieden extra beveiliging en meldingen. Azure Security Center biedt ook een reputatie systeem.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking
Azure-Services registreren systeem-en gebruikers activiteiten uitvoerig, evenals systeem status:

- [Activiteiten logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- [Diagnostische logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, Azure Storage logboeken, Key Vault controle logboeken en Application Gateway toegang en firewall Logboeken. Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. De retentie kan door de gebruiker worden geconfigureerd, tot 730 dagen, om te voldoen aan de specifieke vereisten voor het bewaren van een organisatie.

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken
Deze logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboard rapportage. Zodra de gegevens zijn verzameld, worden deze in verschillende tabellen ingedeeld voor elk gegevens type binnen Log Analytics werk ruimten, waardoor alle gegevens kunnen worden geanalyseerd, ongeacht de oorspronkelijke bron. Bovendien kan Azure Security Center worden geïntegreerd met Azure Monitor-logboeken, zodat klanten Kusto-query's kunnen gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

De volgende Azure- [bewakings oplossingen](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) zijn opgenomen als onderdeel van deze architectuur:

- [Active Directory-evaluatie](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment): de Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [SQL-evaluatie](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment): de SQL Health Check-oplossing evalueert het risico en de status van server omgevingen met een regel matig interval en biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [Status van agent](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth): de status van agent oplossing rapporteert hoeveel agents zijn geïmplementeerd en wat hun geografische distributie zijn, evenals hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
- [Analyse van activiteitenlogboek](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity): de analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

### <a name="azure-monitor"></a>Azure Monitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends door organisaties in te scha kelen, waarschuwingen te maken en gegevens te archiveren, met inbegrip van tracking-API-aanroepen in hun Azure-resources.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) is een uitbreid bare service voor het beheer van toepassings prestaties voor webontwikkelaars op meerdere platforms. Application Insights detecteert prestatie afwijkingen en klanten kunnen deze gebruiken om de Live webtoepassing te bewaken. Het bevat krachtige analyse tools om klanten te helpen bij het onderzoeken van problemen en om te begrijpen wat gebruikers daad werkelijk doen met hun app. Het is ontworpen om klanten te helpen de prestaties en bruikbaarheid continu te verbeteren.

### <a name="azure-key-vault"></a>Azure Key Vault
Maak een kluis voor de organisatie waarin sleutels moeten worden opgeslagen en behoud de verantwoordelijkheid voor operationele taken zoals hieronder:

- De gegevens die zijn opgeslagen in Key Vault omvatten:

   - Toepassings Insight-sleutel
   - Toegangs sleutel voor gegevens opslag
   - Verbindingsreeks
   - Naam gegevens tabel
   - Gebruikers referenties

- Geavanceerd toegangs beleid wordt op basis van behoefte geconfigureerd
- Key Vault toegangs beleid is gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen
- Alle sleutels en geheimen in Key Vault verloop datums hebben
- Alle sleutels in Key Vault worden beveiligd door HSM [Key Type = HSM Protected 2048-bits RSA key]
- Aan alle gebruikers/identiteiten worden mini maal vereiste machtigingen verleend met behulp van op rollen gebaseerde Access Control (RBAC)
- Toepassingen delen een Key Vault alleen als ze elkaar vertrouwen en toegang hebben tot dezelfde geheimen tijdens runtime
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn

### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute
Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze PaaS-referentie architectuur voor webtoepassingen. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Door een beveiligde VPN-tunnel te implementeren met Azure, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een Azure-Virtual Network worden gemaakt. Deze verbinding vindt plaats via internet en stelt klanten in staat om ' tunnel-informatie veilig ' te maken binnen een versleutelde koppeling tussen het netwerk van de klant en Azure. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De IPsec-tunnel modus wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer binnen de VPN-tunnel via internet met een site-naar-site-VPN gaat, biedt micro soft een andere, nog veiliger verbindings optie. Azure ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Omdat dit een directe verbinding is tussen de telecom municatie-provider van de klant, worden de gegevens niet via internet verzonden en worden ze daarom niet blootgesteld aan IT.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="cost-considerations"></a>Kostenoverwegingen
Als u nog geen Azure-account hebt, kunt u een gratis versie maken. Ga naar de [pagina gratis account](https://azure.microsoft.com/free/) om aan de slag te gaan. Zie wat u kunt doen met een gratis Azure-account en ontdek welke producten gedurende 12 maanden gratis zijn.

Als u de resources in de voor beeld-app wilt implementeren met behulp van de beveiligings functies, moet u betalen voor een aantal Premium-functies. Wanneer de app wordt geschaald en de gratis lagen en tests die door Azure worden aangeboden, moeten worden bijgewerkt om te voldoen aan de toepassings vereisten, kunnen uw kosten toenemen. Gebruik de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) van Azure om uw kosten te schatten.

## <a name="next-steps"></a>Volgende stappen
De volgende artikelen kunnen u helpen bij het ontwerpen, ontwikkelen en implementeren van beveiligde toepassingen.

- [Ontwerpen](secure-design.md)
- [Ontwikkelen](secure-develop.md)
- [Implementeren](secure-deploy.md)

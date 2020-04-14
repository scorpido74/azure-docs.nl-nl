---
title: Bescherming tegen bedreiging in Azure Security Center
description: In dit onderwerp worden de bronnen beschreven die worden beschermd door de functies voor bedreigingsbeveiliging van Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: fdf22e4d981549b876a14aed2b0a1d7e0c76e40e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263451"
---
# <a name="threat-protection-in-azure-security-center"></a>Bescherming tegen bedreiging in Azure Security Center

Wanneer Security Center een bedreiging detecteert in een gebied van uw omgeving, genereert het een waarschuwing. Deze waarschuwingen beschrijven details van de getroffen resources, voorgestelde herstelstappen en in sommige gevallen een optie om een logische app als antwoord te activeren.

De bescherming tegen bedreigingen van Azure Security Center biedt uitgebreide beveiligingen voor uw omgeving:

* **Bescherming tegen bedreigingen voor Azure-gegevensbronnen:** Windows-machines, Linux-machines, Azure App Service en Azure-containers

* **Bescherming van bedreigingen voor Azure-gegevensbronnen:** SQL Database en SQL Data Warehouse, Azure Storage en Azure Cosmos DB

* **Bescherming van bedreigingen voor Azure-servicelagen:** Azure-netwerklaag, Azure-beheerlaag (Azure Resource Manager) (Voorbeeld) en Azure Key Vault (voorbeeld)

Of een waarschuwing wordt gegenereerd door Security Center of door Security Center wordt ontvangen van een ander beveiligingsproduct, u deze exporteren. Als u uw waarschuwingen wilt exporteren naar Azure Sentinel (of een SIEM van derden) of een ander extern hulpmiddel, volgt u de instructies voor [het exporteren van waarschuwingen naar een SIEM.](continuous-export.md) 




## <a name="threat-protection-for-windows-machines"></a>Bescherming tegen bedreigingen voor Windows-machines<a name="windows-machines"></a>

Azure Security Center integreert met Azure-services om uw Windows-apparaten te bewaken en te beschermen. Security Center presenteert de waarschuwingen en herstelsuggesties van al deze services in een gebruiksvriendelijke indeling.

* **Microsoft Defender ATP** <a name="windows-atp"></a> - Security Center breidt zijn cloudworkloadbeveiligingsplatforms uit door te integreren met Microsoft Defender Advanced Threat Protection (ATP). Samen bieden ze uitgebreide endpoint detection and response (EDR) mogelijkheden.

    > [!IMPORTANT]
    > De Microsoft Defender ATP-sensor is automatisch ingeschakeld op Windows-servers die gebruikmaken van Security Center.

    Wanneer Microsoft Defender ATP een bedreiging detecteert, wordt een waarschuwing geactiveerd. De waarschuwing wordt weergegeven op het dashboard van het Beveiligingscentrum. Vanuit het dashboard u draaien naar de Microsoft Defender ATP-console en een gedetailleerd onderzoek uitvoeren om de omvang van de aanval te achterhalen. Zie [Onboard-servers voor de Microsoft Defender ATP-service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)voor meer informatie over Microsoft Defender ATP.

* **Crash dump analyse** <a name="windows-dump"></a> - Wanneer software crasht, een crash dump vangt een deel van het geheugen op het moment van de crash.

    Een crash kan zijn veroorzaakt door malware of malware bevatten. Om te voorkomen dat ze worden gedetecteerd door beveiligingsproducten, verschillende vormen van malware gebruik maken van een fileless aanval, die vermijdt schrijven naar de schijf of het versleutelen van software componenten geschreven op schijf. Dit type aanval is moeilijk op te sporen met behulp van traditionele schijfgebaseerde benaderingen.

    Echter, met behulp van geheugenanalyse, u dit soort aanvallen detecteren. Door het geheugen in de crashdump te analyseren, kan Security Center de technieken detecteren die de aanval gebruikt. De aanval kan bijvoorbeeld proberen om kwetsbaarheden in de software te misbruiken, toegang te krijgen tot vertrouwelijke gegevens en heimelijk te blijven bestaan in een gecompromitteerde machine. Security Center doet dit werk met minimale impact op de prestaties voor verhuurders.

    Zie de [referentietabel met waarschuwingen voor](alerts-reference.md#alerts-windows)meer informatie over de waarschuwingen voor crashdump.

* **Fileless attack detection** <a name="windows-fileless"></a> - Fileless attacks targeting your endpoints are common. Om detectie te voorkomen, bestandsloze aanvallen injecteren kwaadaardige payloads in het geheugen. Aanvaller payloads blijven in het geheugen van gecompromitteerde processen, en het uitvoeren van een breed scala van kwaadaardige activiteiten.

    Met fileless attack detection, geautomatiseerde geheugen forensische technieken identificeren fileless aanval toolkits, technieken en gedrag. Deze oplossing scant uw machine periodiek tijdens runtime en haalt inzichten rechtstreeks uit het geheugen van beveiligingskritieke processen.

    Het vindt bewijs van uitbuiting, code injectie, en uitvoering van kwaadaardige payloads. Fileless attack detection genereert gedetailleerde beveiligingswaarschuwingen om de waarschuwingstriage, correlatie en downstream responstijd te versnellen. Deze aanpak vormt een aanvulling op event-based EDR-oplossingen en biedt een grotere detectiedekking.

    Zie de [referentietabel met waarschuwingen voor](alerts-reference.md#alerts-windows)meer informatie over de waarschuwingen voor bestandsloze aanvallen.

> [!TIP]
> U Windows-waarschuwingen simuleren door [Azure Security Center Playbook: Beveiligingswaarschuwingen](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)te downloaden.






## <a name="threat-protection-for-linux-machines"></a>Bescherming tegen bedreigingen voor Linux-machines<a name="linux-machines"></a>

Security Center verzamelt auditrecords van Linux-machines met behulp van **gecontroleerde**, een van de meest voorkomende Linux-controleframeworks. gecontroleerd leven in de hoofdlijn kernel. 

* **Linux gecontroleerde waarschuwingen en Log Analytics agent integratie** <a name="linux-auditd"></a> - Het gecontroleerde systeem bestaat uit een subsysteem op kernelniveau, dat verantwoordelijk is voor het monitoren van systeemoproepen. Het filtert ze door een opgegeven regelset en schrijft berichten voor hen naar een socket. Security Center integreert functionaliteiten van het gecontroleerde pakket binnen de Log Analytics-agent. Deze integratie maakt het verzamelen van gecontroleerde gebeurtenissen in alle ondersteunde Linux-distributies mogelijk, zonder enige vereiste.

    gecontroleerde records worden verzameld, verrijkt en samengevoegd tot gebeurtenissen met behulp van de Log Analytics-agent voor Linux-agent. Security Center voegt voortdurend nieuwe analyses toe die Linux-signalen gebruiken om kwaadaardig gedrag op cloud- en on-premises Linux-machines te detecteren. Net als bij Windows-mogelijkheden omvatten deze analyses verdachte processen, dubieuze aanmeldingspogingen, het laden van kernelmodules en andere activiteiten. Deze activiteiten kunnen aangeven dat een machine wordt aangevallen of is geschonden.  

    Zie de [referentietabel met waarschuwingen voor](alerts-reference.md#alerts-linux)een lijst met de Linux-waarschuwingen .

> [!TIP]
> U Linux-waarschuwingen simuleren door [Azure Security Center Playbook: Linux-detecties te](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)downloaden.





## <a name="threat-protection-for-azure-app-service"></a>Bescherming tegen bedreigingen voor Azure App-service<a name="app-services"></a>

> [!NOTE]
> Deze service is momenteel niet beschikbaar in Azure-overheids- en soevereine cloudregio's.

Security Center gebruikt de schaal van de cloud om aanvallen te identificeren die zich richten op toepassingen die via App Service worden uitgevoerd. Aanvallers sonde webapplicaties te vinden en te exploiteren zwakke punten. Voordat ze worden doorgestuurd naar specifieke omgevingen, gaan aanvragen naar toepassingen die in Azure worden uitgevoerd door verschillende gateways, waar ze worden geïnspecteerd en geregistreerd. Deze gegevens worden vervolgens gebruikt om exploits en aanvallers te identificeren en om nieuwe patronen te leren die later zullen worden gebruikt.

Met de zichtbaarheid die Azure heeft als cloudprovider, analyseert Security Center interne logboeken van App Service om aanvalsmethodologie op meerdere doelen te identificeren. Methodologie omvat bijvoorbeeld wijdverbreide scannen en gedistribueerde aanvallen. Dit type aanval komt meestal uit een kleine subset van IP's en toont patronen van crawlen naar vergelijkbare eindpunten op meerdere hosts. De aanvallen zijn op zoek naar een kwetsbare pagina of plugin, en kan niet worden geïdentificeerd vanuit het standpunt van een enkele host.

Als u een Windows-appserviceplan uitvoert, heeft Security Center ook toegang tot de onderliggende sandboxes en VM's. Samen met de hierboven genoemde loggegevens kan de infrastructuur het verhaal vertellen, van een nieuwe aanval die in het wild circuleert tot compromissen in klantmachines. Daarom, zelfs als Security Center wordt geïmplementeerd nadat een web-app is uitgebuit, kan het mogelijk lopende aanvallen detecteren.

Zie de [referentietabel met waarschuwingen voor](alerts-reference.md#alerts-azureappserv)een lijst met waarschuwingen voor Azure App Service.

Zie [App-serviceplannen](https://azure.microsoft.com/pricing/details/app-service/plans/)voor meer informatie over app-serviceplannen.





## <a name="threat-protection-for-azure-containers"></a>Bescherming tegen bedreigingen voor Azure-containers<a name="azure-containers"></a>

> [!NOTE]
> Deze service is momenteel niet beschikbaar in Azure-overheids- en soevereine cloudregio's.

Security Center biedt real-time bescherming tegen bedreigingen voor uw gecontaineriseerde omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

Security Center biedt bescherming tegen bedreigingen op verschillende niveaus: 

* **Hostniveau** - De agent van het Beveiligingscentrum (beschikbaar op de standaardlaag, zie [prijzen](security-center-pricing.md) voor details) controleert Linux op verdachte activiteiten. De agent activeert waarschuwingen voor verdachte activiteiten afkomstig van het knooppunt of een container die erop wordt uitgevoerd. Voorbeelden van dergelijke activiteiten zijn webshelldetectie en verbinding met bekende verdachte IP-adressen.

    Voor een dieper inzicht in de beveiliging van uw gecontaineriseerde omgeving, bewaakt de agent containerspecifieke analyses. Het activeert waarschuwingen voor gebeurtenissen zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en Secure Shell-servers (SSH) die in een Docker-container worden uitgevoerd.

    >[!IMPORTANT]
    > Als u ervoor kiest om de agents niet op uw hosts te installeren, ontvangt u slechts een subset van de voordelen voor bedreigingsbescherming en beveiligingswaarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerkanalyse en communicatie met schadelijke servers.

    Zie de [referentietabel met waarschuwingen voor](alerts-reference.md#alerts-containerhost)een lijst met waarschuwingen op hostniveau .


* Op **AKS-clusterniveau**is de bescherming van bedreigingen gebaseerd op het analyseren van kubernetes' auditlogs. Als u deze **agentless** monitoring wilt inschakelen, voegt u de optie Kubernetes toe aan uw abonnement via de pagina **Prijs& instellingen** (zie [prijzen).](security-center-pricing.md) Als u waarschuwingen op dit niveau wilt genereren, controleert Security Center uw AKS-beheerde services met behulp van de logboeken die door AKS zijn opgehaald. Voorbeelden van gebeurtenissen op dit niveau zijn blootgestelde Kubernetes-dashboards, het maken van hoge bevoorrechte rollen en het maken van gevoelige mounts.

    >[!NOTE]
    > Security Center genereert beveiligingswaarschuwingen voor Azure Kubernetes Service-acties en implementaties die plaatsvinden nadat de Kubernetes-optie is ingeschakeld in de abonnementsinstellingen. 

    Zie de [referentietabel met waarschuwingen voor](alerts-reference.md#alerts-akscluster)een lijst met waarschuwingen op AKS-clusterniveau .

Ook ons wereldwijde team van security onderzoekers voortdurend toezicht op de dreiging landschap. Ze voegen containerspecifieke waarschuwingen en kwetsbaarheden toe wanneer ze worden ontdekt.

> [!TIP]
> U containerwaarschuwingen simuleren door de instructies in [deze blogpost te volgen.](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Bescherming tegen bedreigingen voor SQL Database en SQL Data Warehouse<a name="data-sql"></a>

Geavanceerde bedreigingsbeveiliging voor Azure SQL Database detecteert afwijkende activiteiten die ongebruikelijke en mogelijk schadelijke pogingen om databases te openen of te exploiteren aangeven.

U ziet waarschuwingen wanneer er verdachte databaseactiviteiten, potentiële kwetsbaarheden of SQL-injectieaanvallen zijn en afwijkende databasetoegang en querypatronen.

Advanced Threat Protection for Azure SQL Database and SQL is onderdeel van het Unified Package [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) voor geavanceerde SQL-beveiligingsmogelijkheden, dat betrekking heeft op Azure SQL-databases, Azure SQL Database managed instances, Azure SQL Data Warehouse databases en SQL-servers op Azure Virtual Machines.

Zie voor meer informatie:

* [Geavanceerde bedreigingsbeveiliging inschakelen voor Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Geavanceerde bedreigingsbeveiliging inschakelen voor SQL-servers op Azure Virtual Machines](security-center-iaas-advanced-data.md)
* [De lijst met waarschuwingen voor bedreigingsbescherming voor SQL Database en SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Bescherming tegen bedreigingen voor Azure Storage<a name="azure-storage"></a>

Geavanceerde bedreigingsbeveiliging voor opslag detecteert ongebruikelijke en mogelijk schadelijke pogingen om opslagaccounts te openen of te exploiteren. Met deze beveiligingslaag u bedreigingen aanpakken zonder dat u een beveiligingsexpert hoeft te zijn en u uw beveiligingsbewakingssystemen beheren.

Geavanceerde bedreigingsbeveiliging voor Azure Storage is momenteel alleen beschikbaar voor [Blob Storage.](https://azure.microsoft.com/services/storage/blobs/) 

Deze service is beschikbaar in alle openbare clouds en amerikaanse overheidsclouds, maar geen andere soevereine of Azure-overheidscloudregio's.

Zie de [prijspagina](https://azure.microsoft.com/pricing/details/security-center/)van azure security center voor prijsgegevens, waaronder een gratis proefperiode van 30 dagen.

Zie voor meer informatie:

* [Geavanceerde bedreigingsbeveiliging inschakelen voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [De lijst met waarschuwingen voor bedreigingsbeveiliging voor Azure Storage](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> U Azure Storage-waarschuwingen simuleren door de instructies in [dit blogbericht te volgen.](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)




## <a name="threat-protection-for-azure-cosmos-db"></a>Bescherming tegen bedreigingen voor Azure Cosmos DB<a name="cosmos-db"></a>

De Azure Cosmos DB-waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot Azure Cosmos DB-accounts of deze te exploiteren.

Zie voor meer informatie:

* [Geavanceerde bedreigingsbeveiliging voor Azure Cosmos DB (preview)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [De lijst met waarschuwingen voor bedreigingsbeveiliging voor Azure Cosmos DB (Preview)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Bescherming van bedreigingen voor Azure-netwerklaag<a name="network-layer"></a>

Analyse van netwerklagen van beveiligingscentrumen is gebaseerd op [voorbeeld-IPFIX-gegevens,](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)die pakketheaders zijn die zijn verzameld door Azure-core-routers. Op basis van deze gegevensfeed gebruikt Security Center machine learning-modellen om schadelijke verkeersactiviteiten te identificeren en te markeren. Security Center gebruikt ook de Microsoft Threat Intelligence-database om IP-adressen te verrijken.

Sommige netwerkconfiguraties kunnen beveiligingscentrum beperken voor het genereren van waarschuwingen voor verdachte netwerkactiviteiten. Als u beveiligingscentrum netwerkwaarschuwingen wilt genereren, moet u ervoor zorgen dat:

- Uw virtuele machine heeft een openbaar IP-adres (of staat op een load balancer met een openbaar IP-adres).

- Het netwerkuitgangsverkeer van uw virtuele machine wordt niet geblokkeerd door een externe IDS-oplossing.

- Uw virtuele machine heeft hetzelfde IP-adres toegewezen gekregen voor het hele uur waarin de verdachte communicatie plaatsvond. Dit geldt ook voor VM's die zijn gemaakt als onderdeel van een managed service (bijvoorbeeld AKS, Databricks).

Zie de [referentietabel met waarschuwingen voor](alerts-reference.md#alerts-azurenetlayer)een lijst met waarschuwingen voor Azure-netwerklagen .

Zie [Heuristische DNS-detecties in Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)voor meer informatie over hoe Security Center netwerkgerelateerde signalen kan gebruiken om bedreigingsbeveiliging toe te passen.



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Bedreigingsbeveiliging voor Azure-beheerlaag (Azure Resource Manager) (voorbeeld)<a name ="management-layer"></a>

De beveiligingslaag van het Beveiligingscentrum op basis van Azure Resource Manager bevindt zich momenteel in preview.

Security Center biedt een extra beveiligingslaag door Azure Resource Manager-gebeurtenissen te gebruiken, die wordt beschouwd als het controlevlak voor Azure. Door de Azure Resource Manager-records te analyseren, detecteert Security Center ongebruikelijke of mogelijk schadelijke bewerkingen in de Azure-abonnementsomgeving.

Zie de [referentietabel met waarschuwingen](alerts-reference.md#alerts-azureresourceman)voor een lijst met waarschuwingen voor Azure Resource Manager (Preview).



>[!NOTE]
> Verschillende van de voorgaande analyses worden aangedreven door Microsoft Cloud App Security. Om van deze analyses te kunnen profiteren, moet u een Cloud App Security-licentie activeren. Als u een Cloud App Security-licentie hebt, zijn deze waarschuwingen standaard ingeschakeld. Ga als u de waarschuwingen uit:
>
> 1. Selecteer in het mes **Van het Beveiligingscentrum** de optie **Beveiligingsbeleid**. Selecteer **Instellingen bewerken**voor het abonnement dat u wilt wijzigen.
> 2. Selecteer **Bedreigingsdetectie**.
> 3. Schakel **onder Integraties inschakelen**uit, schakel Microsoft Cloud **App-beveiliging toe om toegang te krijgen tot mijn gegevens**en selecteer **Opslaan**.

>[!NOTE]
>Security Center slaat beveiligingsgerelateerde klantgegevens op in dezelfde geo als de bron. Als Microsoft security center nog niet heeft geïmplementeerd in de geo van de bron, worden de gegevens in de Verenigde Staten opgeslagen. Wanneer Cloud App Security is ingeschakeld, wordt deze informatie opgeslagen in overeenstemming met de geolocatieregels van Cloud App Security. Zie [Gegevensopslag voor niet-regionale diensten voor](https://azuredatacentermap.azurewebsites.net/)meer informatie .








## <a name="threat-protection-for-azure-key-vault-preview"></a>Bescherming tegen bedreigingen voor Azure Key Vault (Voorbeeld)<a name="azure-keyvault"></a>

> [!NOTE]
> Deze service is momenteel niet beschikbaar in Azure-overheids- en soevereine cloudregio's.

Azure Key Vault is een cloudservice die versleutelingssleutels en -geheimen zoals certificaten, verbindingstekenreeksen en wachtwoorden beschermt. 

Azure Security Center bevat Azure-native, geavanceerde bedreigingsbeveiliging voor Azure Key Vault, dat een extra beveiligingslaag biedt. Security Center detecteert ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot Key Vault-accounts of deze te exploiteren. Met deze beveiligingslaag u bedreigingen aanpakken zonder beveiligingsexpert te zijn en zonder dat u beveiligingsbewakingssystemen van derden hoeft te beheren.  

Wanneer afwijkende activiteiten plaatsvinden, toont Security Center waarschuwingen en stuurt deze optioneel via e-mail naar abonnementsbeheerders. Deze waarschuwingen bevatten de details van de verdachte activiteit en aanbevelingen over het onderzoeken en veraneren van bedreigingen. 

Zie de [referentietabel met waarschuwingen voor](alerts-reference.md#alerts-azurekv)een lijst met waarschuwingen voor Azure Key Vault.





## <a name="threat-protection-for-other-microsoft-services"></a>Bescherming tegen bedreigingen voor andere Microsoft-services<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Bescherming tegen bedreigingen voor Azure WAF<a name="azure-waf"></a>

Azure Application Gateway biedt de functie Web Application Firewall (WAF) voor de gecentraliseerde beveiliging van uw webtoepassingen tegen bekende aanvallen en beveiligingsproblemen.

Webapplicaties zijn steeds vaker het doelwit van kwaadaardige aanvallen die misbruik maken van bekende kwetsbaarheden. De Application Gateway WAF is gebaseerd op Core Rule Set 3.0 of 2.2.9 van het Open Web Application Security Project. De WAF wordt automatisch bijgewerkt om te beschermen tegen nieuwe kwetsbaarheden. 

Als u een licentie hebt voor Azure WAF, worden uw WAF-waarschuwingen naar het Beveiligingscentrum gestreamd zonder dat extra configuratie nodig is. Zie [CRS-regelgroepen en -regels voor webtoepassingen firewall CRS](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)voor meer informatie over de waarschuwingen die door WAF worden gegenereerd.


### <a name="threat-protection-for-azure-ddos-protection"></a>Bescherming tegen bedreigingen voor Azure DDoS-beveiliging<a name="azure-ddos"></a>

Distributed denial of service (DDoS) aanvallen staan bekend als eenvoudig uit te voeren. Ze zijn een groot beveiligingsprobleem geworden, vooral als u uw toepassingen naar de cloud verplaatst. 

Een DDoS-aanval probeert de bronnen van een toepassing uit te putten, waardoor de toepassing niet beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen zich richten op elk eindpunt dat via het internet kan worden bereikt.

Als u zich wilt beschermen tegen DDoS-aanvallen, koopt u een licentie voor Azure DDoS Protection en zorgt u ervoor dat u de aanbevolen procedures voor het ontwerpen van toepassingen volgt. DDoS Protection biedt verschillende servicelagen. Zie overzicht [azure DDoS Protection](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)voor meer informatie.

Zie de [referentietabel met waarschuwingen voor](alerts-reference.md#alerts-azureddos)een lijst met azure ddos-beveiligingwaarschuwingen .


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over de beveiligingswaarschuwingen van deze functies voor bedreigingsbescherming:

* [Referentietabel voor alle Azure Security Center-waarschuwingen](alerts-reference.md)
* [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md)
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Beveiligingswaarschuwingen en aanbevelingen exporteren (Voorbeeld)](continuous-export.md)
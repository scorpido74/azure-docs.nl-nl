---
title: Bescherming tegen bedreiging in Azure Security Center
description: In dit onderwerp worden de resources beschreven die worden beveiligd door Azure Security Center beveiligings functies van bedreigingen
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 8bd66b602201dbbb47a1fdba879c52a8ac441762
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372980"
---
# <a name="threat-protection-in-azure-security-center"></a>Bescherming tegen bedreiging in Azure Security Center

Hier vindt u een korte beschrijving van de bronnen van de beveiligings waarschuwingen die door Azure Security Center worden weer gegeven voor gebruikers in de prijs categorie Standard.

Wanneer Security Center een bedreiging detecteert in een van de gebieden van uw omgeving die hieronder worden weer gegeven, wordt er een waarschuwing gegenereerd. Deze waarschuwingen beschrijven de details van de betrokken resources, voorgestelde herstels tappen en in sommige gevallen een logische app activeren als reactie.

Of een waarschuwing wordt gegenereerd door Security Center, of door Security Center van een ander beveiligings product is ontvangen, kunt u het exporteren. Als u uw waarschuwingen wilt exporteren naar een Azure Sentinel (of een SIEM van derden) of een andere externe locatie, volgt u de instructies bij het [exporteren van waarschuwingen en aanbevelingen](continuous-export.md). 




## Bedreigings beveiliging voor Windows-computers<a name="windows-machines"></a>

Azure Security Center integreert met Azure-Services om uw op Windows gebaseerde computers te controleren en te beveiligen. Security Center toont de waarschuwingen en suggesties voor herstel van al deze services in een gemakkelijk te gebruiken indeling.

* **Micro soft Defender ATP** <a name="windows-atp"></a> -Security Center breidt de beveiligings platformen voor Cloud werkbelasting uit door te integreren met micro soft Defender Advanced Threat Protection (ATP). Samen bieden ze uitgebreide functionaliteit voor eindpunt detectie en-antwoorden (EDR).

    > [!IMPORTANT]
    > De micro soft Defender ATP-sensor wordt automatisch ingeschakeld op Windows-servers die gebruikmaken van Security Center.

    Wanneer micro soft Defender ATP een bedreiging detecteert, wordt er een waarschuwing gegenereerd. De waarschuwing wordt weer gegeven op het Security Center dash board. Vanuit het dash board kunt u naar de micro soft Defender ATP-console draaien en een gedetailleerd onderzoek uitvoeren om het bereik van de aanval te ontdekken. Zie voor meer informatie over micro soft Defender ATP [servers onboarding voor de micro soft Defender ATP-service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Crash dump analyse** <a name="windows-dump"></a> : wanneer software vastloopt, legt een crash dump een deel van het geheugen vast op het moment dat het vastloopt.

    Een crash kan zijn veroorzaakt door malware of schadelijke software bevatten. Om te voor komen dat wordt gedetecteerd door beveiligings producten, gebruiken verschillende vormen van malware een aanval zonder bestanden, waarmee het schrijven naar schijf of het versleutelen van software onderdelen die naar de schijf worden geschreven, wordt voor komen. Dit type aanval is moeilijk te detecteren met traditionele benaderingen op basis van schijven.

    Door gebruik te maken van geheugen analyse, kunt u dit soort aanvallen echter detecteren. Door het geheugen in de crash dump te analyseren, kunt Security Center de technieken detecteren die de aanval gebruikt. De aanval kan bijvoorbeeld proberen misbruik te maken van beveiligings problemen in de software, toegang te krijgen tot vertrouwelijke gegevens en ongemerkt blijven bestaan binnen een gemanipuleerde computer. Security Center werkt dit met minimale gevolgen voor de prestaties van hosts.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-windows)voor meer informatie over de waarschuwingen voor de crash dump analyse.

* **Detectie** <a name="windows-fileless"></a> van aanval via een bestand: aanvallen waarbij de eind punten zijn gericht op de bedoelingen, zijn gebruikelijk. Om detectie te voor komen, worden met aanvallen zonder bestanden schadelijke nettoladingen in het geheugen geïnjecteerd. Nettoladingen van aanvallers blijven aanwezig in het geheugen van verdachte processen en kunnen een breed scala aan schadelijke activiteiten uitvoeren.

    Met de aanvals detectie op basis van een bestand, identificeren forensische-technieken met geautomatiseerd geheugen aanvals Toolkit, technieken en gedragingen. Met deze oplossing wordt uw machine periodiek gescand tijdens runtime en worden inzichten direct opgehaald uit het geheugen van essentiële beveiligings processen.

    Er wordt bewezen van exploitatie, code injectie en de uitvoering van schadelijke nettoladingen. Detectie van een aanval met een bestand genereert gedetailleerde beveiligings waarschuwingen voor het versnellen van waarschuwings sorteren, correlatie en downstream-reactie tijd. Deze aanpak is een aanvulling op op gebeurtenissen gebaseerde EDR-oplossingen, waardoor er meer detectie dekking is.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-windows)voor meer informatie over de waarschuwingen voor detectie van aanvals bestanden.

> [!TIP]
> U kunt Windows-waarschuwingen simuleren door [Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)te downloaden.






## Bedreigings beveiliging voor Linux-machines<a name="linux-machines"></a>

Security Center controleert controle records van Linux-machines met behulp van **controle**, een van de meest voorkomende Linux-controle raamwerken. de levens duur van de mainline-kernel wordt gecontroleerd. 

* Door <a name="linux-auditd"></a> **Linux gecontroleerde waarschuwingen en integratie met micro soft Monitoring Agent (MMA)** : het gecontroleerde systeem bestaat uit een subsysteem op kernelniveau, dat verantwoordelijk is voor het bewaken van systeem aanroepen. Ze worden gefilterd op basis van een opgegeven regelset en er worden berichten naar een socket geschreven. Security Center integreert de functionaliteit van het gecontroleerde pakket binnen de micro soft Monitoring Agent (MMA). Met deze integratie kunnen gecontroleerde gebeurtenissen in alle ondersteunde Linux-distributies worden verzameld, zonder dat hiervoor vereisten gelden.  

    gecontroleerde records worden verzameld, verrijkt en geaggregeerd naar gebeurtenissen met behulp van de Linux MMA-agent. Security Center doorlopend nieuwe analyses toevoegen die gebruikmaken van Linux-signalen om schadelijk gedrag op Cloud-en on-premises Linux-machines te detecteren. Net als bij Windows-mogelijkheden, deze analyse bevinden zich in verdachte processen, dubious-aanmeldings pogingen, laden van de kernel-module en andere activiteiten. Deze activiteiten kunnen erop wijzen dat een machine een aanval ondervindt of is geschonden.  

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-linux)voor een lijst met de Linux-waarschuwingen.

> [!TIP]
> U kunt Linux-waarschuwingen simuleren door [Azure Security Center Playbook te downloaden: Linux-detecties](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## Bedreigings beveiliging voor Azure App Service<a name="app-services"></a>

> [!NOTE]
> Deze service is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

Security Center gebruikt de schaal van de cloud om aanvallen te identificeren die gericht zijn op toepassingen die meer dan App Service. Aanvallers kunnen webtoepassingen testen om zwakke plekken te vinden en misbruik te maken. Aanvragen voor toepassingen die in Azure worden uitgevoerd, worden voordat ze naar specifieke omgevingen worden doorgestuurd via verschillende gateways, waar ze worden geïnspecteerd en geregistreerd. Deze gegevens worden vervolgens gebruikt om aanvallen en aanvallers te identificeren en om nieuwe patronen te leren die later zullen worden gebruikt.

Door de zicht baarheid van Azure als een Cloud provider te gebruiken, Security Center analyseert App Service interne Logboeken om een aanvals methodologie op meerdere doelen te identificeren. Zo omvat de methodologie uitgebreide scans en gedistribueerde aanvallen. Dit type aanval is doorgaans afkomstig uit een kleine subset van IP-adressen en toont patronen van het verkennen naar vergelijk bare eind punten op meerdere hosts. De aanvallen zoeken naar een kwets bare pagina of invoeg toepassing en kunnen niet worden geïdentificeerd op basis van het standpunt van één host.

Als u een App Service-abonnement op basis van Windows uitvoert, heeft Security Center ook toegang tot de onderliggende sandboxes en virtuele machines. Samen met de hierboven vermelde logboek gegevens kan de infra structuur het verhaal vertellen van een nieuwe aanval die in het wild wordt circuleren om inbreuk te kunnen vormen op de klant machines. Daarom, zelfs als Security Center wordt geïmplementeerd nadat een web-app is misbruikt, kan het mogelijk worden doorlopende aanvallen te detecteren.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureappserv)voor een overzicht van de waarschuwingen voor Azure app service.

Zie [app service-plannen](https://azure.microsoft.com/pricing/details/app-service/plans/)voor meer informatie over het plannen van app service.





## Bedreigings beveiliging voor Azure-containers<a name="azure-containers"></a>

> [!NOTE]
> Deze service is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

Security Center biedt realtime bescherming van bedreigingen voor uw container omgevingen en genereert waarschuwingen voor verdachte activiteiten. U kunt deze informatie gebruiken om snel beveiligingsproblemen op te lossen en om de beveiliging van uw containers te verbeteren.

Security Center biedt bedreigings beveiliging op verschillende niveaus: 

* **Host level** -Security Center Agent (Zie de [prijs](security-center-pricing.md) informatie voor de Standard-laag) wordt Linux gecontroleerd op verdachte activiteiten. De agent activeert waarschuwingen voor verdachte activiteiten die afkomstig zijn van het knoop punt of een container die erop wordt uitgevoerd. Voor beelden van dergelijke activiteiten zijn Webshell detectie en verbinding met bekende verdachte IP-adressen.

    Voor een diep gaande inzicht in de beveiliging van uw container omgeving bewaakt de agent de container-specifieke analyses. Er worden waarschuwingen geactiveerd voor gebeurtenissen, zoals het maken van geprivilegieerde containers, verdachte toegang tot API-servers en SSH-servers (Secure Shell) die binnen een docker-container worden uitgevoerd.

    >[!IMPORTANT]
    > Als u ervoor kiest om de agents op uw hosts niet te installeren, ontvangt u alleen een subset van de voor delen van bedreigings beveiliging en beveiligings waarschuwingen. U ontvangt nog steeds waarschuwingen met betrekking tot netwerk analyse en communicatie met schadelijke servers.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-containerhost)voor een lijst met waarschuwingen op hostniveau.


* Op het **AKS-cluster niveau**is beveiliging tegen bedreigingen gebaseerd op het analyseren van controle logboeken van Kubernetes. Als u deze bewaking zonder **agents** wilt inschakelen, voegt u de optie Kubernetes toe aan uw abonnement op de pagina met **prijs & instellingen** (Zie [prijzen](security-center-pricing.md)). Als u waarschuwingen op dit niveau wilt genereren, controleert Security Center uw door AKS beheerde services met de logboeken die zijn opgehaald door AKS. Voor beelden van gebeurtenissen op dit niveau zijn onder andere beschik bare Kubernetes-Dash boards, het maken van rollen met hoge bevoegdheden en het maken van gevoelige koppels.

    >[!NOTE]
    > Security Center genereert beveiligings waarschuwingen voor Azure Kubernetes-service acties en implementaties die worden uitgevoerd nadat de optie Kubernetes is ingeschakeld op de abonnements instellingen. 

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-akscluster)voor een lijst met waarschuwingen op AKS-cluster niveau.

Het wereld wijde team van beveiligings onderzoekers bewaakt ook voortdurend de bedreigings landschap. Ze voegen container-specifieke waarschuwingen en beveiligings problemen toe wanneer ze worden gedetecteerd.






## Bedreigings beveiliging voor Azure Network Layer<a name="network-layer"></a>

Security Center Network-laag analyse zijn gebaseerd op voorbeeld [gegevens van IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), die pakket headers zijn die worden verzameld door Azure core-routers. Op basis van deze gegevensfeed gebruikt Security Center machine learning modellen om kwaad aardige verkeers activiteiten te identificeren en te markeren. Security Center gebruikt de micro soft Threat Intelligence-data base ook om IP-adressen te verrijken.

Sommige netwerk configuraties kunnen Security Center beperken van het genereren van waarschuwingen over verdachte netwerk activiteit. Zorg ervoor dat de volgende handelingen worden Security Center om netwerk waarschuwingen te genereren:

- De virtuele machine heeft een openbaar IP-adres (of bevindt zich op een load balancer met een openbaar IP-adres).

- Het netwerk verkeer van de virtuele machine wordt niet geblokkeerd door een externe ID-oplossing.

- De virtuele machine is toegewezen aan hetzelfde IP-adres voor het hele uur waarin de verdachte communicatie heeft plaatsgevonden. Dit geldt ook voor virtuele machines die zijn gemaakt als onderdeel van een beheerde service (bijvoorbeeld AKS, Databricks).

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azurenetlayer)voor een overzicht van de waarschuwingen van de Azure-netwerklaag.

Zie voor meer informatie over hoe Security Center netwerk signalen kunt gebruiken voor het Toep assen van bedreigings beveiliging de [heuristische DNS-detecties in Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).







## Bedreigings beveiliging voor Azure Key Vault (preview-versie)<a name="azure-keyvault"></a>

> [!NOTE]
> Deze service is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

Azure Key Vault is een Cloud service die versleutelings sleutels en geheimen beveiligt, zoals certificaten, verbindings reeksen en wacht woorden. 

Azure Security Center omvat Azure-systeem eigen, geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault, waarmee u een extra laag van beveiligings informatie kunt leveren. Security Center detecteert ongebruikelijke en mogelijk schadelijke pogingen om Key Vault accounts te openen of misbruik te maken. Deze beveiligingslaag biedt u de mogelijkheid bedreigingen te verhelpen zonder een beveiligings expert en zonder de nood zaak om beveiligings bewakings systemen van derden te beheren.  

Als er afwijkende activiteiten optreden, worden in Security Center waarschuwingen weer gegeven en worden ze optioneel verzonden via e-mail naar abonnements beheerders. Deze waarschuwingen omvatten de details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azurekv)voor een overzicht van de waarschuwingen voor Azure Key Vault.








## Bedreigings beveiliging voor SQL Database en SQL Data Warehouse<a name="data-sql"></a>

Advanced Threat Protection voor Azure SQL Database detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

U ziet waarschuwingen wanneer er verdachte database activiteiten, potentiële kwetsbaar heden of SQL-injectie aanvallen en afwijkende database toegang en query patronen zijn.

Advanced Threat Protection voor Azure SQL Database en SQL maakt deel uit van het geïntegreerde pakket voor geavanceerde [gegevens beveiliging (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) voor geavanceerde SQL-beveiligings mogelijkheden, waaronder Azure SQL-data bases, Azure SQL database beheerde instanties, Azure SQL Data Warehouse data bases en SQL-servers in azure virtual machines.

Ga voor meer informatie naar:

* [Geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Geavanceerde beveiliging tegen bedreigingen inschakelen voor SQL-servers in azure Virtual Machines](security-center-iaas-advanced-data.md)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor SQL Database en SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)









## Bedreigings beveiliging voor Azure Storage<a name="azure-storage"></a>

> [!NOTE]
> Deze service is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

Geavanceerde bedreigings beveiliging voor opslag (momenteel alleen beschikbaar voor Blob Storage) detecteert ongebruikelijke en mogelijk schadelijke pogingen om opslag accounts te openen of misbruik te maken. Deze beveiligingslaag biedt u de mogelijkheid om bedreigingen te verhelpen zonder dat u een beveiligings expert hoeft te zijn, en helpt u bij het beheren van uw systemen voor beveiligings bewaking.

Ga voor meer informatie naar:

* [Geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor Azure Storage](alerts-reference.md#alerts-azurestorage)







## Bedreigings beveiliging voor Azure Cosmos DB<a name="cosmos-db"></a>

De Azure Cosmos DB waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om Azure Cosmos DB accounts te openen of misbruik te maken.

Ga voor meer informatie naar:

* [Advanced Threat Protection voor Azure Cosmos DB (preview-versie)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor Azure Cosmos DB (preview-versie)](alerts-reference.md#alerts-azurecosmos)







## Bedreigings beveiliging voor Azure Management Layer (Azure Resource Manager) (preview)<a name ="management-layer"></a>

De beveiligingslaag van Security Center is gebaseerd op Azure Resource Manager momenteel als preview-versie beschikbaar is.

Security Center biedt een extra beveiligingslaag door gebruik te maken van Azure Resource Manager-gebeurtenissen. dit wordt beschouwd als het controle vlak voor Azure. Door de Azure Resource Manager-records te analyseren, Security Center een ongebruikelijke of mogelijk schadelijke bewerking in de Azure-abonnements omgeving detecteert.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureresourceman)voor een lijst met waarschuwingen voor Azure Resource Manager (preview).



>[!NOTE]
> Verschillende van de voor gaande analyses worden mogelijk gemaakt door Microsoft Cloud App Security. Als u van deze analyses wilt profiteren, moet u een Cloud App Security-licentie activeren. Als u een licentie voor Cloud App Security hebt, zijn deze waarschuwingen standaard ingeschakeld. De waarschuwingen uitschakelen:
>
> 1. Selecteer **beveiligings beleid**op de blade **Security Center** . Selecteer **Instellingen bewerken**voor het abonnement dat u wilt wijzigen.
> 2. Selecteer **bedreigingen detectie**.
> 3. Schakel onder **integraties inschakelen**het selectie vakje **Microsoft Cloud app Security toegang tot mijn gegevens toestaan**uit en selecteer **Opslaan**.

>[!NOTE]
>Security Center klant gegevens met betrekking tot beveiliging worden opgeslagen in dezelfde geografische regio als de resource. Als micro soft Security Center nog niet heeft geïmplementeerd in de geografische regio van de resource, worden de gegevens opgeslagen in de Verenigde Staten. Als Cloud App Security is ingeschakeld, wordt deze informatie opgeslagen in overeenstemming met de geolocatie regels van Cloud App Security. Zie [gegevens opslag voor niet-regionale Services](https://azuredatacentermap.azurewebsites.net/)voor meer informatie.



## Beveiligings waarschuwingen van andere micro soft-Services<a name="alerts-other"></a>

### Bedreigings beveiliging voor Azure WAF<a name="azure-waf"></a>

Azure Application Gateway biedt de functie Web Application Firewall (WAF) voor de gecentraliseerde beveiliging van uw webtoepassingen tegen bekende aanvallen en beveiligingsproblemen.

Webtoepassingen worden steeds gericht op kwaad aardige aanvallen die veelvoorkomende beveiligings problemen misbruiken. De Application Gateway WAF is gebaseerd op de core Rule set 3,0 of 2.2.9 van het open Web Application Security-project. De WAF wordt automatisch bijgewerkt om te beschermen tegen nieuwe beveiligings problemen. 

Als u een licentie voor Azure WAF hebt, worden uw WAF-waarschuwingen gestreamd naar Security Center zonder dat er aanvullende configuratie nodig is. Zie voor meer informatie over de waarschuwingen die door WAF worden gegenereerd, [CRS-regel groepen en-regels voor Web Application firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### Bedreigings beveiliging voor Azure DDoS Protection<a name="azure-ddos"></a>

DDoS-aanvallen (Distributed Denial of service) zijn bekend om eenvoudig te worden uitgevoerd. Ze zijn een geweldig beveiligings probleem, met name als u uw toepassingen naar de Cloud verplaatst. 

Met een DDoS-aanval wordt geprobeerd de resources van een toepassing uit te putten, waardoor de toepassing niet meer beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eind punt dat kan worden bereikt via internet.

Schaf een licentie voor Azure DDoS Protection aan en zorg ervoor dat u de aanbevolen procedures voor het ontwerpen van toepassingen gebruikt om DDoS-aanvallen te voor komen. DDoS Protection biedt verschillende service lagen. Zie [Azure DDoS Protection Overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)voor meer informatie.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureddos)voor een overzicht van de waarschuwingen voor Azure DDoS Protection.


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over de beveiligings waarschuwingen van deze functies voor bedreigingen beveiliging:

* [Referentie tabel voor alle Azure Security Center waarschuwingen](alerts-reference.md)
* [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md)
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Beveiligings waarschuwingen en aanbevelingen exporteren (preview-versie)](continuous-export.md)
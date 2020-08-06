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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: e74dac779fc1eafaf33ffbc63bf997cf26b64954
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836799"
---
# <a name="threat-protection-in-azure-security-center"></a>Bescherming tegen bedreiging in Azure Security Center

Wanneer Security Center een bedreiging in een wille keurig gebied van uw omgeving detecteert, wordt er een waarschuwing gegenereerd. Deze waarschuwingen beschrijven de details van de betrokken resources, voorgestelde herstels tappen en in sommige gevallen een logische app activeren als reactie.

De bedreigings beveiliging van Azure Security Center biedt uitgebreide beveiligingen voor uw omgeving:

* **Bedreigings beveiliging voor Azure Compute-resources**: Windows-computers, Linux-machines, Azure app service en Azure-containers

* **Bedreigings beveiliging voor Azure-gegevens bronnen**: SQL Database en SQL Data Warehouse, Azure Storage en Azure Cosmos db

* **Bedreigings beveiliging voor Azure-service lagen**: Azure-netwerklaag, Azure Management layer (Azure Resource Manager) (preview) en Azure Key Vault (preview-versie)

Of een waarschuwing wordt gegenereerd door Security Center, of door Security Center van een ander beveiligings product is ontvangen, kunt u het exporteren. Als u uw waarschuwingen wilt exporteren naar Azure Sentinel, eventuele SIEM van derden of een ander extern hulp programma, volgt u de instructies in [waarschuwingen exporteren naar een Siem](continuous-export.md). 

> [!NOTE]
> Het kan even duren voordat waarschuwingen van verschillende bronnen worden weer gegeven. Waarschuwingen waarvoor analyse van netwerk verkeer nodig is, kunnen bijvoorbeeld langer duren dan waarschuwingen die betrekking hebben op de verdachte processen die op virtuele machines worden uitgevoerd.

> [!TIP]
> Als u de mogelijkheden voor bedreigings beveiliging van Security Center wilt inschakelen, moet u de prijs categorie Standard Toep assen op het abonnement dat de toepasselijke werk belastingen bevat.
>
> U kunt bedreigings beveiliging inschakelen voor **Azure Storage accounts** op het niveau van het abonnement of het resourceniveau.
> U kunt bedreigingen beveiliging inschakelen voor **Azure SQL database SQL-servers** op abonnements niveau of op resource niveau.
> U kunt bedreigingen beveiliging inschakelen voor **Azure database for MariaDB/MySQL/PostgreSQL** op het niveau van de resource.



## <a name="threat-protection-for-windows-machines"></a>Bedreigings beveiliging voor Windows-computers<a name="windows-machines"></a>

Azure Security Center integreert met Azure-Services om uw op Windows gebaseerde computers te controleren en te beveiligen. Security Center toont de waarschuwingen en suggesties voor herstel van al deze services in een gemakkelijk te gebruiken indeling.

* **Micro soft Defender Advanced Threat Protection (ATP)** <a name="windows-atp"></a> -Security Center breidt de beveiligings platforms voor Cloud werkbelasting uit door te integreren met micro soft Defender Advanced Threat Protection (ATP). Samen bieden ze uitgebreide functionaliteit voor eindpunt detectie en-antwoorden (EDR).

    > [!IMPORTANT]
    > De micro soft Defender ATP-sensor wordt automatisch ingeschakeld op Windows-servers die gebruikmaken van Security Center.

    Wanneer micro soft Defender ATP een bedreiging detecteert, wordt er een waarschuwing gegenereerd. De waarschuwing wordt weer gegeven op het Security Center dash board. Vanuit het dash board kunt u naar de micro soft Defender ATP-console draaien en een gedetailleerd onderzoek uitvoeren om het bereik van de aanval te ontdekken. Zie voor meer informatie over micro soft Defender ATP [servers onboarding voor de micro soft Defender ATP-service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* Detectie van bestanden met een **aanval** <a name="windows-fileless"></a> -Aanvallen zonder bestanden voeren schadelijke nettoladingen in het geheugen in om detectie door op schijf gebaseerde scan technieken te voor komen. De nettolading van de aanvaller bevindt zich vervolgens in het geheugen van verdachte processen en voert een breed scala aan schadelijke activiteiten uit.

    Met de aanvals detectie op basis van een bestand, identificeren forensische-technieken met geautomatiseerd geheugen aanvals Toolkit, technieken en gedragingen. Met deze oplossing wordt uw machine periodiek gescand tijdens runtime en worden inzichten direct opgehaald uit het geheugen van processen. Specifieke inzichten voor Linux bevatten de identificatie van: 

    - Bekende tool kits en crypto analyse-software 
    - Shell code, een klein stukje code dat doorgaans wordt gebruikt als de payload bij de exploitatie van een software beveiligings probleem.
    - Geïnjecteerd schadelijk uitvoerbaar bestand in het proces geheugen

    Detectie van een aanval met een bestand genereert gedetailleerde beveiligings waarschuwingen met de beschrijvingen met aanvullende proces meta gegevens, zoals netwerk activiteit. Dit versnelt de reactie tijd van de waarschuwing sorteren, correlatie en downstream. Deze benadering vormt een aanvulling op op gebeurtenissen gebaseerde EDR-oplossingen en biedt meer detectie dekking.

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-windows)voor meer informatie over de waarschuwingen voor detectie van aanvals bestanden.

> [!TIP]
> U kunt Windows-waarschuwingen simuleren door [Azure Security Center Playbook: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)te downloaden.






## <a name="threat-protection-for-linux-machines"></a>Bedreigings beveiliging voor Linux-machines<a name="linux-machines"></a>

Security Center controleert controle records van Linux-machines met behulp van **controle**, een van de meest voorkomende Linux-controle raamwerken. de levens duur van de mainline-kernel wordt gecontroleerd. 

* Door **Linux gecontroleerde waarschuwingen en integratie** <a name="linux-auditd"></a> van log Analytics-agent -Het gecontroleerde systeem bestaat uit een subsysteem op kernelniveau dat verantwoordelijk is voor het bewaken van systeem aanroepen. Ze worden gefilterd op basis van een opgegeven regelset en er worden berichten naar een socket geschreven. Security Center integreert de functionaliteit van het gecontroleerde pakket binnen de Log Analytics-agent. Met deze integratie kunnen gecontroleerde gebeurtenissen in alle ondersteunde Linux-distributies worden verzameld, zonder dat hiervoor vereisten gelden.

    gecontroleerde records worden verzameld, verrijkt en geaggregeerd in gebeurtenissen met behulp van de Log Analytics agent voor Linux agent. Security Center doorlopend nieuwe analyses toevoegen die gebruikmaken van Linux-signalen om schadelijk gedrag op Cloud-en on-premises Linux-machines te detecteren. Net als bij Windows-mogelijkheden, deze analyse bevinden zich in verdachte processen, dubious-aanmeldings pogingen, laden van de kernel-module en andere activiteiten. Deze activiteiten kunnen erop wijzen dat een machine een aanval ondervindt of is geschonden.  

    Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-linux)voor een lijst met de Linux-waarschuwingen.

> [!TIP]
> U kunt Linux-waarschuwingen simuleren door [Azure Security Center Playbook te downloaden: Linux-detecties](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Bedreigings beveiliging voor Azure App Service<a name="app-services"></a>

> [!NOTE]
> Deze service is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

Security Center gebruikt de schaal van de cloud om aanvallen te identificeren die gericht zijn op toepassingen die meer dan App Service. Aanvallers kunnen webtoepassingen testen om zwakke plekken te vinden en misbruik te maken. Aanvragen voor toepassingen die in Azure worden uitgevoerd, worden voordat ze naar specifieke omgevingen worden doorgestuurd via verschillende gateways, waar ze worden geïnspecteerd en geregistreerd. Deze gegevens worden vervolgens gebruikt om aanvallen en aanvallers te identificeren en om nieuwe patronen te leren die later zullen worden gebruikt.

Door de zicht baarheid van Azure als een Cloud provider te gebruiken, Security Center analyseert App Service interne Logboeken om een aanvals methodologie op meerdere doelen te identificeren. Zo omvat de methodologie uitgebreide scans en gedistribueerde aanvallen. Dit type aanval is doorgaans afkomstig uit een kleine subset van IP-adressen en toont patronen van het verkennen naar vergelijk bare eind punten op meerdere hosts. De aanvallen zoeken naar een kwets bare pagina of invoeg toepassing en kunnen niet worden geïdentificeerd op basis van het standpunt van één host.

Als u een App Service-abonnement op basis van Windows uitvoert, heeft Security Center ook toegang tot de onderliggende sandboxes en virtuele machines. Samen met de hierboven vermelde logboek gegevens kan de infra structuur het verhaal vertellen van een nieuwe aanval die in het wild wordt circuleren om inbreuk te kunnen vormen op de klant machines. Daarom, zelfs als Security Center wordt geïmplementeerd nadat een web-app is misbruikt, kan het mogelijk worden doorlopende aanvallen te detecteren.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureappserv)voor een overzicht van de waarschuwingen voor Azure app service.

Zie [app service-plannen](https://azure.microsoft.com/pricing/details/app-service/plans/)voor meer informatie over het plannen van app service.





## <a name="threat-protection-for-containers"></a>Bedreigings beveiliging voor containers<a name="azure-containers"></a>

### <a name="availability"></a>Beschikbaarheid

- Release status: **algemene Beschik baarheid**
- Vereiste rollen: **beveiligings beheerder** kan waarschuwingen negeren. **Beveiligings lezer** kan bevindingen weer geven.
- Clouds<br>
    ✔ Commerciële Clouds<br>
    ✘ US Gov<br>
    ✘ China gov, andere gov

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Bedreigings beveiliging voor SQL Database en SQL Data Warehouse<a name="data-sql"></a>

Advanced Threat Protection voor Azure SQL Database detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases.

U ziet waarschuwingen wanneer er verdachte database activiteiten, potentiële kwetsbaar heden of SQL-injectie aanvallen en afwijkende database toegang en query patronen zijn.

Advanced Threat Protection voor Azure SQL Database en SQL maakt deel uit van het geïntegreerde pakket voor geavanceerde [gegevens beveiliging (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) voor geavanceerde SQL-beveiligings mogelijkheden, waaronder Azure SQL database, Azure SQL Managed instances, Azure SQL Data Warehouse data bases en SQL-servers op Azure virtual machines.

Zie voor meer informatie:

* [Geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Geavanceerde beveiliging tegen bedreigingen inschakelen voor SQL-servers in azure Virtual Machines](security-center-iaas-advanced-data.md)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor SQL Database en SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Bedreigings beveiliging voor Azure Storage<a name="azure-storage"></a>

### <a name="availability"></a>Beschikbaarheid

- Release status:
    - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (algemene Beschik baarheid)
    - [Azure files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (preview-versie)
    - [Azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (preview-versie)
- Clouds<br>
    ✔ Commerciële Clouds<br>
    ✔ US Gov<br>
    ✘ China gov, andere gov

### <a name="whats-protected"></a>Wat is er beveiligd?

Bedreigings beveiliging voor Azure Storage detecteert mogelijk schadelijke activiteiten op uw Azure Storage-accounts. Uw gegevens kunnen worden beveiligd, ongeacht of deze zijn opgeslagen als blob-containers, bestands shares of gegevens-meren.

Deze beveiligingslaag biedt u de mogelijkheid om bedreigingen te verhelpen *zonder* dat u een beveiligings expert hoeft te zijn, en helpt u bij het beheren van uw systemen voor beveiligings bewaking.

Uw opslag accounts zijn beveiligd 

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>Wat voor soort waarschuwingen biedt bedreigings beveiliging voor Azure Storage?

Beveiligings waarschuwingen worden geactiveerd wanneer zich:

- **Verdachte activiteit** : bijvoorbeeld: het opslag account is geopend vanuit een IP-adres dat een actief eind punt van Tor wordt genoemd
- **Afwijkend gedrag** : bijvoorbeeld wijzigingen in het toegangs patroon op een opslag account
- **Potentiële malware geüpload** -hash-reputatie analyse geeft aan dat een geüpload bestand malware bevat

Waarschuwingen bevatten details over het incident waarmee ze zijn geactiveerd, evenals aanbevelingen voor het onderzoeken en oplossen van bedreigingen.

### <a name="what-is-hash-reputation-analysis-for-malware"></a>Wat is hash-reputatie analyse voor malware?

Als u wilt bepalen of een geüpload bestand verdacht is, wordt de beveiliging van de hash-reputatie voor Azure Storage gebruikt door [micro soft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684). Met de hulpprogram ma's voor beveiliging tegen bedreigingen worden de geüploade bestanden niet gescand, en worden de opslag logboeken onderzocht en worden de hashes van recent geüploade bestanden vergeleken met die van bekende virussen, Trojaanse paarden, spyware en Ransomware. 

Wanneer een bestand wordt verdacht om malware te bevatten, wordt in Security Center een waarschuwing weer gegeven en kan de opslag eigenaar voor goed keuring e-mail verzenden om het verdachte bestand te verwijderen. Als u dit automatisch verwijderen van bestanden wilt instellen die door hash-reputatie analyses worden aangegeven, implementeert u een [automatisering op werk stromen om waarschuwingen te activeren met mogelijke malware die is geüpload naar een opslag account](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005).



### <a name="next-steps"></a>Volgende stappen 

Voor prijs informatie, inclusief een gratis proef versie van 30 dagen, raadpleegt u de [pagina met Azure Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/).

Zie voor meer informatie:

* [Geavanceerde beveiliging tegen bedreigingen inschakelen voor Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor Azure Storage](alerts-reference.md#alerts-azurestorage)
* [De Threat Intelligence-mogelijkheden van micro soft](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> U kunt opslag waarschuwingen simuleren door de instructies in [dit blog bericht](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)te volgen.







## <a name="threat-protection-for-azure-cosmos-db"></a>Bedreigings beveiliging voor Azure Cosmos DB<a name="cosmos-db"></a>

De Azure Cosmos DB waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om Azure Cosmos DB accounts te openen of misbruik te maken.

Zie voor meer informatie:

* [Advanced Threat Protection voor Azure Cosmos DB (preview-versie)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [De lijst met beveiligings waarschuwingen voor bedreigingen voor Azure Cosmos DB (preview-versie)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Bedreigings beveiliging voor Azure Network Layer<a name="network-layer"></a>

Security Center Network-laag analyse zijn gebaseerd op voorbeeld [gegevens van IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), die pakket headers zijn die worden verzameld door Azure core-routers. Op basis van deze gegevensfeed gebruikt Security Center machine learning modellen om kwaad aardige verkeers activiteiten te identificeren en te markeren. Security Center gebruikt de micro soft Threat Intelligence-data base ook om IP-adressen te verrijken.

Sommige netwerk configuraties kunnen Security Center beperken van het genereren van waarschuwingen over verdachte netwerk activiteit. Zorg ervoor dat de volgende handelingen worden Security Center om netwerk waarschuwingen te genereren:

- De virtuele machine heeft een openbaar IP-adres (of bevindt zich op een load balancer met een openbaar IP-adres).

- Het netwerk verkeer van de virtuele machine wordt niet geblokkeerd door een externe ID-oplossing.

- De virtuele machine is toegewezen aan hetzelfde IP-adres voor het hele uur waarin de verdachte communicatie heeft plaatsgevonden. Dit geldt ook voor virtuele machines die zijn gemaakt als onderdeel van een beheerde service (bijvoorbeeld AKS, Databricks).

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azurenetlayer)voor een overzicht van de waarschuwingen van de Azure-netwerklaag.

Zie voor meer informatie over hoe Security Center netwerk signalen kunt gebruiken voor het Toep assen van bedreigings beveiliging de [heuristische DNS-detecties in Security Center](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Bedreigings beveiliging voor Azure Management Layer (Azure Resource Manager) (preview)<a name ="management-layer"></a>

De beveiligingslaag van Security Center is gebaseerd op Azure Resource Manager momenteel als preview-versie beschikbaar is.

Security Center biedt een extra beveiligingslaag door gebruik te maken van Azure Resource Manager-gebeurtenissen. dit wordt beschouwd als het controle vlak voor Azure. Door de Azure Resource Manager-records te analyseren, Security Center een ongebruikelijke of mogelijk schadelijke bewerking in de Azure-abonnements omgeving detecteert.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureresourceman)voor een lijst met waarschuwingen voor Azure Resource Manager (preview).



>[!NOTE]
> Verschillende van de voor gaande analyses worden mogelijk gemaakt door Microsoft Cloud App Security. Als u van deze analyses wilt profiteren, moet u een Cloud App Security-licentie activeren. Als u een licentie voor Cloud App Security hebt, zijn deze waarschuwingen standaard ingeschakeld. De waarschuwingen uitschakelen:
>
> 1. Selecteer in het menu van Security Center **prijzen & instellingen**.
> 1. Selecteer het abonnement dat u wilt wijzigen.
> 1. Selecteer **bedreigingen detectie**.
> 1. Schakel **Microsoft Cloud app Security toegang tot mijn gegevens toestaan**uit en selecteer **Opslaan**.

>[!NOTE]
>Security Center klant gegevens met betrekking tot beveiliging worden opgeslagen in dezelfde geografische regio als de resource. Als micro soft Security Center nog niet heeft geïmplementeerd in de geografische regio van de resource, worden de gegevens opgeslagen in de Verenigde Staten. Als Cloud App Security is ingeschakeld, wordt deze informatie opgeslagen in overeenstemming met de geolocatie regels van Cloud App Security. Zie [gegevens opslag voor niet-regionale Services](https://azuredatacentermap.azurewebsites.net/)voor meer informatie.

1. Stel de werk ruimte in waarop u de agent wilt installeren. Zorg ervoor dat de werk ruimte zich in hetzelfde abonnement bevindt dat u in Security Center gebruikt en dat u lees-en schrijf machtigingen hebt voor de werk ruimte.

1. Stel de prijs categorie Standard in en selecteer **Opslaan**.






## <a name="threat-protection-for-azure-key-vault-preview"></a>Bedreigings beveiliging voor Azure Key Vault (preview-versie)<a name="azure-keyvault"></a>

> [!NOTE]
> Deze service is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

Azure Key Vault is een Cloud service die versleutelings sleutels en geheimen beveiligt, zoals certificaten, verbindings reeksen en wacht woorden. 

Azure Security Center omvat Azure-systeem eigen, geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault, waarmee u een extra laag van beveiligings informatie kunt leveren. Security Center detecteert ongebruikelijke en mogelijk schadelijke pogingen om Key Vault accounts te openen of misbruik te maken. Deze beveiligingslaag biedt u de mogelijkheid bedreigingen te verhelpen zonder een beveiligings expert en zonder de nood zaak om beveiligings bewakings systemen van derden te beheren.  

Als er afwijkende activiteiten optreden, worden in Security Center waarschuwingen weer gegeven en worden ze optioneel verzonden via e-mail naar abonnements beheerders. Deze waarschuwingen omvatten de details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azurekv)voor een overzicht van de waarschuwingen voor Azure Key Vault.





## <a name="threat-protection-for-other-microsoft-services"></a>Bedreigings beveiliging voor andere micro soft-Services<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Bedreigings beveiliging voor Azure WAF<a name="azure-waf"></a>

Azure Application Gateway biedt de functie Web Application Firewall (WAF) voor de gecentraliseerde beveiliging van uw webtoepassingen tegen bekende aanvallen en beveiligingsproblemen.

Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van algemeen bekende beveiligingsproblemen. De Application Gateway WAF is gebaseerd op de core Rule set 3,0 of 2.2.9 van het open Web Application Security-project. De WAF wordt automatisch bijgewerkt om te beschermen tegen nieuwe beveiligings problemen. 

Als u een licentie voor Azure WAF hebt, worden uw WAF-waarschuwingen gestreamd naar Security Center zonder dat er aanvullende configuratie nodig is. Zie voor meer informatie over de waarschuwingen die door WAF worden gegenereerd, [CRS-regel groepen en-regels voor Web Application firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Bedreigings beveiliging voor Azure DDoS Protection<a name="azure-ddos"></a>

DDoS-aanvallen (Distributed Denial of service) zijn bekend om eenvoudig te worden uitgevoerd. Ze zijn een geweldig beveiligings probleem, met name als u uw toepassingen naar de Cloud verplaatst. 

Een DDoS-aanval probeert de bronnen van een toepassing te ontnemen, waardoor de toepassing niet beschikbaar is voor legitieme gebruikers. DDoS-aanvallen kunnen worden gericht op elk eind punt dat kan worden bereikt via internet.

Schaf een licentie voor Azure DDoS Protection aan en zorg ervoor dat u de aanbevolen procedures voor het ontwerpen van toepassingen gebruikt om DDoS-aanvallen te voor komen. DDoS Protection biedt verschillende service lagen. Zie [Azure DDoS Protection Overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)voor meer informatie.

Zie de [naslag tabel met waarschuwingen](alerts-reference.md#alerts-azureddos)voor een overzicht van de waarschuwingen voor Azure DDoS Protection.


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over de beveiligings waarschuwingen van deze functies voor bedreigingen beveiliging:

* [Referentie tabel voor alle Azure Security Center waarschuwingen](alerts-reference.md)
* [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-overview.md)
* [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Beveiligings waarschuwingen en aanbevelingen exporteren (preview-versie)](continuous-export.md)
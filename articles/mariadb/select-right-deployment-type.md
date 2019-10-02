---
title: Het juiste implementatie type voor uw Azure Database for MariaDB selecteren
description: In dit artikel wordt beschreven wat de overweging is die moet worden uitgevoerd voordat u verdergaat met IaaS (Infrastructure-as-a-Service) of platform-as-a-Service (PaaS) voor uw Azure Database for MariaDB.
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: fa87748719e2d3775034e5a2850281cf8f1a0e8a
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817434"
---
# <a name="choose-the-right-mariadb-server-option-in-azure"></a>Kies de juiste MariaDB-Server optie in azure

Met Azure kunnen de workloads van uw MariaDB-server worden uitgevoerd in een gehoste infra structuur-VM (IaaS) of als een gehoste service (PaaS). PaaS heeft meerdere implementatie opties en er zijn service lagen binnen elke implementatie optie. Bij het kiezen tussen PaaS of IaaS, moet u bepalen of u uw Data Base wilt beheren, patches wilt Toep assen en back-ups wilt maken, of als u deze bewerkingen wilt delegeren naar Azure.</br>

Houd rekening met de volgende opties op basis van uw antwoord op deze vraag: <br/>

**Azure database for MariaDB** is een volledig beheerde MariaDB-data base-engine op basis van de stabiele versie van de Community Edition. De relationele data base-as-a-Service (DBaaS), die wordt gehost in de Azure-Cloud, valt in de branche categorie van platform-as-a-Service (PaaS). Met een beheerd exemplaar van MariaDB op Azure kunt u ingebouwde functies en functionaliteit gebruiken waarvoor een uitgebreide configuratie is vereist bij het gebruik van MariaDB server (on-premises of op een virtuele machine van Azure). Wanneer u MariaDB als een service gebruikt, betaalt u op basis van de opties voor het omhoog of omlaag schalen van grotere kracht zonder onderbreking. Daarnaast heeft Azure Database for MariaDB, in tegens telling tot zelfstandige MariaDB-server, extra functies, zoals ingebouwde hoge Beschik baarheid, intelligentie en beheer. <br/><br/>
**MariaDB op de virtuele machine van Azure** valt in de branche categorie Infrastructure-as-a-Service (IaaS) en biedt u de mogelijkheid om MariaDB server uit te voeren in een volledig beheerde virtuele machine in de Azure-Cloud. Alle recente versies en edities van MariaDB kunnen worden geïnstalleerd op een virtuele machine met IaaS. Het belangrijkste verschil van Azure Database for MariaDB is dat MariaDB op Azure-Vm's controle over de data base-engine mogelijk maakt. Dit besturings element bevindt zich echter op de kosten van extra verantwoordelijkheid voor het beheren van de virtuele machines en talloze DBA-taken, zoals het onderhoud/patchen van database servers, het ontwerp voor herstel en hoge Beschik baarheid, enzovoort.

De belangrijkste verschillen tussen deze opties worden weer gegeven in de volgende tabel:

|            | **Azure Database for MariaDB** | **MariaDB op Azure Vm's**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | Biedt een SLA van 99,99% Beschik baarheid| Maxi maal 99,95% Beschik baarheid met 2 of meer exemplaren in dezelfde Beschikbaarheidsset. <br/>99,9% virtuele machine met één exemplaar met Premium Storage <br/> 99,99% met beschikbaarheids zone met 2 of meer exemplaren in twee of meer beschikbaarheids sets.<br/> Opmerking- [Sla voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **BESTURINGSSYSTEEM patches**        | Automatisch  | Beheerd door klanten |
|**MariaDB-patching**     | Automatisch  | Beheerd door klanten |
| **Hoge beschikbaarheid** | Het model voor hoge Beschik baarheid (HA) is gebaseerd op ingebouwde failover-mechanismen wanneer een onderbreking op knooppunt niveau optreedt. In dergelijke gevallen wordt er door de service automatisch een exemplaar gemaakt en wordt er opslag aan dit nieuwe exemplaar gekoppeld. | Hoge Beschik baarheid is ontworpen, geïmplementeerd, getest en onderhouden door de klant. Dit kan altijd betrekking hebben op een permanente (failover clustering of groeps replicatie), het registreren van de back-ups en transactionele replicatie, afhankelijk van de versie van de MariaDB-engine die in gebruik is.|
| **Zone redundantie** | Wordt momenteel niet ondersteund. | Virtuele Azure-machines kunnen worden ingesteld om te worden uitgevoerd in verschillende beschikbaarheids zones. Voor een on-premises oplossing wordt verwacht dat klanten hun eigen secundaire Data Center kunnen maken, beheren en onderhouden.|
| **Hybride scenario's** | Met [replicatie van inkomende gegevens](https://docs.microsoft.com/azure/MariaDB/concepts-data-in-replication) kunt u gegevens van een externe MariaDB-server synchroniseren met de Azure database for MariaDB-service. De externe server kan on-premises, in virtuele machines of een database service worden gehost door andere cloud providers.  <br/> <br/> Met de functie [replica lezen](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) kunt u gegevens van een Azure database for MariaDB server (Master) repliceren naar Maxi maal vijf alleen-lezen servers (replica's) binnen dezelfde Azure-regio of in verschillende regio's. Alleen-lezen replica's worden asynchroon bijgewerkt met behulp van binlog-replicatie technologie.   <br/> <br/> Opmerking-Lees replicatie in meerdere regio's is momenteel beschikbaar als open bare preview.| Beheerd door klanten <br/>
| **Back-up en herstel** | Maakt automatisch [Server back-ups](https://docs.microsoft.com/azure/MariaDB/concepts-backup#backups) en slaat ze op in een door de gebruiker geconfigureerd lokaal redundante of geografisch redundante opslag. De service maakt gebruik van volledige, differentiële en back-ups van transactie logboeken | Beheerd door klanten |
| **Database bewerkingen bewaken** | Hiermee kunnen klanten [waarschuwingen instellen](https://docs.microsoft.com/azure/MariaDB/concepts-monitoring) voor de database bewerking en reageren op drempel waarden. | Beheerd door klanten |
| **Geavanceerde thread beveiliging** | Biedt [geavanceerde beveiliging tegen bedreigingen](https://docs.microsoft.com/azure/MariaDB/howto-database-threat-protection-portal) die afwijkende activiteiten detecteert die ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases. <br/> <br/> Opmerking: geavanceerde thread beveiliging bevindt zich momenteel in de open bare preview.| Klanten moeten zichzelf zelf bouwen.
| **Back-ups (nood herstel)** | Slaat automatische back-ups op die door de gebruiker zijn geconfigureerd in [lokaal redundante of geografisch redundante opslag](https://docs.microsoft.com/azure/MariaDB/howto-restore-server-portal). Back-ups kunnen ook worden gebruikt om een server naar een bepaald tijdstip te herstellen. De Bewaar periode kan worden ingesteld van 7-35 dagen. Herstel kan worden uitgevoerd met behulp van de Azure Portal. <br/> | Volledig beheerd door de klant, inclusief, maar niet beperkt tot het plannen, testen, archiveren, bewaren en bewaren. Een extra optie is Azure Recovery Services-kluis gebruiken om back-ups te maken van virtuele machines in Azure en data bases op Vm's (in preview-versie). |
| **Aanbevolen prestatie** | Biedt klanten een proactieve [prestatie aanbeveling](https://techcommunity.microsoft.com/t5/Azure-Database-for-MariaDB/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) op basis van het gebruik van telemetrie voor het optimaliseren van werk belastingen. <br/> <br/> Opmerking: aanbevolen prestatie aanbeveling is momenteel beschikbaar als open bare preview. | Beheerd door klanten |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Zakelijke motivaties voor het kiezen van PaaS of IaaS

Er zijn verschillende factoren die van invloed kunnen zijn op uw beslissing om PaaS of IaaS te kiezen voor het hosten van uw MariaDB-data bases:

### <a name="cost"></a>Kosten

Of u nu een opstart procedure bent die is gebootsd voor kas of een team in een gevestigde onderneming die werkt onder strakke budget beperkingen, is een beperkte financiering vaak de primaire overweging bij het bepalen van de beste oplossing voor het hosten van uw data bases. In deze sectie worden de basis beginselen van facturering en licenties in azure beschreven met betrekking tot Azure Database for MariaDB en MariaDB op Azure-Vm's:

#### <a name="billing"></a>Billing

Op dit moment is Azure Database for MariaDB beschikbaar als een service in verschillende lagen met verschillende prijzen voor resources, die allemaal per uur worden gefactureerd tegen een vast tarief. Zie [inkoop model op basis van vCore](https://docs.microsoft.com/azure/MariaDB/concepts-pricing-tiers)voor de meest recente informatie over de huidige ondersteunde service lagen, reken grootten en opslag bedragen. U kunt Service lagen en reken grootten dynamisch aanpassen zodat deze overeenkomen met de verschillende doorvoer behoeften van uw toepassing. Er worden kosten in rekening gebracht voor uitgaand Internet verkeer tegen normale [tarieven voor gegevens overdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

Met Azure Database for MariaDB wordt de database software automatisch geconfigureerd, gepatched en bijgewerkt door micro soft, waardoor de beheer kosten worden verminderd. Bovendien kunt u met de [ingebouwde back-up](https://docs.microsoft.com/azure/MariaDB/concepts-backup)mogelijkheden aanzienlijk op kosten besparen, vooral wanneer u een groot aantal databases hebt. Met MariaDB op virtuele machines van Azure kunt u een van de MariaDB-versies kiezen en uitvoeren. Ongeacht de MariaDB-versie die u gebruikt, betaalt u voor de ingerichte virtuele machine en de kosten voor het specifieke licentie type dat wordt gebruikt voor MariaDB.

Azure Database for MariaDB biedt ingebouwde hoge Beschik baarheid voor alle soorten onderbrekingen op knooppunt niveau en behoudt nog steeds de SLA van 99,99% voor de service. Voor database hoge Beschik baarheid (HA) binnen Vm's moet de klant echter de opties voor hoge Beschik baarheid door lopen die beschikbaar zijn in de MariaDB-data base, zoals [MariaDB-replicatie](https://mariadb.com/kb/en/library/setting-up-replication/). Het gebruik van een ondersteunde optie voor hoge Beschik baarheid biedt geen extra SLA, maar biedt u de mogelijkheid om de beschik baarheid van > 99,99% data base te benutten tegen extra kosten en administratieve overhead.

Zie de volgende bronnen voor meer informatie over prijzen:
* [Azure Database for MariaDB prijzen](https://azure.microsoft.com/pricing/details/MariaDB/)
* [Prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure prijscalculator](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Beheer

Voor veel bedrijven is het besluit om over te stappen op een Cloud service, net zo veel over het offloaden van de complexiteit van het beheer. Met IaaS en PaaS beheert micro soft de onderliggende infra structuur en worden alle gegevens automatisch gerepliceerd om herstel na nood gevallen te bieden, de database software te configureren en bij te werken, de taak verdeling te beheren, en wordt een transparante failover uitgevoerd als er een Server fout.

* **Met Azure database for MariaDB**kunt u uw data base blijven beheren, maar u hoeft de data base-engine, het besturings systeem of de hardware niet langer te beheren. Voorbeelden van items die u kunt blijven beheren zijn onder meer databases en aanmeldingen, index en query afstemmen, en controle en beveiliging. Daarnaast is het configureren van hoge Beschik baarheid voor een ander Data Center Mini maal of geen configuratie of beheer.<br/><br/>
* **Met MariaDB op virtuele machines van Azure**hebt u volledige controle over de configuratie van het besturings systeem en het MariaDB-Server exemplaar. Met een VM kunt u bepalen wanneer u het besturings systeem en de database software bijwerkt/bijwerkt en wanneer u aanvullende software, zoals een antivirus toepassing, installeert. Sommige geautomatiseerde functies worden geleverd om patchen, het maken van een back-up en hoge beschikbaarheid aanzienlijk te vereenvoudigen. Bovendien kunt u de grootte van de VM, het aantal schijven en hun opslagconfiguraties beheren. Zie virtual machine and Cloud service sizes for Azure (Engelstalig) voor meer informatie.

### <a name="time-to-move-to-azure-br"></a>Tijd om over te stappen op Azure <br/>
* **Azure database for MariaDB** is de juiste oplossing voor toepassingen die in de Cloud zijn ontworpen wanneer de productiviteit van ontwikkel aars en snelle time-to-Market voor nieuwe oplossingen cruciaal zijn. Met een programmatische DBA-achtige functionaliteit is de service perfect voor Cloud Architects en ontwikkel aars, omdat deze de nood zaak voor het beheren van het onderliggende besturings systeem en de data base verlaagt.<br/><br/>
* **MariaDB op virtuele Azure-machines** is perfect voor bestaande of nieuwe toepassingen waarvoor MariaDB-data base of toegang tot functies in MariaDB-Data Base op Windows/Linux is vereist en u wilt voor komen dat er nieuwe on-premises hardware wordt aangestuurd. Deze optie is ook geschikt voor het migreren van bestaande on-premises toepassingen en data bases naar Azure as-is – voor gevallen waarin een Azure Database for MariaDB-exemplaar niet geschikt is. Omdat het niet nodig is om de presentatie-, toepassings-en gegevens lagen te wijzigen, bespaart u tijd en budget bij het opnieuw ontwerpen van uw bestaande oplossing. In plaats daarvan kunt u zich richten op het migreren van al uw oplossingen naar Azure en bij het adresseren van een aantal prestatie optimalisaties die mogelijk vereist zijn voor het Azure-platform.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure database for MariaDB prijzen](https://azure.microsoft.com/pricing/details/MariaDB/)
* Aan de slag gaan met het [maken van uw eerste server](https://review.docs.microsoft.com/azure/MariaDB/quickstart-create-MariaDB-server-database-using-azure-portal).


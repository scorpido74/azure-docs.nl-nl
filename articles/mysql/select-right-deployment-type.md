---
title: Het juiste implementatie type voor uw Azure Database for MySQL selecteren
description: In dit artikel wordt beschreven wat de overweging is die moet worden uitgevoerd voordat u verdergaat met IaaS (Infrastructure-as-a-Service) of platform-as-a-Service (PaaS) voor uw Azure Database for MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7bcbf379ea8d046c8c477dc716711a6a6ffa1dc9
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71813832"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Kies de optie juiste MySQL-server in azure

Met Azure kunnen de workloads van uw MySQL-server worden uitgevoerd in een gehoste infra structuur-VM (IaaS) of als een gehoste service (PaaS). PaaS heeft meerdere implementatie opties en er zijn service lagen binnen elke implementatie optie. Bij het kiezen tussen PaaS of IaaS, moet u bepalen of u uw Data Base wilt beheren, patches wilt Toep assen en back-ups wilt maken, of als u deze bewerkingen wilt delegeren naar Azure.</br>

Houd rekening met de volgende opties op basis van uw antwoord op deze vraag: <br/>

**Azure database for MySQL** is een volledig beheerde MySQL-data base-engine op basis van de stabiele versie van de Community Edition. De relationele data base-as-a-Service (DBaaS), die wordt gehost in de Azure-Cloud, valt in de branche categorie van platform-as-a-Service (PaaS). Met een beheerd exemplaar van MySQL op Azure kunt u ingebouwde functies en functionaliteit gebruiken waarvoor een uitgebreide configuratie is vereist bij het gebruik van MySQL server (on-premises of in een virtuele machine van Azure). Wanneer u MySQL als een service gebruikt, betaalt u op basis van de opties voor het omhoog of omlaag schalen van grotere kracht zonder onderbreking. Daarnaast heeft Azure Database for MySQL, in tegens telling tot zelfstandige MySQL-server, extra functies, zoals ingebouwde hoge Beschik baarheid, intelligentie en beheer. <br/><br/>
**MySQL op Azure VM** valt onder de branche categorie Infrastructure-as-a-Service (IaaS) en biedt u de mogelijkheid om mysql server uit te voeren in een volledig beheerde virtuele machine in de Azure-Cloud. Alle recente versies en edities van MySQL kunnen worden geïnstalleerd op een virtuele IaaS-machine. Het belangrijkste verschil van Azure Database for MySQL is dat MySQL op Azure-Vm's de controle over de data base-engine mogelijk maakt. Dit besturings element bevindt zich echter op de kosten van extra verantwoordelijkheid voor het beheren van de virtuele machines en talloze DBA-taken, zoals het onderhoud/patchen van database servers, het ontwerp voor herstel en hoge Beschik baarheid, enzovoort.

De belangrijkste verschillen tussen deze opties worden weer gegeven in de volgende tabel:

|            | **Azure Database for MySQL** | **MySQL op virtuele Azure-machines**    |
|:-------------------|:-----------------------------|:--------------------|
| **SLA**                | Biedt een SLA van 99,99% Beschik baarheid| Maxi maal 99,95% Beschik baarheid met 2 of meer exemplaren in dezelfde Beschikbaarheidsset. <br/>99,9% virtuele machine met één exemplaar met Premium Storage <br/> 99,99% met beschikbaarheids zone met 2 of meer exemplaren in twee of meer beschikbaarheids sets.<br/> Opmerking- [Sla voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) |
| **BESTURINGSSYSTEEM patches**        | Automatisch  | Beheerd door klanten |
|**MySQL-patches**     | Automatisch  | Beheerd door klanten |
| **Hoge beschikbaarheid** | Het model voor hoge Beschik baarheid (HA) is gebaseerd op ingebouwde failover-mechanismen wanneer een onderbreking op knooppunt niveau optreedt. In dergelijke gevallen wordt er door de service automatisch een exemplaar gemaakt en wordt er opslag aan dit nieuwe exemplaar gekoppeld. | Hoge Beschik baarheid is ontworpen, geïmplementeerd, getest en onderhouden door de klant. Dit kan altijd betrekking hebben op een permanente (failover clustering of groeps replicatie), het registreren van de back-ups en transactionele replicatie, afhankelijk van de versie van de MySQL-engine die wordt gebruikt.|
| **Zone redundantie** | Wordt momenteel niet ondersteund. | Virtuele Azure-machines kunnen worden ingesteld om te worden uitgevoerd in verschillende beschikbaarheids zones. Voor een on-premises oplossing wordt verwacht dat klanten hun eigen secundaire Data Center kunnen maken, beheren en onderhouden.|
| **Hybride scenario's** | Met [replicatie van inkomende gegevens](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication) kunt u gegevens van een externe mysql-server synchroniseren met de Azure database for MySQL-service. De externe server kan on-premises, in virtuele machines of een database service worden gehost door andere cloud providers.  <br/> <br/> Met de functie [replica lezen](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) kunt u gegevens van een Azure database for mysql server (Master) repliceren naar Maxi maal vijf alleen-lezen servers (replica's) binnen dezelfde Azure-regio of in verschillende regio's. Alleen-lezen replica's worden asynchroon bijgewerkt met behulp van binlog-replicatie technologie.   <br/> <br/> Opmerking-Lees replicatie in meerdere regio's is momenteel beschikbaar als open bare preview.| Beheerd door klanten <br/>
| **Back-up en herstel** | Maakt automatisch [Server back-ups](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) en slaat ze op in een door de gebruiker geconfigureerd lokaal redundante of geografisch redundante opslag. De service maakt gebruik van volledige, differentiële en back-ups van transactie logboeken | Beheerd door klanten |
| **Database bewerkingen bewaken** | Hiermee kunnen klanten [waarschuwingen instellen](https://docs.microsoft.com/azure/mysql/concepts-monitoring) voor de database bewerking en reageren op drempel waarden. | Beheerd door klanten |
| **Geavanceerde thread beveiliging** | Biedt [geavanceerde beveiliging tegen bedreigingen](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal) die afwijkende activiteiten detecteert die ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases. | Klanten moeten zichzelf zelf bouwen.
| **Back-ups (nood herstel)** | Slaat automatische back-ups op die door de gebruiker zijn geconfigureerd in [lokaal redundante of geografisch redundante opslag](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal). Back-ups kunnen ook worden gebruikt om een server naar een bepaald tijdstip te herstellen. De Bewaar periode kan worden ingesteld van 7-35 dagen. Herstel kan worden uitgevoerd met behulp van de Azure Portal. <br/> | Volledig beheerd door de klant, inclusief, maar niet beperkt tot het plannen, testen, archiveren, bewaren en bewaren. Een extra optie is Azure Recovery Services-kluis gebruiken om back-ups te maken van virtuele machines in Azure en data bases op Vm's (in preview-versie). |
| **Aanbevolen prestatie** | Biedt klanten een proactieve [prestatie aanbeveling](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) op basis van het gebruik van telemetrie voor het optimaliseren van werk belastingen. | Beheerd door klanten |


## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Zakelijke motivaties voor het kiezen van PaaS of IaaS

Er zijn verschillende factoren die van invloed kunnen zijn op uw beslissing om PaaS of IaaS te kiezen voor het hosten van uw MySQL-data bases:

### <a name="cost"></a>Kosten

Of u nu een opstart procedure bent die is gebootsd voor kas of een team in een gevestigde onderneming die werkt onder strakke budget beperkingen, is een beperkte financiering vaak de primaire overweging bij het bepalen van de beste oplossing voor het hosten van uw data bases. In deze sectie worden de grond beginselen van facturering en licenties in azure beschreven met betrekking tot Azure Database for MySQL en MySQL op Azure Vm's:

#### <a name="billing"></a>Billing

Op dit moment is Azure Database for MySQL beschikbaar als een service in verschillende lagen met verschillende prijzen voor resources, die allemaal per uur worden gefactureerd tegen een vast tarief. Zie [inkoop model op basis van vCore](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)voor de meest recente informatie over de huidige ondersteunde service lagen, reken grootten en opslag bedragen. U kunt Service lagen en reken grootten dynamisch aanpassen zodat deze overeenkomen met de verschillende doorvoer behoeften van uw toepassing. Er worden kosten in rekening gebracht voor uitgaand Internet verkeer tegen normale [tarieven voor gegevens overdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

Met Azure Database for MySQL wordt de database software automatisch geconfigureerd, gepatched en bijgewerkt door micro soft, waardoor de beheer kosten worden verminderd. Bovendien kunt u met de [ingebouwde back-up](https://docs.microsoft.com/azure/mysql/concepts-backup)mogelijkheden aanzienlijk op kosten besparen, vooral wanneer u een groot aantal databases hebt. Met MySQL op virtuele Azure-machines kunt u de MySQL-versie kiezen en uitvoeren. Ongeacht de MySQL-versie die u gebruikt, betaalt u voor de ingerichte virtuele machine en de kosten voor het specifieke licentie type dat wordt gebruikt voor MySQL.

Azure Database for MySQL biedt ingebouwde hoge Beschik baarheid voor alle soorten onderbrekingen op knooppunt niveau en behoudt nog steeds de SLA van 99,99% voor de service. Voor database hoge Beschik baarheid (HA) binnen Vm's, moet de klant echter de opties voor hoge Beschik baarheid door lopen die beschikbaar zijn in de MySQL-data base, zoals [MySQL-replicatie](https://dev.mysql.com/doc/refman/8.0/en/replication.html). Het gebruik van een ondersteunde optie voor hoge Beschik baarheid biedt geen extra SLA, maar biedt u de mogelijkheid om de beschik baarheid van > 99,99% data base te benutten tegen extra kosten en administratieve overhead.

Zie de volgende bronnen voor meer informatie over prijzen:
* [Azure Database for MySQL prijzen](https://azure.microsoft.com/pricing/details/mysql/)
* [Prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure prijscalculator](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Beheer

Voor veel bedrijven is het besluit om over te stappen op een Cloud service, net zo veel over het offloaden van de complexiteit van het beheer. Met IaaS en PaaS beheert micro soft de onderliggende infra structuur en worden alle gegevens automatisch gerepliceerd om herstel na nood gevallen te bieden, de database software te configureren en bij te werken, de taak verdeling te beheren, en wordt een transparante failover uitgevoerd als er een Server fout.

* **Met Azure database for MySQL**kunt u uw data base blijven beheren, maar u hoeft de data base-engine, het besturings systeem of de hardware niet langer te beheren. Voorbeelden van items die u kunt blijven beheren zijn onder meer databases en aanmeldingen, index en query afstemmen, en controle en beveiliging. Daarnaast is het configureren van hoge Beschik baarheid voor een ander Data Center Mini maal of geen configuratie of beheer.<br/><br/>
* **Met MySQL op Azure-vm's**hebt u volledige controle over het besturings systeem en de configuratie van het MySQL-Server exemplaar. Met een VM kunt u bepalen wanneer u het besturings systeem en de database software bijwerkt/bijwerkt en wanneer u aanvullende software, zoals een antivirus toepassing, installeert. Sommige geautomatiseerde functies worden geleverd om patchen, het maken van een back-up en hoge beschikbaarheid aanzienlijk te vereenvoudigen. Bovendien kunt u de grootte van de VM, het aantal schijven en hun opslagconfiguraties beheren. Zie virtual machine and Cloud service sizes for Azure (Engelstalig) voor meer informatie.

### <a name="time-to-move-to-azure-br"></a>Tijd om over te stappen op Azure <br/>
* **Azure database for MySQL** is de juiste oplossing voor toepassingen die in de Cloud zijn ontworpen wanneer de productiviteit van ontwikkel aars en snelle time-to-Market voor nieuwe oplossingen cruciaal zijn. Met een programmatische DBA-achtige functionaliteit is de service perfect voor Cloud Architects en ontwikkel aars, omdat deze de nood zaak voor het beheren van het onderliggende besturings systeem en de data base verlaagt.<br/><br/>
* **MySQL op virtuele Azure-machines** is perfect voor bestaande of nieuwe toepassingen waarvoor een MySQL-data base of toegang tot functies in MySQL-data base in Windows/Linux is vereist en u wilt voor komen dat nieuwe on-premises hardware worden aangestuurd. Deze optie is ook geschikt voor het migreren van bestaande on-premises toepassingen en data bases naar Azure as-is – voor gevallen waarin een Azure Database for MySQL-exemplaar niet geschikt is. Omdat het niet nodig is om de presentatie-, toepassings-en gegevens lagen te wijzigen, bespaart u tijd en budget bij het opnieuw ontwerpen van uw bestaande oplossing. In plaats daarvan kunt u zich richten op het migreren van al uw oplossingen naar Azure en bij het adresseren van een aantal prestatie optimalisaties die mogelijk vereist zijn voor het Azure-platform.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure database for MySQL prijzen](https://azure.microsoft.com/pricing/details/mysql/)
* Aan de slag gaan met het [maken van uw eerste server](https://review.docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).


---
title: Het juiste implementatie type voor Azure Database for MySQL selecteren
description: In dit artikel wordt beschreven welke factoren u moet overwegen voordat u Azure Database for MySQL implementeert als Infrastructure as a Service (IaaS) of platform as a Service (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 5cdd65d5509d8f46f095d91c509a1fda288517c4
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132434"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Kies de optie juiste MySQL-server in azure

Met Azure kunnen de werk belasting van uw MySQL-server worden uitgevoerd in een gehoste VM-infra structuur als een service (IaaS) of als een gehoste platform as a Service (PaaS). PaaS heeft meerdere implementatie opties en er zijn service lagen binnen elke implementatie optie. Wanneer u kiest tussen IaaS en PaaS, moet u beslissen of u uw Data Base wilt beheren, patches wilt Toep assen en back-ups wilt maken, of als u deze bewerkingen wilt delegeren naar Azure.

Houd rekening met de volgende twee opties bij het nemen van uw beslissing:

- **Azure database for MySQL**. Deze optie is een volledig beheerde MySQL-data base-engine op basis van de stabiele versie van MySQL Community Edition. Deze relationele Database as a Service (DBaaS), die wordt gehost op het Azure-Cloud platform, valt in de branche categorie van PaaS.

  Met een beheerd exemplaar van MySQL op Azure kunt u ingebouwde functies gebruiken waarvoor een uitgebreide configuratie nodig is wanneer de MySQL-server on-premises of in een Azure-VM is.

  Wanneer u MySQL als een service gebruikt, betaalt u met opties voor het opschalen of uitschalen voor meer controle zonder onderbreking. En in tegens telling tot zelfstandige MySQL-server, heeft Azure Database for MySQL extra functies, zoals ingebouwde hoge Beschik baarheid, intelligentie en beheer.

- **MySQL op virtuele Azure-machines**. Deze optie valt buiten de branche categorie van IaaS. Met deze service kunt u MySQL-server uitvoeren in een volledig beheerde virtuele machine op het Azure-Cloud platform. Alle recente versies en edities van MySQL kunnen worden geïnstalleerd op een virtuele IaaS-machine.

  In het belangrijkste verschil van Azure Database for MySQL biedt MySQL op Azure Vm's controle over de data base-engine. Dit besturings element is echter gebaseerd op de kosten van verantwoordelijkheden voor het beheren van de Vm's en veel DBA-taken (data base Administration). Deze taken omvatten het onderhouden en patchen van database servers, database herstel en ontwerp met hoge Beschik baarheid.

De belangrijkste verschillen tussen deze opties worden weer gegeven in de volgende tabel:

|            | Azure Database for MySQL | MySQL op virtuele Azure-machines    |
|:-------------------|:-----------------------------|:--------------------|
| Serviceovereenkomst (SLA)                | Biedt een SLA van 99,99% Beschik baarheid| Maxi maal 99,95% Beschik baarheid met twee of meer exemplaren in dezelfde beschikbaarheidsset.<br/><br/>99,9% Beschik baarheid met een virtuele machine met één exemplaar met Premium Storage.<br/><br/>99,99% met Beschikbaarheidszones met meerdere exemplaren in meerdere beschikbaarheids sets.<br/><br/>Zie de [virtual machines Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Patches besturings systeem        | Automatisch  | Beheerd door klanten |
| MySQL-patches     | Automatisch  | Beheerd door klanten |
| Hoge beschikbaarheid | Het model voor hoge Beschik baarheid (HA) is gebaseerd op ingebouwde failover-mechanismen voor wanneer een onderbreking op knooppunt niveau optreedt. In dergelijke gevallen maakt de service automatisch een nieuw exemplaar en koppelt de opslag aan dit exemplaar. | Klanten bouwen, implementeren, testen en onderhouden hoge Beschik baarheid. Mogelijkheden zijn onder meer Failover Clustering, altijd op groeps replicatie, logboek verzending of transactionele replicatie.|
| Zone redundantie | Momenteel niet ondersteund | Virtuele Azure-machines kunnen worden ingesteld om te worden uitgevoerd in verschillende beschikbaarheids zones. Voor een on-premises oplossing moeten klanten hun eigen secundaire Data Center maken, beheren en onderhouden.|
| Hybride scenario's | Met [replicatie van inkomende gegevens](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)kunt u gegevens van een externe mysql-server synchroniseren met de Azure database for MySQL-service. De externe server kan on-premises, in virtuele machines of een database service worden gehost door andere cloud providers.<br/><br/> Met de functie [replica lezen](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) kunt u gegevens van een Azure database for MySQL hoofd server repliceren naar Maxi maal vijf alleen-lezen replica servers. De replica's bevinden zich in dezelfde Azure-regio of in verschillende regio's. Alleen-lezen replica's worden asynchroon bijgewerkt met behulp van binlog-replicatie technologie.| Beheerd door klanten
| Back-ups maken en herstellen | Maakt automatisch [Server back-ups](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) en slaat deze op in door de gebruiker geconfigureerde opslag die lokaal redundant of geo-redundant is. De service maakt gebruik van volledige, differentiële en back-ups van transactie logboeken | Beheerd door klanten |
| Database bewerkingen bewaken | Biedt klanten de mogelijkheid om [waarschuwingen](https://docs.microsoft.com/azure/mysql/concepts-monitoring) in te stellen voor de database bewerking en om te reageren op drempel waarden. | Beheerd door klanten |
| Advanced Threat Protection | Biedt [geavanceerde beveiliging tegen bedreigingen](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal). Deze beveiliging detecteert afwijkende activiteiten die ongebruikelijke en potentieel schadelijke pogingen voor toegang tot of exploiten van data bases aanduiden. | Klanten moeten deze bescherming zelf bouwen.
| Herstel na noodgeval | Hiermee worden automatische back-ups opgeslagen in [lokaal redundante of geografisch redundante opslag](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)die door de gebruiker is geconfigureerd. Met back-ups kunt u ook een server herstellen naar een bepaald tijdstip. De retentie tijd is een periode van 7 tot 35 dagen. De herstel bewerking wordt uitgevoerd met behulp van de Azure Portal. | Volledig beheerd door klanten. Verantwoordelijkheden zijn onder andere, maar zijn niet beperkt tot het plannen, testen, archiveren, opslaan en bewaren. Een extra optie is een Azure Recovery Services kluis gebruiken om back-ups te maken van Azure Vm's en data bases op Vm's. Deze optie is beschikbaar als preview-versie. |
| Aanbevelingen voor prestaties | Biedt klanten [aanbevelingen voor prestaties](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) op basis van door het systeem gegenereerde gebruiks logboek bestanden. De aanbevelingen helpen bij het optimaliseren van werk belastingen. | Beheerd door klanten |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Zakelijke motivaties voor het kiezen van PaaS of IaaS

Er zijn verschillende factoren die van invloed kunnen zijn op uw beslissing om PaaS of IaaS te kiezen voor het hosten van uw MySQL-data bases.

### <a name="cost"></a>Kosten

Beperkte financiering is vaak de primaire overweging voor het bepalen van de beste oplossing voor het hosten van uw data bases. Dit is waar, of u nu een opstart met weinig geld of een team in een gevestigde onderneming bent die werkt onder strakke budget beperkingen. In deze sectie worden de basis beginselen van facturering en licenties in azure beschreven, omdat deze van toepassing zijn op Azure Database for MySQL en MySQL op virtuele Azure-machines.

#### <a name="billing"></a>Billing

Azure Database for MySQL is momenteel beschikbaar als een service in meerdere lagen met verschillende prijzen voor resources. Alle resources worden per uur gefactureerd tegen een vast bedrag. Zie [vCore-gebaseerd inkoop model](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)voor de meest recente informatie over de momenteel ondersteunde service lagen, reken grootten en opslag bedragen. U kunt Service lagen en reken grootten dynamisch aanpassen zodat deze overeenkomen met de verschillende doorvoer behoeften van uw toepassing. U wordt gefactureerd voor uitgaand Internet verkeer tegen normale [tarieven voor gegevens overdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

Met Azure Database for MySQL worden de database software automatisch geconfigureerd en bijgewerkt door micro soft. Deze geautomatiseerde acties verminderen de beheer kosten. Daarnaast heeft Azure Database for MySQL [ingebouwde back-](https://docs.microsoft.com/azure/mysql/concepts-backup) upfunctionaliteiten. Met deze mogelijkheden kunt u aanzienlijke kosten besparingen realiseren, met name wanneer u een groot aantal data bases hebt. In tegens telling tot de virtuele machines van Azure kunt u met MySQL een MySQL-versie kiezen en uitvoeren. Ongeacht welke MySQL-versie u gebruikt, betaalt u voor de ingerichte virtuele machine en de kosten voor het specifieke MySQL-licentie type dat wordt gebruikt.

Azure Database for MySQL biedt ingebouwde hoge Beschik baarheid voor elk soort onderbreking op knooppunt niveau, terwijl de SLA-garantie van 99,99% voor de service nog steeds wordt gehandhaafd. Voor een hoge Beschik baarheid van de data base binnen Vm's moeten klanten echter de opties voor hoge Beschik baarheid, zoals [MySQL-replicatie](https://dev.mysql.com/doc/refman/8.0/en/replication.html) , gebruiken die beschikbaar zijn in een MySQL-data base. Het gebruik van een ondersteunde optie voor hoge Beschik baarheid biedt geen aanvullende SLA. Maar dit biedt u meer dan 99,99% Beschik baarheid van de data base tegen extra kosten en administratieve overhead.

Zie de volgende artikelen voor meer informatie over prijzen:
* [Azure Database for MySQL prijzen](https://azure.microsoft.com/pricing/details/mysql/)
* [Prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Beheer

Voor veel bedrijven is het besluit om over te stappen op een Cloud service, net als bij het overdragen van de complexiteit van het beheer. Met IaaS en PaaS, micro soft:

- Beheert de onderliggende infra structuur.
- Repliceert automatisch alle gegevens om herstel na nood gevallen mogelijk te maken.
- Hiermee wordt de database software geconfigureerd en bijgewerkt.
- Beheert de taak verdeling.
- Voert een transparante failover uit als er een server fout is opgetreden.

In de volgende lijst worden administratieve aandachtspunten voor elke optie beschreven:

* Met Azure Database for MySQL kunt u uw data base blijven beheren. Maar u hoeft de data base-engine, het besturings systeem of de hardware niet langer te beheren. Voor beelden van items die u kunt blijven beheren, zijn onder andere:

  - Databases
  - Aanmelden
  - Indexafstemming
  - Query afstemmen
  - Controleren
  - Beveiliging

  Daarnaast is het configureren van hoge Beschik baarheid voor een ander Data Center Mini maal, zodat er geen configuratie of beheer nodig is.

* Met MySQL op Azure Vm's hebt u volledige controle over het besturings systeem en de configuratie van het MySQL-Server exemplaar. Met een VM kunt u bepalen wanneer u het besturings systeem en de database software bijwerkt of bijwerkt. U beslist ook wanneer u extra software, zoals een antivirus toepassing, installeert. Sommige geautomatiseerde functies zijn beschikbaar om patches, back-ups en hoge Beschik baarheid aanzienlijk te vereenvoudigen. U kunt de grootte van de virtuele machine, het aantal schijven en de opslag configuraties beheren. Zie [Virtual machine and Cloud service sizes for Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)(Engelstalig) voor meer informatie.

### <a name="time-to-move-to-azure"></a>Tijd om over te stappen op Azure

* Azure Database for MySQL is de juiste oplossing voor toepassingen die in de Cloud zijn ontworpen wanneer ontwikkel aars productiviteit en snelle time-to-Market voor nieuwe oplossingen cruciaal zijn. Met een programmatische functionaliteit zoals DBA is de service geschikt voor Cloud architecten en-ontwikkel aars, omdat deze de nood zaak voor het beheren van het onderliggende besturings systeem en de data base verlaagt.

* Als u de tijd en kosten voor het verkrijgen van nieuwe on-premises hardware wilt voor komen, is MySQL op Azure Vm's de juiste oplossing voor toepassingen waarvoor een MySQL-data base of toegang tot MySQL-functies in Windows of Linux is vereist. Deze oplossing is ook geschikt voor het migreren van bestaande on-premises toepassingen en data bases naar Azure, voor gevallen waarin Azure Database for MySQL een slechte plaats is.

  Omdat het niet nodig is om de presentatie-, toepassings-en gegevens lagen te wijzigen, bespaart u tijd en budget bij het opnieuw ontwerpen van uw bestaande oplossing. In plaats daarvan kunt u zich richten op het migreren van al uw oplossingen naar Azure en het adresseren van een aantal prestatie optimalisaties die het Azure-platform mogelijk vereist.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure database for MySQL prijzen](https://azure.microsoft.com/pricing/details/MySQL/).
* Aan de slag gaan met het [maken van uw eerste server](https://review.docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal).

---
title: Het juiste implementatietype selecteren - Azure Database voor MySQL
description: In dit artikel wordt beschreven met welke factoren u moet rekening houden voordat u Azure Database voor MySQL implementeert als infrastructuur als een service (IaaS) of platform as a service (PaaS).
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 50bcd42189b1bcc945d726277975892f07f1baa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255538"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>Kies de juiste MySQL Server-optie in Azure

Met Azure kunnen uw MySQL-serverworkloads worden uitgevoerd in een gehoste virtual machine-infrastructuur als service (IaaS) of als gehost platform as a service (PaaS). PaaS heeft meerdere implementatieopties en er zijn servicelagen binnen elke implementatieoptie. Wanneer u kiest tussen IaaS en PaaS, moet u beslissen of u uw database wilt beheren, patches wilt toepassen en back-ups wilt maken of dat u deze bewerkingen wilt delegeren aan Azure.

Overweeg bij het nemen van uw beslissing de volgende twee opties:

- **Azure-database voor MySQL**. Deze optie is een volledig beheerde MySQL-databaseengine op basis van de stabiele versie van De MySQL-community-editie. Deze relationele database as a service (DBaaS), gehost op het Azure-cloudplatform, valt in de branchecategorie van PaaS.

  Met een beheerde instantie van MySQL op Azure u ingebouwde functies gebruiken die anders uitgebreide configuratie vereisen wanneer MySQL Server on-premises of in een Azure VM is.

  Wanneer u MySQL als service gebruikt, betaalt u naar keuze met opties om op te schalen of uit te schalen voor meer controle zonder onderbreking. En in tegenstelling tot standalone MySQL Server, heeft Azure Database for MySQL extra functies zoals ingebouwde hoge beschikbaarheid, intelligentie en beheer.

- **MySQL op Azure VM's**. Deze optie valt in de industriecategorie van IaaS. Met deze service u MySQL Server uitvoeren in een volledig beheerde virtuele machine op het Azure-cloudplatform. Alle recente versies en edities van MySQL kunnen worden geïnstalleerd op een IaaS virtuele machine.

  In het belangrijkste verschil met Azure Database voor MySQL biedt MySQL op Azure VM's controle over de databaseengine. Deze controle gaat echter ten koste van de verantwoordelijkheid voor het beheer van de VM's en veel dba-taken (databasebeheer). Deze taken omvatten het onderhouden en patchen van databaseservers, databaseherstel en ontwerp met hoge beschikbaarheid.

De belangrijkste verschillen tussen deze opties worden vermeld in de volgende tabel:

|            | Azure Database for MySQL | MySQL op Azure VM's    |
|:-------------------|:-----------------------------|:--------------------|
| Service-level overeenkomst (SLA)                | Aanbiedingen SLA van 99,99% beschikbaarheid| Tot 99,95% beschikbaarheid met twee of meer exemplaren in dezelfde beschikbaarheidsset.<br/><br/>99,9% beschikbaarheid met een enkele instantie VM met behulp van premium opslag.<br/><br/>99,99% met beschikbaarheidszones met meerdere exemplaren in meerdere beschikbaarheidssets.<br/><br/>Zie de [SLA voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). |
| Patchen van het besturingssysteem        | Automatisch  | Beheerd door klanten |
| MySQL-patchen     | Automatisch  | Beheerd door klanten |
| Hoge beschikbaarheid | Het HA-model (High Availability) is gebaseerd op ingebouwde failovermechanismen voor wanneer een onderbreking op nodeniveau optreedt. In dergelijke gevallen maakt de service automatisch een nieuwe instantie en koppelt de opslag aan dit exemplaar. | Klanten ontwerpen, implementeren, testen en onderhouden een hoge beschikbaarheid. Mogelijkheden kunnen clustering, replicatie etc. omvatten.|
| Zoneredundantie | Momenteel niet ondersteund | Azure VM's kunnen worden ingesteld om in verschillende beschikbaarheidszones uit te voeren. Voor een on-premises oplossing moeten klanten hun eigen secundaire datacenter maken, beheren en onderhouden.|
| Hybride scenario's | Met [Gegevens-in-replicatie](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)u gegevens van een externe MySQL-server synchroniseren met de Azure Database for MySQL-service. De externe server kan on-premises zijn, in virtuele machines of een databaseservice die wordt gehost door andere cloudproviders.<br/><br/> Met de [leesreplicafunctie](https://docs.microsoft.com/azure/mysql/concepts-read-replicas) u gegevens uit een Azure Database voor MySQL-hoofdserver repliceren naar maximaal vijf alleen-lezen replicaservers. De replica's bevinden zich binnen dezelfde Azure-regio of in verschillende regio's. Alleen-lezen replica's worden asynchroon bijgewerkt met behulp van binlog-replicatietechnologie.| Beheerd door klanten
| Back-up en herstel | Hiermee worden automatisch [serverback-ups](https://docs.microsoft.com/azure/mysql/concepts-backup#backups) gemaakt en deze opgeslagen in door de gebruiker geconfigureerde opslag die lokaal redundant of georedundant is. De service neemt volledige, differentiële en transactielogboekback-ups | Beheerd door klanten |
| Databasebewerkingen controleren | Biedt klanten de mogelijkheid om [waarschuwingen in](https://docs.microsoft.com/azure/mysql/concepts-monitoring) te stellen op de databasebewerking en te handelen bij het bereiken van drempels. | Beheerd door klanten |
| Advanced Threat Protection | Biedt [geavanceerde bescherming tegen bedreigingen.](https://docs.microsoft.com/azure/mysql/howto-database-threat-protection-portal) Deze bescherming detecteert afwijkende activiteiten die duiden op ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van databases. | Klanten moeten deze bescherming zelf opbouwen.
| Herstel na noodgeval | Slaat geautomatiseerde back-ups op in lokaal [redundante of georedundante opslag](https://docs.microsoft.com/azure/mysql/howto-restore-server-portal)die door de gebruiker is geconfigureerd. Back-ups kunnen een server ook herstellen naar een tijdstip. De bewaartermijn bedraagt 7 tot 35 dagen. De restauratie wordt uitgevoerd met behulp van de Azure-portal. | Volledig beheerd door klanten. Verantwoordelijkheden zijn onder meer, maar zijn niet beperkt tot planning, testen, archiveren, opslaan en bewaren. Een extra optie is het gebruik van een Azure Recovery Services-kluis om back-ups te maken van Azure VM's en databases op VM's. Deze optie is in preview. |
| Aanbevelingen voor prestaties | Biedt klanten [prestatieaanbevelingen](https://techcommunity.microsoft.com/t5/Azure-Database-for-MySQL/Azure-brings-intelligence-and-high-performance-to-Azure-Database/ba-p/769110) op basis van door het systeem gegenereerde systeemlogboekbestanden. De aanbevelingen helpen om workloads te optimaliseren. | Beheerd door klanten |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>Zakelijke motivaties voor het kiezen van PaaS of IaaS

Er zijn verschillende factoren die van invloed kunnen zijn op uw beslissing om PaaS of IaaS te kiezen om uw MySQL-databases te hosten.

### <a name="cost"></a>Kosten

Beperkte financiering is vaak de primaire overweging die de beste oplossing voor het hosten van uw databases bepaalt. Dit is waar of u nu een startup bent met weinig geld of een team in een gevestigd bedrijf dat werkt onder krappe budgetbeperkingen. In deze sectie worden de basisprincipes van facturering en licenties in Azure beschreven die van toepassing zijn op Azure Database voor MySQL en MySQL op Azure VM's.

#### <a name="billing"></a>Billing

Azure Database voor MySQL is momenteel beschikbaar als service in verschillende lagen met verschillende prijzen voor resources. Alle resources worden per uur gefactureerd tegen een vast tarief. Zie [vCore-gebaseerd inkoopmodel](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers)voor de meest recente informatie over de momenteel ondersteunde servicelagen, rekengroottes en opslagbedragen. U servicelagen en rekenformaten dynamisch aanpassen aan de uiteenlopende doorvoerbehoeften van uw toepassing. U wordt gefactureerd voor uitgaand internetverkeer tegen regelmatige [gegevensoverdrachtstarieven.](https://azure.microsoft.com/pricing/details/data-transfers/)

Met Azure Database voor MySQL configureert, patches en upgrades van Microsoft automatisch de databasesoftware. Deze geautomatiseerde acties verlagen uw administratiekosten. Azure Database voor MySQL heeft ook [ingebouwde back-upmogelijkheden.](https://docs.microsoft.com/azure/mysql/concepts-backup) Deze mogelijkheden helpen u aanzienlijke kostenbesparingen te realiseren, vooral wanneer u een groot aantal databases hebt. Met MySQL op Azure VM's u daarentegen elke MySQL-versie kiezen en uitvoeren. Het maakt niet uit welke MySQL-versie u gebruikt, u betaalt voor de ingerichte VM en de kosten voor het specifieke MySQL-licentietype dat wordt gebruikt.

Azure Database voor MySQL biedt ingebouwde hoge beschikbaarheid voor elke vorm van onderbreking op nodeniveau, terwijl de 99,99% SLA-garantie voor de service behouden blijft. Voor databasehoge beschikbaarheid binnen VM's moeten klanten echter de opties voor hoge beschikbaarheid gebruiken, zoals [MySQL-replicatie](https://dev.mysql.com/doc/refman/8.0/en/replication.html) die beschikbaar zijn in een MySQL-database. Als u een ondersteunde optie voor hoge beschikbaarheid gebruikt, wordt er geen extra SLA weergegeven. Maar het laat u wel meer dan 99,99% database beschikbaarheid te bereiken tegen extra kosten en administratieve overhead.

Zie de volgende artikelen voor meer informatie over de prijzen:
* [Azure Database voor MySQL-prijzen](https://azure.microsoft.com/pricing/details/mysql/)
* [Prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Beheer

Voor veel bedrijven gaat de beslissing om over te stappen op een cloudservice net zo zeer over het ontladen van de complexiteit van het beheer als over kosten. Met IaaS en PaaS, Microsoft:

- Beheert de onderliggende infrastructuur.
- Repliceert automatisch alle gegevens om herstel na noodgevallen te bieden.
- Configureert en upgrades van de database software.
- Beheert het balanceren van de lasten.
- Werkt transparant fail-over als er een serverstoring optreedt.

In de volgende lijst worden administratieve overwegingen voor elke optie beschreven:

* Met Azure Database voor MySQL u uw database blijven beheren. Maar u hoeft niet langer de database-engine, het besturingssysteem of de hardware te beheren. Voorbeelden van items die u blijven beheren, zijn:

  - Databases
  - Aanmelden
  - Indexafstemming
  - Queryafstemming
  - Controleren
  - Beveiliging

  Bovendien vereist het configureren van hoge beschikbaarheid voor een ander datacenter minimale tot geen configuratie of beheer.

* Met MySQL op Azure VM's hebt u volledige controle over het besturingssysteem en de MySQL-serverinstantieconfiguratie. Met een VM bepaalt u wanneer u het besturingssysteem en de databasesoftware wilt bijwerken of upgraden. U bepaalt ook wanneer u extra software wilt installeren, zoals een antivirustoepassing. Sommige geautomatiseerde functies zijn bedoeld om patchen, back-up en hoge beschikbaarheid sterk te vereenvoudigen. U de grootte van de VM, het aantal schijven en de opslagconfiguraties ervan beheren. Zie [Virtuele machine- en cloudservicegroottes voor Azure voor](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)meer informatie.

### <a name="time-to-move-to-azure"></a>Tijd om over te stappen naar Azure

* Azure Database voor MySQL is de juiste oplossing voor cloud-ontworpen toepassingen wanneer de productiviteit van ontwikkelaars en snelle time-to-market voor nieuwe oplossingen van cruciaal belang zijn. Met programmatische functionaliteit die is zoals DBA, de dienst is geschikt voor cloud architecten en ontwikkelaars, omdat het vermindert de noodzaak voor het beheer van het onderliggende besturingssysteem en database.

* Wanneer u de tijd en kosten van het aanschaffen van nieuwe on-premises hardware wilt vermijden, is MySQL op Azure VM's de juiste oplossing voor toepassingen waarvoor een MySQL-database of toegang tot MySQL-functies op Windows of Linux vereist is. Deze oplossing is ook geschikt voor het migreren van bestaande on-premises toepassingen en databases naar Azure intact, voor gevallen waarin Azure Database voor MySQL slecht past.

  Omdat u de presentatie-, toepassings- en gegevenslagen niet hoeft te wijzigen, bespaart u tijd en budget bij het opnieuw ontwerpen van uw bestaande oplossing. In plaats daarvan u zich richten op het migreren van al uw oplossingen naar Azure en het aanpakken van een aantal prestatieoptimalisaties die het Azure-platform mogelijk nodig heeft.

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Database for MySQL-prijzen](https://azure.microsoft.com/pricing/details/MySQL/).
* Ga aan de slag door [uw eerste server te maken.](https://docs.microsoft.com/azure/MySQL/quickstart-create-MySQL-server-database-using-azure-portal)

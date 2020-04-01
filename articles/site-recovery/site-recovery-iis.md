---
title: Noodherstel instellen voor een IIS-webapp met Azure Site Recovery
description: Meer informatie over het repliceren van virtuele machines van iIS-webfarms met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: dfed398124ca20771e169f6f9e7d08d4d799ee1e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478285"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Noodherstel instellen voor een op meerdere niveaus IIS-gebaseerde webtoepassing

Applicatiesoftware is de motor van de bedrijfsproductiviteit in een organisatie. Verschillende webapplicaties kunnen verschillende doeleinden in een organisatie dienen. Sommige toepassingen, zoals toepassingen die worden gebruikt voor salarisverwerking, financiële toepassingen en klantgerichte websites, kunnen van cruciaal belang zijn voor een organisatie. Om productiviteitsverlies te voorkomen, is het belangrijk dat de organisatie deze toepassingen continu operationeel heeft. Wat nog belangrijker is, het hebben van deze toepassingen consistent beschikbaar kan helpen voorkomen dat schade aan het merk of imago van de organisatie.

Kritieke webtoepassingen worden meestal ingesteld als toepassingen met meerdere lagen: het web, de database en de toepassing bevinden zich op verschillende niveaus. De toepassingen kunnen niet alleen over verschillende lagen worden verspreid, maar ook meerdere servers in elke laag gebruiken om het verkeer te laden. Bovendien kunnen de toewijzingen tussen verschillende lagen en op de webserver gebaseerd zijn op statische IP-adressen. Bij failover moeten sommige van deze toewijzingen worden bijgewerkt, vooral als meerdere websites op de webserver zijn geconfigureerd. Als webtoepassingen TLS gebruiken, moet u certificaatbindingen bijwerken.

Traditionele herstelmethoden die niet zijn gebaseerd op replicatie, omvatten een back-up van verschillende configuratiebestanden, registerinstellingen, bindingen, aangepaste componenten (COM of .NET), inhoud en certificaten. Bestanden worden hersteld via een reeks handmatige stappen. De traditionele herstelmethoden voor het maken van back-ups en het handmatig herstellen van bestanden zijn omslachtig, foutgevoelig en niet schaalbaar. U bijvoorbeeld gemakkelijk vergeten een back-up van certificaten te maken. Na failover, je bent vertrokken met geen andere keuze dan om nieuwe certificaten te kopen voor de server.

Een goede oplossing voor noodherstel ondersteunt het modelleren van herstelplannen voor complexe toepassingsarchitecturen. U moet ook aangepaste stappen kunnen toevoegen aan het herstelplan om toepassingstoewijzingen tussen lagen te verwerken. Als er een ramp is, bieden toepassingstoewijzingen een oplossing met één klik, sure-shot die helpt leiden tot een lagere RTO.

In dit artikel wordt beschreven hoe u een webtoepassing die is gebaseerd op Internet Information Services (IIS) beveiligen met [Azure Site Recovery.](site-recovery-overview.md) Het artikel bevat aanbevolen procedures voor het repliceren van een webtoepassing met drie lagen, iis naar Azure, hoe u een noodhersteloefening doen en hoe u falen over de toepassing naar Azure.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u de volgende taken moet uitvoeren:

* [Een virtuele machine repliceren naar Azure](vmware-azure-tutorial.md)
* [Een herstelnetwerk ontwerpen](site-recovery-network-design.md)
* [Een testfailover naar Azure](site-recovery-test-failover-to-azure.md)
* [Een failover naar Azure](site-recovery-failover.md)
* [Een domeincontroller repliceren](site-recovery-active-directory.md)
* [SQL Server repliceren](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Implementatiepatronen
Een iis-gebaseerde webtoepassing volgt doorgaans een van de volgende implementatiepatronen:

**Implementatiepatroon 1**

Een IIS-gebaseerde webfarm met Arr (Application Request Routing), een IIS-server en SQL Server.

![Diagram van een IIS-gebaseerde webfarm met drie lagen](./media/site-recovery-iis/deployment-pattern1.png)

**Implementatiepatroon 2**

Een IIS-gebaseerde webfarm met ARR, een IIS-server, een toepassingsserver en SQL Server.

![Diagram van een IIS-gebaseerde webfarm met vier lagen](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Ondersteuning voor Site Recovery

Voor de voorbeelden in dit artikel gebruiken we virtuele VMware-machines met IIS 7.5 op Windows Server 2012 R2 Enterprise. Omdat replicatie van siteherstel niet toepassingsspecifiek is, worden de aanbevelingen in dit artikel naar verwachting toegepast in de scenario's in de volgende tabel en voor verschillende versies van IIS.

### <a name="source-and-target"></a>Bron en doel

Scenario | Op een secundaire site | In Azure
--- | --- | ---
Hyper-V | Ja | Ja
VMware | Ja | Ja
Fysieke server | Nee | Ja
Azure|N.v.t.|Ja

## <a name="replicate-virtual-machines"></a>Virtuele machines repliceren

Als u wilt beginnen met het repliceren van alle virtuele iIS-webfarmmachines naar Azure, volgt u de richtlijnen in [Failover testen naar Azure in Siterecovery](site-recovery-test-failover-to-azure.md).

Als u een statisch IP-adres gebruikt, u het IP-adres opgeven dat u wilt dat de virtuele machine inneemt. Als u het IP-adres wilt instellen, gaat u naar**TARGET-IP** **voor compute- en netwerkinstellingen** > .

![Schermafbeelding van het instellen van het doel-IP in het deelvenster Gegevensberekening en netwerk van siteherstel](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Een herstelplan maken
Een herstelplan ondersteunt de volgorde van verschillende lagen in een multi-tier toepassing tijdens een failover. Sequencing helpt de consistentie van de toepassing te behouden. Wanneer u een herstelplan maakt voor een multi-tier webtoepassing, voert u de stappen uit die zijn beschreven in [Een herstelplan maken met Siteherstel](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failovergroepen
Een typische IIS-webtoepassing met meerdere lagen bestaat uit de volgende componenten:
* Een databaselaag met virtuele SQL-machines.
* De weblaag, die bestaat uit een IIS-server en een toepassingslaag. 

Virtuele machines toevoegen aan verschillende groepen op basis van de laag:

1. Maak een herstelplan. Voeg de virtuele machines van de databaselaag toe onder Groep 1. Dit zorgt ervoor dat virtuele machines in databaselagen als laatste worden afgesloten en als eerste worden opgehaald.
1. Voeg de virtuele machines van de toepassingslaag toe onder Groep 2. Dit zorgt ervoor dat virtuele machines van de toepassingslaag worden opgevoed nadat de databaselaag is ingesteld.
1. Voeg de virtuele machines van de weblaag toe in groep 3. Dit zorgt ervoor dat virtuele webtier-machines worden opgevoed nadat de toepassingslaag is opgehaald.
1. Voeg virtuele machines met lastbalans toe in groep 4. Dit zorgt ervoor dat virtuele machines met lastbalans worden opgehaald nadat de weblaag is opgehaald.

Zie [Het herstelplan aanpassen](site-recovery-runbook-automation.md#customize-the-recovery-plan)voor meer informatie .


### <a name="add-a-script-to-the-recovery-plan"></a>Een script toevoegen aan het herstelplan
Als u de IIS-webfarm correct wilt laten functioneren, moet u mogelijk een aantal bewerkingen uitvoeren op de virtuele Azure-machines na de failover of tijdens een testfailover. U sommige post-failoverbewerkingen automatiseren. U bijvoorbeeld de DNS-vermelding bijwerken, een sitebinding wijzigen of een verbindingstekenreeks wijzigen door overeenkomstige scripts toe te voegen aan het herstelplan. [Met een VMM-script toevoegen aan een herstelplan](site-recovery-how-to-add-vmmscript.md) wordt beschreven hoe u geautomatiseerde taken instelt met behulp van een script.

#### <a name="dns-update"></a>DNS-update
Als DNS is geconfigureerd voor dynamische DNS-update, werken virtuele machines de DNS meestal bij met het nieuwe IP-adres wanneer ze worden gestart. Als u een expliciete stap wilt toevoegen om DNS bij te werken met de nieuwe IP-adressen van de virtuele machines, voegt u een script toe [om IP bij te werken in DNS](https://aka.ms/asr-dns-update) als een post-failoveractie op herstelplangroepen.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Verbindingstekenreeks in de web.config van een toepassing
De verbindingstekenreeks geeft de database op waarmee de website communiceert. Als de verbindingstekenreeks de naam van de virtuele databasemachine draagt, zijn er geen verdere stappen nodig na de failover. De toepassing kan automatisch communiceren met de database. Als het IP-adres voor de virtuele databasemachine behouden blijft, hoeft de verbindingstekenreeks ook niet bij te werken. 

Als de verbindingstekenreeks verwijst naar de virtuele machine van de database met behulp van een IP-adres, moet deze worden bijgewerkt na de failover. De volgende verbindingstekenreeks verwijst bijvoorbeeld naar de database met het IP-adres 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Als u de verbindingstekenreeks in de weblaag wilt bijwerken, voegt u na groep 3 een [IIS-verbindingsupdatescript](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) toe in het herstelplan.

#### <a name="site-bindings-for-the-application"></a>Sitebindingen voor de toepassing
Elke site bestaat uit bindende informatie. De bindende informatie omvat het type binding, het IP-adres waarop de IIS-server luistert naar de aanvragen voor de site, het poortnummer en de hostnamen voor de site. Tijdens de failover moet u deze bindingen mogelijk bijwerken als er een wijziging is in het IP-adres dat eraan is gekoppeld.

> [!NOTE]
>
> Als u de sitebinding instelt op **Alle niet-toegewezen,** hoeft u deze bindende nafailer niet bij te werken. Als het IP-adres dat aan een site is gekoppeld, niet wordt gewijzigd na de failover, hoeft u de sitebinding niet bij te werken. (Het bewaren van het IP-adres is afhankelijk van de netwerkarchitectuur en subnetten die aan de primaire en herstelsites zijn toegewezen. Het bijwerken ervan is mogelijk niet haalbaar voor uw organisatie.)

![Schermafbeelding van het instellen van de TLS/SSL-binding](./media/site-recovery-iis/sslbinding.png)

Als u het IP-adres aan een site hebt gekoppeld, werkt u alle sitebindingen bij met het nieuwe IP-adres. Als u de sitebindingen wilt wijzigen, voegt u na groep 3 een [IIS-weblaagupdatescript](https://aka.ms/asr-web-tier-update-runbook-classic) toe in het herstelplan.

#### <a name="update-the-load-balancer-ip-address"></a>Het IP-adres van de load balancer bijwerken
Als u een virtuele ARR-machine hebt, voegt u na groep 4 een [IIS ARR-failoverscript](https://aka.ms/asr-iis-arrtier-failover-script-classic) toe om het IP-adres bij te werken.

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>TLS/SSL-certificaatbinding voor een HTTPS-verbinding
Een website kan een bijbehorend TLS/SSL-certificaat hebben dat zorgt voor een veilige communicatie tussen de webserver en de browser van de gebruiker. Als de website een HTTPS-verbinding heeft en ook een bijbehorende HTTPS-site heeft die bindend is voor het IP-adres van de IIS-server met een TLS/SSL-certificaatbinding, moet u een nieuwe sitebinding voor het certificaat toevoegen met het IP-adres van de virtuele machine van IIS na de failover.

Het TLS/SSL-certificaat kan worden uitgegeven tegen deze onderdelen:

* De volledig gekwalificeerde domeinnaam van de website.
* De naam van de server.
* Een wildcardcertificaat voor de domeinnaam.  
* Een IP-adres. Als het TLS/SSL-certificaat wordt uitgegeven tegen het IP-adres van de IIS-server, moet een ander TLS/SSL-certificaat worden uitgegeven tegen het IP-adres van de IIS-server op de Azure-site. Er moet een extra TLS-binding voor dit certificaat worden gemaakt. Daarom raden we aan om geen TLS/SSL-certificaat te gebruiken dat is uitgegeven tegen het IP-adres. Deze optie wordt minder veel gebruikt en zal binnenkort worden afgeschaft in overeenstemming met nieuwe certificaat autoriteit / browser forum wijzigingen.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>De afhankelijkheid tussen de weblaag en de toepassingslaag bijwerken
Als u een toepassingsspecifieke afhankelijkheid hebt die is gebaseerd op het IP-adres van de virtuele machines, moet u deze afhankelijkheid na failover bijwerken.

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

1. Selecteer in de Azure-portal de kluis Van Herstelservices.
2. Selecteer het herstelplan dat u hebt gemaakt voor de IIS-webfarm.
3. Selecteer **Failover testen**.
4. Als u het failoverproces wilt starten, selecteert u het herstelpunt en het virtuele Azure-netwerk.
5. Wanneer de secundaire omgeving is up, u validaties uitvoeren.
6. Wanneer validaties zijn voltooid, selecteert u **Validaties voltooien**om de failoveromgeving van de test schoon te maken .

Zie [Failover testen naar Azure in Siterecovery voor](site-recovery-test-failover-to-azure.md)meer informatie.

## <a name="run-a-failover"></a>Een failover uitvoeren

1. Selecteer in de Azure-portal de kluis Van Herstelservices.
1. Selecteer het herstelplan dat u hebt gemaakt voor de IIS-webfarm.
1. Selecteer **Failover**.
1. Als u het failoverproces wilt starten, selecteert u het herstelpunt.

Zie [Failover in Siteherstel](site-recovery-failover.md)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het repliceren van andere toepassingen](site-recovery-workload.md) met Site recovery.

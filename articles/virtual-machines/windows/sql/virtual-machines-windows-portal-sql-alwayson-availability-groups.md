---
title: Hoge beschikbaarheid instellen voor Azure Resource Manager VM's | Microsoft Documenten
description: In deze zelfstudie ziet u hoe u een groep Always On-beschikbaarheid maakt met virtuele Azure-machines in de Azure Resource Manager-modus.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d7c88e500886453fbfb53655748ccf7025ab7d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75374249"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Beschikbaarheidsgroepen altijd configureren in Azure Virtual Machines: Resource Manager

In deze zelfstudie ziet u hoe u een SQL Server-beschikbaarheidsgroep maakt die gebruikmaakt van virtuele azure-machines voor Azure Resource Manager. De zelfstudie gebruikt Azure-blades om een sjabloon te configureren. U de standaardinstellingen controleren, vereiste instellingen typen en de bladen in de portal bijwerken terwijl u deze zelfstudie doorloopt.

Met de volledige zelfstudie wordt een SQL Server-beschikbaarheidsgroep gemaakt op Azure Virtual Machines die de volgende elementen bevatten:

* Een virtueel netwerk met meerdere subnetten, waaronder een frontend en een backend subnet
* Twee domeincontrollers met een Active Directory-domein
* Twee virtuele machines die SQL Server uitvoeren en worden geïmplementeerd op het backend-subnet en zijn samengevoegd met het Active Directory-domein
* Een failovercluster met drie nodes met het quorummodel Knooppuntmeerderheid
* Een beschikbaarheidsgroep met twee synchrone commit replica's van een beschikbaarheidsdatabase

De volgende illustratie vertegenwoordigt de volledige oplossing.

![Labarchitectuur testen voor beschikbaarheidsgroepen in Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Alle resources in deze oplossing behoren tot één resourcegroep.

Bevestig het volgende voordat u deze zelfstudie start:

* U hebt al een Azure-account. Als je er geen hebt, [meld je dan aan voor een proefaccount.](https://azure.microsoft.com/pricing/free-trial/)
* U weet al hoe u de GUI moet gebruiken om een virtuele SQL Server-machine in te richten vanuit de virtuele machinegalerie. Zie [Een virtuele SQL Server-machine inrichten op Azure](virtual-machines-windows-portal-sql-server-provision.md)voor meer informatie.
* Je hebt al een goed begrip van beschikbaarheidsgroepen. Zie [Beschikbaarheidsgroepen (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)voor meer informatie.

> [!NOTE]
> Als u beschikbaarheidsgroepen wilt gebruiken met SharePoint, raadpleegt u SQL [Server 2012 Altijd beschikbaarheidsgroepen configureren voor SharePoint 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

Gebruik in deze zelfstudie de Azure-portal om:

* Kies de sjabloon Altijd aan in de portal.
* Controleer de sjablooninstellingen en werk een aantal configuratie-instellingen voor uw omgeving bij.
* Houd Azure in de gaten terwijl het de hele omgeving creëert.
* Maak verbinding met een domeincontroller en vervolgens met een server waarop SQL Server wordt uitgevoerd.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Het cluster vanuit de galerie inrichten
Azure biedt een galerijafbeelding voor de hele oplossing. Ga als u de sjabloon zoeken:

1. Meld u aan bij de Azure-portal met uw account.
2. Klik in de Azure-portal op **Een resource maken** om het deelvenster **Nieuw** te openen.
3. Zoek in het **nieuwe** deelvenster naar **AlwaysOn**.
   ![Alwayson-sjabloon zoeken](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Zoek SQL Server **AlwaysOn-cluster**in de zoekresultaten .
   ![Alwayson-sjabloon](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Kies **Resourcebeheer**in **Een implementatiemodel selecteren**.

### <a name="basics"></a>Basisbeginselen
Klik **op Basisbeginselen** en configureer de volgende instellingen:

* **De gebruikersnaam van** de beheerder is een gebruikersaccount met machtigingen voor domeinbeheerders en is lid van de vaste serverrol van SQL Server sysadmin op beide exemplaren van SQL Server. Gebruik **DomainAdmin**voor deze zelfstudie .
* **Wachtwoord** is het wachtwoord voor het domeinbeheerdersaccount. Gebruik een complex wachtwoord. Bevestig het wachtwoord.
* **Abonnement** is het abonnement dat Azure factureert om alle geïmplementeerde resources voor de beschikbaarheidsgroep uit te voeren. Als uw account meerdere abonnementen heeft, u een ander abonnement opgeven.
* **Resourcegroep** is de naam voor de groep waartoe alle Azure-resources behoren die door deze sjabloon zijn gemaakt. Gebruik **SQL-HA-RG**voor deze zelfstudie . Zie voor meer informatie [Overzicht van Azure Resource Manager](../../../azure-resource-manager/management/overview.md#resource-groups).
* **Locatie** is het Azure-gebied waar de zelfstudie de resources maakt. Kies een Azure-gebied.

De volgende schermafbeelding is een voltooid **basicsblad:**

![Basisbeginselen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klik op **OK**.

### <a name="domain-and-network-settings"></a>Domein- en netwerkinstellingen
Met deze Azure-galeriesjabloon wordt een domein- en domeincontrollers gemaakt. Het creëert ook een netwerk en twee subnetten. De sjabloon kan geen servers maken in een bestaand domein of virtueel netwerk. De volgende stap configureert de domein- en netwerkinstellingen.

Controleer op het blad **Domain en netwerkinstellingen** de vooraf ingestelde waarden voor de domein- en netwerkinstellingen:

* **Forestrootdomeinnaam** is de domeinnaam voor het Active Directory-domein dat het cluster host. Gebruik **contoso.com**voor de zelfstudie.
* **De naam van het virtuele netwerk** is de netwerknaam voor het virtuele Azure-netwerk. Gebruik voor de zelfstudie **autohaVNET**.
* **Subnetnaam Domeincontroller** is de naam van een deel van het virtuele netwerk dat de domeincontroller host. **Subnet-1 gebruiken**. In dit subnet wordt adresvoorvoegssel **10.0.0.0/24 gebruikt.**
* **Sql Server-subnetnaam** is de naam van een deel van het virtuele netwerk dat de servers host waarop SQL Server en de getuige voor bestandsshare worden uitgevoerd. **Subnet-2 gebruiken**. In dit subnet wordt adresvoorvoegssel **10.0.1.0/26 gebruikt.**

Zie [Virtueel netwerkoverzicht](../../../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele netwerken in Azure.  

De **domein- en netwerkinstellingen** moeten er uitzien als de volgende schermafbeelding:

![Domein- en netwerkinstellingen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Indien nodig u deze waarden wijzigen. Gebruik voor deze zelfstudie de vooraf ingestelde waarden.

Controleer de instellingen en klik op **OK**.

### <a name="availability-group-settings"></a>Instellingen voor beschikbaarheidsgroepen
Controleer in **groepsinstellingen beschikbaarheid**de vooraf ingestelde waarden voor de beschikbaarheidsgroep en de listener.

* De naam van de **beschikbaarheidsgroep** is de geclusterde resourcenaam voor de beschikbaarheidsgroep. Gebruik Voor deze zelfstudie **Contoso-ag**.
* **De naam van** de groep listener voor beschikbaarheid wordt gebruikt door het cluster en de interne load balancer. Clients die verbinding maken met SQL Server kunnen deze naam gebruiken om verbinding te maken met de juiste replica van de database. Gebruik voor deze zelfstudie **Contoso-listener**.
* **De serverlistenerpoort** voor beschikbaarheidsgroepen geeft de TCP-poort van de SQL Server-listener op. Gebruik voor deze zelfstudie de standaardpoort **1433**.

Indien nodig u deze waarden wijzigen. Gebruik voor deze zelfstudie de vooraf ingestelde waarden.  

![instellingen voor beschikbaarheidsgroepen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klik op **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Virtuele machinegrootte, opslaginstellingen
Kies op **VM-formaat, opslaginstellingen**een SQL Server-grootte van de virtuele machine en controleer de andere instellingen.

* **SQL Server virtuele machinegrootte** is de grootte voor beide virtuele machines die SQL Server uitvoeren. Kies een geschikte virtuele machinegrootte voor uw werkbelasting. Als u deze omgeving bouwt voor de zelfstudie, gebruikt u **DS2**. Kies voor productieworkloads een virtuele machinegrootte die de werkbelasting kan ondersteunen. Veel productieworkloads vereisen **DS4** of groter. De sjabloon bouwt twee virtuele machines van dit formaat en installeert SQL Server op elk van deze apparaten. Zie [Grootte voor virtuele machines voor](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)meer informatie .

> [!NOTE]
> Azure installeert de Enterprise Edition van SQL Server. De kosten zijn afhankelijk van de editie en de grootte van de virtuele machine. Zie de prijzen van [virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)voor gedetailleerde informatie over de huidige kosten.
>
>

* **Domeincontroller virtuele machine grootte** is de virtuele machine grootte voor de domeincontrollers. Voor deze zelfstudie gebruik **D2**.
* **File Share Witness virtuele machine grootte** is de virtuele machine grootte voor het bestand delen getuige. Gebruik Voor deze zelfstudie **A1**.
* **SQL Storage-account** is de naam van het opslagaccount dat de SQL Server-gegevens en besturingssysteemschijven bevat. Gebruik voor deze zelfstudie **alwaysonsql01**.
* **DC Storage-account** is de naam van het opslagaccount voor de domeincontrollers. Gebruik voor deze zelfstudie **alwaysondc01**.
* **SQL Server-gegevensschijfgrootte** in TB is de grootte van de SQL Server-gegevensschijf in TB. Geef een getal op van 1 tot en met 4. Gebruik voor deze zelfstudie **1**.
* **Opslagoptimalisatie** stelt specifieke opslagconfiguratie-instellingen in voor de virtuele SQL Server-machines op basis van het werkbelastingtype. Alle virtuele SQL Server-machines in dit scenario gebruiken premium opslag met Azure disk host cache ingesteld op alleen-lezen. Bovendien u SQL Server-instellingen voor de werkbelasting optimaliseren door een van de volgende drie instellingen te kiezen:

  * **Algemene werkbelasting** stelt geen specifieke configuratie-instellingen in.
  * **Transactionele verwerking** stelt trace flag 1117 en 1118 in.
  * **Data warehousing** sets trace flag 1117 en 610.

Gebruik voor deze zelfstudie **Algemene werkbelasting**.

![Instellingen voor opslag van VM-formaat](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Controleer de instellingen en klik op **OK**.

#### <a name="a-note-about-storage"></a>Een opmerking over opslag
Extra optimalisaties zijn afhankelijk van de grootte van de SQL Server-gegevensschijven. Voor elke terabyte aan gegevensschijf voegt Azure een extra 1 TB premium-opslag toe. Wanneer een server 2 TB of meer nodig heeft, maakt de sjabloon een opslaggroep op elke virtuele SQL Server-machine. Een opslaggroep is een vorm van opslagvirtualisatie waarbij meerdere schijven zijn geconfigureerd om een hogere capaciteit, tolerantie en prestaties te bieden.  De sjabloon maakt vervolgens een opslagruimte in de opslaggroep en presenteert één gegevensschijf aan het besturingssysteem. De sjabloon wijst deze schijf aan als de gegevensschijf voor SQL Server. De sjabloon stemt de opslaggroep voor SQL Server af met de volgende instellingen:

* Streepgrootte is de interleave-instelling voor de virtuele schijf. Transactionele workloads gebruiken 64 KB. Data warehousing workloads gebruiken 256 KB.
* Veerkracht is eenvoudig (geen veerkracht).

> [!NOTE]
> Azure premium storage is lokaal redundant en houdt drie kopieën van de gegevens binnen één regio, dus extra tolerantie in de opslaggroep is niet vereist.
>
>

* Het aantal kolomtypen is gelijk aan het aantal schijven in de opslaggroep.

Zie voor meer informatie over opslagruimte en opslagpools:

* [Overzicht van Opslagruimten](https://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server-back-up- en opslaggroepen](https://technet.microsoft.com/library/dn390929.aspx)

Zie Aanbevolen procedures voor prestaties voor [SQL Server in virtuele Azure-machines voor](virtual-machines-windows-sql-performance.md)meer informatie over best practices voor SQL Server-configuratie.

### <a name="sql-server-settings"></a>SQL Server-instellingen
Controleer en wijzig op **SQL Server-instellingen**het sql server-voorvoegsel voor virtuele machines, de SQL Server-versie, sql server-serviceaccount en -wachtwoord en het SQL-onderhoudsschema voor automatisch patchen.

* **SQL Server Name Prefix** wordt gebruikt om een naam te maken voor elke virtuele SQL Server-machine. Gebruik **sqlserver**voor deze zelfstudie . De sjabloon noemt de SQL Server virtuele machines *sqlserver-0* en *sqlserver-1*.
* **SQL Server-versie** is de versie van SQL Server. Gebruik SQL **Server 2014 voor**deze zelfstudie . U ook kiezen voor **SQL Server 2012** of **SQL Server 2016.**
* **De gebruikersnaam van sql server-serviceaccount** is de domeinnaam van het domein voor de SQL Server-service. Gebruik **sqlservice**voor deze zelfstudie.
* **Wachtwoord** is het wachtwoord voor het SQL Server-serviceaccount.  Gebruik een complex wachtwoord. Bevestig het wachtwoord.
* **SQL Auto Patching onderhoudsschema** identificeert de dag van de week dat Azure automatisch patches de SQL Servers. Typ voor deze zelfstudie **zondag.**
* **SQL Auto Patching onderhoudsstartuur** is het tijdstip van de dag voor de Azure-regio wanneer de automatische patching begint.

> [!NOTE]
> Het patchvenster voor elke virtuele machine wordt met een uur gespreid. Slechts één virtuele machine wordt tegelijkertijd gepatcht om verstoring van de dienstverlening te voorkomen.
>
>

![SQL Server-instellingen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Controleer de instellingen en klik op **OK**.

### <a name="summary"></a>Samenvatting
Op de overzichtspagina valideert Azure de instellingen. U de sjabloon ook downloaden. Bekijk de samenvatting. Klik op **OK**.

### <a name="buy"></a>Kopen
Dit laatste blad bevat **gebruiksvoorwaarden**en **privacybeleid**. Bekijk deze informatie. Wanneer u klaar bent voor Azure om te beginnen met het maken van de virtuele machines en alle andere vereiste resources voor de beschikbaarheidsgroep, klikt u op **Maken**.

De Azure-portal maakt de brongroep en alle resources.

## <a name="monitor-deployment"></a>Implementatie bewaken
Controleer de voortgang van de implementatie vanuit de Azure-portal. Een pictogram dat de implementatie vertegenwoordigt, wordt automatisch vastgemaakt aan het Azure-portaldashboard.

![Azure-dashboard](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Verbinding maken met SQL Server
De nieuwe exemplaren van SQL Server worden uitgevoerd op virtuele machines met IP-adressen met internetverbinding. U extern bureaublad (RDP) rechtstreeks naar elke virtuele SQL Server-machine leiden.

Voer de volgende stappen uit om RDP naar een SQL Server te gaan:

1. Controleer in het Azure-portaldashboard of de implementatie is geslaagd.
2. Klik **op Resources**.
3. Klik in het blade **Resources** op **sqlserver-0**, de computernaam van een van de virtuele machines waarop SQL Server wordt uitgevoerd.
4. Klik op het blad voor **sqlserver-0**op **Verbinden**. Uw browser vraagt of u het externe verbindingsobject wilt openen of opslaan. Klik op **Openen**.
5. **Verbinding met extern bureaublad** kan u waarschuwen dat de uitgever van deze externe verbinding niet kan worden geïdentificeerd. Klik op **Verbinden**.
6. Windows security vraagt u om uw referenties in te voeren om verbinding te maken met het IP-adres van de primaire domeincontroller. Klik **op Een ander account gebruiken**. Typ voor **gebruikersnaam** **contoso\DomainAdmin**. U hebt dit account geconfigureerd wanneer u de gebruikersnaam van de beheerder in de sjabloon instelt. Gebruik het complexe wachtwoord dat u hebt gekozen toen u de sjabloon configureerde.
7. **Extern bureaublad** kan u waarschuwen dat de externe computer niet kan worden geverifieerd vanwege problemen met het beveiligingscertificaat. Het toont u de naam van het beveiligingscertificaat. Als u de tutorial hebt gevolgd, wordt de naam **sqlserver-0.contoso.com**. Klik **op Ja**.

U bent nu verbonden met RDP met de virtuele SQL Server-machine. U SQL Server Management Studio openen, verbinding maken met de standaardinstantie van SQL Server en controleren of de beschikbaarheidsgroep is geconfigureerd.

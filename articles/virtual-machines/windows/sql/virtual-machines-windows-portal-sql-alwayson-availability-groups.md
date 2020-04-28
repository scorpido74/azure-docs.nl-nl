---
title: Hoge Beschik baarheid instellen voor Azure Resource Manager Vm's | Microsoft Docs
description: In deze zelf studie leert u hoe u een AlwaysOn-beschikbaarheids groep maakt met virtuele Azure-machines in Azure Resource Manager modus.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75374249"
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>AlwaysOn AlwaysOn-beschikbaarheids groepen in azure Virtual Machines automatisch configureren: Resource Manager

In deze zelf studie wordt uitgelegd hoe u een SQL Server beschikbaarheids groep maakt die gebruikmaakt van Azure Resource Manager virtuele machines. De zelf studie maakt gebruik van Azure-Blades voor het configureren van een sjabloon. U kunt de standaard instellingen controleren, vereiste instellingen typen en de Blades bijwerken in de portal tijdens deze zelf studie.

Met de volledige zelf studie maakt u een SQL Server-beschikbaarheids groep in azure Virtual Machines die de volgende elementen bevat:

* Een virtueel netwerk met meerdere subnetten, met inbegrip van een front-end en een back-end-subnet
* Twee domein controllers met een Active Directory domein
* Twee virtuele machines met SQL Server en worden geïmplementeerd op het back-end-subnet en zijn gekoppeld aan het Active Directory domein
* Een failover-cluster met drie knoop punten met het quorum model knooppunt meerderheid
* Een beschikbaarheids groep met twee replica's met synchrone door Voer van een beschikbaarheids database

De volgende afbeelding geeft de volledige oplossing.

![Test Lab-architectuur voor beschikbaarheids groepen in azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Alle resources in deze oplossing behoren tot één resource groep.

Voordat u met deze zelf studie begint, moet u het volgende bevestigen:

* U hebt al een Azure-account. Als u er nog geen hebt, kunt u [zich aanmelden voor een proef account](https://azure.microsoft.com/pricing/free-trial/).
* U weet al hoe u de GUI kunt gebruiken om een SQL Server virtuele machine in te richten vanuit de galerie met virtuele machines. Zie [een SQL Server virtuele machine inrichten in azure](virtual-machines-windows-portal-sql-server-provision.md)voor meer informatie.
* U hebt al een solide uitleg van beschikbaarheids groepen. Zie AlwaysOn [Availability groups (SQL Server)](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)voor meer informatie.

> [!NOTE]
> Zie voor meer informatie over het gebruik van beschikbaarheids groepen met share point ook de [configuratie SQL Server 2012 altijd on-beschikbaarheids groepen configureren voor share point 2013](/SharePoint/administration/configure-an-alwayson-availability-group).
>
>

In deze zelf studie gebruikt u de Azure Portal voor het volgende:

* Kies de sjabloon altijd aan in de portal.
* Controleer de sjabloon instellingen en werk een paar configuratie-instellingen voor uw omgeving bij.
* Bewaak Azure terwijl het de hele omgeving maakt.
* Maak verbinding met een domein controller en vervolgens naar een server waarop SQL Server wordt uitgevoerd.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Het cluster inrichten vanuit de galerie
Azure biedt een galerie-afbeelding voor de volledige oplossing. De sjabloon zoeken:

1. Meld u aan bij de Azure Portal met uw account.
2. Klik in de Azure Portal op **een resource maken** om het **nieuwe** deel venster te openen.
3. Zoek in het **nieuwe** deel venster naar **AlwaysOn**.
   ![AlwaysOn-sjabloon zoeken](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Zoek in de zoek resultaten **SQL Server AlwaysOn-cluster**.
   ![AlwaysOn-sjabloon](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Kies in **een implementatie model selecteren de**optie **Resource Manager**.

### <a name="basics"></a>Basisbeginselen
Klik op **basis principes** en configureer de volgende instellingen:

* De **gebruikers naam** van de beheerder is een gebruikers account met beheerders machtigingen voor het domein en is lid van de vaste serverrol SQL Server sysadmin op beide exemplaren van SQL Server. Voor deze zelf studie gebruikt u **domein Administrator**.
* Het **wacht woord is het** wacht woord voor het domein beheerders account. Gebruik een complex wacht woord. Bevestig het wachtwoord.
* **Abonnement** is het abonnement dat door Azure wordt gebruikt om alle geïmplementeerde resources voor de beschikbaarheids groep uit te voeren. Als uw account meerdere abonnementen heeft, kunt u een ander abonnement opgeven.
* De **resource groep** is de naam voor de groep waartoe alle Azure-resources behoren die door deze sjabloon worden gemaakt. Voor deze zelf studie gebruikt u **SQL-ha-RG**. Zie voor meer informatie [Overzicht van Azure Resource Manager](../../../azure-resource-manager/management/overview.md#resource-groups).
* De **locatie** is de Azure-regio waar de zelf studie de resources maakt. Kies een Azure-regio.

De volgende scherm afbeelding is een voltooide Blade **basis beginselen** :

![Basisbeginselen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Klik op **OK**.

### <a name="domain-and-network-settings"></a>Domein-en netwerk instellingen
Deze Azure Gallery-sjabloon maakt een domein en domein controllers. Er worden ook een netwerk en twee subnetten gemaakt. De sjabloon kan geen servers in een bestaand domein of virtueel netwerk maken. Met de volgende stap worden de domein-en netwerk instellingen geconfigureerd.

Controleer op de Blade **domein en netwerk instellingen** de vooraf ingestelde waarden voor de domein-en netwerk instellingen:

* **Forest-hoofd domein naam** is de domein naam voor het Active Directory domein dat als host fungeert voor het cluster. Gebruik **contoso.com**voor de zelf studie.
* **Virtual Network naam** is de netwerk naam voor het virtuele netwerk van Azure. Gebruik **autohaVNET**voor de zelf studie.
* De naam van het subnet van de **domein controller** is de naam van een deel van het virtuele netwerk dat als host fungeert voor de domein controller. **Subnet-1**gebruiken. Dit subnet maakt gebruik van adres voorvoegsel **10.0.0.0/24**.
* **SQL Server naam** van het subnet is de naam van een deel van het virtuele netwerk dat als host fungeert voor de servers waarop SQL Server wordt uitgevoerd en de bestandssharewitness. **Subnet-2**gebruiken. Dit subnet gebruikt het adres voorvoegsel **10.0.1.0/26**.

Zie [overzicht van virtueel netwerk](../../../virtual-network/virtual-networks-overview.md)voor meer informatie over virtuele netwerken in Azure.  

De **domein-en netwerk instellingen** moeten eruitzien als in de volgende scherm afbeelding:

![Domein-en netwerk instellingen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Als dat nodig is, kunt u deze waarden wijzigen. Voor deze zelf studie gebruikt u de vooraf ingestelde waarden.

Controleer de instellingen en klik vervolgens op **OK**.

### <a name="availability-group-settings"></a>Instellingen van beschikbaarheids groep
Controleer de vooraf ingestelde waarden voor de beschikbaarheids groep en de listener op de instellingen van de **beschikbaarheids groep**.

* De naam van de **beschikbaarheids groep** is de geclusterde resource naam voor de beschikbaarheids groep. Gebruik **Contoso-AG**voor deze zelf studie.
* De naam van de **beschikbaarheids groep-listener** wordt gebruikt door het cluster en de interne Load Balancer. Clients die verbinding maken met SQL Server kunnen deze naam gebruiken om verbinding te maken met de juiste replica van de data base. Voor deze zelf studie gebruikt u **Contoso-listener**.
* **Poort van beschikbaarheids groep-listener** specificeert de TCP-poort van de SQL Server-listener. Voor deze zelf studie gebruikt u de standaard poort **1433**.

Als dat nodig is, kunt u deze waarden wijzigen. Voor deze zelf studie gebruikt u de vooraf ingestelde waarden.  

![instellingen van beschikbaarheids groep](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Klik op **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Grootte van de virtuele machine, opslag instellingen
Voor de **VM-grootte, opslag instellingen**, kiest u een SQL Server grootte van de virtuele machine en bekijkt u de overige instellingen.

* **SQL Server grootte van de virtuele machine** is de grootte voor zowel virtuele machines waarop SQL Server wordt uitgevoerd. Kies een geschikte grootte voor de virtuele machine voor uw werk belasting. Als u deze omgeving bouwt voor de zelf studie, gebruikt u **DS2**. Kies voor werk belastingen voor productie een grootte van virtuele machines die de werk belasting kan ondersteunen. Veel productie werkbelastingen vereisen **DS4** of groter. Met de sjabloon worden twee virtuele machines van deze grootte gebouwd en worden er SQL Server op elke machine geïnstalleerd. Zie [grootten voor virtuele machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)voor meer informatie.

> [!NOTE]
> Azure installeert de Enter prise-editie van SQL Server. De kosten zijn afhankelijk van de editie en de grootte van de virtuele machine. Zie [prijzen van virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql)voor meer informatie over de huidige kosten.
>
>

* De grootte van de virtuele machine van de **domein controller** is de grootte van de virtuele machine voor de domein controllers. Voor deze zelf studie gebruikt u **D2**.
* **Grootte van de virtuele machine van de bestands share-Witness** is de grootte van de virtuele machine voor de bestands share-Witness. Voor deze zelf studie gebruikt u **a1**.
* **SQL-opslag account** is de naam van het opslag account dat de SQL Server gegevens en besturingssysteem schijven bevat. Voor deze zelf studie gebruikt u **alwaysonsql01**.
* **DC-opslag account** is de naam van het opslag account voor de domein controllers. Voor deze zelf studie gebruikt u **alwaysondc01**.
* **SQL Server grootte van de gegevens schijf** in TB is de grootte van de SQL Server gegevens schijf in TB. Geef een getal tussen 1 en 4 op. Voor deze zelf studie gebruikt u **1**.
* **Opslag optimalisatie** stelt specifieke opslag configuratie-instellingen voor de virtuele machines van SQL Server in op basis van het type werk belasting. Alle SQL Server virtuele machines in dit scenario gebruiken Premium-opslag met de Azure Disk host-cache ingesteld op alleen-lezen. Daarnaast kunt u de SQL Server-instellingen voor de werk belasting optimaliseren door een van de volgende drie instellingen te kiezen:

  * **Algemene werk belasting** stelt geen specifieke configuratie-instellingen in.
  * **Transactionele verwerking** sets tracerings vlag 1117 en 1118.
  * **Gegevens opslag** sets tracerings vlag 1117 en 610.

Voor deze zelf studie gebruikt u de **algemene werk belasting**.

![Opslag instellingen voor VM-grootte](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Controleer de instellingen en klik vervolgens op **OK**.

#### <a name="a-note-about-storage"></a>Een opmerking over opslag
Extra optimalisaties zijn afhankelijk van de grootte van de SQL Server gegevens schijven. Voor elke terabyte van de gegevens schijf voegt Azure een extra Premium-opslag van 1 TB toe. Wanneer een server 2 TB of meer vereist, maakt de sjabloon een opslag groep op elke virtuele machine van SQL Server. Een opslag groep is een vorm van opslag virtualisatie waarbij meerdere schijven zijn geconfigureerd om hogere capaciteit, tolerantie en prestaties te bieden.  De sjabloon maakt vervolgens een opslag ruimte op de opslag groep en presenteert één gegevens schijf aan het besturings systeem. De sjabloon geeft deze schijf aan als de gegevens schijf voor SQL Server. Met de sjabloon wordt de opslag groep voor SQL Server gestemd met behulp van de volgende instellingen:

* De Stripe-grootte is de Interleave-instelling voor de virtuele schijf. Transactionele workloads gebruiken 64 KB. Werk belastingen voor gegevens opslag gebruiken 256 KB.
* Tolerantie is eenvoudig (geen tolerantie).

> [!NOTE]
> Azure Premium Storage is lokaal redundant en houdt drie kopieën van de gegevens binnen één regio, waardoor er geen extra tolerantie bij de opslag groep nodig is.
>
>

* Het aantal kolommen is gelijk aan het aantal schijven in de opslag groep.

Zie voor meer informatie over opslag ruimte en opslag groepen:

* [Overzicht van Opslagruimten](https://technet.microsoft.com/library/hh831739.aspx)
* [Windows Server Back-up en opslag groepen](https://technet.microsoft.com/library/dn390929.aspx)

Zie [Best practices voor prestaties voor SQL Server in azure virtual machines](virtual-machines-windows-sql-performance.md)voor meer informatie over aanbevolen procedures voor de configuratie van SQL Server.

### <a name="sql-server-settings"></a>SQL Server-instellingen
Bekijk en wijzig de SQL Server naam van de virtuele machine, SQL Server versie, SQL Server service account en wacht woord en de onderhouds planning voor SQL auto-patching voor **SQL Server instellingen**.

* **SQL Server naam voorvoegsel** wordt gebruikt voor het maken van een naam voor elke virtuele machine van SQL Server. Voor deze zelf studie gebruikt u **sqlserver**. De sjabloon namen de SQL Server virtuele machines *sqlserver-0* en *sqlserver-1*.
* **SQL Server versie** is de versie van SQL Server. Voor deze zelf studie gebruikt u **SQL Server 2014**. U kunt ook kiezen **SQL Server 2012** of **SQL Server 2016**.
* De **gebruikers naam van SQL Server-service account** is de naam van het domein account voor de SQL Server-service. Voor deze zelf studie gebruikt u **sqlservice**.
* Het **wacht woord is het** wacht woord voor de SQL Server-service account.  Gebruik een complex wacht woord. Bevestig het wachtwoord.
* De **onderhouds planning voor SQL auto patching** geeft aan op welke dag van de week de SQL-servers automatisch worden bijgewerkt door Azure. Voor deze zelf studie typt u **zondag**.
* **Onderhoud start uur van SQL auto patching** is de tijd van de dag voor de Azure-regio wanneer automatische patching wordt gestart.

> [!NOTE]
> Het patch venster voor elke virtuele machine wordt één uur gespreid. Er wordt slechts één virtuele machine tegelijk patches uitgevoerd om onderbreking van services te voor komen.
>
>

![SQL Server-instellingen](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Controleer de instellingen en klik vervolgens op **OK**.

### <a name="summary"></a>Samenvatting
Op de pagina samen vatting valideert Azure de instellingen. U kunt de sjabloon ook downloaden. Bekijk de samenvatting. Klik op **OK**.

### <a name="buy"></a>Kopen
Deze laatste Blade bevat **gebruiks voorwaarden**en het **Privacybeleid**. Lees deze informatie. Wanneer u klaar bent om Azure te gaan maken van de virtuele machines en alle andere vereiste resources voor de beschikbaarheids groep, klikt u op **maken**.

De Azure Portal maakt de resource groep en alle resources.

## <a name="monitor-deployment"></a>Implementatie bewaken
De voortgang van de implementatie van de Azure Portal bewaken. Een pictogram dat de implementatie vertegenwoordigt, wordt automatisch vastgemaakt aan het dash board van Azure Portal.

![Azure-dash board](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Verbinding maken met SQL Server
De nieuwe exemplaren van SQL Server worden uitgevoerd op virtuele machines met IP-adressen die via internet zijn verbonden. U kunt extern bureau blad (RDP) rechtstreeks op elke virtuele machine van SQL Server.

Ga als volgt te werk om een SQL Server uit te voeren:

1. Controleer in het dash board van Azure Portal of de implementatie is geslaagd.
2. Klik op **resources**.
3. Klik op de Blade **resources** op **sqlserver-0**. Dit is de computer naam van een van de virtuele machines met SQL Server.
4. Klik op de Blade voor **sqlserver-0**op **verbinden**. U wordt gevraagd of u het object voor externe verbinding wilt openen of opslaan. Klik op **Openen**.
5. Via **verbinding met extern bureau blad** krijgt u mogelijk een waarschuwing dat de uitgever van deze externe verbinding niet kan worden geïdentificeerd. Klik op **Verbinden**.
6. Windows-beveiliging vraagt u uw referenties in te voeren om verbinding te maken met het IP-adres van de primaire domein controller. Klik op **een ander account gebruiken**. Typ **contoso\DomainAdmin**voor **gebruikers naam**. U hebt dit account geconfigureerd bij het instellen van de gebruikers naam van de beheerder in de sjabloon. Gebruik het complexe wacht woord dat u hebt gekozen tijdens het configureren van de sjabloon.
7. **Extern bureau blad** waarschuwt u mogelijk dat de externe computer niet kan worden geverifieerd vanwege problemen met het beveiligings certificaat. Hier wordt de naam van het beveiligings certificaat weer gegeven. Als u de zelf studie hebt gevolgd, is de naam **sqlserver-0.contoso.com**. Klik op **Ja**.

U hebt nu verbinding met RDP met de virtuele machine van SQL Server. U kunt SQL Server Management Studio openen, verbinding maken met het standaard exemplaar van SQL Server en controleren of de beschikbaarheids groep is geconfigureerd.

---
title: 'Zelf studie: beschikbaarheids groep configureren'
description: Deze zelf studie laat zien hoe u een SQL Server AlwaysOn-beschikbaarheids groep maakt op Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.custom: seo-lt-2019
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: ed5fc923c82fb0d0e4004e18159d943564c6f55e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249852"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Zelf studie: beschikbaarheids groep op Azure SQL Server VM hand matig configureren

Deze zelf studie laat zien hoe u een SQL Server AlwaysOn-beschikbaarheids groep maakt op Azure Virtual Machines. Met de volledige zelf studie maakt u een beschikbaarheids groep met een database replica op twee SQL-servers.

**Tijd schatting**: duurt ongeveer 30 minuten om te volt ooien zodra aan de vereisten wordt voldaan.

In het diagram ziet u wat u in de zelf studie maakt.

![Beschikbaarheidsgroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Vereisten

In de zelf studie wordt ervan uitgegaan dat u een basis memorandum hebt van SQL Server AlwaysOn-beschikbaarheids groepen. Zie overzicht van AlwaysOn [Availability groups (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)als u meer informatie nodig hebt.

De volgende tabel bevat de vereisten die u moet volt ooien voordat u met deze zelf studie begint:

|  |Vereiste |Beschrijving |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Twee SQL-servers | -In een Azure-beschikbaarheidsset <br/> -In één domein <br/> -Met functie Failoverclustering geïnstalleerd |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Bestands share voor cluster Witness |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server-service account | Domeinaccount |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server Agent-service account | Domeinaccount |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Firewall poorten geopend | -SQL Server: **1433** voor standaard exemplaar <br/> -Data base mirroring-eind punt: **5022** of een beschik bare poort <br/> -Beschikbaarheids groep load balancer IP-adres test: **59999** of een wille keurige beschik bare poort <br/> -Cluster kern load balancer IP-adres test: **58888** of een wille keurige beschik bare poort |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Functie Failover Clustering toevoegen | Voor beide SQL-servers is deze functie vereist |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Account voor installatie domein | -Lokale beheerder op elke SQL Server <br/> -Lid van de vaste serverrol SQL Server sysadmin voor elk exemplaar van SQL Server  |


Voordat u met de zelf studie begint, moet u de vereisten voor het maken van AlwaysOn- [beschikbaarheids groepen in Azure virtual machines volt ooien](virtual-machines-windows-portal-sql-availability-group-prereq.md). Als deze vereisten al zijn voltooid, kunt u naar een [cluster maken](#CreateCluster)gaan.

  >[!NOTE]
  > Veel van de stappen in deze zelf studie kunnen nu worden geautomatiseerd met [Azure SQL VM cli](virtual-machines-windows-sql-availability-group-cli.md) en [Azure Quick](virtual-machines-windows-sql-availability-group-quickstart-template.md)start-sjablonen.


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Het cluster maken

Nadat de vereisten zijn voltooid, is de eerste stap het maken van een Windows Server-failovercluster dat twee SQL-servers en een Witness-server bevat.

1. RDP naar de eerste SQL Server met behulp van een domein account dat een beheerder is op de SQL-servers en de witness-server.

   >[!TIP]
   >Als u het [document vereisten](virtual-machines-windows-portal-sql-availability-group-prereq.md)hebt gevolgd, hebt u een account gemaakt met de naam **CORP\Install**. Gebruik dit account.

2. Selecteer in het **Serverbeheer** -dash board **extra**en klik vervolgens op **Failoverclusterbeheer**.
3. Klik in het linkerdeel venster met de rechter muisknop op **Failoverclusterbeheer**en klik vervolgens op **een cluster maken**.
   ![cluster maken](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. Maak in de wizard cluster maken een cluster met één knoop punt door de pagina's door te lopen met de instellingen in de volgende tabel:

   | Faxvoorblad | Instellingen |
   | --- | --- |
   | Voordat u begint |Standaard instellingen gebruiken |
   | Servers selecteren |Typ de eerste SQL Server naam in **Voer de server naam** in en klik op **toevoegen**. |
   | Validatie waarschuwing |Selecteer **Nee. Ik heb geen ondersteuning van micro soft voor dit cluster nodig en wil daarom de validatie tests niet uitvoeren. Wanneer ik op volgende klik, ga dan door met het maken van het cluster**. |
   | Toegangs punt voor beheer van het cluster |Typ een cluster naam, bijvoorbeeld **SQLAGCluster1** in de **naam**van het cluster.|
   | Bevestiging |Gebruik standaard instellingen tenzij u opslag ruimten gebruikt. Zie de opmerking na deze tabel. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Het IP-adres van het Windows Server failover cluster instellen

  > [!NOTE]
  > In Windows Server 2019 maakt het cluster een **gedistribueerde server naam** in plaats van de naam van het **cluster netwerk**. Als u Windows Server 2019 gebruikt, slaat u de stappen over die verwijzen naar de naam van de cluster kern in deze zelf studie. U kunt een cluster netwerk naam maken met behulp van [Power shell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019). Bekijk het failovercluster van de blog [: cluster netwerk object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) voor meer informatie. 

1. Schuif in **Failoverclusterbeheer**omlaag naar **cluster kern resources** en vouw de cluster gegevens uit. U moet zowel de **naam** als het **IP-adres** van de resources in de **mislukte** status zien. De IP-adres bron kan niet online worden gebracht omdat het cluster hetzelfde IP-adres krijgt als de computer zelf. Daarom is het een dubbel adres.

2. Klik met de rechter muisknop op de mislukte **IP-adres** bron en klik vervolgens op **Eigenschappen**.

   ![Cluster eigenschappen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Selecteer **statisch IP-adres** en geef een beschikbaar adres op dat zich in hetzelfde subnet bevindt als uw virtuele machines.

4. Klik in de sectie **cluster Core Resources** met de rechter muisknop op cluster naam en klik op **online brengen**. Wacht totdat beide bronnen online zijn. Wanneer de cluster naam bron online is, wordt de DC-server bijgewerkt met een nieuw account voor een AD-computer. Gebruik dit AD-account om de geclusterde service voor de beschikbaarheids groep later uit te voeren.

### <a name="addNode"></a>De andere SQL Server toevoegen aan het cluster

Voeg de andere SQL Server toe aan het cluster.

1. Klik in de browser structuur met de rechter muisknop op het cluster en klik op **knoop punt toevoegen**.

    ![Knoop punt toevoegen aan het cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. Klik in de **wizard knoop punt toevoegen**op **volgende**. Voeg op de pagina **servers selecteren** de tweede SQL Server toe. Typ de server naam in **Typ server naam** en klik vervolgens op **toevoegen**. Wanneer u klaar bent, klikt u op **volgende**.

1. Klik op het tabblad **validatie waarschuwing** op **Nee** (in een productie scenario moet u de validatie tests uitvoeren). Klik op **Volgende**.

8. Schakel op de pagina **bevestiging** het selectie vakje **alle in aanmerking komende opslag toevoegen aan het cluster uit.**

   ![Bevestiging van knoop punt toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Als u opslag ruimten gebruikt en de optie **alle in aanmerking komende opslag toevoegen**niet uitschakelt aan het cluster, worden de virtuele schijven tijdens het cluster losgekoppeld. Als gevolg hiervan worden ze niet weer gegeven in schijf beheer of Explorer totdat de opslag ruimten uit het cluster worden verwijderd en opnieuw zijn gekoppeld met Power shell. Met opslag ruimten worden meerdere schijven in opslag groepen gegroepeerd. Zie [opslag ruimten](https://technet.microsoft.com/library/hh831739)voor meer informatie.

1. Klik op **Volgende**.

1. Klik op **Voltooien**.

   Failoverclusterbeheer geeft aan dat het cluster een nieuw knoop punt heeft en dit in de **knoop punten** container vermeldt.

10. Meld u af bij de Extern-bureaublad sessie.

### <a name="add-a-cluster-quorum-file-share"></a>Een cluster quorum bestands share toevoegen

In dit voor beeld gebruikt het Windows-cluster een bestands share om een cluster quorum te maken. In deze zelf studie wordt gebruikgemaakt van een knoop punt-en bestands share meerderheids quorum. Raadpleeg [Understanding Quorum Configurations in a Failover Cluster](https://technet.microsoft.com/library/cc731739.aspx) (Inzicht in quorumconfiguraties in een failovercluster) voor meer informatie.

1. Verbinding maken met de lidserver van de bestands share-Witness met een extern-bureaublad sessie.

1. Klik op **Serverbeheer**op **extra**. Open **computer beheer**.

1. Klik op **gedeelde mappen**.

1. Klik met de rechter muisknop op **shares**en klik op **nieuwe share...** .

   ![Nieuwe share](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Gebruik **de wizard gedeelde map maken** om een share te maken.

1. Klik op **Bladeren** in het **mappad**en zoek of maak een pad voor de gedeelde map. Klik op **Volgende**.

1. Controleer bij **naam, beschrijving en instellingen** de share naam en het pad. Klik op **Volgende**.

1. **Machtigingen voor gedeelde mappen** **aanpassen machtigingen**instellen. Klik op **aangepast...** .

1. Klik bij **machtigingen aanpassen**op **toevoegen...** .

1. Zorg ervoor dat het account dat is gebruikt voor het maken van het cluster volledig beheer heeft.

   ![Nieuwe share](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Klik op **OK**.

1. Klik in **machtigingen voor gedeelde map**op **volt ooien**. Klik opnieuw op **volt ooien** .  

1. Afmelden bij de server

### <a name="configure-cluster-quorum"></a>Cluster quorum configureren

Stel vervolgens het cluster quorum in.

1. Maak verbinding met het eerste cluster knooppunt met extern bureau blad.

1. Klik in **Failoverclusterbeheer**met de rechter muisknop op het cluster, wijs **meer acties**aan en klik op **quorum instellingen van het cluster configureren...** .

   ![Nieuwe share](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. Klik in de **wizard cluster quorum configureren**op **volgende**.

1. In **optie quorum configuratie selecteren**kiest **u de quorumwitness selecteren**en klikt u op **volgende**.

1. Klik in **quorum-Witness selecteren**op **een bestands share-Witness configureren**.

   >[!TIP]
   >Windows Server 2016 ondersteunt een Cloud-Witness. Als u dit type Witness kiest, hebt u geen bestandssharewitness nodig. Zie [een Cloudwitness implementeren voor een failovercluster](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)voor meer informatie. In deze zelf studie wordt een bestands share-Witness gebruikt, die wordt ondersteund door eerdere besturings systemen.

1. Typ het pad voor de share die u hebt gemaakt op de **Bestands share-Witness configureren**. Klik op **Volgende**.

1. Controleer de instellingen op **bevestiging**. Klik op **Volgende**.

1. Klik op **Voltooien**.

De kern bronnen van het cluster worden geconfigureerd met een bestandssharewitness.

## <a name="enable-availability-groups"></a>Beschikbaarheids groepen inschakelen

Schakel vervolgens de functie **AlwaysOn-beschikbaarheidsgroepen** in. Voer deze stappen uit op beide SQL-servers.

1. Start **SQL Server Configuration Manager**in het **Start** scherm.
2. Klik in de browser structuur op **SQL Server services**, klik met de rechter muisknop op de service **SQL Server (MSSQLSERVER)** en klik op **Eigenschappen**.
3. Klik op het tabblad voor **AlwaysOn-Beschik baarheid** en selecteer vervolgens **AlwaysOn-beschikbaarheidsgroepen inschakelen**als volgt:

    ![AlwaysOn-beschikbaarheidsgroepen inschakelen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Klik op **Apply** (Toepassen). Klik op **OK** in het pop-upvenster.

5. Start de SQL Server-service opnieuw.

Herhaal deze stappen op de andere SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Maak een Data Base op de eerste SQL Server

1. Start het RDP-bestand op het eerste SQL Server met een domein account dat lid is van de vaste serverrol sysadmin.
1. Open SQL Server Management Studio en maak verbinding met de eerste SQL Server.
7. Klik in **objectverkenner**met de rechter muisknop op **data bases** en klik op **nieuwe data base**.
8. Typ **MyDB1**in de **database naam**en klik vervolgens op **OK**.

### <a name="backupshare"></a>Een back-upshare maken

1. Klik op de eerste SQL Server in **Serverbeheer**op **extra**. Open **computer beheer**.

1. Klik op **gedeelde mappen**.

1. Klik met de rechter muisknop op **shares**en klik op **nieuwe share...** .

   ![Nieuwe share](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Gebruik **de wizard gedeelde map maken** om een share te maken.

1. Klik op **Bladeren** **in het mappad en**Zoek of maak een pad voor de data base back-up van de gedeelde map. Klik op **Volgende**.

1. Controleer bij **naam, beschrijving en instellingen** de share naam en het pad. Klik op **Volgende**.

1. **Machtigingen voor gedeelde mappen** **aanpassen machtigingen**instellen. Klik op **aangepast...** .

1. Klik bij **machtigingen aanpassen**op **toevoegen...** .

1. Zorg ervoor dat de service accounts SQL Server en SQL Server Agent voor beide servers volledig beheer hebben.

   ![Nieuwe share](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Klik op **OK**.

1. Klik in **machtigingen voor gedeelde map**op **volt ooien**. Klik opnieuw op **volt ooien** .  

### <a name="take-a-full-backup-of-the-database"></a>Een volledige back-up van de data base maken

U moet een back-up maken van de nieuwe Data Base om de logboek keten te initialiseren. Als u geen back-up van de nieuwe Data Base maakt, kan deze niet worden opgenomen in een beschikbaarheids groep.

1. Klik in **objectverkenner**met de rechter muisknop op de data base, wijs **taken aan...** , klikt u op **back-up**.

1. Klik op **OK** om een volledige back-up naar de standaard locatie voor back-ups te maken.

## <a name="create-the-availability-group"></a>De beschikbaarheids groep maken
U bent nu klaar om een beschikbaarheids groep te configureren met de volgende stappen:

* Maak een Data Base op de eerste SQL Server.
* Een volledige back-up en een transactie logboek back-up van de data base nemen
* Herstel de volledige en logboek back-ups naar de tweede SQL Server met de optie **norecovery**
* De beschikbaarheids groep (**AG1**) maken met synchrone door Voer, automatische failover en lees bare secundaire replica's

### <a name="create-the-availability-group"></a>Maak de beschikbaarheids groep:

1. Op de extern bureau blad-sessie naar de eerste SQL Server. Klik in **objectverkenner** in SSMS met de rechter muisknop op **AlwaysOn hoge Beschik baarheid** en klik op **wizard Nieuwe beschikbaarheids groep**.

    ![Wizard Nieuwe beschikbaarheids groep starten](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Klik op de pagina **Inleiding** op **volgende**. Typ op de pagina **naam van beschikbaarheids groep opgeven** een naam voor de beschikbaarheids groep, bijvoorbeeld **AG1**, in de naam van de **beschikbaarheids groep**. Klik op **Volgende**.

    ![Wizard Nieuwe AG, AG-naam opgeven](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Selecteer uw Data Base op de pagina **data bases selecteren** en klik op **volgende**.

   >[!NOTE]
   >De data base voldoet aan de vereisten voor een beschikbaarheids groep omdat u ten minste één volledige back-up hebt gemaakt op de bedoelde primaire replica.

   ![Wizard Nieuwe AG, data bases selecteren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Klik op de pagina **Replica's opgeven** op **replica toevoegen**.

   ![Wizard Nieuwe AG, Replica's opgeven](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Het dialoog venster **verbinding maken met server** wordt weer gegeven. Typ de naam van de tweede server in **Server naam**. Klik op **Verbinding maken**.

   Op de pagina **Replica's opgeven** ziet u nu de tweede server die wordt vermeld in **beschikbaarheids replica's**. Configureer de replica's als volgt.

   ![Wizard Nieuwe AG, Replica's opgeven (voltooid)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Klik op **eind punten** om het data base mirroring-eind punt voor deze beschikbaarheids groep weer te geven. Gebruik dezelfde poort die u hebt gebruikt bij het instellen [van de firewall regel voor het spie gelen van data bases](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Wizard Nieuwe AG, selecteer initiële gegevens synchronisatie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Selecteer op de pagina **eerste gegevens synchronisatie selecteren** de optie **volledig** en geef een gedeelde netwerk locatie op. Voor de locatie gebruikt u de [back-upshare die u hebt gemaakt](#backupshare). In het voor beeld was dit **\\\\\<eerste SQL Server\>\Backup** \\. Klik op **Volgende**.

   >[!NOTE]
   >Bij volledige synchronisatie wordt een volledige back-up van de Data Base op het eerste exemplaar van SQL Server en wordt deze teruggezet naar het tweede exemplaar. Voor grote data bases wordt volledige synchronisatie niet aanbevolen, omdat het enige tijd kan duren. U kunt deze tijd verkorten door hand matig een back-up van de data base te maken en deze te herstellen met `NO RECOVERY`. Als de data base al is hersteld met `NO RECOVERY` op de tweede SQL Server voordat u de beschikbaarheids groep configureert, kiest u **alleen lid worden**. Als u de back-up wilt maken nadat u de beschikbaarheids groep hebt geconfigureerd, kiest u **synchronisatie van initiële gegevens overs Laan**.

    ![Wizard Nieuwe AG, selecteer initiële gegevens synchronisatie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Klik op de pagina **validatie** op **volgende**. Deze pagina moet er ongeveer uitzien als de volgende afbeelding:

    ![Wizard Nieuwe AG, validatie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Er is een waarschuwing voor de configuratie van de listener omdat u geen listener van een beschikbaarheids groep hebt geconfigureerd. U kunt deze waarschuwing negeren omdat op virtuele machines van Azure de listener wordt gemaakt nadat de Azure-load balancer is gemaakt.

10. Klik op de pagina **samen vatting** op **volt ooien**en vervolgens op een ogen blik geduld. de wizard configureert de nieuwe beschikbaarheids groep. Op de pagina **voortgang** kunt u op **meer details** klikken om de gedetailleerde voortgang weer te geven. Nadat de wizard is voltooid, controleert u de **resultaten** pagina om te controleren of de beschikbaarheids groep is gemaakt.

     ![Wizard Nieuwe AG, resultaten](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Klik op **sluiten** om de wizard af te sluiten.

### <a name="check-the-availability-group"></a>De beschikbaarheids groep controleren

1. Vouw in **objectverkenner** **AlwaysOn-hoge Beschik baarheid**uit en vouw vervolgens **beschikbaarheids groepen**uit. Nu ziet u de nieuwe beschikbaarheids groep in deze container. Klik met de rechter muisknop op de beschikbaarheids groep en klik op **dash board weer geven**.

   ![AG-dash board weer geven](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Uw **AlwaysOn-dash board** moet er ongeveer als volgt uitzien.

   ![AG-dash board](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   U kunt de replica's, de failover-modus van elke replica en de synchronisatie status zien.

2. Klik in **Failoverclusterbeheer**op uw cluster. Selecteer **rollen**. De naam van de beschikbaarheids groep die u hebt gebruikt, is een rol in het cluster. Deze beschikbaarheids groep heeft geen IP-adres voor client verbindingen, omdat u geen listener hebt geconfigureerd. U gaat de listener configureren nadat u een Azure-load balancer hebt gemaakt.

   ![AG in Failoverclusterbeheer](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Voer geen failover uit voor de beschikbaarheids groep van de Failoverclusterbeheer. Alle failover-bewerkingen moeten vanuit het **AlwaysOn-dash board** in SSMS worden uitgevoerd. Zie [beperkingen voor het gebruik van de Failoverclusterbeheer met beschikbaarheids groepen](https://msdn.microsoft.com/library/ff929171.aspx)voor meer informatie.
    >

Op dit moment hebt u een beschikbaarheids groep met replica's op twee exemplaren van SQL Server. U kunt de beschikbaarheids groep verplaatsen tussen exemplaren. U kunt nog geen verbinding maken met de beschikbaarheids groep omdat u geen listener hebt. Op virtuele machines van Azure is een load balancer vereist voor de listener. De volgende stap is het maken van de load balancer in Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Een Azure-load balancer maken

Op virtuele machines van Azure is een load balancer vereist voor een SQL Server-beschikbaarheids groep. Het load balancer bevat de IP-adressen voor de listeners voor de beschikbaarheids groep en het Windows Server-failovercluster. In deze sectie wordt een overzicht gegeven van het maken van de load balancer in de Azure Portal.

Een Azure Load Balancer kan een Standard Load Balancer of een basis Load Balancer zijn. Standard Load Balancer heeft meer functies dan de basis Load Balancer. Voor een beschikbaarheids groep is de Standard Load Balancer vereist als u een beschikbaarheids zone gebruikt (in plaats van een Beschikbaarheidsset). Zie [Load BALANCER SKU-vergelijking](../../../load-balancer/concepts-limitations.md#skus)voor meer informatie over het verschil tussen de Load Balancer typen.

1. Ga in het Azure Portal naar de resource groep waar uw SQL-servers zijn en klik op **+ toevoegen**.
1. Zoeken naar **Load Balancer**. Kies de load balancer gepubliceerd door micro soft.

   ![AG in Failoverclusterbeheer](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Klik op **Create**.
1. Configureer de volgende para meters voor de load balancer.

   | Instelling | Veld |
   | --- | --- |
   | **Naam** |Gebruik een tekst naam voor de load balancer, bijvoorbeeld **sqlLB**. |
   | **Type** |Intern |
   | **Virtueel netwerk** |Gebruik de naam van het virtuele netwerk van Azure. |
   | **Subnet** |Gebruik de naam van het subnet waarin de virtuele machine zich bevindt.  |
   | **Toewijzing van IP-adres** |Statisch |
   | **IP-adres** |Gebruik een beschikbaar adres van het subnet. Gebruik dit adres voor de listener van uw beschikbaarheids groep. Houd er rekening mee dat dit verschilt van het IP-adres van uw cluster.  |
   | **Abonnement** |Gebruik hetzelfde abonnement als de virtuele machine. |
   | **Locatie** |Gebruik dezelfde locatie als de virtuele machine. |

   De Blade Azure Portal moet er als volgt uitzien:

   ![Load Balancer maken](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Klik op **maken**om de Load Balancer te maken.

Als u de load balancer wilt configureren, moet u een back-end-pool maken, een test en de taakverdelings regels instellen. Doe dit in de Azure Portal.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Back-end-pool toevoegen voor de beschikbaarheids groep-listener

1. Ga in het Azure Portal naar uw beschikbaarheids groep. Mogelijk moet u de weer gave vernieuwen om de zojuist gemaakte load balancer weer te geven.

   ![Load Balancer zoeken in resource groep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Klik op de load balancer, klik op **back-end-Pools**en klik op **+ toevoegen**.

1. Typ een naam voor de back-end-groep.

1. Koppel de back-end-pool met de beschikbaarheidsset die de virtuele machines bevat.

1. Controleer onder **IP-configuraties**voor het doelnet **werk de virtuele machine** en kies beide virtuele machines die als host moeten fungeren voor de replica's van de beschikbaarheids groep. Neem de bestands share Witness-server niet op.

   >[!NOTE]
   >Als er geen virtuele machines zijn opgegeven, kunnen verbindingen alleen worden uitgevoerd op de primaire replica.

1. Klik op **OK** om de back-end-pool te maken.

### <a name="set-the-probe"></a>De test instellen

1. Klik op het load balancer, klik op **status controles**en klik op **+ toevoegen**.

1. Stel de status test voor de listener als volgt in:

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | SQLAlwaysOnEndPointProbe |
   | **Protocol** | TCP kiezen | TCP |
   | **Poort** | Alle ongebruikte poort | 59999 |
   | **Interval**  | De hoeveelheid tijd tussen de test pogingen in seconden |5 |
   | **Drempel waarde voor onjuiste status** | Het aantal opeenvolgende test fouten dat moet optreden voor een virtuele machine als een slechte status  | 2 |

1. Klik op **OK** om de status test in te stellen.

### <a name="set-the-load-balancing-rules"></a>De taakverdelings regels instellen

1. Klik op de load balancer, klik op **taakverdelings regels**en klik op **+ toevoegen**.

1. Stel de taakverdelings regels voor listener als volgt in.

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | SQLAlwaysOnEndPointListener |
   | **Frontend-IP-adres** | Kies een adres |Gebruik het adres dat u hebt gemaakt tijdens het maken van de load balancer. |
   | **Protocol** | TCP kiezen |TCP |
   | **Poort** | De poort voor de beschikbaarheids groep-listener gebruiken | 1433 |
   | **Backend-poort** | Dit veld wordt niet gebruikt wanneer er een zwevend IP-adres is ingesteld voor Direct Server Return | 1433 |
   | **Meet** |De naam die u hebt opgegeven voor de test | SQLAlwaysOnEndPointProbe |
   | **Sessie persistentie** | Vervolg keuzelijst | **Geen** |
   | **Time-out voor inactiviteit** | Minuten om een TCP-verbinding open te laten | 4 |
   | **Zwevend IP (Direct Server Return)** | |Ingeschakeld |

   > [!WARNING]
   > Direct server return is ingesteld tijdens het maken. De naam kan niet worden gewijzigd.

1. Klik op **OK** om de taakverdelings regels voor de listener in te stellen.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Het IP-adres van het cluster basis toevoegen voor het Windows Server-failovercluster (WSFC)

Het WSFC IP-adres moet ook op het load balancer zijn.

1. Klik in de portal, op dezelfde Azure-load balancer, op **frontend IP-configuratie** en klik op **+ toevoegen**. Gebruik het IP-adres dat u hebt geconfigureerd voor de WSFC in de kern bronnen van het cluster. Stel het IP-adres in als statisch.

1. Klik op de load balancer op **status controles**en klik op **+ toevoegen**.

1. Stel de status test voor het WSFC-cluster kern IP-adres als volgt in:

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | WSFCEndPointProbe |
   | **Protocol** | TCP kiezen | TCP |
   | **Poort** | Alle ongebruikte poort | 58888 |
   | **Interval**  | De hoeveelheid tijd tussen de test pogingen in seconden |5 |
   | **Drempel waarde voor onjuiste status** | Het aantal opeenvolgende test fouten dat moet optreden voor een virtuele machine als een slechte status  | 2 |

1. Klik op **OK** om de status test in te stellen.

1. De taakverdelings regels instellen. Klik op **taakverdelings regels**en klik op **+ toevoegen**.

1. Stel de taakverdelings regels voor het cluster core IP-adres als volgt in.

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | WSFCEndPoint |
   | **Frontend-IP-adres** | Kies een adres |Gebruik het adres dat u hebt gemaakt tijdens het configureren van het WSFC IP-adres. Dit wijkt af van het IP-adres van de listener |
   | **Protocol** | TCP kiezen |TCP |
   | **Poort** | Gebruik de poort voor het IP-adres van het cluster. Dit is een beschik bare poort die niet wordt gebruikt voor de listener-test poort. | 58888 |
   | **Backend-poort** | Dit veld wordt niet gebruikt wanneer er een zwevend IP-adres is ingesteld voor Direct Server Return | 58888 |
   | **Meet** |De naam die u hebt opgegeven voor de test | WSFCEndPointProbe |
   | **Sessie persistentie** | Vervolg keuzelijst | **Geen** |
   | **Time-out voor inactiviteit** | Minuten om een TCP-verbinding open te laten | 4 |
   | **Zwevend IP (Direct Server Return)** | |Ingeschakeld |

   > [!WARNING]
   > Direct server return is ingesteld tijdens het maken. De naam kan niet worden gewijzigd.

1. Klik op **OK** om de taakverdelings regels in te stellen.

## <a name="configure-listener"></a>De listener configureren

De volgende stap is het configureren van een beschikbaarheids groep-listener op het failovercluster.

> [!NOTE]
> Deze zelf studie laat zien hoe u één listener maakt, met één ILB IP-adres. Als u een of meer listeners wilt maken met behulp van een of meer IP-adressen, raadpleegt u de listener voor de [beschikbaarheids groep maken en Load Balancer | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Listener-poort instellen

Stel in SQL Server Management Studio de listener-poort in.

1. Start SQL Server Management Studio en maak verbinding met de primaire replica.

1. Navigeer naar AlwaysOn- **beschikbaarheids groepen** voor **hoge beschik baarheid** |  | listeners voor de **beschikbaarheids groep**.

1. U ziet nu de naam van de listener die u hebt gemaakt in Failoverclusterbeheer. Klik met de rechter muisknop op de naam van de listener en klik op **Eigenschappen**.

1. Geef in het vak **poort** het poort nummer op voor de beschikbaarheids groep-listener. 1433 is de standaard waarde en klik vervolgens op **OK**.

U hebt nu een SQL Server-beschikbaarheids groep in virtuele Azure-machines die worden uitgevoerd in de modus Resource Manager.

## <a name="test-connection-to-listener"></a>Verbinding met listener testen

De verbinding testen:

1. RDP naar een SQL Server dat zich in hetzelfde virtuele netwerk bevindt, maar geen eigenaar is van de replica. U kunt de andere SQL Server gebruiken in het cluster.

1. Gebruik het **Sqlcmd** -hulp programma om de verbinding te testen. Met het volgende script wordt bijvoorbeeld een **Sqlcmd** -verbinding met de primaire replica tot stand gebracht via de listener met Windows-verificatie:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Als de listener een andere poort dan de standaard poort (1433) gebruikt, geeft u de poort op in het connection string. De volgende Sqlcmd-opdracht maakt bijvoorbeeld verbinding met een listener op poort 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

De SQLCMD-verbinding maakt automatisch verbinding met het exemplaar van SQL Server als host voor de primaire replica.

> [!TIP]
> Zorg ervoor dat de poort die u opgeeft, is geopend op de firewall van beide SQL-servers. Beide servers vereisen een regel voor binnenkomende verbindingen voor de TCP-poort die u gebruikt. Zie [firewall regel toevoegen of bewerken](https://technet.microsoft.com/library/cc753558.aspx)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Voeg een IP-adres toe aan een Load Balancer voor een tweede beschikbaarheids groep](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).

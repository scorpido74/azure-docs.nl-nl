---
title: 'Zelfstudie: beschikbaarheidsgroep configureren'
description: In deze zelfstudie ziet u hoe u een SQL Server Always On Availability Group maakt op Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 426ba4c0ac84799b4d0e6bf9330508f928437fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060179"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Zelfstudie: Beschikbaarheidsgroep op Azure SQL Server VM handmatig configureren

In deze zelfstudie ziet u hoe u een SQL Server Always On Availability Group maakt op Azure Virtual Machines. Met de volledige zelfstudie wordt een beschikbaarheidsgroep gemaakt met een databasereplica op twee SQL-servers.

**Tijdsschatting**: Het duurt ongeveer 30 minuten voordat aan de voorwaarden is voldaan.

Het diagram illustreert wat u in de zelfstudie inbouwt.

![Beschikbaarheidsgroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Vereisten

De zelfstudie gaat ervan uit dat u een basiskennis hebt van SQL Server Always On Availability Groups. Zie [Overzicht van SQL Server (Always On Availability Groups)](https://msdn.microsoft.com/library/ff877884.aspx)als u meer informatie nodig hebt.

In de volgende tabel worden de vereisten weergegeven die u moet voltooien voordat u met deze zelfstudie begint:

|  |Vereiste |Beschrijving |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Twee SQL-servers | - In een Azure-beschikbaarheidsset <br/> - In één domein <br/> - Met Failover Clustering functie geïnstalleerd |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Bestandsshare voor clustergetuige |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server-serviceaccount | Domeinaccount |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server Agent-serviceaccount | Domeinaccount |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Firewallpoorten geopend | - SQL Server: **1433** voor standaardexemplaar <br/> - Database mirroring endpoint: **5022** of een beschikbare poort <br/> - Beschikbaarheid groep load balancer IP-adres health probe: **59999** of een beschikbare poort <br/> - Cluster core load balancer IP-adres health probe: **58888** of een beschikbare poort |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Failoverclustering-functie toevoegen | Beide SQL-servers vereisen deze functie |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Installatiedomeinaccount | - Lokale beheerder op elke SQL Server <br/> - Lid van SQL Server sysadmin fixed server role voor elk exemplaar van SQL Server  |


Voordat u met de zelfstudie begint, moet u [de vereisten voltooien voor het maken van always on-beschikbaarheidsgroepen in Azure Virtual Machines.](virtual-machines-windows-portal-sql-availability-group-prereq.md) Als deze vereisten al zijn voltooid, u naar [Cluster maken.](#CreateCluster)

  >[!NOTE]
  > Veel van de stappen in deze zelfstudie kunnen nu worden geautomatiseerd met [Azure SQL VM CLI-](virtual-machines-windows-sql-availability-group-cli.md) en [Azure Quickstart-sjablonen.](virtual-machines-windows-sql-availability-group-quickstart-template.md)


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Het cluster maken

Nadat de vereisten zijn voltooid, is de eerste stap het maken van een Windows Server Failover-cluster met twee SQL Severs en een getuigenserver.

1. RDP naar de eerste SQL Server met behulp van een domeinaccount dat een beheerder is op zowel SQL Servers als de witness-server.

   >[!TIP]
   >Als u het [document met vereisten hebt gevolgd,](virtual-machines-windows-portal-sql-availability-group-prereq.md)hebt u een account gemaakt met de naam **CORP\Install**. Gebruik dit account.

2. Selecteer **in**het dashboard **Serverbeheer** de optie Extra en klik vervolgens op **Failoverclusterbeheer**.
3. Klik in het linkerdeelvenster met de rechtermuisknop op **Failoverclusterbeheer**en klik vervolgens op **Een cluster maken**.
   ![Cluster maken](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. Maak in de wizard Cluster maken een cluster met één knooppunt door door de pagina's te stappen met de instellingen in de volgende tabel:

   | Pagina | Instellingen |
   | --- | --- |
   | Voordat u begint |Standaardinstellingen gebruiken |
   | Servers selecteren |Typ de eerste SQL Server-naam in **Servernaam invoeren** en klik op **Toevoegen**. |
   | Validatiewaarschuwing |Selecteer **Nee, ik heb geen ondersteuning van Microsoft nodig voor dit cluster en wil daarom de validatietests niet uitvoeren. Wanneer ik op Volgende klik, gaat u verder met Het cluster maken**. |
   | Toegangspunt voor het beheren van het cluster |Typ een clusternaam, bijvoorbeeld **SQLAGCluster1** in **clusternaam**.|
   | Bevestiging |Gebruik standaardinstellingen, tenzij u opslagruimten gebruikt. Zie de notitie na deze tabel. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Het IP-adres van het failovercluster van de Windows-server instellen

  > [!NOTE]
  > Op Windows Server 2019 maakt het cluster een **gedistribueerde servernaam** in plaats van de naam van het **clusternetwerk**. Als u Windows Server 2019 gebruikt, slaat u alle stappen over die verwijzen naar de naam van de clusterkern in deze zelfstudie. U een clusternetwerknaam maken met [PowerShell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019). Bekijk het [failovercluster: clusternetwerkobject](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) voor meer informatie. 

1. Schuif in **Failoverclusterbeheer**omlaag naar **Clustercore-bronnen** en vouw de clusterdetails uit. U ziet zowel de **bronnen Naam** als het **IP-adres** in de **status Mislukt.** De IP-adresbron kan niet online worden gebracht omdat het cluster hetzelfde IP-adres krijgt toegewezen als de machine zelf, daarom is het een duplicaatadres.

2. Klik met de rechtermuisknop op de mislukte **IP-adresbron** en klik vervolgens op **Eigenschappen**.

   ![Clustereigenschappen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Selecteer **Statisch IP-adres** en geef een beschikbaar adres op van hetzelfde subnet als uw virtuele machines.

4. Klik in de sectie **Clusterkernbronnen** met de rechtermuisknop op clusternaam en klik op **Online brengen**. Wacht vervolgens tot beide bronnen online zijn. Wanneer de bron voor de clusternaam online komt, wordt de DC-server bijgewerkt met een nieuw AD-computeraccount. Gebruik dit AD-account om de geclusterde service Beschikbaarheidsgroep later uit te voeren.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>De andere SQL Server toevoegen aan cluster

Voeg de andere SQL Server toe aan het cluster.

1. Klik in de browserstructuur met de rechtermuisknop op het cluster en klik op **Knooppunt toevoegen**.

    ![Knooppunt toevoegen aan het cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. Klik in de **wizard Knooppunt toevoegen**op **Volgende**. Voeg op de pagina **Servers selecteren** de tweede SQL Server toe. Typ de servernaam in **Servernaam invoeren** en klik op **Toevoegen**. Als u klaar bent, klikt u op **Volgende**.

1. Klik op de pagina **Validatiewaarschuwing** op **Nee** (in een productiescenario moet u de validatietests uitvoeren). Klik vervolgens op **Volgende.**

8. Schakel **op** de pagina Bevestiging als u opslagruimten gebruikt, het selectievakje Uit met het label **Alle in aanmerking komende opslag toevoegen aan het cluster.**

   ![Bevestiging van knooppunt toevoegen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Als u opslagruimten gebruikt en het selectievakje **Alle in aanmerking komende opslag toevoegen aan het cluster**niet uitschakelt, ontkoppelt Windows de virtuele schijven tijdens het clusterproces. Als gevolg hiervan worden ze niet weergegeven in Schijfbeheer of Explorer totdat de opslagruimten uit het cluster zijn verwijderd en opnieuw zijn gekoppeld met PowerShell. Opslagruimten groepeert meerdere schijven in opslaggroepen. Zie [Opslagruimten voor](https://technet.microsoft.com/library/hh831739)meer informatie.

1. Klik op **Volgende**.

1. Klik op **Voltooien**.

   Failoverclusterbeheer laat zien dat uw cluster een nieuw knooppunt heeft en geeft het weer in de container **Knooppunten.**

10. Afmelden bij de externe desktopsessie.

### <a name="add-a-cluster-quorum-file-share"></a>Een clusterquorumbestandsshare toevoegen

In dit voorbeeld gebruikt het Windows-cluster een bestandsshare om een clusterquorum te maken. In deze zelfstudie wordt een quorum knooppunt en meerderheid sleerien van de bestandsmeerderheid gebruikt. Raadpleeg [Understanding Quorum Configurations in a Failover Cluster](https://technet.microsoft.com/library/cc731739.aspx) (Inzicht in quorumconfiguraties in een failovercluster) voor meer informatie.

1. Maak verbinding met de server van het getuigenlid van het bestand met een externe desktopsessie.

1. Klik op **Serverbeheer**op **Extra**. Computerbeheer **openen**.

1. Klik **op Gedeelde mappen**.

1. Klik met de rechtermuisknop op **Shares**en klik op **Nieuw delen...**.

   ![Nieuw aandeel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Gebruik **Wizard Een gedeelde map maken** om een share te maken.

1. Klik op **Mappad**op **Bladeren** en zoek of maak een pad voor de gedeelde map. Klik op **Volgende**.

1. Controleer in **Naam, Beschrijving en Instellingen** de naam en het pad van de share. Klik op **Volgende**.

1. Stel Machtigingen voor **gedeelde mappen** instellen Machtigingen **aanpassen**. Klik **op Aangepast...**.

1. Klik **op Machtigingen aanpassen**op **Toevoegen...**.

1. Zorg ervoor dat het account dat wordt gebruikt om het cluster te maken de volledige controle heeft.

   ![Nieuw aandeel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Klik op **OK**.

1. Klik in **Machtigingen voor gedeelde mappen**op **Voltooien**. Klik nogmaals **op Voltooien.**  

1. Afmelden bij de server

### <a name="configure-cluster-quorum"></a>Clusterquorum configureren

Stel vervolgens het clusterquorum in.

1. Maak verbinding met het eerste clusterknooppunt met extern bureaublad.

1. Klik in **Failoverclusterbeheer**met de rechtermuisknop op het cluster, wijs **Meer acties**aan en klik op Instellingen **voor clusterquorum configureren...**.

   ![Nieuw aandeel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. Klik **in de wizard Clusterquorum configureren**op **Volgende**.

1. Kies **in De optie Quorumconfiguratie selecteren**de optie De **quorumgetuige**selecteren en klik op **Volgende**.

1. Klik op **Quorumgetuige selecteren**op **Een getuige voor bestandsshare configureren**.

   >[!TIP]
   >Windows Server 2016 ondersteunt een cloudgetuige. Als u dit type getuige kiest, hebt u geen getuige nodig voor het delen van bestanden. Zie [Een cloudgetuige implementeren voor een failovercluster voor](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)meer informatie voor meer informatie. Deze zelfstudie maakt gebruik van een getuige voor bestandsshare, die wordt ondersteund door eerdere besturingssystemen.

1. Typ in **Getuige voor bestandsdelen configureren**het pad voor het aandeel dat u hebt gemaakt. Klik op **Volgende**.

1. Controleer de instellingen op **Bevestiging**. Klik op **Volgende**.

1. Klik op **Voltooien**.

De clusterkernbronnen zijn geconfigureerd met een getuige voor bestandsshare.

## <a name="enable-availability-groups"></a>Beschikbaarheidsgroepen inschakelen

Schakel vervolgens de functie **Beschikbaarheidsgroepen alwayson in.** Doe deze stappen op beide SQL-servers.

1. Start SQL Server **Configuration Manager**vanaf het **startscherm** .
2. Klik in de browserstructuur op **SQL Server Services**en klik vervolgens met de rechtermuisknop op de SQL **Server-service (MSSQLSERVER)** en klik op **Eigenschappen**.
3. Klik op het tabblad **AlwaysOn Hoge beschikbaarheid** en selecteer **AlwaysOn beschikbaarheidsgroepen inschakelen**als volgt:

    ![Beschikbaarheidsgroepen voor AlwaysOn inschakelen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Klik op **Toepassen**. Klik op **OK** in het pop-updialoogvenster.

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

## <a name="create-a-database-on-the-first-sql-server"></a>Een database maken op de eerste SQL Server

1. Start het RDP-bestand naar de eerste SQL Server met een domeinaccount dat lid is van de vaste serverrol van sysadmin.
1. Open SQL Server Management Studio en maak verbinding met de eerste SQL Server.
7. Klik in **Object Explorer**met de rechtermuisknop op **Databases** en klik op **Nieuwe database**.
8. Typ **MyDB1**in **Databasenaam**en klik op **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a>Een back-upshare maken

1. Klik op de eerste SQL Server in **Serverbeheer**op **Extra**. Computerbeheer **openen**.

1. Klik **op Gedeelde mappen**.

1. Klik met de rechtermuisknop op **Shares**en klik op **Nieuw delen...**.

   ![Nieuw aandeel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Gebruik **Wizard Een gedeelde map maken** om een share te maken.

1. Klik op **Mappad**op **Bladeren** en zoek of maak een pad voor de gedeelde map met databaseback-ups. Klik op **Volgende**.

1. Controleer in **Naam, Beschrijving en Instellingen** de naam en het pad van de share. Klik op **Volgende**.

1. Stel Machtigingen voor **gedeelde mappen** instellen Machtigingen **aanpassen**. Klik **op Aangepast...**.

1. Klik **op Machtigingen aanpassen**op **Toevoegen...**.

1. Controleer of de SQL Server- en SQL Server Agent-serviceaccounts voor beide servers de volledige controle hebben.

   ![Nieuw aandeel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Klik op **OK**.

1. Klik in **Machtigingen voor gedeelde mappen**op **Voltooien**. Klik nogmaals **op Voltooien.**  

### <a name="take-a-full-backup-of-the-database"></a>Neem een volledige back-up van de database

U moet een back-up maken van de nieuwe database om de logboekketen te initialiseren. Als u geen back-up van de nieuwe database neemt, kan deze niet worden opgenomen in een beschikbaarheidsgroep.

1. Klik in **Object Explorer**met de rechtermuisknop op de database, **wijs Taken aan...**, klik op **Back-up maken**.

1. Klik op **OK** om een volledige back-up naar de standaardback-uplocatie te nemen.

## <a name="create-the-availability-group"></a>De beschikbaarheidsgroep maken
U bent nu klaar om een beschikbaarheidsgroep te configureren met de volgende stappen:

* Maak een database op de eerste SQL Server.
* Neem zowel een volledige back-up als een back-up van het transactielogboek van de database
* De volledige back-ups herstellen en back-ups naar de tweede SQL Server herstellen met de optie **NORECOVERY**
* De beschikbaarheidsgroep **(AG1)** maken met synchrone commit, automatische failover en leesbare secundaire replica's

### <a name="create-the-availability-group"></a>Maak de beschikbaarheidsgroep:

1. Op externe desktopsessie naar de eerste SQL Server. Klik in **Object Explorer** in SSMS met de rechtermuisknop **op AlwaysOn High Availability** en klik op **wizard Groep nieuwe beschikbaarheid**.

    ![Wizard Nieuwe beschikbaarheid stoven](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Klik **op** de pagina Inleiding op **Volgende**. Typ op de pagina **Beschikbaarheidsgroepnaam opgeven** een naam voor de beschikbaarheidsgroep, bijvoorbeeld **AG1**, in **de groepsnaam Beschikbaarheid**. Klik op **Volgende**.

    ![Nieuwe wizard AG, AG-naam opgeven](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Selecteer **op** de pagina Databases selecteren de database en klik op **Volgende**.

   >[!NOTE]
   >De database voldoet aan de vereisten voor een beschikbaarheidsgroep omdat u ten minste één volledige back-up hebt gemaakt op de beoogde primaire replica.

   ![Nieuwe wizard AG, Databases selecteren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Klik op de pagina **Replica's opgeven** op **Replica toevoegen**.

   ![Nieuwe wizard AG, Replica's opgeven](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Het dialoogvenster **Verbinding maken met** server wordt weergegeven. Typ de naam van de tweede server in **Servernaam**. Klik op **Verbinden**.

   Terug in de pagina **Replica's opgeven** ziet u nu de tweede server die wordt weergegeven in **Beschikbaarheidsreplica's.** Configureer de replica's als volgt.

   ![Nieuwe wizard AG, Replica's opgeven (voltooid)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Klik **op Eindpunten** om het eindpunt voor het spiegelen van de database voor deze beschikbaarheidsgroep te bekijken. Gebruik dezelfde poort die u hebt gebruikt toen u de [firewallregel instelt voor databasemirroring-eindpunten.](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall)

    ![Nieuwe wizard AG, Eerste gegevenssynchronisatie selecteren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Selecteer op de pagina **Initiële gegevenssynchronisatie** selecteren de optie **Volledig** en geef een gedeelde netwerklocatie op. Gebruik voor de locatie het [back-upaandeel dat u hebt gemaakt.](#backupshare) In het voorbeeld was het ** \\ \\ \<First SQL\>Server\\\Backup**. Klik op **Volgende**.

   >[!NOTE]
   >Volledige synchronisatie neemt een volledige back-up van de database in eerste instantie van SQL Server en herstelt deze naar de tweede instantie. Voor grote databases wordt volledige synchronisatie niet aanbevolen omdat het lang kan duren. U deze tijd verkorten door handmatig een back-up van de database te maken en deze te herstellen met `NO RECOVERY`. Als de database al `NO RECOVERY` is hersteld met op de tweede SQL Server voordat u de beschikbaarheidsgroep configureert, kiest **u Join only**. Als u de back-up wilt maken nadat u de beschikbaarheidsgroep hebt geconfigureerd, kiest u **Initiële gegevenssynchronisatie overslaan**.

    ![Nieuwe wizard AG, Eerste gegevenssynchronisatie selecteren](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Klik **op** de pagina Validatie op **Volgende**. Deze pagina moet er hetzelfde uitzien als de volgende afbeelding:

    ![Nieuwe wizard AG, Validatie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Er is een waarschuwing voor de listenerconfiguratie omdat u geen listener voor beschikbaarheidsgroep hebt geconfigureerd. U deze waarschuwing negeren omdat u op virtuele Azure-machines de listener maakt nadat u de Azure-load balancer hebt gemaakt.

10. Klik **op** de pagina Overzicht op **Voltooien**en wacht terwijl de wizard de nieuwe beschikbaarheidsgroep configureert. Klik **op** de pagina Voortgang op **Meer details** om de gedetailleerde voortgang weer te geven. Zodra de wizard is voltooid, controleert u de pagina **Resultaten** om te controleren of de beschikbaarheidsgroep is gemaakt.

     ![Nieuwe wizard AG, Resultaten](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Klik op **Sluiten** om de wizard af te sluiten.

### <a name="check-the-availability-group"></a>De beschikbaarheidsgroep controleren

1. Vouw in **Object Explorer** **AlwaysOn High Availability**uit en vouw **beschikbaarheidsgroepen**uit . U ziet nu de nieuwe beschikbaarheidsgroep in deze container. Klik met de rechtermuisknop op de beschikbaarheidsgroep en klik op **Dashboard weergeven**.

   ![AG-dashboard weergeven](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Uw **AlwaysOn Dashboard** moet er hetzelfde uitzien als dit.

   ![AG-dashboard](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   U de replica's, de failovermodus van elke replica en de synchronisatiestatus zien.

2. Klik in **Failoverclusterbeheer**op uw cluster. Selecteer **Rollen**. De naam beschikbaarheidsgroep die u hebt gebruikt, is een rol in het cluster. Die beschikbaarheidsgroep heeft geen IP-adres voor clientverbindingen, omdat u geen listener hebt geconfigureerd. U configureert de listener nadat u een Azure-load balancer hebt gemaakt.

   ![AG in Failoverclusterbeheer](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Probeer niet te mislukken via de beschikbaarheidsgroep van de failoverclusterbeheer. Alle failoverbewerkingen moeten worden uitgevoerd vanuit **AlwaysOn Dashboard** in SSMS. Zie [Beperkingen voor het gebruik van de failoverclusterbeheer met beschikbaarheidsgroepen](https://msdn.microsoft.com/library/ff929171.aspx)voor meer informatie .
    >

Op dit moment hebt u een beschikbaarheidsgroep met replica's op twee exemplaren van SQL Server. U de beschikbaarheidsgroep tussen instanties verplaatsen. U nog geen verbinding maken met de beschikbaarheidsgroep omdat u geen listener hebt. In azure virtuele machines, de luisteraar vereist een load balancer. De volgende stap is het maken van de load balancer in Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Een Azure-load balancer maken

Op virtuele Azure-machines vereist een SQL Server Availability Group een load balancer. De load balancer bevat de IP-adressen voor de listeners Beschikbaarheidsgroep en het Failovercluster van Windows Server. In deze sectie wordt samengevat hoe u de load balancer maakt in de Azure-portal.

Een Azure Load Balancer kan een Standaard Load Balancer of een Basic Load Balancer zijn. Standard Load Balancer heeft meer functies dan de Basic Load Balancer. Voor een beschikbaarheidsgroep is de standaard load balancer vereist als u een beschikbaarheidszone gebruikt (in plaats van een beschikbaarheidsset). Zie [Load Balancer SKU-vergelijking](../../../load-balancer/concepts-limitations.md#skus)voor meer informatie over het verschil tussen de typen load balancer.

1. Ga in de Azure-portal naar de brongroep waar uw SQL-servers zich bevinden en klik op **+ Toevoegen**.
1. Zoeken naar **Load Balancer**. Kies de door Microsoft gepubliceerde load balancer.

   ![AG in Failoverclusterbeheer](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Klik **op Maken**.
1. Configureer de volgende parameters voor de load balancer.

   | Instelling | Veld |
   | --- | --- |
   | **Naam** |Gebruik een tekstnaam voor de load balancer, bijvoorbeeld **sqlLB**. |
   | **Type** |Intern |
   | **Virtueel netwerk** |Gebruik de naam van het virtuele Azure-netwerk. |
   | **Subnet** |Gebruik de naam van het subnet waarin de virtuele machine zich bevindt.  |
   | **Toewijzing ip-adres** |Statisch |
   | **IP-adres** |Gebruik een beschikbaar adres van subnet. Gebruik dit adres voor de listener van de beschikbaarheidsgroep. Houd er rekening mee dat dit anders is dan het IP-adres van uw cluster.  |
   | **Abonnement** |Gebruik hetzelfde abonnement als de virtuele machine. |
   | **Locatie** |Gebruik dezelfde locatie als de virtuele machine. |

   Het Azure-portalblad moet er als volgt uitzien:

   ![Load Balancer maken](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Klik **op Maken**om de load balancer te maken.

Als u de load balancer wilt configureren, moet u een backendpool, een sonde, maken en de regels voor taakverdeling instellen. Doe deze in de Azure-portal.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Backendpool toevoegen voor de listener van de beschikbaarheidsgroep

1. Ga in de Azure-portal naar uw beschikbaarheidsgroep. Mogelijk moet u de weergave vernieuwen om de nieuw gemaakte load balancer te zien.

   ![Load Balancer zoeken in resourcegroep](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Klik op de load balancer, klik op **Backend-pools**en klik op **+Toevoegen**.

1. Typ een naam voor de backendpool.

1. Koppel de backendpool aan de beschikbaarheidsset die de VM's bevat.

1. Controleer **onder IP-configuraties van het doelnetwerk** **virtuele machine** en kies beide virtuele machines die de beschikbaarheidsgroepreplica's hosten. Neem de file share witness-server niet op.

   >[!NOTE]
   >Als beide virtuele machines niet zijn opgegeven, worden de verbindingen alleen verbroken met de primaire replica.

1. Klik op **OK** om de backendpool te maken.

### <a name="set-the-probe"></a>De sonde instellen

1. Klik op de load balancer, klik op **Statussondes**en klik op **+Toevoegen**.

1. Stel de statussonde van listener als volgt in:

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | SQLAlwaysOnEndPointProbe |
   | **Protocol** | Kies TCP | TCP |
   | **Poort** | Ongebruikte poort | 59999 |
   | **Interval**  | De hoeveelheid tijd tussen sondepogingen in seconden |5 |
   | **Ongezonde drempelwaarde** | Het aantal opeenvolgende sondestoringen dat moet optreden om een virtuele machine als ongezond te laten worden beschouwd  | 2 |

1. Klik op **OK** om de statussonde in te stellen.

### <a name="set-the-load-balancing-rules"></a>De regels voor het balanceren van de lastenverdeling instellen

1. Klik op de load balancer, klik op **Taakverdelingsregels**en klik op **+Toevoegen**.

1. Stel de regels voor het balanceren van de listener als volgt in.

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | SQLAlwaysOnEndPointListener |
   | **Frontend IP-adres** | Een adres kiezen |Gebruik het adres dat u hebt gemaakt toen u de load balancer maakte. |
   | **Protocol** | Kies TCP |TCP |
   | **Poort** | De poort gebruiken voor de listener van de beschikbaarheidsgroep | 1433 |
   | **Backend-poort** | Dit veld wordt niet gebruikt wanneer Floating IP is ingesteld voor direct serverretouren | 1433 |
   | **Sonde** |De naam die u voor de sonde hebt opgegeven | SQLAlwaysOnEndPointProbe |
   | **Sessiepersistentie** | Vervolgkeuzelijst | **Geen** |
   | **Time-out voor inactiviteit** | Minuten om een TCP-verbinding open te houden | 4 |
   | **Zwevend IP (direct server retour)** | |Ingeschakeld |

   > [!WARNING]
   > Direct serverretour wordt ingesteld tijdens het maken. De naam kan niet worden gewijzigd.

1. Klik op **OK** om de regels voor het balanceren van de listener te instellen.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Het IP-adres van de clusterkern toevoegen voor het Failovercluster (Windows Server Failovercluster)

Het WSFC IP-adres moet ook op de load balancer staan.

1. Klik in de portal op dezelfde Azure-loadbalancer op **Frontend IP-configuratie** en klik op **+Toevoegen**. Gebruik het IP-adres dat u hebt geconfigureerd voor de WSFC in de clusterkernbronnen. Stel het IP-adres in als statisch.

1. Klik op de load balancer op **Statussondes**en klik **op +Toevoegen**.

1. Stel de IP-adresstatussssprobe van de WSFC-clusterkern als volgt in:

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | WSFCEndPointProbe |
   | **Protocol** | Kies TCP | TCP |
   | **Poort** | Ongebruikte poort | 58888 |
   | **Interval**  | De hoeveelheid tijd tussen sondepogingen in seconden |5 |
   | **Ongezonde drempelwaarde** | Het aantal opeenvolgende sondestoringen dat moet optreden om een virtuele machine als ongezond te laten worden beschouwd  | 2 |

1. Klik op **OK** om de statussonde in te stellen.

1. Stel de regels voor het balanceren van de lasten in. Klik **op Taakverdelingsregels**en klik op **+Toevoegen**.

1. Stel de regels voor het balanceren van de clusterkern-IP-adres als volgt in.

   | Instelling | Beschrijving | Voorbeeld
   | --- | --- |---
   | **Naam** | Tekst | WSFCEndPoint |
   | **Frontend IP-adres** | Een adres kiezen |Gebruik het adres dat u hebt gemaakt toen u het WSFC-IP-adres configureerde. Dit is anders dan het IP-adres van de luisteraar |
   | **Protocol** | Kies TCP |TCP |
   | **Poort** | Gebruik de poort voor het cluster-IP-adres. Dit is een beschikbare poort die niet wordt gebruikt voor de poort van de listenersonde. | 58888 |
   | **Backend-poort** | Dit veld wordt niet gebruikt wanneer Floating IP is ingesteld voor direct serverretouren | 58888 |
   | **Sonde** |De naam die u voor de sonde hebt opgegeven | WSFCEndPointProbe |
   | **Sessiepersistentie** | Vervolgkeuzelijst | **Geen** |
   | **Time-out voor inactiviteit** | Minuten om een TCP-verbinding open te houden | 4 |
   | **Zwevend IP (direct server retour)** | |Ingeschakeld |

   > [!WARNING]
   > Direct serverretour wordt ingesteld tijdens het maken. De naam kan niet worden gewijzigd.

1. Klik op **OK** om de regels voor het balanceren van de lasten in te stellen.

## <a name="configure-the-listener"></a><a name="configure-listener"></a>De listener configureren

Het volgende ding om te doen is het configureren van een beschikbaarheidgroep listener op de failover cluster.

> [!NOTE]
> In deze zelfstudie ziet u hoe u één listener maakt - met één Ip-adres van ILB. Zie [Listener beschikbaarheidsgroep en load balancer maken | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Listenerpoort instellen

Stel in SQL Server Management Studio de listenerpoort in.

1. Start SQL Server Management Studio en maak verbinding met de primaire replica.

1. Navigeer naar**de groepslisteners****beschikbaarheidvan beschikbaarheidsgroepen** | voor **beschikbaarheid alwayson met hoge beschikbaarheid** | .

1. U moet nu de listenernaam zien die u hebt gemaakt in Failoverclusterbeheer. Klik met de rechtermuisknop op de naam van de listener en klik op **Eigenschappen**.

1. Geef **in** het vak Poort het poortnummer op voor de listener Beschikbaarheidsgroep. 1433 is de standaardinstelling en klik vervolgens op **OK**.

U hebt nu een SQL Server Availability Group in Azure virtuele machines die worden uitgevoerd in de Resource Manager-modus.

## <a name="test-connection-to-listener"></a>Verbinding met listener testen

Ga als het gaat om het testen van de verbinding:

1. RDP naar een SQL Server die zich in hetzelfde virtuele netwerk bevindt, maar niet de eigenaar is van de replica. U de andere SQL Server in het cluster gebruiken.

1. Gebruik **sqlcmd** utility om de verbinding te testen. In het volgende script wordt bijvoorbeeld een **sqlcmd-verbinding** met de primaire replica gemaakt via de listener met Windows-verificatie:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Als de listener een andere poort gebruikt dan de standaardpoort (1433), geeft u de poort in de verbindingstekenreeks op. De volgende sqlcmd-opdracht maakt bijvoorbeeld verbinding met een listener op poort 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

De SQLCMD-verbinding maakt automatisch verbinding met de instantie van SQL Server die de primaire replica host.

> [!TIP]
> Zorg ervoor dat de poort die u opgeeft, is geopend op de firewall van beide SQL-servers. Beide servers vereisen een inkomende regel voor de TCP-poort die u gebruikt. Zie [Firewallregel toevoegen of bewerken voor](https://technet.microsoft.com/library/cc753558.aspx)meer informatie .

## <a name="next-steps"></a>Volgende stappen

- [Voeg een IP-adres toe aan een load balancer voor een tweede beschikbaarheidsgroep](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).

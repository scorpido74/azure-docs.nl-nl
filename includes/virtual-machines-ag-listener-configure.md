---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 73ba78eca710f0b98b2a209494519cb8003e554b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75466872"
---
De beschikbaarheids groep-listener is een IP-adres en netwerk naam waarop de SQL Server beschikbaarheids groep luistert. Ga als volgt te werk om de beschikbaarheids groep-listener te maken:

1. <a name="getnet"></a>Haal de naam van de cluster netwerk bron op.

    a. Gebruik RDP om verbinding te maken met de virtuele Azure-machine die als host fungeert voor de primaire replica. 

    b. Open Failoverclusterbeheer.

    c. Selecteer het knoop punt **netwerken** en noteer de naam van het cluster netwerk. Gebruik deze naam in de `$ClusterNetworkName` variabele in het Power shell-script. In de volgende afbeelding is de cluster netwerk naam **cluster netwerk 1**:

   ![Cluster netwerk naam](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Voeg het client toegangs punt toe.  
    Het client toegangs punt is de netwerk naam die toepassingen gebruiken om verbinding te maken met de data bases in een beschikbaarheids groep. Maak het client toegangs punt in Failoverclusterbeheer.

    a. Vouw de cluster naam uit en klik vervolgens op **rollen**.

    b. Klik in het deel venster **functies** met de rechter muisknop op de naam van de beschikbaarheids groep en selecteer vervolgens **bron** > **Client Access Point**toevoegen.

   ![Client toegangs punt](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Maak in het vak **naam** een naam voor deze nieuwe listener. 
   De naam voor de nieuwe listener is de netwerk naam die toepassingen gebruiken om verbinding te maken met data bases in de SQL Server beschikbaarheids groep.

    d. Klik twee keer op **volgende** en klik vervolgens op **volt ooien**om het maken van de listener te volt ooien. Zet de listener of bron op dit punt niet online.

1. Zet de cluster functie voor de beschikbaarheids groep offline. Klik in **Failoverclusterbeheer** onder **rollen**met de rechter muisknop op de rol en selecteer **Stop Role**.

1. <a name="congroup"></a>Configureer de IP-resource voor de beschikbaarheids groep.

    a. Klik op het tabblad **resources** en vouw vervolgens het client toegangs punt uit dat u hebt gemaakt.  
    Het client toegangs punt is offline.

   ![Client toegangs punt](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Klik met de rechter muisknop op de IP-bron en klik vervolgens op Eigenschappen. Noteer de naam van het IP-adres en gebruik het in de `$IPResourceName` variabele in het Power shell-script.

    c. Klik onder **IP-adres**op **statisch IP-adres**. Stel het IP-adres in als het adres dat u hebt gebruikt bij het instellen van het load balancer adres op het Azure Portal.

   ![IP-bron](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Zorg ervoor dat de resource van de SQL Server-beschikbaarheids groep afhankelijk is van het client toegangs punt.

    a. Klik in Failoverclusterbeheer op **rollen**en klik vervolgens op uw beschikbaarheids groep.

    b. Klik op het tabblad **resources** onder **andere resources**met de rechter muisknop op de resource groep Beschik baarheid en klik vervolgens op **Eigenschappen**. 

    c. Voeg op het tabblad Afhankelijkheden de naam van het client toegangs punt (de listener) toe.

   ![IP-bron](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klik op **OK**.

1. <a name="listname"></a>Maak de bron van het client toegangs punt afhankelijk van het IP-adres.

    a. Klik in Failoverclusterbeheer op **rollen**en klik vervolgens op uw beschikbaarheids groep. 

    b. Klik op het tabblad **resources** met de rechter muisknop op de bron van het client toegangs punt onder **Server naam**en klik vervolgens op **Eigenschappen**. 

   ![IP-bron](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klik op het tabblad **afhankelijkheden** . Controleer of het IP-adres afhankelijk is. Als dat niet het geval is, stelt u een afhankelijkheid op het IP-adres in. Als er meerdere bronnen worden weer gegeven, controleert u of de IP-adressen of, niet en, afhankelijkheden hebben. Klik op **OK**. 

   ![IP-bron](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >U kunt controleren of de afhankelijkheden correct zijn geconfigureerd. Ga in Failoverclusterbeheer naar rollen, klik met de rechter muisknop op de beschikbaarheids groep, klik op **meer acties**en klik vervolgens op **afhankelijkheids rapport weer geven**. Wanneer de afhankelijkheden correct zijn geconfigureerd, is de beschikbaarheids groep afhankelijk van de netwerk naam en de netwerk naam is afhankelijk van het IP-adres. 


1. <a name="setparam"></a>Stel de cluster parameters in Power shell in.

   a. Kopieer het volgende Power shell-script naar een van uw SQL Server-exemplaren. Werk de variabelen voor uw omgeving bij.

   - `$ListenerILBIP`is het IP-adres dat u hebt gemaakt in de Azure-load balancer voor de beschikbaarheids groep-listener.
    
   - `$ListenerProbePort`is de poort die u hebt geconfigureerd op de Azure-load balancer voor de beschikbaarheids groep-listener.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Stel de cluster parameters in door het Power shell-script uit te voeren op een van de cluster knooppunten.  

   > [!NOTE]
   > Als uw SQL Server instanties zich in verschillende regio's bevinden, moet u het Power shell-script twee keer uitvoeren. De eerste keer gebruikt u de `$ListenerILBIP` en `$ListenerProbePort` uit de eerste regio. De tweede keer gebruikt u de `$ListenerILBIP` en `$ListenerProbePort` van de tweede regio. De cluster netwerk naam en de naam van het cluster-IP-adres zijn ook verschillend voor elke regio.

1. Breng de cluster functie voor de beschikbaarheids groep online. Klik in **Failoverclusterbeheer** onder **rollen**met de rechter muisknop op de rol en selecteer **rol starten**.

Herhaal indien nodig de bovenstaande stappen om de cluster parameters in te stellen voor het IP-adres van het WSFC-cluster.

1. Haal de IP-adres naam van het IP-adres van het WSFC-cluster op. Zoek in **Failoverclusterbeheer** onder **cluster kern resources**naar **Server naam**.

1. Klik met de rechter muisknop op **IP-adres**en selecteer **Eigenschappen**.

1. Kopieer de **naam** van het IP-adres. Het kan zijn `Cluster IP Address`. 

1. <a name="setwsfcparam"></a>Stel de cluster parameters in Power shell in.
  
   a. Kopieer het volgende Power shell-script naar een van uw SQL Server-exemplaren. Werk de variabelen voor uw omgeving bij.

   - `$ClusterCoreIP`is het IP-adres dat u hebt gemaakt op de Azure-load balancer voor de WSFC-kern cluster bron. Het verschilt van het IP-adres voor de listener van de beschikbaarheids groep.

   - `$ClusterProbePort`is de poort die u hebt geconfigureerd op de Azure-load balancer voor de WSFC-status test. Dit wijkt af van de test voor de beschikbaarheids groep-listener.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Stel de cluster parameters in door het Power shell-script uit te voeren op een van de cluster knooppunten.  

>[!WARNING]
>De status test poort van de listener voor de beschikbaarheids groep moet verschillen van de status test poort van het cluster kern IP-adres. In deze voor beelden is de listener-poort 59999 en de status test poort van het cluster kern IP-adres is 58888. Beide poorten vereisen een regel voor binnenkomende Firewall toestaan.

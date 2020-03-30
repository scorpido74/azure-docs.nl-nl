---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 73ba78eca710f0b98b2a209494519cb8003e554b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75466872"
---
De authenticiteit van de beschikbaarheidsgroep is een IP-adres en netwerknaam waarop de SQL Server-beschikbaarheidsgroep luistert. Ga als volgt te werk om de listener van de beschikbaarheidsgroep te maken:

1. <a name="getnet"></a>De naam van de clusternetwerkbron op.

    a. Gebruik RDP om verbinding te maken met de virtuele Azure-machine die de primaire replica host. 

    b. Failoverclusterbeheer openen.

    c. Selecteer het knooppunt **Netwerken** en noteer de naam van het clusternetwerk. Gebruik deze naam `$ClusterNetworkName` in de variabele in het PowerShell-script. In de volgende afbeelding is de naam van het clusternetwerk **clusternetwerk 1:**

   ![Clusternetwerknaam](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>Voeg het toegangspunt van de client toe.  
    Het clienttoegangspunt is de netwerknaam die toepassingen gebruiken om verbinding te maken met de databases in een beschikbaarheidsgroep. Maak het clienttoegangspunt in Failoverclusterbeheer.

    a. Vouw de clusternaam uit en klik op **Rollen**.

    b. Klik **in** het deelvenster Rollen met de rechtermuisknop op de naam van de beschikbaarheidsgroep en selecteer vervolgens Het toegangspunt **voor** > **resources**toevoegen .

   ![Toegangspunt voor client](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Maak in het vak **Naam** een naam voor deze nieuwe listener. 
   De naam voor de nieuwe listener is de netwerknaam die toepassingen gebruiken om verbinding te maken met databases in de sql server-beschikbaarheidsgroep.

    d. Als u de listener wilt maken, klikt u twee maal op **Volgende** en klikt u vervolgens op **Voltooien**. Breng de luisteraar of bron niet online op dit punt.

1. Haal de clusterrol beschikbaarheidsgroep offline. Klik in **Failoverclusterbeheer** onder **Rollen**met de rechtermuisknop op de rol en selecteer **Rol stoppen**.

1. <a name="congroup"></a>Configureer de IP-bron voor de beschikbaarheidsgroep.

    a. Klik op het tabblad **Resources** en vouw het clienttoegangspunt uit dat u hebt gemaakt.  
    Het toegangspunt voor de client is offline.

   ![Toegangspunt voor client](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Klik met de rechtermuisknop op de IP-bron en klik vervolgens op eigenschappen. Let op de naam van het IP-adres en gebruik deze in de `$IPResourceName` variabele in het PowerShell-script.

    c. Klik **onder IP-adres**op **Statisch IP-adres**. Stel het IP-adres in als hetzelfde adres dat u hebt gebruikt toen u het adres van de load balancer instelt op de Azure-portal.

   ![IP-bron](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>Maak de SQL Server-bron voor beschikbaarheid afhankelijk van het clienttoegangspunt.

    a. Klik in Failoverclusterbeheer op **Rollen**en klik vervolgens op uw beschikbaarheidsgroep.

    b. Klik op het tabblad **Resources** onder **Andere bronnen**met de rechtermuisknop op de groep beschikbaarheidsbronnen en klik vervolgens op **Eigenschappen**. 

    c. Voeg op het tabblad Afhankelijkheden de naam van de bron van het clienttoegangspunt (de listener) toe.

   ![IP-bron](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Klik op **OK**.

1. <a name="listname"></a>Maak de clienttoegangspuntbron afhankelijk van het IP-adres.

    a. Klik in Failoverclusterbeheer op **Rollen**en klik vervolgens op uw beschikbaarheidsgroep. 

    b. Klik op het tabblad **Resources** met de rechtermuisknop op de bron van het clienttoegangspunt onder **Servernaam**en klik vervolgens op **Eigenschappen**. 

   ![IP-bron](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Klik op het tabblad **Afhankelijkheden.** Controleer of het IP-adres een afhankelijkheid is. Als dit niet het is, stelt u een afhankelijkheid in van het IP-adres. Als er meerdere bronnen worden vermeld, controleert u of de IP-adressen of, niet EN, afhankelijkheden hebben. Klik op **OK**. 

   ![IP-bron](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >U valideren of de afhankelijkheden correct zijn geconfigureerd. Ga in Failoverclusterbeheer naar Rollen, klik met de rechtermuisknop op de beschikbaarheidsgroep, klik op **Meer acties**en klik vervolgens op **Afhankelijkheidsrapport weergeven**. Wanneer de afhankelijkheden correct zijn geconfigureerd, is de beschikbaarheidsgroep afhankelijk van de netwerknaam en is de netwerknaam afhankelijk van het IP-adres. 


1. <a name="setparam"></a>Stel de clusterparameters in PowerShell in.

   a. Kopieer het volgende PowerShell-script naar een van uw SQL Server-exemplaren. Werk de variabelen voor uw omgeving bij.

   - `$ListenerILBIP`is het IP-adres dat u hebt gemaakt op de Azure load balancer voor de listener van de beschikbaarheidsgroep.
    
   - `$ListenerProbePort`is de poort die u hebt geconfigureerd op de Azure load balancer voor de listener van de beschikbaarheidsgroep.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Stel de clusterparameters in door het PowerShell-script uit te voeren op een van de clusterknooppunten.  

   > [!NOTE]
   > Als uw SQL Server-exemplaren zich in afzonderlijke regio's bevinden, moet u het PowerShell-script twee keer uitvoeren. De eerste keer, `$ListenerILBIP` `$ListenerProbePort` gebruik maken van de en uit de eerste regio. De tweede keer, `$ListenerILBIP` `$ListenerProbePort` gebruik maken van de en uit de tweede regio. De naam van het clusternetwerk en de naam van de cluster-IP-bron zijn ook verschillend voor elke regio.

1. Breng de clusterrol van de beschikbaarheidsgroep online. Klik in **Failoverclusterbeheer** onder **Rollen**met de rechtermuisknop op de rol en selecteer **Rol starten**.

Herhaal indien nodig de bovenstaande stappen om de clusterparameters voor het IP-adres van het WSFC-cluster in te stellen.

1. De IP-adresnaam van het IP-adres van het WSFC-cluster op. Zoek **in Failoverclusterbeheer** onder **Clusterkernbronnen** **servernaam**.

1. Klik met de rechtermuisknop op **IP-adres**en selecteer **Eigenschappen**.

1. Kopieer de **naam** van het IP-adres. Het kan `Cluster IP Address`zijn. 

1. <a name="setwsfcparam"></a>Stel de clusterparameters in PowerShell in.
  
   a. Kopieer het volgende PowerShell-script naar een van uw SQL Server-exemplaren. Werk de variabelen voor uw omgeving bij.

   - `$ClusterCoreIP`is het IP-adres dat u hebt gemaakt op de Azure load balancer voor de WSFC-kernclusterbron. Het is anders dan het IP-adres voor de authenticiteitsgroep beschikbaarheid.

   - `$ClusterProbePort`is de poort die u hebt geconfigureerd op de Azure load balancer voor de WSFC-statussonde. Het is verschillend van de sonde voor de luisteraar van de beschikbaarheidsgroep.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. Stel de clusterparameters in door het PowerShell-script uit te voeren op een van de clusterknooppunten.  

>[!WARNING]
>De statusssenvan de listener-statussprobe-poort voor de beschikbaarheidsgroep moet afwijken van de poort van de clusterkern-IP-adresstatus. In deze voorbeelden is de listenerpoort 59999 en de clusterkern-IP-adresstatussprobepoort 58888. Beide poorten vereisen een inkomende firewallregel toestaan.

---
title: Infrastructuur maken voor een cluster op Azure VM's
description: In deze zelfstudie leert u hoe u de Azure VM-infrastructuur instelt om een Cluster Van Servicefabric uit te voeren.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 93a7e2507ab3a467ef83924479872694cae2dd5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614006"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Zelfstudie: Azure VM-infrastructuur maken om een ServiceFabric-cluster te hosten

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze zelfstudiereeks maakt u een zelfstandig cluster dat wordt gehost op Azure VM's en installeert u er een toepassing op.

Deze zelfstudie is deel één van een serie. In dit artikel genereert u de Azure VM-resources die nodig zijn om uw zelfstandige cluster van Service Fabric te hosten. In andere artikelen uit deze serie gaat u een zelfstandig Service Fabric-cluster installeren, een voorbeeldtoepassing in het cluster installeren en ten slotte het cluster opschonen.

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Een set AzureVM-exemplaren maken
> * De beveiligingsgroep wijzigen
> * Aanmelden bij een van de exemplaren
> * Het exemplaar voorbereiden voor Service Fabric

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien.  Als u nog geen account hebt, gaat u naar de [Azure-portal](https://portal.azure.com) om er een te maken.

## <a name="create-azure-virtual-machine-instances"></a>Azure Virtual Machine-exemplaren maken

1. Meld u aan bij de Azure-portal en selecteer **Virtuele machines** (geen virtuele machines (klassiek)).

   ![Azure-portalVM][az-console]

2. Selecteer de knop **Toevoegen,** waarmee het **formulier Een virtuele machine maken** wordt geopend.

3. Kies op het tabblad **Basisbeginselen** de gewenste abonnements- en resourcegroep (met een nieuwe brongroep wordt aanbevolen).

4. Wijzig het **afbeeldingstype** in **Windows Server 2016-datacenter**. 
 
5. Wijzig de **instantiegrootte** in **Standaard DS2 v2**. Stel een **beheerder gebruikersnaam** en **wachtwoord,** nota wat ze zijn.

6. Laat de **inkomende poortregels** voorlopig geblokkeerd; we zullen deze configureren in de volgende sectie.

7. Maak **op** het tabblad Netwerken een nieuw **virtueel netwerk** en neem nota van de naam ervan.

8. Stel vervolgens de **NIC-netwerkbeveiligingsgroep** in op **Geavanceerd**. Maak een nieuwe beveiligingsgroep, die de naam ervan opschrijft, en maak de volgende regels om TCP-verkeer van elke bron toe te staan:

   ![sf-inbound][sf-inbound]

   * Poort `3389`, voor RDP en ICMP (basisconnectiviteit).
   * Poorten, `19000-19003`voor Service Fabric.
   * Poorten, `19080-19081`voor Service Fabric.
   * Poort `8080`, voor webbrowseraanvragen.

   > [!TIP]
   > Om uw virtuele machines te verbinden in Service Fabric, moeten de VM's die uw infrastructuur hosten dezelfde referenties hebben.  Er zijn twee manieren om consistente referenties te hanteren: alle machines toevoegen aan hetzelfde domein of op elke VM hetzelfde beheerderswachtwoord instellen. Gelukkig kunnen alle virtuele machines op hetzelfde **virtuele netwerk** eenvoudig verbinding maken, dus we zullen er zeker van zijn dat al onze exemplaren op hetzelfde netwerk staan.

9. Voeg een andere regel toe. Stel de bron in **op ServiceTag** en stel de bronservicetag in op **VirtualNetwork.** Service Fabric vereist dat de volgende poorten open zijn voor communicatie binnen het cluster: 135.137-139.445.20001-20031,20606-20861.

   ![vnet-inbound][vnet-inbound]

10. De rest van de opties zijn aanvaardbaar in hun standaardstatus. Bekijk ze als je wilt, en start dan je virtuele machine.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Meer exemplaren maken voor uw Service Fabric-cluster

Start nog twee **virtuele machines,** zodat u dezelfde instellingen behoudt die in de vorige sectie zijn beschreven. Houd in het bijzonder dezelfde gebruikersnaam en wachtwoord van dezelfde beheerder aan. De beveiligingsgroep **Voor virtuele netwerken** en **NIC-netwerken** mag niet opnieuw worden gemaakt; selecteer de opties die u al hebt gemaakt in het vervolgkeuzemenu. Het kan enkele minuten duren voordat elk van uw exemplaren is geïmplementeerd.

## <a name="connect-to-your-instances"></a>Verbinding maken met uw instanties

1. Selecteer een van uw exemplaren in de sectie **Virtuele machine.**

2. Neem op het tabblad **Overzicht** nota van het privé-IP-adres. *private* Klik vervolgens op **Verbinden**.

3. Houd er op het **RDP-tabblad** rekening mee dat we het openbare IP-adres en poort 3389 gebruiken, dat we eerder specifiek hebben geopend. Download het RDP-bestand.
 
4. Open het RDP-bestand en voer desgevraagd de gebruikersnaam en het wachtwoord in die u in de VM-installatie hebt opgegeven.

5. Zodra u bent verbonden met een instantie, moet u valideren dat het externe register is uitgevoerd, SMB inschakelen en de vereiste poorten voor het Mkb- en externe register openen.

   Als u SMB wilt inschakelen, is dit de opdracht PowerShell:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. Dit is de PowerShell-opdracht voor het openen van de poorten in de firewall:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Herhaal dit proces voor uw andere instanties en wijs nogmaals op de privé-IP-adressen.

## <a name="verify-your-settings"></a>Uw instellingen verifiëren

1. Als u de basisconnectiviteit wilt valideren, maakt u verbinding met een van de VM's via RDP.

2. Open de **opdrachtprompt** vanuit die VM en gebruik vervolgens de ping-opdracht om verbinding te maken van de ene vm naar de andere en vervang het onderstaande IP-adres door een van de privé-IP-adressen die u eerder hebt opgemerkt (niet het IP van de VM waarmee u al bent verbonden).

   ```
   ping 172.31.20.163
   ```

   Als de uitvoer er gedurende vier pogingen uitziet zoals `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`, werkt de verbinding tussen de exemplaren.

3. Gebruik nu de volgende opdracht om te controleren of delen via SMB werkt:

   ```
   net use * \\172.31.20.163\c$
   ```

   De uitvoer moet gelijk zijn aan `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Nu zijn uw instanties goed voorbereid op Service Fabric.

## <a name="next-steps"></a>Volgende stappen

In deel één van de serie hebt u geleerd hoe u drie Azure VM-exemplaren starten en deze configureren voor de installatie van Service Fabric:

> [!div class="checklist"]
> * Een set Azure VM-exemplaren maken
> * De beveiligingsgroep wijzigen
> * Aanmelden bij een van de exemplaren
> * Het exemplaar voorbereiden voor Service Fabric

Ga naar deel twee van de reeks om Service Fabric te configureren in uw cluster.

> [!div class="nextstepaction"]
> [Service Fabric installeren](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png

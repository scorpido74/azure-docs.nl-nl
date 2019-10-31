---
title: Zelf studie voor het maken van de infra structuur voor een Service Fabric cluster op Azure Vm's-Azure Service Fabric | Microsoft Docs
description: In deze zelf studie leert u hoe u de Azure VM-infra structuur kunt instellen om een Service Fabric cluster uit te voeren.
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: c9dd9cf0f0fb6d20d6837b07ab46d376e379ca25
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177725"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Zelf studie: een Azure VM-infra structuur maken om een Service Fabric cluster te hosten

Zelfstandige Service Fabric-clusters bieden u de mogelijkheid om uw eigen omgeving te kiezen en een cluster te maken als onderdeel van de benadering "Elk besturingssysteem, elke cloud" die we in Service Fabric hanteren. In deze zelfstudie reeks maakt u een zelfstandig cluster dat wordt gehost op virtuele machines van Azure en installeert u er een toepassing op.

Deze zelfstudie is deel één van een serie. In dit artikel genereert u de Azure VM-resources die nodig zijn voor het hosten van uw zelfstandige cluster van Service Fabric. In andere artikelen uit deze serie gaat u een zelfstandig Service Fabric-cluster installeren, een voorbeeldtoepassing in het cluster installeren en ten slotte het cluster opschonen.

In deel 1 van de reeks leert u het volgende:

> [!div class="checklist"]
> * Een set AzureVM-instanties maken
> * De beveiligingsgroep wijzigen
> * Aanmelden bij een van de exemplaren
> * Het exemplaar voorbereiden voor Service Fabric

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-abonnement nodig om deze zelfstudie te voltooien.  Als u nog geen account hebt, gaat u naar de [Azure Portal](https://portal.azure.com) om er een te maken.

## <a name="create-azure-virtual-machine-instances"></a>Exemplaren van virtuele Azure-machines maken

1. Meld u aan bij de Azure Portal en selecteer **virtuele machines** (niet virtual machines (klassiek)).

   ![Azure Portal VM][az-console]

2. Selecteer de knop **toevoegen** om het formulier **virtuele machine maken** te openen.

3. Op het tabblad **basis beginselen** moet u het gewenste abonnement en de resource groep kiezen (met een nieuwe resource groep wordt aanbevolen).

4. Wijzig het type **installatie kopie** naar **Windows Server 2016 Data Center**. 
 
5. Wijzig de instantie **grootte** in **Standard DS2 v2**. Stel een beheerders **naam** en- **wacht woord**in, waarbij u weet wat ze zijn.

6. De **Binnenkomende poort regels** voor Taan geblokkeerd laten. deze worden geconfigureerd in de volgende sectie.

7. Op het tabblad **netwerken** maakt u een nieuwe **Virtual Network** en noteert u de naam.

8. Stel vervolgens de **NIC-netwerk beveiligings groep** in op **Geavanceerd**. Maak een nieuwe beveiligings groep, laat de naam ongewijzigd en maak de volgende regels om TCP-verkeer toe te staan van elke bron:

   ![SF-inkomend][sf-inbound]

   * Poort `3389`, voor RDP en ICMP (Basic-verbinding).
   * Poorten `19000-19003`, voor Service Fabric.
   * Poorten `19080-19081`, voor Service Fabric.
   * Poort `8080`, voor aanvragen van webbrowser.

   > [!TIP]
   > Om uw virtuele machines te verbinden in Service Fabric, moeten de VM's die uw infrastructuur hosten dezelfde referenties hebben.  Er zijn twee manieren om consistente referenties te hanteren: alle machines toevoegen aan hetzelfde domein of op elke VM hetzelfde beheerderswachtwoord instellen. Gelukkig staat Azure alle virtuele machines in hetzelfde **virtuele netwerk** toe om eenvoudig verbinding te maken. Daarom zullen we ervoor zorgen dat alle exemplaren op hetzelfde netwerk.

9. Voeg nog een regel toe. Stel de bron in op **service label** en stel de bron service label in op **VirtualNetwork**. Service Fabric moeten de volgende poorten zijn geopend voor communicatie binnen het cluster: 135137-139, 445, 20001-20031, 20606-20861.

   ![vnet-inkomend][vnet-inbound]

10. De overige opties zijn acceptabel in de standaard status. Bekijk deze indien gewenst en start vervolgens de virtuele machine.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>Meer exemplaren maken voor uw Service Fabric cluster

Start twee meer **virtual machines**en zorg ervoor dat u dezelfde instellingen behoudt als beschreven in de vorige sectie. Houd in het bijzonder dezelfde beheerders naam en hetzelfde wacht woord. De **beveiligings groep** voor **Virtual Network** en NIC-netwerk moet niet opnieuw worden gemaakt. Selecteer de items die u al hebt gemaakt in de vervolg keuzelijst. Het kan enkele minuten duren voordat elke instantie wordt geïmplementeerd.

## <a name="connect-to-your-instances"></a>Verbinding maken met uw instanties

1. Selecteer een van de exemplaren in de sectie **virtuele machine** .

2. Noteer het *privé* -IP-adres op het tabblad **overzicht** . Klik vervolgens op **verbinding maken**.

3. Op het tabblad **RDP** ziet u dat we het open bare IP-adres en de poort 3389 gebruiken, die we specifiek eerder hebben geopend. Down load het RDP-bestand.
 
4. Open het RDP-bestand en voer de gebruikers naam en het wacht woord in die u hebt opgegeven tijdens de installatie van de virtuele machine.

5. Zodra u verbinding hebt gemaakt met een exemplaar, moet u controleren of het externe REGI ster actief is en de vereiste poorten openen.

6. Dit is de PowerShell-opdracht voor het openen van de poorten in de firewall:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139
   ```

7. Herhaal dit proces voor uw andere instanties en pas de privé-IP-adressen opnieuw uit.

## <a name="verify-your-settings"></a>Uw instellingen verifiëren

1. Als u de basis verbinding wilt valideren, maakt u verbinding met een van de virtuele machines met behulp van RDP.

2. Open de **opdracht prompt** vanuit die VM en gebruik vervolgens de ping-opdracht om verbinding te maken tussen de ene VM en de andere, waarbij u het IP-adres vervangt door een van de privé-IP-adressen die u eerder hebt genoteerd (en niet de IP van de virtuele machine waarmee u al bent verbonden).

   ```
   ping 172.31.20.163
   ```

   Als de uitvoer er gedurende vier pogingen uitziet zoals `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128`, werkt de verbinding tussen de exemplaren.

   Uw instanties zijn nu voor bereid voor Service Fabric.

## <a name="next-steps"></a>Volgende stappen

In deel één van de serie hebt u geleerd hoe u drie Azure VM-exemplaren kunt starten en ze kunt ophalen die zijn geconfigureerd voor de Service Fabric-installatie:

> [!div class="checklist"]
> * Een set van Azure VM-exemplaren maken
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

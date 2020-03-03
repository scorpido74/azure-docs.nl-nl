---
title: 'Snelstartgids: een NAT-gateway maken-Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: In deze Quick start ziet u hoe u een NAT-gateway maakt met behulp van de Azure Portal
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 231c5f1bc6fd76f4f9e89d2d53639e9abe6cde0e
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228326"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Snelstartgids: een NAT-gateway maken met behulp van de Azure Portal

In deze Quick start ziet u hoe u de NAT-service van Azure Virtual Network gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor een virtuele machine in Azure. 

>[!NOTE] 
>Azure Virtual Network NAT is op dit moment beschikbaar als open bare preview en beschikbaar in een beperkt aantal [regio's](./nat-overview.md#region-availability). Deze preview is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en para meters

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt gebruiken, moet u de resource groep en het virtuele netwerk maken.

In deze sectie moet u de volgende para meters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Value                |
|-----------------------------|----------------------|
| **\<resource-group-name >**  | myResourceGroupNAT |
| **\<virtuele-netwerk naam >** | myVNet          |
| **\<regio-naam >**          | US - oost 2      |
| **\<IPv4-adres ruimte >**   | 192.168.0.0 \ 16          |
| **\<subnet naam >**          | mySubnet        |
| **\<subnet-adres bereik >** | 192.168.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Een virtuele machine maken voor het gebruik van de NAT-gateway

We gaan nu een VM maken voor het gebruik van de NAT-service. Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaar niveau om toegang te krijgen tot de virtuele machine. De NAT-service is stroom richting, en vervangt de standaard Internet bestemming in uw subnet. Het open bare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **Compute** > **Ubuntu Server 18,04 LTS**, of zoek naar **Ubuntu Server 18,04 LTS** in de zoek functie voor Marketplace.

2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   -  > **resource groep** **abonnement** : Selecteer **myResourceGroupNAT**.
   - **Exemplaar Details** > **naam van de virtuele machine**: Typ **myVM**.
   - **Exemplaar Details** > **regio** > Selecteer **VS Oost 2**.
   - **Administrator-account** > **verificatie type**: Selecteer **wacht woord**.
   - **Beheerders account** > Geef de **gebruikers naam**, het **wacht woord**en de **wachtwoord** gegevens op.
   - **Binnenkomende poort regels** > **open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Binnenkomende poort regels** > **binnenkomende poorten selecteren**: Selecteer **SSH (22)**
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Controleer op het tabblad **netwerken** of het volgende is geselecteerd:
   - **Virtueel netwerk**: **myVnet**
   - **Subnet**: **mySubnet**
   - **Open bare IP-** > Selecteer **nieuwe maken**.  Typ **myPublicIPVM** in het veld **naam** in het venster **openbaar IP-adres maken** en kies **standaard** voor de **SKU**.  Klik op **OK**.
   - **NIC-netwerk beveiligings groep**: Selecteer **basis**.
   - **Open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Selecteer binnenkomende poorten**: Bevestig **SSH** is geselecteerd.

4. Stel op het tabblad **beheer** , onder **bewaking**, **Diagnostische gegevens over opstarten** in op **uit**.

5. Selecteer **Controleren + maken**. 

6. Controleer de instellingen en klik op **maken**.

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

U kunt een of meer open bare IP-adres bronnen, open bare IP-voor voegsels of beide gebruiken. We voegen een open bare IP-resource, een openbaar IP-voor voegsel en een NAT-gateway bron toe.

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service kunt maken en configureren met behulp van de NAT-gateway resource:
  - Een open bare IP-adres groep en een openbaar IP-voor voegsel dat moet worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gateway resource.
  - Wijzig de time-out voor inactiviteit van de standaard waarde van 4 minuten in 10 minuten.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **netwerk** > **open bare IP-adres**, of zoek naar een **openbaar IP-adres** in de zoek opdracht voor Marketplace.

2. Voer in **openbaar IP-adres maken**de volgende gegevens in of Selecteer deze:

    | Instelling | Value |
    | ------- | ----- |
    | IP-versie | Selecteer **IPv4**.
    | SKU | selecteer **Standaard**.
    | Name | Voer **myPublicIP**in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **myResourceGroupNAT**. |
    | Location | Selecteer **VS - oost 2**.|

3. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voor voegsel maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **netwerk** > **openbaar IP-voor voegsel**of zoek naar **openbaar IP-voor voegsel** in de zoek opdracht voor Marketplace. 

2. Typ of Selecteer in het **voor voegsel een openbaar IP-adres maken**de volgende waarden op het tabblad **basis beginselen** :
   -  > **resource groep** **abonnement** : Selecteer **myResourceGroupNAT**>
   - **Exemplaar details** > **naam**: Typ **myPublicIPprefix**.
   - **Exemplaar details** > **regio**: Selecteer **VS Oost 2**.
   - **Exemplaar gegevens** > **grootte van voor voegsel**: Select **/31 (2 adressen)**

3. Laat de overige standaard instellingen ongewijzigd en selecteer **controleren + maken**.

4. Controleer de instellingen en selecteer vervolgens **Maken**.
   

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gateway resource maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource maken** > **netwerk** > NAT- **Gateway**, of zoek naar de **NAT-gateway** in de zoek functie voor Marketplace.

2. Typ of Selecteer in de **Gateway Create Network Address Translation (NAT)** de volgende waarden op het tabblad **basis beginselen** :
   -  > **resource groep** **abonnement** : Selecteer **myResourceGroupNAT**.
   - **Exemplaar details** > **naam**van de NAT-gateway: Typ **myNATgateway**.
   - **Exemplaar details** > **regio**: Selecteer **VS Oost 2**.
   - **Exemplaar gegevens** > **time-out voor inactiviteit (minuten)** : type **10**.
   - Selecteer het tabblad **openbaar IP-adres** of selecteer **volgende: openbaar IP-adres**.

3. Typ of selecteer de volgende waarden op het tabblad **openbaar IP** :
   - **Open bare IP-adressen**: Selecteer **myPublicIP**.
   - Voor **voegsels van open bare IP-adressen**: Selecteer **myPublicIPprefix**.
   - Selecteer het tabblad **subnet** of selecteer **volgende: subnet**.

4. Op het tabblad **subnet** typt of selecteert u de volgende waarden:
   - **Virtual Network**: selecteer **myResourceGroupNAT** > **myVnet**.
   - **Subnetnaam**: Schakel het selectie vakje in naast **mySubnet**.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="discover-the-ip-address-of-the-vm"></a>Het IP-adres van de virtuele machine detecteren

1. Selecteer op de linkerkant van de portal **resource groepen**.
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVM**.
4. Kopieer in het **overzicht**de waarde voor het **open bare IP-adres** en plak deze in Klad blok, zodat u deze kunt gebruiken om toegang te krijgen tot de virtuele machine.

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken om toegang te krijgen tot de virtuele machine.

## <a name="sign-in-to-vm"></a>Aanmelden bij de VM

Open een [Azure Cloud shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, de NAT-gateway en alle gerelateerde resources. Selecteer de **myResourceGroupNAT** van de resource groep die de NAT-gateway bevat en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een NAT-gateway en een virtuele machine gemaakt om deze te gebruiken. 

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de bron uitputting van de beschik bare SNAT-poorten.  Bron uitputting van de SNAT-poorten wordt opgelost door extra open bare IP-adres bronnen of open bare IP-prefix bronnen of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over de [NAT gateway-resource](./nat-gateway-resource.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure cli](./quickstart-create-nat-gateway-cli.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure Portal](./quickstart-create-nat-gateway-portal.md).
- [Feedback geven over de open bare preview](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]


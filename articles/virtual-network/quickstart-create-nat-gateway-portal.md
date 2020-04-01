---
title: 'Snelstart: een NAT-gateway maken - Azure-portal'
titlesuffix: Azure Virtual Network NAT
description: In deze quickstart wordt uitgelegd hoe u een NAT-gateway maakt met behulp van de Azure-portal
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
ms.openlocfilehash: 1ff13d8ef0ca4c6cf499c3245d3ef14370283075
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80066389"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Snelstart: een NAT-gateway maken met de Azure-portal

In deze snelstart ziet u hoe u de NAT-service van Azure Virtual Network gebruiken. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor een virtuele machine in Azure. 

Als u dat liever hebt, u deze stappen uitvoeren met de [Azure CLI,](quickstart-create-nat-gateway-cli.md) [Azure PowerShell](quickstart-create-nat-gateway-powershell.md)of een [ARM-sjabloon](quickstart-create-nat-gateway-powershell.md) implementeren in plaats van de portal.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

Voordat u een VM implementeert en uw NAT-gateway gebruiken, moeten we de brongroep en het virtuele netwerk maken.

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | myResourceGroupNAT |
| **\<>van de naam van het virtuele netwerk** | myVNet          |
| **\<regionaam>**          | VS - oost 2      |
| **\<IPv4-adresruimte>**   | 192.168.0.0\16          |
| **\<subnetnaam>**          | mySubnet        |
| **\<subnet-adresbereik>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Een VM maken om de NAT-gateway te gebruiken

We maken nu een VM om de NAT-service te gebruiken. Deze VM heeft een openbaar IP-adres om te gebruiken als openbaar IP op instantie, zodat u toegang hebt tot de VM. NAT-service is de stroomrichting bewust en vervangt de standaardinternetbestemming in uw subnet. Het openbare IP-adres van de VM wordt niet gebruikt voor uitgaande verbindingen.

1. Selecteer linksboven in de portal **de** > optie Een bron**Compute** > **Ubuntu Server 18.04 LTS**maken of zoek naar **Ubuntu Server 18.04 LTS** in de Marketplace-zoekopdracht.

2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Abonnementsbrongroep** > **Resource Group**: Selecteer **myResourceGroupNAT**.
   - **Instantiedetails** > **Virtuele machinenaam:** Typ **myVM**.
   - **Instantiedetails** > **regio** > selecteer Oost **US 2**.
   - **Verificatietype beheerdersaccount** > **Authentication type**: **Wachtwoord selecteren**.
   - **Beheerdersaccount** > Voer de **gebruikersnaam,** **wachtwoord**en **wachtwoordgegevens bevestigen in.**
   - **Inkomende poortregels** > **Openbare binnenkomende poorten:** Selecteer **Geselecteerde poorten toestaan**.
   - **Binnenkomende poortregels** > **Selecteer binnenkomende poorten:** **Selecteer SSH (22)**
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Controleer op het tabblad **Netwerken** of de volgende opties zijn geselecteerd:
   - **Virtueel netwerk**: **myVnet**
   - **Subnet**: **mySubnet**
   - **Openbaar IP->** **Nieuw maken selecteren**.  Typ **myPublicIPVM** in het veld **Naam** in het venster **Openbaar IP-adres maken** en kies **Standaard** voor de **SKU**.  Klik op **OK**.
   - **NIC-netwerkbeveiligingsgroep**: Selecteer **Basic**.
   - **Openbare binnenkomende poorten:** selecteer **Geselecteerde poorten toestaan**.
   - **Selecteer binnenkomende poorten:** bevestig dat **SSH** is geselecteerd.

4. Stel op het tabblad **Beheer** onder **Controleren** **de diagnose Opstarten** in op **Uit**.

5. Selecteer **Controleren + maken**. 

6. Controleer de instellingen en klik op **Maken**.

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

U een of meer openbare IP-adresbronnen, openbare IP-voorvoegsels of beide gebruiken. We voegen een openbare IP-bron, openbaar IP-voorvoegsel en een NAT-gatewaybron toe.

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service maken en configureren met behulp van de NAT-gatewaybron:
  - Een openbare IP-groep en een openbaar IP-voorvoegsel die moeten worden gebruikt voor uitgaande stromen die zijn vertaald door de NAT-gatewaybron.
  - Wijzig de niet-actieve time-out van de standaardinstelling van 4 minuten naar 10 minuten.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

1. Selecteer linksboven in de portal **de** > optie Een**openbaar IP-adres**voor**resourcenetwerken** > maken of zoek naar **openbaar IP-adres** in de Marketplace-zoekopdracht.

2. Voer in **Openbaar IP-adres maken**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | IP-versie | Selecteer **IPv4**.
    | SKU | Selecteer **Standaard**.
    | Name | Voer **myPublicIP in**. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **myResourceGroupNAT**. |
    | Locatie | Selecteer **VS - oost 2**.|

3. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voorvoegsel maken

1. Selecteer linksboven in de portal **het** > **openbare IP-voorvoegsel**van**resourcenetwerken** > maken of zoek naar **openbaar IP-voorvoegsel** in de Marketplace-zoekopdracht. 

2. Typ of selecteer in **Een openbaar IP-voorvoegsel**maken de volgende waarden op het tabblad **Basisbeginselen:**
   - **Subscription** > **Abonnementsbrongroep**: **MyResourceGroupNAT selecteren**>
   - **Naam van instantiedetails** > **Name**: Typ **myPublicIPprefix**.
   - **Instantiedetails** > **Regio**: Selecteer **Oost-VS 2**.
   - **Instantiedetails** > **Voorvoegselgrootte**: **Selecteer /31 (2 adressen)**

3. Laat de rest de standaardwaarden en selecteer **Controleren + maken**.

4. Controleer de instellingen en selecteer vervolgens **Maken**.
   

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gatewaybron maken

1. Selecteer linksboven in de portal de optie Een**NAT-gateway**voor > **bronnetwerken** > maken of zoek naar **NAT-gateway** in de Marketplace-zoekopdracht. **Create a resource**

2. Typ of selecteer in **NAT-gateway (Network Address translation)** de volgende waarden op het tabblad **Basisbeginselen:**
   - **Abonnementsbrongroep** > **Resource Group**: Selecteer **myResourceGroupNAT**.
   - **Instantiedetails** > **NAT-gatewaynaam:** Typ **myNATgateway**.
   - **Instantiedetails** > **Regio**: Selecteer **Oost-VS 2**.
   - **Instantiedetails** > **Idle time-out (minuten)**: Type **10**.
   - Selecteer het tabblad **Openbaar IP** of selecteer **Volgende: Openbaar IP**.

3. Typ of selecteer op het tabblad **Openbaar IP** de volgende waarden:
   - **Openbare IP-adressen**: Selecteer **myPublicIP**.
   - **Openbare IP-voorvoegsels**: Selecteer **myPublicIPprefix**.
   - Selecteer het tabblad **Subnet** of selecteer **Volgende: Subnet**.

4. Typ of selecteer op het tabblad **Subnet** de volgende waarden:
   - **Virtueel netwerk**: Selecteer **myResourceGroupNAT** > **myVnet**.
   - **Subnetnaam**: Schakel het vak naast **mySubnet**in.

5. Selecteer **Controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="discover-the-ip-address-of-the-vm"></a>Ontdek het IP-adres van de VM

1. Selecteer **Resourcegroepen**aan de linkerkant van de portal .
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVM**.
4. Kopieer in **Overzicht**de waarde **van het IP-adres van het openbaar** en plak deze in notitieblokken, zodat u deze gebruiken om toegang te krijgen tot de vm.

>[!IMPORTANT]
>Kopieer het openbare IP-adres en plak het in een notitieblok, zodat u het gebruiken om toegang te krijgen tot de VM.

## <a name="sign-in-to-vm"></a>Aanmelden bij VM

Open een [Azure Cloud Shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap naar SSH is opgehaald naar de virtuele machine.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de brongroep, NAT-gateway en alle gerelateerde bronnen wanneer dit niet meer nodig is. Selecteer de brongroep **myResourceGroupNAT** die de NAT-gateway bevat en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een NAT-gateway en een VM gemaakt om deze te gebruiken. 

Bekijk de statistieken in Azure Monitor om uw NAT-service te bekijken. Diagnose problemen zoals resource uitputting van de beschikbare SNAT-poorten.  Uitputting van resources van SNAT-poorten wordt aangepakt door extra openbare IP-adresbronnen of openbare IP-voorvoegselbronnen of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over [NAT-gatewaybron](./nat-gateway-resource.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snel starten voor het implementeren van [NAT-gatewaybron met Azure-portal.](./quickstart-create-nat-gateway-portal.md)
> [!div class="nextstepaction"]


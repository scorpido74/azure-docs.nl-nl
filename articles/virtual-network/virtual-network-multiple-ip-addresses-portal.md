---
title: Meerdere IP-adressen voor Azure virtual machines-Portal | Microsoft Docs
description: Meer informatie over het toewijzen van meerdere IP-adressen aan een virtuele machine met behulp van de Azure Portal | Resource Manager.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: 3a8cae97-3bed-430d-91b3-274696d91e34
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: anavin
ms.openlocfilehash: 66cbb843369dee103f102c9c743da544a833ccf1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356611"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Meerdere IP-adressen toewijzen aan virtuele machines met behulp van de Azure Portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> In dit artikel wordt uitgelegd hoe u een virtuele machine (VM) maakt via het Azure Resource Manager-implementatie model met behulp van de Azure Portal. Er kunnen geen meerdere IP-adressen worden toegewezen aan resources die zijn gemaakt via het klassieke implementatie model. Lees het artikel over [implementatie modellen begrijpen](../resource-manager-deployment-model.md) voor meer informatie over Azure-implementatie modellen.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Een virtuele machine met meerdere IP-adressen maken

Als u een virtuele machine met meerdere IP-adressen of een statisch privé-IP-adres wilt maken, moet u deze maken met behulp van Power shell of de Azure CLI. Als u wilt weten hoe, klikt u op de Power shell-of CLI-opties boven aan dit artikel. U kunt een virtuele machine maken met één dynamisch privé-IP-adres en (optioneel) één openbaar IP-adres. Gebruik de portal door de stappen in de [een Windows-VM maken](../virtual-machines/virtual-machines-windows-hero-tutorial.md) of [een Linux VM](../virtual-machines/linux/quick-create-portal.md) -artikel maken te volgen. Nadat u de virtuele machine hebt gemaakt, kunt u het IP-adres type van dynamisch naar statisch wijzigen en extra IP-adressen toevoegen met behulp van de portal door de stappen in de sectie [IP-adressen toevoegen aan een VM](#add) in dit artikel te volgen.

## <a name="add"></a>IP-adressen toevoegen aan een VM

U kunt persoonlijke en open bare IP-adressen toevoegen aan een Azure-netwerk interface door de volgende stappen uit te voeren. In de voor beelden in de volgende secties wordt ervan uitgegaan dat u al een virtuele machine hebt met de drie IP-configuraties die in het [scenario](#scenario)zijn beschreven, maar dit is niet vereist.

### <a name="coreadd"></a>Kern stappen

1. Ga naar de Azure Portal op https://portal.azure.com en meld u indien nodig aan.
2. Klik in de portal op **meer services** > type *virtuele machines* in het vak Filter en klik vervolgens op **virtuele machines**.
3. Klik in het deel venster **virtuele machines** op de virtuele machine waaraan u IP-adressen wilt toevoegen. Klik op **netwerk interfaces** in het deel venster virtuele machine dat wordt weer gegeven en selecteer vervolgens de netwerk interface waaraan u de IP-adressen wilt toevoegen. In het voor beeld dat in de volgende afbeelding wordt weer gegeven, is de NIC geselecteerd met de naam *myNIC* uit de virtuele machine met de naam *myVM* :

    ![Netwerkinterface](./media/virtual-network-multiple-ip-addresses-portal/figure1.png)

4. In het deel venster dat wordt weer gegeven voor de NIC die u hebt geselecteerd, klikt u op **IP-configuraties**.

Voer de stappen in een van de volgende secties uit, op basis van het type IP-adres dat u wilt toevoegen.

### <a name="add-a-private-ip-address"></a>**Een privé-IP-adres toevoegen**

Voer de volgende stappen uit om een nieuw privé-IP-adres toe te voegen:

1. Volg de stappen in de sectie [kern stappen](#coreadd) van dit artikel.
2. Klik op **Toevoegen**. Maak in het deel venster **IP-configuratie toevoegen** dat wordt weer gegeven een IP-configuratie met de naam *ipconfig-4* met *10.0.0.7* als een *statisch* privé-IP-adres en klik vervolgens op **OK**.

    > [!NOTE]
    > Bij het toevoegen van een statisch IP-adres moet u een niet-gebruikt, geldig adres opgeven in het subnet waarmee de NIC is verbonden. Als het door u geselecteerde adres niet beschikbaar is, wordt in de portal een X weer gegeven voor het IP-adres en moet u een andere naam selecteren.

3. Nadat u op OK hebt geklikt, wordt het deel venster gesloten en ziet u de nieuwe IP-configuratie die wordt weer gegeven. Klik op **OK** om het deel venster **IP-configuratie toevoegen** te sluiten.
4. U kunt klikken op **toevoegen** om extra IP-configuraties toe te voegen, of alle geopende Blades sluiten om het toevoegen van IP-adressen te volt ooien.
5. Voeg de privé-IP-adressen toe aan het VM-besturings systeem door de stappen in de sectie [IP-adressen toevoegen aan een VM-besturings systeem](#os-config) van dit artikel uit te voeren.

### <a name="add-a-public-ip-address"></a>Een openbaar IP-adres toevoegen

Een openbaar IP-adres wordt toegevoegd door een open bare IP-adres resource te koppelen aan een nieuwe IP-configuratie of een bestaande IP-configuratie.

> [!NOTE]
> Open bare IP-adressen hebben een nominale vergoeding. Lees de pagina met [prijzen voor IP-adressen](https://azure.microsoft.com/pricing/details/ip-addresses) voor meer informatie over de prijzen van IP-adressen. Er is een limiet voor het aantal open bare IP-adressen dat kan worden gebruikt in een abonnement. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-public-ip"></a>Een resource met een openbaar IP-adres maken

Een openbaar IP-adres is één instelling voor een open bare IP-adres resource. Als u een open bare IP-adres resource hebt die momenteel niet is gekoppeld aan een IP-configuratie die u wilt koppelen aan een IP-configuratie, slaat u de volgende stappen over en voert u de stappen in een van de onderstaande secties uit, zoals u dat nodig hebt. Als u geen beschik bare open bare IP-adres hebt, voert u de volgende stappen uit om een resource te maken:

1. Ga naar de Azure Portal op https://portal.azure.com en meld u indien nodig aan.
3. Klik in de portal op **een resource maken** > **netwerk** > **openbaar IP-adres**.
4. In het deel venster **openbaar IP-adres maken** dat wordt weer gegeven, voert u een **naam**in, selecteert u een **IP-adres toewijzings** type, een **abonnement**, een **resource groep**en een **locatie**. vervolgens klikt u op **maken**, zoals wordt weer gegeven in de volgende afbeelding:

    ![Een resource met een openbaar IP-adres maken](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Voer de stappen in een van de volgende secties uit om de open bare IP-adres bron te koppelen aan een IP-configuratie.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>De resource van het open bare IP-adres koppelen aan een nieuwe IP-configuratie

1. Volg de stappen in de sectie [kern stappen](#coreadd) van dit artikel.
2. Klik op **Toevoegen**. In het deel venster **IP-configuratie toevoegen** dat wordt weer gegeven, maakt u een IP-configuratie met de naam *ipconfig-4*. Schakel het **open bare IP-adres** in en selecteer een bestaande, beschik bare open bare IP-adres resource in het deel venster **openbaar IP-adres kiezen** dat wordt weer gegeven.

    Wanneer u de resource voor het open bare IP-adres hebt geselecteerd, klikt u op **OK** om het deel venster te sluiten. Als u geen bestaand openbaar IP-adres hebt, kunt u er een maken door de stappen in de sectie [een open bare IP-adres bron maken](#create-public-ip) in dit artikel te volt ooien. 

3. Controleer de nieuwe IP-configuratie. Hoewel een privé-IP-adres niet expliciet is toegewezen, is er automatisch een toegewezen aan de IP-configuratie, omdat alle IP-configuraties een privé-IP-adres moeten hebben.
4. U kunt klikken op **toevoegen** om extra IP-configuraties toe te voegen, of alle geopende Blades sluiten om het toevoegen van IP-adressen te volt ooien.
5. Voeg het privé-IP-adres toe aan het VM-besturings systeem door de stappen voor uw besturings systeem in de sectie [IP-adressen toevoegen aan een VM-besturings systeem](#os-config) van dit artikel uit te voeren. Voeg het open bare IP-adres niet toe aan het besturings systeem.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>De resource van het open bare IP-adres koppelen aan een bestaande IP-configuratie

1. Volg de stappen in de sectie [kern stappen](#coreadd) van dit artikel.
2. Klik op de IP-configuratie waaraan u de open bare IP-adres resource wilt toevoegen.
3. Klik in het deel venster IPConfig dat wordt weer gegeven op **IP-adres**.
4. Selecteer een openbaar IP-adres in het deel venster **openbaar IP-adres kiezen** dat wordt weer gegeven.
5. Klik op **Opslaan** om het deel venster te sluiten. Als u geen bestaand openbaar IP-adres hebt, kunt u er een maken door de stappen in de sectie [een open bare IP-adres bron maken](#create-public-ip) in dit artikel te volt ooien.
3. Controleer de nieuwe IP-configuratie.
4. U kunt klikken op **toevoegen** om extra IP-configuraties toe te voegen, of alle geopende Blades sluiten om het toevoegen van IP-adressen te volt ooien. Voeg het open bare IP-adres niet toe aan het besturings systeem.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

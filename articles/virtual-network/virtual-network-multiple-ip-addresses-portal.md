---
title: Meerdere IP-adressen voor virtuele Azure-machines - Portal | Microsoft Documenten
description: Meer informatie over het toewijzen van meerdere IP-adressen aan een virtuele machine via de Azure-portal | Resource Manager.
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
ms.openlocfilehash: a5bb9bb9c584aef8ac79b3c275d01d3c498da843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060608"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Meerdere IP-adressen toewijzen aan virtuele machines met behulp van de Azure-portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> In dit artikel wordt uitgelegd hoe u een virtuele machine (VM) maakt via het Azure Resource Manager-implementatiemodel met behulp van de Azure-portal. Meerdere IP-adressen kunnen niet worden toegewezen aan resources die zijn gemaakt via het klassieke implementatiemodel. Lees het artikel [Implementatiemodellen begrijpen](../resource-manager-deployment-model.md) voor meer informatie over Azure-implementatiemodellen.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Een virtuele machine met meerdere IP-adressen maken

Als u een VM wilt maken met meerdere IP-adressen of een statisch privé-IP-adres, moet u deze maken met PowerShell of azure cli. Klik boven aan dit artikel op de opties PowerShell of CLI. U een VM maken met één dynamisch privé-IP-adres en (optioneel) één openbaar IP-adres. Gebruik de portal door de stappen te volgen in de [artikelen Een Windows VM maken](../virtual-machines/virtual-machines-windows-hero-tutorial.md) of Een Linux VM [maken.](../virtual-machines/linux/quick-create-portal.md) Nadat u de VM hebt gemaakt, u het IP-adrestype wijzigen van dynamisch naar statisch en extra IP-adressen toevoegen met behulp van de portal door stappen te volgen in het [gedeelte IP-adressen toevoegen aan een VM-gedeelte](#add) van dit artikel.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-adressen toevoegen aan een virtuele machine

U privé- en openbare IP-adressen toevoegen aan een Azure-netwerkinterface door de volgende stappen uit te voeren. De voorbeelden in de volgende secties gaan ervan uit dat u al een VM hebt met de drie IP-configuraties die in het [scenario](#scenario)zijn beschreven, maar dit is niet vereist.

### <a name="core-steps"></a><a name="coreadd"></a>Kernstappen

1. Blader naar de https://portal.azure.com Azure-portal en meld u hier indien nodig aan.
2. Klik in de portal op **Meer services** > *virtuele machines* in het filtervak typen en klik vervolgens op Virtuele **machines**.
3. Klik in het deelvenster **Virtuele machines** op de VM waaraan u IP-adressen wilt toevoegen. Navigeer naar het tabblad **Netwerken.** Klik op **de netwerkinterface** op de pagina. Zoals te zien is op onderstaande foto: 


    ![Een openbaar IP-adres toevoegen aan een VM](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. Klik in het deelvenster **Netwerkinterface** op de **IP-configuraties**.

5. Klik in het deelvenster dat wordt weergegeven voor de geselecteerde NIC op **IP-configuraties**. Klik **op Toevoegen,** voer de stappen uit in een van de volgende secties, op basis van het type IP-adres dat u wilt toevoegen en klik vervolgens op **OK**. 

### <a name="add-a-private-ip-address"></a>**Een privé-IP-adres toevoegen**

Voer de volgende stappen uit om een nieuw privé-IP-adres toe te voegen:

1. Voer de stappen uit in het gedeelte [Kernstappen](#coreadd) van dit artikel.
2. Klik op**toevoegen**. Maak in het **deelvenster IP-configuratie toevoegen** dat wordt weergegeven een IP-configuratie met de naam *IPConfig-4* met *10.0.0.7* als *statisch* privé IP-adres en klik vervolgens op **OK**.

    > [!NOTE]
    > Wanneer u een statisch IP-adres toevoegt, moet u een ongebruikt, geldig adres opgeven op het subnet waarmee de NIC is verbonden. Als het geselecteerde adres niet beschikbaar is, geeft de portal een X voor het IP-adres weer en moet u een ander adres selecteren.

3. Zodra u op OK klikt, wordt het deelvenster gesloten en wordt de nieuwe IP-configuratie weergegeven. Klik op **OK** om het **deelvenster IP-configuratie toevoegen** te sluiten.
4. U op **Toevoegen** klikken om extra IP-configuraties toe te voegen of alle geopende bladen sluiten om het toevoegen van IP-adressen af te ronden.
5. Voeg de privé-IP-adressen toe aan het VM-besturingssysteem door de stappen in het [GEDEELTE IP-adressen toevoegen aan een VM-besturingssysteemgedeelte](#os-config) van dit artikel in te vullen.

### <a name="add-a-public-ip-address"></a>Een openbaar IP-adres toevoegen

Een openbaar IP-adres wordt toegevoegd door een openbare IP-adresbron te koppelen aan een nieuwe IP-configuratie of een bestaande IP-configuratie.

> [!NOTE]
> Openbare IP-adressen hebben een nominale vergoeding. Lees de pagina IP-adresprijzen voor meer informatie over de prijzen van [IP-adres.](https://azure.microsoft.com/pricing/details/ip-addresses) Er is een limiet aan het aantal openbare IP-adressen dat in een abonnement kan worden gebruikt. Lees voor meer informatie over de limieten het artikel [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Een bron voor een openbaar IP-adres maken

Een openbaar IP-adres is een instelling voor een openbare IP-adresbron. Als u een openbare IP-adresbron hebt die momenteel niet is gekoppeld aan een IP-configuratie die u wilt koppelen aan een IP-configuratie, slaat u de volgende stappen over en voert u de stappen uit in een van de volgende secties, zoals u dat wilt. Als u geen beschikbare openbare IP-adresbron hebt, voert u de volgende stappen uit om er een te maken:

1. Blader naar de https://portal.azure.com Azure-portal en meld u hier indien nodig aan.
3. Klik in de portal op Een openbaar > **IP-adres voor** >  **resourcenetwerken****maken**.
4. Voer in het deelvenster **Openbaar IP-adres maken** dat wordt weergegeven, voer een **naam**in, selecteer een **IP-adrestoewijzingstype,** een **abonnement,** een **resourcegroep**en een **locatie**en klik vervolgens op **Maken,** zoals in de volgende afbeelding wordt weergegeven:

    ![Een bron voor een openbaar IP-adres maken](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Voer de stappen uit in een van de volgende secties om de bron van het openbare IP-adres aan een IP-configuratie te koppelen.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>De bron voor openbare IP-adres koppelen aan een nieuwe IP-configuratie

1. Voer de stappen uit in het gedeelte [Kernstappen](#coreadd) van dit artikel.
2. Klik op**toevoegen**. Maak in het **deelvenster IP-configuratie toevoegen** dat wordt weergegeven een IP-configuratie met de naam *IPConfig-4*. Schakel het **openbare IP-adres** in en selecteer een bestaande, beschikbare openbare IP-adresbron in het deelvenster **Openbaar IP-adres kiezen** dat wordt weergegeven.

    Nadat u de bron voor openbare IP-adres hebt geselecteerd, klikt u op **OK** en wordt het deelvenster gesloten. Als u geen bestaand openbaar IP-adres hebt, u er een maken door de stappen in het gedeelte [Een openbaar IP-adres van](#create-public-ip) dit artikel maken in te vullen. 

3. Bekijk de nieuwe IP-configuratie. Hoewel een privé-IP-adres niet expliciet is toegewezen, werd er automatisch een toegewezen aan de IP-configuratie, omdat alle IP-configuraties een privé-IP-adres moeten hebben.
4. U op **Toevoegen** klikken om extra IP-configuraties toe te voegen of alle geopende bladen sluiten om het toevoegen van IP-adressen af te ronden.
5. Voeg het privé-IP-adres toe aan het VM-besturingssysteem door de stappen voor uw besturingssysteem in het [gedeelte IP-adressen toevoegen aan een vm-besturingssysteemgedeelte](#os-config) van dit artikel in te vullen. Voeg het openbare IP-adres niet toe aan het besturingssysteem.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>De bron voor openbare IP-adres koppelen aan een bestaande IP-configuratie

1. Voer de stappen uit in het gedeelte [Kernstappen](#coreadd) van dit artikel.
2. Klik op de IP-configuratie waaraan u de bron voor openbare IP-adres wilt toevoegen.
3. Klik in het deelvenster IPConfig dat wordt weergegeven op **IP-adres**.
4. Selecteer in het deelvenster **Openbaar IP-adres kiezen** dat wordt weergegeven een openbaar IP-adres.
5. Klik **op Opslaan** en de deelvensters sluiten. Als u geen bestaand openbaar IP-adres hebt, u er een maken door de stappen in het gedeelte [Een openbaar IP-adres van](#create-public-ip) dit artikel maken in te vullen.
3. Bekijk de nieuwe IP-configuratie.
4. U op **Toevoegen** klikken om extra IP-configuraties toe te voegen of alle geopende bladen sluiten om het toevoegen van IP-adressen af te ronden. Voeg het openbare IP-adres niet toe aan het besturingssysteem.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

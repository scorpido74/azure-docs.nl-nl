---
title: Een gezonde Windows-vm maken met de Azure-portal
description: Een Windows-vm maken in een beschikbaarheidszone met de Azure-portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: c6f843693ccfa16f9b31027ba370242e0462b138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033893"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Een virtuele Windows-machine maken in een beschikbaarheidszone met de Azure-portal

In dit artikel wordt de Azure-portal gebruikt om een virtuele machine te maken in een Azure-beschikbaarheidszone. Een [beschikbaarheidszone](../../availability-zones/az-overview.md) is een fysiek afgescheiden zone in een Azure-regio. Gebruik beschikbaarheidszones om uw apps en gegevens te beschermen tegen het onwaarschijnlijke risico van een storing of het verloren gaan van een heel datacenter.

Als u een beschikbaarheidszone wilt gebruiken, maakt u een virtuele machine in een [ondersteunde Azure-regio](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure 

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-virtual-machine"></a>Virtuele machine maken

1. Klik **op Een resource maken** in de linkerbovenhoek van de Azure-portal.

2. Selecteer **Compute** en vervolgens **Windows Server 2016 Datacenter**. 

3. Geef de informatie van de virtuele machine op. De gebruikersnaam en het wachtwoord die u hier opgeeft, worden gebruikt voor aanmelding bij de virtuele machine. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](faq.md#what-are-the-password-requirements-when-creating-a-vm). Kies een locatie zoals Oost US 2 die beschikbaarheidszones ondersteunt. Na het voltooien klikt u op **OK**.

    ![Voer basisinformatie over uw virtuele machine in op de portalblade](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Kies een grootte voor de virtuele machine. Selecteer een aanbevolen grootte of filter op basis van functies. Controleer of de grootte beschikbaar is in de zone die u wilt gebruiken.

    ![Een VM-grootte selecteren](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. Selecteer **onder Instellingen** > **Hoge beschikbaarheid**een van de genummerde zones in de vervolgkeuzelijst **Beschikbaarheidszone,** houd de resterende standaardwaarden en klik op **OK**.

    ![Een beschikbaarheidszone selecteren](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Klik op de overzichtspagina op **Maken** om de implementatie van de virtuele machine te starten.

7. De VM wordt aan het dashboard van de Azure Portal vastgemaakt. Zodra de implementatie is voltooid, wordt de samenvatting van de VM automatisch geopend.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Zone voor beheerde schijf en IP-adres bevestigen

Wanneer de VM wordt geïmplementeerd in een beschikbaarheidszone, wordt een beheerde schijf voor de vm gemaakt in dezelfde beschikbaarheidszone. Standaard wordt er ook een openbaar IP-adres gemaakt in die zone.

U de zone-instellingen voor deze bronnen in de portal bevestigen.  

1. Klik **op Resourcegroepen** en vervolgens op de naam van de resourcegroep voor de vm, zoals *myResourceGroup*.

2. Klik op de naam van de bron Schijf. De **overzichtspagina** bevat details over de locatie- en beschikbaarheidszone van de resource.

    ![Beschikbaarheidszone voor beheerde schijf](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klik op de naam van de bron openbaar IP-adres. De **overzichtspagina** bevat details over de locatie- en beschikbaarheidszone van de resource.

    ![Beschikbaarheidszone voor IP-adres](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een VM maakt in een beschikbaarheidszone. Meer informatie over [de beschikbaarheid](availability.md) voor Azure VM's.

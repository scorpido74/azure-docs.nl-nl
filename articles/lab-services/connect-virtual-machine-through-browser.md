---
title: Verbinding maken met uw virtuele machines via een browser-Azure | Microsoft Docs
description: Meer informatie over hoe u via een browser verbinding kunt maken met uw virtuele machines.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 22d7afa889994068c3f906d0b9adbc1a74bc4839
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642589"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Verbinding maken met uw virtuele machines via een browser 

DevTest Labs kan worden geïntegreerd met [Azure Bastion](https://docs.microsoft.com/azure/bastion/), waarmee u via een browser verbinding kunt maken met uw virtuele machines. Zie [browser verbinding inschakelen op Lab virtual machines](enable-browser-connection-lab-virtual-machines.md)voor meer informatie over het inschakelen van deze functie in DevTest Labs.

Zodra de *browser verbinding maken* is ingeschakeld, hebben Lab-gebruikers via een browser toegang tot virtuele machines.  

> [!NOTE]
> Het inschakelen van een browser verbinding op virtuele lab-machines is in preview.

## <a name="create-a-lab-virtual-machine"></a>Een virtuele machine voor het lab maken

U moet eerst de virtuele machine van het lab maken binnen een VNet waarop Bastion is geconfigureerd. U kunt een VNet selecteren tijdens het maken van de virtuele machine door naar het tabblad **Geavanceerde instellingen** te gaan.

![Virtuele machine maken](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Virtuele machine in een browser starten

Zodra de virtuele machine is gemaakt, kunt u deze in een browser openen door te klikken op de knop *verbinding maken* en uw gebruikers naam en wacht woord voor de machine in te voeren.  

![Starten in een browser](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Volgende stappen

[Een VM toevoegen aan een lab in Azure DevTest Labs](devtest-lab-add-vm.md)
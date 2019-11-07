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
ms.openlocfilehash: 1bb5d979ff7c9fe6e2afec4c7ebd81649b4488e3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581234"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Verbinding maken met uw virtuele machines via een browser 

DevTest Labs kan worden geïntegreerd met [Azure Bastion](https://docs.microsoft.com/azure/bastion/), waarmee u via een browser verbinding kunt maken met uw virtuele machines. Zie [browser verbinding inschakelen op Lab virtual machines](enable-browser-connection-lab-virtual-machines.md)voor meer informatie over het inschakelen van deze functie in DevTest Labs.

Zodra de *browser verbinding maken* is ingeschakeld, hebben Lab-gebruikers via een browser toegang tot virtuele machines.  


## <a name="create-a-lab-virtual-machine"></a>Een virtuele machine voor het lab maken

U moet eerst de virtuele machine van het lab maken binnen een VNet waarop Bastion is geconfigureerd. U kunt een VNet selecteren tijdens het maken van de virtuele machine door naar het tabblad **Geavanceerde instellingen** te gaan.

![Virtuele machine maken](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Virtuele machine in een browser starten

Zodra de virtuele machine is gemaakt, kunt u deze in een browser openen door te klikken op de knop *verbinding maken* en uw gebruikers naam en wacht woord voor de machine in te voeren.  

![Starten in een browser](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Volgende stappen

[Een VM toevoegen aan een lab in Azure DevTest Labs](devtest-lab-add-vm.md)

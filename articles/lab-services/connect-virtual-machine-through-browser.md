---
title: Verbinding maken met uw virtuele machines via een browser - Azure | Microsoft Documenten
description: Meer informatie over hoe u verbinding maken met uw virtuele machines via een browser.
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
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974280"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Verbinding maken met uw virtuele machines via een browser 

DevTest Labs integreert met [Azure Bastion,](https://docs.microsoft.com/azure/bastion/)waarmee u verbinding maken met uw virtuele machines via een browser. Zie Browserverbinding inschakelen op virtuele machines in [het lab](enable-browser-connection-lab-virtual-machines.md)voor informatie over het inschakelen van deze functie in DevTest Labs.

Zodra de *browserverbinding* is ingeschakeld, hebben labgebruikers toegang tot virtuele machines via een browser.  

## <a name="create-a-lab-virtual-machine"></a>Een virtuele labmachine maken

U moet eerst de virtuele machine van het lab maken binnen een virtueel netwerk waarop Bastion is geconfigureerd. Selecteer het tweede **subnet dat** u hebt gemaakt, niet het AzureBastionSubnet. U een virtueel netwerk selecteren tijdens het maken van virtuele machines door naar het tabblad **Geavanceerde instellingen** te gaan.

![Virtuele machine maken](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Virtuele machine starten in een browser

Zodra de virtuele machine is gemaakt, u deze starten in een browser door op de knop *Browserverbinding te* klikken en uw gebruikersnaam en wachtwoord voor de machine in te voeren.  

![Starten in een browser](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Volgende stappen

[Een VM toevoegen aan een lab in Azure DevTest Labs](devtest-lab-add-vm.md)

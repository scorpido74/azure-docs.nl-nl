---
title: Geneste virtualisatie inschakelen op een sjabloon-VM in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het maken van een sjabloon-vm met meerdere VM's erin.  Met andere woorden, schakel geneste virtualisatie in op een sjabloon-VM in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502008"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Geneste virtualisatie inschakelen op een virtuele sjabloonmachine in Azure Lab Services

Op dit moment u met Azure Lab Services één virtuele sjabloonmachine in een lab instellen en één exemplaar beschikbaar maken voor elk van uw gebruikers. Als u een professor bent die netwerk-, beveiligings- of IT-klassen onderwijst, moet u mogelijk elk van uw studenten een omgeving bieden waarin meerdere virtuele machines met elkaar kunnen praten via een netwerk.

Geneste virtualisatie stelt u in staat om een multi-VM-omgeving te maken in de virtuele machine van een lab. Als u de sjabloon publiceert, krijgt elke gebruiker in het lab een virtuele machine die is ingesteld met meerdere VM's erin.  In dit artikel wordt ingaan op het instellen van geneste virtualisatie op een sjabloonmachine in Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Wat is geneste virtualisatie?

Geneste virtualisatie stelt u in staat om virtuele machines te maken binnen een virtuele machine. Geneste virtualisatie gebeurt via Hyper-V en is alleen beschikbaar op Windows VM's.

Zie de volgende artikelen voor meer informatie over geneste virtualisatie:

- [Geneste virtualisatie in Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Geneste virtualisatie inschakelen in een Azure VM](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Overwegingen

Voordat u een lab met geneste virtualisatie, hier zijn een paar dingen om rekening mee te houden.

- Selecteer bij het maken van een nieuw lab de grootte **Van Medium (Geneste virtualisatie)** of **Grote (Geneste virtualisatie)** voor de grootte van de virtuele machine. Deze virtuele machineformaten ondersteunen geneste virtualisatie.
- Kies een grootte die goede prestaties biedt voor zowel de virtuele machines van de host als de client.  Vergeet niet dat bij het gebruik van virtualisatie de grootte die u kiest, voldoende moet zijn voor niet slechts één machine, maar de host en alle clientmachines die gelijktijdig moeten worden uitgevoerd.
- Virtuele clientmachines hebben geen toegang tot Azure-bronnen, zoals DNS-servers in het virtuele Azure-netwerk.
- Host virtuele machine vereist setup om voor de client machine om internetverbinding te hebben.
- Client virtuele machines zijn gelicentieerd als onafhankelijke machines. Zie [Microsoft Licensing](https://www.microsoft.com/licensing/default) voor informatie over licenties voor Microsoft-besturingssystemen en -producten. Controleer licentieovereenkomsten voor andere software die wordt gebruikt voordat u de sjabloonmachine instelt.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Geneste virtualisatie inschakelen in een sjabloon-VM

In dit artikel wordt ervan uitgegaan dat u een labaccount en lab hebt gemaakt.  Zie Zelfstudie voor het instellen van [een Lab-account voor](tutorial-setup-lab-account.md)meer informatie over het maken van een nieuw labaccount. Zie [een lesprogramma voor een klaslokaalvoor de les](tutorial-setup-classroom-lab.md)in het laboratorium instellen voor meer informatie over het maken van een lab.

>[!IMPORTANT]
>Selecteer **Groot (geneste virtualisatie)** of **Medium (geneste virtualisatie)** voor de grootte van de virtuele machine bij het maken van het lab.  Geneste virtualisatie werkt anders niet.  

Zie [een klassjabloon maken en beheren](how-to-create-manage-template.md)als u verbinding wilt maken met de sjabloonmachine.

### <a name="using-script-to-enable-nested-virtualization"></a>Script gebruiken om geneste virtualisatie in te schakelen

Zie Geneste virtualisatie inschakelen [op een sjabloon virtuele machine in Azure Lab Services inschakelen met behulp van een script](how-to-enable-nested-virtualization-template-vm-using-script.md)als u de geautomatiseerde installatie voor geneste virtualisatie wilt gebruiken met Windows Server 2016 of Windows Server 2019. U gebruikt scripts van [Lab Services Hyper-V scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) om de Hyper-V rol te installeren.  De scripts zullen ook het opzetten van netwerken, zodat de Hyper-V virtuele machines kunnen toegang tot internet hebben.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Windows-hulpprogramma's gebruiken om geneste virtualisatie in te schakelen

De installatie geneste virtualisatie voor Windows Server 2016 of Windows Server 2019 met Windows-rollen en beheerhulpprogramma's, zie [Geneste virtualisatie inschakelen op een virtuele sjabloonmachine in Azure Lab Services.](how-to-enable-nested-virtualization-template-vm-ui.md)  Instructies zullen ook betrekking hebben op het opzetten van netwerken, zodat de Hyper-V virtuele machines kunnen toegang tot internet hebben.

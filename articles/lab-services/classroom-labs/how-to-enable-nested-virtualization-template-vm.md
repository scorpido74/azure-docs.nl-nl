---
title: Geneste virtualisatie inschakelen op een sjabloon-VM in Azure Lab Services | Microsoft Docs
description: Meer informatie over het inschakelen van geneste virtualisatie op een sjabloon-VM in Azure Lab Services.
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
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981993"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Geneste virtualisatie inschakelen op een virtuele machine van een sjabloon in Azure Lab Services
In dit artikel wordt beschreven hoe u een geneste virtualisatie instelt op een virtuele machine van een sjabloon in Azure Lab Services. Geneste virtualisatie wordt gebruikt in Azure Lab Services als voor elke student in een klasse meerdere machines zijn vereist.
 
## <a name="considerations"></a>Overwegingen
Voordat u een Lab met geneste virtualisatie instelt, zijn hier enkele dingen die u in overweging moet nemen.

- Bij het maken van een nieuw Lab selecteert u **Gemiddeld (geneste virtualisatie)** of **grote** grootten voor de grootte van de virtuele machine. Deze virtuele-machine grootten ondersteunen geneste virtualisatie. 
- Kies een grootte die goede prestaties biedt voor zowel de host als de virtuele machine van de client.  Houd er rekening mee dat als u virtualisatie gebruikt, de grootte die u kiest, niet alleen op één machine kan worden uitgevoerd, maar de host, evenals alle client computers die gelijktijdig moeten worden gestart.
- Virtuele machines van de client hebben geen toegang tot Azure-resources, zoals DNS-servers in het virtuele Azure-netwerk.
- Voor de virtuele machine van de host is installatie vereist zodat de client computer verbinding met Internet kan hebben. 
- Virtuele machines van de client worden als onafhankelijke computers in licentie gegeven. Zie [micro soft-licentie verlening](https://www.microsoft.com/licensing/default) voor informatie over licenties voor micro soft-besturings systemen en-producten. Controleer de licentie overeenkomsten op alle andere software die wordt gebruikt voordat u de sjabloon machine instelt.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Geneste virtualisatie inschakelen op een sjabloon-VM
De stappen in deze sectie zijn gericht op het instellen van geneste virtualisatie voor Windows Server 2016 of Windows Server 2019. U gebruikt een script voor het instellen van de sjabloon computer met Hyper-V. Voor een geautomatiseerde oplossing raadpleegt u scripts op [Lab-Services Hyper-V-scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV). In de volgende stappen wordt uitgelegd hoe u het script gebruikt.

1. Als u Internet Explorer gebruikt, moet u mogelijk `https://github.com` toevoegen aan de lijst met vertrouwde websites. 
    1. Open Internet Explorer.
    1. Selecteer het tandwiel pictogram en kies **Internet opties**.  
    1. Wanneer het dialoog venster **Internet opties** wordt weer gegeven, selecteert u **beveiliging**, selecteer **vertrouwde sites**en klikt u op de knop **sites** .
    1. Wanneer het dialoog venster **vertrouwde sites** wordt weer gegeven, voegt u `https://github.com` toe aan de lijst met vertrouwde websites en selecteert u **sluiten**.

        ![Vertrouwde sites](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  Down load de Git-opslagplaats bestanden zoals beschreven in de volgende stappen.  De Git-opslag plaats kan ook worden gekloond van [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git). 
    1. Ga naar [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Klik op de knop ***klonen of downloaden** .
    1. Klik op **down load zip**.
    1. Het ZIP-bestand uitpakken
1. Start **Power shell** in de **beheerders** modus.
1. Navigeer in het Power shell-venster naar de map met het gedownloade script. Als u vanuit de bovenste map van de opslagplaats bestanden navigeert, bevindt het script zich op `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Mogelijk moet u het uitvoerings beleid wijzigen om het script uit te voeren. Voer de volgende opdracht uit:
    
    ```powershell
    Set-ExecutionPolicy bypass -force 
    ```
1. Voer het script uit:
    
    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Het script vereist mogelijk dat de computer opnieuw wordt opgestart. Volg de instructies in het script en voer het script opnieuw uit totdat het **script is voltooid** wordt weer gegeven in de uitvoer.
1. Vergeet niet om het uitvoerings beleid opnieuw in te stellen. Voer de volgende opdracht uit: 

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusie
Uw sjabloon machine is nu gereed voor het maken van virtuele Hyper-V-machines. Zie [een virtuele machine maken in Hyper-v](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) voor instructies over het maken van virtuele Hyper-v-machines. Zie ook [micro soft Evaluation Center](https://www.microsoft.com/evalcenter/) om beschik bare besturings systemen en software te bekijken.  

## <a name="next-steps"></a>Volgende stappen 
Zie de volgende artikelen:

- [Geneste virtualisatie inschakelen in een Azure VM](../../virtual-machines/windows/nested-virtualization.md) 
- [De Hyper-V-functie installeren op Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Lab-Services, Hyper-V-scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)

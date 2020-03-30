---
title: Geneste virtualisatie inschakelen op een sjabloon-VM in Azure Lab Services (Script) | Microsoft Documenten
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
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503034"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Geneste virtualisatie inschakelen op een virtuele sjabloonmachine in Azure Lab Services met behulp van een script

Geneste virtualisatie stelt u in staat om een multi-VM-omgeving te maken in de virtuele machine van een lab. Als u de sjabloon publiceert, krijgt elke gebruiker in het lab een virtuele machine die is ingesteld met meerdere VM's erin.  Zie [Geneste virtualisatie inschakelen op een virtuele sjabloon in Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md)voor meer informatie over geneste virtualisatie en Azure Lab Services.

De stappen in dit artikel richten zich op het instellen van geneste virtualisatie voor Windows Server 2016 of Windows Server 2019. U gebruikt een script om een sjabloonmachine in te stellen met Hyper-V.  De volgende stappen zullen u begeleiden bij het gebruik van de [Lab Services Hyper-V scripts.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)

>[!IMPORTANT]
>Selecteer **Groot (geneste virtualisatie)** of **Medium (geneste virtualisatie)** voor de grootte van de virtuele machine bij het maken van het lab.  Geneste virtualisatie werkt anders niet.  

## <a name="run-script"></a>Script uitvoeren

1. Als u Internet Explorer gebruikt, moet `https://github.com` u mogelijk toevoegen aan de lijst met vertrouwde sites.
    1. Open Internet Explorer.
    1. Selecteer het tandwielpictogram en kies **internetopties**.  
    1. Wanneer het dialoogvenster **Internetopties** wordt weergegeven, selecteert u **Beveiliging**, **selecteer Vertrouwde sites**, klik op knop **Sites.**
    1. Wanneer het dialoogvenster **Vertrouwde** `https://github.com` sites wordt weergegeven, voegt u toe aan de lijst met vertrouwde websites en selecteert u **Sluiten**.

        ![Vertrouwde sites](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Download de Git-repository-bestanden zoals beschreven in de volgende stappen.
    1. Ga [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)naar.
    1. Klik op de knop **Klonen of Downloaden.**
    1. Klik **op ZIP downloaden**.
    1. Het ZIP-bestand extraheren

    >[!TIP]
    >Je de Git repository ook klonen op [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git).

1. **Start PowerShell** in **de beheerdersmodus.**
1. Navigeer in het PowerShell-venster naar de map met het gedownloade script. Als u vanuit de bovenste map van de archiefbestanden navigeert, bevindt het script zich op `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Mogelijk moet u het uitvoeringsbeleid wijzigen om het script uit te voeren. Voer de volgende opdracht uit:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Voer het volgende script uit:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Het script kan vereisen dat de machine opnieuw wordt opgestart. Volg de instructies van het script en voer het script opnieuw uit totdat **script voltooid** is in de uitvoer.
1. Vergeet niet om het uitvoeringsbeleid te resetten. Voer de volgende opdracht uit:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusie

Nu is uw sjabloonmachine klaar om virtuele Hyper-V-machines te maken. Zie [Een virtuele machine maken in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) voor instructies over het maken van virtuele hyperv-virtuele machines. Zie ook [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) om beschikbare besturingssystemen en software te bekijken.  

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het opzetten van een lab.

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quotum instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een schema instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Koppelingen naar e-mailregistratie naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)
---
title: Een defecte Azure-vm oplossen met geneste virtualisatie in Azure | Microsoft Documenten
description: Een probleem Azure VM oplossen door geneste virtualisatie in Azure te gebruiken
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: e1acfc3216ccfaeac035f1ff31e82c7b67c17daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119615"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Een defecte Azure-vm oplossen met geneste virtualisatie in Azure

In dit artikel ziet u hoe u een geneste virtualisatieomgeving maakt in Microsoft Azure, zodat u de schijf van de defecte VM monteren op de Hyper-V-host (Rescue VM) voor probleemoplossingsdoeleinden.

## <a name="prerequisites"></a>Vereisten

Om de defecte VM te monteren, moet de Rescue VM hetzelfde type opslagaccount (standaard of Premium) gebruiken als de defecte VM.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Stap 1: Een Reddings-VM maken en hyper-v-rol installeren

1.  Maak een nieuwe Rescue VM:

    -  Besturingssysteem: Windows Server 2016 Datacenter

    -  Grootte: elke V3-serie met ten minste twee kernen die geneste virtualisatie ondersteunen. Zie [Introductie van de nieuwe Dv3- en Ev3 VM-formaten](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)voor meer informatie.

    -  Dezelfde locatie, opslagaccount en resourcegroep als de defecte VM.

    -  Selecteer hetzelfde opslagtype als de defecte VM (Standard of Premium).

2.  Nadat de VM voor redding is gemaakt, extern bureaublad naar de Rescue VM.

3.  Selecteer in Serverbeheer De optie**Rollen en onderdelen** **beheren** > .

4.  Selecteer in de sectie **Installatietype** de optie **Op rollen gebaseerde of op functies gebaseerde installatie**.

5.  Controleer in de sectie **Doelserver selecteren** of de vm voor de redding is geselecteerd.

6.  Selecteer de **Functie Toevoegen van Hyper V-rollen** > **Add Features**.

7.  Selecteer **Volgende** in de sectie **Onderdelen.**

8.  Als er een virtuele switch beschikbaar is, selecteert u deze. Selecteer **anders Volgende**.

9.  Selecteer **Volgende** in **Next** de sectie Migratie

10. Selecteer **Volgende**in de sectie **Standaardopslag** .

11. Schakel het selectievakje in om de server automatisch opnieuw op te starten indien nodig.

12. Selecteer **Installeren**.

13. Laat de server de Hyper-V-rol installeren. Dit duurt een paar minuten en de server wordt automatisch opnieuw opgestart.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Stap 2: Maak de defecte VM op de Hyper-V-server van de Rescue VM

1.  [Maak een momentopnameschijf](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) voor de OS-schijf van de VM die probleem heeft en voeg de momentopnameschijf toe aan de afbeeldingsvm.

2.  Extern bureaublad naar de Rescue VM.

3.  Schijfbeheer openen (diskmgmt.msc). Controleer of de schijf van de defecte vm is ingesteld op **Offline.**

4.  Open Hyper-V Manager: Selecteer in **Serverbeheer**de **functie Hyper-V**. Klik met de rechtermuisknop op de server en selecteer vervolgens **Hyper-V Manager**.

5.  Klik in De Hyper-V Manager met de rechtermuisknop op de reddingsvm en selecteer **Vervolgens Nieuwe** > **virtuele machine** > **volgende**.

6.  Typ een naam voor de virtuele machine en selecteer **Volgende**.

7.  Selecteer **Generatie 1**.

8.  Stel het opstartgeheugen in op 1024 MB of meer.

9. Selecteer indien van toepassing de Hyper-V-netwerkswitch die is gemaakt. Anders naar de volgende pagina gaan.

10. Selecteer **Later een virtuele harde schijf koppelen**.

    ![de afbeelding over de optie Later een virtuele harde schijf koppelen](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Selecteer **Voltooien** wanneer de vm wordt gemaakt.

12. Klik met de rechtermuisknop op de vm die u hebt gemaakt en selecteer **Instellingen**.

13. Selecteer **IDE-controller 0,** selecteer **Harde schijf**en klik op **Toevoegen**.

    ![de afbeelding over voegt nieuwe harde schijf](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. Selecteer **in Fysieke harde schijf**de schijf van de defecte VM die u aan de Azure VM hebt gekoppeld. Als er geen schijven worden weergegeven, controleert u of de schijf is ingesteld op offline met schijfbeheer.

    ![de afbeelding over het monteren van de schijf](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Selecteer **Apply** en vervolgens **OK**.

16. Dubbelklik op de vm en start deze.

17. Nu u werken aan de VM als de on-premises VM. U alle stappen voor het oplossen van problemen volgen die u nodig hebt.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Stap 3: Vervang de OS-schijf die wordt gebruikt door de defecte VM

1.  Nadat u de VM weer online hebt gezet, schakelt u de VM in de Hyper-V-manager uit.

2.  [De gerepareerde osschijf loskoppelen en loskoppelen](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Vervang de osschijf die door de VM wordt gebruikt door de gerepareerde osschijf](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Volgende stappen

Zie Problemen [met RDP-verbindingen met een Azure VM](troubleshoot-rdp-connection.md)als u problemen ondervindt bij het maken van verbinding met uw VM. Zie Problemen met de connectiviteit van [toepassingen op een Windows-vm oplossen voor](troubleshoot-app-connection.md)problemen met de toegang tot toepassingen op uw vm.

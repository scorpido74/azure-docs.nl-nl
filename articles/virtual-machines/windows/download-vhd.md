---
title: Een Windows VHD downloaden van Azure
description: Download een Windows VHD via de Azure-portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: d1c98fa4f3572c40279978d787b1719746478a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75940455"
---
# <a name="download-a-windows-vhd-from-azure"></a>Een Windows VHD downloaden van Azure

In dit artikel leert u hoe u een Windows Virtual Hard Disk (VHD)-bestand (Virtual Hard Disk) van Azure downloadt via de Azure-portal.

## <a name="optional-generalize-the-vm"></a>Optioneel: de VM generaliseren

Als u de VHD als [afbeelding](tutorial-custom-images.md) wilt gebruiken om andere VM's te maken, moet u [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) gebruiken om het besturingssysteem te generaliseren. 

Als u de VHD als afbeelding wilt gebruiken om andere VM's te maken, generaliseert u de VM.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als u dat nog niet hebt gedaan.
2. [Maak verbinding met de VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Open het venster Opdrachtprompt op de VM als beheerder.
4. Wijzig de map in *%windir%\system32\sysprep* en voer sysprep.exe uit.
5. Selecteer in het dialoogvenster Systeemvoorbereidingstool de optie **Systeem-out-of-box-ervaring (OOBE)** invoeren en controleer of **Generaliseren** is geselecteerd.
6. **Selecteer**Afsluiten opties en klik op **OK**. 


## <a name="stop-the-vm"></a>De virtuele machine stoppen

Een VHD kan niet worden gedownload van Azure als deze is gekoppeld aan een draaiende VM. Je moet de VM stoppen om een VHD te downloaden. 

1. Klik in het menu Hub in de Azure-portal op **Virtuele machines**.
1. Selecteer de VM in de lijst.
1. Klik op het blad voor de VM op **Stoppen**.


## <a name="generate-download-url"></a>Download-URL genereren

Als u het VHD-bestand wilt downloaden, moet u een [SAS-URL (Shared Access Signature)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) genereren. Wanneer de URL wordt gegenereerd, wordt een vervaldatum toegewezen aan de URL.

1. Klik op de pagina voor de virtuele machine op **Schijven** in het linkermenu.
1. Selecteer de schijf van het besturingssysteem voor de VM.
1. Selecteer **schijfexporteren** op de pagina voor de schijf in het linkermenu.
1. De standaardvervaldatum van de URL is *3600* seconden. Verhoog dit naar **36000** voor Windows OS-schijven.
1. Klik **op URL genereren**.

> [!NOTE]
> De vervaldatum wordt verhoogd vanaf de standaardperiode om voldoende tijd te bieden om het grote VHD-bestand voor een Windows Server-besturingssysteem te downloaden. Afhankelijk van uw verbinding u verwachten dat een VHD-bestand met het Windows Server-besturingssysteem enkele uren in beslag neemt. Als u een VHD voor een gegevensschijf downloadt, is de standaardtijd voldoende. 
> 
> 

## <a name="download-vhd"></a>VHD downloaden

1. Klik onder de GEGENEREERDe URL op Het VHD-bestand downloaden.
1. Mogelijk moet u op **Opslaan** in uw browser klikken om de download te starten. De standaardnaam voor het VHD-bestand is *abcd*.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [uploaden van een VHD-bestand naar Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Beheerde schijven maken van niet-beheerde schijven in een opslagaccount](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Azure-schijven beheren met PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


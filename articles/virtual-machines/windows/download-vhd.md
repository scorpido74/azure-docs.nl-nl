---
title: Een Windows-VHD downloaden vanuit Azure | Microsoft Docs
description: Down load een Windows-VHD met behulp van de Azure Portal.
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
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: c1c09382102045dd248b6771d8d0ea1ef090b6eb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079623"
---
# <a name="download-a-windows-vhd-from-azure"></a>Een Windows-VHD downloaden vanuit Azure

In dit artikel leert u hoe u een Windows-VHD-bestand (virtuele harde schijf) kunt downloaden van Azure met behulp van de Azure Portal.

## <a name="stop-the-vm"></a>De virtuele machine stoppen

Een VHD kan niet worden gedownload van Azure als deze is gekoppeld aan een actieve VM. U moet de virtuele machine stoppen om een VHD te downloaden. Als u een VHD als [installatie kopie](tutorial-custom-images.md) wilt gebruiken om andere virtuele machines met nieuwe schijven te maken, gebruikt u [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) om het besturings systeem in het bestand te generaliseren en stopt u vervolgens de virtuele machine. Als u de VHD als schijf wilt gebruiken voor een nieuw exemplaar van een bestaande virtuele machine of gegevens schijf, hoeft u de virtuele machine alleen te stoppen en toe te wijzen.

Voer de volgende stappen uit om de VHD als een installatie kopie te gebruiken om andere Vm's te maken:

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com/) als u dat nog niet hebt gedaan.
2.  [Maak verbinding met de virtuele machine](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  Open het opdracht prompt venster op de virtuele machine als beheerder.
4.  Wijzig de Directory in *%windir%\system32\sysprep* en voer Sysprep. exe uit.
5.  Selecteer in het dialoog venster hulp programma voor systeem voorbereiding de optie **systeem out-of-Box Experience (OOBE) opgeven**en zorg ervoor dat generaliseren is geselecteerd.
6.  Selecteer in afsluit opties de optie **Afsluiten**en klik vervolgens op **OK**. 

Voer de volgende stappen uit om de VHD als schijf te gebruiken voor een nieuw exemplaar van een bestaande virtuele machine of gegevens schijf:

1.  Klik in het menu hub in de Azure Portal op **virtual machines**.
2.  Selecteer de virtuele machine in de lijst.
3.  Klik op de Blade voor de virtuele machine op **stoppen**.

    ![VM stoppen](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS-URL genereren

Als u het VHD-bestand wilt downloaden, moet u een [SAS-URL (Shared Access Signature)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) genereren. Wanneer de URL wordt gegenereerd, wordt een verloop tijd toegewezen aan de URL.

1.  Klik in het menu van de Blade voor de virtuele machine op **schijven**.
2.  Selecteer de schijf met het besturings systeem voor de virtuele machine en klik vervolgens op **schijf exporteren**.
3.  Stel de verloop tijd van de URL in op *36000*.
4.  Klik op **URL genereren**.

    ![URL genereren](./media/download-vhd/export-generate-new.png)

> [!NOTE]
> De verval tijd wordt verhoogd van de standaard waarde om voldoende tijd te bieden voor het downloaden van het grote VHD-bestand voor een Windows Server-besturings systeem. Afhankelijk van uw verbinding kunt u een VHD-bestand met het Windows Server-besturings systeem verwachten dat er enkele uren mee moeten worden gedownload. Als u een VHD voor een gegevens schijf downloadt, is de standaard tijd voldoende. 
> 
> 

## <a name="download-vhd"></a>VHD downloaden

1.  Klik onder de gegenereerde URL op het VHD-bestand downloaden.

    ![VHD downloaden](./media/download-vhd/export-download.png)

2.  Mogelijk moet u in de browser op **Opslaan** klikken om het downloaden te starten. De standaard naam voor het VHD-bestand is *ABCD*.

    ![Klik op opslaan in de browser](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [uploaden van een VHD-bestand naar Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Beheerde schijven maken op basis van niet-beheerde schijven in een opslag account](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Azure-schijven beheren met Power shell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


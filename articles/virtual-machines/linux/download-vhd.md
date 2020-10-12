---
title: Een Linux-VHD downloaden vanuit Azure
description: Down load een Linux-VHD met behulp van de Azure CLI en de Azure Portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 897cae53e589f4058e5499c0e6e941d4f1d9bb2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87761047"
---
# <a name="download-a-linux-vhd-from-azure"></a>Een Linux-VHD downloaden vanuit Azure

In dit artikel leert u hoe u een virtueel VHD-bestand (virtuele harde schijf) van Linux kunt downloaden van Azure met behulp van de Azure Portal. 

## <a name="stop-the-vm"></a>De virtuele machine stoppen

Een VHD kan niet worden gedownload van Azure als deze is gekoppeld aan een actieve VM. U moet de virtuele machine stoppen om de VHD te downloaden. 

1.  Meld u aan bij [Azure Portal](https://portal.azure.com/).
2.  Selecteer **virtual machines**in het linkermenu.
3.  Selecteer de virtuele machine in de lijst.
4.  Selecteer op de pagina voor de virtuele machine **stoppen**.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Hiermee wordt de menu knop voor het stoppen van de virtuele machine weer gegeven.":::

## <a name="generate-sas-url"></a>SAS-URL genereren

Als u het VHD-bestand wilt downloaden, moet u een [SAS-URL (Shared Access Signature)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) genereren. Wanneer de URL wordt gegenereerd, wordt een verloop tijd toegewezen aan de URL.

1. Selecteer **schijven**in het menu van de pagina voor de virtuele machine.
2. Selecteer de schijf met het besturings systeem voor de virtuele machine en selecteer vervolgens **schijf exporteren**.
1. Als het nodig is, werkt u de waarde van **URL verloopt over (seconden)** om u voldoende tijd te geven om het downloaden te volt ooien. De standaard waarde is 3600 seconden (één uur).
3. Selecteer **URL genereren**.
 
      
## <a name="download-vhd"></a>VHD downloaden

1.  Selecteer onder de gegenereerde URL **het VHD-bestand downloaden**.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Hiermee wordt de menu knop voor het stoppen van de virtuele machine weer gegeven.":::

2.  Mogelijk moet u **Opslaan** in de browser selecteren om het downloaden te starten. De standaard naam voor het VHD-bestand is *ABCD*.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [uploaden en maken van een virtuele Linux-machine op basis van een aangepaste schijf met de Azure cli](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Azure-schijven beheren de Azure cli](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

---
title: Een Linux VHD downloaden van Azure
description: Download een Linux VHD met de Azure CLI en de Azure portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 02c3ee483e6a31960fd5123070a49f568ac4c690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968786"
---
# <a name="download-a-linux-vhd-from-azure"></a>Een Linux VHD downloaden van Azure

In dit artikel leert u hoe u een VHD-bestand (Virtual Hard Disk) van Linux downloadt van Azure met behulp van de Azure CLI- en Azure-portal. 

Als u dit nog niet hebt gedaan, installeert u [Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>De virtuele machine stoppen

Een VHD kan niet worden gedownload van Azure als deze is gekoppeld aan een draaiende VM. Je moet de VM stoppen om een VHD te downloaden. Als u een VHD als [afbeelding](tutorial-custom-images.md) wilt gebruiken om andere VM's met nieuwe schijven te maken, moet u het besturingssysteem in het bestand deprovisioneren en generaliseren en de VM stoppen. Als u de VHD als schijf wilt gebruiken voor een nieuw exemplaar van een bestaande vm of gegevensschijf, hoeft u alleen de VM te stoppen en uit te wijzen.

Voer de volgende stappen uit om de VHD als afbeelding te gebruiken om andere VM's te maken:

1. Gebruik SSH, de accountnaam en het openbare IP-adres van de VIRTUELE V.D. om er verbinding mee te maken en te deprovisioneren. U vindt het openbare IP-adres met [az-netwerk public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Met de parameter +user wordt ook het laatst ingerichte gebruikersaccount verwijderd. Als u accountreferenties indeelt aan de VM, laat u deze parameter +gebruiker weg. In het volgende voorbeeld wordt het laatst ingerichte gebruikersaccount verwijderd:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Meld u aan bij uw Azure-account met [az-aanmelding](https://docs.microsoft.com/cli/azure/reference-index).
3. Stop en detoewijzing van de VM.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generaliseer de VM. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Voer de volgende stappen uit om de VHD als schijf te gebruiken voor een nieuw exemplaar van een bestaande vm of gegevensschijf:

1.  Meld u aan bij [Azure Portal](https://portal.azure.com/).
2.  Selecteer **virtuele machines**in het linkermenu .
3.  Selecteer de VM in de lijst.
4.  Selecteer Op de pagina voor de vm de optie **Stoppen**.

    ![VM stoppen](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS-URL genereren

Als u het VHD-bestand wilt downloaden, moet u een [SAS-URL (Shared Access Signature)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) genereren. Wanneer de URL wordt gegenereerd, wordt een vervaldatum toegewezen aan de URL.

1.  Selecteer **Schijven**in het menu van de pagina voor de virtuele machine .
2.  Selecteer de schijf van het besturingssysteem voor de vm en selecteer **Schijfexporteren**.
3.  Selecteer **URL genereren**.

    ![URL genereren](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>VHD downloaden

1.  Selecteer onder de URL die is gegenereerd de optie **Het VHD-bestand downloaden**.
**
    ![VHD downloaden](./media/download-vhd/export-download.png)

2.  Mogelijk moet u **Opslaan** in de browser selecteren om de download te starten. De standaardnaam voor het VHD-bestand is *abcd*.

    ![Opslaan in de browser selecteren](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [uploaden en maken van een Linux-vm vanaf aangepaste schijf met de Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Azure-schijven beheren, de Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


---
title: Een Linux-VHD downloaden vanuit Azure
description: Down load een Linux-VHD met behulp van de Azure CLI en de Azure Portal.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 02c3ee483e6a31960fd5123070a49f568ac4c690
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968786"
---
# <a name="download-a-linux-vhd-from-azure"></a>Een Linux-VHD downloaden vanuit Azure

In dit artikel leert u hoe u een virtueel VHD-bestand (virtuele harde schijf) van Linux kunt downloaden van Azure met behulp van Azure CLI en Azure Portal. 

Als u dit nog niet hebt gedaan, installeert u [Azure cli](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>De virtuele machine stoppen

Een VHD kan niet worden gedownload van Azure als deze is gekoppeld aan een actieve VM. U moet de virtuele machine stoppen om een VHD te downloaden. Als u een VHD als [installatie kopie](tutorial-custom-images.md) wilt gebruiken om andere virtuele machines met nieuwe schijven te maken, moet u het besturings systeem dat in het bestand is opgenomen en generaliseren en de virtuele machine stoppen. Als u de VHD als schijf wilt gebruiken voor een nieuw exemplaar van een bestaande virtuele machine of gegevens schijf, hoeft u de virtuele machine alleen te stoppen en toe te wijzen.

Voer de volgende stappen uit om de VHD als een installatie kopie te gebruiken om andere Vm's te maken:

1. Gebruik SSH, de account naam en het open bare IP-adres van de virtuele machine om er verbinding mee te maken en de inrichting uit te sluiten. U kunt het open bare IP-adres vinden met [AZ Network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). De +-gebruikers parameter verwijdert ook de laatste ingerichte gebruikers account. Als u Baking-account referenties hebt in bij de VM, moet u deze + gebruikers parameter weglaten. In het volgende voor beeld wordt het laatste ingerichte gebruikers account verwijderd:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Meld u aan bij uw Azure-account met [AZ login](https://docs.microsoft.com/cli/azure/reference-index).
3. Stop de toewijzing van de virtuele machine en hef deze op.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generaliseer de virtuele machine. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Voer de volgende stappen uit om de VHD als schijf te gebruiken voor een nieuw exemplaar van een bestaande virtuele machine of gegevens schijf:

1.  Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2.  Selecteer **virtual machines**in het linkermenu.
3.  Selecteer de virtuele machine in de lijst.
4.  Selecteer op de pagina voor de virtuele machine **stoppen**.

    ![VM stoppen](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS-URL genereren

Als u het VHD-bestand wilt downloaden, moet u een [SAS-URL (Shared Access Signature)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) genereren. Wanneer de URL wordt gegenereerd, wordt een verloop tijd toegewezen aan de URL.

1.  Selecteer **schijven**in het menu van de pagina voor de virtuele machine.
2.  Selecteer de schijf met het besturings systeem voor de virtuele machine en selecteer vervolgens **schijf exporteren**.
3.  Selecteer **URL genereren**.

    ![URL genereren](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>VHD downloaden

1.  Selecteer onder de gegenereerde URL **het VHD-bestand downloaden**.
**
    ![VHD downloaden](./media/download-vhd/export-download.png)

2.  Mogelijk moet u **Opslaan** in de browser selecteren om het downloaden te starten. De standaard naam voor het VHD-bestand is *ABCD*.

    ![Selecteer opslaan in de browser](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [uploaden en maken van een virtuele Linux-machine op basis van een aangepaste schijf met de Azure cli](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Azure-schijven beheren de Azure cli](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


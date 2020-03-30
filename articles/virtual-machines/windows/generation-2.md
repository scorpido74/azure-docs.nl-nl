---
title: Azure-ondersteuning voor generatie 2 VM's
description: Overzicht van Azure-ondersteuning voor generatie 2 VM's
services: virtual-machines-windows
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: ed7d62ef5c1c4837b3eb6d02f301085178fe5e0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528214"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Ondersteuning voor generatie 2 VM's op Azure

Ondersteuning voor generatie 2 virtuele machines (VM's) is nu beschikbaar op Azure. U de generatie van een virtuele machine niet wijzigen nadat u deze hebt gemaakt, dus bekijk de overwegingen op deze pagina voordat u een generatie kiest.

Generatie 2 VM's ondersteunen belangrijke functies die niet worden ondersteund in generatie 1 VM's. Deze functies omvatten meer geheugen, Intel Software Guard Extensions (Intel SGX), en gevirtualiseerd persistent geheugen (vPMEM). Generatie 2 VM's die on-premises worden uitgevoerd, hebben een aantal functies die nog niet worden ondersteund in Azure. Zie de sectie [Functies en mogelijkheden](#features-and-capabilities) voor meer informatie.

Generatie 2 VM's maken gebruik van de nieuwe UEFI-gebaseerde opstartarchitectuur in plaats van de BIOS-gebaseerde architectuur die wordt gebruikt door generatie 1 VM's. In vergelijking met generatie 1 VM's, generatie 2 VM's zou kunnen hebben verbeterd boot en installatie tijden. Voor een overzicht van generatie 2 VM's en enkele verschillen tussen generatie 1 en generatie 2, zie [Moet ik een generatie 1 of 2 virtuele machine maken in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Generatie 2 VM-formaten

Generatie 1 VM's worden ondersteund door alle VM-formaten in Azure (met uitzondering van Mv2-serie VM's). Azure biedt nu generatie 2-ondersteuning voor de volgende geselecteerde VM-reeksen:

* [B-serie](https://docs.microsoft.com/azure/virtual-machines/windows/b-series-burstable)
* [DC-serie](../dcv2-series.md)
* [DSv2-serie](../dv2-dsv2-series.md) en [Dsv3-serie](../dv3-dsv3-series.md)
* [Esv3-serie](../ev3-esv3-series.md)
* [Fsv2-serie](../fsv2-series.md)
* [GS-serie](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#gs-series)
* [HB-serie](../hb-series.md)
* [HC-serie](../hc-series.md)
* [Ls-series](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-previous-gen#ls-series) en [Lsv2-series](../lsv2-series.md)
* [Mv2-serie](../mv2-series.md)
* [NCv2-series](../ncv2-series.md) en [NCv3-series](../ncv3-series.md)
* [ND-serie](../nd-series.md)
* [NVv3-serie](../nvv3-series.md)

> [!NOTE]
> Het gebruik van generatie 2 VM-beelden voor Mv2-serie VM's is over het algemeen beschikbaar, omdat de Mv2-serie werkt met generatie 2 VM beelden uitsluitend. Generatie 1 VM-beelden worden niet ondersteund op Vm's uit de Mv2-serie. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Generatie 2 VM-afbeeldingen in Azure Marketplace

Generatie 2 VM's ondersteunen de volgende Marketplace-afbeeldingen:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enterprise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* RHEL 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 7.0
* Cent OS 8.0, 7.7, 7.6, 7.5, 7.4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>On-premises vs. Azure-generatie 2 VM's

Azure ondersteunt momenteel niet een aantal functies die on-premises Hyper-V ondersteunt voor generatie 2 VM's.

| Generatie 2-functie                | On-premises Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Veilig opstarten                         | :heavy_check_mark:  | :x:   |
| Afgeschermde VM                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Beveiliging op basis van virtualisatie (VBS) | :heavy_check_mark:  | :x:   |
| VHDX-indeling                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Functies en mogelijkheden

### <a name="generation-1-vs-generation-2-features"></a>Generatie 1 vs. generatie 2 functies

| Functie | Eerste generatie | Tweede generatie |
|---------|--------------|--------------|
| Opstarten             | PCAT (PCAT)                      | UEFI (UEFI)                               |
| Schijfcontrollers | IDE                       | SCSI                               |
| Formaten van virtuele machines         | Alle VM-formaten | Alleen VM's die premium opslag ondersteunen |

### <a name="generation-1-vs-generation-2-capabilities"></a>Generatie 1 versus generatie 2 mogelijkheden

| Mogelijkheid | Eerste generatie | Tweede generatie |
|------------|--------------|--------------|
| OS-schijf > 2 TB                    | :x:                | :heavy_check_mark: |
| Aangepast schijf-/afbeeldings-/swapbesturingssysteem         | :heavy_check_mark: | :heavy_check_mark: |
| Ondersteuning voor virtuele machineschaalset | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Back-up/herstel                    | :heavy_check_mark: | :heavy_check_mark: |
| Gedeelde afbeeldingsgalerie              | :heavy_check_mark: | :heavy_check_mark: |
| Azure-schijfversleuteling             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Een generatie 2 VM maken

### <a name="marketplace-image"></a>Afbeelding van Marktplaats

In de Azure-portal of Azure CLI u generatie 2 VM's maken uit een Marketplace-afbeelding die UEFI-boot ondersteunt.

#### <a name="azure-portal"></a>Azure Portal

Hieronder vindt u de stappen om een generatie 2 (Gen2) VM in Azure-portal te maken.

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Selecteer **Een resource maken**.
1. Klik op **Alles bekijken** vanuit de Azure Marketplace aan de linkerkant.
1. Selecteer een afbeelding die Gen2 ondersteunt.
1. Klik **op Maken**.
1. Selecteer op het tabblad **Geavanceerd** onder de sectie **VM-generatie** de optie **Gen 2.**
1. Ga op het tabblad **Basisbeginselen** onder **instantie**naar **Grootte** en open het **mes met vm-grootte selecteren.**
1. Selecteer een [ondersteunde generatie 2 VM](#generation-2-vm-sizes).
1. Ga door de [stroom voor het maken van Azure-portalen](quick-create-portal.md) om de VM te maken.

![Gen 1- of Gen 2 VM selecteren](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

U PowerShell ook gebruiken om een VM te maken door rechtstreeks te verwijzen naar de generatie 1 of generatie 2 SKU.

Gebruik bijvoorbeeld de volgende PowerShell-cmdlet om een lijst `WindowsServer` met de SKU's in de aanbieding te krijgen.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

U de Azure CLI ook gebruiken om alle beschikbare generatie 2-afbeeldingen te bekijken, die worden vermeld door **Publisher.**

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Als u een VM maakt met Windows Server 2012 als besturingssysteem, selecteert u de VM SKU van generatie 1 (BIOS) of generatie 2 (UEFI), die er als volgt uitziet:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Zie de sectie [Functies en mogelijkheden](#features-and-capabilities) voor een actuele lijst met ondersteunde Marketplace-afbeeldingen.

### <a name="managed-image-or-managed-disk"></a>Beheerde afbeelding of beheerde schijf

U een generatie 2 VM maken op dezelfde manier als een generatie 1 VM.

### <a name="virtual-machine-scale-sets"></a>Virtuele-machineschaalsets

U ook generatie 2 VM's maken met behulp van virtuele machineschaalsets. Gebruik azure-schaalsets in Azure om 2 VM's van generatie te maken.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

* **Zijn generatie 2 VM's beschikbaar in alle Azure-regio's?**  
    Ja. Maar niet alle [generatie 2 VM maten](#generation-2-vm-sizes) zijn beschikbaar in elke regio. De beschikbaarheid van de generatie 2 VM is afhankelijk van de beschikbaarheid van de VM-grootte.

* **Is er een prijsverschil tussen generatie 1 en generatie 2 VM's?**  
   Nee.

* **Ik heb een .vhd-bestand van mijn on-premises generatie 2 VM. Kan ik dat vhd-bestand gebruiken om een generatie 2 VM in Azure te maken?**
  Ja, u uw generatie 2.vhd-bestand naar Azure brengen en dat gebruiken om een generatie 2-VM te maken. Gebruik hiervoor de volgende stappen:
    1. Upload de .vhd naar een opslagaccount in dezelfde regio waar u uw vm wilt maken.
    1. Maak een beheerde schijf vanuit het VHD-bestand. Stel de eigenschap Hyper-V Generation in op V2. Met de volgende PowerShell-opdrachten wordt de eigenschap Hyper-V-generatie ingesteld bij het maken van beheerde schijf.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Zodra de schijf beschikbaar is, maakt u een VM door deze schijf te bevestigen. De VM gemaakt zal een generatie 2 VM.
    Wanneer de generatie 2 VM wordt gemaakt, u optioneel de afbeelding van deze VM generaliseren. Door de afbeelding te generaliseren, u deze gebruiken om meerdere VM's te maken.

* **Hoe verhoog ik de schijfgrootte van het besturingssysteem?**  
  OS-schijven groter dan 2 TB zijn nieuw voor de generatie 2 VM's. Standaard zijn os-schijven kleiner dan 2 TB voor generatie 2 VM's. U de schijfgrootte verhogen tot een aanbevolen maximum van 4 TB. Gebruik de Azure CLI of de Azure-portal om de schijfgrootte van het besturingssysteem te vergroten. Zie Het [formaat van een schijf](expand-os-disk.md)programmatisch uitbreiden voor informatie over het programmatisch uitbreiden van schijven.

  Ga als u de grootte van de OS-schijf uit de Azure-portal vergroten:

  1. Ga in de Azure-portal naar de pagina VM-eigenschappen.
  1. Als u de VM wilt afsluiten en detoewijzing wilt opheffen, selecteert u de knop **Stoppen.**
  1. Selecteer **in** de sectie Schijven de osschijf die u wilt verhogen.
  1. Selecteer **in** de sectie Schijven de optie **Configuratie**en werk de **grootte bij** naar de gewenste waarde.
  1. Ga terug naar de pagina VM-eigenschappen en **Start** de VM.
  
  Mogelijk ziet u een waarschuwing voor schijven van besturingssysteem groter dan 2 TB. De waarschuwing is niet van toepassing op generatie 2 VM's. De schijfformaten van het besturingssysteem groter dan 4 TB worden echter *niet aanbevolen.*

* **Ondersteunen generatie 2 VM's versnelde netwerken?**  
    Ja. Zie [Een virtuele machine maken met versneld netwerken](../../virtual-network/create-vm-accelerated-networking-cli.md)voor meer informatie.

* **Wordt VHDX ondersteund op generatie 2?**  
    Nee, generatie 2 VM's ondersteunen alleen VHD.

* **Ondersteunen generatie 2 VM's Azure Ultra Disk Storage?**  
    Ja.

* **Kan ik een VM migreren van generatie 1 naar generatie 2?**  
    Nee, u de generatie van een vm niet wijzigen nadat u deze hebt gemaakt. Als u moet schakelen tussen VM-generaties, maakt u een nieuwe VM van een andere generatie.

* **Waarom is mijn VM-grootte niet ingeschakeld in de groottekiezer wanneer ik een Gen2-vm probeer te maken?**

    Dit kan worden opgelost door het volgende te doen:

    1. Controleer of de eigenschap **VM-generatie** is ingesteld op **Gen 2** op het tabblad **Geavanceerd.**
    1. Controleer of u op zoek bent naar een [VM-grootte die Gen2 VM's ondersteunt.](#generation-2-vm-sizes)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [virtuele machines van generatie 2 in Hyper-V.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

* Meer informatie over het voorbereiden van [een VHD](prepare-for-upload-vhd-image.md) om te uploaden van on-premises systemen naar Azure.

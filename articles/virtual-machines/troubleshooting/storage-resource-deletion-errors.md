---
title: Fouten in het verwijderen van opslagbronnen op Linux VM's in Azure oplossen | Microsoft Documenten
description: Problemen oplossen bij het verwijderen van opslagbronnen die gekoppelde VHD's bevatten.
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 50ab4b0f1e676ffcba0ce69ab6aa957e4c77ab88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058156"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Fouten in het verwijderen van opslagbronnen oplossen

In bepaalde scenario's u een van de volgende fouten tegenkomen die optreden tijdens het verwijderen van een Azure-opslagaccount, container of blob in een Azure Resource Manager-implementatie:

> **Kan het opslagaccount 'StorageAccountName' niet verwijderen. Fout: het opslagaccount kan niet worden verwijderd omdat de artefacten in gebruik zijn.**
> 
> **Kan # out of # container(s) niet verwijderen:<br>vhds: Er is momenteel een lease op de container en er is geen lease-ID opgegeven in de aanvraag.**
> 
> **Kan # out of #<br>blobs niet verwijderen: BlobName.vhd: Er is momenteel een lease op de blob en er is geen lease-ID opgegeven in de aanvraag.**

De VHD's die in Azure VM's worden gebruikt, zijn .vhd-bestanden die zijn opgeslagen als paginablobs in een standaard- of premium opslagaccount in Azure. Zie onze [inleiding tot beheerde schijven](../linux/managed-disks-overview.md)voor meer informatie over Azure-schijven.

Azure voorkomt het verwijderen van een schijf die is gekoppeld aan een vm om beschadiging te voorkomen. Het voorkomt ook verwijdering van containers en opslagaccounts met een paginablob die is gekoppeld aan een VM. 

Het proces om een opslagaccount, container of blob te verwijderen bij het ontvangen van een van deze fouten is: 
1. Blobs identificeren die aan een vm zijn gekoppeld
2. [VM's met gekoppelde **osschijf verwijderen**](#step-2-delete-vm-to-detach-os-disk)
3. [Alle **gegevensschijf(s) loskoppelen** van de resterende VM's](#step-3-detach-data-disk-from-the-vm)

Probeer opnieuw het opslagaccount, de container of de blob te verwijderen nadat deze stappen zijn voltooid.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Stap 1: Blob identificeren die is gekoppeld aan een virtuele machine

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenario 1: Een blob verwijderen – identificeer gekoppelde VM
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer alle **bronnen**in het menu Hub . Ga naar het opslagaccount onder **Blob Service** selecteer **Containers**en navigeer naar de blob om te verwijderen.
3. Als de **blobleasestatus** is **gehuurd,** klikt u met de rechtermuisknop en selecteert u **Metagegevens bewerken** om het deelvenster Kloddermetametaal te openen. 

    ![Schermafbeelding van de portal, met de blobs van het opslagaccount en de rechtermuisknop > 'Metagegevens bewerken' gemarkeerd](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. Controleer in het deelvenster Metagegevens van Blob de waarde voor **MicrosoftAzureCompute_VMName**. Deze waarde is de naam van de VM waaraan de VHD is gekoppeld. (Zie **belangrijk** als dit veld niet bestaat)
5. Controleer in het deelvenster Metagegevens van Klodder de waarde van **MicrosoftAzureCompute_DiskType**. Deze waarde geeft aan of de gekoppelde schijf besturingssysteem of gegevensschijf is (Zie **belangrijk** als dit veld niet bestaat). 

     ![Schermafbeelding van de portal, waarbij het deelvenster 'Blob-metagegevens' wordt geopend](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Als het type blobschijf **OSDisk is,** volg [stap 2: VM verwijderen om de schijf van het besturingssysteem los te maken](#step-2-delete-vm-to-detach-os-disk). Als het type blobschijf **anders DataDisk is,** volgt u de stappen in [stap 3: Gegevensschijf loskoppelen van de VM](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Als **MicrosoftAzureCompute_VMName** en **MicrosoftAzureCompute_DiskType** niet worden weergegeven in de blobmetagegevens, geeft dit aan dat de blob expliciet is geleased en niet is gekoppeld aan een vm. Gehuurde blobs kunnen niet worden verwijderd zonder eerst de lease te verbreken. Als u lease wilt verbreken, klikt u met de rechtermuisknop op de blob en selecteert u **Lease neigen**. Gehuurde blobs die niet aan een VM zijn gekoppeld, voorkomen verwijdering van de blob, maar voorkomen niet dat de container- of opslagaccount wordt verwijderd.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenario 2: Een container verwijderen - alle blob(s) in de container identificeren die aan VM's zijn gekoppeld
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer alle **bronnen**in het menu Hub . Ga naar het opslagaccount onder **Blob Service** selecteer **Containers**en zoek de container die moet worden verwijderd.
3. Klik hierom de container te openen en de lijst met blobs erin wordt weergegeven. Identificeer alle blobs met Blob-type = **paginablob** en leasestatus = **geleased** uit deze lijst. Volg scenario 1 om de VM te identificeren die aan elk van deze blobs is gekoppeld.

    ![Schermafbeelding van de portal, met blobs van het opslagaccount en de 'Leasestatus' met gemarkeerde 'Lease'.](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Volg [stap 2](#step-2-delete-vm-to-detach-os-disk) en stap [3](#step-3-detach-data-disk-from-the-vm) om VM(s) te verwijderen met **OSDisk** en **DataDisk**los te maken. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenario 3: Opslagaccount verwijderen - identificeer alle blob(s) binnen het opslagaccount die zijn gekoppeld aan VM's
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer alle **bronnen**in het menu Hub . Ga naar het opslagaccount onder **Blob Service** selecteer **Blobs**.
3. Identificeer in **het** deelvenster Containers alle containers waar **LeaseStatus** wordt **geleased** en volg [scenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) voor elke **gehuurde** container.
4. Volg [stap 2](#step-2-delete-vm-to-detach-os-disk) en stap [3](#step-3-detach-data-disk-from-the-vm) om VM(s) te verwijderen met **OSDisk** en **DataDisk**los te maken. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Stap 2: VM verwijderen om de schijf van het besturingssysteem los te maken
Als de VHD een OS-schijf is, moet u de VM verwijderen voordat de bijgevoegde VHD kan worden verwijderd. Er is geen extra actie vereist voor gegevensschijven die aan dezelfde vm zijn gekoppeld nadat deze stappen zijn voltooid:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **virtuele machines**in het menu Hub .
3. Selecteer de VM waaraan de VHD is gekoppeld.
4. Zorg ervoor dat niets actief wordt gebruikt de virtuele machine, en dat u niet langer de virtuele machine nodig.
5. Selecteer boven aan het **detailvenster Virtuele machine** de optie **Verwijderen**en klik op **Ja** om te bevestigen.
6. De VM moet worden verwijderd, maar de VHD kan worden behouden. De VHD mag echter niet langer aan een VM worden gekoppeld of er een huurovereenkomst op hebben. Het kan enkele minuten duren voordat de huurovereenkomst wordt vrijgegeven. Als u wilt controleren of de lease wordt **Blob properties** vrijgegeven, bladert u naar de bloblocatie en moet de **leasestatus** **beschikbaar**zijn.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Stap 3: Gegevensschijf loskoppelen van de VM
Als de VHD een gegevensschijf is, ontkoppelt u de VHD van de VM om de lease te verwijderen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **virtuele machines**in het menu Hub .
3. Selecteer de VM waaraan de VHD is gekoppeld.
4. Selecteer **Schijven** in het **detailvenster Virtuele machine.**
5. Selecteer de gegevensschijf waaraan de VHD is gekoppeld. U bepalen welke blob in de schijf is gekoppeld door de URL van de VHD te controleren.
6. U de bloblocatie verifiëren door op de schijf te klikken om het pad in het **Veld VHD URI** te controleren.
7. Selecteer **Bewerken** boven aan het deelvenster **Schijven.**
8. Klik **op het pictogram losmaken** van de gegevensschijf die moet worden verwijderd.

     ![Schermafbeelding van de portal, waarbij het deelvenster 'Blob-metagegevens' wordt geopend](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Selecteer **Opslaan**. De schijf is nu losgekoppeld van de VM, en de VHD is niet langer geleased. Het kan enkele minuten duren voordat de huurovereenkomst wordt vrijgegeven. Als u wilt controleren of de lease is vrijgegeven, bladert u naar de bloblocatie en moet de waarde **Leasestatus** worden **ontgrendeld** of **beschikbaar**. **Blob properties**

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm


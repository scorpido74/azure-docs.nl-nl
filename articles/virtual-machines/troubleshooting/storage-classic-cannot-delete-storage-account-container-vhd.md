---
title: Fouten oplossen wanneer u klassieke Azure-opslagaccounts, containers of VHD's verwijdert | Microsoft Documenten
description: Problemen oplossen bij het verwijderen van opslagbronnen die gekoppelde VHD's bevatten.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 95c85309058911d6767eb44efd7b37ddac7a9119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915034"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Problemen met het verwijderen van klassieke opslagbronnen oplossen
In dit artikel vindt u richtlijnen voor het oplossen van problemen wanneer een van de volgende fouten optreedt om het blobbestand voor klassieke Opslag, container of *.vhd-paginatekbestanden van Azure te verwijderen. 


Dit artikel heeft alleen betrekking op problemen met klassieke opslagbronnen. Als een gebruiker een klassieke virtuele machine verwijdert met behulp van de Azure-portal, PowerShell of CLI, worden de schijven niet automatisch verwijderd. De gebruiker krijgt de optie om de bron 'Schijf' te verwijderen. Als de optie niet is geselecteerd, voorkomt de bron 'Schijf' het verwijderen van het opslagaccount, de container en het werkelijke blobbestand van *.vhd-pagina.

Meer informatie over Azure-schijven vindt u [hier](../../virtual-machines/windows/managed-disks-overview.md). Azure voorkomt het verwijderen van een schijf die is gekoppeld aan een vm om beschadiging te voorkomen. Het voorkomt ook verwijdering van containers en opslagaccounts, die een paginablob hebben die is gekoppeld aan een VM. 

## <a name="what-is-a-disk"></a>Wat is een "Schijf"?
Een "Schijf"-bron wordt gebruikt om een blobbestand van een *.vhd-pagina te monteren op een virtuele machine, als een os-schijf of gegevensschijf. Een OS-schijf of gegevensschijfbron blijft een lease-overeenkomst houden op het *.vhd-bestand totdat deze is verwijderd. Elke opslagbron in het pad dat in de onderstaande afbeelding wordt weergegeven, kan niet worden verwijderd als er een bron 'Schijf' naar verwijst.

![Schermafbeelding van de portal, waarbij het deelvenster Schijf (klassiek) 'Eigenschap' is geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Stappen tijdens het verwijderen van een klassieke virtuele machine 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Verwijder de klassieke virtuele machine.
2. Als het selectievakje 'Schijven' is ingeschakeld, is de **schijflease** (weergegeven in afbeelding hierboven) gekoppeld aan de paginablob *.vhd verbroken. Het werkelijke paginablob *.vhd-bestand bestaat nog steeds in het opslagaccount.
![Schermafbeelding van de portal, waarbij het foutvenster 'Verwijderen' van de virtuele machine is geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Zodra de lease van de schijf(en) is verbroken, kan de paginablob(s) zelf worden verwijderd. Een opslagaccount of container kan worden verwijderd zodra alle "Disk"-bron die erin aanwezig is, is verwijderd.

>[!NOTE] 
>Als de gebruiker de VM verwijdert, maar niet de VHD, blijven er opslagkosten in rekening worden gebracht op het blob *.vhd-bestand van de pagina.vhd. De kosten zijn in overeenstemming met het type opslagaccount, kijk op de [prijspagina](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie. Als de gebruiker niet langer van plan is om de VHD(s) te gebruiken, verwijdert u deze/haar om toekomstige kosten te voorkomen. 

## <a name="unable-to-delete-storage-account"></a>Kan opslagaccount niet verwijderen 

Wanneer de gebruiker probeert een klassiek opslagaccount te verwijderen dat niet meer nodig is, kan de gebruiker het volgende gedrag zien.

#### <a name="azure-portal"></a>Azure Portal 
Gebruiker navigeert naar het klassieke opslagaccount op de [Azure-portal](https://portal.azure.com) en klikt op **Verwijderen**, de gebruiker ziet het volgende bericht: 

Met schijf(en) "bevestigd" aan een virtuele machine

![Schermafbeelding van de portal, waarbij het foutvenster 'Verwijderen' van de virtuele machine is geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Met schijf(en) "unattached" aan een virtuele machine

![Schermafbeelding van de portal, waarbij het deelvenster 'Verwijderen' zonder fout openen van de virtuele machine (klassiek)](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Gebruiker probeert een opslagaccount te verwijderen, dat niet meer wordt gebruikt, met behulp van klassieke PowerShell-cmdlets. De gebruiker ziet het volgende bericht:

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: Storage account myclassicaccount has some active image(s) and/or disk(s), b.v.  
> mijn klassieke account. Zorg ervoor dat deze afbeelding(en) en/of schijf(en) worden verwijderd voordat u dit opslagaccount verwijdert.</span>

## <a name="unable-to-delete-storage-container"></a>Geen opslagcontainer kunnen verwijderen

Wanneer de gebruiker een klassieke opslagblobcontainer probeert te verwijderen die niet meer nodig is, kan de gebruiker het volgende gedrag zien.

#### <a name="azure-portal"></a>Azure Portal 
Azure-portal staat niet toe dat de gebruiker een container verwijdert als er een lease voor 'Schijf(s)' bestaat die wijst op een blobbestand van een *.vhd-pagina in de container. Het is door het ontwerp om te voorkomen dat per ongeluk verwijderen van een vhd (s) bestand met Disk (s) lease op hen. 

![Schermafbeelding van de portal, waarbij het deelvenster 'lijstlijst' van de opslagcontainer is geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Als de gebruiker ervoor kiest powershell te verwijderen, leidt dit tot de volgende fout. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Naam vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : De externe server heeft een fout geretourneerd: (412) Er is momenteel een lease-overeenkomst op de container en er is geen lease-id opgegeven in de aanvraag.. HTTP-statuscode: 412 - HTTP-foutbericht: er is momenteel een lease-overeenkomst op de container en er is geen lease-id opgegeven in de aanvraag.</span>

## <a name="unable-to-delete-a-vhd"></a>Kan een vhd niet verwijderen 

Na het verwijderen van de virtuele Azure-machine probeert de gebruiker het vhd-bestand (paginablob) te verwijderen en het onderstaande bericht te ontvangen:

#### <a name="azure-portal"></a>Azure Portal 
Op de portal kunnen er twee ervaringen zijn, afhankelijk van de lijst met blobs die zijn geselecteerd voor verwijdering.

1. Als alleen 'Gehuurde' blobs zijn geselecteerd, wordt de knop Verwijderen niet weergegeven.
![Schermafbeelding van de portal, waarbij het deelvenster 'lijstlijst' van de container is geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Als een mix van 'Gehuurde' en 'Beschikbare' blobs zijn geselecteerd, wordt de knop Verwijderen weergegeven. Maar de "Delete" operatie zal achter de pagina blobs, die een disk lease op hen hebben. 
![Schermafbeelding van de portal, waarbij het deelvenster](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
!['lijstlijst' van de container wordt geopend Schermafbeelding van de portal, waarbij het geselecteerde blobvenster 'verwijderen' is geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Als de gebruiker ervoor kiest powershell te verwijderen, leidt dit tot de volgende fout. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : De externe server heeft een fout geretourneerd: (412) Er is momenteel een lease op de blob en er is geen lease-id opgegeven in de aanvraag.. HTTP-statuscode: 412 - HTTP-foutbericht: er is momenteel een lease-overeenkomst op de blob en er is geen lease-id opgegeven in de aanvraag.</span>


## <a name="resolution-steps"></a>Oplossingen

### <a name="to-remove-classic-disks"></a>Klassieke schijven verwijderen
Volg de volgende stappen op de Azure-portal:
1.  Navigeer naar de [Azure-portal](https://portal.azure.com).
2.  Navigeer naar de schijven(klassiek). 
3.  Klik op het ![tabblad Schijven. Schermafbeelding van de portal, waarbij het deelvenster 'lijstlijst' van de container is geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Selecteer de gegevensschijf en klik vervolgens op Schijf verwijderen.
 ![Schermafbeelding van de portal, waarbij het deelvenster 'lijstlijst' van de container is geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Probeer de bewerking Verwijderen opnieuw die voorheen is mislukt.
6.  Een opslagaccount of container kan niet worden verwijderd zolang deze één schijf heeft.

### <a name="to-remove-classic-images"></a>Klassieke afbeeldingen verwijderen   
Volg de volgende stappen op de Azure-portal:
1.  Navigeer naar de [Azure-portal](https://portal.azure.com).
2.  Navigeer naar OS-afbeeldingen (klassiek).
3.  Verwijder de afbeelding.
4.  Probeer de bewerking Verwijderen opnieuw die voorheen is mislukt.
5.  Een opslagaccount of container kan niet worden verwijderd zolang deze één afbeelding heeft.

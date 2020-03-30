---
title: Problemen met schijven oplossen die zijn gekoppeld aan Azure VM's | Microsoft Documenten
description: Biedt koppelingen naar probleemoplossingsbronnen voor virtuele harde schijven (Virtual Machine Virtual Machine, HdS) van Azure.
services: storage
author: roygara
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: b81c0947327dc8e84c5d3fbbecde3aa31313cc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061185"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Problemen met schijven die zijn gekoppeld aan Azure VM's oplossen 

Azure Virtual Machines (VM's) vertrouwen op Virtual Hard Disks (VHD's) voor de OS-schijf en alle aangesloten gegevensschijven. VHD's worden opgeslagen als paginablobs in een of meer Azure Storage-accounts. In dit artikel wordt gesproken over het oplossen van problemen met veelvoorkomende problemen die zich kunnen voordoen bij VHD's. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Fouten in het verwijderen van opslag voor een virtuele machine oplossen

In bepaalde gevallen u een fout tegenkomen tijdens het verwijderen van een opslagbron wanneer een VM in een Resource Manager-implementatie gekoppelde VHD's bevat. Zie een van de volgende artikelen voor hulp bij het oplossen van dit probleem: 

  * Op Linux VM's: [Fouten bij het verwijderen van opslag in de implementatie van Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Op Windows VM's: [fouten bij het verwijderen van opslag in de implementatie van Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Onverwachte reboots van VM's met gekoppelde VHD's oplossen

Als u onverwachte reboots tegenkomt van een VM met een groot aantal gekoppelde VHD's, raadpleegt u een van de volgende artikelen:

  * Op Linux VM's: [Onverwachte reboots van VM's met bijgevoegde VHD's](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Op Windows VM's: [Onverwachte reboots van VM's met bijgevoegde VHD's](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)

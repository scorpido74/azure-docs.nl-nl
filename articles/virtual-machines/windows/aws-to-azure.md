---
title: Een Windows AWS VM's verplaatsen naar Azure
description: Een Amazon Web Services (AWS) EC2 Windows-exemplaar verplaatsen naar een virtuele Azure-machine.
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
ms.openlocfilehash: 9bd01f24ac2cada02f51089d238519cd6c7e0248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74039273"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Een Windows-vm verplaatsen van Amazon Web Services (AWS) naar een virtuele Azure-machine

Als u azure-virtuele machines evalueert voor het hosten van uw workloads, u een bestaande Amazon Web Services (AWS) EC2 Windows VM-instantie exporteren en vervolgens de virtuele harde schijf (VHD) uploaden naar Azure. Zodra de VHD is geüpload, u een nieuwe VM in Azure maken vanuit de VHD. 

In dit artikel wordt het verplaatsen van één VM van AWS naar Azure. Zie [Virtuele machines migreren in Amazon Web Services (AWS) naar Azure met Azure Site Recovery](../../site-recovery/site-recovery-migrate-aws-to-azure.md)als u VM's op schaal van AWS naar Azure wilt verplaatsen.

## <a name="prepare-the-vm"></a>De virtuele machine voorbereiden 
 
U zowel algemene als gespecialiseerde VHD's uploaden naar Azure. Elk type vereist dat u de VM voorbereidt voordat u vanuit AWS exporteert. 

- **Gegeneraliseerde VHD** - een gegeneraliseerde VHD heeft al uw persoonlijke accountgegevens verwijderd met behulp van Sysprep. Als u van plan bent om de VHD te gebruiken als een afbeelding om nieuwe VM's van te maken, moet u: 
 
    * [Een Windows-VM voorbereiden](prepare-for-upload-vhd-image.md).  
    * Generaliseer de virtuele machine met Behulp van Sysprep.  

 
- **Gespecialiseerde VHD** - een gespecialiseerde VHD onderhoudt de gebruikersaccounts, applicaties en andere staatsgegevens van uw oorspronkelijke VM. Als u van plan bent de VHD-as-is te gebruiken om een nieuwe virtuele machine te maken, moet u ervoor zorgen dat de volgende stappen zijn voltooid.  
    * [Bereid een Windows VHD voor om te uploaden naar Azure.](prepare-for-upload-vhd-image.md) **Generaliseer** de VM niet met Sysprep. 
    * Verwijder alle hulpprogramma's en agents voor gastvirtualisatie die op de VM zijn geïnstalleerd (d.w.z. VMware-hulpprogramma's). 
    * Controleer of de VM is geconfigureerd om zijn IP-adres en DNS-instellingen via DHCP op te halen. Dit zorgt ervoor dat de server een IP-adres binnen het VNet verkrijgt wanneer deze wordt opgestart.  


## <a name="export-and-download-the-vhd"></a>De VHD exporteren en downloaden 

Exporteer de EC2-instantie naar een VHD in een Amazon S3-bucket. Volg de stappen in het Amazon-documentatieartikel [Een instantie exporteren als een VM met VM Importeren/exporteren](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) en voer de opdracht [create-instance-export-task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) uit om de EC2-instantie naar een VHD-bestand te exporteren. 

Het geëxporteerde VHD-bestand wordt opgeslagen in de Amazon S3-bucket die u opgeeft. De basissyntaxis voor het exporteren van de VHD \<is hieronder, vervang gewoon de tijdelijke aanduiding tekst tussen haakjes> met uw informatie.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Zodra de VHD is geëxporteerd, volg de instructies in [Hoe download ik een object uit een S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) 

> [!IMPORTANT]
> AWS brengt kosten voor gegevensoverdracht in rekening voor het downloaden van de VHD. Zie [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

Nu u de VHD uploaden naar Azure en een nieuwe vm maken. 

- Als u Sysprep op uw bron hebt uitgevoerd om deze te **generaliseren** voordat u exporteert, raadpleegt u [Een gegeneraliseerde VHD uploaden en deze gebruiken om een nieuwe VM's in Azure te maken](upload-generalized-managed.md)
- Als u Sysprep niet hebt uitgevoerd voordat u exporteert, wordt de VHD als **gespecialiseerd**beschouwd, zie [Een gespecialiseerde VHD uploaden naar Azure en een nieuwe VM maken](create-vm-specialized.md)

 

---
title: Azure VMware-oplossing per cloudSimple - Migreer workload VM's naar Private Cloud
description: Beschrijft hoe u virtuele machines migreert van on-premises vCenter naar CloudSimple Private Cloud vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87b8a112a319519dbde977ee30136a884137212d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019992"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Workload VM's migreren van on-premises vCenter naar Private Cloud vCenter-omgeving

Als u VM's wilt migreren van een on-premises datacenter naar uw CloudSimple Private Cloud, zijn verschillende opties beschikbaar.  De Private Cloud biedt native toegang tot VMware vCenter en tools die door VMware worden ondersteund, kunnen worden gebruikt voor workloadmigratie. In dit artikel worden enkele van de vCenter-migratieopties beschreven.

## <a name="prerequisites"></a>Vereisten

Migratie van VM's en gegevens uit uw on-premises datacenter vereist netwerkconnectiviteit van het datacenter naar uw Private Cloud-omgeving.  Gebruik een van de volgende methoden om netwerkconnectiviteit tot stand te brengen:

* [Site-to-Site VPN-verbinding](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tussen uw on-premises omgeving en uw Private Cloud.
* ExpressRoute Global Reach-verbinding tussen uw on-premises ExpressRoute-circuit en een CloudSimple ExpressRoute-circuit.

Het netwerkpad van uw on-premises vCenter-omgeving naar uw Private Cloud moet beschikbaar zijn voor migratie van VM's met vMotion.  Het vMotion-netwerk op uw on-premises vCenter moet routeringsmogelijkheden hebben.  Controleer of uw firewall al het vMotion-verkeer tussen uw on-premises vCenter en Private Cloud vCenter toestaat. (In de Private Cloud is routering op het vMotion-netwerk standaard geconfigureerd.)

## <a name="migrate-isos-and-templates"></a>ISO's en sjablonen migreren

Als u nieuwe virtuele machines wilt maken op uw Private Cloud, gebruikt u ISO's en VM-sjablonen.  Als u de ISO's en sjablonen wilt uploaden naar uw Private Cloud vCenter en deze beschikbaar wilt maken, gebruikt u de volgende methode.

1. Upload de ISO naar de Private Cloud vCenter van vCenter UI.
2. [Publiceer een inhoudsbibliotheek](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) op uw Private Cloud vCenter:

    1. Publiceer uw on-premises inhoudsbibliotheek.
    2. Maak een nieuwe inhoudsbibliotheek in het VCenter private cloud.
    3. Abonneer u op de gepubliceerde on-premises inhoudsbibliotheek.
    4. De inhoudsbibliotheek synchroniseren voor toegang tot geabonneerde inhoud.

## <a name="migrate-vms-using-powercli"></a>VM's migreren met PowerCLI

Als u VM's wilt migreren van het on-premises vCenter naar het VCenter voor Private Cloud, gebruikt u VMware PowerCLI of het Cross vCenter Workload Migration Utility dat beschikbaar is bij VMware Labs.  In het volgende voorbeeldscript worden de PowerCLI-migratieopdrachten weergegeven.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Als u de namen van de vCenterserver en ESXi-hosts wilt gebruiken, configureert u DNS-doorsturen van on-premises naar uw Private Cloud.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>VM's migreren met NSX Layer 2 VPN

Met deze optie u workloads live migreren van uw on-premises VMware-omgeving naar de Private Cloud in Azure.  Met dit uitgerekte Layer 2-netwerk is het subnet van on-premises beschikbaar in de Private Cloud.  Na migratie is nieuwe IP-adrestoewijzing niet vereist voor de VM's.

[Workloads migreren met Layer 2 stretched networks](migration-layer-2-vpn.md) beschrijft hoe je een Layer 2 VPN gebruiken om een Layer 2-netwerk uit te rekken van je on-premises omgeving naar je Private Cloud.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>VM's migreren met behulp van hulpprogramma's voor back-upen en noodherstel

Migratie van VM's naar Private Cloud kan worden gedaan met behulp van back-up-/hersteltools en hulpprogramma's voor noodherstel.  Gebruik de Private Cloud als doel voor herstel van back-ups die zijn gemaakt met behulp van een tool van derden.  De Private Cloud kan ook worden gebruikt als doel voor disaster recovery met VMware SRM of een tool van derden.

Zie de volgende onderwerpen voor meer informatie met behulp van deze tools:

* [Back-up workload virtuele machines op CloudSimple Private Cloud met Veeam B&R](backup-workloads-veeam.md)
* [CloudSimple Private Cloud instellen als een rampherstelsite voor on-premises VMware-workloads](disaster-recovery-zerto.md)

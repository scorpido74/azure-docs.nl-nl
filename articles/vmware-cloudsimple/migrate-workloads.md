---
title: "Azure VMware-oplossingen (AVS): werk belasting-Vm's migreren naar AVS Privécloud"
description: Hierin wordt beschreven hoe u virtuele machines migreert van on-premises vCenter naar AVS-Privécloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: af02bd947c73b670306704a10a09ca981b34c9a9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019992"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-avs-private-cloud-vcenter-environment"></a>VM-Vm's migreren van on-premises vCenter naar AVS Privécloud-omgeving

Er zijn verschillende opties beschikbaar om Vm's te migreren van een on-premises Data Center naar de Privécloud van uw AVS. De AVS-privécloud biedt systeem eigen toegang tot VMware vCenter en hulpprogram ma's die door VMware worden ondersteund, kunnen worden gebruikt voor de migratie van werk belastingen. In dit artikel worden enkele van de vCenter-migratie opties beschreven.

## <a name="prerequisites"></a>Vereisten

De migratie van Vm's en gegevens van uw on-premises Data Center vereist een netwerk verbinding van het Data Center naar de Privécloud van uw AVS. Gebruik een van de volgende methoden om netwerk verbinding tot stand te brengen:

* [Site-naar-site-VPN-verbinding](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) tussen uw on-premises omgeving en de privécloud van uw AVS.
* ExpressRoute Global Reach verbinding tussen uw on-premises ExpressRoute-circuit en een AVS ExpressRoute-circuit.

Het netwerkpad van uw on-premises vCenter-omgeving naar uw AVS-Privécloud moet beschikbaar zijn voor de migratie van Vm's met behulp van vMotion. Het netwerk van vMotion in uw on-premises vCenter moet routerings vaardigheden hebben. Controleer of uw firewall al het verkeer van vMotion toestaat tussen uw on-premises vCenter-en AVS Private Cloud vCenter. (In de Privécloud van de AVS wordt route ring in het netwerk van vMotion standaard geconfigureerd.)

## <a name="migrate-isos-and-templates"></a>Iso's en sjablonen migreren

Gebruik Iso's-en VM-sjablonen om nieuwe virtuele machines te maken in de Privécloud van uw AVS. Als u de Iso's en sjablonen naar uw AVS-Privécloud wilt uploaden en deze beschikbaar wilt maken, gebruikt u de volgende methode.

1. Upload de ISO naar de automatische AVS-Cloud-vCenter vanuit de vCenter-gebruikers interface.
2. [Een inhouds bibliotheek publiceren](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) op uw AVS-privécloud:

    1. Publiceer uw on-premises inhouds bibliotheek.
    2. Maak een nieuwe inhouds bibliotheek op de AVS-vCenter voor de Privécloud.
    3. Abonneer u op de gepubliceerde on-premises inhouds bibliotheek.
    4. Synchroniseer de inhouds bibliotheek voor toegang tot geabonneerde inhoud.

## <a name="migrate-vms-using-powercli"></a>Vm's migreren met behulp van PowerCLI

Als u Vm's van de on-premises vCenter wilt migreren naar de AVS-Privécloud, gebruikt u VMware PowerCLI of het hulp programma migratie van cross vCenter-workloads dat beschikbaar is vanuit VMware Labs. In het volgende voorbeeld script ziet u de PowerCLI-migratie-opdrachten.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Als u de namen van de doel-vCenter-Server en ESXi-hosts wilt gebruiken, configureert u DNS-forwarding van on-premises naar uw AVS-privécloud.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Vm's migreren met NSX Layer 2 VPN

Met deze optie kunt u livemigratie van werk belastingen van uw on-premises VMware-omgeving naar de AVS-Privécloud in Azure. Met dit uitgerekte Layer 2-netwerk is het subnet van on-premises beschikbaar in de Privécloud van de AVS. Na de migratie is nieuwe IP-adres toewijzing niet vereist voor de Vm's.

[Werk belastingen migreren met behulp van laag 2 uitgerekte netwerken](migration-layer-2-vpn.md) hier wordt beschreven hoe u een laag 2-VPN gebruikt om een laag 2-netwerk uit te breiden van uw on-premises omgeving naar uw AVS-privécloud.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Vm's migreren met back-ups en hulpprogram ma's voor herstel na nood gevallen

Migratie van Vm's naar AVS Privécloud kan worden uitgevoerd met back-up-en herstel Programma's en hulpprogram ma's voor herstel na nood gevallen. Gebruik de automatische AVS-Cloud als doel voor het terugzetten van back-ups die zijn gemaakt met een hulp programma van derden. De AVS-privécloud kan ook worden gebruikt als een doel voor nood herstel met behulp van VMware-beveiligings groep of een hulp programma van derden.

Zie de volgende onderwerpen voor meer informatie over het gebruik van deze hulpprogram ma's:

* [Een back-up maken van virtuele workload-machines op een AVS-privécloud met Veeam B & R](backup-workloads-veeam.md)
* [Een persoonlijke cloud van AVS instellen als een nood herstel site voor on-premises VMware-workloads](disaster-recovery-zerto.md)

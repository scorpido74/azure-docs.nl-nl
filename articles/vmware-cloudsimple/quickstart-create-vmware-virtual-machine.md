---
title: "Snelstart: VMware VM's gebruiken op Azure"
titleSuffix: Azure VMware Solution by CloudSimple
description: Meer informatie over het configureren en gebruiken van Vm's van VMware vanuit Azure-portal met Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019550"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Snelstart - VMware VM's gebruiken op Azure

Als u een virtuele machine wilt maken in de Azure-portal, gebruikt u virtuele machinesjablonen die uw CloudSimple-beheerder voor uw abonnement heeft ingeschakeld. De VM-sjablonen zijn te vinden in de VMware-infrastructuur.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>Voor het maken van VM's op Azure is een VM-sjabloon vereist

Maak een virtuele machine op uw Private Cloud vanuit de vCenter-gebruikersinterface. Als u een sjabloon wilt maken, volgt u de instructies in [Een virtuele machine klonen naar een sjabloon in de vSphere-webclient.](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html) Sla de VM-sjabloon op in uw Private Cloud vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Een virtuele machine maken in Azure Portal

1. Selecteer **Alle services**.

2. Zoeken naar **CloudSimple virtuele machines**.

3. Klik op**toevoegen**.

    ![CloudSimple virtuele machine maken](media/create-cloudsimple-virtual-machine.png)

4. Voer basisgegevens in en klik op **Volgende:Grootte**.

    ![CloudSimple virtuele machine maken - basics](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Veld | Beschrijving |
    | ------------ | ------------- |
    | Abonnement | Azure-abonnement dat is gekoppeld aan uw Private Cloud.  |
    | Resourcegroep | Resourcegroep waaraan de VM wordt toegewezen. U een bestaande groep selecteren of een nieuwe groep maken. |
    | Name | Naam om de VM te identificeren.  |
    | Locatie | Azure-regio waarin deze VM wordt gehost.  |
    | Privécloud | CloudSimple Private Cloud waar u de virtuele machine wilt maken. |
    | Resourcegroep | Toegewezen resourcegroep voor de VM. Kies uit de beschikbare resourcegroepen. |
    | vSphere-sjabloon | vSphere-sjabloon voor de VM.  |
    | Gebruikersnaam | Gebruikersnaam van de VM-beheerder (voor Windows-sjablonen).|
    | Wachtwoord |  Wachtwoord voor de VM-beheerder (voor Windows-sjablonen). |
    | Wachtwoord bevestigen | Bevestig het wachtwoord. |

5. Selecteer het aantal cores en geheugencapaciteit voor de VM en klik op **Volgende:Configuraties**. Schakel het selectievakje in als u volledige CPU-virtualisatie wilt blootstellen aan het gastbesturingssysteem. Toepassingen die hardwarevirtualisatie vereisen, kunnen worden uitgevoerd op virtuele machines zonder binaire vertaling of paravirtualisatie. Zie voor meer informatie het VMware-artikel <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">Bloot VMware Hardware Assisted Virtualization</a>.

    ![CloudSimple virtuele machine maken - grootte](media/create-cloudsimple-virtual-machine-size.png)

6. Configureer netwerkinterfaces en schijven zoals beschreven in de volgende tabellen en klik op **Controleren + maken**.

    ![CloudSimple virtuele machine maken - configuraties](media/create-cloudsimple-virtual-machine-configurations.png)

    Klik voor netwerkinterfaces op **Netwerkinterface toevoegen** en configureer de volgende instellingen.

    | Beheer | Beschrijving |
    | ------------ | ------------- |
    | Name | Voer een naam in om de interface te identificeren.  |
    | Netwerk | Kies in de lijst met geconfigureerde gedistribueerde poortgroep in uw Private Cloud vSphere.  |
    | Adapter | Selecteer een vSphere-adapter in de lijst met beschikbare typen die voor de VM zijn geconfigureerd. Zie voor meer informatie het VMware knowledge base-artikel <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Het kiezen van een netwerkadapter voor uw virtuele machine.</a> |
    | Inschakelen bij Boot | Kies of u de NIC-hardware wilt inschakelen wanneer de VM wordt opgestart. De standaardinstelling is **Inschakelen**. |

    Klik voor schijven op **Schijf toevoegen** en configureer de volgende instellingen.

    | Item | Beschrijving |
    | ------------ | ------------- |
    | Name | Voer een naam in om de schijf te identificeren.  |
    | Grootte | Selecteer een van de beschikbare maten.  |
    | SCSI-controller | Selecteer een SCSI-controller voor de schijf.  |
    | Modus | Hiermee bepaalt u hoe de schijf deelneemt aan momentopnamen. Kies een van de volgende opties: <br> - Onafhankelijke persistent: Alle gegevens die naar de schijf worden geschreven, worden permanent geschreven.<br> - Onafhankelijke niet-persistent: wijzigingen die op de schijf zijn geschreven, worden verwijderd wanneer u de virtuele machine uitschakelt of opnieuw instelt.  Met de onafhankelijke niet-permanente modus u de vm altijd in dezelfde status opnieuw starten. Zie voor meer informatie de <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware-documentatie</a>.

7. Wanneer de validatie is voltooid, controleert u de instellingen en klikt u op **Maken**. Als u wijzigingen wilt aanbrengen, klikt u op de tabbladen bovenaan of klikt u op de tabbladen.

    ![CloudSimple virtuele machine maken - review](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Volgende stappen

* [Lijst met virtuele cloudmachines van CloudSimple weergeven](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [CloudSimple virtuele machine beheren vanuit Azure](azure-manage-vm.md)

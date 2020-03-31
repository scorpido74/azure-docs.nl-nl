---
title: Azure VMware-oplossing door CloudSimple - Een virtuele machine maken in Azure met VM-sjablonen
description: Beschrijft hoe u virtuele machines in Azure maakt met VM-sjablonen op de VMware-infrastructuur voor uw CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ee3029de9826aee17dc76d0e69f08b3c1068423b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244691"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Een virtuele machine maken in Azure met VM-sjablonen op de VMware-infrastructuur

U een virtuele machine maken in de Azure-portal met VM-sjablonen op de VMware-infrastructuur die uw CloudSimple-beheerder voor uw abonnement heeft ingeschakeld.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [Azure Portal](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>CloudSimple virtuele machine maken

1. Selecteer **Alle services**.

2. Zoeken naar **CloudSimple virtuele machines**.

3. Klik op**toevoegen**.

    ![CloudSimple virtuele machine maken](media/create-cloudsimple-virtual-machine.png)

4. Voer basisgegevens in op **Volgende:Grootte**.

    > [!NOTE]
    > Voor het maken van virtuele machines in CloudSimple op Azure is een VM-sjabloon vereist.  Deze VM-sjabloon moet bestaan op uw Private Cloud vCenter.  Maak een virtuele machine op uw Private Cloud vanuit vCenter UI met het gewenste besturingssysteem en configuraties.  Met behulp van instructies in [Kloon een virtuele machine naar een sjabloon in de vSphere-webclient,](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html)maakt u een sjabloon.

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
    | Gebruikersnaam | Gebruikersnaam van de VM-beheerder (voor Windows-sjablonen)|
    | Wachtwoord <br>Wachtwoord bevestigen | Wachtwoord voor de VM-beheerder (voor Windows-sjablonen).  |

5. Selecteer het aantal cores en geheugencapaciteit voor de VM en klik op **Volgende:Configuraties**. Schakel het selectievakje in als u volledige CPU-virtualisatie wilt blootstellen aan het gastbesturingssysteem, zodat toepassingen waarvoor hardwarevirtualisatie vereist is, op virtuele machines kunnen worden uitgevoerd zonder binaire vertaling of paravirtualisatie. Zie voor meer informatie het VMware-artikel [Bloot VMware Hardware Assisted Virtualization](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![CloudSimple virtuele machine maken - grootte](media/create-cloudsimple-virtual-machine-size.png)

6. Configureer netwerkinterfaces en schijven zoals beschreven in de volgende tabellen en klik op **Controleren + maken**.

    ![CloudSimple virtuele machine maken - configuraties](media/create-cloudsimple-virtual-machine-configurations.png)

    Klik voor netwerkinterfaces op **Netwerkinterface toevoegen** en configureer de volgende instellingen.

    | Beheer | Beschrijving |
    | ------------ | ------------- |
    | Name | Voer een naam in om de interface te identificeren.  |
    | Netwerk | Kies in de lijst met geconfigureerde gedistribueerde poortgroep in uw Private Cloud vSphere.  |
    | Adapter | Selecteer een vSphere-adapter in de lijst met beschikbare typen die voor de VM zijn geconfigureerd. Zie voor meer informatie het VMware knowledge base-artikel [Het kiezen van een netwerkadapter voor uw virtuele machine.](https://kb.vmware.com/s/article/1001805) |
    | Inschakelen bij Boot | Kies of u de NIC-hardware wilt inschakelen wanneer de VM wordt opgestart. De standaardinstelling is **Inschakelen**. |

    Klik voor schijven op **Schijf toevoegen** en configureer de volgende instellingen.

    | Item | Beschrijving |
    | ------------ | ------------- |
    | Name | Voer een naam in om de schijf te identificeren.  |
    | Grootte | Selecteer een van de beschikbare maten.  |
    | SCSI-controller | Selecteer een SCSI-controller voor de schijf.  |
    | Modus | Hiermee bepaalt u hoe de schijf deelneemt aan momentopnamen. Kies een van de volgende opties: <br> - Onafhankelijke persistent: Alle gegevens die naar de schijf worden geschreven, worden permanent geschreven.<br> - Onafhankelijke niet-persistent: wijzigingen die op de schijf zijn geschreven, worden verwijderd wanneer u de virtuele machine uitschakelt of opnieuw instelt.  Met de onafhankelijke niet-permanente modus u de vm altijd in dezelfde status opnieuw starten. Zie voor meer informatie de [VMware-documentatie](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. Zodra de validatie is voltooid, controleert u de instellingen en klikt u op **Maken**. Als u wijzigingen wilt aanbrengen, klikt u op de tabbladen bovenaan of klikt u op de tabbladen.

    ![CloudSimple virtuele machine maken - review](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Lijst met virtuele cloudmachines van CloudSimple weergeven

1. Selecteer **Alle services**.

2. Zoeken naar **CloudSimple virtuele machines**.

3. Selecteer de waarvoor uw Private Cloud is gemaakt.

    ![Lijst met virtuele cloudmachines van CloudSimple](media/list-cloudsimple-virtual-machines.png)

Lijst met virtuele cloudmachines bevat virtuele machines die zijn gemaakt met Azure-portal.  Virtuele machines die zijn gemaakt op Private Cloud vCenter in de toegewezen vCenter-resourcegroep, worden weergegeven in de lijst.  

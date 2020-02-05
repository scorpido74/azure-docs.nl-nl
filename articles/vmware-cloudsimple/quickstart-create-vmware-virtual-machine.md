---
title: 'Quick start voor Azure VMware-oplossingen (AVS): virtuele VMware-machines maken op Azure'
description: Meer informatie over het maken en configureren van virtuele VMware-machines van Azure Portal met behulp van Azure VMware-oplossingen (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 575d59d60ebfcfdbe4d234d608e8d988006773c2
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019550"
---
# <a name="quickstart---create-vmware-vms-on-azure"></a>Snelstartgids: virtuele VMware-machines maken op Azure

Als u een virtuele machine in de Azure Portal wilt maken, gebruikt u virtuele-machine sjablonen die door uw AVS-beheerder zijn ingeschakeld voor uw abonnement. De VM-sjablonen vindt u in de VMware-infra structuur.

## <a name="avs-vm-creation-on-azure-requires-a-vm-template"></a>Voor het maken van een AVS-VM in Azure is een VM-sjabloon vereist

Een virtuele machine maken in de Privécloud van uw AVS vanuit de vCenter-gebruikers interface. Als u een sjabloon wilt maken, volgt u de instructies in [een virtuele machine klonen naar een sjabloon in de vSphere-webclient](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Sla de VM-sjabloon op in uw automatische AVS-Cloud-vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Een virtuele machine maken in de Azure Portal

1. Selecteer **Alle services**.

2. Zoeken naar **AVS-virtual machines**.

3. Klik op **Add**.

    ![Virtuele AVS-machine maken](media/create-cloudsimple-virtual-machine.png)

4. Voer basis informatie in en klik op **volgende: grootte**.

    ![Virtuele AVS-machine maken-basis beginselen](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Veld | Beschrijving |
    | ------------ | ------------- |
    | Abonnement | Het Azure-abonnement dat is gekoppeld aan de Privécloud van uw AVS. |
    | Resourcegroep | De resource groep waaraan de virtuele machine wordt toegewezen. U kunt een bestaande groep selecteren of een nieuwe maken. |
    | Name | Naam voor het identificeren van de virtuele machine.  |
    | Locatie | De Azure-regio waarin deze VM wordt gehost.  |
    | Privécloud van de automatische AVS | De Privécloud die u wilt maken voor de virtuele machine. |
    | Resourcegroep | Toegewezen resource groep voor de virtuele machine. Selecteer een van de beschik bare resource groepen. |
    | vSphere-sjabloon | vSphere-sjabloon voor de virtuele machine.  |
    | Gebruikersnaam | De gebruikers naam van de VM-beheerder (voor Windows-sjablonen).|
    | Wachtwoord |  Wacht woord voor de VM-beheerder (voor Windows-sjablonen). |
    | Wachtwoord bevestigen | Bevestig het wachtwoord. |

5. Selecteer het aantal kernen en geheugen capaciteit voor de virtuele machine en klik op **volgende: configuraties**. Schakel het selectie vakje in als u wilt dat volledige CPU-virtualisatie wordt weer gegeven op het gast besturingssysteem. Toepassingen waarvoor hardwarematige virtualisatie vereist is, kunnen worden uitgevoerd op virtuele machines zonder binaire omzetting of paravirtualisatie. Zie voor meer informatie het VMware-artikel een door <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">VMware ondersteunde virtualisatie beschikbaar</a>maken.

    ![Virtuele machine van AVS maken-grootte](media/create-cloudsimple-virtual-machine-size.png)

6. Configureer netwerk interfaces en schijven zoals beschreven in de volgende tabellen en klik op **beoordelen + maken**.

    ![Virtuele AVS-machine maken-configuraties](media/create-cloudsimple-virtual-machine-configurations.png)

    Voor netwerk interfaces klikt u op **netwerk interface toevoegen** en configureert u de volgende instellingen.

    | Beheer | Beschrijving |
    | ------------ | ------------- |
    | Name | Voer een naam in om de interface te identificeren.  |
    | Netwerk | Selecteer in de lijst met geconfigureerde gedistribueerde poort groepen in de vSphere van uw AVS-persoonlijke Cloud. |
    | Hostadapter | Selecteer een vSphere-adapter in de lijst met beschik bare typen die voor de virtuele machine zijn geconfigureerd. Zie het artikel over VMware Knowledge Base voor meer informatie. <a href="https://kb.vmware.com/s/article/1001805" target="_blank">Kies een netwerk adapter voor de virtuele machine</a>. |
    | Inschakelen bij opstarten | Kies of u de NIC-hardware wilt inschakelen wanneer de virtuele machine wordt opgestart. De standaard instelling is **ingeschakeld**. |

    Voor schijven, klikt u op **schijf toevoegen** en configureert u de volgende instellingen.

    | Item | Beschrijving |
    | ------------ | ------------- |
    | Name | Voer een naam in om de schijf aan te duiden. |
    | Grootte | Selecteer een van de beschik bare grootten. |
    | SCSI-controller | Selecteer een SCSI-controller voor de schijf. |
    | Modus | Hiermee wordt bepaald hoe de schijf deel uitmaakt van moment opnamen. Kies een van de volgende opties: <br> -Onafhankelijk permanent: alle gegevens die naar de schijf worden geschreven, worden permanent geschreven.<br> -Onafhankelijk niet-persistent: wijzigingen die naar de schijf worden geschreven, worden genegeerd wanneer u de virtuele machine uitschakelt of opnieuw instelt. Met een onafhankelijke, niet-permanente modus kunt u de virtuele machine in dezelfde staat altijd opnieuw opstarten. Raadpleeg de <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">VMware-documentatie</a>voor meer informatie.

7. Wanneer de validatie is voltooid, controleert u de instellingen en klikt u op **maken**. Als u wijzigingen wilt aanbrengen, klikt u op de tabbladen bovenaan of klikt u op.

    ![Virtuele AVS-machine maken-controleren](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Volgende stappen

* [Lijst met virtuele AVS-machines weer geven](azure-create-vm.md#view-list-of-avs-virtual-machines)
* [De virtuele machine van AVS beheren vanuit Azure](azure-manage-vm.md)

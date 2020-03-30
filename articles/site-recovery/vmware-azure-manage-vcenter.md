---
title: VMware vCenter-servers beheren in Azure Site Recovery
description: In dit artikel wordt beschreven hoe vMware vCenter voor noodherstel van VMware VM's met Azure worden toegevoegd en beheren met Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257262"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter-server beheren

In dit artikel worden beheeracties op een VMware vCenter Server in [Azure Site Recovery samengevat.](site-recovery-overview.md)

## <a name="verify-prerequisites-for-vcenter-server"></a>Vereisten voor vCenter Server verifiëren

De vereisten voor vCenter-servers en VM's tijdens het herstel van VMware-VM's voor Azure worden weergegeven in de [ondersteuningsmatrix.](vmware-physical-azure-support-matrix.md#replicated-machines)

## <a name="set-up-an-account-for-automatic-discovery"></a>Een account instellen voor automatische detectie

Wanneer u noodherstel instelt voor on-premises VMware VM's, heeft Site Recovery toegang nodig tot de vCenter Server/vSphere-host. De siteherstelprocesserver kan vervolgens automatisch VM's detecteren en deze naar behoefte niet meer detecteren. Standaard wordt de processerver uitgevoerd op de siteherstelconfiguratieserver. Voeg als volgt een account toe voor de configuratieserver om verbinding te maken met de vCenter Server/vSphere-host:

1. Meld u aan bij de configuratieserver.
1. Open het hulpprogramma voor configuratieserver _(cspsconfigtool.exe)_ met de snelkoppeling Bureaublad.
1. Klik op het tabblad **Account beheren** op **Account toevoegen**.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Geef de accountgegevens op en klik op **OK** om deze toe te voegen. De bevoegdheden van het account moeten worden samengevat in de tabel met accountmachtigingen.

   > [!NOTE]
   > Het duurt ongeveer 15 minuten om accountgegevens te synchroniseren met Site Recovery.

### <a name="account-permissions"></a>Accountmachtigingen

|**Taak** | **Account** | **Machtigingen** | **Details**|
|--- | --- | --- | ---|
|**VM-detectie/-migratie (zonder failback)** | Ten minste een alleen-lezen gebruikersaccount. | Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **Geen toegang** toe aan het onderliggende object voor **het onderliggende** object aan de onderliggende objecten (vSphere-hosts, gegevensopslag, virtuele machines en netwerken).|
|**Replicatie/failover** | Ten minste een alleen-lezen gebruikersaccount. | Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **Geen toegang** toe aan het **onderliggende** object aan het onderliggende object (vSphere-hosts, gegevensopslag, virtuele machines en netwerken).<br/><br/> Handig voor migratiedoeleinden, maar niet volledige replicatie, failover, failback.|
|**Replicatie/failover/failback** | We raden u aan een rol (AzureSiteRecoveryRole) te maken met de vereiste machtigingen en de rol vervolgens toe te wijzen aan een VMware-gebruiker of -groep. | Object (datacenterobject) > zich overgeven aan onderliggend object, rol=AzureSiteRecoveryRole<br/><br/> Gegevensopslag –> Ruimte toewijzen, browsen in de gegevensopslag, bestandsbewerkingen op laag niveau, bestand verwijderen, bestanden van virtuele machines bijwerken<br/><br/> Netwerk –> Netwerk toewijzen<br/><br/> Resource –> VM toewijzen aan resourcegroep, uitgeschakelde VM migreren, ingeschakelde VM migreren<br/><br/> Taken –> Taak maken, taak bijwerken<br/><br/> Virtuele machine –> Configuratie<br/><br/> Virtuele machine –> Interactie –> vraag beantwoorden, apparaatverbinding, CD's configureren, diskettes configureren, uitschakelen, inschakelen, VMware-hulpprogramma's installeren<br/><br/> Virtuele machine -> Inventaris –> Maken, registreren, registratie opheffen<br/><br/> Virtuele machine –> Inrichten –> Downloaden van virtuele machine toestaan, toestaan dat bestanden van virtuele machines worden geüpload<br/><br/> Virtuele machine –> Momentopnamen -> Momentopnamen verwijderen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **Geen toegang** toe aan het onderliggende object voor **het onderliggende** object aan de onderliggende objecten (vSphere-hosts, gegevensopslag, virtuele machines en netwerken).|

## <a name="add-vmware-server-to-the-vault"></a>VMware-server toevoegen aan de kluis

Wanneer u noodherstel instelt voor on-premises Vm's vMware, voegt u de vCenter Server/vSphere-host toe waarop u VM's ontdekt aan de kluis Site Recovery als volgt:

1. Open de configuratieserver in vault > **Site Recovery Infrastructure** > **Severs.**
1. Klik **op de** pagina Details op **vCenter**.
1. Geef **in VCenter toevoegen**een vriendelijke naam op voor de vSphere-host- of vCenter-server.
1. Geef het IP-adres of FQDN van de server op.
1. Behoud poort 443 tenzij de VMware-servers zijn geconfigureerd om te luisteren naar aanvragen van een andere poort.
1. Selecteer het account dat wordt gebruikt om verbinding te maken met de VMware vCenter- of vSphere ESXi-server. Klik vervolgens op **OK**.

## <a name="modify-credentials"></a>Referenties wijzigen

Indien nodig u de referenties die worden gebruikt om verbinding te maken met de vCenter Server/vSphere-host als volgt wijzigen:

1. Meld u aan bij de configuratieserver.
1. Open het hulpprogramma voor configuratieserver _(cspsconfigtool.exe)_ met de snelkoppeling Bureaublad.
1. Klik **op Account toevoegen** op het tabblad Account **beheren.**

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Geef de nieuwe accountgegevens op en klik op **OK**. Het account heeft de machtigingen nodig die worden vermeld in de tabel [met accountmachtigingen.](#account-permissions)
1. Open de configuratieserver in de kluis > **Site Recovery Infrastructure** > **Configuration Severs.**
1. Klik **in Details**op Server **vernieuwen**.
1. Nadat de taak Server vernieuwen is voltooid, selecteert u de vCenterserver.
1. Selecteer **in het samengevat**het nieuw toegevoegde account in **vCenter server/vSphere-hostaccount**en klik op **Opslaan**.

   ![wijzigen-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Een vCenter-server verwijderen

1. Open de configuratieserver in de kluis > **Site Recovery Infrastructure** > **Configuration Severs.**
1. Selecteer **op** de pagina Details de vCenter-server.
1. Klik op de knop **Verwijderen.**

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Het IP-adres en de poort wijzigen

U het IP-adres van de vCenterserver of de poorten die worden gebruikt voor communicatie tussen de server en siteherstel wijzigen. Site Recovery heeft standaard toegang tot vCenter Server/vSphere-hostinformatie via poort 443.

1. Klik in de kluis > **Site Recovery Infrastructure** > **Configuration Servers**op de configuratieserver waaraan de vCenterServer is toegevoegd.
1. Klik **in vCenter-servers**op de vCenter-server die u wilt wijzigen.
1. **Werk in het kort**het IP-adres en de poort bij en sla de wijzigingen op.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Als wijzigingen effectief worden, wacht u 15 minuten of [vernieuwt u de configuratieserver.](vmware-azure-manage-configuration-server.md#refresh-configuration-server)

## <a name="migrate-all-vms-to-a-new-server"></a>Alle VM's migreren naar een nieuwe server

Als u alle VM's wilt migreren om een nieuwe vCenter-server te gebruiken, hoeft u alleen het IP-adres dat is toegewezen aan de vCenterserver bij te werken. Voeg geen ander VMware-account toe, omdat dit kan leiden tot dubbele vermeldingen. Werk het adres als volgt bij:

1. Klik in de kluis > **Site Recovery Infrastructure** > **Configuration Servers**op de configuratieserver waaraan de vCenterServer is toegevoegd.
1. Klik in de sectie **vCenter-servers** op de vCenter-server waarvan u wilt migreren.
1. **Werk in het kort**het IP-adres bij naar dat van de nieuwe vCenterServer en sla de wijzigingen op.
1. Zodra het IP-adres is bijgewerkt, ontvangt Site Recovery vm-detectiegegevens van de nieuwe vCenter-server. Dit heeft geen invloed op lopende replicatieactiviteiten.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Een paar VM's migreren naar een nieuwe server

Als u slechts een paar van uw replicerende VM's wilt migreren naar een nieuwe vCenter-server, gaat u als volgt te werk:

1. [Voeg](#add-vmware-server-to-the-vault) de nieuwe vCenter Server toe aan de configuratieserver.
1. [Schakel replicatie uit](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) voor VM's die naar de nieuwe server worden verplaatst.
1. Migreer in VMware de VM's naar de nieuwe vCenterServer.
1. [Schakel replicatie](vmware-azure-tutorial.md#enable-replication) voor de gemigreerde VM's opnieuw in en selecteer de nieuwe vCenterserver.

## <a name="migrate-most-vms-to-a-new-server"></a>De meeste VM's migreren naar een nieuwe server

Als het aantal VM's dat u wilt migreren naar een nieuwe vCenter-server hoger is dan het aantal VM's dat op de oorspronkelijke vCenterServer blijft staan, gaat u als volgt te werk:

1. [Werk het IP-adres](#modify-the-ip-address-and-port) dat is toegewezen aan de vCenterserver in de configuratieserverinstellingen bij naar het adres van de nieuwe vCenterServer.
1. [Schakel replicatie uit](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) voor de weinige VM's die op de oude server blijven staan.
1. [Voeg de oude vCenterServer](#add-vmware-server-to-the-vault) en het IP-adres toe aan de configuratieserver.
1. [Replicatie opnieuw inschakelen](vmware-azure-tutorial.md#enable-replication) voor de VM's die op de oude server blijven staan.

## <a name="next-steps"></a>Volgende stappen

Zie Problemen [oplossen met detectiefouten vCenter Server](vmware-azure-troubleshoot-vcenter-discovery-failures.md)als u problemen hebt.

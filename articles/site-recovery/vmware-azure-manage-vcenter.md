---
title: VMware vCenter-servers beheren voor nood herstel van virtuele VMware-machines naar Azure met behulp van Azure Site Recovery | Microsoft Docs '
description: In dit artikel wordt beschreven hoe u VMware vCenter kunt toevoegen en beheren voor nood herstel van virtuele VMware-machines in azure met Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 59088d8351bf89c859312774e3e9e396be8dd532
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904259"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter-Server beheren

In dit artikel worden de verschillende Site Recovery bewerkingen beschreven die kunnen worden uitgevoerd op een VMware vCenter. Controleer de [vereisten](vmware-physical-azure-support-matrix.md#replicated-machines) voordat u begint.


## <a name="set-up-an-account-for-automatic-discovery"></a>Een account instellen voor automatische detectie

Site Recovery moet toegang hebben tot VMware voor de proces server om automatisch virtuele machines te detecteren en voor failover en failback van virtuele machines. Maak als volgt een account voor toegang:

1. Meld u aan bij de computer met de configuratie server.
2. Open de cspsconfigtool. exe starten met behulp van de snelkoppeling op het bureau blad.
3. Klik op het tabblad **account beheren** op **account toevoegen** .

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Geef de account gegevens op en klik op **OK** om deze toe te voegen.  Het account moet de bevoegdheden in de volgende tabel hebben. 

Het duurt ongeveer 15 minuten voordat de account gegevens worden gesynchroniseerd met de Site Recovery-service.

### <a name="account-permissions"></a>Account machtigingen

|**Taak** | **Account** | **Machtigingen** | **Details**|
|--- | --- | --- | ---|
|**Automatische detectie/migratie (zonder failback)** | U hebt mini maal een alleen-lezen gebruiker nodig | Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **geen toegang** met het object **door geven naar** onderliggend item toe aan de onderliggende objecten (vSphere-hosts, gegevens opslag, virtuele machines en netwerken).|
|**Replicatie/failover** | U hebt mini maal een alleen-lezen gebruiker nodig| Datacentrumobject –> doorgeven naar onderliggend object, rol=Alleen-lezen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **geen toegang** met het object **door geven naar** onderliggend item toe aan de onderliggende objecten (vSphere-hosts, gegevens opslag, virtuele machines en netwerken).<br/><br/> Nuttig voor migratie doeleinden, maar niet de volledige replicatie, failover, failback.|
|**Replicatie/failover/failback** | U wordt aangeraden een rol (AzureSiteRecoveryRole) met de vereiste machtigingen te maken en vervolgens de rol toe te wijzen aan een VMware-gebruiker of-groep | Data Center-object-> door geven naar onderliggend object, Role = AzureSiteRecoveryRole<br/><br/> Gegevensopslag –> Ruimte toewijzen, browsen in de gegevensopslag, bestandsbewerkingen op laag niveau, bestand verwijderen, bestanden van virtuele machines bijwerken<br/><br/> Netwerk –> Netwerk toewijzen<br/><br/> Resource –> VM toewijzen aan resourcegroep, uitgeschakelde VM migreren, ingeschakelde VM migreren<br/><br/> Taken –> Taak maken, taak bijwerken<br/><br/> Virtuele machine –> Configuratie<br/><br/> Virtuele machine –> Interactie –> vraag beantwoorden, apparaatverbinding, CD's configureren, diskettes configureren, uitschakelen, inschakelen, VMware-hulpprogramma's installeren<br/><br/> Virtuele machine -> Inventaris –> Maken, registreren, registratie opheffen<br/><br/> Virtuele machine –> Inrichten –> Downloaden van virtuele machine toestaan, toestaan dat bestanden van virtuele machines worden geüpload<br/><br/> Virtuele machine –> Momentopnamen -> Momentopnamen verwijderen | Gebruiker wordt toegewezen op datacentrumniveau, en heeft toegang tot alle objecten in het datacentrum.<br/><br/> Als u de toegang wilt beperken, wijst u de rol **geen toegang** met het object **door geven naar** onderliggend item toe aan de onderliggende objecten (vSphere-hosts, gegevens opslag, virtuele machines en netwerken).|


## <a name="add-vmware-server-to-the-vault"></a>VMware-Server toevoegen aan de kluis

1. Open in de Azure Portal uw kluis > **site Recovery infra structuur** > **configuratie servers**en open de configuratie server.
2. Klik op de pagina **Details** op **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Referenties wijzigen

Wijzig de referenties die worden gebruikt om als volgt verbinding te maken met de vCenter-Server of de ESXi-host:

1. Meld u aan bij de configuratie server en start cspsconfigtool. exe op het bureau blad.
2. Klik op het tabblad **account beheren** op **account toevoegen** .

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Geef de nieuwe account gegevens op en klik op **OK** om deze toe te voegen. Het account moet beschikken over de bevoegdheden die [hierboven](#account-permissions)worden vermeld.
4. Open op het Azure Portal de kluis >**configuratie**servers voor **site Recovery infra structuur** > en open de configuratie server.
5. Klik op de pagina **Details** op **server vernieuwen**.
6. Nadat de taak server vernieuwen is voltooid, selecteert u de vCenter Server om de overzichts pagina van vCenter te openen.
7. Selecteer het zojuist toegevoegde account in het veld **vCenter-Server-vSphere** en klik op **Opslaan**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Een vCenter-Server verwijderen

1. Open in de Azure Portal uw kluis > **site Recovery infra structuur** > **configuratie servers**en open de configuratie server.
2. Selecteer op de pagina **Details** de vCenter-Server.
3. Klik op de knop **verwijderen** .

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Het IP-adres en de poort van vCenter wijzigen

1. Meld u aan bij Azure Portal.
2. Navigeer naar **Recovery Services kluis** > **site Recovery infrastructuur** > **configuratie servers**.
3. Klik op de configuratie server waaraan de vCenter is toegewezen.
4. Klik in de sectie **vCenter-servers** op de vCenter die u wilt wijzigen.
5. Werk op de overzichts pagina vCenter het IP-adres en de poort van de vCenter in de desbetreffende velden bij en sla de wijzigingen op.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Als u de wijzigingen van kracht wilt laten worden, wacht u 15 minuten of vernieuwt u [de configuratie server](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Alle beveiligde virtuele machines migreren naar een nieuwe vCenter

Als u alle virtuele machines naar de nieuwe vCenter wilt migreren, moet u geen ander vCenter-account toevoegen. Dit kan leiden tot dubbele vermeldingen. U hoeft alleen het IP-adres van de nieuwe vCenter bij te werken:

1. Meld u aan bij Azure Portal.
2. Navigeer naar **Recovery Services kluis** > **site Recovery infrastructuur** > **configuratie servers**.
3. Klik op de configuratie server waaraan de oude vCenter is toegewezen.
4. Klik in de sectie **vCenter-servers** op de vCenter waarvan u de migratie wilt plannen.
5. Werk op de overzichts pagina van vCenter het IP-adres van de nieuwe vCenter in het veld **vCenter-Server/vSphere hostname of IP-adres**bij. Sla uw wijzigingen op.

Zodra het IP-adres is bijgewerkt, worden Site Recovery-onderdelen detectie gegevens van virtuele machines ontvangen van de nieuwe vCenter. Dit heeft geen invloed op de voortdurende replicatie activiteiten.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Enkele beveiligde virtuele machines migreren naar een nieuwe vCenter

> [!NOTE]
> Deze sectie is alleen van toepassing wanneer u een aantal van uw beveiligde virtuele machines migreert naar een nieuwe vCenter. Als u een nieuwe set virtuele machines vanuit een nieuwe vCenter wilt beveiligen, voegt u [nieuwe vCenter-gegevens toe aan de configuratie server](#add-vmware-server-to-the-vault) en begint u met **[beveiliging inschakelen](vmware-azure-tutorial.md#enable-replication)** .

Een paar virtuele machines verplaatsen naar een nieuwe vCenter:

1. [Voeg de nieuwe vCenter-gegevens toe aan de configuratie server](#add-vmware-server-to-the-vault).
2. [Schakel de replicatie uit van de virtuele machines](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) die u wilt migreren.
3. Voltooi de migratie van de geselecteerde virtuele machines naar de nieuwe vCenter.
4. Beveilig nu gemigreerde virtuele machines door [de nieuwe vCenter te selecteren wanneer u beveiliging](vmware-azure-tutorial.md#enable-replication)inschakelt.

> [!TIP]
> Als het aantal virtuele machines dat wordt gemigreerd **groter** is dan het aantal virtuele machines dat in de oude vCenter wordt bewaard, werkt u het IP-adres van de nieuwe vCenter bij met behulp van de instructies die hier worden gegeven. Voor de paar virtuele machines die in de oude vCenter worden bewaard, [schakelt u replicatie uit](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure). [Voeg nieuwe vCenter-gegevens toe aan de configuratie server](#add-vmware-server-to-the-vault)en start de **[beveiliging inschakelen](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

1. Als de beveiligde virtuele machines van de ene ESXi-host naar een andere worden verplaatst, heeft dit invloed op de replicatie?

    Nee, dit heeft geen invloed op de doorlopende replicatie. [Zorg er echter voor dat u de hoofddoel server met voldoende bevoegdheden implementeert](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Wat zijn de poort nummers die worden gebruikt voor communicatie tussen vCenter en andere Site Recovery-onderdelen?

    De standaard poort is 443. De configuratie server heeft via deze poort toegang tot de vCenter-vSphere. Als u deze informatie wilt bijwerken, klikt u [hier](#modify-the-vcenter-ip-address-and-port).

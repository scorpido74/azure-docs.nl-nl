---
title: Een azure-herstelkluis verwijderen
description: Meer informatie over het verwijderen van een kluis van Recovery Services die is geconfigureerd voor Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: rajanaki
ms.openlocfilehash: 0e409ffdedbac822aedf48833f2dd85f8e04afa2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894979"
---
# <a name="delete-a-site-recovery-services-vault"></a>Een Site Recovery Services-kluis verwijderen

In dit artikel wordt beschreven hoe u een kluis van Herstelservices voor siteherstel verwijdert. Zie [Een kluis voor back-up verwijderen in Azure](../backup/backup-azure-delete-vault.md)als u een kluis wilt verwijderen die is gebruikt in Azure Backup.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>Voordat u begint

Voordat u een kluis verwijderen, moet u geregistreerde servers en items in de kluis verwijderen. Wat u moet verwijderen, is afhankelijk van de replicatiescenario's die u hebt geïmplementeerd. 


## <a name="delete-a-vault-azure-vm-to-azure"></a>Een vault-Azure VM verwijderen naar Azure

1. Volg [deze instructies](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure) om alle beveiligde VM's te verwijderen.
2. Verwijder vervolgens de kluis.

## <a name="delete-a-vault-vmware-vm-to-azure"></a>Een vault-VMware VM verwijderen naar Azure

1. Volg [deze instructies](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) om alle beveiligde VM's te verwijderen.
2. Volg [deze stappen](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) om alle replicatiebeleidsregels te verwijderen.
3. Verwijder verwijzingen naar vCenter met behulp van [deze stappen](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).
4. Volg [deze instructies](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server) om een configuratieserver uit te stellen.
5. Verwijder vervolgens de kluis.


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>Een vault-Hyper-V VM (met VMM) verwijderen naar Azure

1. Volg [deze stappen](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario) om Hyper-V VM's te verwijderen die worden beheerd door System Center VMM.
2. Alle replicatiebeleidsregels loskoppelen en verwijderen. Doe dit in uw kluis > **Site Recovery Infrastructure** > **For System Center VMM-replicatiebeleid** > **Replication Policies**.
3. Volg [deze stappen](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) om het registreren van een verbonden VMM-server uit te schrijven.
4. Verwijder vervolgens de kluis.

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>Een vault-Hyper-V VM verwijderen naar Azure

1. Volg [deze stappen](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) om alle beveiligde VM's te verwijderen.
2. Alle replicatiebeleidsregels loskoppelen en verwijderen. Doe dit in uw kluis > **Site Recovery Infrastructure** > **For Hyper-V Sites** > **Replicatie beleid**.
3. Volg [deze instructies](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site) om een Hyper-V-host uit te schrijven.
4. Verwijder de Hyper-V-site.
5. Verwijder vervolgens de kluis.


## <a name="use-powershell-to-force-delete-the-vault"></a>PowerShell gebruiken om de kluis te verwijderen 

> [!Important]
> Als u het product test en u zich geen zorgen maakt over gegevensverlies, gebruikt u de methode voor het verwijderen van de kracht om de kluis en alle afhankelijkheden snel te verwijderen.
> Met de opdracht PowerShell wordt alle inhoud van de kluis verwijderd en is **deze niet omkeerbaar.**

Als u de kluis Siteherstel wilt verwijderen, zelfs als er beveiligde items zijn, gebruikt u de volgende opdrachten:

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

Meer informatie over [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)en [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault).

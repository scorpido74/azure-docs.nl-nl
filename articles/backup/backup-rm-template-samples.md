---
title: Azure Resource Manager-sjablonen
description: Azure Resource Manager-sjablonen voor gebruik met Vaults van Recovery Services en Azure Backup-functies
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 3c05f345c6c5df5d0350c75e52381c24b0609501
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74172170"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-sjablonen voor Azure Backup

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor gebruik bij Recovery Services-kluizen en Azure Backup-functies. Zie [Microsoft.RecoveryServices resource types](/azure/templates/microsoft.recoveryservices/allversions) (Microsoft.RecoveryServices resourcetypen) voor informatie over de JSON-syntaxis en eigenschappen.

|   |   |
|---|---|
|**Recovery Services-kluis** | |
| [Een kluis van Recovery Services maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Maak een Recovery Services-kluis. De kluis kan worden gebruikt voor Azure Backup en Azure Site Recovery. |
|**Back-up maken van virtuele machines**| |
| [Back-ups maken van Resource Manager-VM's](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Gebruik de bestaande Recovery Services-kluis en het back-upbeleid om back-ups te maken van Resource Manager-virtuele machines in dezelfde resourcegroep.|
| [Back-ups maken van IaaS-VM's naar een Recovery Services-kluis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Sjabloon voor het maken van back-ups van klassieke en Resource Manager-virtuele machines. |
| [Beleid voor wekelijkse back-up voor IaaS VM's maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Sjabloon maakt Recovery Services kluis en een wekelijks back-up beleid, dat wordt gebruikt om een back-up klassieke en Resource Manager-virtuele machines.|
| [Beleid voor dagelijkse back-up voor IaaS VM's maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Sjabloon maakt Recovery Services kluis en een dagelijkse back-up beleid, dat wordt gebruikt om een back-up klassieke en Resource Manager-virtuele machines.|
| [Windows Server-VM met back-up implementeren](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Met de sjabloon wordt een Windows Server-VM en Recovery Services-kluis gemaakt met het standaard back-upbeleid ingeschakeld.|
|**Back-uptaken controleren** |  |
| [Logboeken van Azure Monitor gebruiken met Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Template implementeert Azure Monitor-logboeken met Azure Backup, waarmee u back-uptaken, back-upwaarschuwingen en de cloudopslag die wordt gebruikt in de vaults van uw Herstelservices, controleren.|  
|**Back-ups maken van SQL Server in Azure VM** |  |
| [Back-ups maken van SQL Server in Azure VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Sjabloon maakt een Vault en Workload specific Backup Policy Recovery Services. Het registreert de VM met Azure Backup-service en configureert beveiliging op die VM. Momenteel werkt het alleen voor SQL Gallery-afbeeldingen. |
|   |   |

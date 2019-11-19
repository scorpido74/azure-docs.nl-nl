---
title: Azure Resource Manager-sjablonen
description: Azure Resource Manager sjablonen voor gebruik met Recovery Services kluizen en Azure Backup functies
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 3c05f345c6c5df5d0350c75e52381c24b0609501
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172170"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-sjablonen voor Azure Backup

De volgende tabel bevat koppelingen naar Azure Resource Manager-sjablonen voor gebruik bij Recovery Services-kluizen en Azure Backup-functies. Zie [Microsoft.RecoveryServices resource types](/azure/templates/microsoft.recoveryservices/allversions) (Microsoft.RecoveryServices resourcetypen) voor informatie over de JSON-syntaxis en eigenschappen.

|   |   |
|---|---|
|**Recovery Services-kluis** | |
| [Een Recovery Services-kluis maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Maak een Recovery Services-kluis. De kluis kan worden gebruikt voor Azure Backup en Azure Site Recovery. |
|**Back-up maken van virtuele machines**| |
| [Back-ups maken van Resource Manager-VM's](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Gebruik de bestaande Recovery Services-kluis en het back-upbeleid om back-ups te maken van Resource Manager-virtuele machines in dezelfde resourcegroep.|
| [Back-ups maken van IaaS-VM's naar een Recovery Services-kluis](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Sjabloon voor het maken van back-ups van klassieke en Resource Manager-virtuele machines. |
| [Beleid voor wekelijkse back-up voor IaaS VM's maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Sjabloon maakt Recovery Services kluis en een wekelijks back-upbeleid dat wordt gebruikt voor het maken van back-ups van klassieke en Resource Manager-virtuele machines.|
| [Beleid voor dagelijkse back-up voor IaaS VM's maken](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Sjabloon maakt Recovery Services kluis en een dagelijks back-upbeleid dat wordt gebruikt voor het maken van back-ups van klassieke en Resource Manager-virtuele machines.|
| [Windows Server-VM met back-up implementeren](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Met de sjabloon wordt een Windows Server-VM en Recovery Services-kluis gemaakt met het standaard back-upbeleid ingeschakeld.|
|**Back-uptaken controleren** |  |
| [Logboeken van Azure Monitor gebruiken met Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Met de sjabloon worden Azure Monitor-logboeken geïmplementeerd met Azure Backup, waarmee u back-up-en herstel taken, back-upwaarschuwingen en de Cloud opslag kunt bewaken die worden gebruikt in uw Recovery Services-kluizen.|  
|**Back-up maken van SQL Server in azure VM** |  |
| [Back-up maken van SQL Server in azure VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | Met de sjabloon maakt u een Recovery Services kluis en specifiek back-upbeleid voor werk belastingen. De virtuele machine wordt geregistreerd bij Azure Backup-service en de beveiliging wordt geconfigureerd op die VM. Op dit moment werkt dit alleen voor installatie kopieën van SQL-galerie. |
|   |   |

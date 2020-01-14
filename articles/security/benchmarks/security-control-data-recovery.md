---
title: Azure-beveiligings beheer-gegevens herstel
description: Herstel van gegevens van beveiligings beheer
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: c585ebd903d4070f6247456e06efffbc6ec45270
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934493"
---
# <a name="security-control-data-recovery"></a>Beveiligings beheer: gegevens herstellen

Zorg ervoor dat er regel matig een back-up wordt gemaakt van alle systeem gegevens, configuraties en geheimen.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 9,1 | 10.1 | Klant |

Schakel Azure Backup in en configureer de back-upbron (Azure Vm's, SQL Server of bestands shares), evenals de gewenste frequentie en bewaar periode.

Azure Backup inschakelen:

https://docs.microsoft.com/azure/backup/

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 9.2 | 10.2 | Klant |

Schakel Azure Backup en doel-VM ('s) in, evenals de gewenste frequentie en retentie perioden. Back-ups van door de klant beheerde sleutels binnen Azure Key Vault.

Azure Backup inschakelen:

https://docs.microsoft.com/azure/backup/

Hoe kan ik een back-up maken van sleutel kluis sleutels in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 9,3 | 10,3 | Klant |

Zorg ervoor dat het gegevens herstel van inhoud in Azure Backup regel matig wordt uitgevoerd. Test, indien nodig, de herstel bewerking naar een geïsoleerd VLAN. Herstel van back-ups van door de klant beheerde sleutels testen.

Bestanden herstellen vanuit back-up van Azure virtual machine:

https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm

Sleutel kluis sleutels herstellen in Azure:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

| Azure-ID | CIS-Id's | Verantwoordelijkheid |
|--|--|--|
| 9.4 | 10,4 | Klant |

Voor on-premises back-ups wordt versleuteling in rust geboden met de wachtwoordzin die u opgeeft wanneer u een back-up maakt in Azure. Voot Azure-VM’s worden gegevens in rust versleuteld met behulp van SSE (Storage Service Encryption). U kunt zacht verwijderen inschakelen in Key Vault om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.

Het inschakelen van zacht verwijderen in Key Vault:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## <a name="next-steps"></a>Volgende stappen

Zie het volgende beveiligings beheer: [respons op incidenten](security-control-incident-response.md)

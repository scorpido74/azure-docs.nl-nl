---
title: Azure-beveiligings beheer-gegevens herstel
description: Herstel van gegevens van Azure-beveiligings beheer
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 4f3e8540902809f951a441aa2fe8d00026c44d82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408600"
---
# <a name="security-control-data-recovery"></a>Beveiligings beheer: gegevens herstellen

Zorg ervoor dat er regel matig een back-up wordt gemaakt van alle systeem gegevens, configuraties en geheimen.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1: controleren op regel matige automatische back-ups

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 9,1 | 10.1 | Klant |

Schakel Azure Backup in en configureer de back-upbron (Azure Vm's, SQL Server of bestands shares), evenals de gewenste frequentie en bewaar periode.

- [Azure Backup inschakelen](https://docs.microsoft.com/azure/backup/)

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: volledige back-ups van het systeem uitvoeren en een back-up maken van een door de klant beheerde sleutels

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 9,2 | 10.2 | Klant |

Schakel Azure Backup en doel-VM ('s) in, evenals de gewenste frequentie en retentie perioden. Back-ups van door de klant beheerde sleutels binnen Azure Key Vault.

- [Azure Backup inschakelen](https://docs.microsoft.com/azure/backup/)

- [Hoe kan ik een back-up maken van sleutel kluis sleutels in azure?](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: alle back-ups valideren, inclusief door de klant beheerde sleutels

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 9,3 | 10,3 | Klant |

Zorg ervoor dat het gegevens herstel van inhoud in Azure Backup regel matig wordt uitgevoerd. Herstel van back-ups van door de klant beheerde sleutels testen.

- [Bestanden herstellen vanuit back-up van Azure virtual machine](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

- [Sleutel kluis sleutels herstellen in azure](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: zorg voor de bescherming van back-ups en door de klant beheerde sleutels

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 9.4 | 10,4 | Klant |

Voor on-premises back-ups wordt versleuteling in rust geboden met de wachtwoordzin die u opgeeft wanneer u een back-up maakt in Azure. Voot Azure-VM’s worden gegevens in rust versleuteld met behulp van SSE (Storage Service Encryption). Gebruik op rollen gebaseerd toegangs beheer voor het beveiligen van back-ups en door de klant beheerde sleutels.  

Schakel de beveiliging voor voorlopig verwijderen en opschonen in Key Vault in om sleutels te beschermen tegen onbedoelde of schadelijke verwijdering.  Als Azure Storage wordt gebruikt voor het opslaan van back-ups, schakelt u de optie voor het tijdelijk verwijderen in om uw gegevens op te slaan en te herstellen wanneer blobs of BLOB-moment opnamen worden verwijderd. 

- [Meer informatie over Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

- [Het inschakelen van de functie voor voorlopig verwijderen en het opschonen van beveiliging in Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Voorlopig verwijderen voor Azure Storage-blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)


## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer: [respons op incidenten](security-control-incident-response.md)
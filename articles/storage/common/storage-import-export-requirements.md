---
title: Vereisten voor de Azure Import/Export service | Microsoft Docs
description: Informatie over de hardware- en softwarevereisten voor de Azure Import/Export-service.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978476"
---
# <a name="azure-importexport-system-requirements"></a>Azure Import/Export-systeemvereisten

Dit artikel beschrijft de belangrijke vereisten voor uw Azure Import/Export-service. Het is raadzaam dat u de informatie voordat u de Import/Export-service gebruiken en vervolgens terug naar deze zo nodig tijdens de bewerking verwijzen zorgvuldig te controleren.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Voor het voorbereiden van de harde schijven met het hulpprogramma WAImportExport van de volgende **64-bits besturingssysteem die ondersteuning bieden voor BitLocker-stationsversleuteling** worden ondersteund.


|Platform |Versie |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> WindowsServer 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Andere vereiste software voor Windows-client

|Platform |Versie |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

Azure Import/Export-service ondersteunt de volgende typen opslagaccounts:

- Standard Algemeen v2-opslag accounts (aanbevolen voor de meeste scenario's)
- Blob Storage-accounts
- Algemeen v1-opslag accounts (klassiek of Azure Resource Manager implementaties),

Zie voor meer informatie over de storage-accounts, [overzicht van Azure storage-accounts](storage-account-overview.md).

Elke taak kan worden gebruikt voor het overbrengen van gegevens naar of van slechts één opslagaccount. Een taak één import/export kan niet met andere woorden, omvatten voor meerdere opslagaccounts. Zie voor meer informatie over het maken van een nieuw opslagaccount [over het maken van een Storage-Account](storage-account-create.md).

> [!IMPORTANT]
> De Azure Import Export-service biedt geen ondersteuning voor storage-accounts waarbij de [Virtual Network-Service-eindpunten](../../virtual-network/virtual-network-service-endpoints-overview.md) functie is ingeschakeld. 

## <a name="supported-storage-types"></a>Ondersteunde opslagtypen

De volgende lijst met opslagtypen wordt ondersteund met Azure Import/Export-service.


|Taak  |Storage-Service |Ondersteund  |Niet ondersteund  |
|---------|---------|---------|---------|
|Importeren     |  Azure Blob Storage <br><br> Azure File storage       | Blok-Blobs en pagina-blobs ondersteund <br><br> Ondersteunde bestanden          |
|Exporteren     |   Azure Blob Storage       | Blok-blobs, pagina-blobs en toevoeg-blobs ondersteund         | Azure Files niet ondersteund


## <a name="supported-hardware"></a>Ondersteunde hardware

Voor de Azure Import/Export-service moet u ondersteunde schijven om gegevens te kopiëren.

### <a name="supported-disks"></a>Ondersteunde schijven

De volgende lijst van schijven wordt ondersteund voor gebruik met de Import/Export-service.


|Schijftype  |Grootte  |Ondersteund |
|---------|---------|---------|
|SSD    |   2,5-inch      |SATA III          |
|HDD     |  2,5-inch<br>3,5-inch       |SATA II, SATA III         |

De volgende schijf typen worden niet ondersteund:
- USBs.
- Externe HDD met ingebouwde USB-adapter.
- Schijven die zich in het hoofdletter gebruik van een externe HDD bevinden.

Een enkele import-/ exporttaak kan hebben:
- Maximaal 10 HDD/SSD's.
- Een combinatie van HDD per SSD van elke grootte.

Groot aantal stations kan worden verdeeld over meerdere taken en er is geen beperkingen voor het aantal taken dat kan worden gemaakt. Voor de taken van gegevensimport, wordt alleen de eerste gegevensvolume op het station verwerkt. Het gegevensvolume moet zijn geformatteerd met NTFS.

Bij het voorbereiden van harde schijven en kopiëren van de gegevens met behulp van het hulpprogramma WAImportExport, kunt u externe USB-adapters. Meest gebruiksklare USB 3.0 of hoger adapters kunnen worden gebruikt.


## <a name="next-steps"></a>Volgende stappen

* [Instellen van het hulpprogramma WAImportExport](storage-import-export-tool-how-to.md)
* [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](storage-use-azcopy.md)
* [Voorbeeld van Azure Import Export REST-API](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)

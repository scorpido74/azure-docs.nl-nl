---
title: Vereisten voor azure import/exportservice | Microsoft Documenten
description: Inzicht in de software- en hardwarevereisten voor azure import/exportservice.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 58997b20c01f33037a5e5e149caa59e1630373ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255312"
---
# <a name="azure-importexport-system-requirements"></a>Systeemvereisten voor Azure Import/Export

In dit artikel worden de belangrijke vereisten voor uw Azure Import/Export-service beschreven. We raden u aan de informatie zorgvuldig te bekijken voordat u de import/exportservice gebruikt en deze vervolgens tijdens de bewerking zo nodig terugte verwijzen.

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Om de harde schijven voor te bereiden met het gereedschap WAImportExport, wordt het volgende **64-bits besturingssysteem ondersteund dat BitLocker-stationsversleuteling ondersteunt.**


|Platform |Versie |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Andere vereiste software voor Windows-client

|Platform |Versie |
|---------|---------|
|.NET Framework    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

Azure Import/Export-service ondersteunt de volgende typen opslagaccounts:

- Standaard v2-opslagaccounts voor algemeen gebruik (aanbevolen voor de meeste scenario's)
- Blob Storage-accounts
- V1-opslagaccounts voor algemeen gebruik (zowel Classic- als Azure Resource Manager-implementaties),

Zie overzicht van [Azure-opslagaccounts](storage-account-overview.md)voor meer informatie over opslagaccounts.

Elke taak kan worden gebruikt om gegevens over te dragen van of naar slechts één opslagaccount. Met andere woorden, één import-/exporttaak kan niet overmeerdere opslagaccounts heen. Zie [Een opslagaccount maken](storage-account-create.md)voor informatie over het maken van een nieuw opslagaccount .

> [!IMPORTANT]
> De Azure Import Export-service biedt geen ondersteuning voor opslagaccounts waarbij de functie [Eindpunten voor virtuele netwerkservice](../../virtual-network/virtual-network-service-endpoints-overview.md) is ingeschakeld. 

## <a name="supported-storage-types"></a>Ondersteunde opslagtypen

De volgende lijst met opslagtypen wordt ondersteund met de Azure Import/Export-service.


|Taak  |Opslagservice |Ondersteund  |Niet ondersteund  |
|---------|---------|---------|---------|
|Importeren     |  Azure Blob Storage <br><br> Azure File Storage       | Ondersteunde blobs en paginablobs blokkeren <br><br> Ondersteunde bestanden          |
|Exporteren     |   Azure Blob Storage       | Blobs blokkeren, blobs van pagina's en ondersteuning voor toevoegen         | Azure-bestanden worden niet ondersteund


## <a name="supported-hardware"></a>Ondersteunde hardware

Voor de Azure Import/Export-service hebt u ondersteunde schijven nodig om gegevens te kopiëren.

### <a name="supported-disks"></a>Ondersteunde schijven

De volgende lijst met schijven wordt ondersteund voor gebruik met de service Importeren/exporteren.


|Schijftype  |Grootte  |Ondersteund |
|---------|---------|---------|
|SSD    |   2.5"      |SATA III          |
|HDD     |  2.5"<br>3.5"       |SATA II, SATA III         |

De volgende schijftypen worden niet ondersteund:
- USB's.
- Externe HDD met ingebouwde USB adapter.
- Schijven die zich in de behuizing van een externe HDD bevinden.

Een enkele import/exporttaak kan hebben:
- Maximaal 10 HDD/SSD's.
- Een mix van HDD / SSD van elke grootte.

Een groot aantal stations kan worden verspreid over meerdere taken en er is geen limiet aan het aantal banen dat kan worden gemaakt. Voor importtaken wordt alleen het eerste gegevensvolume op het station verwerkt. Het gegevensvolume moet worden opgemaakt met NTFS.

Bij het voorbereiden van harde schijven en het kopiëren van de gegevens met behulp van de WAImportExport tool, u externe USB-adapters gebruiken. De meeste kant-en-klare USB 3.0 of later adapters moeten werken.


## <a name="next-steps"></a>Volgende stappen

* [Het gereedschap WAImportExport instellen](storage-import-export-tool-how-to.md)
* [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](storage-use-azcopy.md)
* [Voorbeeld van Azure Import Export REST API-voorbeeld](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)

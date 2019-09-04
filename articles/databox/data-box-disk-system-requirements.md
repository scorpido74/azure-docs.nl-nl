---
title: Systeem vereisten voor Microsoft Azure Data Box Disk | Microsoft Docs
description: Meer informatie over de software-en netwerk vereisten voor uw Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: f054d0665c49ee048451681a7cc34d2a648a6e9e
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70240229"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Systeem vereisten voor Azure Data Box Disk

In dit artikel worden de belangrijkste systeem vereisten beschreven voor uw Microsoft Azure Data Box Disk oplossing en voor de clients die verbinding maken met de Data Box Disk. We raden u aan de informatie zorgvuldig te bekijken voordat u uw Data Box Disk implementeert en vervolgens naar de gewenste gegevens te verwijzen tijdens de implementatie en de volgende bewerking.

De systeem vereisten omvatten de ondersteunde platforms voor clients die verbinding maken met schijven, ondersteunde opslag accounts en opslag typen.

::: zone-end

::: zone target="chromeless"

# <a name="review-prerequisites"></a>Vereisten controleren

1. U moet uw data Box Disk hebben besteld [met behulp van de zelf studie: Bestel uw Azure Data Box Disk](data-box-disk-deploy-ordered.md). U hebt uw schijven en één verbindings kabel per schijf ontvangen.
2. U hebt een clientcomputer beschikbaar van waaruit u de gegevens kunt kopiëren. De clientcomputer moet voldoen aan deze vereisten:

    - Een ondersteund besturings systeem uitvoeren.
    - Andere vereiste software is geïnstalleerd.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Ondersteunde besturings systemen voor clients

Hier volgt een lijst met de ondersteunde besturings systemen voor het ontgrendelen van de schijf en het kopiëren van gegevens via de clients die zijn verbonden met de Data Box Disk.

| **Besturingssysteem** | **Geteste versies** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 bits) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04, 16.04, 18.04 <br> 8,11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Andere vereiste software voor Windows-clients

Voor Windows-clients moet de volgende ook worden geïnstalleerd.

| **Software**| **Versie** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET Framework |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Andere vereiste software voor Linux-clients

Voor Linux-client installeert de Data Box Disk toolset de volgende vereiste software:

- vergren deling
- OpenSSL

::: zone target="docs"

## <a name="supported-connection"></a>Ondersteunde verbinding

De client computer met de gegevens moet een USB 3,0-poort of hoger hebben. De schijven maken verbinding met deze client met behulp van de meegeleverde kabel.

## <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

Hier volgt een lijst met de ondersteunde opslag typen voor de Data Box Disk.

| **Opslagaccount** | **Opmerkingen** |
| --- | --- |
| Klassiek | Standard |
| Algemeen doel  |Standaard zowel v1 als v2 worden ondersteund. Zowel warme als coole lagen worden ondersteund. |
| Blob-opslag account | |

>[!NOTE]
> Azure Data Lake Storage gen 2-accounts worden niet ondersteund.


## <a name="supported-storage-types-for-upload"></a>Ondersteunde opslag typen voor uploaden

Hier volgt een lijst met de opslag typen die worden ondersteund voor geüpload naar Azure met behulp van Data Box Disk.

| **Bestands indeling** | **Opmerkingen** |
| --- | --- |
| Azure-blok-BLOB | |
| Azure-pagina-BLOB  | |
| Azure Files  | |
| Beheerde schijven | |


## <a name="next-step"></a>Volgende stap

* [Uw Azure Data Box Disk implementeren](data-box-disk-deploy-ordered.md)

::: zone-end


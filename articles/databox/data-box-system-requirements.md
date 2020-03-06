---
title: Systeem vereisten voor Microsoft Azure Data Box | Microsoft Docs
description: Meer informatie over de software- en netwerkvereisten voor de Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380583"
---
# <a name="azure-data-box-system-requirements"></a>Systeem vereisten voor Azure Data Box

In dit artikel worden de belangrijkste systeem vereisten voor uw Microsoft Azure Data Box en voor de clients die verbinding maken met de Data Box beschreven. We raden u aan de informatie zorgvuldig te bekijken voordat u uw Data Box implementeert en vervolgens naar de gewenste gegevens te verwijzen tijdens de implementatie en de volgende bewerking.

De systeem vereisten zijn onder andere:

* **Software vereisten voor hosts die verbinding maken met data Box** : beschrijft de ondersteunde platforms, browsers voor de lokale webgebruikersinterface, SMB-clients en eventuele aanvullende vereisten voor hosts die verbinding kunnen maken met de data box.
* **Netwerk vereisten voor de data Box** : bevat informatie over de netwerk vereisten voor de optimale werking van de data box.


## <a name="software-requirements"></a>Softwarevereisten

De software vereisten omvatten de informatie over de ondersteunde besturings systemen, ondersteunde browsers voor de lokale web-UI en SMB-clients.

### <a name="supported-operating-systems-for-clients"></a>Ondersteunde besturingssystemen voor clients

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Ondersteunde bestands systemen voor Linux-clients

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Ondersteunde opslagtypen

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Ondersteunde webbrowsers

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Netwerk vereisten

Uw datacenter moet een netwerk met hoge snelheid hebben. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevens koppeling worden gebruikt voor het kopiëren van gegevens, maar de Kopieer snelheden worden beïnvloed.

### <a name="port-requirements"></a>Poort vereisten

De volgende tabel geeft een lijst van de poorten die in uw firewall moeten worden geopend om SMB-of NFS-verkeer toe te staan. In deze tabel verwijst *naar* of *binnenkomend* naar de richting waarin de inkomende client toegang tot uw apparaat vraagt. *Out* of *uitgaand* verwijst naar de richting waarin uw data Box apparaat gegevens extern verzendt, naast de implementatie: bijvoorbeeld uitgaand naar Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Volgende stappen

* [Uw Azure Data Box implementeren](data-box-deploy-ordered.md)

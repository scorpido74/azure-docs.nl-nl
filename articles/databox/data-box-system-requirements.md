---
title: Systeemvereisten voor Microsoft Azure Data Box| Microsoft Documenten
description: Meer informatie over de software- en netwerkvereisten voor de Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259953"
---
# <a name="azure-data-box-system-requirements"></a>Systeemvereisten voor Azure Data Box

In dit artikel worden de belangrijke systeemvereisten beschreven voor uw Microsoft Azure Data Box en voor de clients die verbinding maken met het gegevensvak. We raden u aan de informatie zorgvuldig te controleren voordat u uw gegevensvak implementeert en deze vervolgens terug te verwijzen als dat nodig is tijdens de implementatie en de daaropvolgende bewerking.

De systeemvereisten omvatten:

* **Softwarevereisten voor hosts die verbinding maken met Data Box** - beschrijft de ondersteunde platforms, browsers voor de lokale web-gebruikersinterface, SMB-clients en eventuele aanvullende vereisten voor hosts die verbinding kunnen maken met het gegevensvak.
* **Netwerkvereisten voor de Data Box** - geeft informatie over de netwerkvereisten voor een optimale werking van de Data Box.


## <a name="software-requirements"></a>Softwarevereisten

De softwarevereisten omvatten de informatie over de ondersteunde besturingssystemen, ondersteunde browsers voor de lokale web-gebruikersinterface en SMB-clients.

### <a name="supported-operating-systems-for-clients"></a>Ondersteunde besturingssystemen voor clients

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Ondersteunde bestandssystemen voor Linux-clients

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Ondersteunde opslagaccounts

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Ondersteunde opslagtypen

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Ondersteunde webbrowsers

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Netwerkvereisten

Uw datacenter moet een netwerk met hoge snelheid hebben. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een gegevenskoppeling van 1 GbE worden gebruikt om gegevens te kopiëren, maar de kopieersnelheden worden beïnvloed.

### <a name="port-requirements"></a>Poortvereisten

In de volgende tabel worden de poorten weergegeven die in uw firewall moeten worden geopend om SMB- of NFS-verkeer mogelijk te maken. In deze tabel verwijst *in* of *inbound* naar de richting van waaruit binnenkomende client toegang tot uw apparaat vraagt. *Out-* of *outbound* verwijst naar de richting waarin uw Data Box-apparaat gegevens extern verzendt, buiten de implementatie: bijvoorbeeld uitgaand naar het internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Volgende stappen

* [Uw Azure-gegevensvak implementeren](data-box-deploy-ordered.md)

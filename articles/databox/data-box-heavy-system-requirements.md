---
title: Microsoft Azure Data Box Zwaar-systeemvereisten| Microsoft Documenten
description: Meer informatie over de software- en netwerkvereisten voor uw Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260070"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Zware systeemvereisten

In dit artikel worden de belangrijke systeemvereisten beschreven voor uw Azure Data Box Heavy-apparaat en voor de clients die verbinding maken met het apparaat. We raden u aan de informatie zorgvuldig te controleren voordat u uw Data Box Heavy implementeert en deze vervolgens terug te verwijzen als dat nodig is tijdens de implementatie en de daaropvolgende bewerking.

De systeemvereisten omvatten:

* **Softwarevereisten voor hosts die verbinding maken met Data Box Heavy** - beschrijft de ondersteunde platforms, browsers voor de lokale web-gebruikersinterface, SMB-clients en eventuele aanvullende vereisten voor hosts die verbinding kunnen maken met het gegevensvak.
* **Netwerkvereisten voor de Data Box Heavy** - geeft informatie over de netwerkvereisten voor de optimale werking van het Data Box Heavy-apparaat.

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

Uw datacenter moet een netwerk met hoge snelheid hebben. Voor de snelste kopieersnelheden kunnen twee 40 GbE-verbindingen parallel worden gebruikt (één per knooppunt). Als u geen 40 GbE beschikbaar hebt, raden we u aan ten minste twee 10-GbE-aansluitingen (één per knooppunt) te hebben.

### <a name="port-requirements"></a>Poortvereisten

In de volgende tabel worden de poorten weergegeven die in uw firewall moeten worden geopend om SMB- of NFS-verkeer mogelijk te maken. In deze tabel verwijst *in* of *inbound* naar de richting van waaruit binnenkomende client toegang tot uw apparaat vraagt. *Out-* of *outbound* verwijst naar de richting waarin uw Data Box Heavy-apparaat gegevens extern verzendt, buiten de implementatie: bijvoorbeeld uitgaand naar het internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Volgende stappen

* [Uw Azure-gegevensvak implementeren](data-box-deploy-ordered.md)

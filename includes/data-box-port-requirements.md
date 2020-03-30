---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67839740"
---
| Poort nr.| In of uit | Poortbereik| Vereist| Opmerkingen |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|In|LAN|Ja|Deze poort wordt gebruikt om verbinding te maken met API's voor gegevensboxblogopslag REST i.s. over HTTP. Als u geen verbinding maakt met REST API's, wordt deze automatisch omgeleid naar de lokale webgebruikersinterface over 8443. |
| TCP 443 (HTTPS)|In|LAN|Ja|Deze poort wordt gebruikt om verbinding te maken met GEGEVENSbox Blog opslag REST API's via HTTPS. Als u geen verbinding maakt met REST API's, wordt deze automatisch omgeleid naar de lokale webgebruikersinterface over 8443. |
| TCP 8443 (HTTPS-Alt)|In|LAN|Ja|Dit is een alternatieve poort voor HTTPS en wordt gebruikt bij het maken van verbinding met lokale web-gebruikersinterface voor apparaatbeheer. |
| TCP 445 (SMB)|Uit/in|LAN|In sommige gevallen<br>Zie notities|Deze poort is alleen vereist als u verbinding maakt via SMB. |
| TCP 2049 (NFS)|Uit/in|LAN|In sommige gevallen<br>Zie notities|Deze poort is alleen vereist als u verbinding maakt via NFS. |
| TCP 111 (NFS)|Uit/in|LAN|In sommige gevallen<br>Zie notities|Deze poort wordt gebruikt voor rpcbind/port mapping en alleen vereist als u verbinding maakt via NFS.  |

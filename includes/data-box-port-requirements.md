---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67839740"
---
| Poort nummer| In of uit | Poort bereik| Vereist| Opmerkingen |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|In|LAN|Ja|Deze poort wordt gebruikt om verbinding te maken met Data Box blog opslag REST Api's via HTTP. Als er geen verbinding wordt gemaakt met REST-Api's, wordt deze automatisch omgeleid naar de lokale web-UI via 8443. |
| TCP 443 (HTTPS)|In|LAN|Ja|Deze poort wordt gebruikt om verbinding te maken met Data Box blog opslag REST Api's via HTTPS. Als er geen verbinding wordt gemaakt met REST-Api's, wordt deze automatisch omgeleid naar de lokale web-UI via 8443. |
| TCP 8443 (HTTPS-ALT)|In|LAN|Ja|Dit is een alternatieve poort voor HTTPS en wordt gebruikt om verbinding te maken met een lokale web-UI voor Apparaatbeheer. |
| TCP 445 (SMB)|Uit/in|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u verbinding maakt via SMB. |
| TCP 2049 (NFS)|Uit/in|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u verbinding maakt via NFS. |
| TCP 111 (NFS)|Uit/in|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort wordt gebruikt voor rpcbind/Port Mapping en is alleen vereist als u verbinding maakt via NFS.  |

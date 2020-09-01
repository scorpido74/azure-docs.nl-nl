---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/16/2019
ms.author: alkohli
ms.openlocfilehash: baf18ae0263215e6ff83570557255d06c3117fd4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084042"
---
| Poort nummer| In of uit | Poort bereik| Vereist|   Notities |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Uit|WAN |Nee|De uitgaande poort wordt gebruikt voor toegang tot internet om updates op te halen. <br>De uitgaande webproxy kan door de gebruiker worden geconfigureerd. |
| TCP 443 (HTTPS)|Uit|WAN|Ja|De uitgaande poort wordt gebruikt voor toegang tot gegevens in de Cloud.<br>De uitgaande webproxy kan door de gebruiker worden geconfigureerd.|
| UDP 123 (NTP)|Uit|WAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u een NTP-server op Internet gebruikt.  |   
| UDP 53 (DNS)|Uit|WAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u een DNS-server op Internet gebruikt.<br>U kunt het beste een lokale DNS-server gebruiken. |
| TCP 5985 (WinRM)|Uit/in|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is vereist om verbinding te maken met het apparaat via externe Power shell via HTTP.  |
| UDP 67 (DHCP)|Uit|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u een lokale DHCP-server gebruikt.  |
| TCP 80 (HTTP)|Uit/in|LAN|Ja|Deze poort is de binnenkomende poort voor lokale gebruikers interface op het apparaat voor lokaal beheer. <br>Toegang tot de lokale gebruikers interface via HTTP wordt automatisch omgeleid naar HTTPS.  |
| TCP 443 (HTTPS)|Uit/in|LAN|Ja|Deze poort is de binnenkomende poort voor lokale gebruikers interface op het apparaat voor lokaal beheer. Deze poort wordt ook gebruikt om Azure Resource Manager verbinding te maken met de lokale Api's van het apparaat om Blob-opslag te verbinden via REST-Api's en de Security Token Service (STS) voor verificatie via toegangs-en vernieuwings tokens.|
| TCP 445 (SMB)|In|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u verbinding maakt via SMB. |
| TCP 2049 (NFS)|In|LAN|In sommige gevallen<br>Opmerkingen weer geven|Deze poort is alleen vereist als u verbinding maakt via NFS. |



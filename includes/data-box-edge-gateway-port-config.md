---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176475"
---
| Poort nr.| In of uit | Poortbereik| Vereist|   Opmerkingen |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|Uit|WAN |Nee|Uitgaande poort wordt gebruikt voor internettoegang om updates op te halen. <br>De uitgaande webproxy is configureerbaar voor de gebruiker. |
| TCP 443 (HTTPS)|Uit|WAN|Ja|Uitgaande poort wordt gebruikt voor toegang tot gegevens in de cloud.<br>De uitgaande webproxy is configureerbaar voor de gebruiker.|
| UDP 123 (NTP)|Uit|WAN|In sommige gevallen<br>Zie notities|Deze poort is alleen vereist als u een NTP-server op internet gebruikt.  |   
| UDP 53 (DNS)|Uit|WAN|In sommige gevallen<br>Zie notities|Deze poort is alleen vereist als u een dns-server op internet gebruikt.<br>We raden u aan een lokale DNS-server te gebruiken. |
| TCP 5985 (WinRM)|Uit/in|LAN|In sommige gevallen<br>Zie notities|Deze poort is vereist om verbinding te maken met het apparaat via externe PowerShell via HTTP.  |
| UDP 67 (DHCP)|Uit|LAN|In sommige gevallen<br>Zie notities|Deze poort is alleen vereist als u een lokale DHCP-server gebruikt.  |
| TCP 80 (HTTP)|Uit/in|LAN|Ja|Deze poort is de binnenkomende poort voor lokale gebruikersinterface op het apparaat voor lokaal beheer. <br>Als u de lokale gebruikersinterface via HTTP opent, wordt deze automatisch omgeleid naar HTTPS.  |
| TCP 443 (HTTPS)|Uit/in|LAN|Ja|Deze poort is de binnenkomende poort voor lokale gebruikersinterface op het apparaat voor lokaal beheer. |
| TCP 445 (SMB)|In|LAN|In sommige gevallen<br>Zie notities|Deze poort is alleen vereist als u verbinding maakt via SMB. |
| TCP 2049 (NFS)|In|LAN|In sommige gevallen<br>Zie notities|Deze poort is alleen vereist als u verbinding maakt via NFS. |

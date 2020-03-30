---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175981"
---
In deze stap maakt u een firewallregel om de sondepoort voor het load-balanced endpoint (59999, zoals eerder opgegeven) te openen en een andere regel om de listenerpoort voor beschikbaarheidsgroep te openen. Omdat u het laadpunt voor de belasting hebt gemaakt op de VM's die replica's van de beschikbaarheidsgroep bevatten, moet u de sondepoort en de listenerpoort op de desbetreffende VM's openen.

1. Start **Windows Firewall met geavanceerde beveiliging**op VM's die replica's hosten.

2. Met de rechtermuisknop op **regels voor binnenkomende verbindingen**, en klik vervolgens op **nieuwe regel**.

3. Selecteer op de pagina **Regeltype** de optie **Poort**en klik op **Volgende**.

4. Selecteer **op** de pagina Protocol en poorten **TCP**, typ **59999** in het vak **Specifieke lokale poorten** en klik op **Volgende**.

5. Houd op de **actiepagina** **De geselecteerde verbinding toestaan** en klik vervolgens op **Volgende**.

6. Accepteer **op** de pagina Profiel de standaardinstellingen en klik op **Volgende**.

7. Geef op de pagina **Naam** in het tekstvak **Naam** een regelnaam op, zoals **Altijd op de sondepoort van listener**, en klik vervolgens op **Voltooien**.

8. Herhaal de voorgaande stappen voor de listenerpoort van de beschikbaarheidsgroep (zoals eerder opgegeven in de parameter $EndpointPort van het script) en geef vervolgens een geschikte regelnaam op, zoals **Altijd listenerpoort**.


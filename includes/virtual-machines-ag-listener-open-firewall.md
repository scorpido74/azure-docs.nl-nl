---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175981"
---
In deze stap maakt u een firewall regel voor het openen van de test poort voor het eind punt met gelijke taak verdeling (59999, zoals eerder is opgegeven) en een andere regel om de listener-poort van de beschikbaarheids groep te openen. Omdat u het eind punt met gelijke taak verdeling hebt gemaakt op de Vm's die replica's van de beschikbaarheids groep bevatten, moet u de test poort en de listener-poort op de respectievelijke Vm's openen.

1. Start **Windows Firewall met geavanceerde beveiliging**op vm's die replica's hosten.

2. Met de rechtermuisknop op **regels voor binnenkomende verbindingen**, en klik vervolgens op **nieuwe regel**.

3. Selecteer op de pagina **regel type** **poort**en klik vervolgens op **volgende**.

4. Selecteer op de pagina **protocollen en poorten** **TCP**, typ **59999** in het vak **specifieke lokale poorten** en klik vervolgens op **volgende**.

5. Behoud op de pagina **actie** **de verbinding toestaan** geselecteerd en klik op **volgende**.

6. Accepteer de standaard instellingen op de **profiel** pagina en klik vervolgens op **volgende**.

7. Geef op de pagina **naam** in het tekstvak **naam** een regel naam op, bijvoorbeeld **altijd op listener-test poort**, en klik vervolgens op **volt ooien**.

8. Herhaal de voor gaande stappen voor de listener-poort van de beschikbaarheids groep (zoals eerder is beschreven in de para meter $EndpointPort van het script) en geef vervolgens een geschikte regel naam op, bijvoorbeeld **altijd op listener-poort**.


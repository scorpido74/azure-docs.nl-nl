---
title: 'Azure VMware-oplossingen (AVS): open bare IP-adressen toewijzen'
description: Hierin wordt beschreven hoe u open bare IP-adressen toewijst voor virtuele machines in de cloud omgeving van de AVS
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87133f5efb9f096d3fdb0956aab1caac58b4bd94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024293"
---
# <a name="allocate-public-ip-addresses-for-avs-private-cloud-environment"></a>Open bare IP-adressen voor de cloud omgeving van de AVS toewijzen

Open het tabblad open bare Ip's op de pagina netwerk om open bare IP-adressen toe te wijzen voor virtuele machines in de cloud omgeving van uw AVS.

1. [Open de AVS-Portal](access-cloudsimple-portal.md) en selecteer **netwerk** in het menu aan de zijkant.
2. Selecteer **open bare ip's**.
3. Klik op **nieuw openbaar IP-adres**.

    ![Open bare IPs-pagina](media/public-ips-page.png)

4. Voer een naam in om de IP-adres vermelding te identificeren.
5. Behoud de standaard locatie.
6. Gebruik de schuif regelaar om, indien nodig, de time-out voor inactiviteit te wijzigen.
7. Voer het lokale IP-adres in waarvoor u een openbaar IP-adres wilt toewijzen.
8. Voer een bijbehorende DNS-naam in.
9. Klik op **Submit**

![Open bare IP-adressen toewijzen](media/network-public-ip-allocate.png)

De taak voor het toewijzen van het open bare IP-adres begint. U kunt de status van de taak controleren op de pagina **activiteit > taken** . Wanneer de toewijzing is voltooid, wordt de nieuwe vermelding op de open bare IPs-pagina weer gegeven.

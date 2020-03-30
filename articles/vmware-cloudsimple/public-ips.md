---
title: Azure VMware-oplossing per cloudSimple - Openbare IP-adressen toewijzen
description: Beschrijft hoe u openbare IP-adressen voor virtuele machines toewijst in de Private Cloud-omgeving
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 565c9ad0fbd37b026a1ba555d83b9032d2efcba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024293"
---
# <a name="allocate-public-ip-addresses-for-private-cloud-environment"></a>Openbare IP-adressen toewijzen voor private cloudomgeving

Open het tabblad Openbare IP's op de pagina Netwerk om openbare IP-adressen toe te wijzen voor virtuele machines in uw Private Cloud-omgeving.

1. [Toegang tot de CloudSimple-portal](access-cloudsimple-portal.md) en selecteer **Netwerk** in het zijmenu.
2. Selecteer **openbare IP's**.
3. Klik **op Nieuw openbaar IP**.

    ![Pagina Openbare IP's](media/public-ips-page.png)

4. Voer een naam in om de IP-adresvermelding te identificeren.
5. Houd de standaardlocatie.
6. Gebruik de schuifregelaar om indien nodig de niet-actieve time-out te wijzigen.
7. Voer het lokale IP-adres in waarvoor u een openbaar IP-adres wilt toewijzen.
8. Voer een bijbehorende DNS-naam in.
9. Klik **op Verzenden**.

![Openbare IP's toewijzen](media/network-public-ip-allocate.png)

De taak van het toewijzen van het openbare IP-adres begint. U de status van de taak controleren op de pagina **Activiteit > Taken.** Wanneer de toewijzing is voltooid, wordt de nieuwe vermelding weergegeven op de pagina Openbare IP's.

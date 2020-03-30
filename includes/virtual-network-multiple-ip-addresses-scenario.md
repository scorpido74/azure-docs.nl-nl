---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175957"
---
## <a name="scenario"></a>Scenario
Een VM met één NIC wordt gemaakt en verbonden met een virtueel netwerk. De VM vereist drie verschillende privé-IP-adressen en twee *openbare* IP-adressen. *private* De IP-adressen worden toegewezen aan de volgende IP-configuraties:

* **IPConfig-1:** Wijst een *statisch* privé IP-adres en een *statisch* openbaar IP-adres toe.
* **IPConfig-2:** Wijst een *statisch* privé IP-adres en een *statisch* openbaar IP-adres toe.
* **IPConfig-3:** Wijst een *statisch* privé IP-adres en geen openbaar IP-adres toe.
  
    ![Meerdere IP-adressen](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

De IP-configuraties zijn gekoppeld aan de NIC wanneer de NIC wordt gemaakt en de NIC is gekoppeld aan de VM wanneer de VM wordt gemaakt. De typen IP-adressen die voor het scenario worden gebruikt, zijn ter illustratie. U alle IP-adres- en toewijzingstypen toewijzen die u nodig hebt.

> [!NOTE]
> Hoewel de stappen in dit artikel alle IP-configuraties aan één NIC toewijzen, u ook meerdere IP-configuraties toewijzen aan een NIC in een multi-NIC-VM. Lees het artikel Een vm maken met meerdere NIC's voor meer informatie over het maken van een vm [met meerdere NIC's.](../articles/virtual-machines/windows/multiple-nics.md)
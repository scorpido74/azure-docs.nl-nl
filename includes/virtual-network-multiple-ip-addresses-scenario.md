---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67175957"
---
## <a name="scenario"></a>Scenario
Er wordt een virtuele machine met één NIC gemaakt en verbonden met een virtueel netwerk. De VM vereist drie verschillende *privé* -IP-adressen en twee *open bare* IP-adressen. De IP-adressen worden toegewezen aan de volgende IP-configuraties:

* **Ipconfig-1:** Hiermee wijst u een *statisch* privé-IP-adres en een *statisch* openbaar IP-adres toe.
* **Ipconfig-2:** Hiermee wijst u een *statisch* privé-IP-adres en een *statisch* openbaar IP-adres toe.
* **Ipconfig-3:** Hiermee wijst u een *statisch* privé-IP-adres en geen openbaar IP-adres toe.
  
    ![Meerdere IP-adressen](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

De IP-configuraties zijn gekoppeld aan de NIC wanneer de NIC wordt gemaakt en de NIC wordt gekoppeld aan de virtuele machine wanneer de virtuele machine wordt gemaakt. De typen IP-adressen die worden gebruikt voor het scenario, zijn ter illustratie. U kunt elk gewenst IP-adres en dezelfde toewijzings typen toewijzen.

> [!NOTE]
> Hoewel in de stappen in dit artikel alle IP-configuraties aan één NIC worden toegewezen, kunt u ook meerdere IP-configuraties toewijzen aan elke NIC in een multi-NIC-VM. Lees het artikel [een VM met meerdere Nic's maken](../articles/virtual-machines/windows/multiple-nics.md) voor meer informatie over het maken van een virtuele machine met meerdere nic's.
---
title: bestand opnemen
description: bestand opnemen
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78244986"
---
## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en een subnet.

1. Selecteer in de linkerbovenhoek van het scherm **een resource maken > netwerk > virtueel netwerk** of zoek naar **virtueel netwerk** in het zoekvak.

2. In **virtueel netwerk maken**typt of selecteert u deze informatie op het tabblad **basis beginselen** :

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Project Details**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **nieuwe maken**, Voer in **\<resource-group-name>** en selecteer OK of selecteer een bestaand op **\<resource-group-name>** basis van para meters. |
    | **Exemplaardetails** |                                                                 |
    | Name             | Voer**\<virtual-network-name>**                                    |
    | Regio           | Uitgeschakeld**\<region-name>** |

3. Selecteer het tabblad **IP-adressen** of selecteer de knop **volgende: IP-adressen** aan de onderkant van de pagina.

4. Voer de volgende gegevens in op het tabblad **IP-adressen** :

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adres ruimte | Voer**\<IPv4-address-space>** |

5. Selecteer bij **subnetnaam**het woord **standaard**.

6. Voer in **subnet bewerken**de volgende gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | Voer**\<subnet-name>** |
    | Subnetadresbereik | Voer**\<subnet-address-range>**

7. Selecteer **Opslaan**.

8. Selecteer het tabblad **controleren + maken** of selecteer de knop **beoordeling + maken** .

9. Selecteer **Maken**.
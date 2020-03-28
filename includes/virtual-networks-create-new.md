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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244986"
---
## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en subnet.

1. Selecteer linksboven in het scherm **de** optie Een bron maken > netwerken > virtueel netwerk of zoek naar virtueel **netwerk** in het zoekvak.

2. Voer in **Virtueel netwerk maken**deze informatie in of selecteer deze op het tabblad **Basisbeginselen:**

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **Nieuw maken,**>** \<brongroepnaam **invoeren, selecteer OK of selecteer een bestaande ** \<resourcegroepnaam>** op basis van parameters. |
    | **Instantiedetails** |                                                                 |
    | Name             | Voer ** \<>voor virtuele netwerken in**                                    |
    | Regio           | Regionaam ** \<>selecteren** |

3. Selecteer het tabblad **IP-adressen** of selecteer de knop **Volgende: IP-adressen** onder aan de pagina.

4. Voer op het tabblad **IP-adressen** de tekst in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adresruimte | Voer ** \<>IPv4-adresruimte in** |

5. Selecteer **onder Subnetnaam**het woord **standaard**.

6. Voer in **Subnet Bewerken**deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | Voer ** \<>subnetnaam in** |
    | Subnetadresbereik | Vul ** \<>subnet-adresbereik in**

7. Selecteer **Opslaan**.

8. Selecteer het tabblad **Controleren + maken** of selecteer de knop Controleren + **maken.**

9. Selecteer **Maken**.
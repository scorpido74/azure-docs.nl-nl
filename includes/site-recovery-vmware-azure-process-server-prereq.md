---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 11/28/2019
ms.author: raynew
ms.openlocfilehash: de15e3028cf22cdd03ce29385278fc5e2babaa9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74566249"
---
In dit artikel wordt van het volgende uitgegaan:

1. Er is reeds een **site-naar-site VPN**- of een **Express Route**-verbinding tot stand gebracht tussen uw on-premises netwerk en Azure Virtual Network.
2. Uw gebruikersaccount heeft machtigingen voor het maken van een nieuwe virtuele machine in het Azure-abonnement waar de virtuele machines tijdens een failover zijn geplaatst.
3. Uw abonnement heeft minimaal 8 cores beschikbaar om een nieuwe processserver virtuele machine op te draaien.
4. U hebt de beschikking over de **wachtwoordzin voor de configuratieserver**.

> [!TIP]
> Zorg ervoor dat u verbinding hebt met poort 443 van de configuratieserver (wordt on-premises uitgevoerd) van Azure Virtual Network waar de virtuele machines tijdens een failover zijn geplaatst.

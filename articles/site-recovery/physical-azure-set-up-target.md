---
title: De doelomgeving instellen voor fysieke servers in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u de beoogde Azure-omgeving instelt voor noodherstel van fysieke servers met Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953904"
---
# <a name="prepare-target-vmware-to-azure"></a>Doel voorbereiden (VMware naar Azure)

In dit artikel wordt beschreven hoe u uw Azure-omgeving voorbereidt om fysieke servers (x64) met Windows of Linux te repliceren naar Azure.

## <a name="prerequisites"></a>Vereisten

Het artikel gaat ervan uit:
- U hebt een Recovery Services Vault gemaakt om uw fysieke servers te beschermen. U een Vault voor Herstelservices maken vanuit de [Azure-portal.](https://portal.azure.com "Azure Portal")
- U hebt [uw on-premises omgeving ingesteld](physical-azure-disaster-recovery.md) om fysieke servers te repliceren naar Azure.

## <a name="prepare-target"></a>Doel voorbereiden

Na het voltooien van de **stap 1:Selecteer beveiligingsdoel** en **stap 2:Bron voorbereiden,** wordt u meegenomen naar **stap 3: Doel**

![Doel voorbereiden](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Abonnement:** Selecteer in de vervolgkeuzelijst het abonnement waaraan u uw fysieke servers wilt repliceren.
2. **Implementatiemodel:** Het implementatiemodel selecteren (Klassiek of Resourcebeheer)

Op basis van het gekozen implementatiemodel wordt een validatie uitgevoerd om ervoor te zorgen dat u ten minste één compatibel opslagaccount en virtueel netwerk in het doelabonnement hebt om uw fysieke servers te repliceren en te mislukken.

Zodra de validaties zijn voltooid, klikt u op OK om naar de volgende stap te gaan.

Als u geen compatibel Resource Manager-opslagaccount of virtueel netwerk hebt, u er een maken door boven aan de pagina op de knoppen **+ opslagaccount** of **+ netwerk** te klikken.

## <a name="next-steps"></a>Volgende stappen
[Replicatie-instellingen configureren](vmware-azure-set-up-replication.md).

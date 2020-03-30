---
title: Het Vm-replicatiedoel vMware voorbereiden in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u uw doelAzure-omgeving voorbereidt op VMware VM-replicatie in Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73693165"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>De doelomgeving voorbereiden op noodherstel van Vm's of fysieke servers naar Azure

In dit artikel wordt beschreven hoe u uw doelazure-omgeving voorbereidt om virtuele VMware-machines of fysieke servers te repliceren naar Azure.

## <a name="prerequisites"></a>Vereisten

Het artikel gaat ervan uit:
- U hebt een Recovery Services Vault gemaakt op [Azure-portal](https://portal.azure.com "Azure Portal") om uw bronmachines te beschermen
- U hebt uw on-premises omgeving ingesteld om de virtuele software of [fysieke servers](physical-azure-set-up-source.md) van de bron [VMware](vmware-azure-set-up-source.md) te repliceren naar Azure.

## <a name="prepare-target"></a>Doel voorbereiden

Na het voltooien van stap **1: Selecteer beschermingsdoel** en **stap 2: Bron voorbereiden,** wordt u meegenomen naar **stap 3: Target**

![Doel voorbereiden](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Abonnement:** Selecteer in de vervolgkeuzelijst het abonnement waaraan u uw virtuele machines of fysieke servers wilt repliceren.
2. **Implementatiemodel:** Het implementatiemodel selecteren (Klassiek of Resourcebeheer)

Op basis van het gekozen implementatiemodel wordt een validatie uitgevoerd om ervoor te zorgen dat u ten minste één virtueel netwerk in het doelabonnement hebt om uw virtuele machine of fysieke server te repliceren en te mislukken.

Zodra de validaties zijn voltooid, klikt u op OK om naar de volgende stap te gaan.

Als u geen virtueel netwerk hebt, u er een maken door boven aan de pagina op de knop **+ netwerk** te klikken.

## <a name="next-steps"></a>Volgende stappen
[Replicatie-instellingen configureren](vmware-azure-set-up-replication.md).

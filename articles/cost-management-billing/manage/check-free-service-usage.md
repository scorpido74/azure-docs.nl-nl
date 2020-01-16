---
title: Gebruik van gratis Azure-services controleren en bijhouden
description: Lees hier hoe u het gebruik van gratis services kunt controleren in de Azure-portal.
author: amberbhargava
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 83937e31d844ba0cc8efc17f1ecefa2ad6e7a7c1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992827"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Gebruik van gratis services controleren die deel uitmaken van uw gratis Azure-account

Er worden geen kosten in rekening gebracht voor services die gratis worden geleverd bij uw gratis Azure-account, tenzij u de limieten van de services overschrijdt. Als u binnen de limiet wilt blijven, kunt u de Azure-portal gebruiken om het gebruik van de gratis services bij te houden.

## <a name="check-usage-in-the-azure-portal"></a>Gebruik controleren in het Azure-portal

1.  Meld u aan bij de [Azure Portal](https://portal.azure.com).

2.  Zoek naar **Abonnementen**.

    ![Schermopname van zoekopdracht in portal naar abonnementen](./media/check-free-service-usage/billing-search-subscriptions.png)

3.  Selecteer het abonnement dat is gemaakt toen u zich aanmeldde voor een gratis Azure-account.

4.  Schuif omlaag naar de tabel waarin het gebruik van de gratis services wordt weergegeven.

    ![Schermopname met gebruik van gratis services](./media/check-free-service-usage/subscription-usage-free-services.png)

    De tabel heeft de volgende kolommen:

* **Meter:** Hiermee wordt de maat eenheid geïdentificeerd voor de service die wordt verbruikt.
* **Gebruiks-en limiet:** Gebruik en limiet van huidige maand voor de meter.
* **Status:** De gebruiks status van de service. Op basis van uw gebruik wordt een van de volgende statuswaarden weergegeven:
  * **Niet in gebruik:** U hebt de meter niet gebruikt, of het gebruik van de meter heeft het facturerings systeem nog niet bereikt.
  * **Overschreden op \<datum >:** U hebt de limiet voor de meter op \<datum overschreden >.
  * **Onwaarschijnlijke overschrijding:** U bent waarschijnlijk niet langer dan de limiet voor de meter.
  * **Is groter dan \<datum >:** Waarschijnlijk overschrijdt u de limiet voor de meter op \<datum >.

> [!IMPORTANT]
>
> Gratis services zijn alleen beschikbaar voor het abonnement dat is gemaakt toen u zich aanmeldde voor een gratis Azure-account. Als u de tabel voor gratis services niet ziet op de overzichtspagina van het abonnement, zijn deze niet beschikbaar voor het abonnement.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u een vraag wilt stellen of hulp nodig hebt, maakt u een [ondersteuningsaanvraag](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- [Uw gratis Azure-account upgraden](upgrade-azure-subscription.md)

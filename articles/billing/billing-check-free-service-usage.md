---
title: Gebruik van gratis Azure-services controleren en bijhouden
description: Lees hier hoe u het gebruik van gratis services kunt controleren in de Azure-portal en in het CSV-bestand met gebruiksgegevens.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491428"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Gebruik van gratis services controleren die deel uitmaken van uw gratis Azure-account

Er worden geen kosten in rekening gebracht voor services die gratis worden geleverd bij een gratis Azure-account, tenzij u de limiet van de services overschrijdt. Als u binnen de limiet wilt blijven, kunt u het Azure-portal of het bestand met gebruiksgegevens gebruiken om het gebruik van een gratis service te controleren en bij te houden.

## <a name="check-usage-in-the-azure-portal"></a>Gebruik controleren in het Azure-portal

1.  Meld u aan bij [Azure Portal](https://portal.azure.com).

2.  Selecteer in het navigatiegebied links **Alle services**.

3.  Selecteer **Abonnementen**.

4.  Selecteer het abonnement dat u hebt gemaakt toen u zich aanmeldde voor een gratis account.

    ![Schermopname met alle abonnementen](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  In het gedeelte Overzicht ziet u essentiële informatie over uw abonnement, zoals de abonnements-id, het type aanbieding en de abonnementsnaam. U kunt ook informatie vinden over wanneer het tegoed van uw gratis account verloopt.

    ![Schermopname met essentiële informatie over abonnement](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Schuif omlaag om informatie over uw huidige en voorspelde kosten te vinden. De kosten hebben betrekking op servicegebruik dat niet is inbegrepen bij uw gratis account en gebruik over de limiet van gratis services.

    ![Schermopname met kostengegevens van abonnement](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Het laatste gedeelte van het overzicht bevat een tabel met gebruiksgegevens van gratis services.

    ![Schermopname met gebruik van gratis services](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    De tabel heeft de volgende kolommen:

* **Meter:** de maateenheid voor de meter die wordt verbruikt. Zie [Gratis servicemetertoewijzing begrijpen](billing-understand-free-service-meter-mapping.md) voor meer informatie.
* **Gebruik/limiet:** Gebruik en limiet voor de huidige maand voor de meter. Deze informatie is ook beschikbaar op de statusbalk.
* **Status:** gebruiksstatus van de meter. Op basis van uw gebruikspatroon wordt een van de volgende statuswaarden weergegeven:
  * **Niet in gebruik:** u hebt de meter niet gebruikt of het gebruik voor de meter heeft het factureringssysteem niet bereikt.
  * **Overschreden op \<datum>:** u hebt de limiet voor de meter overschreden op \<datum >.
  * **Waarschijnlijk geen overschrijding:** de kans is klein dat de limiet voor de meter wordt overschreden.
  * **Wordt overschreden op \<datum>:** de limiet voor de meter wordt waarschijnlijk overschreden op \<datum>.

## <a name="check-usage-with-the-usage-file"></a>Gebruik controleren met het bestand met gebruiksgegevens

Het bestand met gebruiksgegevens bevat gedetailleerde informatie over uw Azure-abonnement. U kunt een maandelijks en dagelijks bestand met gebruiksgegevens downloaden uit het Azure-accountcentrum. Zie [Uw Azure-factuur en de dagelijkse gebruiksgegevens downloaden of weergeven](billing-download-azure-invoice-daily-usage-date.md) voor informatie over het downloaden van het bestand en de toegang die hiervoor nodig is. Zie [Meer informatie over de gebruiksvoorwaarden in uw bestand voor Azure-gebruik en -kosten](billing-understand-your-usage.md) voor informatie over de kolommen in het bestand met gebruiksgegevens.

Het bestand met gebruiksgegevens bevat gebruiksgegevens voor zowel gratis als betaalde services. Meters voor gratis services herkent u aan **Free** aan het einde van de meternaam. Als u gratis meters wilt vinden, opent u het bestand in Excel en filtert u de kolom **Meter Category** op cellen met de tekst **- Free** (gebruik Tekstfilters &rarr; Contains filter).


![Schermopname met gebruik van gratis services](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- [Uw abonnement bijwerken](billing-upgrade-azure-subscription.md)

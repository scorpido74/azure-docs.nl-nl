---
title: Azure-reserveringskosten terugstorten
description: Meer informatie over het weergeven van Azure-reserveringskosten voor terugstorting.
author: yashesvi
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 04e4e8dfce8549494eb9d2984d6307c18b88dc73
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287324"
---
# <a name="charge-back-azure-reservation-costs"></a>Azure-reserveringskosten terugstorten

Enterprise Enrollment-factureringslezers en factureringslezers van een Microsoft-klantovereenkomst kunnen afgeschreven kostengegevens voor reserveringen weergeven. Ze kunnen deze kostengegevens gebruiken om de monetaire waarde voor een abonnement, resourcegroep, resource of een tag terug te storten aan hun partners. In afgeschreven gegevens zijn de effectieve prijs de evenredige reserveringskosten per uur. De kosten zijn de totale kosten voor reserveringsgebruik door de resource op die dag.

Gebruikers met een individueel abonnement kunnen de afgeschreven kostengegevens bekijken in hun gebruiksbestand. Wanneer een resource een reserveringskorting krijgt, bevat het gedeelte *AdditionalInfo* in het gebruiksbestand de reserveringsdetails. Zie [Gebruiksgegevens downloaden vanuit Azure Portal](https://docs.microsoft.com/azure/cost-management-billing/understand/download-azure-daily-usage#download-usage-from-the-azure-portal-csv) voor meer informatie.

## <a name="get-reservation-charge-back-data-for-chargeback"></a>Gegevens over terugstorting van reserveringskosten weergeven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar **Kostenbeheer en facturering**.
1. Selecteer onder **Werkelijke kosten** de metriek **Afgeschreven kosten**.
1. Als u wilt zien welke resources door een reservering zijn gebruikt, past u een filter toe voor **Reservering** en selecteert u vervolgens reserveringen.
1. Stel **Granulariteit** in op **Maandelijks** of **Dagelijks**.
1. Stel het grafiektype in op **Tabel**.
1. Stel de optie **Groeperen op** in op **Resource**.

[![Voorbeeld van resources van reserveringskosten die u voor terugstorting kunt gebruiken](./media/charge-back-usage/amortized-reservation-costs.png)](./media/charge-back-usage/amortized-reservation-costs.png#lightbox)

Hier volgt een video waarin wordt weergegeven hoe de kosten voor het reserveringsgebruik in Azure Portal worden weergegeven.

 > [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4sQOw] 

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-reserveringen beheren](manage-reserved-vm-instance.md) voor meer informatie over het beheren van een reservering.
- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
  - [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
  - [Reserveringen beheren in Azure](manage-reserved-vm-instance.md)

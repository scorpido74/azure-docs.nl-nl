---
title: Azure-reserveringskosten terugstorten
description: Meer informatie over het weergeven van Azure-reserveringskosten voor terugstorting.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 4387a1aa5b22c9808f36364b71f89a71736958b8
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689470"
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

---
title: Aankoop- en terugbetalingstransacties van Azure-reserveringen weergeven
description: Meer informatie over het weergeven van aankoop- en terugbetalingstransacties van Azure-reserveringen.
author: yashesvi
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 988dd057834f6eb19dfd75a868c2893aefb61435
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290755"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Aankoop- en terugbetalingstransacties van reserveringen weergeven

Reserveringsaankopen en terugbetalingstransacties kunnen op een aantal verschillende manieren worden weergegeven. U kunt hiervoor Azure Portal, Power BI en REST-API's gebruiken.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Reserveringstransacties weergeven in Azure Portal

Een Enterprise Enrollment-beheerder of een factureringsbeheerder van een Microsoft-klantovereenkomst kan reserveringstransacties weergeven in Cost Management en Billing.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.
1. Selecteer **Reserveringstransacties**.
1. Als u de resultaten wilt filteren, selecteert u **Periode**, **Type** of **Beschrijving**.
1. Selecteer **Toepassen**.

[![Schermafbeelding van reserveringstransacties in Azure Portal](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Reserveringstransacties weergeven in Power BI

Een Enterprise Enrollment-beheerder of een factureringsbeheerder van een Microsoft-klantovereenkomst kan reserveringstransacties weergeven met de Cost Management Power BI-app.

1. Download de [Cost Management Power BI-app](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Navigeer naar het rapport over RI-aankopen.

[![Voorbeeld van reserveringstransacties](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Zie [Azure Cost Management Power BI-app voor Enterprise Agreements](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app) voor meer informatie.

## <a name="use-apis-to-get-reservation-transactions"></a>Reserveringsacties weergeven met API's

Gebruikers van Enterprise Agreement (EA) en de Microsoft-klantovereenkomst kunnen gegevens over reserveringstransacties ophalen aan de hand van de informatie in [Reserveringstransacties - Lijst-API](https://docs.microsoft.com/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-reserveringen beheren](manage-reserved-vm-instance.md) voor meer informatie over het beheren van een reservering.
- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
  - [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
  - [Reserveringen beheren in Azure](manage-reserved-vm-instance.md)

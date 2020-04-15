---
title: Reserveringen voor Azure-resources weergeven | Microsoft Docs
description: Lees hier meer over het weergeven van Azure-reserveringen in de Azure-portal.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: banders
ms.openlocfilehash: 0635c21ee30a40344281f31c8f9aedf9d74a1284
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633857"
---
# <a name="view-azure-reservations"></a>Azure-reserveringen weergeven

U kunt de aangeschafte reservering in de Azure-portal weergeven en beheren.   

## <a name="permissions-to-view-a-reservation"></a>Machtigingen voor het weergeven van een reservering

Als u een reservering wilt weergeven of beheren, moet u hiervoor de benodigde machtigingen van een lezer of eigenaar hebben. Als u een reservering koopt, krijgen u en de accountbeheerder standaard automatisch de rol van eigenaar voor de reserveringsorder en de reservering zelf. Als u wilt dat andere personen de reservering kunnen bekijken, moet u hen toevoegen als **Eigenaar** of **Lezer** van de reserveringsorder of de reservering. Als u iemand toevoegt aan het abonnement dat wordt gebruikt voor facturering van de reservering, wordt deze persoon niet automatisch toegevoegd aan de reservering. 

Zie [Gebruikers toevoegen of wijzigen die een reservering kunnen beheren](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation) voor meer informatie.

## <a name="view-reservation-and-utilization-in-azure-portal"></a>Reservering en gebruik bekijken in de Azure-portal

Een reservering weergeven als eigenaar of lezer

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Ga naar [Reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. De lijst bevat alle reserveringen waarvoor u de rol van eigenaar of lezer hebt. Voor elke reservering ziet u het laatst bekende gebruikspercentage.
4. Klik op het gebruikspercentage om de geschiedenis en de details van het gebruik te bekijken. Zie de onderstaande video voor meer informatie.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Reserveringen en gebruik opvragen met behulp van API's, PowerShell, CLI

Een lijst met alle reserveringen opvragen met behulp van de volgende resources
- [API: Reserveringsorder: lijst](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Reserveringsorder: lijst](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [CLI: Reserveringsorder: lijst](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

U kunt het [reserveringsgebruik](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) opvragen met behulp van de API voor het gebruik van gereserveerde instanties. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Reserveringen en gebruik weergeven in Power BI

Er zijn twee opties voor Power BI-gebruikers
- Inhoudspakket: Reserveringsaankopen en gebruiksgegevens zijn beschikbaar in het [inhoudspakket Microsoft Azure Consumption Insights voor Power BI](/power-bi/desktop-connect-azure-cost-management). Maak de gewenste rapporten met dit inhoudspakket. 
- Kostenbeheer-app: Gebruik de [Kostenbeheer-app](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) voor vooraf gemaakte rapporten die u verder kunt aanpassen.

## <a name="next-steps"></a>Volgende stappen

- [Azure-reserveringen beheren](manage-reserved-vm-instance.md).
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
- [Inzicht in het gebruik van reserveringen voor CSP-abonnementen](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

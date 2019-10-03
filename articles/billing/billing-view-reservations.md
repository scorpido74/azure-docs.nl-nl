---
title: Reserveringen voor Azure-resources weergeven | Microsoft Docs
description: Lees hier meer over het weergeven van Azure-reserveringen in de Azure-portal.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 3e379581440019a794388d3e01c4f230c9617a58
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718583"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Azure-reserveringen weergeven in de Azure-portal

Afhankelijk van het type abonnement en machtigingen, zijn er verschillende manieren om reserveringen voor Azure weer te geven.

## <a name="view-purchased-reservations"></a>Aangeschafte reserveringen weer geven

Wanneer u een reservering koopt, is het standaard zo dat u en de accountbeheerder toegang hebben tot de reservering. U en de accountbeheerder krijgen namelijk automatisch de rol Eigenaar voor de reserveringsorder en de reservering zelf. Als u wilt dat andere personen de reservering kunnen bekijken, moet u hen toevoegen als **Eigenaar** of **Lezer** van de reserveringsorder of de reservering.

Zie [Gebruikers toevoegen of wijzigen die een reservering kunnen beheren](billing-manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation) voor meer informatie.

Een reservering weergeven als eigenaar of lezer:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek naar **Reserveringen**.
    ![Schermopname met zoekfunctie in de Azure-portal](./media/billing-view-reservation/portal-reservation-search.png)  
3. De lijst bevat alle reserveringen waarvoor u de rol Eigenaar of Lezer hebt. Voor elke reservering ziet u het laatst bekende gebruikspercentage.  
    ![Voorbeeld van lijst met reserveringen](./media/billing-view-reservation/view-reservations.png)
4. Selecteer een reservering en bekijk de gebruikstrend voor de afgelopen vijf dagen.  
    ![Voorbeeld van gebruikstrend van een reservering](./media/billing-view-reservation/reservation-utilization.png)
5. U kunt [het gebruik van een reservering](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) ook ophalen met behulp van de API voor het gebruik van gereserveerde instanties, evenals met [het inhoudspakket Microsoft Azure Consumption Insights voor Power BI](/power-bi/service-connect-to-azure-consumption-insights).

Als u het bereik van een reservering moet wijzigen, een reservering wilt splitsen of wilt wijzigen wie een reservering kan beheren, raadpleegt u [Reserveringen voor Azure-resources beheren](billing-manage-reserved-vm-instance.md).

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>Reserveringstransacties voor Enterprise-inschrijvingen weergeven

 Als u een Enterprise-inschrijving hebt die door een partner wordt beheerd, kunt u reserveringen weergeven via **Reports** in de EA-portal. Voor andere Enterprise-inschrijvingen kunt u reserveringen bekijken in de EA-portal en in de Azure-portal. U moet een EA-beheerder zijn om reserveringstransacties te kunnen inzien.

Reserveringstransacties weergeven in de Azure-portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek naar **Kostenbeheer en facturering**.

    ![Schermopname van de zoekopdracht in de Azure-portal](./media/billing-view-reservation/portal-cm-billing-search.png)

1. Selecteer **Reserveringstransacties**.
1. Als u de resultaten wilt filteren , selecteert u **Duur**, **Type** of **Beschrijving**.
1. Selecteer **Toepassen**.

    ![Schermopname van resultaten voor reserveringstransacties](./media/billing-view-reservation/portal-billing-reservation-transaction-results.png)

Als u de gegevens wilt opvragen met een API, raadpleegt u [Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) (Transactiekosten voor gereserveerde instanties opvragen voor Enterprise-klanten).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn reserveringen voor Azure?](billing-save-compute-costs-reservations.md)
- [Reserveringen voor Azure beheren](billing-manage-reserved-vm-instance.md)

Een serviceabonnement kopen:

- [Vooruitbetalen voor gereserveerde Cosmos DB-capaciteit](../cosmos-db/cosmos-db-reserved-capacity.md)
- [Vooruitbetalen voor compute-resources van SQL Database met gereserveerde capaciteit voor Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Vooruitbetalen voor Virtual Machines met Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)

Een softwareabonnement kopen:

- [Vooruitbetalen voor Red Hat-software-abonnementen vanuit Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Vooruitbetalen voor SUSE-software-abonnementen vanuit Azure Reservations](../virtual-machines/linux/prepay-suse-software-charges.md)

Inzicht in het gebruik:

- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](billing-understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](billing-understand-reserved-instance-usage-ea.md)
- [Inzicht in het gebruik van reserveringen voor CSP-abonnementen](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

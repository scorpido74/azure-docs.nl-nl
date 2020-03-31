---
title: Quota en limieten aanpassen in Azure Data Lake Analytics
description: Meer informatie over het aanpassen en verhogen van quota en limieten in ADLA-accounts (Azure Data Lake Analytics).
services: data-lake-analytics
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonwhowell
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: c1d7c8cc4b50682a74ac88b2113f0d44ebc55199
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644712"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Quota en limieten aanpassen in Azure Data Lake Analytics

Meer informatie over het aanpassen en verhogen van het quotum en de limieten in ADLA-accounts (Azure Data Lake Analytics). Als u deze limieten kent, u uw U-SQL-taakgedrag begrijpen. Alle quotalimieten zijn zacht, zodat u de maximale limieten verhogen door contact op te nemen met Azure-ondersteuning.

## <a name="azure-subscriptions-limits"></a>Limieten voor Azure-abonnementen

**Maximum aantal ADLA-accounts per abonnement per regio:** 5

Als u een zesde ADLA-account probeert aan te maken, krijgt u een foutmelding "U hebt het maximum aantal toegestane Data Lake Analytics-accounts (5) in de regio onder abonnementsnaam bereikt".

Als u verder wilt gaan dan deze limiet, u de volgende opties proberen:
* kies een andere regio indien geschikt
* neem contact op met Azure-ondersteuning door [een ondersteuningsticket](#increase-maximum-quota-limits) te openen om een quotumverhoging aan te vragen.

## <a name="default-adla-account-limits"></a>Standaard ADLA-accountlimieten

**Maximum aantal Analytics Units (AU's) per account:** 250, standaard 32

Dit is het maximum aantal AU's dat gelijktijdig in uw account kan worden uitgevoerd. Als uw totale aantal lopende AU's voor alle taken deze limiet overschrijdt, worden nieuwere taken automatisch in de wachtrij geplaatst. Bijvoorbeeld:

* Als u slechts één taak met 32 AU's hebt uitgevoerd, wacht deze bij het indienen van een tweede taak in de taakwachtrij totdat de eerste taak is voltooid.
* Als u al vier taken hebt uitgevoerd en elk 8 AU's gebruikt, wacht het bij het indienen van een vijfde taak die 8 AU's nodig heeft in de taakwachtrij totdat er 8 AU's beschikbaar zijn.

    ![Azure Data Lake Analytics-limieten en quotapagina](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Maximum aantal Analytics Units (AU's) per taak:** 250, standaard 32

Dit is het maximum aantal AU's dat elke afzonderlijke taak in uw account kan worden toegewezen. Taken die meer dan deze limiet toegewezen krijgen, worden afgewezen, tenzij de indiener wordt beïnvloed door een rekenbeleid (limiet voor het indienen van taken) waarmee ze meer AU's per taak krijgen. De bovengrens van deze waarde is de AU-limiet voor het account.

**Maximaal aantal gelijktijdige U-SQL-taken per account:** 20

Dit is het maximum aantal taken dat gelijktijdig in uw account kan worden uitgevoerd. Boven deze waarde worden nieuwere taken automatisch in de wachtrij geplaatst.

## <a name="adjust-adla-account-limits"></a>ADLA-accountlimieten aanpassen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Kies een bestaand ADLA-account.
3. Klik op **Eigenschappen**.
4. Pas de waarden voor **maximale AU's,** **Maximaal aantal lopende taken**en limieten voor het indienen van **vacatures** aan uw behoeften aan.

## <a name="increase-maximum-quota-limits"></a>Maximumquotumlimieten verhogen

Meer informatie over Azure-limieten vindt u in de [azure-servicespecifieke limietendocumentatie.](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits)

1. Open een ondersteuningsaanvraag in azure-portal.

    ![Azure Data Lake Analytics-portalpagina](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics-portalpagina](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Selecteer het quotum voor het **uitgiftetype**.
3. Selecteer uw **abonnement** (zorg ervoor dat het geen proefabonnement is).
4. Selecteer quotumtype **Data Lake Analytics**.

    ![Azure Data Lake Analytics-portalpagina](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. In de probleempagina legt u uw gevraagde verhogingslimiet uit met **details** over waarom u deze extra capaciteit nodig hebt.

    ![Azure Data Lake Analytics-portalpagina](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Controleer uw contactgegevens en maak het ondersteuningsverzoek.

Microsoft beoordeelt uw verzoek en probeert zo snel mogelijk aan uw bedrijfsbehoeften te voldoen.

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Azure Data Lake Analytics-taken bewaken en problemen oplossen met Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

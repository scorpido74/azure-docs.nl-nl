---
title: Azure Data Lake Analytics-Azure Portal bewaken
description: In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om Azure Data Lake Analytics taken op te lossen.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "71316593"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Taken in Azure Data Lake Analytics bewaken met behulp van de Azure-portal

**Om alle taken weer te geven**

1. Klik in de Azure Portal op **Microsoft Azure** in de linkerbovenhoek.
2. Klik op de tegel met de naam van uw Data Lake Analytics-account.  Het taak overzicht wordt weer gegeven op de tegel **taak beheer** .

    ![Azure Data Lake Analytics taak beheer](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    Het taak beheer geeft u een overzicht van de taak status. U ziet dat er een mislukte taak is.
3. Klik op de tegel **taak beheer** om de taken weer te geven. De taken worden gecategoriseerd in **actieve**, in de **wachtrij geplaatst**en **beëindigd**. De mislukte taak wordt weer geven in de sectie **beëindigd** . Het is de eerste in de lijst. Wanneer u veel taken hebt, kunt u op **filter** klikken om u te helpen bij het zoeken naar taken.

    ![Azure Data Lake Analytics filter taken](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Klik op de mislukte taak in de lijst om de taak details te openen:

    ![Azure Data Lake Analytics mislukte taak](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    Let op de knop **opnieuw verzenden** . Nadat u het probleem hebt opgelost, kunt u de taak opnieuw verzenden.
5. Klik op gemarkeerd deel van de vorige scherm afbeelding om de fout details te openen.  U ziet iets als:

    ![Details van mislukte taak Azure Data Lake Analytics](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    U krijgt de bronmap niet te vinden.
6. Klik op **script dupliceren**.
7. Werk het **van** -pad bij naar:

    Bestand/Samples/data/searchlog.TSV
8. Klik op **Taak verzenden**.

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Aan de slag met Azure Data Lake Analytics met Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Azure Data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md)

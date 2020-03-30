---
title: Azure Data Lake Analytics - Azure-portal bewaken
description: In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om problemen op te lossen met Azure Data Lake Analytics-taken.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 7fb85f179f1e1e1ac873fc7d6d937b34c1d48ada
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316593"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>Taken in Azure Data Lake Analytics bewaken met behulp van de Azure-portal

**Alle vacatures bekijken**

1. Klik in de Azure-portal op **Microsoft Azure** in de linkerbovenhoek.
2. Klik op de tegel met de naam van uw Data Lake Analytics-account.  Het functieoverzicht wordt weergegeven op de tegel **Taakbeheer.**

    ![Azure Data Lake Analytics-taakbeheer](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    De job Management geeft u een blik op de functiestatus. Merk op dat er een mislukte taak is.
3. Klik op de tegel **Taakbeheer** om de taken weer te geven. De taken worden gecategoriseerd in **Lopend**, **In de wachtrij**en **Beëindigd**. Je ziet je mislukte taak in de sectie **Afgelopen.** Het is de eerste in de lijst. Wanneer u veel vacatures hebt, u op **Filter** klikken om u te helpen bij het vinden van vacatures.

    ![Azure Data Lake Analytics-filtertaken](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. Klik op de mislukte taak in de lijst om de taakgegevens te openen:

    ![Azure Data Lake Analytics mislukte taak](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    De knop **Opnieuw indienen ziet erin.** Nadat u het probleem hebt opgelost, u de taak opnieuw indienen.
5. Klik op gemarkeerd deel van de vorige schermafbeelding om de foutdetails te openen.  Je zult iets zien als:

    ![Azure Data Lake Analytics heeft geen taakgegevens](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Het vertelt u dat de bronmap niet is gevonden.
6. Klik **op Script dupliceren**.
7. Werk het **VAN-pad** bij naar:

    "/Samples/Data/SearchLog.tsv"
8. Klik op **Taak verzenden**.

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Aan de slag met Azure Data Lake Analytics met Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Azure Data Lake Analytics beheren met Azure-portal](data-lake-analytics-manage-use-portal.md)

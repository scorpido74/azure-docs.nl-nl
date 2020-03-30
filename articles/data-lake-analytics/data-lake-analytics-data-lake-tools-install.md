---
title: Azure Data Lake-tools voor Visual Studio installeren
description: In dit artikel wordt beschreven hoe u Azure Data Lake Tools voor Visual Studio installeert.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914090"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake Tools voor Visual Studio installeren

Meer informatie over het gebruik van Visual Studio om Azure Data Lake Analytics-accounts te maken. U taken in [U-SQL](data-lake-analytics-u-sql-get-started.md) definiëren en taken indienen bij de Data Lake Analytics-service. Zie overzicht van [Azure Data Lake Analytics](data-lake-analytics-overview.md)voor meer informatie over Data Lake Analytics.

## <a name="prerequisites"></a>Vereisten

* **Visual Studio**: alle versies behalve Express worden ondersteund.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK voor .NET** versie 2.7.1 of hoger. U kunt dit installeren met het [webplatforminstallatieprogramma](https://www.microsoft.com/web/downloads/platform.aspx).
* Een **Data Lake Analytics-account.** Zie [Aan de slag met Azure Data Lake Analytics met Azure Portal](data-lake-analytics-get-started-portal.md) om een account te maken.

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Azure Data Lake-hulpprogramma's installeren voor Visual Studio 2017 of Visual Studio 2019

Azure Data Lake Tools voor Visual Studio wordt ondersteund in Visual Studio 2017 15.3 of hoger. De tool maakt deel uit van de workloads **voor gegevensopslag en -verwerking** en **Azure-ontwikkeling.** Schakel een van deze twee workloads in als onderdeel van uw installatie van Visual Studio.

Schakel de werkbelasting **voor gegevensopslag en -verwerking** in zoals weergegeven:

![Werkbelasting voor gegevensopslag en -verwerking inschakelen](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Schakel de **Azure-ontwikkelingswerkbelasting** in zoals weergegeven:

![Azure-ontwikkelingswerkbelasting selecteren](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Azure Data Lake Tools voor Visual Studio 2013 en 2015 installeren

Microsoft [Azure Data Lake en Stream Analytics-hulpprogramma's voor Visual Studio ](https://aka.ms/adltoolsvs)downloaden en installeren. Na de installatie heeft Visual Studio de volgende wijzigingen:

* Het**Azure-knooppunt van** **Server Explorer** > bevat een **Gegevensmeeranalytics-knooppunt.**
* Het menu **Extra** bevat een **Data Lake**-item.

## <a name="next-steps"></a>Volgende stappen

* Zie [Diagnostische logboeken voor Azure Data Lake Analytics openen voor](data-lake-analytics-diagnostic-logs.md)diagnostische gegevens.
* Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) voor een complexere query.
* Zie [De uitvoeringsweergave Vertex gebruiken in Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)als u de weergave vertex-uitvoering wilt gebruiken.

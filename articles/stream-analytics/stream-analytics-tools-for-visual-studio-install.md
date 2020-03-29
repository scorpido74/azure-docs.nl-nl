---
title: Azure Stream Analytics-hulpprogramma's instellen voor Visual Studio
description: In dit artikel worden de installatievereisten beschreven en hoe u de Azure Stream Analytics-hulpprogramma's voor Visual Studio instellen.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354372"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Azure Stream Analytics-hulpprogramma's installeren voor Visual Studio

Visual Studio 2019 en Visual Studio 2017 ondersteunen Azure Data Lake en Stream Analytics Tools. In dit artikel wordt beschreven hoe u de hulpprogramma's installeren en verwijderen.

Zie [Snelstart: Een Azure Stream Analytics-taak maken met Visual Studio](stream-analytics-quick-create-vs.md)voor meer informatie over het gebruik van de hulpprogramma's.

## <a name="install"></a>Installeren

Visual Studio Enterprise (Ultimate/Premium), Professional en Community-edities ondersteunen de tools. Express edition en Visual Studio voor Mac ondersteunen ze niet.

We raden Visual Studio 2019 aan.

### <a name="install-for-visual-studio-2019-and-2017"></a>Installeren voor Visual Studio 2019 en 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Azure Data Lake en Stream Analytics Tools maken deel uit van de **azure-beheer-** en **gegevensopslag- en** verwerkingsworkloads. Schakel een van deze twee workloads in tijdens de installatie. Als Visual Studio al is geïnstalleerd, selecteert u **Extra** > **Gereedschap en functies om** workloads toe te voegen.

Download [Visual Studio 2019 (Preview 2 of hoger) of Visual Studio 2017 (15.3 of hoger)](https://www.visualstudio.com/) en volg instructies om te installeren.

Selecteer de werkbelasting **voor gegevensopslag en -verwerking** zoals weergegeven:

![Gegevensopslag- en verwerkingswerkbelasting is geselecteerd](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Selecteer de **Azure-ontwikkelingswerkbelasting** zoals weergegeven:

![Azure-ontwikkelingswerkbelasting is geselecteerd](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Nadat u de werkbelasting hebt toegevoegd, werkt u de hulpprogramma's bij. Deze procedure verwijst naar Visual Studio 2019:

1. Selecteer **Extensies** > **beheren Extensies**.

1. Selecteer **in Extensies beheren**de optie **Updates** en kies Azure Data Lake en Stream **Analytics Tools**.

1. Selecteer **Bijwerken** om de nieuwste extensie te installeren.

![Visual Studio-extensies en -updates](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Installeren voor Visual Studio 2015 en 2013<a name="visual-studio-2015-2013"></a>

Visual Studio Enterprise (Ultimate/Premium), Professional en Community-edities ondersteunen de tools. Express edition ondersteunt ze niet.

* Visual Studio 2015 of Visual Studio 2013 Update 4 installeren.
* Installeer de Microsoft Azure SDK voor .NET-versie 2.7.1 of hoger met behulp van het installatieprogramma van het [webplatform.](https://www.microsoft.com/web/downloads/platform.aspx)
* Installeer [Microsoft Azure Data Lake en Stream Analytics Tools voor Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Update<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Voor Visual Studio 2019 en Visual Studio 2017 verschijnt een nieuwe versieherinnering als visual studio-melding.

Voor Visual Studio 2015 en Visual Studio 2013 controleren de hulpprogramma's automatisch op nieuwe versies. Volg de instructies om de nieuwste versie te installeren.

## <a name="uninstall"></a>Verwijderen

U Azure Data Lake en Stream Analytics Tools verwijderen. Selecteer **Tools** > **en functies voor**Visual Studio 2019 of Visual Studio 2017. Schakel in **Het wijzigen**de selectie van Azure Data Lake en Stream Analytics **Tools uit.** Deze wordt weergegeven onder de werkbelasting **voor gegevensopslag en -verwerking** of de **Azure-ontwikkelingswerkbelasting.**

Als u de installatie wilt verwijderen van Visual Studio 2015 of Visual Studio 2013, gaat u naar**Programma's en functies**van het **Configuratiescherm** > . Verwijder **Microsoft Azure Data Lake en Stream Analytics Tools voor Visual Studio**.

---
title: Azure Kinect-bibliotheek toevoegen aan uw Visual Studio-project
description: Meer informatie over het toevoegen van het Azure Kinect NuGet-pakket aan uw Visual Studio-project.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, sensor, SDK, Visual Studio 2017, Visual Studio 2019, nuget
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85277309"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Azure Kinect-bibliotheek toevoegen aan uw Visual Studio-project

Dit artikel begeleidt u bij het toevoegen van een Azure Kinect NuGet-pakket aan uw Visual Studio-project.

## <a name="install-azure-kinect-nuget-package"></a>Azure Kinect NuGet-pakket installeren

Het Azure Kinect NuGet-pakket installeren:

1. In de snelstartgids vindt u gedetailleerde instructies voor het installeren van een NuGet-pakket in Visual Studio [: een pakket installeren en gebruiken in Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Als u het pakket wilt toevoegen, kunt u de gebruikers interface van pakket beheer gebruiken door met de rechter muisknop op verwijzingen te klikken en NuGet-pakketten beheren te kiezen van Solution Explorer.
3. Kies [nuget.org](https://www.nuget.org) als pakket bron, selecteer het tabblad Bladeren en zoek naar `Microsoft.Azure.Kinect.Sensor` .
4. Selecteer dat pakket in de lijst en installeer.

## <a name="use-azure-kinect-nuget-package"></a>Azure Kinect NuGet-pakket gebruiken

Zodra het pakket is toegevoegd, voegt u het header-bestand toe aan de bron code, zoals:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[U bent nu klaar om uw eerste toepassing te bouwen](build-first-app.md)

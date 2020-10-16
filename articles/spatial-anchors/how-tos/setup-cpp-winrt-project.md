---
title: Ruimtelijke Azure-ankers installeren voor een C++/WinRT HoloLens-toepassing
description: Een C++/WinRT HoloLens-project configureren voor het gebruik van Azure spatiale ankers
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 43d5c1ae03c359dcbef21f8e7ba3205bc6ab0004
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096110"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Ruimtelijke Azure-ankers configureren in een C++/WinRT HoloLens-project

## <a name="requirements"></a>Vereisten

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) is geïnstalleerd met de **universeel Windows-platform Development** -werk belasting en het onderdeel **Windows 10 SDK (10.0.18362.0 of hoger)** .

## <a name="configuring-a-project"></a>Een project configureren

De Azure spatiale ankers voor HoloLens en C++/WinRT worden gedistribueerd met het [micro soft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet-pakket.

Volg de instructies [hier](/nuget/consume-packages/install-use-packages-visual-studio) om NuGet package manager van Visual Studio te gebruiken om het NuGet-pakket [micro soft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) in uw project te installeren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Procedure: ankers maken en vinden in C++/WinRT](./create-locate-anchors-cpp-winrt.md)
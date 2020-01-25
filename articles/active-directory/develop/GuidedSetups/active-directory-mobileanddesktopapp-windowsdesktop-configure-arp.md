---
title: Aan de slag met Azure AD v2 Windows-bureau blad-config | Microsoft Docs
description: Hoe een Windows Desktop .NET (XAML)-toepassing een toegangs token kan ophalen en een API kan aanroepen die wordt beveiligd door Azure Active Directory v2-eind punt.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: e84d99d18d999a7b629f65f9d18355321f04e538
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712512"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>De registratie gegevens van de toepassing toevoegen aan uw app
In deze stap moet u de toepassings-id toevoegen aan uw project.

1.  Open `App.xaml.cs` en vervang de regel met de `ClientId` door:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Wat is de volgende?

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

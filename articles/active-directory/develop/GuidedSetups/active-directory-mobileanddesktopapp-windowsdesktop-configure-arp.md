---
title: Azure AD v2 Windows-bureau blad aan de slag-config
description: Hoe een Windows Desktop .NET (XAML)-toepassing een toegangs token kan ophalen en een API kan aanroepen die wordt beveiligd door Azure Active Directory v2-eind punt.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885766"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>De registratie gegevens van de toepassing toevoegen aan uw app
In deze stap moet u de toepassings-id toevoegen aan uw project.

1.  Open `App.xaml.cs` en vervang de regel met het `ClientId` volgende:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Wat is de volgende?

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

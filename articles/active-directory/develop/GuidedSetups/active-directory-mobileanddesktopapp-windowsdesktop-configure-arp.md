---
title: Azure AD v2 Windows Desktop aan de slag - Config | Microsoft Documenten
description: Hoe een XAML-toepassing (Windows Desktop .NET) een toegangstoken kan krijgen en een API kan aanroepen die is beschermd door Azure Active Directory v2-eindpunt.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4c757b52e7378bffe4a4954022b131b772a337cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897681"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>De registratiegegevens van de toepassing toevoegen aan uw app
In deze stap moet u de toepassings-id aan uw project toevoegen.

1.  Open `App.xaml.cs` en vervang de `ClientId` lijn met de:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Wat is de volgende stap

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

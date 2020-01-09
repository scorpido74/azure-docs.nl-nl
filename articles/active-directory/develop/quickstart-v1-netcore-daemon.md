---
title: Microsoft Graph voor het verkrijgen van token &-aanroepen (.NET Core-Console) (v 1.0) | Azure
description: Een .NET daemon-toepassing bouwen die kan worden geïntegreerd met Azure AD & Azure AD-beveiligde Api's aanroepen met OAuth 2,0
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d44dfe3eb03ff086d3785311c34ab1a6a5b3982a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424053"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-using-console-apps-identity-v10"></a>Snelstartgids: tokens verkrijgen en Microsoft Graph aanroepen met behulp van de identiteit van de console-app (v 1.0)

[Micro soft Identity platform](v2-overview.md) is een evolutie van het ontwikkel platform van de Azure Active Directory (Azure AD). Met het Microsoft Identity Platform kunnen ontwikkelaars toepassingen maken waarbij gebruikers zich met alle Microsoft-identiteiten kunnen aanmelden en waarmee tokens worden opgehaald voor het aanroepen van Microsoft-API's, zoals Microsoft Graph, of API's die door ontwikkelaars zijn gemaakt.

Met [micro soft Authentication Library (MSAL)](msal-overview.md) kunnen ontwikkel aars tokens verkrijgen van het micro soft Identity platform-eind punt om toegang te krijgen tot beveiligde web-api's. Active Directory Authentication Library (ADAL) wordt geïntegreerd met het eind punt van Azure AD voor ontwikkel aars (v 1.0), waarbij MSAL integreert met het micro soft Identity platform (v 2.0)-eind punt.

## <a name="next-steps"></a>Volgende stappen

Voor nieuwe .NET daemon-toepassingen raden we u aan micro soft Identity platform (v 2.0) en MSAL te gebruiken om tokens te verkrijgen en toegang te krijgen tot beveiligde web-Api's: [Quick Start: Schaf een token aan en roep Microsoft Graph-API aan vanuit een console-app met behulp van de identiteit van een app](quickstart-v2-netcore-daemon.md).

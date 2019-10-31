---
title: Token ophalen en Microsoft Graph-API aanroepen met de id van de console-app (v 1.0) | Microsoft Docs
description: Meer informatie over het bouwen van een .NET daemon-toepassing die kan worden geïntegreerd met Azure AD Azure AD-beveiligde Api's met behulp van OAuth 2,0
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d79d448f10c0d404516371be19d561bb21c8c0c7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149600"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-api-with-console-apps-identity-v10"></a>Quick Start: token verkrijgen en Microsoft Graph-API aanroepen met de id van de console-app (v 1.0) 

[Micro soft Identity platform](v2-overview.md) is een evolutie van het ontwikkel platform van de Azure Active Directory (Azure AD). Met het Microsoft Identity Platform kunnen ontwikkelaars toepassingen maken waarbij gebruikers zich met alle Microsoft-identiteiten kunnen aanmelden en waarmee tokens worden opgehaald voor het aanroepen van Microsoft-API's, zoals Microsoft Graph, of API's die door ontwikkelaars zijn gemaakt.

Met [micro soft Authentication Library (MSAL)](msal-overview.md) kunnen ontwikkel aars tokens verkrijgen van het micro soft Identity platform-eind punt om toegang te krijgen tot beveiligde web-api's. Active Directory Authentication Library (ADAL) wordt geïntegreerd met het eind punt van Azure AD voor ontwikkel aars (v 1.0), waarbij MSAL integreert met het micro soft Identity platform (v 2.0)-eind punt.

## <a name="next-steps"></a>Volgende stappen

Voor nieuwe .NET daemon-toepassingen raden we u aan micro soft Identity platform (v 2.0) en MSAL te gebruiken om tokens te verkrijgen en toegang te krijgen tot beveiligde web-Api's: [Quick Start: Schaf een token aan en roep Microsoft Graph-API aan vanuit een console-app met behulp van de identiteit van een app](quickstart-v2-netcore-daemon.md).

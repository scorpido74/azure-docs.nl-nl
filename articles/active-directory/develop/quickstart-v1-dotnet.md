---
title: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een .NET Desktop-app (WPF) | Microsoft Docs
description: Meer informatie over het bouwen van een NET Windows Desktop-app die kan worden geïntegreerd met Azure Active Directory voor aanmelden en Azure Active Directory-beveiligde API-aanroepen met behulp van OAuth 2.0.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1629cae69116f71428ccb150afda0ba668146631
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920954"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Quick Start: gebruikers aanmelden en de Microsoft Graph-API aanroepen vanuit een app van een .NET-Desktop (WPF)

[Micro soft Identity platform](v2-overview.md) is een evolutie van het ontwikkel platform van de Azure Active Directory (Azure AD). Met het Microsoft Identity Platform kunnen ontwikkelaars toepassingen maken waarbij gebruikers zich met alle Microsoft-identiteiten kunnen aanmelden en waarmee tokens worden opgehaald voor het aanroepen van Microsoft-API's, zoals Microsoft Graph, of API's die door ontwikkelaars zijn gemaakt.

Met [micro soft Authentication Library (MSAL)](msal-overview.md) kunnen ontwikkel aars tokens verkrijgen van het micro soft Identity platform-eind punt om toegang te krijgen tot beveiligde web-api's. Active Directory Authentication Library (ADAL) wordt geïntegreerd met het eind punt van Azure AD voor ontwikkel aars (v 1.0), waarbij MSAL integreert met het micro soft Identity platform (v 2.0)-eind punt.

Voor nieuwe desktop toepassingen raden we u aan micro soft Identity platform (v 2.0) en MSAL te gebruiken om tokens te verkrijgen en beveiligde web-Api's te openen: [Snelstartgids: een token verkrijgen en Microsoft Graph-API aanroepen vanuit een Windows-bureau blad-app](quickstart-v2-windows-desktop.md)

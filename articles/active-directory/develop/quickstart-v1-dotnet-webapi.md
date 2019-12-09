---
title: Een .NET-Web-API maken met Azure AD voor verificatie en autorisatie | Microsoft Docs
description: Zo bouwt u een .NET MVC web-API die is geïntegreerd met Azure Active Directory voor verificatie en autorisatie.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
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
ms.openlocfilehash: 27af5d6782b0fe45df1a82e320c593b9a0f332cd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921055"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Snelstart: bouw een .NET web-API die is geïntegreerd met Azure Active Directory voor verificatie en autorisatie

[Micro soft Identity platform](v2-overview.md) is een evolutie van het ontwikkel platform van de Azure Active Directory (Azure AD). Met het Microsoft Identity Platform kunnen ontwikkelaars toepassingen maken waarbij gebruikers zich met alle Microsoft-identiteiten kunnen aanmelden en waarmee tokens worden opgehaald voor het aanroepen van Microsoft-API's, zoals Microsoft Graph, of API's die door ontwikkelaars zijn gemaakt.

Met [micro soft Authentication Library (MSAL)](msal-overview.md) kunnen ontwikkel aars tokens verkrijgen van het micro soft Identity platform-eind punt om toegang te krijgen tot beveiligde web-api's. Active Directory Authentication Library (ADAL) wordt geïntegreerd met het eind punt van Azure AD voor ontwikkel aars (v 1.0), waarbij MSAL integreert met het micro soft Identity platform (v 2.0)-eind punt.

Voor nieuwe web-Api's raden we u aan micro soft Identity platform (v 2.0) en MSAL te gebruiken om tokens te verkrijgen en beveiligde web-Api's te openen: [Quick Start: aanmelden toevoegen met micro soft aan een ASP.net-Web-app](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)

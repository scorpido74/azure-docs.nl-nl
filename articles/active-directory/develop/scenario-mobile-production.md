---
title: Mobiele app voor het aanroepen van web-Api's voor productie voorbereiden | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept. (Apps voorbereiden voor productie.)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1ea19b8b76f4eb4a2c984f0e39eb0fd373c8b83c
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132393"
---
# <a name="prepare-mobile-apps-for-production"></a>Mobiele apps voorbereiden voor productie

Dit artikel bevat informatie over het verbeteren van de kwaliteit en betrouw baarheid van uw mobiele app voordat u deze naar productie gaat verplaatsen.

## <a name="handle-errors"></a>Fouten verwerken

Wanneer u een mobiele app voorbereidt voor productie, kunnen verschillende fout situaties optreden. De belangrijkste gevallen die u kunt afhandelen, zijn stille storingen en terugvals voor interactie. Andere voor waarden die u moet overwegen, zijn geen netwerk situaties, service storingen, vereisten voor beheerders toestemming en andere scenario's.

Voor elk type micro soft Authentication Library (MSAL) vindt u voorbeeld code en wiki-inhoud waarin wordt beschreven hoe u fout voorwaarden afhandelt:

- [Android-wiki MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET-wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Problemen oplossen en onderzoeken

Gegevens verzamelen voor betere diagnose van problemen in uw app. Zie [logboek registratie in MSAL-toepassingen](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)voor informatie over de soorten gegevens die u kunt verzamelen.

Hier volgen enkele suggesties voor het verzamelen van gegevens:

- Gebruikers kunnen om hulp vragen wanneer ze problemen ondervinden. Een best practice is Logboeken vastleggen en tijdelijk opslaan. Geef een locatie op waar gebruikers de logboeken kunnen uploaden. MSAL biedt logboek registratie-extensies voor het vastleggen van gedetailleerde informatie over verificatie.

- Als telemetrie beschikbaar is, kunt u dit inschakelen via MSAL om gegevens te verzamelen over de manier waarop gebruikers zich aanmelden bij uw app.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Zie [Desktop en mobiele open bare client-apps](sample-v2-code.md#desktop-and-mobile-public-client-apps)voor meer voor beelden.

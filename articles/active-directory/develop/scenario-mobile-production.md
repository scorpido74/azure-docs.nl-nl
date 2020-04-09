---
title: Web API's voor mobiele apps voorbereiden voor productie | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-API's aanroept. (Apps voorbereiden op productie.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 28ace84f9a80b71209d7963d02b66317292b151b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882536"
---
# <a name="prepare-mobile-apps-for-production"></a>Mobiele apps voorbereiden op productie

In dit artikel vindt u informatie over hoe u de kwaliteit en betrouwbaarheid van uw mobiele app verbeteren voordat u deze in productie neemt.

## <a name="handle-errors"></a>Fouten verwerken

Als u een mobiele app voorbereidt op productie, kunnen er verschillende foutvoorwaarden optreden. De belangrijkste gevallen die u behandelt zijn stille fouten en terugval naar interactie. Andere voorwaarden die u moet overwegen zijn no-network situaties, service onderbrekingen, vereisten voor toestemming van beheerders, en andere scenario-specifieke gevallen.

Voor elk MSAL-type (Microsoft Authentication Library) u voorbeeldcode en wiki-inhoud vinden waarin wordt beschreven hoe u foutvoorwaarden verwerken:

- [MSAL Android wiki](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [MSAL iOS wiki](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)

## <a name="mitigate-and-investigate-issues"></a>Problemen beperken en onderzoeken

Om problemen in uw app beter te diagnosticeren, verzamelt u gegevens. Zie [Inlog in MSAL-toepassingen](https://docs.microsoft.com/azure/active-directory/develop/msal-logging)voor informatie over de soorten gegevens die u verzamelen.

Hier volgen enkele suggesties voor het verzamelen van gegevens:

- Gebruikers kunnen om hulp vragen wanneer ze problemen hebben. Een aanbevolen praktijk is het vastleggen en tijdelijk opslaan van logboeken. Geef een locatie op waar gebruikers de logboeken kunnen uploaden. MSAL biedt logboekextensies om gedetailleerde informatie over verificatie vast te leggen.

- Als telemetrie beschikbaar is, schakelt u dit via MSAL in om gegevens te verzamelen over hoe gebruikers zich bij uw app aanmelden.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

Zie Apps voor [openbare bureaublad- en mobiele clientapps](sample-v2-code.md#desktop-and-mobile-public-client-apps)voor meer voorbeelden uitproberen.

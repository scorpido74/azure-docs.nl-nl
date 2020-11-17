---
title: 'Zelfstudie: Een veilige web-app ontwikkelen in Azure App Service | Azure'
description: In deze zelfstudie leert u hoe u een web-app ontwikkelt met behulp van Azure App Service, verificatie inschakelt, Azure Storage aanroept en Microsoft Graph aanroept.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428221"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Zelfstudie: Verificatie inschakelen in App Service en toegang tot opslag en Microsoft Graph krijgen

In deze zelfstudie wordt een veelvoorkomend toepassingsscenario beschreven en leert u procedures om het volgende te doen:

- [(A) Verificatie configureren voor een web-app](scenario-secure-app-authentication-app-service.md) en de toegang beperken tot gebruikers in uw organisatie.
- [(B) Veilig toegang tot Azure Storage krijgen](scenario-secure-app-access-storage.md) namens de web-app met behulp van beheerde identiteiten.
- (C) Toegang tot gegevens in Microsoft Graph krijgen [namens de aangemelde gebruiker](scenario-secure-app-access-microsoft-graph-as-user.md) of [namens de web-app](scenario-secure-app-access-microsoft-graph-as-app.md) met behulp van beheerde identiteiten.
- [De resources opschonen](scenario-secure-app-clean-up-resources.md) die u voor deze zelfstudie hebt gemaakt.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Toepassingsscenario's in het Microsoft-identiteitsplatform" border="false":::

Om te beginnen leert u hoe u verificatie inschakelt voor een web-app.

> [!div class="nextstepaction"]
> [Verificatie configureren voor een web-app](scenario-secure-app-authentication-app-service.md)

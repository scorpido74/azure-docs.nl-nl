---
title: Accepteert mijn aanmeldings pagina voor Azure AD micro soft-accounts | Microsoft Docs
description: Hoe scherm berichten de gebruikers naam zoeken weer spie gelen tijdens het aanmelden
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81ba7f77bb3396f49c3cf41d2266567aa48a8257
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731355"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Aanmeldings opties voor micro soft-accounts in Azure Active Directory

De Microsoft 365-aanmeldings pagina voor Azure Active Directory (Azure AD) ondersteunt werk-of school accounts en micro soft-accounts, maar afhankelijk van de situatie van de gebruiker, kan dit een van beide of beide zijn. De aanmeldings pagina van Azure AD ondersteunt bijvoorbeeld:

* Apps die aanmeldingen accepteren van beide typen accounts
* Organisaties die gasten accepteren

## <a name="identification"></a>Identificatie
U kunt zien of de aanmeldings pagina die door uw organisatie wordt gebruikt, micro soft-accounts ondersteunt door te kijken naar de tekst van de hint in het veld username. Als de tekst van de hint "E-mail, telefoon of Skype" luidt, ondersteunt de aanmeldings pagina micro soft-accounts.

![Verschil tussen de aanmeldings pagina's van een account](./media/signin-account-support/ui-prompt.png)

[Aanvullende aanmeldings opties werken alleen voor persoonlijke micro soft-accounts](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , maar kunnen niet worden gebruikt voor het aanmelden bij resources van het werk-of school account.

## <a name="next-steps"></a>Volgende stappen

[Uw huis stijl voor aanmelden aanpassen](../fundamentals/add-custom-domain.md)
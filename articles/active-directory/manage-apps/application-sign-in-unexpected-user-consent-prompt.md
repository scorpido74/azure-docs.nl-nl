---
title: Onverwachte toestemming vragen bij het aanmelden bij een toepassing | Microsoft Docs
description: Problemen oplossen wanneer een gebruiker een toestemming prompt krijgt voor een toepassing die u hebt geïntegreerd met Azure AD en die u niet verwacht
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83d043ecef152f977437e21e2caec40d1c40ce0d
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "65781155"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Onverwachte toestemming vragen bij het aanmelden bij een toepassing

Veel toepassingen die met Azure Active Directory worden geïntegreerd, hebben machtigingen voor verschillende resources nodig om te kunnen worden uitgevoerd. Wanneer deze resources ook zijn geïntegreerd met Azure Active Directory, wordt er met behulp van het Azure AD-instemming raamwerk toestemming gevraagd om ze te openen. 

Dit resulteert in een toestemming prompt die de eerste keer wordt gebruikt om een toepassing te gebruiken. Dit is vaak een eenmalige bewerking. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenario's waarin gebruikers toestemming geven voor het goed keuren

Er kunnen in verschillende scenario's extra prompts worden verwacht:

* De set met machtigingen die vereist zijn voor de toepassing is gewijzigd.

* De gebruiker die oorspronkelijk heeft ingestemd met de toepassing, is geen beheerder en nu gebruikt een andere gebruiker (niet-beheerder) de toepassing voor de eerste keer.

* De gebruiker die oorspronkelijk heeft ingestemd met de toepassing, was een beheerder, maar heeft geen toestemming verleend namens de hele organisatie.

* De toepassing gebruikt [incrementele en dynamische toestemming](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) om aanvullende machtigingen aan te vragen nadat de toestemming voor het eerst is toegekend. Dit wordt vaak gebruikt wanneer optionele functies van een toepassing extra machtigingen vereisen dan vereisten die vereist zijn voor de basislijn functionaliteit.

* De toestemming is ingetrokken nadat deze in eerste instantie is toegekend.

* De ontwikkelaar heeft de toepassing zo geconfigureerd dat elke keer dat deze wordt gebruikt een toestemming wordt gevraagd (Let op: dit is niet best practice).

## <a name="next-steps"></a>Volgende stappen

-   [Apps, machtigingen en toestemming in Azure Active Directory (v 1.0-eind punt)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Bereiken, machtigingen en toestemming in de Azure Active Directory (v 2.0-eind punt)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)



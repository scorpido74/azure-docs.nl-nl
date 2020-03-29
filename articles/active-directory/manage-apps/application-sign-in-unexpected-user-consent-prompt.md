---
title: Onverwachte toestemmingsprompt bij het aanmelden bij een aanvraag | Microsoft Documenten
description: Problemen oplossen wanneer een gebruiker een toestemmingsprompt ziet voor een toepassing die u hebt geïntegreerd met Azure AD die u niet had verwacht
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65781155"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Onverwachte toestemmingsprompt bij het aanmelden bij een toepassing

Veel toepassingen die zijn geïntegreerd met Azure Active Directory vereisen machtigingen voor verschillende bronnen om deze uit te voeren. Wanneer deze bronnen ook zijn geïntegreerd met Azure Active Directory, worden machtigingen voor toegang tot deze bronnen aangevraagd met behulp van het Azure AD-toestemmingskader. 

Dit resulteert in een toestemmingprompt die wordt weergegeven wanneer een toepassing voor het eerst wordt gebruikt, wat vaak een eenmalige bewerking is. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenario's waarin gebruikers toestemmingsprompts zien

Aanvullende aanwijzingen kunnen worden verwacht in verschillende scenario's:

* De set machtigingen die door de toepassing vereist zijn, is gewijzigd.

* De gebruiker die oorspronkelijk ingestemd met de toepassing was geen beheerder, en nu een andere (niet-admin) Gebruiker is met behulp van de toepassing voor de eerste keer.

* De gebruiker die oorspronkelijk instemde met de toepassing was een beheerder, maar stemde niet in namens de hele organisatie.

* De toepassing gebruikt [incrementele en dynamische toestemming](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent) om aanvullende machtigingen aan te vragen nadat de toestemming in eerste instantie is verleend. Dit wordt vaak gebruikt wanneer optionele functies van een toepassing extra machtigingen vereisen die verder gaan dan die welke nodig zijn voor basislijnfunctionaliteit.

* De toestemming werd ingetrokken nadat deze in eerste instantie was verleend.

* De ontwikkelaar heeft de toepassing geconfigureerd om elke keer dat deze wordt gebruikt een toestemmingsprompt te vereisen (let op: dit is geen best practice).

## <a name="next-steps"></a>Volgende stappen

-   [Apps, machtigingen en toestemming in Azure Active Directory (v1.0-eindpunt)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)

-   [Scopes, machtigingen en toestemming in de Azure Active Directory (v2.0-eindpunt)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)



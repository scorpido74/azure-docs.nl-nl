---
title: Standaardinstellingen voor tokenlevensduur wijzigen voor aangepaste Azure AD-apps | Microsoft Documenten
description: Token Lifetime-beleid bijwerken voor uw toepassing die u ontwikkelt op Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 431f18b9babb52b5000d3bf4cca75a0f5e29bb93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702807"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>De standaardinstellingen voor de tokenlevensduur voor een op maat ontwikkelde toepassing wijzigen

In dit artikel ziet u hoe u Azure AD PowerShell gebruiken om een beleid voor een levensduur van een token in te stellen. Azure AD Premium stelt app-ontwikkelaars en tenantbeheerders in staat om de levensduur van tokens die zijn uitgegeven voor niet-vertrouwelijke clients te configureren. Token-levenslang beleid wordt ingesteld op tenantbrede basis of de resources die worden geopend.

1. Als u een beleid voor de levensduur van een token wilt instellen, moet u de [Azure AD PowerShell-module](https://www.powershellgallery.com/packages/AzureADPreview)downloaden.
1. Voer de opdracht **Connect-AzureAD -Bevestigen uit.**

    Hier is een voorbeeldbeleid dat het token voor het vernieuwen van de maximale leeftijd met één factor instelt. Maak het beleid:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Volgende stappen

* Zie [Configureerbare tokenlevensduur in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) voor meer informatie over het configureren van tokenlevensduurdie is uitgegeven door Azure AD, inclusief hoe u tokenlevensduur instelt voor alle apps in uw organisatie, voor een multi-tenant-app of voor een specifieke serviceprincipal in uw organisatie. 
* [Naslaginformatie over Azure AD-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

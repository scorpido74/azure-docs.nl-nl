---
title: De standaard waarden van de token levensduur wijzigen voor aangepaste Azure AD-apps
description: De levens duur van tokens bijwerken voor uw toepassing die u ontwikkelt op Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 7eb01ccda3c3e13827a8977b8ee0e244aef6b0be
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613235"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>De standaard waarden voor de levens duur van tokens wijzigen voor een aangepaste toepassing

In dit artikel wordt beschreven hoe u Azure AD Power shell gebruikt om een beleid voor levens duur van tokens in te stellen. Met Azure AD Premium kunnen app-ontwikkel aars en Tenant beheerders de levens duur configureren van tokens die zijn uitgegeven voor niet-vertrouwelijke clients. Het token levensduur beleid wordt ingesteld op basis van de Tenant of de bronnen waartoe toegang wordt verkregen.

Als u een token levensduur beleid wilt instellen, moet u de [Azure AD Power shell-module](https://www.powershellgallery.com/packages/AzureADPreview)downloaden.
Voer de opdracht **Connect-AzureAD-confirm** uit.

Hier volgt een voor beeld van een beleid dat gebruikers in uw web-app vaker moeten verifiëren. Met dit beleid wordt de levens duur van de toegangs-ID-tokens en de maximale leeftijd van een multi-factor-sessie token ingesteld op de service-principal van uw web-app. Maak het beleid en wijs dit toe aan uw service-principal. U moet ook de ObjectId voor uw Service-Principal ophalen.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Configureer bare token levensduur in azure AD](./active-directory-configurable-token-lifetimes.md) voor meer informatie over het configureren van de levens duur van tokens die zijn uitgegeven door Azure AD, inclusief het instellen van de levens duur van tokens voor alle apps in uw organisatie, voor een multi tenant-app of voor een specifieke Service-Principal in uw organisatie. 
* [Azure AD-token referentie](./id-tokens.md)
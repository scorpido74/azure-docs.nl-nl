---
title: RequestDisallowedByPolicy-fout
description: Beschrijft de oorzaak van de fout RequestDisallowedByPolicy bij het implementeren van resources met Azure Resource Manager.
author: genlin
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41581ba48da2f2e717c5abf2a749f8fd2b86ac06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477666"
---
# <a name="requestdisallowedbypolicy-error-with-azure-resource-policy"></a>Fout van RequestDisallowedByPolicy aanvragen met Azure-bronbeleid

In dit artikel wordt de oorzaak van de fout RequestDisallowedByPolicy beschreven, maar biedt ook oplossing voor deze fout.

## <a name="symptom"></a>Symptoom

Tijdens de implementatie ontvangt u mogelijk een fout **van RequestDisallowedByPolicy** waardoor u de resources niet maken. In het volgende voorbeeld wordt de fout weergegeven:

```json
{
  "statusCode": "Forbidden",
  "serviceRequestId": null,
  "statusMessage": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}",
  "responseBody": "{\"error\":{\"code\":\"RequestDisallowedByPolicy\",\"message\":\"The resource action 'Microsoft.Network/publicIpAddresses/write' is disallowed by one or more policies. Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'.\"}}"
}
```

## <a name="troubleshooting"></a>Problemen oplossen

Als u details wilt ophalen over het beleid dat uw implementatie heeft geblokkeerd, gebruikt u de volgende methoden:

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Geef in PowerShell die beleids-id op als parameter `Id` om details op te halen over het beleid dat uw implementatie heeft geblokkeerd.

```powershell
(Get-AzPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

### <a name="azure-cli"></a>Azure-CLI

Geef in Azure CLI de naam van de beleidsdefinitie op:

```azurecli
az policy definition show --name regionPolicyAssignment
```

## <a name="solution"></a>Oplossing

Voor beveiliging of naleving kunnen uw abonnementsbeheerders beleidsregels toewijzen die de manier beperken waarop resources worden geïmplementeerd. Uw abonnement kan bijvoorbeeld een beleid hebben dat voorkomt dat openbare IP-adressen, netwerkbeveiligingsgroepen, door de gebruiker gedefinieerde routes of routetabellen worden gemaakt. Het foutbericht in de sectie **Symptomen** toont de naam van het beleid.
Als u dit probleem wilt oplossen, controleert u het resourcebeleid en bepaalt u hoe resources moeten worden geïmplementeerd die aan dat beleid voldoen.

Raadpleeg voor meer informatie de volgende artikelen:

- [Wat is Azure Policy?](../../governance/policy/overview.md)
- [Beleidsregels voor het afdwingen van naleving maken en beheren](../../governance/policy/tutorials/create-and-manage.md)

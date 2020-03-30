---
title: Naleving met Azure-beleid
description: Ingebouwd beleid toewijzen in Azure-beleid om de naleving van uw Azure-containerregisters te controleren
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330733"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Naleving van Azure-containerregisters controleren met Azure-beleid

[Azure Policy](../governance/policy/overview.md) is een service in Azure die u gebruikt om beleid te maken, toe te wijzen en te beheren. Met deze beleidsregels worden verschillende regels en effecten afgedwongen voor uw resources, zodat deze resources voldoen aan de standaarden en service level agreements in uw bedrijf.

In dit artikel wordt ingebouwd beleid (voorbeeld) voor Azure Container Registry geïntroduceerd. Gebruik dit beleid om nieuwe en bestaande registers te controleren op naleving.

Er zijn geen kosten verbonden aan het gebruik van Azure Policy.

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="built-in-policy-definitions"></a>Ingebouwde beleidsdefinities

De volgende ingebouwde beleidsdefinities zijn specifiek voor Azure Container Registry:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

Zie ook de ingebouwde definitie van netwerkbeleid: [[Preview] Container Registry moet een eindpunt voor virtuele netwerkservice gebruiken.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)

## <a name="assign-policies"></a>Beleid toewijzen

* Wijs beleidsregels toe via de [Azure-portal,](../governance/policy/assign-policy-portal.md) [Azure CLI,](../governance/policy/assign-policy-azurecli.md)een [resourcemanagersjabloon](../governance/policy/assign-policy-template.md)of de Azure Policy SDKs.
* Een beleidstoewijzing toewijzen aan een resourcegroep, een abonnement of een [Azure-beheergroep](../governance/management-groups/overview.md). Beleidstoewijzingen voor containerregister zijn van toepassing op bestaande en nieuwe containerregisters binnen het bereik.
* Handhaving [van het beleid](../governance/policy/concepts/assignment-structure.md#enforcement-mode) op elk gewenst moment in- of uitschakelen.

> [!NOTE]
> Nadat u een beleid hebt toegewezen of bijgewerkt, duurt het enige tijd voordat de toewijzing wordt toegepast op resources in het gedefinieerde bereik. Zie informatie over [beleidsevaluatietriggers](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Naleving van het beleid controleren

Toegang tot nalevingsgegevens die worden gegenereerd door uw beleidstoewijzingen met behulp van de Azure-portal, Azure-opdrachtregelhulpprogramma's of de Azure Policy SDKs. Zie [Nalevingsgegevens van Azure-bronnen opvragen voor](../governance/policy/how-to/get-compliance-data.md)meer informatie.

Wanneer een resource niet-compatibel is, zijn er veel mogelijke redenen. Zie [Niet-naleving bepalen](../governance/policy/how-to/determine-non-compliance.md)om de reden te bepalen of de verantwoordelijke wijziging te vinden.

### <a name="policy-compliance-in-the-portal"></a>Naleving van het beleid in de portal:

1. Selecteer **Alle services**en zoek naar **Beleid**.
1. Selecteer **Naleving**.
1. Gebruik de filters om nalevingsstatussen te beperken of om te zoeken naar beleidsnaleving ![beleid in portal](./media/container-registry-azure-policy/azure-policy-compliance.png).
1. Selecteer een beleid om de geaggregeerde nalevingsgegevens en -gebeurtenissen te controleren. Selecteer desgewenst een specifiek register voor resourcecompliance.

### <a name="policy-compliance-in-the-azure-cli"></a>Naleving van het beleid in de Azure CLI

U de Azure CLI ook gebruiken om nalevingsgegevens op te halen. Gebruik bijvoorbeeld de [opdracht lijst met az-beleidstoewijzing](/cli/azure/policy/assignment#az-policy-assignment-list) in de CLI om de beleids-iD's op te halen van het Azure Container Registry-beleid dat wordt toegepast:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Voorbeelduitvoer:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Voer vervolgens de [lijst met AZ-beleid uit](/cli/azure/policy/state#az-policy-state-list) om de nalevingsstatus met JSON-indeling voor alle resources onder een specifieke beleids-ID te retourneren:

```azurecli
az policy state list \
  --resource <policyID>
```

Of voer de [lijst met az-beleid uit](/cli/azure/policy/state#az-policy-state-list) om de door JSON geformatteerde nalevingsstatus van een specifieke registerbron, zoals *myregistry,* terug te geven:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [azure-beleidsdefinities](../governance/policy/concepts/definition-structure.md) en [-effecten](../governance/policy/concepts/effects.md)

* Een [aangepaste beleidsdefinitie maken](../governance/policy/tutorials/create-custom-policy-definition.md)

* Meer informatie over [beheermogelijkheden](../governance/index.yml) in Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
---
title: Beleid implementeren dat kan worden hersteld
description: Meer informatie over het aan boord van een klant naar Azure gedelegeerd resourcebeheer, zodat hun resources kunnen worden geopend en beheerd via uw eigen tenant.
ms.date: 10/11/2019
ms.topic: conceptual
ms.openlocfilehash: c06ed4ea597808aee18d4a848bcfea7152b9cf8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270639"
---
# <a name="deploy-a-policy-that-can-be-remediated-within-a-delegated-subscription"></a>Een beleid implementeren dat kan worden gesaneerd binnen een gedelegeerd abonnement

[Azure Lighthouse](../overview.md) stelt serviceproviders in staat om beleidsdefinities te maken en te bewerken binnen een gedelegeerd abonnement. Als u echter beleid wilt implementeren dat een [hersteltaak](../../governance/policy/how-to/remediate-resources.md) gebruikt (dat wil zeggen beleid met het [effect DeployIfNotExists](../../governance/policy/concepts/effects.md#deployifnotexists) of [wijzigen),](../../governance/policy/concepts/effects.md#modify) moet u een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) maken in de klanttenant. Deze beheerde identiteit kan door Azure Policy worden gebruikt om de sjabloon binnen het beleid te implementeren. Er zijn stappen nodig om dit scenario in te schakelen, zowel wanneer u aan boord van de klant voor Azure gedelegeerd resourcebeheer is en wanneer u het beleid zelf implementeert.

## <a name="create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant"></a>Een gebruiker maken die rollen kan toewijzen aan een beheerde identiteit in de klanttenant

Wanneer u een klant aan boord maakt voor Azure delegated resource management, gebruikt u een [Azure Resource Manager-sjabloon](onboard-customer.md#create-an-azure-resource-manager-template) samen met een parametersbestand dat de gebruikers, gebruikersgroepen en serviceprincipals definieert in uw beheertenant die toegang hebben tot de gedelegeerde resources in de clienttenant. In uw parametersbestand krijgt elk van deze gebruikers **(principalId)** een [ingebouwde rol](../../role-based-access-control/built-in-roles.md) **(roleDefinitionId)** toegewezen die het toegangsniveau definieert.

Als u wilt dat een **principalId** een beheerde identiteit in de klanttenant maakt, moet u de **rolDefinitionId** instellen op **User Access Administrator**. Hoewel deze rol over het algemeen niet wordt ondersteund, kan deze in dit specifieke scenario worden gebruikt, zodat de gebruikers met deze toestemming een of meer specifieke ingebouwde rollen kunnen toewijzen aan beheerde identiteiten. Deze rollen worden gedefinieerd in de eigenschap **delegatedRoleDefinitionIds.** U hier elke ingebouwde rol opnemen, behalve voor beheerder of eigenaar van gebruikerstoegang.

Nadat de klant is aan boord genomen, kan de **principalId** die in deze autorisatie is gemaakt, deze ingebouwde rollen toewijzen aan beheerde identiteiten in de klanttenant. Ze hebben echter geen andere machtigingen die normaal zijn gekoppeld aan de functie Gebruikerstoegangsbeheerder.

In het onderstaande voorbeeld ziet u een **principalId** met de functie Gebruikerstoegangsbeheerder. Deze gebruiker kan twee ingebouwde rollen toewijzen aan beheerde identiteiten in de klanttenant: inzender en bijdrager log analytics.

```json
{
    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
    "principalIdDisplayName": "Policy Automation Account",
    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
    "delegatedRoleDefinitionIds": [
         "b24988ac-6180-42a0-ab88-20f7382dd24c",
         "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
    ]
}
```

## <a name="deploy-policies-that-can-be-remediated"></a>Beleidsregels implementeren die kunnen worden gesaneerd

Zodra u de gebruiker hebt gemaakt met de nodige machtigingen zoals hierboven beschreven, kan die gebruiker beleidsregels implementeren in de klanttenant die hersteltaken gebruiken.

Stel dat u diagnostische gegevens wilt inschakelen op Azure Key Vault-resources in de tenant van de klant, zoals geïllustreerd in dit [voorbeeld.](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) Een gebruiker in de beheertenant met de juiste machtigingen (zoals hierboven beschreven) zou een [Azure Resource Manager-sjabloon](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring/enforceAzureMonitoredKeyVault.json) implementeren om dit scenario in te schakelen.

Houd er rekening mee dat het maken van de beleidstoewijzing die moet worden gebruikt met een gedelegeerd abonnement, momenteel moet worden uitgevoerd via API's, niet in de Azure-portal. Daarbij moet de **apiVersion** zijn ingesteld op **2019-04-01-preview,** inclusief de eigenschap nieuwe **delegatedManagedIdentityResourceId.** Met deze eigenschap u een beheerde identiteit opnemen die zich in de tenant van de klant bevindt (in een abonnement of brongroep die is aangesloten bij azure-gedelegeerd bronbeheer).

In het volgende voorbeeld wordt een roltoewijzing met een **gedelegeerdManagedIdentityResourceId weergegeven.**

```json
"type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2019-04-01-preview",
            "name": "[parameters('rbacGuid')]",
            "dependsOn": [
                "[variables('policyAssignment')]"
            ],
            "properties": {
                "roleDefinitionId": "[concat(subscription().id, '/providers/Microsoft.Authorization/roleDefinitions/', variables('rbacContributor'))]",
                "principalType": "ServicePrincipal",
                "delegatedManagedIdentityResourceId": "[concat(subscription().id, '/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment'))]",
                "principalId": "[toLower(reference(concat('/providers/Microsoft.Authorization/policyAssignments/', variables('policyAssignment')), '2018-05-01', 'Full' ).identity.principalId)]"
            }
```

> [!TIP]
> Een [soortgelijk voorbeeld](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) is beschikbaar om aan te tonen hoe u een beleid implementeert of verwijdert dat een tag toevoegt of verwijdert (met het wijzigingseffect) naar een gedelegeerd abonnement.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure-beleid](../../governance/policy/index.yml).
- Meer informatie over [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md).

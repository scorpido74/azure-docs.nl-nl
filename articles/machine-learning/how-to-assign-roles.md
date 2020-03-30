---
title: Rollen in uw werkruimte beheren
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u toegang krijgt tot een Azure Machine Learning-werkruimte met behulp van rbac (Role-based access control).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270093"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Toegang tot een Azure Machine Learning-werkruimte beheren
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u de toegang tot een Azure Machine Learning-werkruimte beheert. [RBAC (Role-based access control)](/azure/role-based-access-control/overview) wordt gebruikt om de toegang tot Azure-bronnen te beheren. Gebruikers in uw Azure Active Directory krijgen specifieke rollen toegewezen, waarmee ze toegang krijgen tot bronnen. Azure biedt zowel ingebouwde rollen als de mogelijkheid om aangepaste rollen te maken.

## <a name="default-roles"></a>Standaardrollen

Een Azure Machine Learning-werkruimte is een Azure-bron. Net als andere Azure-resources wordt bij het maken van een nieuwe Azure Machine Learning-werkruimte drie standaardrollen geleverd. U gebruikers aan de werkruimte toevoegen en deze toewijzen aan een van deze ingebouwde rollen.

| Rol | Toegangsniveau |
| --- | --- |
| **Lezer** | Alleen-lezen acties in de werkruimte. Lezers kunnen assets in een werkruimte aanbieden en weergeven, maar kunnen deze elementen niet maken of bijwerken. |
| **Inzender** | Elementen in een werkruimte weergeven, maken, bewerken of verwijderen (indien van toepassing). Bijdragers kunnen bijvoorbeeld een experiment maken, een compute cluster maken of koppelen, een run verzenden en een webservice implementeren. |
| **Eigenaar** | Volledige toegang tot de werkruimte, inclusief de mogelijkheid om (indien van toepassing) elementen in een werkruimte weer te geven, te maken, te bewerken of te verwijderen. Daarnaast u roltoewijzingen wijzigen. |

> [!IMPORTANT]
> Roltoegang kan worden beperkt tot meerdere niveaus in Azure. Iemand met eigenaartoegang tot een werkruimte heeft bijvoorbeeld geen eigenaartoegang tot de brongroep die de werkruimte bevat. Zie [Hoe RBAC werkt](/azure/role-based-access-control/overview#how-rbac-works)voor meer informatie.

Zie [Ingebouwde rollen voor Azure voor](/azure/role-based-access-control/built-in-roles)meer informatie over specifieke ingebouwde rollen.

## <a name="manage-workspace-access"></a>Werkruimtetoegang beheren

Als u eigenaar bent van een werkruimte, u rollen voor de werkruimte toevoegen en verwijderen. U ook rollen toewijzen aan gebruikers. Gebruik de volgende koppelingen om te ontdekken hoe u de toegang beheren:
- [Gebruikersinterface van Azure-portal](/azure/role-based-access-control/role-assignments-portal)
- [Powershell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure-CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST-API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager-sjablonen](/azure/role-based-access-control/role-assignments-template)

Als u de [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md)hebt geïnstalleerd, u ook een OPDRACHT CLI gebruiken om rollen toe te wijzen aan gebruikers.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Het `user` veld is het e-mailadres van een bestaande gebruiker in het exemplaar van Azure Active Directory waar het abonnement voor de ouder van de werkruimte leeft. Hier volgt een voorbeeld van het gebruik van deze opdracht:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Aangepast rol maken

Als de ingebouwde rollen onvoldoende zijn, u aangepaste rollen maken. Aangepaste rollen hebben mogelijk machtigingen voor lezen, schrijven, verwijderen en berekenen van resources in die werkruimte. U de rol beschikbaar maken op een specifiek werkplekniveau, een specifiek resourcegroepniveau of een specifiek abonnementsniveau.

> [!NOTE]
> U moet eigenaar zijn van de resource op dat niveau om aangepaste rollen binnen die resource te maken.

Als u een aangepaste rol wilt maken, maakt u eerst een JSON-bestand voor roldefinitie waarin de machtiging en het bereik voor de rol wordt opgegeven. In het volgende voorbeeld wordt een aangepaste rol gedefinieerd met de naam 'Gegevenswetenschapper' die op een specifiek werkruimteniveau is uitgevoerd:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

U `AssignableScopes` het veld wijzigen om het bereik van deze aangepaste rol in te stellen op abonnementsniveau, het niveau van de resourcegroep of een specifiek werkruimteniveau.

Deze aangepaste rol kan alles in de werkruimte doen, behalve voor de volgende acties:

- Het kan geen compute resource maken of bijwerken.
- Een compute resource kan niet worden verwijderd.
- Het kan roltoewijzingen niet toevoegen, verwijderen of wijzigen.
- De werkruimte kan niet worden verwijderd.

Als u deze aangepaste rol wilt implementeren, gebruikt u de volgende opdracht Azure CLI:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Na implementatie wordt deze rol beschikbaar in de opgegeven werkruimte. U deze rol nu toevoegen en toewijzen in de Azure-portal. U deze rol ook aan een `az ml workspace share` gebruiker toewijzen met de opdracht CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Zie Aangepaste rollen voor [Azure-resources voor](/azure/role-based-access-control/custom-roles)meer informatie over aangepaste rollen.

Zie [Resourceprovideroperations](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)voor meer informatie over de bewerkingen (acties) die kunnen worden gebruikt voor aangepaste rollen.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>V. Wat zijn de machtigingen die nodig zijn om verschillende acties uit te voeren in de Azure Machine Learning-service?

De volgende tabel is een overzicht van Azure Machine Learning-activiteiten en de machtigingen die nodig zijn om deze op het minste bereik uit te voeren. Als voorbeeld als een activiteit kan worden uitgevoerd met een werkruimtebereik (kolom 4), werkt alle hogere scope met die machtiging ook automatisch. Alle paden in deze tabel `Microsoft.MachineLearningServices/`zijn relatieve **paden** naar .

| Activiteit | Bereik op abonnementsniveau | Scope op resourcegroepniveau | Bereik op werkruimteniveau |
|---|---|---|---|
| Nieuwe werkruimte maken | Niet vereist | Eigenaar of bijdrager | N/A (wordt eigenaar of erft een grotere scoperol na creatie) |
| Nieuw compute cluster maken | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol die het mogelijk maakt:`workspaces/computes/write` |
| Nieuwe notebook-vm maken | Niet vereist | Eigenaar of bijdrager | Niet mogelijk |
| Nieuwe rekeninstantie maken | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol die het mogelijk maakt:`workspaces/computes/write` |
| Activiteit van gegevensvlak, zoals het indienen van run, toegang tot gegevens, implementeren van model of publicatiepijplijn | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol die het mogelijk maakt:`workspaces/*/write` <br/> Houd er rekening mee dat u ook een gegevensarchief nodig hebt dat is geregistreerd bij de werkruimte, zodat MSI toegang heeft tot gegevens in uw opslagaccount. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>V. Hoe kan ik alle aangepaste rollen in mijn abonnement weergeven?

Voer in de Azure CLI de volgende opdracht uit.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>V. Hoe vind ik de roldefinitie voor een rol in mijn abonnement?

Voer in de Azure CLI de volgende opdracht uit. Houd `<role-name>` er rekening mee dat moet worden in dezelfde indeling geretourneerd door de opdracht hierboven.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>V. Hoe werk ik een roldefinitie bij?

Voer in de Azure CLI de volgende opdracht uit.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Houd er rekening mee dat u machtigingen moet hebben voor het volledige bereik van uw nieuwe roldefinitie. Als deze nieuwe rol bijvoorbeeld een bereik heeft voor drie abonnementen, moet u machtigingen hebben voor alle drie de abonnementen. 

> [!NOTE]
> Rolupdates kunnen 15 minuten tot een uur duren om alle roltoewijzingen in dat bereik toe te passen.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>V. Kan ik een rol definiëren die het bijwerken van de workspace Edition voorkomt? 

Ja, u een rol definiëren die het bijwerken van de workspace Edition voorkomt. Aangezien de werkruimte-update een PATCH-aanroep op het werkruimteobject is, doet u dit door de volgende actie in de `"NotActions"` array in uw JSON-definitie te plaatsen: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>V. Welke machtigingen zijn nodig om quotabewerkingen in een werkruimte uit te voeren? 

U hebt machtigingen op abonnementsniveau nodig om een quotagerelateerde bewerking in de werkruimte uit te voeren. Dit betekent dat het instellen van een quotum op abonnementsniveau of quotum op werkniveau voor uw beheerde rekenbronnen alleen kan plaatsvinden als u schrijfmachtigingen hebt in het abonnementsbereik. 


## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Enterprise-beveiliging](concept-enterprise-security.md)
- [Experimenten en gevolgtrekkingen binnen een virtueel netwerk veilig uitvoeren](how-to-enable-virtual-network.md)
- [Zelfstudie: Modellen trainen](tutorial-train-models-with-aml.md)
- [Resourceproviderbewerkingen](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)

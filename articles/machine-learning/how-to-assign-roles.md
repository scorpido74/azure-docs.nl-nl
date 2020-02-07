---
title: Rollen in uw werk ruimte beheren
titleSuffix: Azure Machine Learning
description: Meer informatie over toegang tot een Azure Machine Learning-werk ruimte met behulp van op rollen gebaseerd toegangs beheer (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5257d9f94f6304c2a8dbea3f1648a71d0ba65e94
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064747"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Toegang tot een Azure Machine Learning-werk ruimte beheren
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u de toegang tot een Azure Machine Learning-werk ruimte beheert. [Op rollen gebaseerd toegangs beheer (RBAC)](/azure/role-based-access-control/overview) wordt gebruikt om de toegang tot Azure-resources te beheren. Gebruikers in uw Azure Active Directory krijgen specifieke rollen toegewezen, waarmee toegang tot resources wordt verleend. Azure biedt zowel ingebouwde rollen als de mogelijkheid om aangepaste rollen te maken.

## <a name="default-roles"></a>Standaard rollen

Een Azure Machine Learning-werk ruimte is een Azure-resource. Net als bij andere Azure-resources geldt dat als er een nieuwe Azure Machine Learning-werk ruimte wordt gemaakt, deze drie standaard rollen bevat. U kunt gebruikers toevoegen aan de werk ruimte en ze toewijzen aan een van deze ingebouwde rollen.

| Rol | Toegangsniveau |
| --- | --- |
| **Lezer** | Alleen-lezen acties in de werk ruimte. Lezers kunnen assets in een werk ruimte vermelden en weer geven, maar kunnen deze assets niet maken of bijwerken. |
| **Inzender** | Activa in een werk ruimte weer geven, maken, bewerken of verwijderen (indien van toepassing). Inzenders kunnen bijvoorbeeld een experiment maken, een berekenings cluster maken of koppelen, een run verzenden en een webservice implementeren. |
| **Eigenaar** | Volledige toegang tot de werk ruimte, met inbegrip van de mogelijkheid om activa in een werk ruimte weer te geven, te maken, te bewerken of te verwijderen (indien van toepassing). Daarnaast kunt u roltoewijzingen wijzigen. |

> [!IMPORTANT]
> De toegang tot rollen kan worden beperkt tot meerdere niveaus in Azure. Bijvoorbeeld: iemand met een eigenaar van de gebruikers toegang tot een werk ruimte heeft geen eigenaars toegang tot de resource groep die de werk ruimte bevat. Zie [How RBAC werkt](/azure/role-based-access-control/overview#how-rbac-works)voor meer informatie.

Zie [ingebouwde rollen voor Azure](/azure/role-based-access-control/built-in-roles)voor meer informatie over specifieke ingebouwde rollen.

## <a name="manage-workspace-access"></a>Toegang tot de werk ruimte beheren

Als u eigenaar bent van een werk ruimte, kunt u rollen toevoegen en verwijderen voor de werk ruimte. U kunt ook rollen toewijzen aan gebruikers. Gebruik de volgende koppelingen om te ontdekken hoe u de toegang beheert:
- [Azure Portal gebruikers interface](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST-API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager sjablonen](/azure/role-based-access-control/role-assignments-template)

Als u de [Azure machine learning cli](reference-azure-machine-learning-cli.md)hebt geïnstalleerd, kunt u ook een CLI-opdracht gebruiken om rollen toe te wijzen aan gebruikers.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Het `user` veld is het e-mail adres van een bestaande gebruiker in het exemplaar van Azure Active Directory waar het bovenliggende abonnement van de werk ruimte woont. Hier volgt een voor beeld van het gebruik van deze opdracht:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Aangepast rol maken

Als de ingebouwde rollen onvoldoende zijn, kunt u aangepaste rollen maken. Aangepaste rollen kunnen Lees-, schrijf-, verwijder-en reken resource machtigingen hebben in die werk ruimte. U kunt de rol beschikbaar maken op een specifiek werk ruimte niveau, op een specifiek niveau van een resource groep of op een specifiek abonnements niveau.

> [!NOTE]
> U moet een eigenaar van de resource op dat niveau zijn om aangepaste rollen te maken binnen die resource.

Als u een aangepaste rol wilt maken, maakt u eerst een JSON-bestand met een roldefinitie waarmee u de machtiging en het bereik voor de rol opgeeft. In het volgende voor beeld wordt een aangepaste rol met de naam ' data wetenschapper ' gedefinieerd op een specifiek niveau van de werk ruimte:

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

U kunt het `AssignableScopes` veld wijzigen om het bereik van deze aangepaste rol in te stellen op abonnements niveau, op het niveau van de resource groep of op een bepaald niveau van de werk ruimte.

Deze aangepaste rol kan alles in de werk ruimte doen, met uitzonde ring van de volgende acties:

- Het is niet mogelijk om een compute-resource te maken of bij te werken.
- Er kan geen reken resource worden verwijderd.
- Er kunnen geen roltoewijzingen worden toegevoegd, verwijderd of gewijzigd.
- De werk ruimte kan niet worden verwijderd.

Gebruik de volgende Azure CLI-opdracht om deze aangepaste functie te implementeren:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Na de implementatie wordt deze rol beschikbaar in de opgegeven werk ruimte. U kunt deze rol nu toevoegen en toewijzen in de Azure Portal. U kunt deze rol ook toewijzen aan een gebruiker met behulp van de `az ml workspace share` CLI-opdracht:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Zie [aangepaste rollen voor Azure-resources](/azure/role-based-access-control/custom-roles)voor meer informatie over aangepaste rollen.

Zie bewerkingen voor de [resource provider](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)voor meer informatie over de bewerkingen (acties) die kunnen worden gebruikt met aangepaste rollen.


## <a name="frequently-asked-questions"></a>Veelgestelde vragen


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>V. Wat zijn de machtigingen die nodig zijn om verschillende acties uit te voeren in de Azure Machine Learning-service?

De volgende tabel bevat een overzicht van Azure Machine Learning activiteiten en de machtigingen die nodig zijn om ze uit te voeren op het minste bereik. Als een voor beeld van een activiteit kan worden uitgevoerd met een werkruimte bereik (kolom 4), zal alle hogere bereiken met die machtiging ook automatisch werken. Alle paden in deze tabel zijn **relatieve paden** naar `Microsoft.MachineLearningServices/`.

| Activiteit | Bereik op abonnements niveau | Bereik van resource groeps niveau | Bereik op werkruimte niveau |
|---|---|---|---|
| Nieuwe werk ruimte maken | Niet vereist | Eigenaar of bijdrager | N.v.t. (wordt eigenaar of neemt de hogere bereik functie over nadat deze is gemaakt) |
| Nieuw reken cluster maken | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `workspaces/computes/write` |
| Nieuwe VM van notebook maken | Niet vereist | Eigenaar of bijdrager | Niet mogelijk |
| Nieuw reken exemplaar maken | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `workspaces/computes/write` |
| Activiteit voor gegevens vlak zoals het verzenden van een uitvoering, toegang tot gegevens, het implementeren van een model of publicatie pijplijn | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor: `workspaces/*/write` <br/> U hebt ook een gegevens opslag die is geregistreerd in de werk ruimte, zodat MSI toegang heeft tot gegevens in uw opslag account. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>V. Hoe kan ik alle aangepaste rollen in mijn abonnement weer geven?

Voer de volgende opdracht uit in de Azure CLI.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>V. De roldefinitie voor een rol in mijn abonnement Hoe kan ik vinden?

Voer de volgende opdracht uit in de Azure CLI. Houd er rekening mee dat `<role-name>` dezelfde indeling moet hebben als wordt geretourneerd door de bovenstaande opdracht.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>V. Een roldefinitie Hoe kan ik bijwerken?

Voer de volgende opdracht uit in de Azure CLI.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Houd er rekening mee dat u machtigingen hebt voor het hele bereik van de nieuwe functie definitie. Als deze nieuwe rol bijvoorbeeld een bereik heeft over drie abonnementen, moet u machtigingen hebben voor alle drie de abonnementen. 

> [!NOTE]
> Het kan 15 minuten tot een uur duren voordat de functie-updates zijn toegepast op alle roltoewijzingen in dat bereik.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>V. Kan ik een rol definiëren waarmee wordt voor komen dat de werkruimte editie wordt bijgewerkt? 

Ja, u kunt een rol definiëren die voor komt dat de werkruimte editie wordt bijgewerkt. Omdat het bijwerken van de werk ruimte een PATCH oproep in het werkruimte object is, doet u dit door de volgende actie in de `"NotActions"`-matrix in uw JSON-definitie te plaatsen: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>V. Welke machtigingen zijn er nodig om quotum bewerkingen in een werk ruimte uit te voeren? 

U hebt machtigingen op abonnements niveau nodig voor het uitvoeren van een gerelateerde bewerking op quota in de werk ruimte. Dit betekent dat het instellen van quotum op abonnements niveau of quotum voor het werkruimte niveau voor uw beheerde reken resources alleen kan optreden als u schrijf machtigingen hebt voor het abonnements bereik. 


## <a name="next-steps"></a>Volgende stappen

- [Overzicht van ENTER prise Security](concept-enterprise-security.md)
- [Veilig experimenten en demijnen/Score in een virtueel netwerk uitvoeren](how-to-enable-virtual-network.md)
- [Zelf studie: modellen trainen](tutorial-train-models-with-aml.md)
- [Bewerkingen van de resource provider](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)

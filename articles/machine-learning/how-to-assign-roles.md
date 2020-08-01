---
title: Rollen in uw werk ruimte beheren
titleSuffix: Azure Machine Learning
description: Meer informatie over toegang tot een Azure Machine Learning-werk ruimte met behulp van op rollen gebaseerd toegangs beheer (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: 6a49497cbe71dddb8ab6e76be9b3679dd62b0cee
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449034"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Toegang tot een Azure Machine Learning-werk ruimte beheren
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u de toegang tot een Azure Machine Learning-werk ruimte beheert. [Op rollen gebaseerd toegangs beheer (RBAC)](/azure/role-based-access-control/overview) wordt gebruikt om de toegang tot Azure-resources te beheren. Gebruikers in uw Azure Active Directory krijgen specifieke rollen toegewezen, waarmee toegang tot resources wordt verleend. Azure biedt zowel ingebouwde rollen als de mogelijkheid om aangepaste rollen te maken.

## <a name="default-roles"></a>Standaard rollen

Een Azure Machine Learning-werkruimte is een Azure-resource. Net als andere Azure-resources, heeft een nieuwe Azure Machine Learning-werkruimte drie standaardrollen wanneer deze wordt gemaakt. U kunt gebruikers toevoegen aan de werk ruimte en ze toewijzen aan een van deze ingebouwde rollen.

| Rol | Toegangsniveau |
| --- | --- |
| **Lezer** | Alleen-lezen acties in de werk ruimte. Lezers kunnen assets weer geven en bekijken, inclusief [Data Store](how-to-access-data.md) -referenties, in een werk ruimte. Lezers kunnen deze assets niet maken of bijwerken. |
| **Inzender** | Activa in een werk ruimte weer geven, maken, bewerken of verwijderen (indien van toepassing). Zo kunnen inzenders een experiment maken, een rekencluster maken of koppelen, een uitvoering starten en een webservice implementeren. |
| **Eigenaar** | Volledige toegang tot de werk ruimte, met inbegrip van de mogelijkheid om activa in een werk ruimte weer te geven, te maken, te bewerken of te verwijderen (indien van toepassing). Daarnaast kunt u roltoewijzingen wijzigen. |
| **Aangepaste rol** | Hiermee kunt u de toegang tot specifieke besturings elementen of gegevenslaag bewerkingen in een werk ruimte aanpassen. Bijvoorbeeld het verzenden van een run, het maken van een compute, het implementeren van een model of het registreren van een gegevensset. |

> [!IMPORTANT]
> De toegang tot rollen kan worden beperkt tot meerdere niveaus in Azure. Bijvoorbeeld: iemand met een eigenaar van de gebruikers toegang tot een werk ruimte heeft geen eigenaars toegang tot de resource groep die de werk ruimte bevat. Zie [How RBAC werkt](/azure/role-based-access-control/overview#how-rbac-works)voor meer informatie.

Zie [ingebouwde rollen voor Azure](/azure/role-based-access-control/built-in-roles)voor meer informatie over specifieke ingebouwde rollen.

## <a name="manage-workspace-access"></a>Toegang tot de werk ruimte beheren

Als u eigenaar bent van een werk ruimte, kunt u rollen toevoegen en verwijderen voor de werk ruimte. U kunt ook rollen toewijzen aan gebruikers. Gebruik de volgende koppelingen om te ontdekken hoe u de toegang beheert:
- [Azure Portal gebruikers interface](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure-CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager-sjablonen](/azure/role-based-access-control/role-assignments-template)

Als u de [Azure machine learning cli](reference-azure-machine-learning-cli.md)hebt geïnstalleerd, kunt u de CLI-opdrachten gebruiken om rollen toe te wijzen aan gebruikers:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Het `user` veld is het e-mail adres van een bestaande gebruiker in het exemplaar van Azure Active Directory waarin het bovenliggende abonnement van de werk ruimte woont. Hier volgt een voor beeld van het gebruik van deze opdracht:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> de opdracht AZ ml Workspace share werkt niet voor een federatief account door Azure Active Directory B2B. Gebruik de Azure UI-Portal in plaats van de opdracht.


## <a name="azure-machine-learning-operations"></a>Azure Machine Learning bewerkingen

Azure Machine Learning ingebouwde acties voor veel bewerkingen en taken. Zie [Azure resource providers-bewerkingen](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)voor een volledige lijst.

## <a name="create-custom-role"></a>Aangepast rol maken

Als de ingebouwde rollen niet voldoen, kunt u aangepaste rollen maken. Aangepaste rollen kunnen Lees-, schrijf-, verwijder-en reken resource machtigingen hebben in die werk ruimte. U kunt de rol beschikbaar maken op een specifiek werk ruimte niveau, op een specifiek niveau van een resource groep of op een specifiek abonnements niveau.

> [!NOTE]
> U moet een eigenaar van de resource op dat niveau zijn om aangepaste rollen te maken binnen die resource.

Als u een aangepaste rol wilt maken, maakt u eerst een JSON-bestand met een roldefinitie waarmee u de machtiging en het bereik voor de rol opgeeft. In het volgende voor beeld wordt een aangepaste functie gedefinieerd met de naam ' data wetenschapper Custom ' op een specifiek niveau van de werk ruimte:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> U kunt het `AssignableScopes` veld wijzigen om het bereik van deze aangepaste rol in te stellen op abonnements niveau, op het niveau van de resource groep of op een bepaald niveau van de werk ruimte.
> De bovenstaande aangepaste rol is slechts een voor beeld. Zie een aantal voorgestelde [aangepaste rollen voor de Azure machine learning-service](#customroles).

Deze aangepaste rol kan alles in de werk ruimte doen, met uitzonde ring van de volgende acties:

- Het is niet mogelijk om een compute-resource te maken of bij te werken.
- Er kan geen reken resource worden verwijderd.
- Er kunnen geen roltoewijzingen worden toegevoegd, verwijderd of gewijzigd.
- De werk ruimte kan niet worden verwijderd.

Gebruik de volgende Azure CLI-opdracht om deze aangepaste functie te implementeren:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Na de implementatie wordt deze rol beschikbaar in de opgegeven werk ruimte. U kunt deze rol nu toevoegen en toewijzen in de Azure Portal. U kunt deze rol ook toewijzen aan een gebruiker met behulp van de `az ml workspace share` cli-opdracht:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Zie aangepaste rollen voor [Azure](/azure/role-based-access-control/custom-roles)voor meer informatie over aangepaste rollen. Zie bewerkingen voor de [resource provider](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)voor meer informatie over de bewerkingen (acties en niet acties) die kunnen worden gebruikt met aangepaste rollen.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>V. Wat zijn de machtigingen die nodig zijn voor het uitvoeren van enkele algemene scenario's in de Azure Machine Learning-service?

De volgende tabel bevat een overzicht van Azure Machine Learning activiteiten en de machtigingen die nodig zijn om ze uit te voeren op het minste bereik. Als een activiteit bijvoorbeeld kan worden uitgevoerd met een werkruimte bereik (kolom 4), zal alle grotere bereiken met die machtiging ook automatisch worden gebruikt:

> [!IMPORTANT]
> Alle paden in deze tabel die beginnen met `/` **relatieve paden** zijn `Microsoft.MachineLearningServices/` :

| Activiteit | Bereik op abonnements niveau | Bereik van resource groeps niveau | Bereik op werkruimte niveau |
| ----- | ----- | ----- | ----- |
| Nieuwe werk ruimte maken | Niet vereist | Eigenaar of bijdrager | N.v.t. (wordt eigenaar of neemt de hogere bereik functie over nadat deze is gemaakt) |
| De versie van de werk ruimte bijwerken | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor:`/workspaces/write` |
| Amlcompute-quotum niveau aanvragen of quotum voor werkruimte niveau instellen | Eigenaar of Inzender, of aangepaste rol </br>kunnen`/locations/updateQuotas/action`</br> bij abonnements bereik | Niet geautoriseerd | Niet geautoriseerd |
| Nieuw reken cluster maken | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor:`/workspaces/computes/write` |
| Nieuw reken exemplaar maken | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor:`/workspaces/computes/write` |
| Elk type uitvoering verzenden | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor:`"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Een pijplijn eindpunt publiceren | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor:`"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Een geregistreerd model implementeren op een AKS/ACI-bron | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor:`"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Score voor een geïmplementeerd AKS-eind punt | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol waardoor: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (wanneer u geen Aad-verificatie gebruikt) of `"/workspaces/read"` (wanneer u token verificatie gebruikt) |
| Toegang tot opslag met interactieve notebooks | Niet vereist | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor:`"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*"` |
| Nieuwe aangepaste rol maken | Eigenaar, bijdrager of aangepaste rol die`Microsoft.Authorization/roleDefinitions/write` | Niet vereist | Eigenaar, bijdrager of aangepaste rol, waardoor:`/workspaces/computes/write` |


### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>V. Publiceren we ingebouwde rollen van Azure voor de Machine Learning-service?

Er worden momenteel geen [ingebouwde rollen van Azure](/azure/role-based-access-control/built-in-roles) gepubliceerd voor de machine learning-service. Een ingebouwde rol na publicatie kan niet worden bijgewerkt en er worden nog steeds de roldefinities op basis van klant scenario's en feedback gefiatteerd. 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>V. Zijn er aangepaste sjablonen voor de meest voorkomende scenario's in Machine Learning service?

Ja hier volgen enkele algemene scenario's met aangepaste roldefinities die u als basis kunt gebruiken om uw eigen aangepaste rollen te definiëren:

* __Data wetenschapper Custom__: Hiermee staat u toe dat een gegevens wetenschapper alle bewerkingen binnen een werk ruimte kan uitvoeren **, met uitzonde ring**van:

    * Maken van compute
    * Modellen implementeren in een productie AKS-cluster
    * Een pijplijn eindpunt implementeren in productie

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Gegevens wetenschapper beperkt aangepast__: een meer beperkte roldefinitie zonder joker tekens in de toegestane acties. Hiermee kunnen alle bewerkingen binnen een werk ruimte worden uitgevoerd **, met uitzonde ring**van:

    * Maken van compute
    * Modellen implementeren in een productie AKS-cluster
    * Een pijplijn eindpunt implementeren in productie

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __MLOps aangepast__: Hiermee kunt u een rol aan een Service-Principal toewijzen en gebruiken om uw MLOps-pijp lijnen te automatiseren. Bijvoorbeeld voor het verzenden van uitvoeringen voor een al gepubliceerde pijp lijn:

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Werkruimte beheerder__: Hiermee kunt u alle bewerkingen binnen het bereik van een werk ruimte uitvoeren, **met uitzonde ring**van:

    * Een nieuwe werk ruimte maken
    * Quota voor abonnements-of werkruimte niveau toewijzen
    * De versie van de werk ruimte bijwerken

    De werkruimte beheerder kan ook geen nieuwe rol maken. U kunt alleen bestaande ingebouwde of aangepaste rollen toewijzen binnen het bereik van hun werk ruimte:

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Labeler aangepast__: Hiermee kunt u een functie bereik definiëren die alleen is gericht op het labelen van gegevens:

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>V. Hoe kan ik alle aangepaste rollen in mijn abonnement weer geven?

Voer de volgende opdracht uit in de Azure CLI.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>V. Hoe kan ik wilt u de bewerkingen vinden die worden ondersteund door de Machine Learning-service?

Voer de volgende opdracht uit in de Azure CLI.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

Ze kunnen ook worden gevonden in de lijst met [bewerkingen van de resource provider](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>V. Wat zijn enkele algemene gotchas bij het gebruik van Azure RBAC?

Hier volgen enkele dingen waarvan u op de hoogte moet zijn terwijl u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruikt:

- Wanneer u een resource maakt in azure, zegt u een werk ruimte, bent u niet rechtstreeks de eigenaar van de werk ruimte. Uw rol wordt overgenomen van de hoogste rol van het bereik waarvoor u gemachtigd bent in dat abonnement. Als u bijvoorbeeld een netwerk beheerder bent en u de machtigingen hebt om een Machine Learning-werk ruimte te maken, wordt u de rol netwerk beheerder toegewezen voor die werk ruimte en niet de rol van eigenaar.
- Wanneer er twee roltoewijzingen aan dezelfde AAD-gebruiker zijn met conflicterende delen van acties/verhoudingen, worden uw bewerkingen die worden vermeld in de ene rol, mogelijk niet van kracht als ze ook worden weer gegeven als acties in een andere rol. Lees [hoe Azure RBAC bepaalt of een gebruiker toegang heeft tot een resource](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource) voor meer informatie over hoe Azure-roltoewijzingen parseert.
- Als u uw reken resources binnen een VNet wilt implementeren, moet u expliciet machtigingen hebben voor ' micro soft. Network/virtualNetworks/join's/Action ' op die VNet-resource.
- Het kan soms tot 1 uur duren voordat de nieuwe roltoewijzingen van kracht worden via de cache machtigingen in de stack.


### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>V. Welke machtigingen heb ik nodig om een door de gebruiker toegewezen beheerde identiteit te gebruiken met mijn Amlcompute-clusters?

Als u een door de gebruiker toegewezen identiteit op Amlcompute-clusters wilt toewijzen, moet u schrijf machtigingen hebben om Compute te maken en de [rol Managed Identity-operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator)hebben. Lees voor meer informatie over RBAC met beheerde identiteiten [hoe u de door de gebruiker toegewezen identiteit beheert](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)


### <a name="q-do-we-support-role-based-access-controls-on-the-studio-portal"></a>V. Bieden we ondersteuning voor op rollen gebaseerd toegangs beheer op de Studio Portal?

Azure Machine Learning Studio ondersteunt op rollen gebaseerd toegangs beheer. 

> [!IMPORTANT]
> Wanneer u een aangepaste rol hebt toegewezen met specifieke machtigingen voor een gegevens wetenschapper in uw werk ruimte, worden de bijbehorende acties (zoals het toevoegen van een compute button) automatisch verborgen voor de gebruikers. Door deze items te verbergen, voor komt u dat Verwar ring wordt weer gegeven die een melding van onbevoegde toegang van de service retour neren als deze wordt gebruikt.

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>V. De roldefinitie voor een rol in mijn abonnement Hoe kan ik vinden?

Voer de volgende opdracht uit in de Azure CLI. De `<role-name>` notatie moet hetzelfde zijn als de indeling die wordt geretourneerd door de bovenstaande opdracht.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>V. Een roldefinitie Hoe kan ik bijwerken?

Voer de volgende opdracht uit in de Azure CLI.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

U moet machtigingen hebben voor het hele bereik van de nieuwe roldefinitie. Als deze nieuwe rol bijvoorbeeld een bereik heeft over drie abonnementen, moet u machtigingen hebben voor alle drie de abonnementen. 

> [!NOTE]
> Het kan 15 minuten tot een uur duren voordat de functie-updates zijn toegepast op alle roltoewijzingen in dat bereik.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>V. Kan ik een rol definiëren waarmee wordt voor komen dat de werkruimte editie wordt bijgewerkt? 

Ja, u kunt een rol definiëren die voor komt dat de werkruimte editie wordt bijgewerkt. Omdat het bijwerken van de werk ruimte een PATCH oproep in het werkruimte object is, doet u dit door de volgende actie in de `"NotActions"` matrix in de JSON-definitie te plaatsen: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>V. Welke machtigingen zijn er nodig om quotum bewerkingen in een werk ruimte uit te voeren? 

U hebt machtigingen op abonnements niveau nodig om aan quota gerelateerde bewerkingen in de werk ruimte uit te voeren. Dit betekent dat het instellen van quotum op abonnements niveau of quotum voor het werkruimte niveau voor uw beheerde reken resources alleen kan optreden als u schrijf machtigingen hebt voor het abonnements bereik. 


## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Enterprise-beveiliging](concept-enterprise-security.md)
- [Veilig experimenten en demijnen/Score in een virtueel netwerk uitvoeren](how-to-enable-virtual-network.md)
- [Zelf studie: modellen trainen](tutorial-train-models-with-aml.md)
- [Resourceproviderbewerkingen](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)

---
title: Een klant in gedelegeerd Azure-resourcebeheer plaatsen
description: Meer informatie over hoe u een klant kunt vrijmaken voor het beheer van Azure-resources, zodat deze toegankelijk is en kan worden beheerd via uw eigen Tenant.
ms.date: 05/26/2020
ms.topic: how-to
ms.openlocfilehash: dc8156307a91a0d9fd598f330b4fbc28223e4483
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636543"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Een klant in gedelegeerd Azure-resourcebeheer plaatsen

In dit artikel wordt uitgelegd hoe u, als service provider, een klant kan opsturen naar een Azure-gedelegeerd resource beheer, zodat de gedelegeerde resources (abonnementen en/of resource groepen) kunnen worden geopend en beheerd via uw eigen Azure Active Directory (Azure AD)-Tenant. Hoewel we hier naar service providers en klanten verwijzen, kunnen [bedrijven die meerdere tenants beheren](../concepts/enterprise.md) , hetzelfde proces gebruiken voor het instellen van Azure Lighthouse en het samen voegen van hun beheer ervaring.

U kunt dit proces herhalen als u resources beheert voor meerdere klanten. Wanneer een geautoriseerde gebruiker zich aanmeldt bij uw Tenant, kan die gebruiker worden geautoriseerd door de klant pacht bereiken om beheer bewerkingen uit te voeren zonder dat ze zich hoeven aan te melden bij elke afzonderlijke klant Tenant.

Als u de gevolgen voor klant afspraken wilt bijhouden en de herkenning wilt ontvangen, koppelt u uw Microsoft Partner Network-ID (MPN) aan ten minste één gebruikers account dat toegang heeft tot elk van de kant-en-Board abonnementen. Houd er rekening mee dat u deze koppeling moet uitvoeren in de Tenant van de service provider. Ter vereenvoudiging raden we u aan een Service-Principal-account te maken in uw Tenant die is gekoppeld aan uw MPN-ID, en de IT-lezer toegang te geven tot elke klant die u uitschakelt. Zie [een partner-id koppelen aan uw Azure-accounts](../../billing/billing-partner-admin-link-started.md)voor meer informatie. 

> [!NOTE]
> Klanten kunnen ook worden voorbereidd wanneer ze een Managed Services-aanbieding (openbaar of privé) aanschaffen die u naar Azure Marketplace hebt gepubliceerd. Zie [Managed Services aanbiedingen to Azure Marketplace](publish-managed-services-offers.md)(Engelstalig) voor meer informatie. U kunt ook het voorbereidings proces gebruiken dat hier wordt beschreven, samen met een aanbieding die is gepubliceerd op Azure Marketplace.

Voor het voorbereidings proces moeten acties worden uitgevoerd vanuit zowel de Tenant van de service provider als van de Tenant van de klant. Al deze stappen worden in dit artikel beschreven.

## <a name="gather-tenant-and-subscription-details"></a>Tenant-en abonnements gegevens verzamelen

Voor een onboarding van de Tenant van een klant moet deze een actief Azure-abonnement hebben. U moet het volgende weten:

- De Tenant-ID van de Tenant van de service provider (waar u de resources van de klant gaat beheren)
- De Tenant-ID van de Tenant van de klant (die resources heeft die worden beheerd door de service provider)
- De abonnements-Id's voor elk specifiek abonnement in de Tenant van de klant die worden beheerd door de service provider (of die de resource groep (en) bevat die worden beheerd door de service provider).

> [!NOTE]
> Zelfs als u slechts een of meer resource groepen wilt vrijgeven binnen een abonnement, moet de implementatie worden uitgevoerd op het abonnements niveau, dus u hebt de abonnements-ID nodig.

Als u deze ID-waarden al niet hebt, kunt u ze op een van de volgende manieren ophalen. Zorg ervoor dat u deze exacte waarden in uw implementatie gebruikt.

### <a name="azure-portal"></a>Azure Portal

Uw Tenant-ID kan worden gezien door de muis aanwijzer over de naam van uw account in de rechter bovenhoek van de Azure Portal te bewegen of door een **andere map**te selecteren. Als u uw Tenant-ID wilt selecteren en kopiëren, zoekt u in de portal naar ' Azure Active Directory ' en selecteert u vervolgens **Eigenschappen** en kopieert u de waarde die wordt weer gegeven in het veld **Directory-id** . Als u de ID van een abonnement in de Tenant van de klant wilt zoeken, zoekt u naar ' Abonnementen ' en selecteert u vervolgens de juiste abonnements-ID.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Wanneer u een abonnement (of een of meer resource groepen binnen een abonnement) gebruikt met behulp van het proces dat hier wordt beschreven, wordt de resource provider **micro soft. ManagedServices** geregistreerd voor dat abonnement.

## <a name="define-roles-and-permissions"></a>Rollen en machtigingen definiëren

Als service provider wilt u mogelijk meerdere taken uitvoeren voor één klant, waarbij verschillende toegangs rechten voor verschillende bereiken zijn vereist. U kunt zoveel autorisaties definiëren als u nodig hebt om op [rollen gebaseerd toegangs beheer (RBAC) ingebouwde rollen](../../role-based-access-control/built-in-roles.md) toe te wijzen aan gebruikers in uw Tenant.

Om het beheer te vereenvoudigen, raden we u aan Azure AD-gebruikers groepen te gebruiken voor elke rol, zodat u afzonderlijke gebruikers aan de groep kunt toevoegen of verwijderen, in plaats van machtigingen rechtstreeks aan die gebruiker toe te wijzen. Mogelijk wilt u ook rollen toewijzen aan een service-principal. Zorg ervoor dat u het principe van minimale bevoegdheden volgt, zodat gebruikers alleen over de benodigde machtigingen beschikken om hun taak te volt ooien. Zie [tenants, gebruikers en rollen in azure Lighthouse-scenario's](../concepts/tenants-users-roles.md)voor aanbevelingen en informatie over ondersteunde rollen.

> [!IMPORTANT]
> Om machtigingen voor een Azure AD-groep toe te voegen, moet het **groeps type** **beveiliging** zijn en niet **Office 365**. Deze optie wordt geselecteerd wanneer de groep wordt gemaakt. Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie.

Als u autorisaties wilt definiëren, moet u de ID-waarden weten voor elke gebruiker, gebruikers groep of Service-Principal in de Tenant van de service provider waaraan u toegang wilt verlenen. U hebt ook de roldefinitie-ID nodig voor elke ingebouwde rol die u wilt toewijzen. Als u deze niet al hebt, kunt u deze ophalen door de onderstaande opdrachten uit te voeren in de Tenant van de service provider.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> U kunt het beste de functie voor het verwijderen van de [registratie toewijzing beheerde services](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) toewijzen bij het voorbereiden van een klant, zodat gebruikers in uw Tenant [de toegang tot de overdracht](remove-delegation.md) later indien nodig kunnen verwijderen. Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de Tenant van de klant.

## <a name="create-an-azure-resource-manager-template"></a>Een Azure Resource Manager sjabloon maken

Voor het onboarden van uw klant moet u een [Azure Resource Manager](../../azure-resource-manager/index.yml) -sjabloon maken voor uw aanbieding met de volgende gegevens. De **mspOfferName** -en **mspOfferDescription** -waarden zijn zichtbaar voor de klant wanneer u details van de aanbieding bekijkt op de [pagina service providers](view-manage-service-providers.md) van de Azure Portal.

|Veld  |Definitie  |
|---------|---------|
|**mspOfferName**     |Een naam die deze definitie beschrijft. Deze waarde wordt weer gegeven aan de klant als de titel van de aanbieding.         |
|**mspOfferDescription**     |Een korte beschrijving van uw aanbieding (bijvoorbeeld ' contoso VM-beheer aanbieding ').      |
|**managedByTenantId**     |Uw Tenant-ID.          |
|**autorisaties**     |De **principalId** -waarden voor de gebruikers/groepen/spn's van uw Tenant, elk met een **principalIdDisplayName** om uw klant te helpen het doel van de autorisatie te begrijpen, en toegewezen aan een ingebouwde **roledefinitionid hebben** -waarde om het toegangs niveau op te geven.      |

Voor het voorbereidings proces is een Azure Resource Manager-sjabloon vereist (opgegeven in onze voor [beelden opslag plaats](https://github.com/Azure/Azure-Lighthouse-samples/)) en een bijbehorend parameter bestand dat u wijzigt zodat dit overeenkomt met uw configuratie en uw autorisaties definieert.

De sjabloon die u kiest, is afhankelijk van het feit of u een volledig abonnement, een resource groep of meerdere resource groepen in een abonnement hebt voor bereid. We bieden ook een sjabloon die kan worden gebruikt voor klanten die een beheerde service aanbieding hebben gekocht die u naar Azure Marketplace hebt gepubliceerd, als u uw abonnement (en) op deze manier wilt vrijgeven.

|Als u dit wilt onboarden  |Deze Azure Resource Manager sjabloon gebruiken  |En dit parameter bestand wijzigen |
|---------|---------|---------|
|Abonnement   |[delegatedResourceManagement. json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement. para meters. json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resourcegroep   |[rgDelegatedResourceManagement. json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement. para meters. json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Meerdere resource groepen binnen een abonnement   |[multipleRgDelegatedResourceManagement. json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement. para meters. json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Abonnement (wanneer u een aanbieding gebruikt die is gepubliceerd op Azure Marketplace)   |[marketplaceDelegatedResourceManagement. json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement. para meters. json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Voor het proces dat hier wordt beschreven, is een afzonderlijke implementatie op abonnements niveau vereist voor elk abonnement dat wordt voorbereid, zelfs als u abonnementen op de werk belasting van de klant onboarding hebt. Er zijn ook afzonderlijke implementaties vereist als u meerdere resource groepen binnen verschillende abonnementen in dezelfde Tenant van de klant wilt voorbereiden. Het voorbereiden van meerdere resource groepen binnen één abonnement kan echter worden uitgevoerd op één implementatie op abonnements niveau.
>
> Er zijn ook afzonderlijke implementaties vereist voor meerdere aanbiedingen die worden toegepast op hetzelfde abonnement (of resource groepen binnen een abonnement). Elke aanbieding die wordt toegepast, moet een andere **mspOfferName**gebruiken.

In het volgende voor beeld ziet u een aangepast **delegatedResourceManagement. para meters. json** -bestand dat kan worden gebruikt voor het onboarden van een abonnement. De parameter bestanden van de resource groep (in de map [RG-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) ) zijn vergelijkbaar, maar bevatten ook een **rgName** -para meter om de specifieke resource groep (en) te identificeren die onboarding moet worden uitgevoerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

De laatste autorisatie in bovenstaand voor beeld voegt een **principalId** toe aan de rol voor gebruikers toegang (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Wanneer u deze rol toewijst, moet u de eigenschap **delegatedRoleDefinitionIds** en een of meer ingebouwde rollen toevoegen. De gebruiker die in deze autorisatie is gemaakt, kan deze ingebouwde rollen toewijzen aan [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) in de Tenant van de klant, wat vereist is om [beleid te implementeren dat kan worden hersteld](deploy-policy-remediation.md). Geen enkele andere machtigingen die normaal gesp roken zijn gekoppeld aan de rol beheerder van gebruikers toegang, zijn van toepassing op deze gebruiker.

## <a name="deploy-the-azure-resource-manager-templates"></a>De Azure Resource Manager-sjablonen implementeren

Zodra u het parameter bestand hebt bijgewerkt, moet een gebruiker in de Tenant van de klant de Azure Resource Manager-sjabloon binnen hun Tenant implementeren als implementatie op abonnements niveau. Er is een afzonderlijke implementatie nodig voor elk abonnement dat u wilt voorbereiden op het beheer van Azure-gedelegeerde resources (of voor elk abonnement dat resource groepen bevat die u wilt voorbereiden).

Omdat dit een implementatie op abonnements niveau is, kan deze niet worden gestart in de Azure Portal. De implementatie kan worden uitgevoerd met behulp van Power shell of Azure CLI, zoals hieronder wordt weer gegeven.

> [!IMPORTANT]
> Deze implementatie op abonnementniveau moet worden uitgevoerd door een niet-gast account in de Tenant van de klant met de [ingebouwde rol](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) van de eigenaar van het abonnement (of de resource groepen die worden uitgevoerd). Als u alle gebruikers wilt zien die het abonnement kunnen delegeren, kan een gebruiker in de Tenant van de klant het abonnement selecteren in de Azure Portal, **toegangs beheer openen (IAM)** en [alle gebruikers met de rol eigenaar weer geven](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).
>
> Als het abonnement is gemaakt met behulp van het [programma Cloud Solution Provider (CSP)](../concepts/cloud-solution-provider.md), kan elke gebruiker met de rol [beheerder](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) in de Tenant van de service provider de implementatie uitvoeren.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Geslaagde onboarding bevestigen

Wanneer het abonnement van een klant is opgeheven voor het Azure-gedelegeerde resource beheer, kunnen gebruikers in de Tenant van de service provider het abonnement en de bijbehorende resources zien (als zij toegang hebben gekregen via het bovenstaande proces, hetzij afzonderlijk of als lid van een Azure AD-groep met de juiste machtigingen). Als u dit wilt bevestigen, controleert u of het abonnement op een van de volgende manieren wordt weer gegeven.  

### <a name="azure-portal"></a>Azure Portal

In de Tenant van de service provider:

1. Navigeer naar de [pagina mijn klanten](view-manage-customers.md).
2. Selecteer **klanten**.
3. Bevestig dat u de abonnement/abonnementen kunt zien met de naam van de aanbieding die u in de Resource Manager-sjabloon hebt opgenomen.

> [!IMPORTANT]
> Als u het gedelegeerde abonnement in [mijn klanten](view-manage-customers.md)wilt zien, moeten gebruikers in de Tenant van de service provider beschikken over de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) (of een andere ingebouwde rol die lezers toegang bevat) wanneer het abonnement voor het beheer van de gedelegeerde resources van Azure is uitgevoerd.

In de Tenant van de klant:

1. Navigeer naar de [pagina service providers](view-manage-service-providers.md).
2. Selecteer **aanbiedingen voor de service provider**.
3. Bevestig dat u de abonnement/abonnementen kunt zien met de naam van de aanbieding die u in de Resource Manager-sjabloon hebt opgenomen.

> [!NOTE]
> Het kan enkele minuten duren voordat de implementatie is voltooid voordat de updates in de Azure Portal worden weer gegeven.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ervaring op het beheer van cross-tenants](../concepts/cross-tenant-management-experience.md).
- [Bekijk en beheer klanten](view-manage-customers.md) door naar **mijn klanten** te gaan in de Azure Portal.
- Meer informatie over het [verwijderen van de toegang tot een overdracht](remove-delegation.md) die eerder is uitgevoerd.

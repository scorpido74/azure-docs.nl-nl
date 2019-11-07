---
title: Een klant in gedelegeerd Azure-resourcebeheer plaatsen
description: Meer informatie over hoe u een klant kunt vrijmaken voor het beheer van Azure-resources, zodat deze toegankelijk is en kan worden beheerd via uw eigen Tenant.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 11/6/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 259a0b1b278588ef2237622d61a89fe02e5c004c
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721393"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Een klant in gedelegeerd Azure-resourcebeheer plaatsen

In dit artikel wordt uitgelegd hoe u, als service provider, een klant kan opsturen naar een Azure-gedelegeerd resource beheer, zodat de gedelegeerde resources (abonnementen en/of resource groepen) kunnen worden geopend en beheerd via uw eigen Azure Active Directory ( Azure AD)-Tenant. Hoewel we hier naar service providers en klanten verwijzen, kunnen bedrijven die meerdere tenants beheren, hetzelfde proces gebruiken om hun beheer ervaring te consolideren.

U kunt dit proces herhalen als u resources beheert voor meerdere klanten. Wanneer een geautoriseerde gebruiker zich aanmeldt bij uw Tenant, kan die gebruiker worden geautoriseerd door de klant pacht bereiken om beheer bewerkingen uit te voeren zonder dat ze zich hoeven aan te melden bij elke afzonderlijke klant Tenant.

U kunt uw Microsoft Partner Network-ID (MPN) koppelen aan uw kant-en-Board abonnementen om de gevolgen voor klant afspraken bij te houden en herkenning te ontvangen. Zie [een partner-id koppelen aan uw Azure-accounts](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started)voor meer informatie. Houd er rekening mee dat u deze koppeling moet uitvoeren in de Tenant van de service provider.

> [!NOTE]
> Klanten kunnen ook worden voorbereidd wanneer ze een Managed Services-aanbieding (openbaar of privé) aanschaffen die u naar Azure Marketplace hebt gepubliceerd. Zie [Managed Services aanbiedingen to Azure Marketplace](publish-managed-services-offers.md)(Engelstalig) voor meer informatie. U kunt ook het voorbereidings proces dat hier wordt beschreven, gebruiken met een aanbieding die is gepubliceerd op Azure Marketplace.

Voor het voorbereidings proces moeten acties worden uitgevoerd vanuit zowel de Tenant van de service provider als van de Tenant van de klant. Al deze stappen worden in dit artikel beschreven.

> [!IMPORTANT]
> Op dit moment kunt u geen abonnement (of resource groep binnen een abonnement) voor Azure delegated Resource Management gebruiken als het abonnement gebruikmaakt van Azure Databricks. Als een abonnement is geregistreerd voor onboarding met de resource provider **micro soft. ManagedServices** , kunt u op dit moment geen Databricks-werk ruimte maken voor dat abonnement.

## <a name="gather-tenant-and-subscription-details"></a>Tenant-en abonnements gegevens verzamelen

Voor een onboarding van de Tenant van een klant moet deze een actief Azure-abonnement hebben. U moet het volgende weten:

- De Tenant-ID van de Tenant van de service provider (waar u de resources van de klant gaat beheren)
- De Tenant-ID van de Tenant van de klant (die resources heeft die worden beheerd door de service provider)
- De abonnements-Id's voor elk specifiek abonnement in de Tenant van de klant die worden beheerd door de service provider (of die de resource groep (en) bevat die worden beheerd door de service provider)

Als u deze gegevens niet al hebt, kunt u deze op een van de volgende manieren ophalen.

### <a name="azure-portal"></a>Azure Portal

Uw Tenant-ID kan worden gezien door de muis aanwijzer over de naam van uw account in de rechter bovenhoek van de Azure Portal te bewegen of door een **andere map**te selecteren. Als u uw Tenant-ID wilt selecteren en kopiëren, zoekt u in de portal naar ' Azure Active Directory ' en selecteert u vervolgens **Eigenschappen** en kopieert u de waarde die wordt weer gegeven in het veld **Directory-id** . Als u de ID van een abonnement wilt vinden, zoekt u naar ' Abonnementen ' en selecteert u vervolgens de juiste abonnements-ID.

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

Als service provider wilt u mogelijk meerdere taken uitvoeren voor één klant, waarbij verschillende toegangs rechten voor verschillende bereiken zijn vereist. U kunt zoveel autorisaties definiëren als u nodig hebt om op [rollen gebaseerd toegangs beheer (RBAC) ingebouwde rollen](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) toe te wijzen aan gebruikers in uw Tenant.

Om het beheer te vereenvoudigen, raden we u aan Azure AD-gebruikers groepen te gebruiken voor elke rol, zodat u afzonderlijke gebruikers aan de groep kunt toevoegen of verwijderen, in plaats van machtigingen rechtstreeks aan die gebruiker toe te wijzen. Mogelijk wilt u ook rollen toewijzen aan een service-principal. Zorg ervoor dat u het principe van minimale bevoegdheden volgt, zodat gebruikers alleen over de benodigde machtigingen beschikken om hun taak te volt ooien. Zie [tenants, gebruikers en rollen in azure Lighthouse-scenario's](../concepts/tenants-users-roles.md)voor aanbevelingen en informatie over ondersteunde rollen.

Als u autorisaties wilt definiëren, moet u de ID-waarden weten voor elke gebruiker, gebruikers groep of service-principal waaraan u toegang wilt verlenen. U hebt ook de roldefinitie-ID nodig voor elke ingebouwde rol die u wilt toewijzen. Als u deze niet al hebt, kunt u ze op een van de volgende manieren ophalen.

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
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> U kunt het beste de functie voor het verwijderen van de [registratie toewijzing beheerde services](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) toewijzen bij het voorbereiden van een klant, zodat gebruikers in uw Tenant [de toegang tot de overdracht](#remove-access-to-a-delegation) later indien nodig kunnen verwijderen. Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de Tenant van de klant.

## <a name="create-an-azure-resource-manager-template"></a>Een Azure Resource Manager sjabloon maken

Voor het onboarden van uw klant moet u een [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) -sjabloon maken voor uw aanbieding met de volgende gegevens. De waarden **mspOfferName** en **mspOfferDescription** zijn zichtbaar voor de klant wanneer u details van de aanbieding bekijkt op de [pagina service providers](view-manage-service-providers.md) van de Azure Portal.

|Veld  |Definitie  |
|---------|---------|
|**mspOfferName**     |Een naam die deze definitie beschrijft. Deze waarde wordt weer gegeven aan de klant als de titel van de aanbieding.         |
|**mspOfferDescription**     |Een korte beschrijving van uw aanbieding (bijvoorbeeld ' contoso VM-beheer aanbieding ')      |
|**managedByTenantId**     |Uw Tenant-ID         |
|**autorisaties**     |De **principalId** -waarden voor de gebruikers/groepen/spn's van uw Tenant, elk met een **principalIdDisplayName** om uw klant te helpen het doel van de autorisatie te begrijpen en te koppelen aan een ingebouwde **roledefinitionid hebben** -waarde om de toegangs niveau         |

Als u het abonnement van een klant wilt opzeggen, gebruikt u de juiste Azure Resource Manager-sjabloon die we hebben opgegeven in onze voor [beelden opslag plaats](https://github.com/Azure/Azure-Lighthouse-samples/), samen met een bijbehorend parameter bestand dat u wijzigt zodat dit overeenkomt met uw configuratie en uw autorisaties definieert. Er worden afzonderlijke sjablonen gegeven, afhankelijk van of u een volledig abonnement, een resource groep of meerdere resource groepen in een abonnement hebt voor bereid. We bieden ook een sjabloon die kan worden gebruikt voor klanten die een beheerde service aanbieding hebben gekocht die u naar Azure Marketplace hebt gepubliceerd, als u uw abonnement (en) op deze manier wilt vrijgeven.

|Als u dit wilt onboarden  |Deze Azure Resource Manager sjabloon gebruiken  |En dit parameter bestand wijzigen |
|---------|---------|---------|
|Abonnement   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resourcegroep   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Meerdere resource groepen binnen een abonnement   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Abonnement (wanneer u een aanbieding gebruikt die is gepubliceerd op Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Voor het proces dat hier wordt beschreven, is een afzonderlijke implementatie vereist voor elk abonnement dat wordt uitgevoerd. Er zijn ook afzonderlijke implementaties nodig als u meerdere resource groepen binnen verschillende abonnementen wilt voorbereiden. Het voorbereiden van meerdere resource groepen binnen één abonnement kan echter worden uitgevoerd in één implementatie.
>
> Er zijn ook afzonderlijke implementaties vereist voor meerdere aanbiedingen die worden toegepast op hetzelfde abonnement (of resource groepen binnen een abonnement). Elke aanbieding die wordt toegepast, moet een andere **mspOfferName**gebruiken.

In het volgende voor beeld ziet u het bestand **delegatedResourceManagement. para meters. json** dat wordt gebruikt voor het onboarden van een abonnement. De parameter bestanden van de resource groep (in de map [RG-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) ) zijn vergelijkbaar, maar bevatten ook een **rgName** -para meter om de specifieke resource groep (en) te identificeren die onboarding moet worden uitgevoerd.

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
De laatste autorisatie in bovenstaand voor beeld voegt een **principalId** toe aan de rol voor gebruikers toegang (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Wanneer u deze rol toewijst, moet u de eigenschap **delegatedRoleDefinitionIds** en een of meer ingebouwde rollen toevoegen. De gebruiker die in deze autorisatie is gemaakt, kan deze ingebouwde rollen toewijzen aan [beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), wat vereist is om [beleid te implementeren dat kan worden hersteld](deploy-policy-remediation.md). Geen enkele andere machtigingen die normaal gesp roken zijn gekoppeld aan de rol beheerder van gebruikers toegang, zijn van toepassing op deze gebruiker.

## <a name="deploy-the-azure-resource-manager-templates"></a>De Azure Resource Manager-sjablonen implementeren

Zodra u het parameter bestand hebt bijgewerkt, moet de klant de Azure Resource Manager-sjabloon in de Tenant van de klant implementeren als implementatie op abonnements niveau. Er is een afzonderlijke implementatie nodig voor elk abonnement dat u wilt voorbereiden op het beheer van Azure-gedelegeerde resources (of voor elk abonnement dat resource groepen bevat die u wilt voorbereiden).

Omdat dit een implementatie op abonnements niveau is, kan deze niet worden gestart in de Azure Portal. De implementatie kan worden uitgevoerd met behulp van Power shell of Azure CLI, zoals hieronder wordt weer gegeven.

> [!IMPORTANT]
> De implementatie moet worden uitgevoerd door een niet-gast account in de Tenant van de klant die de [ingebouwde rol](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) van de eigenaar heeft voor het abonnement (of de resource groepen bevat die worden uitgevoerd). Als u alle gebruikers wilt zien die het abonnement kunnen delegeren, kan een gebruiker in de Tenant van de klant het abonnement selecteren in de Azure Portal, **toegangs beheer openen (IAM)** en [alle gebruikers met de rol eigenaar weer geven](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Geslaagde onboarding bevestigen

Wanneer het abonnement van een klant is opgebruikt voor het beheer van de gedelegeerde resources van Azure, kunnen gebruikers in de Tenant van de service provider het abonnement en de bijbehorende resources zien (als ze hiervoor toegang hebben gekregen via bovenstaand proces) afzonderlijk of als lid van een Azure AD-groep met de juiste machtigingen). Als u dit wilt bevestigen, controleert u of het abonnement op een van de volgende manieren wordt weer gegeven.  

### <a name="azure-portal"></a>Azure Portal

In de Tenant van de service provider:

1. Navigeer naar de [pagina mijn klanten](view-manage-customers.md).
2. Selecteer **klanten**.
3. Bevestig dat u de abonnement/abonnementen kunt zien met de naam van de aanbieding die u in de Resource Manager-sjabloon hebt opgenomen.

> [!IMPORTANT]
> Als u het gedelegeerde abonnement in [mijn klanten](view-manage-customers.md)wilt zien, moeten gebruikers in de Tenant van de service provider beschikken over de rol van [lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (of een andere ingebouwde rol die lezers toegang bevat) wanneer het abonnement voor Azure is voor bereid gedelegeerd resource beheer.

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

## <a name="remove-access-to-a-delegation"></a>Toegang tot een overdracht verwijderen

Standaard kan een gebruiker in de Tenant van de klant die over de juiste machtigingen beschikt, de toegang tot resources die aan een service provider zijn overgedragen, verwijderen op de [pagina service providers](view-manage-service-providers.md#add-or-remove-service-provider-offers) van de Azure Portal.

Als u gebruikers hebt opgenomen in de functie voor het verwijderen van de [registratie toewijzing van beheerde services](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) bij het voorbereiden van de klant voor Azure delegated resource management, kunnen die gebruikers in uw Tenant ook de overdracht verwijderen. Wanneer u dit doet, hebben gebruikers in de Tenant van de service provider geen toegang meer tot de resources die eerder zijn gedelegeerd.

In het onderstaande voor beeld ziet u een toewijzing waarmee de rol van de **registratie toewijzing voor beheerde services** wordt verleend die kan worden opgenomen in een parameter bestand:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c" 
        } 
    ] 
```

Een gebruiker met deze machtiging kan een delegering op een van de volgende manieren verwijderen.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete –assignment <id or full resourceId>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [ervaring op het beheer van cross-tenants](../concepts/cross-tenant-management-experience.md).
- [Bekijk en beheer klanten](view-manage-customers.md) door naar **mijn klanten** te gaan in de Azure Portal.

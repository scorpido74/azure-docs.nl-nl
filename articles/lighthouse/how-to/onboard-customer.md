---
title: Een klant in gedelegeerd Azure-resourcebeheer plaatsen
description: Meer informatie over het aan boord van een klant naar Azure gedelegeerd resourcebeheer, zodat hun resources kunnen worden geopend en beheerd via uw eigen tenant.
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 9eef2e73de7812fc65104131dfd319bf8dfb014e
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984586"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Een klant in gedelegeerd Azure-resourcebeheer plaatsen

In dit artikel wordt uitgelegd hoe u als serviceprovider een klant vastzetten aan azure gedelegeerd bronbeheer, zodat hun gedelegeerde resources (abonnementen en/of resourcegroepen) kunnen worden geopend en beheerd via uw eigen Azure Active Directory-tenant (Azure AD). Hoewel we hier verwijzen naar serviceproviders en klanten, kunnen [bedrijven die meerdere tenants beheren](../concepts/enterprise.md) hetzelfde proces gebruiken om hun beheerervaring te consolideren.

U dit proces herhalen als u resources voor meerdere klanten beheert. Wanneer een geautoriseerde gebruiker zich aanmeldt bij uw tenant, kan die gebruiker vervolgens worden geautoriseerd voor een tehuurcontract van klanten om beheerbewerkingen uit te voeren zonder dat hij zich hoeft aan te melden bij elke individuele klanttenant.

Als u uw impact voor klantinteracties wilt bijhouden en erkenning wilt ontvangen, koppelt u uw MPN-id (Microsoft Partner Network) aan ten minste één gebruikersaccount dat toegang heeft tot elk van uw abonnementen aan boord. Houd er rekening mee dat u deze koppeling moet uitvoeren in de tenant van uw serviceprovider. Voor de eenvoud raden we u aan een serviceprincipal-account in uw tenant aan te maken dat is gekoppeld aan uw MPN-id en deze reader toegang te verlenen aan elke klant die u aan boord hebt. Zie [Een partner-id koppelen aan uw Azure-accounts voor](../../billing/billing-partner-admin-link-started.md)meer informatie. 

> [!NOTE]
> Klanten kunnen ook worden aan boord genomen wanneer ze een managed services-aanbieding (openbaar of privé) kopen die u hebt gepubliceerd op Azure Marketplace. Zie [Managed Services-aanbiedingen publiceren naar Azure Marketplace](publish-managed-services-offers.md)voor meer informatie. U ook gebruik maken van het onboarding-proces dat hier wordt beschreven, samen met een aanbieding die is gepubliceerd op Azure Marketplace.

Het onboardingproces vereist dat acties worden uitgevoerd vanuit zowel de huurder van de serviceprovider als van de huurder van de klant. Al deze stappen worden beschreven in dit artikel.

## <a name="gather-tenant-and-subscription-details"></a>Tenant- en abonnementsgegevens verzamelen

Als u de tenant van een klant wilt aanboord, moet deze een actief Azure-abonnement hebben. Je moet het volgende weten:

- De tenant-id van de huurder van de serviceprovider (waar u de resources van de klant beheert)
- De tenant-id van de huurder van de klant (die resources wordt beheerd door de serviceprovider)
- De abonnements-iD's voor elk specifiek abonnement in de tenant van de klant die wordt beheerd door de serviceprovider (of die de resourcegroep(s) bevat die door de serviceprovider wordt beheerd).

> [!NOTE]
> Zelfs als u alleen een of meer resourcegroepen binnen een abonnement wilt aanboord, moet de implementatie op abonnementsniveau worden uitgevoerd, zodat u de abonnements-ID nodig hebt.

Als u deze ID-waarden nog niet hebt, u deze op een van de volgende manieren ophalen. Zorg ervoor dat u deze exacte waarden in uw implementatie gebruikt.

### <a name="azure-portal"></a>Azure Portal

Uw tenant-id kan worden gezien door boven uw accountnaam boven rechtsboven in de Azure-portal te zweven of door **de schakelmap te**selecteren. Als u uw tenant-id wilt selecteren en kopiëren, zoekt u vanuit de portal naar 'Azure Active Directory', selecteert u **Eigenschappen** en kopieert u de waarde die wordt weergegeven in het veld **Directory-id.** Als u de id van een abonnement in de klanttenant wilt vinden, zoekt u naar 'Abonnementen' en selecteert u vervolgens de juiste abonnements-ID.

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
> Wanneer u een abonnement (of een of meer resourcegroepen binnen een abonnement) instelt met behulp van het hier beschreven proces, wordt de **Microsoft.ManagedServices-resourceprovider** geregistreerd voor dat abonnement.

## <a name="define-roles-and-permissions"></a>Rollen en machtigingen definiëren

Als serviceprovider u meerdere taken voor één klant uitvoeren, waardoor verschillende toegang voor verschillende scopes vereist is. U zoveel autorisaties definiëren als u nodig hebt om [rbac-ingebouwde rollen (role-based access control)](../../role-based-access-control/built-in-roles.md) toe te wijzen aan gebruikers in uw tenant.

Om beheer eenvoudiger te maken, raden we u aan azure AD-gebruikersgroepen voor elke rol te gebruiken, zodat u afzonderlijke gebruikers aan de groep toevoegen of verwijderen in plaats van machtigingen rechtstreeks aan die gebruiker toe te kennen. U ook rollen toewijzen aan een serviceprincipal. Zorg ervoor dat u het principe van de minste bevoegdheden te volgen, zodat gebruikers alleen de machtigingen die nodig zijn om hun taak te voltooien. Zie [Tenants, gebruikers en rollen in Azure Lighthouse-scenario's](../concepts/tenants-users-roles.md)voor aanbevelingen en informatie over ondersteunde rollen.

> [!IMPORTANT]
> Als u machtigingen wilt toevoegen voor een Azure AD-groep, moet het **type Groep** **beveiliging** zijn en geen **Office 365**. Deze optie wordt geselecteerd wanneer de groep wordt gemaakt. Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie.

Als u autorisaties wilt definiëren, moet u de ID-waarden kennen voor elke gebruiker, gebruikersgroep of serviceprincipal in de tenant van de serviceprovider waartoe u toegang wilt verlenen. U hebt ook de functiedefinitie-ID nodig voor elke ingebouwde rol die u wilt toewijzen. Als u ze nog niet hebt, u ze ophalen door de onderstaande opdrachten uit te voeren vanuit de serviceprovidertenant.

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
> We raden u aan de functie Voor het verwijderen van de [registratietoewijzing voor Managed Services toe te wijs](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) wanneer u een klant inbrengt, zodat gebruikers in uw tenant de [toegang tot de delegatie](#remove-access-to-a-delegation) later kunnen verwijderen indien nodig. Als deze rol niet is toegewezen, kunnen gedelegeerde resources alleen worden verwijderd door een gebruiker in de tenant van de klant.

## <a name="create-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon maken

Als u aan boord wilt gaan van uw klant, moet u een [Azure Resource Manager-sjabloon](../../azure-resource-manager/index.yml) voor uw aanbieding maken met de volgende gegevens. De waarden **mspOfferName** en **mspOfferDescription** zijn zichtbaar voor de klant bij het weergeven van [aanbiedingsgegevens](view-manage-service-providers.md) op de pagina Serviceproviders van de Azure-portal.

|Veld  |Definitie  |
|---------|---------|
|**mspOfferNaam**     |Een naam die deze definitie beschrijft. Deze waarde wordt aan de klant weergegeven als de titel van de aanbieding.         |
|**mspOfferBeschrijving**     |Een korte beschrijving van uw aanbieding (bijvoorbeeld "Contoso VM management aanbieding").      |
|**managedByTenantId**     |Je pacht-id.          |
|**Vergunningen**     |De **belangrijkste id-waarden** voor de gebruikers/groepen/SPN's van uw tenant, elk met een **principalIdDisplayName** om uw klant inzicht te geven in het doel van de autorisatie en toegewezen aan een ingebouwde **roleDefinitionId-waarde** om het toegangsniveau op te geven.      |

Voor het onboardingproces is een Azure Resource Manager-sjabloon vereist (zoals in onze [repo-voorbeelden)](https://github.com/Azure/Azure-Lighthouse-samples/)en een bijbehorend parametersbestand dat u wijzigt om aan uw configuratie te voldoen en uw autorisaties te definiëren.

De sjabloon die u kiest, is afhankelijk van of u een volledig abonnement, een resourcegroep of meerdere resourcegroepen binnen een abonnement invoert. We bieden ook een sjabloon die kan worden gebruikt voor klanten die een managed service-aanbieding hebben gekocht die u op Azure Marketplace hebt gepubliceerd, als u op deze manier liever aan boord van hun abonnement(en) wilt.

|Om aan boord van deze  |Deze sjabloon Azure Resource Manager gebruiken  |En wijzig dit parameterbestand |
|---------|---------|---------|
|Abonnement   |[gedelegeerdResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resourcegroep   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Meerdere resourcegroepen binnen een abonnement   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Abonnement (bij gebruik van een aanbieding die is gepubliceerd op Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> Het proces dat hier wordt beschreven, vereist een aparte implementatie op abonnementsniveau voor elk abonnement dat wordt aanboord, zelfs als u abonnementen in dezelfde klanttenant intouwt. Afzonderlijke implementaties zijn ook vereist als u meerdere resourcegroepen binnen verschillende abonnementen in dezelfde klanttenant inwerkt. Het onboardingen van meerdere resourcegroepen binnen één abonnement kan echter worden uitgevoerd in één implementatie op abonnementsniveau.
>
> Er zijn ook afzonderlijke implementaties vereist voor meerdere aanbiedingen die worden toegepast op hetzelfde abonnement (of brongroepen binnen een abonnement). Elke toepassing van de aanbieding moet een andere **mspOfferName**gebruiken.

In het volgende voorbeeld wordt een gewijzigd **bestand gedelegeerd ResourceManagement.parameters.json** weergegeven dat kan worden gebruikt om een abonnement aan boord te nemen. De parameterbestanden van de brongroep (die zich in de map [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) bevinden) zijn vergelijkbaar, maar bevatten ook een **rgName-parameter** om de specifieke resourcegroep(s) te identificeren die aan boord moet worden genomen.

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

De laatste autorisatie in het bovenstaande voorbeeld voegt een **principalId** toe met de functie User Access Administrator (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Wanneer u deze rol toekent, moet u de eigenschap **delegatedRoleDefinitionIds** en een of meer ingebouwde rollen opnemen. De gebruiker die in deze autorisatie is gemaakt, kan deze ingebouwde rollen toewijzen aan [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md) in de klanttenant, wat nodig is om beleid te [implementeren dat kan worden gesaneerd.](deploy-policy-remediation.md) Er zijn geen andere machtigingen die normaal zijn gekoppeld aan de functie Gebruikerstoegangsbeheerder op deze gebruiker.

## <a name="deploy-the-azure-resource-manager-templates"></a>De Azure Resource Manager-sjablonen implementeren

Nadat u uw parameterbestand hebt bijgewerkt, moet een gebruiker in de tenant van de klant de azure resource manager-sjabloon in zijn tenant implementeren als implementatie op abonnementsniveau. Voor elk abonnement dat u aan boord wilt nemen van azure-gedelegeerd bronnenbeheer (of voor elk abonnement dat brongroepen bevat die u aan boord wilt hebben, is een afzonderlijke implementatie nodig die u aan boord wilt maken van azure-gedelegeerde resourcebeheer (of voor elk abonnement dat brongroepen bevat die u aan boord wilt hebben).

Omdat dit een implementatie op abonnementsniveau is, kan deze niet worden gestart in de Azure-portal. De implementatie kan worden uitgevoerd met PowerShell of Azure CLI, zoals hieronder wordt weergegeven.

> [!IMPORTANT]
> Deze implementatie op abonnementsniveau moet worden uitgevoerd door een niet-gastaccount in de tenant van de klant die de [ingebouwde rol van eigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) heeft voor het abonnement dat wordt aanboord (of die de resourcegroepen bevat die aan boord worden genomen). Als u alle gebruikers wilt zien die het abonnement kunnen delegeren, kan een gebruiker in de tenant van de klant het abonnement selecteren in de Azure-portal, **Access-besturingselement (IAM)** openen en [alle gebruikers weergeven met de rol Eigenaar](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

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

## <a name="confirm-successful-onboarding"></a>Succesvolle onboarding bevestigen

Wanneer een klantabonnement is gekoppeld aan azure gedelegeerd resourcebeheer, kunnen gebruikers in de tenant van de serviceprovider het abonnement en de bijbehorende bronnen zien (als ze toegang hebben gekregen tot het abonnement via het bovenstaande proces, afzonderlijk of als lid van een Azure AD-groep met de juiste machtigingen). Controleer om dit te bevestigen of het abonnement op een van de volgende manieren wordt weergegeven.  

### <a name="azure-portal"></a>Azure Portal

In de huurder van de dienstverlener:

1. Navigeer naar de [pagina Mijn klanten](view-manage-customers.md).
2. Selecteer **klanten**.
3. Controleer of u het abonnement(en) zien met de aanbiedingsnaam die u hebt opgegeven in de sjabloon Resourcemanager.

> [!IMPORTANT]
> Om het gedelegeerde abonnement in [Mijn klanten](view-manage-customers.md)te kunnen zien, moeten gebruikers in de tenant van de serviceprovider de [Reader-rol](../../role-based-access-control/built-in-roles.md#reader) (of een andere ingebouwde rol met Reader-toegang) hebben gekregen toen het abonnement is aanboord voor azure-gedelegeerd resourcebeheer.

In de huurder van de klant:

1. Navigeer naar de [pagina Serviceproviders](view-manage-service-providers.md).
2. Aanbiedingen **van geselecteerde serviceproviders**.
3. Controleer of u het abonnement(en) zien met de aanbiedingsnaam die u hebt opgegeven in de sjabloon Resourcemanager.

> [!NOTE]
> Het kan enkele minuten duren voordat uw implementatie is voltooid voordat de updates worden weergegeven in de Azure-portal.

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

## <a name="remove-access-to-a-delegation"></a>Toegang tot een delegatie verwijderen

Gebruikers in de tenant van de klant die over de juiste machtigingen beschikken, kunnen standaard de toegang van serviceproviders tot gedelegeerde bronnen op de [pagina Serviceproviders](view-manage-service-providers.md#add-or-remove-service-provider-offers) van de Azure-portal verwijderen. Wanneer ze dit doen, hebben geen gebruikers in de tenant van de serviceprovider toegang tot de bronnen die eerder waren gedelegeerd.

Als u gebruikers hebt gekoppeld aan de [verwijderrol voor het verwijderen van beheerde servicesregistratietoewijzing](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) bij het inschepen van een klant voor azure-gedelegeerd resourcebeheer, kunnen deze gebruikers de delegatie ook verwijderen.

In het onderstaande voorbeeld wordt een toewijzing weergegeven die de **rol Voor het verwijderen van registratietoewijzing managed services** verleent die kan worden opgenomen in een parameterbestand:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Een gebruiker met deze toestemming kan een delegatie op een van de volgende manieren verwijderen.

### <a name="azure-portal"></a>Azure Portal

1. Navigeer naar de [pagina Mijn klanten](view-manage-customers.md).
2. Selecteer **Delegaties**.
3. Zoek de delegatie die u wilt verwijderen en selecteer vervolgens het pictogram voor prullenbakdat in de rij wordt weergegeven.

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

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [cross-tenant management ervaringen](../concepts/cross-tenant-management-experience.md).
- [Klanten bekijken en beheren](view-manage-customers.md) door naar **Mijn klanten** te gaan in de Azure-portal.

---
title: Azure AD B2C met Azure Monitor bewaken
titleSuffix: Azure AD B2C
description: Meer informatie over het registreren van Azure AD B2C gebeurtenissen met Azure Monitor met behulp van gedelegeerd resource beheer.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: marsma
ms.subservice: B2C
ms.date: 02/03/2020
ms.openlocfilehash: 108c9c1112327a3fcadeff4c4074f31f976a4e3d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026755"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure AD B2C met Azure Monitor bewaken

Gebruik Azure Monitor voor het routeren van Azure Active Directory B2C (Azure AD B2C) gebeurtenissen voor gebruiks activiteiten naar verschillende bewakings oplossingen. U kunt de logboeken bewaren voor lange termijn gebruik of integratie met SIEM-hulpprogram ma's (Security Information and Event Management) van derden om inzicht te krijgen in uw omgeving.

U kunt logboek gebeurtenissen door sturen naar:

* Een Azure Storage-account.
* Een Azure-Event Hub (en kan worden geïntegreerd met uw logische Splunk-en Sumo-instanties).
* Een Azure Log Analytics-werk ruimte (voor het analyseren van gegevens, het maken van Dash boards en waarschuwingen voor specifieke gebeurtenissen).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren, implementeert u een Azure Resource Manager sjabloon met behulp van de module Azure PowerShell.

* [Azure PowerShell-module](https://docs.microsoft.com/powershell/azure/install-az-ps) versie 6.13.1 of hoger

U kunt ook de [Azure Cloud shell](https://shell.azure.com)gebruiken, die de meest recente versie van de module Azure PowerShell bevat.

## <a name="delegated-resource-management"></a>Gedelegeerd resource beheer

Azure AD B2C maakt gebruik van [Azure Active Directory bewaking](../active-directory/reports-monitoring/overview-monitoring.md). Als u *Diagnostische instellingen* in azure Active Directory binnen uw Azure AD B2C Tenant wilt inschakelen, gebruikt u [gedelegeerd resource beheer](../lighthouse/concepts/azure-delegated-resource-management.md).

U geeft een gebruiker in uw Azure AD B2C Directory (de **service provider**) toestemming voor het configureren van de Azure monitor instantie binnen de Tenant die uw Azure-abonnement (de **klant**) bevat. Als u de autorisatie wilt maken, implementeert u een [Azure Resource Manager](../azure-resource-manager/index.yml) sjabloon voor uw Azure AD-Tenant met het abonnement. In de volgende secties wordt uitgelegd hoe u het proces kunt door lopen.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[Maak een resource groep](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)In de Azure Active Directory-Tenant (Azure AD) die uw Azure-abonnement bevat (*niet* de map die uw Azure AD B2C Tenant bevat). Gebruik de volgende waarden:

* **Abonnement**: selecteer uw Azure-abonnement.
* **Resource groep**: Voer een naam in voor de resource groep. Bijvoorbeeld *Azure-AD-B2C-monitor*.
* **Regio**: Selecteer een Azure-locatie. Bijvoorbeeld *US - centraal*.

## <a name="delegate-resource-management"></a>Resource beheer delegeren

Vervolgens moet u de volgende informatie verzamelen:

De **Directory-id** van uw Azure AD B2C Directory (ook wel de Tenant-id genoemd).

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als gebruiker met de rol *gebruikers beheerder* (of hoger).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Selecteer **Azure Active Directory**en selecteer **Eigenschappen**.
1. Noteer de **map-id**.

De **object-id** van de Azure AD B2C groep of gebruiker die u *wilt machtigen* voor de resource groep die u eerder hebt gemaakt in de map met uw abonnement.

Om het beheer te vereenvoudigen, raden we u aan Azure AD-gebruikers *groepen* te gebruiken voor elke rol, zodat u afzonderlijke gebruikers aan de groep kunt toevoegen of verwijderen, in plaats van machtigingen rechtstreeks aan die gebruiker toe te wijzen. In dit overzicht kunt u een gebruiker toevoegen.

1. Selecteer **gebruikers**met **Azure Active Directory** nog steeds geselecteerd in de Azure Portal en selecteer vervolgens een gebruiker.
1. De **object-id**van de gebruiker vastleggen.

### <a name="create-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon maken

Voor een onboarding van uw Azure AD-Tenant (de **klant**), maakt u een [Azure Resource Manager sjabloon](../lighthouse/how-to/onboard-customer.md) voor uw aanbieding met de volgende gegevens. De waarden voor `mspOfferName` en `mspOfferDescription` zijn zichtbaar wanneer u details van de aanbieding bekijkt op de [pagina service providers](../lighthouse/how-to/view-manage-service-providers.md) van de Azure Portal.

| Veld   | Definitie |
|---------|------------|
| `mspOfferName`                     | Een naam die deze definitie beschrijft. Bijvoorbeeld *Azure AD B2C beheerde services*. Deze waarde wordt weer gegeven aan de klant als de titel van de aanbieding. |
| `mspOfferDescription`              | Een korte beschrijving van uw aanbieding. *Hiermee schakelt u bijvoorbeeld Azure monitor in azure AD B2C*.|
| `rgName`                           | De naam van de resource groep die u eerder hebt gemaakt in uw Azure AD-Tenant. Bijvoorbeeld *Azure-AD-B2C-monitor*. |
| `managedByTenantId`                | De **Directory-id** van uw Azure AD B2C Tenant (ook wel de Tenant-id genoemd). |
| `authorizations.value.principalId` | De **object-id** van de B2C-groep of-gebruiker die toegang heeft tot bronnen in dit Azure-abonnement. Voor dit scenario geeft u de object-ID van de gebruiker op die u eerder hebt vastgelegd. |

De Azure Resource Manager sjabloon en de parameter bestanden downloaden:

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Werk vervolgens het parameter bestand bij met de waarden die u eerder hebt vastgelegd. Het volgende JSON-code fragment toont een voor beeld van een bestand met Azure Resource Manager sjabloon parameters. Voor `authorizations.value.roleDefinitionId`gebruikt u de [ingebouwde rol](../role-based-access-control/built-in-roles.md) waarde voor de *rol Inzender*`b24988ac-6180-42a0-ab88-20f7382dd24c`.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>De Azure Resource Manager-sjablonen implementeren

Nadat u het parameter bestand hebt bijgewerkt, implementeert u de Azure Resource Manager-sjabloon in de Azure-Tenant als implementatie op abonnements niveau. Omdat dit een implementatie op abonnements niveau is, kan deze niet worden gestart in de Azure Portal. U kunt implementeren met behulp van de Azure PowerShell-module of de Azure CLI. De Azure PowerShell methode wordt hieronder weer gegeven.

Meld u aan bij de map met uw abonnement met behulp van [Connect-AzAccount](/powershell/azure/authenticate-azureps). Gebruik de vlag `-tenant` om de verificatie naar de juiste map af te dwingen.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Gebruik de cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) om de abonnementen weer te geven waartoe het huidige account toegang kan krijgen onder de Azure AD-Tenant. Registreer de ID van het abonnement dat u wilt projecteren in uw Azure AD B2C-Tenant.

```PowerShell
Get-AzSubscription
```

Ga vervolgens naar het abonnement dat u wilt projecteren in de Azure AD B2C Tenant:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Implementeer ten slotte de Azure Resource Manager sjabloon en de parameter bestanden die u eerder hebt gedownload en bijgewerkt. Vervang de waarden voor `Location`, `TemplateFile`en `TemplateParameterFile` dienovereenkomstig.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

Een geslaagde implementatie van de sjabloon produceert uitvoer die er ongeveer als volgt uitziet (uitvoer afgekapt voor de boog):

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

Nadat u de sjabloon hebt geïmplementeerd, kan het enkele minuten duren voordat de resource projectie is voltooid. Mogelijk moet u enkele minuten wachten (meestal niet meer dan vijf) voordat u naar de volgende sectie gaat om het abonnement te selecteren.

## <a name="select-your-subscription"></a>Selecteer uw abonnement

Nadat u de sjabloon hebt geïmplementeerd en een paar minuten hebt gewacht totdat de resource projectie is voltooid, koppelt u uw abonnement aan uw Azure AD B2C Directory met de volgende stappen.

1. **Meld u af** bij de Azure portal als u momenteel bent aangemeld. Deze en de volgende stap worden uitgevoerd om uw referenties in de portal sessie te vernieuwen.
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure AD B2C beheerders account.
1. Selecteer het pictogram voor het adres van de **map en het abonnement** op de werk balk van de portal.
1. Selecteer de map die uw abonnement bevat.

    ![Scha kelen tussen mappen](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Controleer of u de juiste directory en dit abonnement hebt geselecteerd. In dit voor beeld zijn alle directory's en abonnementen geselecteerd.

    ![Alle mappen die zijn geselecteerd in het Directory &-abonnements filter](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren

Nadat u resource beheer hebt gedelegeerd en uw abonnement hebt geselecteerd, bent u klaar om [Diagnostische instellingen te maken](../active-directory/reports-monitoring/overview-monitoring.md) in de Azure Portal.

Controle-instellingen voor Azure AD B2C activiteiten logboeken configureren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw Azure AD B2C Tenant bevat.
1. **Azure Active Directory** selecteren
1. Onder **bewaking**, selecteer **diagnostische instellingen**.
1. Selecteer **+ Diagnostische instelling toevoegen**.

    ![Het deel venster Diagnostische instellingen in Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het toevoegen en configureren van diagnostische instellingen in Azure Monitor raadpleegt u deze zelf studie in de Azure Monitor documentatie:

[Zelf studie: resource logboeken verzamelen en analyseren vanuit een Azure-resource](/azure-monitor/learn/tutorial-resource-logs.md)

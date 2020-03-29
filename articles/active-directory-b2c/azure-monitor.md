---
title: Azure AD B2C bewaken met Azure-monitor
titleSuffix: Azure AD B2C
description: Meer informatie over het registreren van Azure AD B2C-gebeurtenissen met Azure Monitor met behulp van gedelegeerd bronbeheer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: mimart
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: acba378badb41324b2124b84833407da920a0e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190055"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure AD B2C bewaken met Azure-monitor

Gebruik Azure Monitor om azure Active Directory B2C (Azure AD B2C) [aanmeldings-](view-audit-logs.md) en controlelogboeken te routeren naar verschillende bewakingsoplossingen. U de logboeken bewaren voor langdurig gebruik of integreren met SIEM-tools (third-party security information and event management) om inzicht te krijgen in uw omgeving.

U logboekgebeurtenissen routeren naar:

* Een [Azure-opslagaccount](../storage/blobs/storage-blobs-introduction.md).
* Een [Azure-gebeurtenishub](../event-hubs/event-hubs-about.md) (en integreer met uw Splunk- en Sumo Logic-exemplaren).
* Een [werkruimte log analytics](../azure-monitor/platform/resource-logs-collect-workspace.md) (om gegevens te analyseren, dashboards te maken en specifieke gebeurtenissen te waarschuwen).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt uitvoeren, implementeert u een Azure Resource Manager-sjabloon met behulp van de Azure PowerShell-module.

* [Azure PowerShell-moduleversie](https://docs.microsoft.com/powershell/azure/install-az-ps) 6.13.1 of hoger

U ook de [Azure Cloud Shell](https://shell.azure.com)gebruiken, die de nieuwste versie van de Azure PowerShell-module bevat.

## <a name="delegated-resource-management"></a>Gedelegeerd resourcebeheer

Azure AD B2C maakt gebruik van [Azure Active Directory-bewaking](../active-directory/reports-monitoring/overview-monitoring.md). Als u *diagnostische instellingen* wilt inschakelen in Azure Active Directory in uw Azure AD B2C-tenant, gebruikt u [gedelegeerd bronbeheer](../lighthouse/concepts/azure-delegated-resource-management.md).

U autoriseert een gebruiker of groep in uw Azure AD B2C-map (de **Service Provider)** om het Azure Monitor-exemplaar te configureren binnen de tenant die uw Azure-abonnement bevat (de **klant).** Als u de autorisatie wilt maken, implementeert u een [Azure Resource Manager-sjabloon](../azure-resource-manager/index.yml) naar uw Azure AD-tenant met het abonnement. De volgende secties lopen u door het proces.

## <a name="create-or-choose-resource-group"></a>Resourcegroep maken of kiezen

Dit is de brongroep met het doelAzure-opslagaccount, gebeurtenishub of Log Analytics-werkruimte om gegevens van Azure Monitor te ontvangen. U geeft de naam van de brongroep op wanneer u de sjabloon Azure Resource Manager implementeert.

[Maak een brongroep](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) of kies een bestaande in de Azure Ad-tenant (Azure Directory) die uw Azure-abonnement bevat, *niet* de map die uw Azure AD B2C-tenant bevat.

In dit voorbeeld wordt een resourcegroep met de naam *azure-ad-b2c-monitor* gebruikt in de regio *Midden-VS.*

## <a name="delegate-resource-management"></a>Resourcebeheer delegeren

Verzamel vervolgens de volgende informatie:

**Directory-id** van uw Azure AD B2C-map (ook wel tenant-id genoemd).

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als gebruiker met de functie *Gebruikersbeheerder* (of hoger).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. Selecteer **Azure Active Directory**, selecteer **Eigenschappen**.
1. Neem de **directory-id op**.

**Object-id** van de Azure AD B2C-groep of -gebruiker die u *inzender* toestemming wilt geven aan de brongroep die u eerder hebt gemaakt in de map met uw abonnement.

Om beheer eenvoudiger te maken, *groups* raden we u aan azure AD-gebruikersgroepen voor elke rol te gebruiken, zodat u afzonderlijke gebruikers aan de groep toevoegen of verwijderen in plaats van machtigingen rechtstreeks aan die gebruiker toe te kennen. In deze walkthrough voegt u een gebruiker toe.

1. Als **Azure Active Directory** nog steeds is geselecteerd in de Azure-portal, selecteert u **Gebruikers**en selecteert u vervolgens een gebruiker.
1. De **object-id**van de gebruiker opnemen .

### <a name="create-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon maken

Als u aan boord wilt gaan van uw Azure AD-tenant (de **klant),** maakt u een [Azure Resource Manager-sjabloon](../lighthouse/how-to/onboard-customer.md) voor uw aanbieding met de volgende gegevens. De `mspOfferName` `mspOfferDescription` waarden en waarden zijn zichtbaar wanneer u informatie weergeeft op de [pagina Serviceproviders](../lighthouse/how-to/view-manage-service-providers.md) van de Azure-portal.

| Veld   | Definitie |
|---------|------------|
| `mspOfferName`                     | Een naam die deze definitie beschrijft. Azure *AD B2C Managed Services*. Deze waarde wordt aan de klant weergegeven als de titel van de aanbieding. |
| `mspOfferDescription`              | Een korte beschrijving van uw aanbod. Hiermee schakelt u bijvoorbeeld *Azure Monitor in Azure AD B2C in*.|
| `rgName`                           | De naam van de resourcegroep die u eerder maakt in uw Azure AD-tenant. Bijvoorbeeld *azure-ad-b2c-monitor*. |
| `managedByTenantId`                | De **directory-id** van uw Azure AD B2C-tenant (ook wel tenant-id genoemd). |
| `authorizations.value.principalId` | De **object-id** van de B2C-groep of -gebruiker die toegang heeft tot bronnen in dit Azure-abonnement. Geef voor deze walkthrough de object-id van de gebruiker op die u eerder hebt opgenomen. |

Download de sjabloon en parameterbestanden van Azure Resource Manager:

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

Werk vervolgens het parametersbestand bij met de waarden die u eerder hebt geregistreerd. In het volgende JSON-fragment wordt een voorbeeld weergegeven van een sjabloonparametersbestand van Azure Resource Manager. Voor `authorizations.value.roleDefinitionId`, gebruik maken van de [ingebouwde rol](../role-based-access-control/built-in-roles.md) waarde voor de *rol inzender*, `b24988ac-6180-42a0-ab88-20f7382dd24c`.

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

Zodra u uw parametersbestand hebt bijgewerkt, implementeert u de azure resource manager-sjabloon in de Azure-tenant als een implementatie op abonnementsniveau. Omdat dit een implementatie op abonnementsniveau is, kan deze niet worden gestart in de Azure-portal. U implementeren met de Azure PowerShell-module of de Azure CLI. De Azure PowerShell-methode wordt hieronder weergegeven.

Meld u aan bij de directory met uw abonnement via [Connect-AzAccount](/powershell/azure/authenticate-azureps). Gebruik `-tenant` de vlag om verificatie naar de juiste map te forceren.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

Gebruik de cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) om de abonnementen weer te geven die de huidige account kan openen onder de Azure AD-tenant. Neem de id op van het abonnement dat u wilt projecteren in uw Azure AD B2C-tenant.

```PowerShell
Get-AzSubscription
```

Schakel vervolgens over naar het abonnement dat u wilt projecteren in de Azure AD B2C-tenant:

``` PowerShell
Select-AzSubscription <subscription ID>
```

Implementeer ten slotte de sjabloon azure resource beheer en parameterbestanden die u eerder hebt gedownload en bijgewerkt. Vervang `Location`de `TemplateFile`, `TemplateParameterFile` , en waarden dienovereenkomstig.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

Succesvolle implementatie van de sjabloon levert uitvoer op die vergelijkbaar is met de volgende (uitvoer afgekapt voor beknoptheid):

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

Nadat u de sjabloon hebt geïmplementeerd, kan het enkele minuten duren voordat de resourceprojectie is voltooid. Het kan nodig zijn om een paar minuten te wachten (meestal niet meer dan vijf) voordat u naar de volgende sectie gaat om het abonnement te selecteren.

## <a name="select-your-subscription"></a>Selecteer uw abonnement

Zodra u de sjabloon hebt geïmplementeerd en een paar minuten hebt gewacht tot de bronprojectie is voltooid, koppelt u uw abonnement aan uw Azure AD B2C-map aan de volgende stappen.

1. **Meld u af** bij de Azure-portal als u momenteel bent aangemeld. Dit en de volgende stap worden gedaan om uw referenties in de portalsessie te vernieuwen.
1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure AD B2C-beheeraccount.
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal.
1. Selecteer de map met uw abonnement.

    ![Schakelen tussen mappen](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. Controleer of u de juiste map en abonnement hebt geselecteerd. In dit voorbeeld worden alle mappen en abonnementen geselecteerd.

    ![Alle mappen geselecteerd in het filter Directory & Abonnement](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>Diagnostische instellingen configureren

Diagnostische instellingen bepalen waar logboeken en statistieken voor een resource moeten worden verzonden. Mogelijke bestemmingen zijn:

- [Azure-opslagaccount](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Oplossingen voor gebeurtenishubs.](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Log Analytics-werkruimte](../azure-monitor/platform/resource-logs-collect-workspace.md)

Als u dit nog niet hebt gedaan, maakt u een instantie van het gekozen doeltype in de resourcegroep die u hebt opgegeven in de [sjabloon Azure Resource Manager.](#create-an-azure-resource-manager-template)

### <a name="create-diagnostic-settings"></a>Diagnostische instellingen maken

U bent klaar voor [het maken van diagnostische instellingen](../active-directory/reports-monitoring/overview-monitoring.md) in de Azure-portal.

Ga als volgt te werk om bewakingsinstellingen voor Azure AD B2C-activiteitslogboeken te configureren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer het pictogram **Directory + Abonnement** op de werkbalk van de portal en selecteer vervolgens de map met uw Azure AD B2C-tenant.
1. **Azure Active Directory selecteren**
1. Selecteer **Diagnostische instellingen** onder **Controle**.
1. Als er bestaande instellingen op de bron staan, ziet u een lijst met instellingen die al zijn geconfigureerd. Selecteer **Diagnostische instelling toevoegen** om een nieuwe instelling toe te voegen of De instelling **Bewerken** om een bestaande instelling te bewerken. Elke instelling mag niet meer dan één van elk van de doeltypen hebben..

    ![Deelvenster Diagnostische instellingen in Azure-portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Geef uw instelling een naam als deze er nog niet is.
1. Schakel het selectievakje voor elke bestemming in om de logboeken te verzenden. Selecteer **Configureren** om de instellingen op te geven zoals beschreven in de volgende tabel.

    | Instelling | Beschrijving |
    |:---|:---|
    | Archiveren naar een opslagaccount | Naam van het opslagaccount. |
    | Streamen naar een Event Hub | De naamruimte waar de gebeurtenishub wordt gemaakt (als dit de eerste keer streaming logs is) of gestreamd naar (als er al bronnen zijn die die logboekcategorie naar deze naamruimte streamen).
    | Verzenden naar Log Analytics | Naam van werkruimte. |

1. Selecteer **AuditLogs** en **SignInLogs**.
1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

Zie [Zelfstudie: Bronlogboeken verzamelen en analyseren van een Azure-bron](../azure-monitor/insights/monitor-azure-resource.md)voor meer informatie over het toevoegen en configureren van diagnostische instellingen in Azure Monitor.

Zie [Zelfstudie: Azure Active Directory-logboeken streamen naar een Azure-gebeurtenishub voor](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)informatie over het streamen van Azure AD-logboeken naar een gebeurtenishub.

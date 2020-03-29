---
title: Beheerde app met beheerde identiteit
description: Beheerde toepassing configureren met Beheerde identiteit voor koppelingen naar bestaande resources, het beheren van Azure-resources en het bieden van operationele identiteit voor activiteitslogboek.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651655"
---
# <a name="azure-managed-application-with-managed-identity"></a>Azure Managed Application with Managed Identity

> [!NOTE]
> Managed Identity-ondersteuning voor beheerde toepassingen is momenteel in preview. Gebruik de api-versie 2018-09-01-preview om Managed Identity te gebruiken.

Meer informatie over het configureren van een beheerde toepassing om een beheerde identiteit te bevatten. Managed Identity kan worden gebruikt om de klant toegang te geven tot extra bestaande bronnen. De identiteit wordt beheerd door het Azure-platform en vereist niet dat u geheimen indient of roteert. Zie [Beheerde identiteiten voor Azure-resources voor](../../active-directory/managed-identities-azure-resources/overview.md)meer informatie over beheerde identiteiten in Azure Active Directory (AAD).

Uw aanvraag kan worden toegekend twee soorten identiteiten:

- Een **door het systeem toegewezen identiteit** is gekoppeld aan uw toepassing en wordt verwijderd als uw app wordt verwijderd. Een app kan slechts één door het systeem toegewezen identiteit hebben.
- Een door de gebruiker toegewezen identiteit is een zelfstandige **Azure-bron** die aan uw app kan worden toegewezen. Een app kan meerdere door de gebruiker toegewezen identiteiten hebben.

## <a name="how-to-use-managed-identity"></a>Managed Identity gebruiken

Managed Identity maakt veel scenario's mogelijk voor beheerde toepassingen. Enkele veelvoorkomende scenario's die kunnen worden opgelost zijn:

- Een beheerde toepassing implementeren die is gekoppeld aan bestaande Azure-resources. Een voorbeeld is het implementeren van een Virtuele Azure-machine (VM) in de beheerde toepassing die is gekoppeld aan een [bestaande netwerkinterface.](../../virtual-network/virtual-network-network-interface-vm.md)
- De beheerde toepassing en uitgever toegang verlenen tot Azure-bronnen buiten de **beheerde brongroep**.
- Een operationele identiteit van beheerde toepassingen voor activiteitenlogboek en andere services binnen Azure.

## <a name="adding-managed-identity"></a>Beheerde identiteit toevoegen

Voor het maken van een beheerde toepassing met een beheerde identiteit moet een extra eigenschap worden ingesteld op de Azure-bron. In het volgende voorbeeld wordt een **voorbeeldidentiteitseigenschap** weergegeven:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

Er zijn twee veelvoorkomende manieren om een beheerde toepassing met **identiteit**te maken: [CreateUIDefinition.json-](./create-uidefinition-overview.md) en [Azure Resource Manager-sjablonen](../templates/template-syntax.md). Voor eenvoudige scenario's voor één create moet CreateUIDefinition worden gebruikt om Beheerde identiteit in te schakelen, omdat het een rijkere ervaring biedt. Wanneer het echter gaat om geavanceerde of complexe systemen waarvoor geautomatiseerde of meerdere beheerde toepassingsimplementaties nodig zijn, kunnen sjablonen worden gebruikt.

### <a name="using-createuidefinition"></a>CreateUiDefinition gebruiken

Een beheerde toepassing kan worden geconfigureerd met Beheerde identiteit via [de CreateUIDefinition.json](./create-uidefinition-overview.md). In de [sectie uitvoer](./create-uidefinition-overview.md#outputs) `managedIdentity` kan de sleutel worden gebruikt om de identiteitseigenschap van de sjabloon Beheerde toepassing te overschrijven. De voorbeeldbalg maakt **een systeemtoegewezen** identiteit mogelijk op de beheerde toepassing. Complexere identiteitsobjecten kunnen worden gevormd door createuidefinitie-elementen te gebruiken om de consument om inputs te vragen. Deze ingangen kunnen worden gebruikt om beheerde toepassingen te construeren met **een door de gebruiker toegewezen identiteit.**

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>Wanneer createuidefinition gebruiken voor beheerde identiteit

Hieronder vindt u enkele aanbevelingen over het gebruik van CreateUIDefinition voor het inschakelen van Beheerde identiteit op beheerde toepassingen.

- Het maken van beheerde toepassingen gaat via de Azure-portal of marketplace.
- De Managed Identity vereist complexe input van de consument.
- De beheerde identiteit is nodig bij het maken van de beheerde toepassing.

#### <a name="systemassigned-createuidefinition"></a>Systeemtoegewezen CreateUiDefinition

Een basisCreateUIDefinition waarmee de SystemAssigned-identiteit voor de beheerde toepassing kan worden toegewezen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

Een basis -CreateUIDefinition die een **door de gebruiker toegewezen identiteitsbron** als invoer gebruikt en de door gebruikers toegewezen identiteit voor de beheerde toepassing inschakelt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

Met de bovenstaande CreateUIDefinition.json wordt een gebruikerservaring gemaakt met een tekstvak waarmee een consument de **door de gebruiker toegewezen identiteits-Azure-bron-id** kan invoeren. De gegenereerde ervaring zou eruit zien als volgt:

![Voorbeeld van door de gebruiker toegewezen identiteit CreateUIDefinition](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken

> [!NOTE]
> Marketplace Managed Application-sjablonen worden automatisch gegenereerd voor klanten die via de Azure-portal gaan, en ervaring creëren.
> Voor deze scenario's moet de `managedIdentity` uitvoersleutel op de CreateUIDefinition worden gebruikt om de identiteit in te schakelen.

De Beheerde identiteit kan ook worden ingeschakeld via Azure Resource Manager-sjablonen. De voorbeeldbalg maakt **een systeemtoegewezen** identiteit mogelijk op de beheerde toepassing. Complexere identiteitsobjecten kunnen worden gevormd door sjabloonparameters van Azure Resource Manager te gebruiken om invoer te bieden. Deze ingangen kunnen worden gebruikt om beheerde toepassingen te construeren met **een door de gebruiker toegewezen identiteit.**

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>Wanneer azure resource manager-sjablonen gebruiken voor Beheerde identiteit

Hieronder vindt u enkele aanbevelingen over het gebruik van Azure Resource Manager-sjablonen voor het inschakelen van Beheerde identiteit op beheerde toepassingen.

- Beheerde toepassingen kunnen programmatisch worden geïmplementeerd op basis van een sjabloon.
- Aangepaste roltoewijzingen voor de beheerde identiteit zijn nodig om de beheerde toepassing in te richten.
- De beheerde toepassing heeft de stroom voor het maken van Azure-portal en marktplaatsen niet nodig.

#### <a name="systemassigned-template"></a>Sjabloon SystemAssigned

Een basissjabloon voor Azure Resource Manager waarmee een beheerde toepassing wordt geïmplementeerd met **een door het systeem toegewezen** identiteit.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned-sjabloon

Een basissjabloon voor Azure Resource Manager waarmee een beheerde toepassing wordt geïmplementeerd met een **door de gebruiker toegewezen identiteit**.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Toegang verlenen tot Azure-bronnen

Zodra een beheerde toepassing een identiteit heeft gekregen, kan deze toegang krijgen tot bestaande azure-bronnen. Dit proces kan worden uitgevoerd via de IAM-interface (Access Control) in de Azure-portal. De naam van de beheerde toepassing of **door de gebruiker toegewezen identiteit** kan worden doorzocht om een roltoewijzing toe te voegen.

![Roltoewijzing toevoegen voor Beheerde toepassing](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>Bestaande Azure-bronnen koppelen

> [!NOTE]
> Een **door de gebruiker toegewezen identiteit** moet worden [geconfigureerd](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) voordat de beheerde toepassing wordt geïmplementeerd. Bovendien wordt gekoppelde resourceimplementatie van beheerde toepassingen alleen ondersteund voor de **marktsoort.**

Managed Identity kan ook worden gebruikt om een beheerde toepassing te implementeren die toegang tot bestaande bronnen vereist tijdens de implementatie. Wanneer de beheerde toepassing door de klant is ingericht, kunnen **door de gebruiker toegewezen identiteiten** worden toegevoegd om extra autorisaties te bieden aan de **mainTemplate-implementatie.**

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>De CreateUIDefinition maken met een gekoppelde resource

Wanneer u de implementatie van de beheerde toepassing koppelt aan bestaande resources, moet zowel de bestaande **Azure-bron** als een door de gebruiker toegewezen identiteit worden gekoppeld aan de toepasselijke roltoewijzing op die bron.

 Een voorbeeld van CreateUIDefinition waarvoor twee ingangen nodig zijn: een netwerkinterfacebron-id en een door de gebruiker toegewezen identiteitsbron-id.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

Deze CreateUIDefinition.json genereert een gebruikerservaring die twee velden heeft. In het eerste veld kan de gebruiker de Azure-bron-id invoeren voor de resource die wordt gekoppeld aan de implementatie van beheerde toepassingen. De tweede is dat een consument de **door de gebruiker toegewezen identiteitsAzure-bron-id** invoert, die toegang heeft tot de gekoppelde Azure-bron. De gegenereerde ervaring zou eruit zien als volgt:

![Voorbeeld van CreateUIDefinition met twee ingangen: een netwerkinterfacebron-id en een door de gebruiker toegewezen identiteitsbron-id](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>Het ontwerpen van de mainTemplate met een gekoppelde resource

Naast het bijwerken van de CreateUIDefinition, moet de hoofdsjabloon ook worden bijgewerkt om de doorgegeven in gekoppelde resource-ID te accepteren. De hoofdsjabloon kan worden bijgewerkt om de nieuwe uitvoer te accepteren door een nieuwe parameter toe te voegen. Aangezien `managedIdentity` de uitvoer de waarde op de gegenereerde beheerde toepassingssjabloon overschrijft, wordt deze niet doorgegeven aan de hoofdsjabloon en mag deze niet worden opgenomen in de sectie parameters.

Een voorbeeldsjabloon waarmee het netwerkprofiel wordt ingesteld op een bestaande netwerkinterface die wordt geleverd door de CreateUIDefinition.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>De beheerde toepassing gebruiken met een gekoppelde resource

Zodra het pakket Beheerde toepassing is gemaakt, kan de beheerde toepassing worden verbruikt via de Azure-portal. Voordat het kan worden geconsumeerd, zijn er verschillende vereiste stappen.

- Er moet een exemplaar van de vereiste gekoppelde Azure-bron worden gemaakt.
- De **door de gebruiker toegewezen identiteit** moet worden gemaakt en [roltoewijzingen aan](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) de gekoppelde resource worden gegeven.
- De bestaande gekoppelde **resource-id** en de door de gebruiker toegewezen identiteits-id worden verstrekt aan de CreateUIDefinition.

## <a name="accessing-the-managed-identity-token"></a>Toegang tot het token Beheerde identiteit

Het token van de beheerde toepassing `listTokens` is nu toegankelijk via de api van de uitgevertenant. Een voorbeeldaanvraag kan er uitzien als:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

Hoofdparameters aanvragen:

Parameter | Vereist | Beschrijving
---|---|---
autorisatiePubliek | *nee* | De App ID URI van de doelbron. Het is `aud` ook de (publieks)claim van het uitgegeven token. De standaardwaardehttps://management.azure.com/is " "
userAssignedIdentities | *nee* | De lijst met door de gebruiker toegewezen beheerde identiteiten om een token voor op te halen. Als dit `listTokens` niet is opgegeven, retourneert u het token voor de door het systeem toegewezen beheerde identiteit.


Een voorbeeldreactie kan er uitzien als:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

Het antwoord bevat een reeks `value` tokens onder de eigenschap:

Parameter | Beschrijving
---|---
access_token | Het gevraagde toegangstoken.
expires_in | Het aantal seconden dat het toegangstoken geldig is.
expires_on | De tijdspanne wanneer het toegangstoken verloopt. Dit wordt weergegeven als het aantal seconden uit het tijdperk.
not_before | De tijdspanne waarin het toegangstoken van kracht wordt. Dit wordt weergegeven als het aantal seconden uit het tijdperk.
autorisatiePubliek | Het `aud` (publiek) van het toegangstoken is aangevraagd. Dit is hetzelfde als wat `listTokens` in het verzoek is vermeld.
resourceId | De Azure-bron-id voor het uitgegeven token. Dit is de beheerde toepassings-id of de door de gebruiker toegewezen identiteits-id.
token_type | Het type token.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een beheerde toepassing configureren met een aangepaste provider](../custom-providers/overview.md)

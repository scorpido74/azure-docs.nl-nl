---
title: Een toepassing toegang verlenen tot andere Azure-bronnen
description: In dit artikel wordt uitgelegd hoe u uw servicefabric-toepassing met beheerde identiteit toegang verleent tot andere Azure-bronnen die azure Active Directory-gebaseerde verificatie ondersteunen.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614790"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>De beheerde identiteitstoegang van een Service Fabric-toepassing verlenen tot Azure-bronnen (voorbeeld)

Voordat de toepassing de beheerde identiteit kan gebruiken om toegang te krijgen tot andere bronnen, moeten machtigingen worden verleend aan die identiteit op de beveiligde Azure-bron die wordt geopend. Het verlenen van machtigingen is meestal een beheeractie op het 'controlevlak' van de Azure-service die eigenaar is van de beveiligde bron die via Azure Resource Manager wordt gerouteerd, waardoor elke toepasselijke op rollen gebaseerde toegangscontrole wordt afgedwongen.

De exacte volgorde van de stappen is dan afhankelijk van het type Azure-bron dat wordt geopend, evenals de taal/client die wordt gebruikt om machtigingen te verlenen. De rest van het artikel gaat uit van een door de gebruiker toegewezen identiteit die aan de toepassing is toegewezen en bevat een aantal typische voorbeelden voor uw gemak, maar het is op geen enkele wijze een uitputtende referentie voor dit onderwerp; raadpleeg de documentatie van de betreffende Azure-services voor actuele instructies over het verlenen van machtigingen.  

## <a name="granting-access-to-azure-storage"></a>Toegang verlenen tot Azure Storage
U de beheerde identiteit van de Service Fabric-toepassing (in dit geval door de gebruiker toegewezen) gebruiken om de gegevens uit een Azure-opslagblob op te halen. Geef de identiteit de vereiste machtigingen in de Azure-portal toe aan de volgende stappen:

1. Navigeren naar het opslagaccount
2. Klik op de koppeling Toegangsbeheer (IAM) in het linkerpaneel.
3. (facultatief) Controleer bestaande toegang: selecteer Door het systeem toegewezen beheerde identiteit in het besturingselement 'Zoeken'; de juiste identiteit selecteren in de resultatenlijst van de daarop
4. Klik op + Roltoewijzing toevoegen boven aan de pagina om een nieuwe roltoewijzing toe te voegen voor de identiteit van de toepassing.
Selecteer onder Rol in de vervolgkeuzelijst De optie Opslagblob-gegevenslezer.
5. Kies in de volgende vervolgkeuzelijst `User assigned managed identity`onder Toegang toewijzen tot de optie .
6. Controleer vervolgens of het juiste abonnement wordt weergegeven in de vervolgkeuzelijst Abonnement, en stel Resourcegroep in op Alle resourcegroepen.
7. Kies onder Selecteren de gebruikersinterface die overeenkomt met de servicefabric-toepassing en klik op Opslaan.

Ondersteuning voor door het systeem toegewezen servicefabricbeheerde identiteiten omvat geen integratie in de Azure-portal. Als uw toepassing een door het systeem toegewezen identiteit gebruikt, moet u eerst de client-id van `Azure AD user, group, or service principal` de identiteit van de toepassing vinden en vervolgens de bovenstaande stappen herhalen, maar de optie selecteren in het besturingselement Zoeken.

## <a name="granting-access-to-azure-key-vault"></a>Toegang verlenen tot Azure Key Vault
Op dezelfde manier u met toegang tot opslag gebruikmaken van de beheerde identiteit van een Service Fabric-toepassing om toegang te krijgen tot een Azure-sleutelkluis. De stappen voor het verlenen van toegang in de Azure-portal zijn vergelijkbaar met de bovenstaande stappen en worden hier niet herhaald. Raadpleeg de afbeelding hieronder voor verschillen.

![Toegangsbeleid voor belangrijke vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

In het volgende voorbeeld wordt geïllustreerd dat toegang tot een kluis wordt toegekend via een sjabloonimplementatie; voeg het fragment(s) hieronder toe `resources` als een ander item onder het element van de sjabloon. Het voorbeeld toont toegang verlenen voor zowel de gebruiker toegewezen en systeem-toegewezen identiteitstypen, respectievelijk - kies de toepasselijke.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
En voor door het systeem toegewezen beheerde identiteiten:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Zie [Vaults - Toegangsbeleid bijwerken](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Een Azure Service Fabric-toepassing implementeren met een beheerde identiteit met systeemtoegewezen](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
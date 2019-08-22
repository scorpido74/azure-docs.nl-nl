---
title: 'Azure-Service Fabric: een Service Fabric toepassing toegang verlenen tot andere Azure-resources | Microsoft Docs'
description: In dit artikel wordt uitgelegd hoe u uw beheerde identiteits Service Fabric toegang tot toepassingen kunt verlenen aan andere Azure-resources die op Azure Active Directory gebaseerde verificatie ondersteunen.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: 07b26fb86392b26ef45c4370741a32efc7dc436b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640916"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Het verlenen van toegang tot Azure-resources door de beheerde identiteit van een Service Fabric-toepassing (preview)

Voordat de toepassing de beheerde identiteit voor toegang tot andere bronnen kan gebruiken, moeten er machtigingen worden verleend aan deze identiteit op de beveiligde Azure-resource die wordt geopend. Het verlenen van machtigingen is doorgaans een beheer actie op het ' besturings vlak ' van de Azure-service die eigenaar is van de beveiligde resource die wordt gerouteerd via Azure Resource Manager, waardoor alle toepasselijke op rollen gebaseerde toegangs controle wordt afgedwongen.

De exacte volg orde van de stappen is afhankelijk van het type Azure-resource waartoe toegang wordt verkregen en de taal/client die wordt gebruikt om machtigingen te verlenen. In de rest van het artikel wordt ervan uitgegaan dat er een door de gebruiker toegewezen identiteit aan de toepassing is toegewezen en een aantal typische voor beelden bevat voor uw gemak, maar dit is op geen enkele manier een uitgebreide verwijzing naar dit onderwerp. Raadpleeg de documentatie van de betreffende Azure-Services voor actuele instructies voor het verlenen van machtigingen.  

## <a name="granting-access-to-azure-storage"></a>Toegang verlenen tot Azure Storage
U kunt de Service Fabric beheerde identiteit van de toepassing (in dit geval toegewezen door de gebruiker) gebruiken om de gegevens op te halen uit een Azure Storage-blob. Wijs de vereiste machtigingen toe aan de Azure Portal met de volgende stappen:

1. Navigeer naar het opslag account
2. Klik op de koppeling toegangs beheer (IAM) in het linkerdeel venster.
3. Beschrijving Controleer de bestaande toegang: een door het systeem of de gebruiker toegewezen beheerde identiteit selecteren in het besturings element ' zoeken '; Selecteer de juiste identiteit in de lijst met resultaten
4. Klik boven aan de pagina op functie toewijzing toevoegen om een nieuwe roltoewijzing toe te voegen voor de identiteit van de toepassing.
Selecteer onder rol in de vervolg keuzelijst Storage BLOB data Reader.
5. Kies `User assigned managed identity`in de volgende vervolg keuzelijst toegang toewijzen aan.
6. Controleer vervolgens of het juiste abonnement wordt weer gegeven in de vervolg keuzelijst abonnement en stel de resource groep vervolgens in op alle resource groepen.
7. Kies onder selecteren de UAI die overeenkomt met de Service Fabric toepassing en klik vervolgens op opslaan.

Ondersteuning voor door het systeem toegewezen Service Fabric beheerde identiteiten omvatten geen integratie in de Azure Portal; Als uw toepassing gebruikmaakt van een door het systeem toegewezen identiteit, moet u eerst de client-id van de identiteit van de toepassing zoeken en vervolgens de bovenstaande stappen herhalen, maar `Azure AD user, group, or service principal` de optie selecteren in het besturings element zoeken.

## <a name="granting-access-to-azure-key-vault"></a>Toegang verlenen tot Azure Key Vault
Net als bij het openen van opslag kunt u gebruikmaken van de beheerde identiteit van een Service Fabric toepassing om toegang te krijgen tot een Azure-sleutel kluis. De stappen voor het verlenen van toegang in de Azure Portal zijn vergelijkbaar met die hierboven vermeld, en worden hier niet herhaald. Raadpleeg de onderstaande afbeelding voor verschillen.

![Toegangs beleid Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Het volgende voor beeld illustreert het verlenen van toegang tot een kluis via een sjabloon implementatie. Voeg het volgende fragment onder het `resources` element van de sjabloon toe als een andere vermelding.

```json
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

Zie [kluizen-Access Policy bijwerken](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)

* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
---
title: Encryptie-at-rest met door de klant beheerde sleutels
description: Meer informatie over versleuteling in de rest van uw Azure-containerregister en hoe u uw register versleutelen met een door de klant beheerde sleutel die is opgeslagen in Azure Key Vault
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: fe0736f83db2ba5b872d50bcf1262ca423de9f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498944"
---
# <a name="encryption-using-customer-managed-keys"></a>Versleuteling met door de klant beheerde sleutels

Wanneer u afbeeldingen en andere artefacten opslaat in een Azure-containerregister, versleutelt Azure de registerinhoud in rust automatisch met [servicebeheersleutels.](../security/fundamentals/encryption-atrest.md#data-encryption-models) U standaardversleuteling aanvullen met een extra versleutelingslaag met een sleutel die u maakt en beheert in Azure Key Vault. In dit artikel vindt u de stappen met de Azure CLI en de Azure-portal.

Server-side encryptie met door de klant beheerde sleutels wordt ondersteund door integratie met [Azure Key Vault.](../key-vault/key-vault-overview.md) U uw eigen versleutelingssleutels maken en opslaan in een sleutelkluis, of u de API's van Azure Key Vault gebruiken om versleutelingssleutels te genereren. Met Azure Key Vault u ook het gebruik van de sleutel controleren.

Deze functie is beschikbaar in de servicelaag Van het Premium-containerregister. **Premium** Zie [SKU's](container-registry-skus.md)voor Azure Container Registry voor informatie over lagen en limieten voor registerservice.

> [!IMPORTANT]
> Deze functie is momenteel in preview en er zijn enkele [beperkingen](#preview-limitations) van toepassing. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.
>
   
## <a name="preview-limitations"></a>Preview-beperkingen 

* U deze functie momenteel alleen inschakelen wanneer u een register maakt.
* Nadat u een door de klant beheerde sleutel in een register hebt ingeschakeld, u deze niet uitschakelen.
* [Contentvertrouwensrelatie](container-registry-content-trust.md) wordt momenteel niet ondersteund in een register versleuteld met een door de klant beheerde sleutel.
* In een register dat is versleuteld met een door de klant beheerde sleutel, worden logboeken voor [ACR-taken](container-registry-tasks-overview.md) momenteel slechts 24 uur bewaard. Als u logboeken voor een langere periode wilt bewaren, raadpleegt u richtlijnen voor [het exporteren en opslaan van logboeken voor taakuitvoer.](container-registry-tasks-logs.md#alternative-log-storage)

## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI-stappen in dit artikel wilt gebruiken, hebt u Azure CLI-versie 2.2.0 of hoger nodig. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Klantbeheersleutel inschakelen - CLI

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Voer indien nodig de opdracht [az-groep maken om][az-group-create] een resourcegroep te maken voor het maken van de sleutelkluis, het containerregister en andere vereiste resources.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken

Maak een door de gebruiker toegewezen [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) met de opdracht [AZ-identiteit maken.][az-identity-create] Deze identiteit wordt door uw register gebruikt om toegang te krijgen tot de Key Vault-service.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

Neem in de opdrachtuitvoer nota `id` van `principalId`de volgende waarden: en . U hebt deze waarden in latere stappen nodig om registertoegang tot de sleutelkluis te configureren.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Voor het gemak slaat u deze waarden op in omgevingsvariabelen:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Maak een sleutelkluis met [az keyvault maken][az-keyvault-create] om een door de klant beheerde sleutel voor registerversleuteling op te slaan. 

Om gegevensverlies te voorkomen als gevolg van onbedoelde sleutel- of sleutelkluisverwijderingen, moet u de volgende instellingen inschakelen: **Soft delete** and **Purge protection**. In het volgende voorbeeld worden parameters voor deze instellingen weergegeven: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Toegangsbeleid voor belangrijke kluizen toevoegen

Configureer een beleid voor de sleutelkluis, zodat de identiteit er toegang toe heeft. In de volgende opdracht [az keyvault-setbeleid][az-keyvault-set-policy] geeft u de hoofd-ID van de beheerde identiteit die u hebt gemaakt en die u eerder hebt opgeslagen in een omgevingsvariabele. Stel belangrijke machtigingen in om Key **te krijgen,** **uit pakken**en sleutel **te verpakken**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Sleutel maken en sleutel-ID

Voer de opdracht [az keyvault uit om][az-keyvault-key-create] een sleutel in de sleutelkluis te maken.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Neem in de opdrachtuitvoer nota van `kid`de ID van de sleutel. U gebruikt deze id in de volgende stap:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
Voor het gemak, sla deze waarde in een omgevingsvariabele:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Een register maken met door de klant beheerde sleutel

Voer de opdracht [az acr create uit][az-acr-create] om een register te maken en de door de klant beheerde sleutel in te schakelen. Geef de beheerde identiteitshoofdse id en de sleutel-id door, die eerder zijn opgeslagen in omgevingsvariabelen:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Versleutelingsstatus weergeven

Voer de opdracht [az acr-versleuteling uit][az-acr-encryption-show] om aan te geven of registerversleuteling met een door de klant beheerde sleutel is ingeschakeld:

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Klantbeheersleutel inschakelen - portal

### <a name="create-a-managed-identity"></a>Een beheerde identiteit maken

Maak een door de gebruiker toegewezen [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in de Azure-portal. Zie Een [door de gebruiker toegewezen identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)voor stappen.

Let op de **resourcenaam** van de beheerde identiteit. Je hebt deze naam in latere stappen nodig.

![Beheerde identiteit met gebruiker maken in de Azure-portal](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Zie Snelstart: Een geheim instellen en ophalen uit Azure Key Vault voor stappen om een sleutelkluis te maken [met behulp van de Azure-portal.](../key-vault/quick-create-portal.md)

Wanneer u een sleutelkluis maakt voor een door de klant beheerde sleutel, moet u op het tabblad **Basisbeginselen** de volgende beveiligingsinstellingen inschakelen: **Bescherming voor zachte delete** en **purge.** Met deze instellingen u gegevensverlies voorkomen als gevolg van het per ongeluk verwijderen van sleutels of sleutelkluizen.

![Sleutelkluis maken in de Azure-portal](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Toegangsbeleid voor belangrijke kluizen toevoegen

Configureer een beleid voor de sleutelkluis, zodat de identiteit er toegang toe heeft.

1. Navigeer naar uw sleutelkluis.
1. Selecteer > **Instellingentoegangsbeleid > +Toegangsbeleid toevoegen**. **Settings**
1. Selecteer **Belangrijke machtigingen**en selecteer **De**toets uitpakken en **Toets uitpakken**. **Unwrap Key**
1. Selecteer **Hoofdselecteer** selecteren en selecteer de resourcenaam van uw door de gebruiker toegewezen beheerde identiteit.  
1. Selecteer **Toevoegen**en selecteer **Opslaan**.

![Toegangsbeleid voor belangrijke kluizen maken](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Toets maken

1. Navigeer naar uw sleutelkluis.
1. Selecteer **Instellingentoetsen** > **Keys**.
1. Selecteer **+Genereren/importeren** en voer een unieke naam in voor de sleutel.
1. Accepteer de resterende standaardwaarden en selecteer **Maken**.
1. Selecteer na het maken de sleutel en noteer de huidige sleutelversie.

### <a name="create-azure-container-registry"></a>Azure Container Registry maken

1. Selecteer **Een containerregister voor resourcecontainers** > **Containers** > **maken**.
1. Selecteer of maak een resourcegroep op het tabblad **Basisbeginselen** en voer een registernaam in. Selecteer **Premium**in **SKU**.
1. **Selecteer**ingeschakeld op het tabblad **Versleuteling** in de door de **klant beheerde sleutel**.
1. Selecteer **in Identiteit**de beheerde identiteit die u hebt gemaakt.
1. Selecteer in **Versleutelingssleutel**De optie **Selecteren in Sleutelkluis**.
1. Selecteer in de **sleutelkluis van Azure Key Vault** de sleutelkluis, sleutel en versie die u in de vorige sectie hebt gemaakt.
1. Selecteer **op** het tabblad Versleuteling de optie **Controleren + maken**.
1. Selecteer **Maken** om de registerinstantie te implementeren.

![Een containerregister maken in de Azure-portal](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Als u de versleutelingsstatus van uw register in de portal wilt zien, navigeert u naar uw register. Selecteer **onder Instellingen**de optie Versleuteling **(voorbeeld)**.

## <a name="enable-customer-managed-key---template"></a>Door de klant beheerde sleutel inschakelen - sjabloon

U ook een resourcemanagersjabloon gebruiken om een register te maken en versleuteling in te schakelen met een door de klant beheerde sleutel. 

Met de volgende sjabloon wordt een nieuw containerregister en een door de gebruiker toegewezen beheerde identiteit gemaakt. Kopieer de volgende inhoud naar een nieuw bestand en `CMKtemplate.json`sla het op met een bestandsnaam zoals .

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Volg de stappen in de vorige secties om de volgende bronnen te maken:

* Sleutelkluis, geïdentificeerd op naam
* Sleutelkluissleutel, geïdentificeerd door sleutel-ID

Voer de volgende [opdracht voor het maken van az-groepsimplementatie uit][az-group-deployment-create] om het register te maken met behulp van het vorige sjabloonbestand. Geef waar aangegeven een nieuwe registernaam en beheerde identiteitsnaam op, evenals de sleutelkluisnaam en sleutel-id die u hebt gemaakt. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Versleutelingsstatus weergeven

Voer de opdracht [az acr encryption show-status] [az-acr-encryption-show-status] uit om de status van registerversleuteling weer te geven:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>Het register gebruiken

Nadat u een register hebt ingeschakeld om gegevens te versleutelen met behulp van een door de klant beheerde sleutel, u dezelfde registerbewerkingen uitvoeren die u uitvoert in een register dat niet is versleuteld met een door de klant beheerde sleutel. U bijvoorbeeld verifiëren met het register en Docker-afbeeldingen pushen. Zie voorbeeldopdrachten in [Push en trek een afbeelding](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Toets roteren

U een door de klant beheerde sleutel roteren in Azure Key Vault volgens uw nalevingsbeleid. Maak een nieuwe sleutel en werk het register bij om gegevens te versleutelen met de nieuwe sleutel. U deze stappen uitvoeren met Azure CLI of in de portal.

Voer bijvoorbeeld de opdracht az [keyvault create uit][az-keyvault-key-create] om een nieuwe sleutel te maken:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Voer vervolgens de opdracht [az acr-encryptie rotate-key][az-acr-encryption-rotate-key] uit, door de nieuwe sleutel-ID en de hoofd-id van de eerder geconfigureerde beheerde identiteit door te geven:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Sleutel intrekken

Trek de door de klant beheerde versleutelingssleutel in door het toegangsbeleid op de sleutelkluis te wijzigen of door de sleutel te verwijderen. Gebruik bijvoorbeeld de opdracht [voor het verwijderen van az-keyvault][az-keyvault-delete-policy] om het toegangsbeleid van de beheerde identiteit die door uw register wordt gebruikt, te wijzigen. Bijvoorbeeld:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

Het intrekken van de sleutel blokkeert effectief de toegang tot alle registergegevens, omdat het register geen toegang heeft tot de versleutelingssleutel. Als de toegang tot de sleutel is ingeschakeld of de verwijderde sleutel is hersteld, kiest uw register de sleutel zodat u opnieuw toegang hebt tot de versleutelde registergegevens.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [versleuteling in rust in Azure](../security/fundamentals/encryption-atrest.md).
* Meer informatie over toegangsbeleid en hoe u [de toegang tot een sleutelkluis beveiligen.](../key-vault/key-vault-secure-your-key-vault.md)
* Ga naar de [ACR GitHub-site](https://aka.ms/acr/issues)om feedback te geven over door klanten beheerde sleutels voor Azure Container Registry.


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show

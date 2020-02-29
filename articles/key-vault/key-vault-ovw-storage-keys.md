---
title: Sleutels voor opslag accounts beheren met Azure Key Vault en de Azure CLI
description: Sleutels voor opslag accounts bieden een naadloze integratie tussen Azure Key Vault en toegang tot de sleutel op basis van sleutels tot een Azure-opslag account.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 104f3423b07eaa3269ffccc054cd2f779bbdabf8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199815"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Sleutels voor opslag accounts beheren met Key Vault en de Azure CLI

Een Azure-opslag account gebruikt referenties die bestaan uit een account naam en een sleutel. De sleutel wordt automatisch gegenereerd en fungeert als een wacht woord in plaats van een als cryptografische sleutel. Key Vault beheert Storage-account sleutels door ze op te slaan als [Key Vault geheimen](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

U kunt met behulp van de sleutel functie voor de Key Vault beheerde opslag accounts een lijst (synchronisatie) met een Azure-opslag account maken en de sleutels periodiek opnieuw genereren (draaien). U kunt sleutels voor zowel opslag accounts als klassieke opslag accounts beheren.

Houd rekening met de volgende punten wanneer u de sleutel functie beheerde opslag account gebruikt:

- Sleutel waarden worden nooit geretourneerd als antwoord op een aanroeper.
- Alleen Key Vault moet de sleutels van uw opslag account beheren. Beheer de sleutels niet zelf en voorkom conflicten met Key Vault processen.
- Alleen een enkel Key Vault-object moet de sleutels van het opslag account beheren. Geen sleutel beheer van meerdere objecten toestaan.
- U kunt Key Vault aanvragen om uw opslag account te beheren met een gebruikers-principal, maar niet met een service-principal.
- Sleutels opnieuw genereren met behulp van alleen Key Vault. De sleutels van uw opslag account niet hand matig opnieuw genereren.

U kunt het beste Azure Storage Integration gebruiken met Azure Active Directory (Azure AD), de cloud-gebaseerde identiteits-en toegangs beheer service van micro soft. Azure AD-integratie is beschikbaar voor [Azure-blobs en-wacht rijen](../storage/common/storage-auth-aad.md)en biedt toegang tot Azure Storage op basis van OAuth2-tokens (net als Azure Key Vault).

Met Azure AD kunt u uw client toepassing verifiëren met behulp van een toepassings-of gebruikers-id in plaats van de referenties van het opslag account. U kunt een door [Azure AD beheerde identiteit](/azure/active-directory/managed-identities-azure-resources/) gebruiken wanneer u in azure uitvoert. Beheerde identiteiten verwijderen de nood zaak voor client verificatie en het opslaan van referenties in of met uw toepassing.

Azure AD gebruikt op rollen gebaseerd toegangs beheer (RBAC) voor het beheren van autorisatie, die ook door Key Vault wordt ondersteund.

## <a name="service-principal-application-id"></a>Service-Principal-toepassings-ID

Een Azure AD-Tenant voorziet elke geregistreerde toepassing van een [Service-Principal](/azure/active-directory/develop/developer-glossary#service-principal-object). De Service-Principal fungeert als de toepassings-ID, die wordt gebruikt tijdens het instellen van de autorisatie voor toegang tot andere Azure-resources via RBAC.

Key Vault is een micro soft-toepassing die vooraf is geregistreerd in alle Azure AD-tenants. Key Vault is geregistreerd onder dezelfde toepassings-ID in elke Azure-Cloud.

| Tenants | Cloud | Toepassings-id |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Open bare Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Overige  | Alle | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Vereisten

Als u deze hand leiding wilt volt ooien, moet u eerst het volgende doen:

- [Installeer de Azure cli](/cli/azure/install-azure-cli).
- [Een sleutel kluis maken](quick-create-cli.md)
- [Maak een Azure Storage-account](../storage/common/storage-account-create.md?tabs=azure-cli). De naam van het opslag account mag alleen kleine letters en cijfers bevatten. De naam moet tussen de 3 en 24 tekens lang zijn.
      
## <a name="manage-storage-account-keys"></a>Sleutels voor opslag accounts beheren

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Verifieer uw Azure CLI-sessie met de opdracht [AZ login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault toegang geven tot uw opslag account

Gebruik de opdracht Azure CLI [AZ Role Assignment maken](/cli/azure/role/assignment?view=azure-cli-latest) om Key Vault toegang te geven tot uw opslag account. Geef de opdracht de volgende parameter waarden:

- `--role`: Geef de functie ' rol van account sleutel operator voor opslag ' door. Deze rol beperkt de toegangs Scope tot uw opslag account. Geef voor een klassiek opslag account de rol ' klassieke account sleutel operator ' door.
- `--assignee-object-id`: Geef de waarde ' 93c27d83-f79b-4cb2-8dd4-4aa716542e74 ' door. Dit is de object-ID voor Key Vault in de open bare Azure-Cloud. (Als u de object-ID voor Key Vault wilt ophalen in de Azure Government Cloud, raadpleegt u [Service Principal Application id](#service-principal-application-id).)
- `--scope`: Geef de resource-ID van uw opslag account op in de vorm `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Als u uw abonnement-ID wilt vinden, gebruikt u de opdracht Azure CLI [AZ account list](/cli/azure/account?view=azure-cli-latest#az-account-list) . Als u de naam van uw opslag account en de resource groep voor het opslag account wilt zoeken, gebruikt u de opdracht Azure CLI [AZ Storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Een door Key Vault beheerd opslag account maken

 Maak een Key Vault beheerd opslag account met behulp van de Azure CLI [AZ-opslag](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) opdracht. Stel een regeneratie periode van 90 dagen in. Na 90 dagen Key Vault `key1` opnieuw gegenereerd en wordt de actieve sleutel van `key2` naar `key1`gewisseld. `key1` wordt vervolgens als de actieve sleutel gemarkeerd. Geef de opdracht de volgende parameter waarden:

- `--vault-name`: Geef de naam van de sleutel kluis door. Als u de naam van de sleutel kluis wilt weten, gebruikt u de opdracht Azure CLI AZ, sleutel [kluis List](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) .
- `-n`: Geef de naam van uw opslag account door. Als u de naam van uw opslag account wilt weten, gebruikt u de opdracht Azure CLI [AZ Storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .
- `--resource-id`: Geef de resource-ID van uw opslag account op in de vorm `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Als u uw abonnement-ID wilt vinden, gebruikt u de opdracht Azure CLI [AZ account list](/cli/azure/account?view=azure-cli-latest#az-account-list) . Als u de naam van uw opslag account en de resource groep voor het opslag account wilt zoeken, gebruikt u de opdracht Azure CLI [AZ Storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Shared Access Signature-tokens

U kunt ook Key Vault vragen om de tokens voor Shared Access-hand tekeningen te genereren. Een shared access signature biedt gedelegeerde toegang tot resources in uw opslagaccount. U kunt clients toegang verlenen tot resources in uw opslag account zonder uw account sleutels te delen. Een Shared Access Signature biedt u een veilige manier om uw opslag resources te delen zonder in te boeten voor uw account sleutels.

De opdrachten in deze sectie voeren de volgende acties uit:

- Stel een account in voor de definitie van de gedeelde Access-hand tekening `<YourSASDefinitionName>`. De definitie wordt ingesteld op een Key Vault beheerd opslag account `<YourStorageAccountName>` in uw sleutel kluis `<YourKeyVaultName>`.
- Maak een account voor Shared Access Signature-token voor blob-, bestands-, tabel-en wachtrij Services. Het token is gemaakt voor resource type-service,-container en-object. Het token wordt gemaakt met alle machtigingen, via https en met de opgegeven begin-en eind datum.
- Stel in de kluis een definitie in van een Key Vault Managed Storage-hand tekening voor gedeelde opslag. De definitie heeft de sjabloon-URI van het Shared Access Signature-token dat is gemaakt. De definitie heeft het type gedeelde toegangs handtekening `account` en is N dagen geldig.
- Controleer of de Shared Access-hand tekening is opgeslagen in de sleutel kluis als een geheim.

### <a name="create-a-shared-access-signature-token"></a>Een token voor een Shared Access-hand tekening maken

Maak een definitie van een gedeelde toegangs handtekening met behulp van de opdracht Azure CLI [AZ Storage account generate-SAS](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) . Voor deze bewerking zijn de `storage`-en `setsas` machtigingen vereist.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Nadat de bewerking is uitgevoerd, kopieert u de uitvoer.

```console
"se=2020-01-01&sp=***"
```

Deze uitvoer wordt in de volgende stap door gegeven aan de para meter `--template-id`.

### <a name="generate-a-shared-access-signature-definition"></a>Een definitie van een gedeelde Access-hand tekening genereren

Gebruik de Azure CLI [AZ Storage SAS-definition-opslag](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) opdracht, waarbij de uitvoer van de vorige stap wordt door gegeven aan de para meter `--template-id` om een definitie van een Shared Access-hand tekening te maken.  U kunt de naam van uw keuze opgeven voor de para meter `-n`.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>De definitie van de hand tekening voor gedeelde toegang controleren

U kunt controleren of de definitie van de hand tekening voor gedeelde toegang is opgeslagen in uw sleutel kluis met behulp van de Azure CLI [AZ-geheime lijst](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) en [AZ sleutel kluis Secret opdrachten weer geven](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) .

Zoek eerst de definitie van de hand tekening voor gedeelde toegang in uw sleutel kluis met behulp van de opdracht [AZ Key kluis Secret List](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) .

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Het geheim dat overeenkomt met uw SAS-definitie heeft de volgende eigenschappen:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

U kunt nu de opdracht [AZ sleutel kluis Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) en de eigenschap `id` gebruiken om de inhoud van dat geheim weer te geven.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

De uitvoer van deze opdracht geeft uw SAS-definitie reeks weer als`value`.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [sleutels, geheimen en certificaten](https://docs.microsoft.com/rest/api/keyvault/).
- Bekijk artikelen over de [blog](https://blogs.technet.microsoft.com/kv/)van het Azure Key Vault-team.
- Zie de referentie documentatie [AZ-kluis opslag](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .

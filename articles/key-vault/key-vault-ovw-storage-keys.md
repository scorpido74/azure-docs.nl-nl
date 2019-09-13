---
title: Sleutels voor opslag accounts beheren met Azure Key Vault en de Azure CLI
description: Sleutels voor opslag accounts bieden een naadloze integratie tussen Azure Key Vault en toegang tot de sleutel op basis van sleutels tot een Azure-opslag account.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/01/2019
ms.openlocfilehash: 86ac58aea174ca05580da99b6bde0cc3823ab151
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883062"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Sleutels voor opslag accounts beheren met Azure Key Vault en de Azure CLI 

Azure Key Vault beheert sleutels voor Azure Storage-accounts en klassieke opslag accounts. U kunt de functie beheerde opslag accounts van Key Vault gebruiken om verschillende functies voor sleutel beheer voor u uit te voeren.

Een [Azure-opslag account](/azure/storage/storage-create-storage-account) gebruikt een referentie die bestaat uit een account naam en een sleutel. De sleutel wordt automatisch gegenereerd en fungeert als een wacht woord in plaats van een als cryptografische sleutel. Key Vault beheert Storage-account sleutels door ze op te slaan als [Key Vault geheimen](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Sleutels worden weer gegeven (gesynchroniseerd) met een Azure-opslag account en worden periodiek opnieuw gegenereerd of _gedraaid_. 

Houd rekening met de volgende punten wanneer u de sleutel functie beheerde opslag account gebruikt:

- Sleutel waarden worden nooit geretourneerd als antwoord op een aanroeper.
- Alleen Key Vault moet de sleutels van uw opslag account beheren. Beheer de sleutels niet zelf en voorkom conflicten met Key Vault processen.
- Alleen een enkel Key Vault-object moet de sleutels van het opslag account beheren. Geen sleutel beheer van meerdere objecten toestaan.
- U kunt Key Vault aanvragen om uw opslag account te beheren met een gebruikers-principal, maar niet met een service-principal.
- Sleutels opnieuw genereren met behulp van alleen Key Vault. De sleutels van uw opslag account niet hand matig opnieuw genereren. 

> [!NOTE]
> Azure Storage integratie met Azure Active Directory (Azure AD) is de cloud-gebaseerde identiteits-en toegangs beheer service van micro soft.
> Azure AD-integratie is beschikbaar voor [Azure-blobs en-wacht rijen](../storage/common/storage-auth-aad.md).
> Gebruik Azure AD voor verificatie en autorisatie.
> Azure AD biedt op OAuth2-tokens gebaseerde toegang tot Azure Storage net als Azure Key Vault.
>
> Met Azure AD kunt u uw client toepassing verifiëren met behulp van een toepassings-of gebruikers-id in plaats van de referenties van het opslag account.
> U kunt een door [Azure AD beheerde identiteit](/azure/active-directory/managed-identities-azure-resources/) gebruiken wanneer u in azure uitvoert. Beheerde identiteiten verwijderen de nood zaak voor client verificatie en het opslaan van referenties in of met uw toepassing.
> Azure AD gebruikt op rollen gebaseerd toegangs beheer (RBAC) voor het beheren van autorisatie, die ook door Key Vault wordt ondersteund.

### <a name="service-principal-application-id"></a>Service-Principal-toepassings-ID

Een Azure AD-Tenant voorziet elke geregistreerde toepassing van een [Service-Principal](/azure/active-directory/develop/developer-glossary#service-principal-object). De Service-Principal fungeert als de toepassings-ID. De toepassings-ID wordt gebruikt tijdens het instellen van de autorisatie voor toegang tot andere Azure-resources via RBAC.

Key Vault is een micro soft-toepassing die vooraf is geregistreerd in alle Azure AD-tenants. De Key Vault is geregistreerd onder dezelfde toepassings-ID en binnen elke Azure-Cloud.

| tenants | Cloud | Toepassings-id |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure openbaar | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Overige  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Vereisten

Voordat u Key Vault gebruikt voor het beheren van de sleutel van uw opslag account, raadpleegt u de vereisten:

- Installeer de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Maak een [Azure-opslag account](https://azure.microsoft.com/services/storage/). Volg [deze stappen](../storage/index.yml).
- De naam van het opslag account mag alleen kleine letters en cijfers bevatten. De naam moet tussen de 3 en 24 tekens lang zijn.        
      
## <a name="manage-storage-account-keys"></a>Sleutels voor opslag accounts beheren

Er zijn vier basis stappen voor het gebruik van Key Vault voor het beheren van sleutels voor opslag accounts:

1. Een bestaand opslag account ophalen.
1. Een bestaande sleutel kluis ophalen.
1. Voeg een Key Vault beheerd opslag account toe aan de kluis. Stel `key1` in als de actieve sleutel met een regeneratieperiode van 90 dagen.
1. Gebruiken `key1` om een opslag context in te stellen voor het opgegeven opslag account.

> [!NOTE]
> Aan Key Vault as a-service worden operator machtigingen toegewezen voor uw opslag account.
> 
> Nadat u Azure Key Vault Managed Storage-account sleutels hebt ingesteld, wijzigt u de sleutels alleen met behulp van Key Vault.
> Key Vault beheert de draaiing van de sleutel van het opslag account voor de sleutels van beheerde opslag accounts.

1. Nadat u een opslag account hebt gemaakt, voert u de volgende opdracht uit om de resource-ID op te halen van het opslag account dat u wilt beheren:
    ```
    az storage account show -n storageaccountname
    ```

    Kopieer de waarde van de resource-ID uit de opdracht uitvoer:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Voorbeelduitvoer:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Wijs de RBAC-rol van de operator voor de sleutel van het opslag account toe aan Key Vault. Deze rol beperkt de toegangs Scope tot uw opslag account. Gebruik voor een klassiek opslag account de rol ' klassieke account sleutel operator rol '.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`is de object-ID voor Key Vault in de open bare Azure-Cloud. Zie [Service Principal Application id](#service-principal-application-id)om de object-id voor Key Vault op te halen in de Azure Government Cloud.
    
1. Een door Key Vault beheerd opslag account maken:

    Stel een regeneratie periode van 90 dagen in. Na 90 dagen Key Vault de actieve sleutel opnieuw `key1` gegenereerd en gewisseld van `key2` naar `key1`. `key1`wordt vervolgens als de actieve sleutel gemarkeerd. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Tokens maken en genereren

U kunt ook Key Vault vragen om de tokens voor Shared Access-hand tekeningen te genereren. Een shared access signature biedt gedelegeerde toegang tot resources in uw opslagaccount. U kunt clients toegang verlenen tot resources in uw opslag account zonder uw account sleutels te delen. Een Shared Access Signature biedt u een veilige manier om uw opslag resources te delen zonder in te boeten voor uw account sleutels.

De opdrachten in deze sectie voeren de volgende acties uit:

- Stel de definitie `<YourSASDefinitionName>`van een gedeelde toegangs handtekening voor een account in. De definitie wordt ingesteld op een Key Vault beheerd opslag account `<YourStorageAccountName>` in uw sleutel kluis `<VaultName>`.
- Maak een account voor Shared Access Signature-token voor blob-, bestands-, tabel-en wachtrij Services. Het token is gemaakt voor resource type-service,-container en-object. Het token wordt gemaakt met alle machtigingen, via https en met de opgegeven begin-en eind datum.
- Stel in de kluis een definitie in van een Key Vault Managed Storage-hand tekening voor gedeelde opslag. De definitie heeft de sjabloon-URI van het Shared Access Signature-token dat is gemaakt. De definitie heeft het type `account` Shared Access Signature en is N dagen geldig.
- Haal het daad werkelijke toegangs token op uit het Key Vault geheim dat overeenkomt met de definitie van de Shared Access-hand tekening.

Nadat u de stappen in de vorige sectie hebt voltooid, voert u de volgende opdrachten uit om Key Vault te vragen voor het genereren van tokens voor Shared Access-hand tekeningen. 

1. Maak een definitie van een Shared Access-hand tekening. Nadat de definitie van de hand tekening voor gedeelde toegang is gemaakt, vraagt u Key Vault om meer tokens voor Shared Access-hand tekeningen te genereren. Voor deze bewerking zijn `storage` de `setsas` machtigingen en vereist.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Zie de documentatie [AZ Storage account generate-SAS Reference (](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Engelstalig) voor meer informatie over de werking.

    Nadat de bewerking is uitgevoerd, kopieert u de uitvoer.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Gebruik de `$sasToken` door de vorige opdracht gegenereerde en maak een definitie van een Shared Access-hand tekening. Voor meer informatie over de opdracht parameters, zie de naslag documentatie [AZ-opslag SAS-definitie referentie maken](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) .
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Als de gebruiker geen machtigingen heeft voor het opslag account, moet u eerst de object-ID van de gebruiker ophalen:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Tokens ophalen in code

Voer bewerkingen uit voor uw opslag account door de [Signature-tokens voor gedeelde toegang](../storage/common/storage-dotnet-shared-access-signature-part-1.md) op te halen uit Key Vault.

Er zijn drie manieren om te verifiëren bij Key Vault:

- Een beheerde service-identiteit gebruiken. Deze methode wordt sterk aanbevolen.
- Een Service-Principal en een certificaat gebruiken. 
- Een Service-Principal en wacht woord gebruiken. Deze methode wordt niet aanbevolen.

Zie [Azure Key Vault voor meer informatie: Basis concepten](key-vault-whatis.md#basic-concepts).

In het volgende voor beeld ziet u hoe u de tokens voor Shared Access-hand tekeningen ophaalt. U haalt de tokens op nadat u een definitie van een gedeelde toegangs handtekening hebt gemaakt. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Als uw Shared Access Signature-token bijna is verlopen, haalt u het token voor de Shared Access-hand tekening opnieuw op uit Key Vault en werkt u de code bij.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI-opdrachten

Voor informatie over de Azure CLI-opdrachten die relevant zijn voor beheerde opslag accounts, raadpleegt u de documentatie van de [opslag referentie AZ-kluis](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [sleutels, geheimen en certificaten](https://docs.microsoft.com/rest/api/keyvault/).
- Bekijk artikelen over de [blog](https://blogs.technet.microsoft.com/kv/)van het Azure Key Vault-team.

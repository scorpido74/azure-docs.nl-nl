---
title: Opslagaccountsleutels beheren met Azure Key Vault en de Azure CLI
description: Opslagaccountsleutels bieden naadloze integratie tussen Azure Key Vault en toegang tot een Azure-opslagaccount op basis van sleutels.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 1125bafa43ce1752c58d1cce0bba66a6bbd32c32
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685421"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Opslagaccountsleutels beheren met Key Vault en de Azure CLI

Een Azure-opslagaccount gebruikt referenties met een accountnaam en een sleutel. De sleutel wordt automatisch gegenereerd en dient als een wachtwoord, in plaats van een als een cryptografische sleutel. Key Vault beheert opslagaccountsleutels door ze op te slaan als Key Vault-geheimen. 

U de functie Key Vault managed storage account key gebruiken om sleutels met een Azure-opslagaccount te vermelden (synchroniseren) en de sleutels periodiek te regenereren (roteren). U sleutels beheren voor zowel opslagaccounts als Klassieke opslagaccounts.

Wanneer u de sleutelfunctie voor beheerde opslagaccount gebruikt, moet u rekening houden met de volgende punten:

- Belangrijke waarden worden nooit geretourneerd als reactie op een beller.
- Alleen Key Vault moet uw opslagaccountsleutels beheren. Beheer de sleutels niet zelf en voorkom dat u de Key Vault-processen verstoort.
- Slechts één Key Vault-object mag opslagaccountsleutels beheren. Sta geen sleutelbeheer van meerdere objecten toe.
- U Key Vault aanvragen om uw opslagaccount te beheren met een gebruikersprincipal, maar niet met een serviceprincipal.
- Regenereren van sleutels door alleen Key Vault te gebruiken. Genereer uw opslagaccountsleutels niet handmatig.

We raden u aan azure storage-integratie te gebruiken met Azure Active Directory (Azure AD), de cloudgebaseerde cloud- en toegangsbeheerservice van Microsoft. Azure AD-integratie is beschikbaar voor [Azure-blobs en wachtrijen](../../storage/common/storage-auth-aad.md)en biedt Op OAuth2-token gebaseerde toegang tot Azure Storage (net als Azure Key Vault).

Met Azure AD u uw clienttoepassing verifiëren met behulp van een toepassing of gebruikersidentiteit, in plaats van opslagaccountreferenties. U een [door Azure AD beheerde identiteit](/azure/active-directory/managed-identities-azure-resources/) gebruiken wanneer u op Azure wordt uitgevoerd. Beheerde identiteiten verwijderen de noodzaak voor clientverificatie en het opslaan van referenties in of met uw toepassing.

Azure AD gebruikt rbac (role-based access control) om autorisatie te beheren, die ook wordt ondersteund door Key Vault.

## <a name="service-principal-application-id"></a>Servicehoofdtoepassings-id

Een Azure AD-tenant biedt elke geregistreerde toepassing een [serviceprincipal](/azure/active-directory/develop/developer-glossary#service-principal-object). De serviceprincipal fungeert als de toepassings-id, die wordt gebruikt tijdens autorisatie-instellingen voor toegang tot andere Azure-bronnen via RBAC.

Key Vault is een Microsoft-toepassing die vooraf is geregistreerd in alle Azure AD-tenants. Key Vault wordt geregistreerd onder dezelfde toepassings-id in elke Azure-cloud.

| Tenants | Cloud | Toepassings-id |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Openbare Azure-peering | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Overige  | Alle | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Vereisten

Als u deze handleiding wilt voltooien, moet u eerst het volgende doen:

- [Installeer de Azure CLI](/cli/azure/install-azure-cli).
- [Een sleutelkluis maken](quick-create-cli.md)
- [Maak een Azure-opslagaccount](../../storage/common/storage-account-create.md?tabs=azure-cli). De naam van het opslagaccount mag alleen kleine letters en cijfers gebruiken. De lengte van de naam moet tussen de 3 en 24 tekens liggen.
      
## <a name="manage-storage-account-keys"></a>Opslagaccountsleutels beheren

### <a name="connect-to-your-azure-account"></a>Verbinding maken met uw Azure-account

Verifieer uw Azure CLI-sessie met de [az-aanmeldingsopdrachten.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault toegang geven tot uw opslagaccount

Gebruik de opdracht Voor het maken van azure CLI [az-roltoewijzing](/cli/azure/role/assignment?view=azure-cli-latest) om Key Vault toegang te geven tot uw opslagaccount. Geef de opdracht de volgende parameterwaarden op:

- `--role`: Geef de RBAC-rol 'Storage Account Key Operator Service' door. Deze rol beperkt het toegangsbereik tot uw opslagaccount. Voor een klassiek opslagaccount geeft u in plaats daarvan de functie 'Classic Storage Account Key Operator Service Role' door.
- `--assignee`: Geef dehttps://vault.azure.netwaarde " ", dat is de url voor Key Vault in de Azure public cloud. (Zie [Service principal application ID](#service-principal-application-id)voor Azure Goverment cloud gebruik in plaats daarvan '--asingee-object-id'.
- `--scope`: Geef uw opslagaccountbron-id door, die in het formulier `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`staat. Als u uw abonnements-id wilt vinden, gebruikt u de opdracht Azure CLI [az-accountlijst.](/cli/azure/account?view=azure-cli-latest#az-account-list) Als u de naam en opslagaccountbrongroep van uw opslagaccount wilt vinden, gebruikt u de opdracht Azure CLI [az-opslagaccountlijst.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Geef uw gebruikersaccount toestemming aan beheerde opslagaccounts

Gebruik de cmdlet azure CLI [az keyvault-set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) om het key vault-toegangsbeleid bij te werken en machtigingen voor opslagaccounts toe te kennen aan uw gebruikersaccount.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Houd er rekening mee dat machtigingen voor opslagaccounts niet beschikbaar zijn op de pagina 'Toegangsbeleid' voor opslagaccounts in de Azure-portal.
### <a name="create-a-key-vault-managed-storage-account"></a>Een Key Vault Managed Storage-account maken

 Maak een Key Vault managed storage-account met de opdracht Azure CLI [az keyvault storage.](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) Stel een regeneratieperiode van 90 dagen in. Na 90 dagen regenereert `key1` Key Vault en `key2` wisselt `key1`de actieve sleutel van . `key1`wordt dan gemarkeerd als de actieve sleutel. Geef de opdracht de volgende parameterwaarden op:

- `--vault-name`: Geef de naam van uw sleutelkluis door. Als u de naam van uw sleutelkluis wilt vinden, gebruikt u de opdracht Azure CLI [az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list)
- `-n`: Geef de naam van uw opslagaccount door. Als u de naam van uw opslagaccount wilt vinden, gebruikt u de opdracht Azure CLI [az-opslagaccount.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
- `--resource-id`: Geef uw opslagaccountbron-id door, die in het formulier `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`staat. Als u uw abonnements-id wilt vinden, gebruikt u de opdracht Azure CLI [az-accountlijst.](/cli/azure/account?view=azure-cli-latest#az-account-list) Als u de naam en opslagaccountbrongroep van uw opslagaccount wilt vinden, gebruikt u de opdracht Azure CLI [az-opslagaccountlijst.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokens voor gedeelde toeganghandtekeningen

Je Key Vault ook vragen om tokens voor gedeelde toegangshandtekeningen te genereren. Een handtekening voor gedeelde toegang biedt gedelegeerde toegang tot bronnen in uw opslagaccount. U klanten toegang verlenen tot bronnen in uw opslagaccount zonder uw accountsleutels te delen. Een handtekening voor gedeelde toegang biedt u een veilige manier om uw opslagbronnen te delen zonder uw accountsleutels in gevaar te brengen.

De opdrachten in deze sectie voltooien de volgende acties:

- Een account definitie van `<YourSASDefinitionName>`gedeelde toegang instellen . De definitie is ingesteld op een `<YourStorageAccountName>` Key Vault `<YourKeyVaultName>`managed storage-account in uw sleutelkluis.
- Maak een handtekeningtoken voor gedeelde accountbeheer voor blob-, bestands-, tabel- en wachtrijservices. Het token wordt gemaakt voor resourcetypen Service, Container en Object. Het token wordt gemaakt met alle machtigingen, via https en met de opgegeven begin- en einddatums.
- Stel een key vault managed storage shared access signature definition in de kluis in. De definitie heeft de sjabloon URI van het token voor gedeelde toegangshandtekeningen dat is gemaakt. De definitie heeft het `account` type handtekening van gedeelde toegang en is geldig voor N-dagen.
- Controleer of de handtekening van gedeelde toegang als geheim is opgeslagen in uw sleutelkluis.

### <a name="create-a-shared-access-signature-token"></a>Een handtekeningtoken voor gedeelde toegang maken

Maak een definitie van gedeelde toegangshandtekeningen met de opdracht Azure CLI [az-opslagaccount generate-sas.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Deze bewerking `storage` vereist `setsas` de machtigingen en machtigingen.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Nadat de bewerking is uitgevoerd, kopieert u de uitvoer.

```console
"se=2020-01-01&sp=***"
```

Deze uitvoer wordt in `--template-id` de volgende stap doorgegeven aan de parameter.

### <a name="generate-a-shared-access-signature-definition"></a>Een definitie van een handtekening voor gedeelde toegang genereren

Gebruik de opdracht Azure CLI [az keyvault storage sas-definition](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) create, `--template-id` door de uitvoer van de vorige stap door te geven aan de parameter, om een definitie van gedeelde toegangshandtekening te maken.  U de naam van `-n` uw keuze aan de parameter opgeven.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>De definitie van de handtekening van gedeelde toegang verifiëren

U controleren of de definitie van de handtekening van gedeelde toegang is opgeslagen in uw sleutelkluis met behulp van de azure CLI [az keyvault-geheime lijst](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) en [geheime showopdrachten voor AZ KeyVault.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

Zoek eerst de definitie van gedeelde toegangshandtekeningen in uw sleutelkluis met de opdracht [geheime lijst az keyvault.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Het geheim dat overeenkomt met uw SAS-definitie heeft de volgende eigenschappen:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

U nu gebruik maken van de [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) commando en de `id` eigenschap om de inhoud van dat geheim te bekijken.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

De uitvoer van deze opdracht toont uw`value`SAS-definitietekenreeks als .


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [sleutels, geheimen en certificaten](https://docs.microsoft.com/rest/api/keyvault/).
- Bekijk artikelen op de blog van azure [key vault-team](https://blogs.technet.microsoft.com/kv/).
- Bekijk de referentiedocumentatie [voor az keyvault-opslag.](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

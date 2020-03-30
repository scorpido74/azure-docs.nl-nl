---
title: Door de klant beheerde sleutels configureren voor uw Azure Cosmos DB-account
description: Meer informatie over het configureren van door klanten beheerde sleutels voor uw Azure Cosmos DB-account met Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 6e2a90b8f81b9b945905ee98beb1686c54a62e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063770"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Door de klant beheerde sleutels configureren voor uw Azure Cosmos-account met Azure Key Vault

> [!NOTE]
> Op dit moment moet u toegang vragen om deze mogelijkheid te gebruiken. Neem hiervoor contact [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)op met .

Gegevens die zijn opgeslagen in uw Azure Cosmos-account, worden automatisch en naadloos versleuteld met sleutels die door Microsoft worden beheerd **(servicebeheerde sleutels).** Optioneel u ervoor kiezen om een tweede laag versleuteling toe te voegen met sleutels die u beheert **(door de klant beheerde sleutels).**

![Versleutelingslagen rond klantgegevens](./media/how-to-setup-cmk/cmk-intro.png)

U moet door de klant beheerde sleutels opslaan in [Azure Key Vault](../key-vault/key-vault-overview.md) en een sleutel opgeven voor elk Azure Cosmos-account dat is ingeschakeld met door de klant beheerde sleutels. Deze sleutel wordt gebruikt om alle gegevens die in dat account zijn opgeslagen, te versleutelen.

> [!NOTE]
> Momenteel zijn door de klant beheerde sleutels alleen beschikbaar voor nieuwe Azure Cosmos-accounts. U moet ze configureren tijdens het maken van een account.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>De Azure Cosmos DB-bronprovider registreren voor uw Azure-abonnement

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com/)ga naar uw Azure-abonnement en selecteer **Resourceproviders** onder het tabblad **Instellingen:**

   ![Vermelding van resourceproviders in het linkermenu](./media/how-to-setup-cmk/portal-rp.png)

1. Zoek naar de **Microsoft.DocumentDB-bronprovider.** Controleer of de resourceprovider al is gemarkeerd als geregistreerd. Zo niet, kies dan de resourceprovider en selecteer **Register:**

   ![De Microsoft.DocumentDB-bronprovider registreren](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Uw Azure Key Vault-exemplaar configureren

Als u door de klant beheerde sleutels gebruikt met Azure Cosmos DB, moet u twee eigenschappen instellen op het Azure Key Vault-exemplaar dat u van plan bent te gebruiken om uw versleutelingssleutels te hosten. Deze eigenschappen omvatten **Soft Delete** en Do **Not Purge**. Deze eigenschappen zijn standaard niet ingeschakeld. U ze inschakelen met PowerShell of Azure CLI.

Zie de secties 'Soft-delete' en 'Zuiveringsbeveiliging inschakelen' in een van de volgende artikelen voor meer informatie over het inschakelen van deze eigenschappen op een bestaand Azure Key Vault-exemplaar:

- [Soft-delete gebruiken met PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Soft-delete gebruiken met Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Een toegangsbeleid toevoegen aan uw Azure Key Vault-exemplaar

1. Ga vanuit de Azure-portal naar het Azure Key Vault-exemplaar dat u van plan bent te gebruiken om uw versleutelingssleutels te hosten. Selecteer **Toegangsbeleid** in het linkermenu:

   ![Toegangsbeleid in het linkermenu](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Selecteer **+ Toegangsbeleid toevoegen**.

1. Selecteer onder de vervolgkeuzelijst **Sleutelmachtigingen** de optie **Sleutel** **uitpakken**en **Sleutel inpakken:**

   ![De juiste machtigingen selecteren](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Selecteer **Onder Hoofd selecteren**de optie Geen **geselecteerd**. Zoek vervolgens naar **Azure Cosmos DB-principal** en selecteer deze (om het gemakkelijker `a232010e-820c-4083-83bb-3ace5fc29d0b` te vinden, u ook zoeken `57506a73-e302-42a9-b869-6f12d9ec29e9`op hoofd-id: voor elke Azure-regio behalve Azure Government-regio's waar de hoofd-id is). Kies Tot slot **Selecteer** onderin. Als de **Azure Cosmos DB-principal** niet in de lijst staat, moet u mogelijk de **Microsoft.DocumentDB-bronprovider** opnieuw registreren zoals beschreven in het gedeelte [De resourceprovider registreren](#register-resource-provider) van dit artikel.

   ![Selecteer de Azure Cosmos DB-principal](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Selecteer **Toevoegen** om het nieuwe toegangsbeleid toe te voegen.

## <a name="generate-a-key-in-azure-key-vault"></a>Een sleutel genereren in Azure Key Vault

1. Ga vanuit de Azure-portal naar het Azure Key Vault-exemplaar dat u van plan bent te gebruiken om uw versleutelingssleutels te hosten. Selecteer vervolgens **Toetsen** in het linkermenu:

   ![Vermelding "Sleutels" in het linkermenu](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Selecteer **Genereren/importeren,** geef een naam op voor de nieuwe sleutel en selecteer een RSA-sleutelgrootte. Een minimum van 3072 wordt aanbevolen voor de beste beveiliging. Selecteer vervolgens **Maken:**

   ![Een nieuwe sleutel maken](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Nadat de sleutel is gemaakt, selecteert u de nieuw gemaakte sleutel en vervolgens de huidige versie.

1. Kopieer de **sleutel-id**van de sleutel, behalve het onderdeel na de laatste slash naar voren:

   ![De sleutel-id van de sleutel kopiëren](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Een nieuw Azure Cosmos-account maken

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Wanneer u een nieuw Azure Cosmos DB-account maakt vanuit de Azure-portal, kiest u **Klantbeheerde sleutel** in de **stap Versleuteling.** Plak in het veld **Sleutel URI** de URI/key-id van de Azure Key Vault-sleutel die u uit de vorige stap hebt gekopieerd:

![CMK-parameters instellen in de Azure-portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

Wanneer u een nieuw Azure Cosmos DB-account maakt met PowerShell:

- Geef de URI van de Azure Key Vault-sleutel door die eerder is gekopieerd onder de eigenschap **keyVaultKeyUri** in **PropertyObject**.

- Gebruik **2019-12-12** als API-versie.

> [!IMPORTANT]
> U moet `Location` de parameter expliciet instellen voor het account dat met door de klant beheerde sleutels wordt gemaakt.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon gebruiken

Wanneer u een nieuw Azure Cosmos-account maakt via een Azure Resource Manager-sjabloon:

- Geef de URI van de Azure Key Vault-sleutel door die u eerder hebt gekopieerd onder de eigenschap **keyVaultKeyUri** in het **eigenschappenobject.**

- Gebruik **2019-12-12** als API-versie.

> [!IMPORTANT]
> U moet `Location` de parameter expliciet instellen voor het account dat met door de klant beheerde sleutels wordt gemaakt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

Implementeer de sjabloon met het volgende PowerShell-script:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a>Azure CLI gebruiken

Wanneer u een nieuw Azure Cosmos-account maakt via de Azure CLI, geeft u de URI van de Azure Key Vault-sleutel door die u eerder hebt gekopieerd onder de parameter **--key-uri.**

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Zijn er extra kosten verbonden aan het gebruik van door de klant beheerde sleutels?

Ja. Als u rekening wilt houden met de extra rekenbelasting die nodig is om gegevensversleuteling en -decryptie met door de klant beheerde sleutels te beheren, verbruiken alle bewerkingen die zijn uitgevoerd met het Azure Cosmos-account een toename van 25 procent in [aanvraageenheden.](./request-units.md)

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Welke gegevens worden versleuteld met de door de klant beheerde sleutels?

Alle gegevens die zijn opgeslagen in uw Azure Cosmos-account worden versleuteld met de door de klant beheerde sleutels, met uitzondering van de volgende metagegevens:

- De namen van uw Azure Cosmos [DB-accounts, databases en containers](./account-overview.md#elements-in-an-azure-cosmos-account)

- De namen van uw [opgeslagen procedures](./stored-procedures-triggers-udfs.md)

- De eigenschappenpaden die zijn aangegeven in uw [indexeringsbeleid](./index-policy.md)

- De waarden van de [partitiesleutels](./partitioning-overview.md) van uw containers

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Worden door de klant beheerde sleutels ondersteund voor bestaande Azure Cosmos-accounts?

Deze functie is momenteel alleen beschikbaar voor nieuwe accounts.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Is er een plan om fijnere granulariteit te ondersteunen dan sleutels op accountniveau?

Momenteel niet, maar sleutels op containerniveau worden overwogen.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Welke invloed hebben door de klant beheerde sleutels op een back-up?

Azure Cosmos DB maakt [regelmatig en automatisch back-ups](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) van de gegevens die in uw account zijn opgeslagen. Met deze bewerking wordt een back-up van de versleutelde gegevens. Om de herstelde back-up te gebruiken, is de versleutelingssleutel vereist die u hebt gebruikt op het moment van de back-up. Dit betekent dat er geen intrekking is gemaakt en dat de versie van de sleutel die werd gebruikt op het moment van de back-up nog steeds is ingeschakeld.

### <a name="how-do-i-revoke-an-encryption-key"></a>Hoe trek ik een encryptiesleutel in?

Sleutelintrekking wordt gedaan door de nieuwste versie van de sleutel uit te schakelen:

![De versie van een sleutel uitschakelen](./media/how-to-setup-cmk/portal-akv-rev2.png)

Als u alle sleutels uit een Azure Key Vault-exemplaar wilt intrekken, u ook het toegangsbeleid verwijderen dat is verleend aan de Azure Cosmos DB-principal:

![Het toegangsbeleid voor de Azure Cosmos DB-principal verwijderen](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Welke bewerkingen zijn beschikbaar nadat een door de klant beheerde sleutel is ingetrokken?

De enige bewerking mogelijk wanneer de encryptiesleutel is ingetrokken, is het verwijderen van uw account.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gegevensversleuteling in Azure Cosmos DB](./database-encryption-at-rest.md).
- Krijg een overzicht van [veilige toegang tot gegevens in Cosmos DB.](secure-access-to-data.md)

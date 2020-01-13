---
title: Door de klant beheerde sleutels voor uw Azure Cosmos DB account configureren
description: Meer informatie over het configureren van door de klant beheerde sleutels voor uw Azure Cosmos DB-account
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/11/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 964c3e4e2de43e6bcae353f0b525eb62e6613361
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911861"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>Door de klant beheerde sleutels voor uw Azure Cosmos DB account configureren

> [!NOTE]
> Op dit moment moet u toegang aanvragen om deze mogelijkheid te gebruiken. Als u dit wilt doen, neemt u contact op met [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

Gegevens die zijn opgeslagen in uw Azure Cosmos DB account, worden automatisch en naadloos versleuteld. Azure Cosmos DB biedt twee opties voor het beheren van de sleutels die worden gebruikt voor het versleutelen van uw gegevens in rust:
- Door **service beheerde sleutels**. Standaard beheert micro soft de sleutels die worden gebruikt voor het versleutelen van uw Azure Cosmos DB-account.
- Door de **klant beheerde sleutels (CMK)** . U kunt ervoor kiezen om een tweede laag versleuteling toe te voegen met sleutels die u beheert.

Door de klant beheerde sleutels moeten worden opgeslagen in [Azure Key Vault](../key-vault/key-vault-overview.md). Er moet een sleutel worden gegeven voor elk CMK-account dat wordt gebruikt voor het versleutelen van alle gegevens die in dat account zijn opgeslagen.

## <a name="setup"></a>Instellen

Op dit moment zijn door de klant beheerde sleutels alleen beschikbaar voor nieuwe accounts en moeten ze worden ingesteld tijdens het maken van het account.

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. Controleer of de Azure Cosmos DB resource provider is geregistreerd voor uw Azure-abonnement

Ga vanuit het Azure Portal naar uw Azure-abonnement en selecteer **resource providers** in het menu links:

![De vermelding resource providers in het menu links](./media/how-to-setup-cmk/portal-rp.png)

Zoek naar de resource provider **micro soft. DocumentDB** .
- Als de resource provider al is gemarkeerd als geregistreerd, hoeft niets te worden gedaan.
- Als dat niet het geval is, selecteert u deze en klikt u op **registreren**:

    ![De resource provider micro soft. DocumentDB registreren](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. uw Azure Key Vault-exemplaar configureren

Het gebruik van door de klant beheerde sleutels met Azure Cosmos DB vereist twee eigenschappen die moeten worden ingesteld op het Azure Key Vault exemplaar dat u wilt gebruiken voor het hosten van de versleutelings sleutels: **zacht verwijderen** en **niet wissen**. Deze eigenschappen zijn niet standaard ingeschakeld, maar kunnen worden ingeschakeld met Power shell of de Azure CLI.

Voor meer informatie over het inschakelen van deze eigenschappen voor een bestaande Azure Key Vault-exemplaar, zie de secties met de titel Enable Soft voorlopig verwijderen en leegmaken van beveiliging inschakelen in een van de volgende artikelen:
- [Voorlopig verwijderen gebruiken met Power shell](../key-vault/key-vault-soft-delete-powershell.md)
- [Voorlopig verwijderen gebruiken met Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. een toegangs beleid toevoegen aan uw Azure Key Vault-exemplaar

Ga vanuit het Azure Portal naar het Azure Key Vault exemplaar dat u wilt gebruiken voor het hosten van de versleutelings sleutels. Selecteer vervolgens **toegangs beleid** in het menu links:

![' Toegangs beleid ' in het menu links](./media/how-to-setup-cmk/portal-akv-ap.png)

- Selecteer **+ toegangs beleid toevoegen**
- Selecteer in het vervolg keuzemenu **sleutel machtigingen** de optie **Get**, **dewrap toets** en **wrap**:

    ![De juiste machtigingen selecteren](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- Selecteer onder **Principal selecteren**de optie **geen geselecteerd**. Zoek en selecteer vervolgens de **Azure Cosmos DB** principal. Klik ten slotte op **selecteren** onderaan (als de principal van de **Azure Cosmos DB** niet kan worden gevonden, moet u de resource provider **micro soft. DocumentDB** mogelijk opnieuw registreren bij stap 1):

    ![De Azure Cosmos DB-Principal selecteren](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- Selecteer **toevoegen** om het nieuwe toegangs beleid toe te voegen

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. een sleutel in Azure Key Vault genereren

Ga vanuit het Azure Portal naar de Azure Key Vault instantie die u wilt gebruiken voor het hosten van de versleutelings sleutels. Selecteer vervolgens **sleutels** in het menu links:

![Vermelding ' Keys ' in het menu links](./media/how-to-setup-cmk/portal-akv-keys.png)

- Selecteer **genereren/importeren**
- Geef een naam op voor de nieuwe sleutel, selecteer een RSA-sleutel grootte (mini maal 3072 wordt aanbevolen voor de beste beveiliging) en selecteer **maken**:

    ![Een nieuwe sleutel maken](./media/how-to-setup-cmk/portal-akv-gen.png)

- Zodra de sleutel is gemaakt, klikt u op de zojuist gemaakte sleutel en vervolgens op de huidige versie
- Kopieer de sleutel- **id** van de sleutel, behalve het deel na de laatste slash:

    ![De sleutel-id van de sleutel kopiëren](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. Maak een nieuw Azure Cosmos DB-account

#### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Wanneer u een nieuw Azure Cosmos DB-account maakt vanuit de Azure Portal, kiest u door de **klant beheerde sleutel** bij de **versleutelings** stap. Geef in het veld **sleutel-URI** de URI van de Azure Key Vault sleutel die is gekopieerd uit stap 4:

![CMK-para meters instellen in de Azure Portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>PowerShell gebruiken

Wanneer u een nieuw Azure Cosmos DB-account maakt met Power shell,
- Geef de URI van de Azure Key Vault sleutel die is gekopieerd uit stap 4 onder de eigenschap **keyVaultKeyUri** in de **PropertyObject**,
- Zorg ervoor dat u "2019-12-12" als API-versie gebruikt.

> [!IMPORTANT]
> De para meter `Location` moet expliciet worden ingesteld voor het account dat is gemaakt met CMK.

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

#### <a name="using-azure-resource-manager-templates"></a>Azure Resource Manager sjablonen gebruiken

Bij het maken van een nieuw Azure Cosmos DB-account via een Azure Resource Manager sjabloon:
- Geef de URI van de Azure Key Vault sleutel die is gekopieerd uit stap 4 onder de eigenschap **keyVaultKeyUri** in het object **Properties**
- Zorg ervoor dat u "2019-12-12" als API-versie gebruikt

> [!IMPORTANT]
> De para meter `location` moet expliciet worden ingesteld voor het account dat is gemaakt met CMK.

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

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>Zijn er extra kosten verbonden aan het gebruik van door de klant beheerde sleutels?

Ja. Voor de extra reken belasting die is vereist voor het beheren van gegevens versleuteling en ontsleuteling met door de klant beheerde sleutels, krijgen alle bewerkingen die worden uitgevoerd op het Azure Cosmos DB account, een toename van 25% in de verbruikte [aanvraag eenheden](./request-units.md) .

### <a name="what-data-gets-encrypted-with-the-cmk"></a>Welke gegevens worden versleuteld met de CMK?

Alle gegevens die zijn opgeslagen in uw Azure Cosmos DB account, worden versleuteld met de CMK, met uitzonde ring van de volgende meta gegevens:
- de namen van uw Azure Cosmos DB [accounts, data bases en containers](./account-overview.md#elements-in-an-azure-cosmos-account)
- de namen van uw [opgeslagen procedures](./stored-procedures-triggers-udfs.md),
- de eigenschaps paden die zijn gedeclareerd in uw [indexerings beleid](./index-policy.md),
- de waarden van de [partitie sleutel](./partitioning-overview.md)van uw containers.

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>Worden door de klant beheerde sleutels ondersteund voor bestaande accounts?

Deze functie is momenteel alleen beschikbaar voor nieuwe accounts.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Is er een plan om de nauw keurigheid van sleutels op account niveau te ondersteunen?

Momenteel worden er geen sleutels op container niveau in overweging genomen.

### <a name="how-does-customer-managed-keys-affect-backups"></a>Wat is de invloed van door de klant beheerde sleutels op back-ups?

Azure Cosmos DB maakt [regel matig en automatisch een back-up](./online-backup-and-restore.md) van de gegevens die in uw account zijn opgeslagen. Met deze bewerking wordt een back-up gemaakt van de versleutelde gegevens. Als u een herstelde back-up wilt gebruiken, moet de versleutelings sleutel die op het moment van de back-up wordt gebruikt, nog steeds beschikbaar zijn. Dit betekent dat er geen intrekken is gemaakt en dat de versie van de sleutel die op het moment van de back-up is gebruikt, nog steeds is ingeschakeld.

### <a name="how-do-i-revoke-an-encryption-key"></a>Hoe kan ik een versleutelings sleutel intrekken?

Het intrekken van de sleutel wordt uitgevoerd door de nieuwste versie van de sleutel uit te scha kelen:

![De versie van een sleutel uitschakelen](./media/how-to-setup-cmk/portal-akv-rev2.png)

Als u alle sleutels wilt intrekken van een Azure Key Vault-exemplaar, kunt u ook het toegangs beleid verwijderen dat is verleend aan de Azure Cosmos DB Principal:

![Het toegangs beleid voor de Azure Cosmos DB-Principal verwijderen](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Welke bewerkingen zijn beschikbaar nadat een door de klant beheerde sleutel is ingetrokken?

De enige bewerking die mogelijk is wanneer de versleutelings sleutel is ingetrokken, is account verwijdering.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gegevens versleuteling in azure Cosmos DB](./database-encryption-at-rest.md)
- Bekijk een overzicht van [beveiligde toegang tot gegevens in Cosmos DB](secure-access-to-data.md)
---
title: Zelfstudie over roulatie voor resources met één set verificatiereferenties
description: Gebruik deze zelfstudie om te leren hoe u het rouleren van een geheim kunt automatiseren voor resources die gebruikmaken van één set verificatiereferenties.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 9bff8c040f4cfed612278dd83ebb354b31a3a1f3
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801441"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Het rouleren van een geheim voor resources met één set verificatiereferenties automatiseren

De beste manier om te verifiëren bij Azure-services is met behulp van een [beheerde identiteit](../general/managed-identity.md), maar in enkele gevallen is dat niet mogelijk. In dergelijke gevallen worden toegangssleutels of geheimen gebruikt. U moet toegangssleutels of geheimen regelmatig rouleren.

In deze zelfstudie leert u hoe u de periodieke roulatie van geheimen voor databases en services die gebruikmaken van één set verificatiereferenties kunt automatiseren. Deze zelfstudie laat met name zien hoe SQL Server-wachtwoorden die in Azure Key Vault zijn opgeslagen, worden gerouleerd met behulp van een functie die wordt geactiveerd door een melding van Azure Event Grid:

![Diagram van roulatieoplossing](../media/rotate1.png)

1. Dertig dagen voor de vervaldatum van een geheim publiceert Key Vault deze "bijna verlopende" gebeurtenis naar Event Grid.
1. Event Grid controleert de gebeurtenisabonnementen en maakt gebruik van HTTP POST om het eindpunt voor de functie-app aan te roepen die is geabonneerd op de gebeurtenis.
1. De functie-app ontvangt de informatie over het geheim, genereert een nieuw willekeurig wachtwoord en maakt een nieuwe versie voor het geheim met het nieuwe wachtwoord in Key Vault.
1. De functie-app werkt SQL Server bij met het nieuwe wachtwoord.

> [!NOTE]
> Er kan een vertraging optreden tussen stap 3 en 4. Gedurende die tijd kan het geheim in Key Vault niet worden geverifieerd bij SQL Server. In het geval van een storing tijdens een van de stappen blijft Event Grid het twee uur opnieuw proberen.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Een sleutelkluis en een SQL Server-exemplaar maken

De eerste stap bestaat uit het maken van een sleutelkluis en een SQL Server-exemplaar en -database en het opslaan van het SQL Server-beheerderswachtwoord in Key Vault.

In deze zelfstudie wordt een bestaande Azure Resource Manager-sjabloon gebruikt om onderdelen te maken. U kunt de code hier vinden: [Voorbeeld van een eenvoudige sjabloon voor de roulatie van geheimen](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer onder **Resourcegroep** de optie **Nieuwe maken**. Geef de groep de naam **simplerotation**.
1. Selecteer **Aankoop**.

    ![Een resourcegroep maken](../media/rotate2.png)

U hebt nu een sleutelkluis, een SQL Server-exemplaar en een SQL-database. U kunt deze instelling in de Azure CLI controleren door de volgende opdracht uit te voeren:

```azurecli
az resource list -o table
```

Het resultaat ziet er ongeveer als volgt uit:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Een functie-app maken

Maak vervolgens een functie-app met een door het systeem beheerde identiteit, naast de andere vereiste onderdelen.

Voor de functie-app zijn de volgende onderdelen vereist:
- Een Azure App Service-plan
- Een opslagaccount
- Een toegangsbeleid voor het openen van geheimen in Key Vault via een door de functie-app beheerde identiteit

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer in de lijst **Resourcegroep** de optie **simplerotation**.
1. Selecteer **Aankoop**.

   ![Aankoop selecteren](../media/rotate3.png)

Nadat u de voorgaande stappen hebt voltooid, hebt u een opslagaccount, een serverfarm en een functie-app. U kunt deze instelling in de Azure CLI controleren door de volgende opdracht uit te voeren:

```azurecli
az resource list -o table
```

Het resultaat ziet er ongeveer als volgt uit:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

Zie [Een functie-app maken vanuit Azure Portal](../../azure-functions/functions-create-function-app-portal.md) en [Key Vault-verificatie bieden met een beheerde identiteit](../general/managed-identity.md) voor informatie over het maken van een functie-app en het gebruik van beheerde identiteit voor toegang tot Key Vault.

### <a name="rotation-function"></a>Roulatiefunctie
De functie gebruikt een gebeurtenis om de roulatie van een geheim te activeren door Key Vault en de SQL database bij te werken.

#### <a name="function-trigger-event"></a>Gebeurtenis om functie te activeren

Deze functie leest gebeurtenisgegevens en voert de roulatielogica uit:

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Logica voor geheimroulatie
Met deze roulatiemethode worden databasegegevens uit het geheim gelezen, wordt een nieuwe versie van het geheim gemaakt en wordt de database met het nieuwe geheim bijgewerkt:

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
U vindt de volledige code [op GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Functie-implementatie

1. Download het zip-bestand met de functie-app van [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Upload het bestand simplerotationsample-fn.zip naar Azure Cloud Shell.

   ![Het bestand uploaden](../media/rotate4.png)
1. Gebruik deze Azure CLI-opdracht om het zip-bestand naar de functie-app te implementeren:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Nadat de functie is geïmplementeerd, ziet u twee functies onder simplerotation-fn:

![De functies SimpleRotation en SimpleRotationHttpTest](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Een gebeurtenisabonnement toevoegen voor de gebeurtenis SecretNearExpiry

Kopieer de `eventgrid_extension`-sleutel van de functie-app:

   ![Instellingen voor functie-apps selecteren](../media/rotate6.png)

   ![eventgrid_extension key](../media/rotate7.png)

Gebruik de gekopieerde `eventgrid_extension`-sleutel en uw abonnements-ID in de volgende opdracht om een Event Grid-abonnement voor `SecretNearExpiry`-gebeurtenissen te maken:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Het geheim toevoegen aan Key Vault
Stel uw toegangsbeleid in om machtigingen voor het *beheren van geheimen* te verlenen aan gebruikers:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Maak een nieuw geheim met tags die de gegevensbron en de gebruikers-id van de SQL-database bevatten. Voeg een vervaldatum toe die op morgen is ingesteld.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Als u een geheim met een korte vervaldatum maakt, wordt onmiddellijk een `SecretNearExpiry`-gebeurtenis gepubliceerd, waardoor de functie voor roulatie van het geheim wordt geactiveerd.

## <a name="test-and-verify"></a>Testen en verifiëren
Na enkele minuten moet het `sqluser`-geheim automatisch worden gerouleerd.

Als u wilt controleren of het geheim is gerouleerd, gaat u naar **Key Vault** > **Geheimen**:

![Naar Geheimen gaan](../media/rotate8.png)

Open het **sqluser**-geheim en bekijk de oorspronkelijke en gerouleerde versies:

![Het sqluser-geheim openen](../media/rotate9.png)

### <a name="create-a-web-app"></a>Een webtoepassing maken

Maak een web-app om de SQL-referenties te controleren. Met deze web-app wordt het geheim opgehaald uit Key Vault, worden SQL databasegegevens en -referenties uit het geheim opgehaald en wordt de verbinding met SQL Server getest.

Voor de web-app zijn de volgende onderdelen vereist:
- Een web-app met door het systeem beheerde identiteit
- Een toegangsbeleid voor het openen van geheimen in Key Vault via een door de web-app beheerde identiteit

1. Selecteer de koppeling voor de Azure-sjabloonimplementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer de resourcegroep **simplerotation**.
1. Selecteer **Aankoop**.

### <a name="deploy-the-web-app"></a>De web-app implementeren

U kunt de broncode voor de web-app vinden op [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Voer de volgende stappen uit om de web-app te implementeren:

1. Download het zip-bestand met de functie-app van [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Upload het bestand simplerotationsample-app.zip naar Azure Cloud Shell.
1. Gebruik deze Azure CLI-opdracht om het zip-bestand naar de functie-app te implementeren:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>De web-app openen

Ga naar de geïmplementeerde toepassing en selecteer de URL:
 
![De URL selecteren](../media/rotate10.png)

Wanneer de toepassing in de browser wordt geopend, ziet u de **gegenereerde waarde van het geheim** en de waarde voor de **databaseverbinding** ingesteld op *true*.

## <a name="learn-more"></a>Meer informatie

- Overzicht: [Key Vault bewaken met Azure Event Grid (preview-versie)](../general/event-grid-overview.md)
- Procedure: [E-mail ontvangen wanneer een sleutelkluisgeheim verandert](../general/event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault (preview-versie)](../../event-grid/event-schema-key-vault.md)

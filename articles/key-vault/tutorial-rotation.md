---
title: Zelfstudie voor rotatie met één gebruiker/één wachtwoord
description: Gebruik deze zelfstudie om te leren hoe u de rotatie van een geheim automatiseren voor bronnen die verificatie met één gebruiker/wachtwoord gebruiken.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 684bfccd698217acdafdaac8c33f1e5531cdb4bf
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998902"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>De rotatie van een geheim automatiseren voor bronnen die verificatie voor één gebruiker/wachtwoord gebruiken

De beste manier om te verifiëren voor Azure-services is door een [beheerde identiteit te](managed-identity.md)gebruiken, maar er zijn enkele scenario's waarin dit geen optie is. In die gevallen worden toegangssleutels of geheimen gebruikt. U moet periodiek toegangssleutels of geheimen roteren.

In deze zelfstudie wordt uitgelegd hoe u de periodieke rotatie van geheimen automatiseren voor databases en services die verificatie met één gebruiker/wachtwoord gebruiken. In deze zelfstudie worden SQL Server-wachtwoorden die zijn opgeslagen in Azure Key Vault geroteert met behulp van een functie die wordt geactiveerd door de azure event grid-melding:

![Rotatiediagram](./media/rotate1.png)

1. Dertig dagen voor de vervaldatum van een geheim publiceert Key Vault de gebeurtenis 'bijna verlopen' aan Event Grid.
1. Event Grid controleert de gebeurtenisabonnementen en gebruikt HTTP POST om het eindpunt van de functie-app dat is geabonneerd op de gebeurtenis aan te roepen.
1. De functie-app ontvangt de geheime informatie, genereert een nieuw willekeurig wachtwoord en maakt een nieuwe versie voor het geheim met het nieuwe wachtwoord in Key Vault.
1. De functie-app werkt SQL Server bij met het nieuwe wachtwoord.

> [!NOTE]
> Er kan een vertraging zijn tussen stap 3 en 4. Gedurende die tijd kan het geheim in Key Vault zich niet verifiëren naar SQL Server. In het geval van een storing van een van de stappen, gebeurtenis raster opnieuw pogingen voor twee uur.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Een sleutelkluis en SQL Server-exemplaar maken

De eerste stap is het maken van een key vault en een SQL Server-instantie en -database en het SQL Server-beheerderswachtwoord opslaan in Key Vault.

Deze zelfstudie maakt gebruik van een bestaande Azure Resource Manager-sjabloon om onderdelen te maken. U vindt de code hier: [Basic Secret Rotation Template Sample](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Selecteer de koppeling Azure-sjabloonimplementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer **onder Resourcegroep**de optie **Nieuw maken**. Geef de groep **eenvoudig een naam.**
1. Selecteer **Aankoop**.

    ![Een resourcegroep maken](./media/rotate2.png)

U hebt nu een sleutelkluis, een SQL Server-exemplaar en een SQL-database. U deze instelling verifiëren in de Azure CLI door de volgende opdracht uit te voeren:

```azurecli
az resource list -o table
```

Het resultaat ziet er iets de volgende uitvoer:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Een functie-app maken

Maak vervolgens een functie-app met een systeembeheerde identiteit, naast de andere vereiste componenten.

De functie-app vereist deze componenten:
- Een Azure App Service-abonnement
- Een opslagaccount
- Een toegangsbeleid voor toegang tot geheimen in Key Vault via de door de functie-app beheerde identiteit

1. Selecteer de koppeling Azure-sjabloonimplementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer in de lijst **Resourcegroep** de optie **simplerotation**.
1. Selecteer **Aankoop**.

   ![Selecteer Aankoop](./media/rotate3.png)

Nadat u de vorige stappen hebt voltooid, hebt u een opslagaccount, een serverfarm en een functie-app. U deze instelling verifiëren in de Azure CLI door de volgende opdracht uit te voeren:

```azurecli
az resource list -o table
```

Het resultaat ziet er ongeveer als de volgende uitvoer:

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

Zie [Een functie-app maken vanuit de Azure-portal](../azure-functions/functions-create-function-app-portal.md) en [Sleutelkluisverificatie met een beheerde identiteit](managed-identity.md)voor informatie over het maken van een functie-app en het gebruik van beheerde identiteit om toegang te krijgen tot Key Vault.

### <a name="rotation-function"></a>Rotatie, functie
De functie maakt gebruik van een gebeurtenis om de rotatie van een geheim te activeren door Key Vault en de SQL-database bij te werken.

#### <a name="function-trigger-event"></a>Gebeurtenis Functietrigger

Met deze functie worden gebeurtenisgegevens gelezen en wordt de rotatielogica uitgevoerd:

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

#### <a name="secret-rotation-logic"></a>Geheime rotatielogica
Deze rotatiemethode leest databasegegevens uit het geheim, maakt een nieuwe versie van het geheim en werkt de database bij met het nieuwe geheim:

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
U vindt de volledige code op [GitHub.](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function)

#### <a name="function-deployment"></a>Functie-implementatie

1. Download het zip-bestand van de functie-app van [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Upload het simplerotationsample-fn.zip-bestand naar Azure Cloud Shell.

   ![Het bestand uploaden](./media/rotate4.png)
1. Gebruik deze opdracht Azure CLI om het zip-bestand te implementeren in de functie-app:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Nadat de functie is geïmplementeerd, ziet u twee functies onder simplerotation-fn:

![SimpleRotation en SimpleRotationHttpTest functies](./media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Een gebeurtenisabonnement toevoegen voor de gebeurtenis SecretNearExpiry

Kopieer de sleutel `eventgrid_extension` van de functie-app:

   ![Instellingen voor functie-apps selecteren](./media/rotate6.png)

   ![eventgrid_extension sleutel](./media/rotate7.png)

Gebruik de `eventgrid_extension` gekopieerde sleutel en uw abonnements-id in de `SecretNearExpiry` volgende opdracht om een abonnement op eventgrid voor gebeurtenissen te maken:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Voeg het geheim toe aan Key Vault
Stel uw toegangsbeleid in om *beheerdersmachtigingen* voor beheren toe te kennen aan gebruikers:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Maak een nieuw geheim met tags die de SQL-databasegegevensbron en de gebruikers-id bevatten. Voeg een vervaldatum toe die is ingesteld voor morgen.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Het maken van een geheim met `SecretNearExpiry` een korte vervaldatum zal onmiddellijk publiceren van een gebeurtenis, die op zijn beurt zal leiden tot de functie om het geheim te draaien.

## <a name="test-and-verify"></a>Testen en verifiëren
Na enkele minuten `sqluser` moet het geheim automatisch draaien.

Ga naar **Key Vault** > **Secrets**om te controleren of het geheim is gedraaid:

![Ga naar Geheimen](./media/rotate8.png)

Open het **sqluser-geheim** en bekijk de originele en geroteerde versies:

![Het sqluser-geheim openen](./media/rotate9.png)

### <a name="create-a-web-app"></a>Een webtoepassing maken

Als u de SQL-referenties wilt verifiëren, maakt u een web-app. Deze web-app haalt het geheim van Key Vault, haalt SQL-database-informatie en referenties uit het geheim en test de verbinding met SQL Server.

De web-app vereist deze componenten:
- Een web-app met door het systeem beheerde identiteit
- Een toegangsbeleid voor toegang tot geheimen in Key Vault via web-app beheerde identiteit

1. Selecteer de koppeling Azure-sjabloonimplementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer de **groep simplerotation** resource.
1. Selecteer **Aankoop**.

### <a name="deploy-the-web-app"></a>De web-app implementeren

U broncode voor de web-app vinden op [GitHub.](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp)

Voer de volgende stappen uit om de web-app te implementeren:

1. Download het zip-bestand van de functie-app van [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Upload het bestand simplerotationsample-app.zip naar Azure Cloud Shell.
1. Gebruik deze opdracht Azure CLI om het zip-bestand te implementeren in de functie-app:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>De web-app openen

Ga naar de geïmplementeerde toepassing en selecteer de URL:
 
![Selecteer de URL](./media/rotate10.png)

U moet de gegenereerde geheime waarde met een database verbonden waarde van true zien.

## <a name="learn-more"></a>Meer informatie

- Overzicht: [Key Vault bewaken met Azure Event Grid (voorbeeld)](event-grid-overview.md)
- How to: [E-mail ontvangen wanneer een sleutelkluis geheim verandert](event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault (voorbeeld)](../event-grid/event-schema-key-vault.md)

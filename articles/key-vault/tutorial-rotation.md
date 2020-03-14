---
title: Zelf studie voor het roteren van één gebruiker/wacht woord
description: Gebruik deze zelf studie voor het automatiseren van de draai hoek van één gebruiker/wacht woord
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 890932f7c0e46a2c9c0b6e1cf1461e4d7d25b409
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239369"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>De rotatie van een geheim automatiseren voor bronnen met verificatie met één gebruiker/wacht woord

Hoewel de beste manier om te verifiëren met Azure-Services, is het gebruik van een [beheerde identiteit](managed-identity.md), maar er zijn enkele scenario's waarin dit geen optie is. In deze gevallen worden toegangs sleutels of geheimen gebruikt. Toegangs sleutels of geheimen moeten regel matig worden gedraaid.

In deze zelf studie wordt gedemonstreerd hoe u de periodieke draaiing van geheimen voor data bases en services met enkelvoudige gebruikers-en wachtwoord verificatie automatiseert. Dit scenario roteert met name de SQL server-wacht woorden die zijn opgeslagen in de sleutel kluis met behulp van een functie die wordt geactiveerd door Event Grid melding:

![Draai diagram](./media/rotate1.png)

1. Dertig dagen voor de verval datum van een geheim Key Vault de gebeurtenis bijna verlopen publiceren naar Event Grid.
1. Event Grid controleert de gebeurtenis abonnementen en roept het functie-app eind punt dat is geabonneerd op deze gebeurtenis aan met behulp van http post.
1. De functie-app ontvangt de geheime informatie, genereert een nieuw wille keurig wacht woord en maakt een nieuwe versie voor het geheim met een nieuw wacht woord in Key Vault.
1. Met de functie-app wordt SQL bijgewerkt met het nieuwe wacht woord.

> [!NOTE]
> Er kan een vertraging optreden tussen stap 3 en 4 en tijdens die tijd kan het geheim van Key Vault niet geldig zijn voor verificatie bij SQL. In geval van een fout in een van de stappen Event Grid herhaaldelijk nieuwe pogingen voor twee uur.

## <a name="setup"></a>Instellen

## <a name="create-a-key-vault-and-sql-server"></a>Een sleutel kluis en SQL Server maken

Voordat we beginnen, moeten we een Key Vault maken, een SQL Server en data base maken en het SQL Server beheerders wachtwoord opslaan in Key Vault.

In deze zelf studie wordt een vooraf gemaakte Azure Resource Manager sjabloon gebruikt om onderdelen te maken. U kunt hier volledige code vinden: voor beeld van een [sjabloon basis geheim](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Klik op de koppeling Azure-sjabloon implementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer voor resource groep de optie nieuwe maken en geef deze de naam simplerotation.
1. Selecteer aanschaffen.

    ![Nieuwe resource groep maken](./media/rotate2.png)

Nadat u deze stappen hebt voltooid, hebt u een sleutel kluis, een SQL-Server en een SQL database. U kunt dit controleren in een Azure CLI-Terminal door het volgende uit te voeren:

```azurecli
az resource list -o table
```

De resultaten zien er ongeveer als volgt uit:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>functie-app maken

Maak een functie-app met een door het systeem beheerde identiteit, evenals de aanvullende vereiste onderdelen: 

Voor de functie-app zijn onder onderdelen en configuratie vereist:
- App Service-plan
- Opslagaccount
- Toegangs beleid voor toegang tot geheimen in Key Vault met behulp van functie-app beheerde identiteit

1. Klik op de koppeling Azure-sjabloon implementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer voor resource groep ' simplerotation '.
1. Selecteer aanschaffen.

   ![Scherm aankoop](./media/rotate3.png)

Nadat u de bovenstaande stappen hebt voltooid, hebt u een opslag account, een server farm en een functie-app.  U kunt dit controleren in een Azure CLI-Terminal door het volgende uit te voeren:

```azurecli
az resource list -o table
```

De resultaten zien er ongeveer als volgt uit:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
simplerotationstrg            simplerotation             eastus      Microsoft.Storage/storageAccounts
simplerotation-plan           simplerotation             eastus      Microsoft.Web/serverFarms
simplerotation-fn             simplerotation             eastus      Microsoft.Web/sites
```

Voor informatie over het maken van functie-app en het gebruik van beheerde identiteit voor toegang tot Key Vault raadpleegt u [een functie-app maken op basis van de Azure Portal](../azure-functions/functions-create-function-app-portal.md) en [Key Vault verificatie bieden met een beheerde identiteit](managed-identity.md)

### <a name="rotation-function-and-deployment"></a>Rotatie functie en-implementatie
De functie gebruikt gebeurtenis als trigger en voert de draai hoek van een geheim-update Key Vault en SQL database uit.

#### <a name="function-event-trigger-handler"></a>Trigger-handler voor functie gebeurtenis

Onder functie worden gebeurtenis gegevens gelezen en wordt rotatie logica uitgevoerd

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

#### <a name="secret-rotation-logic"></a>Logica voor het draaien van een geheim
Met deze rotatie methode worden database gegevens uit het geheim gelezen, wordt een nieuwe versie van het geheim gemaakt en wordt de data base bijgewerkt met een nieuw geheim.

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve Current Secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve Secret Info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check db connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update db password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
U kunt hier volledige code vinden: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Functie-implementatie

1. Het zip-bestand van de functie-app downloaden: https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Upload het bestand simplerotationsample-FN. zip naar Azure Cloud Shell.
 
1. Gebruik de onderstaande CLI-opdracht voor het implementeren van een zip-bestand naar de functie-app:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Scherm aankoop](./media/rotate4.png)

Na de implementatie moet u twee functies zien onder simplerotation-FN:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Gebeurtenis abonnement voor de gebeurtenis SecretNearExpiry toevoegen

Kopieer de eventgrid_extension sleutel van de functie-app.

![Azure Cloud Shell](./media/rotate6.png)

![Testen en verifiëren](./media/rotate7.png)

Gebruik de gekopieerde eventgrid-extensie sleutel en uw abonnements-ID in de onderstaande opdracht om een event grid-abonnement voor SecretNearExpiry-gebeurtenissen te maken.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Geheim toevoegen aan Key Vault
Stel uw toegangs beleid in om de machtiging ' geheimen beheren ' te geven aan gebruikers.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Maak nu een nieuw geheim met tags met de SQL data base-gegevens bron en-gebruikers-ID, waarbij de verval datum voor morgen is ingesteld.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Als u een geheim met een korte verloop datum maakt, wordt er onmiddellijk een SecretNearExpiry-gebeurtenis gepubliceerd, die vervolgens wordt geactiveerd door de functie om het geheim te draaien.

### <a name="test-and-verify"></a>Testen en verifiëren
Na enkele minuten moet SQLUser Secret automatisch worden gedraaid.

Ga naar Key Vault > geheimen om te controleren of de verificatie van de geheime rotatie is uitgeschakeld

![Testen en verifiëren](./media/rotate8.png)

Open het geheim ' SQLUser ' en Bekijk de oorspronkelijke en geroteerde versie

![Testen en verifiëren](./media/rotate9.png)

## <a name="create-web-app"></a>Web-app maken

Maak een webtoepassing om SQL-referenties te controleren. Deze webtoepassing haalt het geheim op uit de sleutel kluis, extraheert SQL Data Base-informatie en referenties uit het geheim en test de verbinding met SQL.

Voor de web-app zijn onder onderdelen en configuratie vereist:
- Web-app met door het systeem beheerde identiteit
- Toegangs beleid voor het openen van geheimen in Key Vault met beheerde identiteit voor web-apps

1. Klik op de koppeling Azure-sjabloon implementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. De resource groep **simplerotation** selecteren
1. Klik op kopen

### <a name="deploy-web-app"></a>Web-app implementeren

De bron code voor de web-app die u op https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp kunt vinden voor de implementatie van de web-app, doet u het volgende:

1. Down load het zip-bestand van de functie-app van https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Upload het bestand `simplerotationsample-app.zip` naar Azure Cloud Shell.
1. Gebruik deze Azure CLI-opdracht voor het implementeren van het zip-bestand naar de functie-app:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Webtoepassing openen

Ga naar de geïmplementeerde toepassing en klik op ' URL ':
 
![Testen en verifiëren](./media/rotate10.png)

De gegenereerde geheime waarde moet worden weer gegeven met de data base die is verbonden als waar.

## <a name="learn-more"></a>Meer informatie:

- Overzicht: [Key Vault bewaken met Azure Event grid (preview-versie)](event-grid-overview.md)
- Procedure: [E-mail ontvangen wanneer een sleutel kluis geheim verandert](event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenis schema voor Azure Key Vault (preview-versie)](../event-grid/event-schema-key-vault.md)

---
title: Zelfstudie voor het draaien van één gebruiker/wachtwoord
description: Deze zelfstudie gebruiken voor het automatiseren van rotatie van één gebruiker/wachtwoord
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239369"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-with-single-userpassword-authentication"></a>De rotatie van een geheim automatiseren voor resources met verificatie van één gebruiker/wachtwoord

Hoewel de beste manier om te verifiëren voor Azure-services is met behulp van een [beheerde identiteit,](managed-identity.md)zijn er enkele scenario's waarin dit geen optie is. In deze gevallen worden toegangssleutels of geheimen gebruikt. Toegangssleutels of geheimen moeten periodiek worden gedraaid.

Deze zelfstudie laat zien hoe u de periodieke rotatie van geheimen voor databases en services automatiseren met verificatie van één gebruiker/wachtwoord. In dit scenario worden SQL-serverwachtwoorden die zijn opgeslagen in de sleutelkluis geroteert met behulp van een functie die wordt geactiveerd door de melding gebeurtenisraster:

![Rotatiediagram](./media/rotate1.png)

1. Dertig dagen voor de vervaldatum van een geheim publiceert Key Vault de gebeurtenis 'bijna verlopen' naar Event Grid.
1. Event Grid controleert de gebeurtenisabonnementen en roept met http-bericht het eindpunt van de functie-app op dat is geabonneerd op deze gebeurtenis.
1. De functie-app ontvangt de geheime informatie, genereert een nieuw willekeurig wachtwoord en maakt een nieuwe versie voor het geheim met een nieuw wachtwoord in Key Vault.
1. De functie App werkt SQL bij met nieuw wachtwoord.

> [!NOTE]
> Er kan een vertraging zijn tussen stap 3 en 4 en gedurende die tijd geheim in Key Vault zou niet geldig zijn om te authenticeren naar SQL. In geval van een storing in een van de stappen wordt gebeurtenisraster opnieuw ingeroepen voor 2 uur.

## <a name="setup"></a>Instellen

## <a name="create-a-key-vault-and-sql-server"></a>Een sleutelkluis en SQL-server maken

Voordat we beginnen, moeten we een Key Vault maken, een SQL Server en database maken en het SQL Server-beheerderswachtwoord opslaan in Key Vault.

Deze zelfstudie maakt gebruik van een vooraf gemaakte Azure Resource Manager-sjabloon om onderdelen te maken. U vindt hier de volledige code: [Basic Secret Rotation Template Sample](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Klik op de koppeling Azure-sjabloonimplementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer voor 'Resourcegroep' de optie 'Nieuw maken' en geef deze de naam "simplerotation".
1. Selecteer 'Kopen'.

    ![Nieuwe resourcegroep maken](./media/rotate2.png)

Nadat u deze stappen hebt voltooid, beschikt u over een sleutelkluis, een SQL-server en een SQL-database. U dit verifiëren in een Azure CLI-terminal door het uitvoeren van:

```azurecli
az resource list -o table
```

De resultaten zullen er iets van dit:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv             simplerotation             eastus      Microsoft.KeyVault/vaults
simplerotation-sql            simplerotation             eastus      Microsoft.Sql/servers
simplerotation-sql/master     simplerotation             eastus      Microsoft.Sql/servers/databases
```

## <a name="create-function-app"></a>Functie-app maken

Maak een functie-app met een door het systeem beheerde identiteit, evenals de extra vereiste componenten: 

Functie-app vereist onder componenten en configuratie:
- App Service-plan
- Opslagaccount
- Toegang tot beleid om toegang te krijgen tot geheimen in Key Vault met behulp van Function App Managed Identity

1. Klik op de koppeling Azure-sjabloonimplementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer voor "Resourcegroep" de optie "simplerotation".
1. Selecteer 'Kopen'.

   ![Aankoopscherm](./media/rotate3.png)

Nadat u de bovenstaande stappen hebt voltooid, hebt u een opslagaccount, een serverfarm en een functie-app.  U dit verifiëren in een Azure CLI-terminal door het uitvoeren van:

```azurecli
az resource list -o table
```

De resultaten zullen er iets van dit:

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

Zie [Een functie-app maken vanuit de Azure-portal](../azure-functions/functions-create-function-app-portal.md) en [Sleutelkluis bieden met een beheerde identiteit voor](managed-identity.md) informatie over het maken van functie-app en het gebruik van Managed Identity om toegang te krijgen tot Key Vault.

### <a name="rotation-function-and-deployment"></a>Rotatiefunctie en -implementatie
Functie is het gebruik van gebeurtenis als trigger en het uitvoeren van rotatie van een geheime bijwerken Key Vault en SQL-database.

#### <a name="function-event-trigger-handler"></a>Triggerhandler voor functiegebeurtenis

Onderstaande functie leest gebeurtenisgegevens en voert rotatielogica uit

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

#### <a name="secret-rotation-logic"></a>Logica voor geheime rotatie
Deze rotatiemethode leest database-informatie uit het geheim, maakt een nieuwe versie van het geheim en werkt de database bij met een nieuw geheim.

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
U vindt hier de volledige code:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function

#### <a name="function-deployment"></a>Functie-implementatie

1. Download functie app zip-bestand:https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip

1. Upload bestand simplerotationsample-fn.zip naar Azure Cloud Shell.
 
1. Gebruik de opdracht CLI om zip-bestand te implementeren in de functie-app:

```azurecli
az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
```
![Aankoopscherm](./media/rotate4.png)

Na implementatie moet u twee functies opmerken onder simplerotation-fn:

![Azure Cloud Shell](./media/rotate5.png)

### <a name="add-event-subscription-for-secretnearexpiry-event"></a>Gebeurtenisabonnement toevoegen voor gebeurtenis 'SecretNearExpiry'

Kopieer de functie-app eventgrid_extension toets.

![Azure Cloud Shell](./media/rotate6.png)

![Testen en verifiëren](./media/rotate7.png)

Gebruik de gekopieerde eventgrid-extensiesleutel en uw abonnements-ID in de onderstaande opdracht om een abonnement op het gebeurtenisraster te maken voor SecretNearExpiry-gebeurtenissen.

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

### <a name="add-secret-to-key-vault"></a>Geheim toevoegen aan Key Vault
Stel uw toegangsbeleid in om gebruikers toestemming te geven voor 'geheimen beheren'.

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Maak nu een nieuw geheim met tags met sql-database datasource en user ID, met de vervaldatum ingesteld voor morgen.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Als u een geheim maakt met een korte vervaldatum, wordt onmiddellijk een SecretNearExpiry-gebeurtenis gepubliceerd, die op zijn beurt de functie zal activeren om het geheim te roteren.

### <a name="test-and-verify"></a>Testen en verifiëren
Na enkele minuten moet sqluser secret automatisch draaien.

Als u de verificatie van geheime rotatie wilt verifiëren, gaat u naar Key Vault > Secrets

![Testen en verifiëren](./media/rotate8.png)

Open het geheim van de sqluser en bekijk de originele en gedraaide versie

![Testen en verifiëren](./media/rotate9.png)

## <a name="create-web-app"></a>Web-app maken

Als u SQL-referenties wilt verifiëren, maakt u een webtoepassing. Deze webapplicatie haalt het geheim uit key vault, haalt sql-database-informatie en referenties uit het geheim en test de verbinding met sql.

De web-app vereist onderstaande componenten en configuratie:
- Web-app met door het systeem beheerde identiteit
- Toegang tot beleid om toegang te krijgen tot geheimen in Key Vault met Web App Managed Identity

1. Klik op de koppeling Azure-sjabloonimplementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. De **groep simplerotation** resource selecteren
1. Klik op Kopen

### <a name="deploy-web-app"></a>Web-app implementeren

Ga als volgt te werk https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp voor de web-app die u vinden voor de implementatie van de web-app:

1. Download het zip-bestand van de functie-app vanhttps://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip
1. Upload het `simplerotationsample-app.zip` bestand naar Azure Cloud Shell.
1. Gebruik deze opdracht Azure CLI om het zip-bestand te implementeren in de functie-app:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

#### <a name="open-web-application"></a>Webtoepassing openen

Ga naar de geïmplementeerde toepassing en klik op 'URL':
 
![Testen en verifiëren](./media/rotate10.png)

De gegenereerde geheime waarde moet worden weergegeven met Database Verbonden als waar.

## <a name="learn-more"></a>Meer informatie:

- Overzicht: [Key Vault bewaken met Azure Event Grid (voorbeeld)](event-grid-overview.md)
- How to: [E-mail ontvangen wanneer een sleutelkluis geheim verandert](event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenisschema voor Azure Key Vault (voorbeeld)](../event-grid/event-schema-key-vault.md)

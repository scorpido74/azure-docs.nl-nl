---
title: Zelf studie voor het draaien van één gebruiker/één wacht woord
description: Gebruik deze zelf studie om te leren hoe u de draai hoek van een geheim kunt automatiseren voor bronnen die gebruikmaken van verificatie met één gebruiker of één wacht woord.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f9c0dca29d173eb2c7893a20b2ab41dd31522e1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183208"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>De rotatie van een geheim automatiseren voor bronnen die gebruikmaken van verificatie met één gebruiker of één wacht woord

De beste manier om te verifiëren bij Azure-Services is door gebruik te maken van een [beheerde identiteit](../general/managed-identity.md), maar er zijn enkele scenario's waarin dit geen optie is. In dergelijke gevallen worden toegangs sleutels of geheimen gebruikt. U moet regel matig toegangs sleutels of geheimen draaien.

Deze zelf studie laat zien hoe u de periodieke draaiing van geheimen voor data bases en services die gebruikmaken van verificatie met één gebruiker of één wacht woord automatiseren. Met name deze zelf studie roteert SQL Server wacht woorden die zijn opgeslagen in Azure Key Vault met behulp van een functie die wordt geactiveerd door Azure Event Grid melding:

![Diagram van rotatie oplossing](../media/rotate1.png)

1. Dertig dagen voor de verval datum van een geheim, Key Vault de gebeurtenis bijna verlopen publiceren naar Event Grid.
1. Event Grid controleert de gebeurtenis abonnementen en maakt gebruik van HTTP POST om het functie-app-eind punt aan te roepen dat is geabonneerd op de gebeurtenis.
1. De functie-app ontvangt de geheime informatie, genereert een nieuw wille keurig wacht woord en maakt een nieuwe versie voor het geheim met het nieuwe wacht woord in Key Vault.
1. De functie-app wordt SQL Server bijgewerkt met het nieuwe wacht woord.

> [!NOTE]
> Er kan een vertraging optreden tussen stap 3 en 4. Gedurende die tijd kan het geheim in Key Vault niet worden geverifieerd bij SQL Server. In het geval van een storing van een van de stappen Event Grid herhaaldelijk twee uur.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Een sleutel kluis en een SQL Server-exemplaar maken

De eerste stap bestaat uit het maken van een sleutel kluis en een SQL Server-exemplaar en-data base en het SQL Server beheerders wachtwoord in Key Vault op te slaan.

In deze zelf studie wordt een bestaande Azure Resource Manager sjabloon gebruikt om onderdelen te maken. U kunt de code hier vinden: voor beeld van een [sjabloon basis geheim](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Selecteer de koppeling voor de Azure-sjabloon implementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer onder **resource groep**de optie **nieuwe maken**. Noem de groep **simplerotation**.
1. Selecteer **Aankoop**.

    ![Een resourcegroep maken](../media/rotate2.png)

U hebt nu een sleutel kluis, een SQL Server-exemplaar en een SQL database. U kunt deze instelling in de Azure CLI controleren door de volgende opdracht uit te voeren:

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
- Een Azure App Service plan
- Een opslagaccount
- Een toegangs beleid voor het openen van geheimen in Key Vault via door de functie-app beheerde identiteit

1. Selecteer de koppeling voor de Azure-sjabloon implementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer **simplerotation**in de lijst **resource groep** .
1. Selecteer **Aankoop**.

   ![Aankoop selecteren](../media/rotate3.png)

Nadat u de voor gaande stappen hebt voltooid, hebt u een opslag account, een server farm en een functie-app. U kunt deze instelling in de Azure CLI controleren door de volgende opdracht uit te voeren:

```azurecli
az resource list -o table
```

Het resultaat ziet er ongeveer uit zoals in de volgende uitvoer:

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

Voor informatie over het maken van een functie-app en het gebruik van beheerde identiteit voor toegang tot Key Vault raadpleegt u [een functie-app maken op basis van de Azure Portal](../../azure-functions/functions-create-function-app-portal.md) en [biedt u Key Vault verificatie met een beheerde identiteit](../general/managed-identity.md).

### <a name="rotation-function"></a>De functie Rotation
De functie gebruikt een gebeurtenis om de draai hoek van een geheim te activeren door Key Vault en de SQL database bij te werken.

#### <a name="function-trigger-event"></a>Functie trigger gebeurtenis

Deze functie leest gebeurtenis gegevens en voert de draai logica uit:

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
Met deze rotatie methode worden database gegevens uit het geheim gelezen, wordt een nieuwe versie van het geheim gemaakt en wordt de data base bijgewerkt met het nieuwe geheim:

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
U kunt de volledige code vinden op [github](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Functie-implementatie

1. Down load het zip-bestand van de functie-app vanuit [github](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Upload het bestand simplerotationsample-FN. zip naar Azure Cloud Shell.

   ![Het bestand uploaden](../media/rotate4.png)
1. Gebruik deze Azure CLI-opdracht voor het implementeren van het zip-bestand naar de functie-app:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Nadat de functie is geïmplementeerd, ziet u twee functies onder simplerotation-FN:

![De functies SimpleRotation en SimpleRotationHttpTest](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Een gebeurtenis abonnement toevoegen voor de gebeurtenis SecretNearExpiry

Kopieer de sleutel van `eventgrid_extension` de functie-app:

   ![Instellingen voor functie-apps selecteren](../media/rotate6.png)

   ![eventgrid_extension sleutel](../media/rotate7.png)

Gebruik de gekopieerde `eventgrid_extension` sleutel en uw abonnements-id in de volgende opdracht om een event grid- `SecretNearExpiry` abonnement te maken voor gebeurtenissen:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Voeg het geheim toe aan Key Vault
Stel uw toegangs beleid in voor het verlenen van machtigingen voor het *beheren van geheimen* aan gebruikers:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Maak een nieuw geheim met tags die de SQL database gegevens bron en de gebruikers-ID bevatten. Neem een verval datum op die voor morgen is ingesteld.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Als u een geheim met een korte verloop datum maakt, wordt `SecretNearExpiry` er onmiddellijk een gebeurtenis gepubliceerd, die vervolgens de functie activeren om het geheim te draaien.

## <a name="test-and-verify"></a>Testen en verifiëren
Na enkele minuten moet het `sqluser` geheim automatisch worden gedraaid.

Ga naar **Key Vault** > **geheimen**om te controleren of het geheim is gedraaid:

![Ga naar geheimen](../media/rotate8.png)

Open het **SQLUser** -geheim en Bekijk de oorspronkelijke en geroteerde versies:

![Open het SQLUser-geheim](../media/rotate9.png)

### <a name="create-a-web-app"></a>Een webtoepassing maken

Maak een web-app om de SQL-referenties te controleren. Met deze web-app wordt het geheim opgehaald uit Key Vault, worden SQL database informatie en referenties uit het geheim opgehaald en wordt de verbinding met SQL Server getest.

Voor de web-app zijn de volgende onderdelen vereist:
- Een web-app met door het systeem beheerde identiteit
- Een toegangs beleid voor het openen van geheimen in Key Vault via door de web app beheerde identiteit

1. Selecteer de koppeling voor de Azure-sjabloon implementatie:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Selecteer de resource groep **simplerotation** .
1. Selecteer **Aankoop**.

### <a name="deploy-the-web-app"></a>De web-app implementeren

U vindt de bron code voor de web-app op [github](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Voer de volgende stappen uit om de web-app te implementeren:

1. Down load het zip-bestand van de functie-app vanuit [github](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Upload het bestand simplerotationsample-app. zip naar Azure Cloud Shell.
1. Gebruik deze Azure CLI-opdracht voor het implementeren van het zip-bestand naar de functie-app:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>De web-app openen

Ga naar de geïmplementeerde toepassing en selecteer de URL:
 
![De URL selecteren](../media/rotate10.png)

Wanneer de toepassing in de browser wordt geopend, ziet u de **gegenereerde geheime waarde** en de waarde *waar* **is verbonden met de data base** .

## <a name="learn-more"></a>Meer informatie

- Overzicht: [Key Vault bewaken met Azure Event grid (preview-versie)](../general/event-grid-overview.md)
- Procedure: [E-mail ontvangen wanneer een sleutel kluis geheim verandert](../general/event-grid-logicapps.md)
- [Azure Event Grid-gebeurtenis schema voor Azure Key Vault (preview-versie)](../../event-grid/event-schema-key-vault.md)

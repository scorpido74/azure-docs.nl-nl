---
title: 'Quickstart: Azure Key Vault-clientbibliotheek voor Java'
description: Biedt indelings- en inhoudscriteria voor het schrijven van quickstarts voor Azure SDK-clientbibliotheken.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 6c29141a2e255588ffa581b84ffeb4ddd7fdb703
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87324706"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Quickstart: Azure Key Vault-clientbibliotheek voor Java

Ga aan de slag met de Azure Key Vault-clientbibliotheek voor Java. Volg de onderstaande stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-clientbibliotheek voor Java voor het volgende:

- De beveiliging en controle over sleutels en wachtwoorden verbeteren.
- In een paar minuten versleutelingssleutels maken en importeren.
- Latentie verminderen met schalen in de cloud en globale redundantie.
- Taken voor TLS/SSL-certificaten vereenvoudigen en automatiseren.
- FIPS 140-2 level 2-gevalideerde HSM's gebruiken.

Aanvullende bronnen:

* [Broncode](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault)
* [API-referentiedocumentatie](https://azure.github.io/azure-sdk-for-java)
* [Productdocumentatie](index.yml)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement (u kunt [een gratis abonnement maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable)-versie 8 of hoger
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/)

In deze quickstart wordt ervan uitgegaan dat u [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) en [Apache Maven](https://maven.apache.org) uitvoert in een Linux-terminalvenster.

## <a name="setting-up"></a>Instellen

### <a name="create-new-java-console-app"></a>Nieuwe Java-console-app maken

Gebruik in een consolevenster de opdracht `mvn` om een nieuwe Java-console-app te maken met de naam `akv-java`.

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

De uitvoer van het project ziet er ongeveer als volgt uit:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Wijzig uw map in de zojuist gemaakte map akv-java/.

```console
cd akv-java
```

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand *pom.xml* in uw teksteditor. Voeg de volgende afhankelijkheidselementen toe aan de groep met afhankelijkheden.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Een resourcegroep en sleutelkluis maken

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Een service-principal maken

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De service-principal toegang verlenen tot uw sleutelkluis

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>Omgevingsvariabelen instellen

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Objectmodel

Met de Azure Key Vault-client bibliotheek voor Java kunt u sleutels en gerelateerde assets, zoals certificaten en geheimen, beheren. In de onderstaande codevoorbeelden ziet u hoe u een client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

De volledige console-app is [hieronder](#sample-code) beschikbaar.

## <a name="code-examples"></a>Codevoorbeelden

### <a name="add-directives"></a>Instructies toevoegen

Voeg de volgende instructies toe aan het begin van de code:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Een client verifiëren en maken

Het verifiëren van uw sleutelkluis en het maken van een sleutelkluis-client is afhankelijk van de omgevingsvariabelen in de stap [Omgevingsvariabelen instellen](#set-environmental-variables) hierboven. De naam van de sleutelkluis wordt uitgebreid naar de sleutelkluis-URI, met de indeling `https://<your-key-vault-name>.vault.azure.net`.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, kunt u een geheim toevoegen aan uw sleutelkluis met behulp van de methode `secretClient.setSecret`. Hiervoor is een naam voor het geheim vereist: we hebben in dit voorbeeld de waarde 'mySecret' toegewezen aan de variabele `secretName`.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

U kunt controleren of het geheim is ingesteld met de opdracht [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde ophalen met de `secretClient.getSecret`-methode.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

U hebt nu toegang tot de waarde van het opgehaalde geheim met `retrievedSecret.getValue()`.

### <a name="delete-a-secret"></a>Een geheim verwijderen

Ten slotte verwijderen we het geheim uit uw sleutelkluis met de `secretClient.beginDeleteSecret`-methode.

```java
secretClient.beginDeleteSecret(secretName);
```

U kunt controleren of het geheim is verdwenen met de opdracht [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show):

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de sleutelkluis en de bijbehorende resourcegroep niet meer nodig hebt, kunt u Azure CLI of Azure PowerShell gebruiken om ze te verwijderen.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Voorbeeldcode

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de [Gids voor Azure Key Vault-ontwikkelaars](../general/developers-guide.md)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)

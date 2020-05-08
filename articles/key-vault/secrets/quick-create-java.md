---
title: Quick Start-Azure Key Vault-client bibliotheek voor Java
description: Biedt indelings-en inhouds criteria voor het schrijven van Quick starts voor Azure SDK-client bibliotheken.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: a3e6901d41cc14ab3ea27f7c8cd9bbc4c98608a6
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901332"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Snelstartgids: Azure Key Vault-client bibliotheek voor Java

Ga aan de slag met de Azure Key Vault-client bibliotheek voor Java. Volg de onderstaande stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen.

Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Gebruik de Key Vault-client bibliotheek voor Java om het volgende te doen:

- Verbeter de beveiliging en controle over sleutels en wacht woorden.
- Versleutelings sleutels maken en importeren in een paar minuten.
- Verminder de latentie met Cloud schaal en wereld wijde redundantie.
- Vereenvoudig en Automatiseer taken voor TLS/SSL-certificaten.
- Gebruik FIPS 140-2 level 2 Validated Hsm's.

[Source code](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault) | Naslag[informatie over de](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets) bron code-API-[documentatie](https://azure.github.io/azure-sdk-for-java) | [product documentatie](index.yml) | 

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- JDK-versie 8 of hoger van [Java Development Kit](/java/azure/jdk/?view=azure-java-stable)
- [Apache Maven](https://maven.apache.org)
- [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Azure PowerShell](/powershell/azure/overview)

In deze Snelstartgids wordt ervan uitgegaan dat u [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) en [Apache Maven](https://maven.apache.org) uitvoert in een Linux-Terminal venster.

## <a name="setting-up"></a>Instellen

### <a name="create-new-java-console-app"></a>Nieuwe Java-Console-app maken

Gebruik in een console venster de `mvn` opdracht voor het maken van een nieuwe Java-Console-app `akv-java`met de naam.

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

Wijzig uw directory in de zojuist gemaakte Azure-Java/-map.

```console
cd akv-java
```

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand *pom. XML* in de tekst editor. Voeg de volgende afhankelijkheids elementen toe aan de groep afhankelijkheden.

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

### <a name="create-a-resource-group-and-key-vault"></a>Een resource groep en sleutel kluis maken

In deze Snelstartgids wordt gebruikgemaakt van een vooraf gemaakte Azure-sleutel kluis. U kunt een sleutel kluis maken met behulp van de stappen in de [Snelstartgids van Azure cli](quick-create-cli.md), [Azure PowerShell Snelstartgids](quick-create-powershell.md)of [Azure Portal Quick](quick-create-portal.md)start. U kunt ook de volgende Azure CLI-opdrachten uitvoeren.

> [!Important]
> Elke sleutel kluis moet een unieke naam hebben. Vervang <uw-unieke kluis naam> door de naam van uw sleutel kluis in de volgende voor beelden.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Een service-principal maken

De eenvoudigste manier om een toepassing in de cloud te verifiëren, is met een beheerde identiteit. Zie [een beheerde app Service-id gebruiken voor toegang tot Azure Key Vault](../general/managed-identity.md) voor meer informatie.

In het omwille van de eenvoud maakt deze Snelstartgids echter een bureaublad toepassing, waarvoor het gebruik van een Service-Principal en een beleid voor toegangs beheer vereist is. Voor uw service-principal is een unieke naam vereist in de notatie ' http://<My-Unique-Service-Principle-Name> '.

Maak een service-principal met behulp van de opdracht Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://<my-unique-service-principle-name>" --sdk-auth
```

Met deze bewerking wordt een reeks sleutel-waardeparen geretourneerd. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Let op de clientId, clientSecret en tenantId, aangezien we deze in de volgende twee stappen zullen gebruiken.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>De Service-Principal toegang verlenen tot uw sleutel kluis

Maak een toegangs beleid voor de sleutel kluis die machtigingen verleent aan uw Service-Principal door de clientId door te geven aan de opdracht [AZ-set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Geef de Service-Principal Get, List en stel machtigingen voor zowel sleutels als geheimen.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Omgevings variabelen instellen

De methode DefaultAzureCredential in onze toepassing is afhankelijk van drie omgevings variabelen `AZURE_CLIENT_ID`: `AZURE_CLIENT_SECRET`, en `AZURE_TENANT_ID`. Gebruik deze variabelen instellen op de clientId-, clientSecret-en tenantId-waarden die u hebt genoteerd in de stap [een service-principal maken](#create-a-service-principal) hierboven. Gebruik de `export VARNAME=VALUE` indeling om uw omgevings variabelen in te stellen. (Met deze methode worden alleen de variabelen ingesteld voor uw huidige shell en processen die zijn gemaakt op basis van de shell; als u deze variabelen permanent `/etc/environment ` wilt toevoegen aan uw omgeving, bewerkt u het bestand.) 

U moet de naam van de sleutel kluis ook opslaan als een omgevings variabele `KEY_VAULT_NAME`genaamd.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Object model

Met de Azure Key Vault-client bibliotheek voor Java kunt u sleutels en gerelateerde assets beheren, zoals certificaten en geheimen. In de onderstaande code voorbeelden ziet u hoe u een-client maakt, een geheim instelt, een geheim ophaalt en een geheim verwijdert.

De volledige console-app is [hieronder](#sample-code).

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

Het verifiëren van uw sleutel kluis en het maken van een sleutel kluis-client is afhankelijk van de omgevings variabelen in de bovenstaande stap [omgevings variabelen instellen](#set-environmental-variables) . De naam van de sleutel kluis wordt uitgebreid naar de sleutel kluis-URI in de indeling `https://<your-key-vault-name>.vault.azure.net`.

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Een geheim opslaan

Nu uw toepassing is geverifieerd, kunt u met de `secretClient.setSecret` -methode een geheim aan uw sleutel kluis toevoegen. Hiervoor is een naam vereist voor het geheim: we hebben de waarde ' mySecret ' toegewezen aan de `secretName` variabele in dit voor beeld.  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

U kunt controleren of het geheim is ingesteld met de opdracht [AZ sleutel kluis Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Een geheim ophalen

U kunt nu de eerder ingestelde waarde met de `secretClient.getSecret` methode ophalen.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

U hebt nu toegang tot de waarde van het opgehaalde geheim `retrievedSecret.getValue()`met.

### <a name="delete-a-secret"></a>Een geheim verwijderen

Ten slotte gaan we het geheim verwijderen uit de sleutel kluis met de `secretClient.beginDeleteSecret` -methode.

```java
secretClient.beginDeleteSecret(secretName);
```

U kunt controleren of het geheim is verdwenen met de opdracht [AZ sleutel kluis Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de Azure CLI of Azure PowerShell gebruiken om uw sleutel kluis en de bijbehorende resource groep te verwijderen.

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

In deze Snelstartgids hebt u een sleutel kluis gemaakt, een geheim opgeslagen en dat geheim opgehaald. Ga verder met de volgende artikelen voor meer informatie over Key Vault en hoe u deze integreert met uw toepassingen.

- Een [overzicht van Azure Key Vault](../general/overview.md) lezen
- Raadpleeg de [Azure Key Vault hand leiding voor ontwikkel aars](../general/developers-guide.md)
- [Azure Key Vault aanbevolen procedures](../general/best-practices.md) controleren

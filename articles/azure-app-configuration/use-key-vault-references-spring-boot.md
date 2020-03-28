---
title: Zelfstudie voor het gebruik van Verwijzingen naar azure-app Configuration Key Vault in een Java Spring Boot-app | Microsoft Documenten
description: In deze zelfstudie leert u hoe u de Key Vault-referenties van Azure App Configuration gebruiken vanuit een Java Spring Boot-app
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: b6b6d10165eed331c397e17a18e382b095e1f74f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79216749"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Zelfstudie: Key Vault-verwijzingen gebruiken in een Java Spring-app

In deze zelfstudie leert u hoe u de Azure App Configuration-service samen met Azure Key Vault gebruiken. App Configuration en Key Vault zijn aanvullende services die naast elkaar worden gebruikt in de meeste implementaties van toepassingen.

App-configuratie helpt u de services samen te gebruiken door sleutels te maken die verwijzen naar waarden die zijn opgeslagen in Key Vault. Wanneer app-configuratie dergelijke sleutels maakt, worden de URI's van key vault-waarden opgeslagen in plaats van de waarden zelf.

Uw toepassing gebruikt de clientprovider App-configuratie om Key Vault-verwijzingen op te halen, net als voor andere sleutels die zijn opgeslagen in app-configuratie. In dit geval zijn de waarden die zijn opgeslagen in app-configuratie URI's die verwijzen naar de waarden in de sleutelkluis. Het zijn geen Key Vault-waarden of referenties. Omdat de clientprovider de sleutels herkent als Key Vault-referenties, gebruikt deze Key Vault om hun waarden op te halen.

Uw toepassing is verantwoordelijk voor het correct verifiëren van zowel app-configuratie als Key Vault. De twee diensten communiceren niet rechtstreeks.

In deze zelfstudie ziet u hoe u Key Vault-verwijzingen in uw code implementeert. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u verdergaat, [voltooit u eerst een Java Spring-app met app-configuratie.](./quickstart-java-spring-app.md)

U elke codeeditor gebruiken om de stappen in deze zelfstudie uit te voeren. [Visual Studio Code](https://code.visualstudio.com/) is bijvoorbeeld een cross-platform codeeditor die beschikbaar is voor de Windows-, macOS- en Linux-besturingssystemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een app-configuratiesleutel die verwijst naar een waarde die is opgeslagen in Key Vault.
> * Toegang tot de waarde van deze sleutel vanuit een Java Spring-toepassing.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement - [maak er gratis een](https://azure.microsoft.com/free/)
* Een ondersteunde [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk) met versie 8.
* [Apache Maven](https://maven.apache.org/download.cgi) versie 3.0 of hoger.

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer de optie **Een resource maken** in de linkerbovenhoek van de Azure-portal:

    ![Uitvoer nadat het maken van sleutelkluizen is voltooid](./media/quickstarts/search-services.png)
1. Voer in het zoekvak **Sleutelkluis**in .
1. Selecteer **Sleutelkluizen** aan de linkerkant in de lijst met resultaten.
1. Selecteer Toevoegen **in Sleutelkluizen** **.**
1. Geef aan de rechterkant in **De sleutelkluis maken**de volgende informatie op:
    * Selecteer **Abonnement** om een abonnement te kiezen.
    * Selecteer **in Resourcegroep**De optie **Nieuw maken** en voer een naam van de resourcegroep in.
    * In **Key vault name**is een unieke naam vereist. Voer voor deze zelfstudie **Contoso-vault2**in.
    * Kies in de vervolgkeuzelijst **Regio** een locatie.
1. Laat de andere **opties voor sleutelkluis maken** met hun standaardwaarden.
1. Selecteer **Maken**.

Op dit moment is uw Azure-account de enige die toegang heeft tot deze nieuwe kluis.

![Uitvoer nadat het maken van sleutelkluizen is voltooid](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Om een geheim aan de kluis toe te voegen, moet u slechts een paar extra stappen nemen. Voeg in dit geval een bericht toe dat u gebruiken om Key Vault retrieval te testen. Het bericht heet **Message**en u slaat de waarde 'Hallo van Sleutelkluis' erin op.

1. Selecteer **Geheimen**op de eigenschappenpagina's Van De Sleutelkluis .
1. Selecteer **Genereren/importeren**.
1. Voer in het deelvenster **Een geheim maken** de volgende waarden in:
    * **Uploadopties:** Voer **Handleiding**in .
    * **Naam:** Voer **bericht**in .
    * **Waarde:** Voer **Hallo in vanuit Key Vault**.
1. Laat de andere **Een geheime** eigenschappen maken met hun standaardwaarden.
1. Selecteer **Maken**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Een Key Vault-verwijzing toevoegen aan app-configuratie

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Selecteer **Alle resources**en selecteer vervolgens de instantie app-configuratiearchief die u in de snelstart hebt gemaakt.

1. Selecteer **Configuratieverkenner**.

1. Selecteer **+ Referentie van** > **de toetskluis maken**en geef vervolgens de volgende waarden op:
    * **Sleutel**: Selecteer **/application/config.keyvaultmessage**
    * **Label:** Laat deze waarde leeg.
    * **Abonnement**, **Resourcegroep**en **Sleutelkluis:** voer de waarden in die overeenkomen met de waarden in de sleutelkluis die u in de vorige sectie hebt gemaakt.
    * **Geheim:** selecteer het geheime bericht met de naam **Message** dat u in de vorige sectie hebt gemaakt.

## <a name="connect-to-key-vault"></a>Verbinding maken met Key Vault

1. In deze zelfstudie gebruikt u een serviceprincipal voor verificatie naar Key Vault. Als u deze serviceprincipal wilt maken, gebruikt u de opdracht Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Met deze bewerking wordt een reeks sleutel-/waardeparen geretourneerd:

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

1. Voer de volgende opdracht uit om de serviceprincipal toegang te geven tot uw sleutelkluis:

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Voer de volgende opdracht uit om uw object-id op te halen en deze vervolgens toe te voegen aan app-configuratie.

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Maak de volgende omgevingsvariabelen met de waarden voor de serviceprincipal die in de vorige stap zijn weergegeven:

    * **AZURE_CLIENT_ID**: *clientId*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *tenantId*

> [!NOTE]
> Deze Key Vault-referenties worden alleen gebruikt in uw toepassing.  Uw toepassing verifieert rechtstreeks met Key Vault met behulp van deze referenties zonder tussenkomst van de app-configuratieservice.  De Key Vault biedt verificatie voor zowel uw toepassing als uw app-configuratieservice zonder sleutels te delen of bloot te stellen.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uw code bijwerken om een Key Vault-referentie te gebruiken

1. *MessageProperties.java openen*. Voeg een nieuwe variabele genaamd *keyVaultMessage*toe:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Open *HelloController.java*. Werk de *getMessage-methode* bij om het bericht op te nemen dat is opgehaald uit Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Maak een nieuw bestand genaamd *AzureCredentials.java* en voeg onderstaande code toe.

    ```java
    package com.example;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Maak een nieuw bestand genaamd *AppConfiguration.java*. En voeg de code hieronder.

    ```java
    package com.example;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Maak een nieuw bestand in uw resources META-INF directory genaamd *spring.factories* en toevoegen.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.AppConfiguration
    ```

1. Bouw je Spring Boot applicatie met Maven en voer deze uit, bijvoorbeeld:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Nadat uw toepassing is uitgevoerd, gebruikt u *krul* om uw toepassing te testen, bijvoorbeeld:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    U ziet het bericht dat u hebt ingevoerd in de App Configuration Store. U ziet ook het bericht dat u hebt ingevoerd in Key Vault.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een app-configuratiesleutel gemaakt die verwijst naar een waarde die is opgeslagen in Key Vault. Ga verder naar de volgende zelfstudie voor meer informatie over het gebruik van functievlaggen in uw Java Spring-toepassing.

> [!div class="nextstepaction"]
> [Beheerde identiteitsintegratie](./quickstart-feature-flag-spring-boot.md)

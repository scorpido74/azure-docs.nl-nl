---
title: Zelf studie voor het gebruik van Azure-app configuratie Key Vault verwijzingen in een Java Spring boot-app | Microsoft Docs
description: In deze zelf studie leert u hoe u Key Vault verwijzingen van Azure-app configuratie kunt gebruiken vanuit een Java Spring boot-app
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
ms.openlocfilehash: 6a5bc947c3ea414f197df9cfcdd5f233e4654cbc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085022"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Zelf studie: referenties van Key Vault gebruiken in een Java lente-app

In deze zelf studie leert u hoe u de Azure-app Configuration-service kunt gebruiken in combi natie met Azure Key Vault. App-configuratie en Key Vault zijn complementaire services die naast elkaar worden gebruikt in de meeste toepassings implementaties.

Met app-configuratie kunt u de services gezamenlijk gebruiken door sleutels te maken die verwijzen naar waarden die zijn opgeslagen in Key Vault. Wanneer de app-configuratie dergelijke sleutels maakt, worden de Uri's van Key Vault waarden opgeslagen in plaats van de waarden zelf.

In uw toepassing wordt gebruikgemaakt van de client provider voor app-configuratie om Key Vault verwijzingen op te halen, net zoals bij andere sleutels die zijn opgeslagen in de app-configuratie. In dit geval zijn de waarden die zijn opgeslagen in de app-configuratie Uri's die verwijzen naar de waarden in de Key Vault. Ze zijn niet Key Vault waarden of referenties. Omdat de client provider de sleutels als Key Vault verwijzingen herkent, wordt Key Vault gebruikt om de waarden op te halen.

Uw toepassing is verantwoordelijk voor het correct verifiëren van de app-configuratie en de Key Vault. De twee services communiceren niet rechtstreeks.

In deze zelf studie wordt uitgelegd hoe u Key Vault verwijzingen in uw code implementeert. Dit is gebaseerd op de web-app die is geïntroduceerd in de quickstarts. Voordat u doorgaat, moet u eerst [een Java-lente-app maken met app-configuratie](./quickstart-java-spring-app.md) .

U kunt elke code-editor gebruiken om de stappen in deze zelf studie uit te voeren. [Visual Studio code](https://code.visualstudio.com/) is bijvoorbeeld een platformoverschrijdende code-editor die beschikbaar is voor de Windows-, macOS-en Linux-besturings systemen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een app-configuratie sleutel maken die verwijst naar een waarde die is opgeslagen in Key Vault.
> * Toegang tot de waarde van deze sleutel vanuit een Java-lente toepassing.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Een ondersteund [jdk (Java Development Kit)](https://docs.microsoft.com/java/azure/jdk) met versie 8.
* [Apache Maven](https://maven.apache.org/download.cgi) -versie 3,0 of hoger.

## <a name="create-a-vault"></a>Een kluis maken

1. Selecteer de optie **een resource maken** in de linkerbovenhoek van de Azure portal:

    ![Uitvoer nadat het maken van de sleutel kluis is voltooid](./media/quickstarts/search-services.png)
1. Voer in het zoekvak **Key Vault**in.
1. Selecteer in de lijst met resultaten de optie **sleutel kluizen** aan de linkerkant.
1. Selecteer **toevoegen**in **sleutel kluizen**.
1. Geef aan de rechter kant in **sleutel kluis maken**de volgende informatie op:
    * Selecteer **abonnement** om een abonnement te kiezen.
    * Selecteer in **resource groep**de optie **nieuwe maken** en voer een naam voor de resource groep in.
    * In de naam van de **sleutel kluis**is een unieke naam vereist. Voer voor deze zelf studie **Contoso-vault2**in.
    * Kies een locatie in de vervolg keuzelijst **regio** .
1. Wijzig de andere opties voor **sleutel kluis maken** met de standaard waarden.
1. Selecteer **Maken**.

Op dit moment is uw Azure-account het enige dat is gemachtigd voor toegang tot deze nieuwe kluis.

![Uitvoer nadat het maken van de sleutel kluis is voltooid](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de kluis, hoeft u slechts een paar extra stappen uit te voeren. In dit geval voegt u een bericht toe dat u kunt gebruiken om Key Vault ophalen te testen. Het bericht wordt **bericht**genoemd en u slaat de waarde ' Hello from key Vault ' op.

1. Selecteer op de pagina eigenschappen van Key Vault de optie **geheimen**.
1. Selecteer **genereren/importeren**.
1. Voer in het deel venster **een geheim maken** de volgende waarden in:
    * **Opties voor uploaden**: Voer **hand matig**in.
    * **Naam**: Voer een **bericht**in.
    * **Waarde**: Voer een **Hello van Key Vault in**.
1. Zorg ervoor dat u de andere **geheime eigenschappen maakt** met de standaard waarden.
1. Selecteer **Maken**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Een Key Vault verwijzing toevoegen aan de app-configuratie

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). Selecteer **alle resources**en selecteer vervolgens de app-configuratie Store-instantie die u hebt gemaakt in de Quick Start.

1. Selecteer **Configuration Explorer**.

1. Selecteer **+** > **verwijzing naar sleutel kluis**maken en geef vervolgens de volgende waarden op:
    * **Sleutel**: Selecteer **/Application/config.keyvaultmessage**
    * **Label**: laat deze waarde leeg.
    * **Abonnement**, **resource groep**en **sleutel kluis**: Voer de waarden in die overeenkomen met de waarden in de sleutel kluis die u hebt gemaakt in de vorige sectie.
    * **Geheim**: Selecteer het geheime **bericht** dat u in de vorige sectie hebt gemaakt.

## <a name="connect-to-key-vault"></a>Verbinding maken met Key Vault

1. In deze zelf studie gebruikt u een service-principal voor verificatie om Key Vault. Als u deze service-principal wilt maken, gebruikt u de opdracht Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Met deze bewerking wordt een reeks sleutel/waarde-paren geretourneerd:

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

1. Voer de volgende opdracht uit om de Service-Principal toegang te geven tot uw sleutel kluis:

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Voer de volgende opdracht uit om de object-id op te halen en vervolgens toe te voegen aan de app-configuratie.

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Maak de volgende omgevings variabelen met behulp van de waarden voor de service-principal die in de vorige stap zijn weer gegeven:

    * **AZURE_CLIENT_ID**: *clientId*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *tenantId*

> [!NOTE]
> Deze Key Vault referenties worden alleen in uw toepassing gebruikt.  Uw toepassing verifieert rechtstreeks met Key Vault met behulp van deze referenties zonder de app-configuratie service te gebruiken.  De Key Vault biedt verificatie voor zowel uw toepassing als uw app-configuratie service zonder te delen of sleutels weer te geven.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Uw code bijwerken om een Key Vault referentie te gebruiken

1. Maak een omgevings variabele met de naam **APP_CONFIGURATION_ENDPOINT**. Stel de waarde ervan in op het eind punt voor de app-configuratie opslag. U kunt het eind punt vinden op de Blade **toegangs sleutels** in de Azure Portal.

1. Open *Boots trap. Properties* in de map *resources* . Werk dit bestand bij om het configuratie-eind punt van de app te gebruiken in plaats van een connection string.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. Open *MessageProperties. java*. Voeg een nieuwe variabele met de naam *keyVaultMessage*toe:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Open *HelloController. java*. Werk de methode *GetMessage* bij om het bericht op te halen dat is opgehaald uit Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Maak een nieuw bestand met de naam *AzureCredentials. java* en voeg de onderstaande code toe.

    ```java
    package com.example.demo;

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

1. Maak een nieuw bestand met de naam *AppConfiguration. java*. En voeg de onderstaande code toe.

    ```java
    package com.example.demo;

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

1. Maak een nieuw bestand in uw bronnen META-INF-map met de naam *lente. factoryes* en toevoegen.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Maak een Spring boot-toepassing met maven en voer deze uit, bijvoorbeeld:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Nadat uw toepassing is uitgevoerd, gebruikt u *krul* om uw toepassing te testen, bijvoorbeeld:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    U ziet het bericht dat u hebt ingevoerd in de app-configuratie opslag. U ziet ook het bericht dat u hebt ingevoerd in Key Vault.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een app-configuratie sleutel gemaakt die verwijst naar een waarde die is opgeslagen in Key Vault. Ga door naar de volgende zelf studie voor meer informatie over het gebruik van functie vlaggen in uw Java-toepassing.

> [!div class="nextstepaction"]
> [Beheerde identiteits integratie](./quickstart-feature-flag-spring-boot.md)

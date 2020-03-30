---
title: Uw eigen sleutel configureren voor het versleutelen van Azure Service Bus-gegevens in rust
description: In dit artikel vindt u informatie over het configureren van uw eigen sleutel voor het versleutelen van Azure Service Bus-gegevensrust.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: aeb9a9730ddc61793e49c9e042906457e0068d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624092"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Door de klant beheerde sleutels configureren voor het versleutelen van Azure Service Bus-gegevens in rust met behulp van de Azure-portal
Azure Service Bus Premium biedt versleuteling van gegevens in rust met Azure Storage Service Encryption (Azure SSE). Service Bus Premium vertrouwt op Azure Storage om de gegevens op te slaan en standaard worden alle gegevens die zijn opgeslagen met Azure Storage versleuteld met door Microsoft beheerde sleutels. 

## <a name="overview"></a>Overzicht
Azure Service Bus ondersteunt nu de mogelijkheid om gegevens in rust te versleutelen met door Microsoft beheerde sleutels of door de klant beheerde sleutels (Bring Your Own Key - BYOK). Met deze functie u de toegang tot de door de klant beheerde sleutels maken, roteren, uitschakelen en intrekken die worden gebruikt voor het versleutelen van Azure Service Bus in rust.

Het inschakelen van de BYOK-functie is een eenmalig installatieproces op uw naamruimte.

> [!NOTE]
> Er zijn enkele kanttekeningen bij de klant beheerde sleutel voor service side encryptie. 
>   * Deze functie wordt ondersteund door [de Azure Service Bus Premium-laag.](service-bus-premium-messaging.md) Het kan niet worden ingeschakeld voor standaardservicebusnaamruimten.
>   * De versleuteling kan alleen worden ingeschakeld voor nieuwe of lege naamruimten. Als de naamruimte gegevens bevat, mislukt de versleutelingsbewerking.

U Azure Key Vault gebruiken om uw sleutels te beheren en uw sleutelgebruik te controleren. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u de Azure Key Vault API's gebruiken om sleutels te genereren. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](../key-vault/key-vault-overview.md)

In dit artikel ziet u hoe u een sleutelkluis configureert met door de klant beheerde sleutels met behulp van de Azure-portal. Zie [Snelstart: Een geheim instellen en ophalen uit Azure Key Vault met behulp van de Azure-portal](../key-vault/quick-create-portal.md)voor meer informatie over het maken van een sleutelkluis met de Azure-portal.

> [!IMPORTANT]
> Als u door de klant beheerde sleutels gebruikt met Azure Service Bus, moet de sleutelkluis twee vereiste eigenschappen hebben geconfigureerd. Ze zijn: **Soft Delete** en **Niet zuiveren**. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutelkluis maakt in de Azure-portal. Als u deze eigenschappen echter moet inschakelen op een bestaande sleutelkluis, moet u PowerShell of Azure CLI gebruiken.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen
Voer de volgende stappen uit om door klanten beheerde sleutels in de Azure-portal in te schakelen:

1. Navigeer naar de naamruimte van Service Bus Premium.
2. Selecteer **versleuteling**op de pagina **Instellingen** van uw servicebus.
3. Selecteer de door de **klant beheerde sleutelversleuteling in rust,** zoals in de volgende afbeelding wordt weergegeven.

    ![Klantbeheersleutel inschakelen](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Een sleutelkluis instellen met sleutels

Nadat u door de klant beheerde sleutels hebt ingeschakeld, moet u de door de klant beheerde sleutel koppelen aan de naamruimte van Azure Service Bus. Service Bus ondersteunt alleen Azure Key Vault. Als u de optie **Versleuteling met door de klant beheerde sleutel** inschakelt in de vorige sectie, moet u de sleutel laten importeren in Azure Key Vault. Ook moeten de toetsen **Soft Delete** en Do **Not Purge** geconfigureerd voor de sleutel hebben. Deze instellingen kunnen worden geconfigureerd met [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) of [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Als u een nieuwe sleutelkluis wilt maken, volgt u de [Quickstart](../key-vault/key-vault-overview.md)azure key vault . Zie [Over sleutels, geheimen en certificaten voor](../key-vault/about-keys-secrets-and-certificates.md)meer informatie over het importeren van bestaande sleutels.
1. Als u zowel soft delete- als zuiveringsbeveiliging wilt inschakelen bij het maken van een kluis, gebruikt u de opdracht [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Als u zuiveringsbeveiliging wilt toevoegen aan een bestaande kluis (die al is ingeschakeld voor soft delete), gebruikt u de opdracht [az keyvault-update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Maak toetsen door de volgende stappen te volgen:
    1. Als u een nieuwe sleutel wilt maken, selecteert u **Genereren/importeren** in het menu **Sleutels** onder **Instellingen**.
        
        ![Knop Genereren/importeren selecteren](./media/configure-customer-managed-key/select-generate-import.png)

    1. Stel **Opties** in **om te genereren** en geef de sleutel een naam.

        ![Een sleutel maken](./media/configure-customer-managed-key/create-key.png) 

    1. U deze sleutel nu selecteren om te koppelen aan de naamruimte servicebus voor versleuteling in de vervolgkeuzelijst. 

        ![Toets selecteren uit sleutelkluis](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Voor redundantie u maximaal 3 toetsen toevoegen. In het geval dat een van de sleutels is verlopen of niet toegankelijk is, worden de andere sleutels gebruikt voor versleuteling.
        
    1. Vul de gegevens voor de sleutel in en klik op **Selecteren**. Dit maakt het mogelijk de versleuteling van gegevens in rust op de naamruimte met een door de klant beheerde sleutel. 


    > [!IMPORTANT]
    > Als u op zoek bent naar customer managed key te gebruiken, samen met Geo ramp herstel, bekijk dan de onderstaande - 
    >
    > Om versleuteling in rust met door de klant beheerde sleutel mogelijk te maken, wordt een [toegangsbeleid](../key-vault/key-vault-secure-your-key-vault.md) ingesteld voor de beheerde identiteit van de ServiceBus op de opgegeven Azure KeyVault. Dit zorgt voor gecontroleerde toegang tot de Azure KeyVault vanuit de naamruimte van Azure Service Bus.
    >
    > Hierdoor:
    > 
    >   * Als [Geo disaster recovery](service-bus-geo-dr.md) al is ingeschakeld voor de servicebusnaamruimte en u op zoek bent naar klantbeheersleutel, 
    >     * De koppeling verbreken
    >     * [Stel het toegangsbeleid](../key-vault/managed-identity.md) in voor de beheerde identiteit voor zowel de primaire als de secundaire naamruimten van de sleutelkluis.
    >     * Versleuteling instellen op de primaire naamruimte.
    >     * Koppel de primaire en secundaire naamruimten opnieuw.
    > 
    >   * Als u Geo-DR wilt inschakelen op een naamruimte van de ServiceBus waar de door de klant beheerde sleutel al is ingesteld,
    >     * [Stel het toegangsbeleid](../key-vault/managed-identity.md) in voor de beheerde identiteit voor de secundaire naamruimte naar de sleutelkluis.
    >     * Koppel de primaire en secundaire naamruimten.


## <a name="rotate-your-encryption-keys"></a>Uw versleutelingssleutels roteren

U uw sleutel in de sleutelkluis roteren met behulp van het rotatiemechanisme azure key vaults. Zie [Sleutelrotatie en -controle instellen voor](../key-vault/key-vault-key-rotation-log-monitoring.md)meer informatie . Activerings- en vervaldatums kunnen ook worden ingesteld om de sleutelrotatie te automatiseren. De Service Bus-service detecteert nieuwe belangrijke versies en begint ze automatisch te gebruiken.

## <a name="revoke-access-to-keys"></a>Toegang tot sleutels intrekken

Als u de toegang tot de versleutelingssleutels intrekt, worden de gegevens niet uit Service Bus verwijderd. De gegevens zijn echter niet toegankelijk via de naamruimte van de ServiceBus. U de versleutelingssleutel intrekken via het toegangsbeleid of door de sleutel te verwijderen. Meer informatie over toegangsbeleid en het beveiligen van uw sleutelkluis van [Beveiligde toegang tot een sleutelkluis.](../key-vault/key-vault-secure-your-key-vault.md)

Zodra de versleutelingssleutel is ingetrokken, wordt de Service Bus-service op de versleutelde naamruimte onbruikbaar. Als de toegang tot de sleutel is ingeschakeld of de verwijderde sleutel is hersteld, kiest servicebusservice de sleutel, zodat u toegang hebt tot de gegevens vanuit de naamruimte van de versleutelde servicebus.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Sjabloon Resourcemanager gebruiken om versleuteling in te schakelen
In deze sectie ziet u hoe u de volgende taken uitvoert met **Azure Resource Manager-sjablonen**. 

1. Maak een **premium** servicebusnaamruimte met een **beheerde service-identiteit.**
2. Maak een **sleutelkluis** en geef de serviceidentiteit toegang tot de sleutelkluis. 
3. Werk de naamruimte van de servicebus bij met de sleutelkluisgegevens (sleutel/waarde). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Een premium servicebus-naamruimte maken met een beheerde service-identiteit
In deze sectie ziet u hoe u een Azure Service Bus-naamruimte met beheerde service-identiteit maakt met behulp van een Azure Resource Manager-sjabloon en PowerShell. 

1. Maak een Azure Resource Manager-sjabloon om een naamruimte voor servicebuspremiumlagen te maken met een beheerde service-identiteit. Geef het bestand een naam: **CreateServiceBusPremiumNamespace.json:** 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Maak een sjabloonparameterbestand met de **naam: CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<ServiceBusNamespaceName>`- Naam van de naam van uw servicebusnaamruimte
    > - `<Location>`- Locatie van de naamruimte van uw servicebus

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Voer de volgende PowerShell-opdracht uit om de sjabloon te implementeren om een premium servicebusnaamruimte te maken. Haal vervolgens de id van de naamruimte van de servicebus op om deze later te gebruiken. Vervang `{MyRG}` de naam van de resourcegroep voordat u de opdracht uitvoert.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Naamruimte-identiteitstoegang voor ServiceBus verlenen tot sleutelkluis

1. Voer de volgende opdracht uit om een sleutelkluis te maken met **zuiveringsbeveiliging** en **soft-delete** ingeschakeld. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (OF)
    
    Voer de volgende opdracht uit om een **bestaande sleutelkluis bij**te werken. Geef waarden op voor resourcegroep- en sleutelkluisnamen voordat u de opdracht uitvoert. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Stel het toegangsbeleid voor de sleutelkluis in, zodat de beheerde identiteit van de naamruimte van de ServiceBus toegang heeft tot de sleutelwaarde in de sleutelkluis. Gebruik de id van de naamruimte servicebus uit de vorige sectie. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Gegevens versleutelen in naamruimte van ServiceBus met door de klant beheerde sleutel uit sleutelkluis
U hebt tot nu toe de volgende stappen gezet: 

1. Een premium naamruimte gemaakt met een beheerde identiteit.
2. Maak een sleutelkluis en verleende de beheerde identiteit toegang tot de sleutelkluis. 

In deze stap werkt u de naamruimte van de ServiceBus bij met belangrijke kluisinformatie. 

1. Maak een JSON-bestand met de naam **UpdateServiceBusNamespaceWithEncryption.json** met de volgende inhoud: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Een sjabloonparameterbestand maken: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<ServiceBusNamespaceName>`- Naam van de naam van uw servicebusnaamruimte
    > - `<Location>`- Locatie van de naamruimte van uw servicebus
    > - `<KeyVaultName>`- Naam van uw sleutelkluis
    > - `<KeyName>`- Naam van de sleutel in de sleutelkluis  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Voer de volgende PowerShell-opdracht uit om de sjabloon Resourcebeheer te implementeren. Vervang `{MyRG}` de naam van uw resourcegroep voordat u de opdracht uitvoert. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:
- [Overzicht servicebus](service-bus-messaging-overview.md)
- [Overzicht van Key Vault](../key-vault/key-vault-overview.md)



---
title: Uw eigen sleutel configureren voor het versleutelen van Azure Event Hubs-gegevens in rust
description: In dit artikel vindt u informatie over het configureren van uw eigen sleutel voor het versleutelen van Azure Event Hubs-gegevensrust.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: f515d3ad832db7f78f98111ab67628a2874033ff
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459131"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Door de klant beheerde sleutels configureren voor het in rust versleutelen van Azure Event Hubs-gegevens met behulp van de Azure-portal
Azure Event Hubs biedt versleuteling van gegevens in rust met Azure Storage Service Encryption (Azure SSE). Event Hubs is afhankelijk van Azure Storage om de gegevens op te slaan en standaard worden alle gegevens die zijn opgeslagen met Azure Storage versleuteld met door Microsoft beheerde sleutels. 

## <a name="overview"></a>Overzicht
Azure Event Hubs ondersteunt nu de mogelijkheid om gegevens in rust te versleutelen met door Microsoft beheerde sleutels of door de klant beheerde sleutels (Bring Your Own Key – BYOK). Met deze functie u de toegang tot de door de klant beheerde sleutels maken, roteren, uitschakelen en intrekken die worden gebruikt voor het versleutelen van Azure Event Hubs-gegevens in rust.

Het inschakelen van de BYOK-functie is een eenmalig installatieproces op uw naamruimte.

> [!NOTE]
> De BYOK-mogelijkheid wordt ondersteund door eventhubs voor specifieke clusters [met één tenant.](event-hubs-dedicated-overview.md) Het kan niet worden ingeschakeld voor standaard naamruimten van Event Hubs.

U Azure Key Vault gebruiken om uw sleutels te beheren en uw sleutelgebruik te controleren. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u de Azure Key Vault API's gebruiken om sleutels te genereren. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](../key-vault/general/overview.md)

In dit artikel ziet u hoe u een sleutelkluis configureert met door de klant beheerde sleutels met behulp van de Azure-portal. Zie [Snelstart: Een geheim instellen en ophalen uit Azure Key Vault met behulp van de Azure-portal](../key-vault/secrets/quick-create-portal.md)voor meer informatie over het maken van een sleutelkluis met de Azure-portal.

> [!IMPORTANT]
> Als u door de klant beheerde sleutels gebruikt met Azure Event Hubs, moet de sleutelkluis twee vereiste eigenschappen hebben geconfigureerd. Ze zijn: **Soft Delete** en **Niet zuiveren**. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutelkluis maakt in de Azure-portal. Als u deze eigenschappen echter moet inschakelen op een bestaande sleutelkluis, moet u PowerShell of Azure CLI gebruiken.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen
Voer de volgende stappen uit om door klanten beheerde sleutels in de Azure-portal in te schakelen:

1. Navigeer naar het dedicated cluster van gebeurtenishubs.
1. Selecteer de naamruimte waarop u BYOK wilt inschakelen.
1. Selecteer **versleuteling**op de pagina **Instellingen** van de naamruimte van uw gebeurtenishubs . 
1. Selecteer de door de **klant beheerde sleutelversleuteling in rust,** zoals in de volgende afbeelding wordt weergegeven. 

    ![Klantbeheersleutel inschakelen](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Een sleutelkluis instellen met sleutels
Nadat u door de klant beheerde sleutels hebt ingeschakeld, moet u de door de klant beheerde sleutel koppelen aan de naamruimte van Azure Event Hubs. Event Hubs ondersteunt alleen Azure Key Vault. Als u de optie **Versleuteling met door de klant beheerde sleutel** inschakelt in de vorige sectie, moet u de sleutel laten importeren in Azure Key Vault. Ook moeten de toetsen **Soft Delete** en Do **Not Purge** geconfigureerd voor de sleutel hebben. Deze instellingen kunnen worden geconfigureerd met [PowerShell](../key-vault/general/soft-delete-powershell.md) of [CLI](../key-vault/general/soft-delete-cli.md#enabling-purge-protection).

1. Als u een nieuwe sleutelkluis wilt maken, volgt u de [Quickstart](../key-vault/general/overview.md)azure key vault . Zie [Over sleutels, geheimen en certificaten voor](../key-vault/about-keys-secrets-and-certificates.md)meer informatie over het importeren van bestaande sleutels.
1. Als u zowel soft delete- als zuiveringsbeveiliging wilt inschakelen bij het maken van een kluis, gebruikt u de opdracht [az keyvault create.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Als u zuiveringsbeveiliging wilt toevoegen aan een bestaande kluis (die al is ingeschakeld voor soft delete), gebruikt u de opdracht [az keyvault-update.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Maak toetsen door de volgende stappen te volgen:
    1. Als u een nieuwe sleutel wilt maken, selecteert u **Genereren/importeren** in het menu **Sleutels** onder **Instellingen**.
        
        ![Knop Genereren/importeren selecteren](./media/configure-customer-managed-key/select-generate-import.png)
    1. Stel **Opties** in **om te genereren** en geef de sleutel een naam.

        ![Een sleutel maken](./media/configure-customer-managed-key/create-key.png) 
    1. U deze sleutel nu selecteren om te koppelen aan de naamruimte van gebeurtenishubs voor versleuteling in de vervolgkeuzelijst. 

        ![Toets selecteren uit sleutelkluis](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Vul de gegevens voor de sleutel in en klik op **Selecteren**. Dit maakt het mogelijk de versleuteling van gegevens in rust op de naamruimte met een door de klant beheerde sleutel. 


## <a name="rotate-your-encryption-keys"></a>Uw versleutelingssleutels roteren
U uw sleutel in de sleutelkluis roteren met behulp van het rotatiemechanisme azure key vaults. Zie [Sleutelrotatie en -controle instellen voor](../key-vault/secrets/key-rotation-log-monitoring.md)meer informatie . Activerings- en vervaldatums kunnen ook worden ingesteld om de sleutelrotatie te automatiseren. De Event Hubs-service detecteert nieuwe belangrijke versies en begint ze automatisch te gebruiken.

## <a name="revoke-access-to-keys"></a>Toegang tot sleutels intrekken
Als u de toegang tot de versleutelingssleutels intrekt, worden de gegevens niet uit gebeurtenishubs verwijderd. De gegevens zijn echter niet toegankelijk via de naamruimte van gebeurtenishubs. U de versleutelingssleutel intrekken via het toegangsbeleid of door de sleutel te verwijderen. Meer informatie over toegangsbeleid en het beveiligen van uw sleutelkluis van [Beveiligde toegang tot een sleutelkluis.](../key-vault/general/secure-your-key-vault.md)

Zodra de versleutelingssleutel is ingetrokken, wordt de Gebeurtenishubs-service op de versleutelde naamruimte onbruikbaar. Als de toegang tot de sleutel is ingeschakeld of de verwijdersleutel is hersteld, kiest de gebeurtenishubs-service de sleutel, zodat u toegang hebt tot de gegevens vanuit de naamruimte van versleutelde gebeurtenishubs.

## <a name="set-up-diagnostic-logs"></a>Diagnostische logboeken instellen 
Het instellen van diagnostische logboeken voor door BYOK ingeschakelde naamruimten geeft u de vereiste informatie over de bewerkingen wanneer een naamruimte wordt versleuteld met door de klant beheerde sleutels. Deze logboeken kunnen worden ingeschakeld en later worden gestreamd naar een gebeurtenishub of worden geanalyseerd via loganalyses of naar opslag worden gestreamd om aangepaste analyses uit te voeren. Zie Overzicht van Azure [Diagnostic-logboeken](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over diagnostische logboeken .

## <a name="enable-user-logs"></a>Gebruikerslogboeken inschakelen
Volg deze stappen om logboeken in te schakelen voor door de klant beheerde sleutels.

1. Navigeer in de Azure-portal naar de naamruimte waarmee BYOK is ingeschakeld.
1. Selecteer **Diagnostische instellingen** onder **Controleren**.

    ![Diagnostische instellingen selecteren](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selecteer **+Diagnostische instelling toevoegen**. 

    ![Diagnostische instelling toevoegen selecteren](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Geef een **naam** op en selecteer waar u de logboeken naartoe wilt streamen.
1. Selecteer **CustomerManagedKeyUserLogs** en **Sla op**. Met deze actie kunnen de logboeken voor BYOK op de naamruimte worden ingeschakeld.

    ![De optie Sleutellogboeken met klantbeheer selecteren](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Logboekschema 
Alle logboeken worden opgeslagen in de JSON-indeling (JavaScript Object Notation). Elk item heeft tekenreeksvelden die de notatie gebruiken die in de volgende tabel is beschreven. 

| Naam | Beschrijving |
| ---- | ----------- | 
| Taaknaam | Beschrijving van de taak die is mislukt. |
| ActivityId (ActivityId) | Interne ID die wordt gebruikt voor het bijhouden. |
| category | Hiermee definieert u de classificatie van de taak. Als de sleutel van uw sleutelkluis bijvoorbeeld wordt uitgeschakeld, is deze een informatiecategorie of als een sleutel niet kan worden uitgepakt, kan deze onder fouten vallen. |
| resourceId | Azure Resource Manager-bron-id |
| keyVault | Volledige naam van de sleutelkluis. |
| sleutel | De sleutelnaam die wordt gebruikt om de naamruimte van de gebeurtenishubs te versleutelen. |
| versie | De versie van de sleutel die wordt gebruikt. |
| Bewerking | De bewerking die wordt uitgevoerd op de sleutel in uw sleutelkluis. Schakel bijvoorbeeld de sleutel, wrap of uitpakpunt in |
| code | De code die aan de bewerking is gekoppeld. Voorbeeld: Foutcode, 404 betekent dat de sleutel niet is gevonden. |
| message | Een foutbericht dat is gekoppeld aan de bewerking |

Hier is een voorbeeld van het logboek voor een door de klant beheerde sleutel:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Sjabloon Resourcemanager gebruiken om versleuteling in te schakelen
In deze sectie ziet u hoe u de volgende taken uitvoert met **Azure Resource Manager-sjablonen**. 

1. Maak een **naamruimte voor gebeurtenishubs** met een beheerde service-identiteit.
2. Maak een **sleutelkluis** en geef de serviceidentiteit toegang tot de sleutelkluis. 
3. Werk de naamruimte van gebeurtenishubs bij met de sleutelkluisgegevens (sleutel/waarde). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Een cluster en naamruimte van gebeurtenishubs maken met een beheerde service-identiteit
In deze sectie ziet u hoe u een naamruimte van Azure Event Hubs met beheerde service-identiteit maakt met behulp van een Azure Resource Manager-sjabloon en PowerShell. 

1. Maak een Azure Resource Manager-sjabloon om een naamruimte voor gebeurtenishubs te maken met een beheerde service-identiteit. Geef het bestand een naam: **CreateEventHubClusterAndNamespace.json:** 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Maak een sjabloonparameterbestand met de **naam: CreateEventHubClusterAndNameSpaceParams.json**. 

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<EventHubsClusterName>`- Naam van uw cluster eventhubs    
    > - `<EventHubsNamespaceName>`- Naam van de naamruimte van je eventhubs
    > - `<Location>`- Locatie van de naamruimte van je gebeurtenishubs

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Voer de volgende PowerShell-opdracht uit om de sjabloon te implementeren om een naamruimte voor gebeurtenishubs te maken. Haal vervolgens de id van de naamruimte van de gebeurtenishubs op om deze later te gebruiken. Vervang `{MyRG}` de naam van de resourcegroep voordat u de opdracht uitvoert.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>De naamruimte-identiteitstoegang van gebeurtenishubs verlenen tot sleutelkluis

1. Voer de volgende opdracht uit om een sleutelkluis te maken met **zuiveringsbeveiliging** en **soft-delete** ingeschakeld. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (OF)    
    
    Voer de volgende opdracht uit om een **bestaande sleutelkluis bij**te werken. Geef waarden op voor resourcegroep- en sleutelkluisnamen voordat u de opdracht uitvoert. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Stel het toegangsbeleid voor de sleutelkluis in, zodat de beheerde identiteit van de naamruimte van gebeurtenishubs toegang heeft tot de sleutelwaarde in de sleutelkluis. Gebruik de id van de naamruimte van gebeurtenishubs uit de vorige sectie. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Gegevens versleutelen in naamruimte van Gebeurtenishubs met door de klant beheerde sleutel uit sleutelkluis
U hebt tot nu toe de volgende stappen gezet: 

1. Een premium naamruimte gemaakt met een beheerde identiteit.
2. Maak een sleutelkluis en verleende de beheerde identiteit toegang tot de sleutelkluis. 

In deze stap werk je de naamruimte van Event Hubs bij met belangrijke kluisinformatie. 

1. Maak een JSON-bestand met de naam **CreateEventHubClusterAndNamespace.json** met de volgende inhoud: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Een sjabloonparameterbestand maken: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Vervang de volgende waarden: 
    > - `<EventHubsClusterName>`- Naam van uw cluster eventhubs.        
    > - `<EventHubsNamespaceName>`- Naam van de naamruimte van je eventhubs
    > - `<Location>`- Locatie van de naamruimte van je gebeurtenishubs
    > - `<KeyVaultName>`- Naam van uw sleutelkluis
    > - `<KeyName>`- Naam van de sleutel in de sleutelkluis

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Problemen oplossen
Als aanbevolen praktijk schakelt u altijd logboeken in zoals in de vorige sectie. Het helpt bij het bijhouden van de activiteiten wanneer BYOK-versleuteling is ingeschakeld. Het helpt ook bij het opsporen van de problemen.

Hieronder volgen de veelvoorkomende foutencodes om naar te zoeken wanneer BYOK-versleuteling is ingeschakeld.

| Actie | Foutcode | Resulterende status van gegevens |
| ------ | ---------- | ----------------------- | 
| De machtiging voor uitpakken/uitpakken uit een sleutelkluis verwijderen | 403 |    Ontoegankelijk |
| Aad-rollidmaatschap verwijderen van een AAD-principal die de wrap/uitpaktoestemming heeft verleend | 403 |  Ontoegankelijk |
| Een versleutelingssleutel uit de sleutelkluis verwijderen | 404 | Ontoegankelijk |
| De sleutelkluis verwijderen | 404 | Ontoegankelijk (veronderstelt dat soft-delete is ingeschakeld, wat een vereiste instelling is.) |
| De vervaldatum op de versleutelingssleutel zodanig wijzigen dat deze al is verlopen | 403 |   Ontoegankelijk  |
| Het wijzigen van de NBF (niet eerder) zodanig dat de sleutelversleutelingssleutel niet actief is | 403 | Ontoegankelijk  |
| Het selecteren van de optie **MSFT-services toestaan** voor de firewall van de sleutelkluis of anderszins de toegang tot het netwerk blokkeren tot de sleutelkluis met de versleutelingssleutel | 403 | Ontoegankelijk |
| De sleutelkluis verplaatsen naar een andere tenant | 404 | Ontoegankelijk |  
| Intermitterend netwerkprobleem of DNS/AAD/MSI-storing |  | Toegankelijk met behulp van gegevensversleutelingssleutel in de cache |

> [!IMPORTANT]
> Als u Geo-DR wilt inschakelen op een naamruimte die de BYOK-versleuteling gebruikt, moet de secundaire naamruimte voor het koppelen zich in een speciaal cluster bevinden en moet een systeem toegewezen beheerde identiteit zijn ingeschakeld. Zie [Beheerde identiteiten voor Azure Resources voor](../active-directory/managed-identities-azure-resources/overview.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:
- [Overzicht van gebeurtenishubs](event-hubs-about.md)
- [Overzicht van Key Vault](../key-vault/general/overview.md)





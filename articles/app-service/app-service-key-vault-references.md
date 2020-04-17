---
title: Key Vault-referenties gebruiken
description: Meer informatie over het instellen van Azure App Service en Azure-functies voor het gebruik van Azure Key Vault-referenties. Maak Key Vault-geheimen beschikbaar voor uw toepassingscode.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: dd0a03ea76d517486bb9bda6d9628fb529166dd8
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453724"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Key Vault-verwijzingen gebruiken voor App Service en Azure-functies

In dit onderwerp ziet u hoe u werken met geheimen van Azure Key Vault in uw App Service- of Azure-functietoepassing zonder dat er codewijzigingen nodig zijn. [Azure Key Vault](../key-vault/general/overview.md) is een service die gecentraliseerd geheimenbeheer biedt, met volledige controle over toegangsbeleid en controlegeschiedenis.

## <a name="granting-your-app-access-to-key-vault"></a>Uw app toegang verlenen tot Key Vault

Om geheimen uit Key Vault te lezen, moet u een kluis hebben gemaakt en uw app toestemming geven om toegang te krijgen.

1. Maak een sleutelkluis door de [sleutelkluis snel te volgen.](../key-vault/secrets/quick-create-cli.md)

1. Maak een [door het systeem toegewezen beheerde identiteit](overview-managed-identity.md) voor uw toepassing.

   > [!NOTE] 
   > Key Vault-referenties ondersteunen momenteel alleen door het systeem toegewezen beheerde identiteiten. Door de gebruiker toegewezen identiteiten kunnen niet worden gebruikt.

1. Maak een [toegangsbeleid in Key Vault](../key-vault/general/secure-your-key-vault.md#key-vault-access-policies) voor de toepassingsidentiteit die u eerder hebt gemaakt. Schakel de geheime machtiging 'Krijgen' in voor dit beleid. Configureer de "geautoriseerde toepassing" `applicationId` of instellingen niet, omdat dit niet compatibel is met een beheerde identiteit.

    > [!NOTE]
    > Key Vault-referenties zijn momenteel niet in staat om geheimen op te lossen die zijn opgeslagen in een sleutelkluis met [netwerkbeperkingen.](../key-vault/general/overview-vnet-service-endpoints.md)

## <a name="reference-syntax"></a>Referentiesyntaxis

Een Key Vault-referentie `@Microsoft.KeyVault({referenceString})`is `{referenceString}` van het formulier, waarbij een van de volgende opties wordt vervangen:

> [!div class="mx-tdBreakAll"]
> | Referentietekenreeks                                                            | Beschrijving                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri =_secretUri_                                                       | De **SecretUri** moet de volledige data-plane URI van een geheim in Key Vault, met inbegrip van een versie, bijvoorbeeld,https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931  |
> | VaultName=_vaultName_; SecretName=_secretName_; SecretVersion=_secretVersion_ | De **VaultName** moet de naam van uw Key Vault-bron zijn. De **SecretName** moet de naam van het doelgeheim zijn. De **SecretVersion** moet de versie van het geheim te gebruiken. |

Een volledige verwijzing met Versie ziet er bijvoorbeeld als volgt uit:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
U kunt ook het volgende doen:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Brontoepassingsinstellingen van Key Vault

Key Vault-referenties kunnen worden gebruikt als waarden voor [toepassingsinstellingen,](configure-common.md#configure-app-settings)zodat u geheimen bewaren in Key Vault in plaats van de siteconfig. Toepassingsinstellingen zijn veilig versleuteld in rust, maar als u geheime beheermogelijkheden nodig hebt, moeten ze naar Key Vault gaan.

Als u een Key Vault-verwijzing wilt gebruiken voor een toepassingsinstelling, stelt u de verwijzing in als de waarde van de instelling. Uw app kan het geheim via de sleutel als normaal verwijzen. Er zijn geen codewijzigingen vereist.

> [!TIP]
> De meeste toepassingsinstellingen met Key Vault-referenties moeten worden gemarkeerd als sleufinstellingen, omdat u voor elke omgeving afzonderlijke kluizen moet hebben.

### <a name="azure-resource-manager-deployment"></a>Implementatie van Azure Resource Manager

Wanneer u resourceimplementaties automatiseert via Azure Resource Manager-sjablonen, moet u mogelijk uw afhankelijkheden in een bepaalde volgorde in een bepaalde volgorde plaatsen om deze functie te laten werken. Van belang, moet u uw toepassingsinstellingen als hun eigen `siteConfig` bron, eerder dan gebruikend een eigenschap in de plaatsdefinitie bepalen. Dit komt omdat de site eerst moet worden gedefinieerd, zodat de door het systeem toegewezen identiteit ermee wordt gemaakt en kan worden gebruikt in het toegangsbeleid.

Een voorbeeld psuedo-sjabloon voor een functie-app kan er als volgt uitzien:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> In dit voorbeeld is de implementatie van het bronbeheer afhankelijk van de toepassingsinstellingen. Dit is normaal gesproken onveilig gedrag, omdat de update van de app-instelling zich asynchroon gedraagt. Omdat we echter de `WEBSITE_ENABLE_SYNC_UPDATE_SITE` toepassingsinstelling hebben opgenomen, is de update synchroon. Dit betekent dat de implementatie van het bronbeheer pas begint nadat de toepassingsinstellingen volledig zijn bijgewerkt.

## <a name="troubleshooting-key-vault-references"></a>Problemen met key vault-referenties oplossen

Als een verwijzing niet goed is opgelost, wordt de referentiewaarde in plaats daarvan gebruikt. Dit betekent dat voor toepassingsinstellingen een omgevingsvariabele wordt `@Microsoft.KeyVault(...)` gemaakt waarvan de waarde de syntaxis heeft. Dit kan ertoe leiden dat de toepassing om fouten te gooien, omdat het verwachtte een geheim van een bepaalde structuur.

Meestal is dit te wijten aan een verkeerde configuratie van het [toegangsbeleid voor key vault.](#granting-your-app-access-to-key-vault) Het kan echter ook te wijten zijn aan een geheim dat niet meer bestaat of een syntaxisfout in de verwijzing zelf.

Als de syntaxis juist is, u andere oorzaken voor fouten weergeven door de huidige resolutiestatus in de portal te controleren. Navigeer naar toepassingsinstellingen en selecteer 'Bewerken' voor de verwijzing in kwestie. Onder de instellingsconfiguratie ziet u statusgegevens, inclusief eventuele fouten. Het ontbreken hiervan houdt in dat de syntaxis ongeldig is.

U ook gebruik maken van een van de ingebouwde detectoren om meer informatie te krijgen.

### <a name="using-the-detector-for-app-service"></a>De detector gebruiken voor App-service

1. Navigeer in de portal naar uw app.
2. Selecteer **Problemen vaststellen en oplossen**.
3. Kies **Beschikbaarheid en prestaties** en selecteer **Web-app omlaag.**
4. **Diagnostatie van key vault-toepassingsinstellingen** zoeken en klik op **Meer info**.


### <a name="using-the-detector-for-azure-functions"></a>De detector gebruiken voor Azure-functies

1. Navigeer in de portal naar uw app.
2. Navigeer naar **platformfuncties.**
3. Selecteer **Problemen vaststellen en oplossen**.
4. Kies **Beschikbaarheid en prestaties** en selecteer De app Functie omlaag of **rapportagefouten.**
5. Klik op **Key Vault Application Settings Diagnostics.**

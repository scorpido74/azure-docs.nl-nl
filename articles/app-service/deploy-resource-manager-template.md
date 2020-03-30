---
title: Apps implementeren met sjablonen
description: Richtlijnen voor het maken van Azure Resource Manager-sjablonen voor het inrichten en implementeren van App Service-apps.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: dfdfa9f69e00aa644c21fc96cb70e9fa460ca0c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77211702"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Richtlijnen voor het implementeren van webapps met Azure Resource Manager-sjablonen

In dit artikel vindt u aanbevelingen voor het maken van Azure Resource Manager-sjablonen voor het implementeren van Azure App Service-oplossingen. Deze aanbevelingen kunnen u helpen veelvoorkomende problemen te voorkomen.

## <a name="define-dependencies"></a>Afhankelijkheden definiëren

Het definiëren van afhankelijkheden voor web-apps vereist inzicht in hoe de bronnen in een web-app met elkaar omgaan. Als u afhankelijkheden opgeeft in een onjuiste volgorde, u implementatiefouten veroorzaken of een racevoorwaarde maken die de implementatie blokkeert.

> [!WARNING]
> Als u een MSDeploy-siteextensie in uw sjabloon opneemt, moet u alle configuratiebronnen zo ingesteld dat deze afhankelijk is van de MSDeploy-bron. Configuratiewijzigingen zorgen ervoor dat de site opnieuw wordt opgestart. Door de configuratiebronnen afhankelijk te maken van MSDeploy, zorgt u ervoor dat MSDeploy klaar is voordat de site opnieuw wordt opgestart. Zonder deze afhankelijkheden kan de site opnieuw worden opgestart tijdens het implementatieproces van MSDeploy. Zie [WordPress-sjabloon met afhankelijkheid van webdeploy voor](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)een voorbeeldsjabloon .

In de volgende afbeelding wordt de afhankelijkheidsvolgorde voor verschillende App Service-bronnen weergegeven:

![Afhankelijkheden van webapps](media/web-sites-rm-template-guidance/web-dependencies.png)

U implementeert resources in de volgende volgorde:

**Tier 1**
* App Service-abonnement.
* Andere gerelateerde bronnen, zoals databases of opslagaccounts.

**Tier 2**
* Web-app- is afhankelijk van het App Service-abonnement.
* Het exemplaar Azure Application Insights dat zich richt op de serverfarm, is afhankelijk van het App Service-abonnement.

**Laag 3**
* Bronbeheer - is afhankelijk van de web-app.
* MSDeploy-siteextensie is afhankelijk van de web-app.
* Het exemplaar Azure Application Insights dat zich richt op de web-app, is afhankelijk van de web-app.

**Laag 4**
* App Service-certificaat- is afhankelijk van bronbeheer of MSDeploy als een van beide aanwezig is. Anders hangt het af van de web-app.
* Configuratie-instellingen (verbindingstekenreeksen, web.config-waarden, app-instellingen)--afhankelijk van bronbeheer of MSDeploy als een van beide aanwezig is. Anders hangt het af van de web-app.

**Laag 5**
* Host naam bindingen - is afhankelijk van het certificaat indien aanwezig. Anders is het afhankelijk van een bron op een hoger niveau.
* Site-extensies - afhankelijk van configuratie-instellingen indien aanwezig. Anders is het afhankelijk van een bron op een hoger niveau.

Uw oplossing bevat doorgaans slechts enkele van deze bronnen en lagen. Voor ontbrekende lagen u lagere resources toewijzen aan de volgende hogere laag.

In het volgende voorbeeld wordt een deel van een sjabloon weergegeven. De waarde van de configuratie van de verbindingstekenreeks is afhankelijk van de MSDeploy-extensie. De MSDeploy-extensie is afhankelijk van de web-app en -database. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

Zie [Sjabloon: Bouw een eenvoudige Umbraco Web App](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)voor een kant-en-klaar voorbeeld dat de bovenstaande code gebruikt.

## <a name="find-information-about-msdeploy-errors"></a>Informatie vinden over MSDeploy-fouten

Als uw resourcemanagersjabloon MSDeploy gebruikt, kunnen de foutmeldingen voor implementatie moeilijk te begrijpen zijn. Als u meer informatie wilt krijgen na een mislukte implementatie, probeert u de volgende stappen:

1. Ga naar de [Kudu-console](https://github.com/projectkudu/kudu/wiki/Kudu-console)van de site.
2. Blader naar de map op D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Zoek naar de bestanden appManagerStatus.xml en appManagerLog.xml. Het eerste bestand registreert de status. In het tweede bestand worden gegevens over de fout opgeslagen. Als de fout niet duidelijk is voor u, u deze opnemen wanneer u om hulp vraagt op het [forum.](https://docs.microsoft.com/answers/topics/azure-webapps.html)

## <a name="choose-a-unique-web-app-name"></a>Een unieke naam van de web-app kiezen

De naam voor uw web-app moet wereldwijd uniek zijn. U een naamgevingsconventie gebruiken die waarschijnlijk uniek is, of u de [unieke String-functie](../azure-resource-manager/templates/template-functions-string.md#uniquestring) gebruiken om te helpen bij het genereren van een unieke naam.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Web-app-certificaat implementeren vanuit Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als uw sjabloon een [Microsoft.Web/certificaten-bron](/azure/templates/microsoft.web/certificates) voor SSL-binding bevat en het certificaat is opgeslagen in een Sleutelkluis, moet u ervoor zorgen dat de app-service-identiteit toegang heeft tot het certificaat.

In globale Azure heeft de serviceprincipal van App Service de ID van **abfa0a7c-a6b6-4736-8310-5855508787cd**. Gebruik het als belangrijkste toegang tot Key Vault voor de hoofdsom van de App-serviceservice:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

In Azure Government heeft de serviceprincipal van App Service de ID van **6a02c803-dafd-4136-b4c3-5a6f318b4714**. Gebruik die ID in het voorgaande voorbeeld.

Selecteer in uw sleutelkluis **certificaten** en **Genereer/importeer om** het certificaat te uploaden.

![Certificaat importeren](media/web-sites-rm-template-guidance/import-certificate.png)

Geef in uw sjabloon de naam `keyVaultSecretName`op van het certificaat voor de .

Zie [Een web-app-certificaat uit Key Vault-geheim implementeren en gebruiken voor het maken van SSL-binding en voor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)een voorbeeldsjabloon.

## <a name="next-steps"></a>Volgende stappen

* Zie [Microservices inrichten en implementeren voorspelbaar in Azure](deploy-complex-application-predictably.md)voor een zelfstudie over het implementeren van webapps met een sjabloon.
* Zie [Sjabloonverwijzing](/azure/templates/)azure resource manager voor meer informatie over syntaxis en eigenschappen van JSON voor resourcetypen in sjablonen.

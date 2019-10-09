---
title: Visual Studio Azure-resource groep-projecten maken & implementeren
description: Gebruik Visual Studio om een Azure-resourcegroepproject te maken en de resources in Azure te implementeren.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: tomfitz
ms.openlocfilehash: c7f71dbadf24244756cf65f68ceea24547190737
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170244"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Azure-resourcegroepen maken en implementeren met Visual Studio

Met Visual Studio kunt u een project maken waarmee uw infrastructuur en code in Azure worden geïmplementeerd. U kunt bijvoorbeeld de web-host, de website en de code voor de website implementeren. Visual Studio biedt veel verschillende startsjablonen om te implementeren in algemene scenario's. In dit artikel implementeert u een web-app.

In dit artikel wordt beschreven hoe u [Visual Studio 2019 of hoger gebruikt met de Azure Development-en ASP.net-workloads die zijn geïnstalleerd](/visualstudio/install/install-visual-studio?view=vs-2019). Als u Visual Studio 2017 gebruikt, is uw ervaring grotendeels hetzelfde.

## <a name="create-azure-resource-group-project"></a>Een Azure-resourcegroepproject maken

In deze sectie maakt u een Azure-resource groep-project met een **Web-app** -sjabloon.

1. Kies in Visual Studio **bestand**, **Nieuw**en **project**. Selecteer de project sjabloon **Azure-resource groep** en **volgende**.

    ![Project maken](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Geef uw project een naam. De andere standaard instellingen zijn waarschijnlijk verfijnd, maar bekijken ze voor uw omgeving. Wanneer u klaar bent, selecteert u **Maken**.

    ![Project maken](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-project.png)

1. Kies het sjabloon dat u wilt implementeren in Azure Resource Manager. Er zijn diverse opties beschikbaar op basis van het type project dat u wilt implementeren. Kies voor dit artikel de sjabloon **Web-app** en klik vervolgens op **OK**.

    ![Een sjabloon selecteren](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    Het sjabloon dat u kiest is slechts een beginpunt; u kunt resources toevoegen en verwijderen op basis van uw scenario.

1. Visual Studio maakt een implementatie project voor de resource groep voor de web-app. Als u de bestanden voor uw project wilt zien, bekijkt u het knoop punt in het implementatie project.

    ![Knoop punten weer geven](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Omdat u de sjabloon web-app hebt gekozen, ziet u de volgende bestanden:

   | Bestandsnaam | Description |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Een PowerShell-script waarmee PowerShell-opdrachten worden uitgevoerd om te worden geïmplementeerd in Azure Resource Manager. Visual Studio gebruikt dit Power shell-script voor het implementeren van uw sjabloon. |
   | WebSite.json |Het Resource Manager-sjabloon dat de infrastructuur definieert die u in Azure wilt implementeren en de parameters die u kunt opgeven tijdens de implementatie. Hiermee worden ook de afhankelijkheden tussen resources gedefinieerd zodat deze in de juiste volgorde worden geïmplementeerd. |
   | WebSite.parameters.json |Een parameterbestand dat de waarden bevat die nodig zijn voor de sjabloon. U geeft parameterwaarden door om elke implementatie aan te passen. |

    Alle implementatieprojecten voor resourcegroepen bevatten deze algemene bestanden. Andere projecten bevatten mogelijk extra bestanden ter ondersteuning van andere functies.

## <a name="customize-resource-manager-template"></a>Resource Manager-sjabloon aanpassen

U kunt een implementatie project aanpassen door de Resource Manager-sjabloon te wijzigen waarin de resources worden beschreven die u wilt implementeren. Zie [Azure Resource Manager-sjablonen samenstellen](resource-group-authoring-templates.md) voor meer informatie over de onderdelen van een Resource Manager-sjabloon.

1. Als u wilt werken met uw sjabloon, opent u **website. json**.

1. De Visual Studio-editor biedt hulpprogramma's voor het bewerken van het Resource Manager-sjabloon. In het scherm **JSON-overzicht** ziet u eenvoudig welke elementen zijn gedefinieerd in het sjabloon.

   ![JSON-overzicht weer geven](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

1. Selecteer een element in het overzicht om naar dat deel van de sjabloon te gaan.

   ![Naar JSON navigeren](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

1. U kunt een nieuwe resource toevoegen aan een sjabloon door op de knop **Resource toevoegen** te klikken boven aan het venster JSON Outline of door met de rechtermuisknop op **Resources** te klikken en **Nieuwe resource toevoegen** te selecteren.

   ![Resource toevoegen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

1. Selecteer een **opslag account** en geef deze een naam. Geef een naam op die niet meer dan elf tekens (alleen cijfers en kleine letters) omvat.

   ![Opslag toevoegen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

1. U ziet dat niet alleen de resource is toegevoegd, maar ook een parameter voor het type opslagaccount en een variabele voor de naam van het opslagaccount.

   ![Overzicht weer geven](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

1. De para meter voor het type opslag account is vooraf gedefinieerd met toegestane typen en een standaard type. U kunt deze waarden laten staan of ze bewerken voor uw scenario. Als u niemand toestemming wilt geven om een **Premium_LRS**-opslagaccount te implementeren via deze sjabloon, verwijdert u de sjabloon uit de toegestane typen.

   ```json
   "demoaccountType": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_ZRS",
       "Standard_GRS",
       "Standard_RAGRS"
     ]
   }
   ```

1. Visual Studio biedt ook IntelliSense om u inzicht te geven in welke eigenschappen beschikbaar zijn bij het bewerken van de sjabloon. Als u bijvoorbeeld de eigenschappen bewerkt van uw App Service-plan, navigeert u naar de resource **HostingPlan** en voegt u een nieuwe waarde toe voor de **eigenschappen**. IntelliSense geeft de beschikbare waarden weer en biedt een beschrijving van die waarden.

   ![IntelliSense weer geven](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

   U kunt **numberOfWorkers** instellen op 1 en het bestand opslaan.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Open het bestand **website. para meters. json** . U gebruikt het parameter bestand voor het door geven van waarden tijdens de implementatie waarmee de resource wordt aangepast die wordt geïmplementeerd. Geef een naam op voor het hosting plan en sla het bestand op.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       }
     }
   }
   ```

## <a name="deploy-project-to-azure"></a>Project implementeren in azure

U bent nu klaar om uw project te implementeren in een resource groep.

Het Power shell-script (Deploy-AzureResourceGroup. ps1) in het project maakt standaard gebruik van de module AzureRM. Als u de AzureRM-module nog hebt geïnstalleerd en u deze wilt blijven gebruiken, kunt u dit standaard script gebruiken. Met dit script kunt u de Visual Studio-interface gebruiken om uw oplossing te implementeren.

Als u echter hebt gemigreerd naar de nieuwe [AZ-module](/powershell/azure/new-azureps-module-az), moet u een nieuw script toevoegen aan uw project. Als u een script wilt toevoegen dat gebruikmaakt van de AZ-module, kopieert u het script [Deploy-AzTemplate. ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) en voegt u dit toe aan uw project. Als u dit script voor implementatie wilt gebruiken, moet u het uitvoeren vanuit een Power shell-console, in plaats van de implementatie-interface van Visual Studio te gebruiken.

Beide benaderingen worden weer gegeven in dit artikel. Dit artikel verwijst naar het standaard script als het AzureRM-module script en het nieuwe script als het AZ-module script.

### <a name="az-module-script"></a>AZ-module script

Open een Power shell-console in het AZ-module script en voer het volgende uit:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>AzureRM-module script

Voor het AzureRM-module script gebruikt u Visual Studio:

1. Kies **Implementeren** > **Nieuw** in het snelmenu van het knooppunt van het implementatieproject.

    ![Menu-item nieuwe implementatie](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

1. Het dialoogvenster **Implementeren in resourcegroep** wordt weergegeven. In de vervolgkeuzelijst **Resourcegroep** kiest u een bestaande resourcegroep of maakt u een nieuwe. Selecteer **Implementeren**.

    ![Het dialoog venster implementeren in resource groep](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. U kunt de voortgang van de implementatie bekijken in het venster **Uitvoer**. Wanneer de implementatie is voltooid, ziet u een laatste bericht dat de implementatie is geslaagd en dat er ongeveer als volgt uitziet:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Geïmplementeerde resources weer geven

Laten we de resultaten bekijken.

1. Open in een browser de [Azure Portal](https://portal.azure.com/) en meld u aan bij uw account. Als u de resourcegroep wilt bekijken, selecteert u **Resourcegroepen** en vervolgens de resourcegroep waarin u hebt geïmplementeerd.

1. U ziet alle geïmplementeerde resources. De naam van het opslagaccount komt niet precies overeen met wat u hebt opgegeven bij het toevoegen van die resource. Het opslagaccount moet uniek zijn. De sjabloon voegt automatisch een teken reeks toe aan de naam die u hebt gegeven om een unieke naam te maken.

    ![Resources weer geven](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Code toevoegen aan project

U hebt nu de infrastructuur geïmplementeerd voor uw app, maar er is nog geen code geïmplementeerd in uw project.

1. Voeg een project toe aan uw Visual Studio-oplossing. Klik met de rechtermuisknop op de oplossing en selecteer **Toevoegen** > **Nieuw Project**.

    ![Project toevoegen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-project.png)

1. Een **ASP.net core-webtoepassing**toevoegen.

    ![Web-app toevoegen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)

1. Geef uw web-app een naam en selecteer **maken**.

    ![Naam Web-app](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/name-web-app.png)

1. Selecteer **Webtoepassing** en **maken**.

    ![Webtoepassing selecteren](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project-type.png)

1. Nadat Visual Studio uw web-app heeft gemaakt, ziet u beide projecten in de oplossing.

    ![Projecten weer geven](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-projects.png)

1. Nu moet u nagaan of het nieuwe project gekoppeld is aan uw resourcegroepproject. Ga terug naar het project van de resource groep (ExampleAppDeploy). Klik met de rechtermuisknop op **Verwijzingen** en selecteer **Verwijzing toevoegen**.

    ![Verwijzing toevoegen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-new-reference.png)

1. Selecteer het web-app-project dat u hebt gemaakt.

   ![Verwijzing toevoegen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)

   Door een verwijzing toe te voegen, koppelt u het web-app-project aan het project van de resource groep en stelt u een aantal eigenschappen automatisch in. U ziet deze eigenschappen in het venster **Eigenschappen** voor de verwijzing. **Bestandspad toevoegen** bevat het pad waar het pakket wordt gemaakt. Noteer de map (ExampleApp) en het bestand (package.zip). U moet weten wat deze waarden zijn, omdat u ze als parameters moet opgeven tijdens het implementeren van de app.

   ![Zie Naslag informatie](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)

1. Ga terug naar uw sjabloon (WebSite. json) en voeg een resource toe aan de sjabloon.

    ![Resource toevoegen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource-2.png)

1. Selecteer deze keer **Webimplementatie voor Web Apps**.

    ![Web Deploy toevoegen](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)

   Sla uw sjabloon op.

1. Uw sjabloon bevat enkele nieuwe para meters. Deze zijn toegevoegd in de vorige stap. U hoeft geen waarden op te geven voor **_artifactsLocation** of **_artifactsLocationSasToken** , omdat deze waarden automatisch worden gegenereerd. U moet echter de map en de bestands naam instellen op het pad dat het implementatie pakket bevat. De namen van deze para meters eindigen op **PackageFolder** en **PackageFileName**. Het eerste deel van de naam is de naam van de Web Deploy-resource die u hebt toegevoegd. In dit artikel heten ze **ExampleAppPackageFolder** en **ExampleAppPackageFileName**.

   Open **website. para meters. json** en stel deze para meters in op de waarden die u in de verwijzings eigenschappen hebt gezien. Stel **ExampleAppPackageFolder** in op de naam van de map. Stel **ExampleAppPackageFileName** in op de naam van het zip-bestand.

   ```json
   {
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "hostingPlanName": {
         "value": "demoHostPlan"
       },
       "ExampleAppPackageFolder": {
         "value": "ExampleApp"
       },
       "ExampleAppPackageFileName": {
         "value": "package.zip"
       }
     }
   }
   ```

## <a name="deploy-code-with-infrastructure"></a>Code implementeren met de infra structuur

Omdat u code aan het project hebt toegevoegd, is uw implementatie iets anders. Tijdens de implementatie faseert u artefacten voor uw project naar een locatie waartoe Resource Manager toegang heeft. De artefacten worden klaargezet voor een opslag account.

### <a name="az-module-script"></a>AZ-module script

Er is een kleine wijziging die u moet aanbrengen in uw sjabloon als u het AZ-module script gebruikt. Met dit script wordt een slash toegevoegd aan de artefact locatie, maar uw sjabloon verwacht dat er geen slash wordt. Open WebSite. json en zoek de eigenschappen voor de MSDeploy-extensie. Het heeft een eigenschap met de naam **packageUri**. Verwijder de slash tussen de artefacten en de pakketmap.

Het moet er als volgt uitzien:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

In het vorige voor beeld is er geen `'/',` tussen de **para meters (' _artifactsLocation ')** en de **para meters (' ExampleAppPackageFolder ')** .

Bouw het project opnieuw op. Als u het project bouwt, worden de bestanden die u moet implementeren, toegevoegd aan de map voor gefaseerde installatie.

Open nu een Power shell-console en voer de volgende handelingen uit:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>AzureRM-module script

Voor het AzureRM-module script gebruikt u Visual Studio:

1. Als u opnieuw wilt implementeren, kiest u **implementeren**en de resource groep die u eerder hebt geïmplementeerd.

    ![Project opnieuw implementeren](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

1. Selecteer het opslag account dat u met deze resource groep hebt geïmplementeerd voor het **artefact opslag-account**.

   ![Opnieuw implementeren van Web Deploy](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy-web-app.png)

## <a name="view-web-app"></a>Web-app weer geven

1. Nadat de implementatie is voltooid, selecteert u uw web-app in de portal. Selecteer de URL om naar de site te bladeren.

   ![Door site bladeren](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/browse-site.png)

1. De implementatie van de standaard ASP.NET-app is voltooid.

   ![Geïmplementeerde app weer geven](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Bewerkings dashboard toevoegen

U bent niet beperkt tot alleen de resources die beschikbaar zijn via de Visual Studio-interface. U kunt uw implementatie aanpassen door een aangepaste resource toe te voegen aan uw sjabloon. Als u wilt zien hoe het toevoegen van een resource in zijn werk gaat, voegt u een operationeel dashboard toe voor het beheren van de resource die u hebt geïmplementeerd.

1. Open het bestand WebSite. json en voeg de volgende JSON toe na de bron van het opslag account, maar vóór het sluiten van `]` van de sectie resources.

   ```json
    ,{
      "properties": {
        "lenses": {
          "0": {
            "order": 0,
            "parts": {
              "0": {
                "position": {
                  "x": 0,
                  "y": 0,
                  "colSpan": 4,
                  "rowSpan": 6
                },
                "metadata": {
                  "inputs": [
                    {
                      "name": "resourceGroup",
                      "isOptional": true
                    },
                    {
                      "name": "id",
                      "value": "[resourceGroup().id]",
                      "isOptional": true
                    }
                  ],
                  "type": "Extension/HubsExtension/PartType/ResourceGroupMapPinnedPart"
                }
              },
              "1": {
                "position": {
                  "x": 4,
                  "y": 0,
                  "rowSpan": 3,
                  "colSpan": 4
                },
                "metadata": {
                  "inputs": [],
                  "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                  "settings": {
                    "content": {
                      "settings": {
                        "content": "__Customizations__\n\nUse this dashboard to create and share the operational views of services critical to the application performing. To customize simply pin components to the dashboard and then publish when you're done. Others will see your changes when you publish and share the dashboard.\n\nYou can customize this text too. It supports plain text, __Markdown__, and even limited HTML like images <img width='10' src='https://portal.azure.com/favicon.ico'/> and <a href='https://azure.microsoft.com' target='_blank'>links</a> that open in a new tab.\n",
                        "title": "Operations",
                        "subtitle": "[resourceGroup().name]"
                      }
                    }
                  }
                }
              }
            }
          }
        },
        "metadata": {
          "model": {
            "timeRange": {
              "value": {
                "relative": {
                  "duration": 24,
                  "timeUnit": 1
                }
              },
              "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
            }
          }
        }
      },
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "type": "Microsoft.Portal/dashboards",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Implementeer het project opnieuw.

1. Nadat de implementatie is voltooid, bekijkt u het dash board in de portal. Selecteer **dash board** en kies het abonnement dat u hebt geïmplementeerd.

   ![Aangepast dashboard](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/view-custom-dashboards.png)

1. U ziet het aangepaste dash board.

   ![Aangepast dashboard](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/Ops-DemoSiteGroup-dashboard.png)

U kunt de toegang tot het dashboard beheren met behulp van RBAC-groepen. U kunt ook het uiterlijk van het dashboard aanpassen zodra de implementatie is voltooid. Als u de resourcegroep echter opnieuw implementeert, wordt het dashboard opnieuw ingesteld op de standaardstatus in de sjabloon. Zie [Op programmatische wijze Azure-dashboards maken](../azure-portal/azure-portal-dashboards-create-programmatically.md) voor meer informatie over het maken van dashboards.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer in het Azure Portal **resource groepen** in het menu links.

1. Selecteer de naam van de resourcegroep.

1. Selecteer **Resourcegroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In deze snelstartgids hebt u geleerd hoe u sjablonen kunt maken en implementeren met Visual Studio. Zie voor meer informatie over het ontwikkelen van sjablonen onze nieuwe zelf studie reeks voor beginners:

> [!div class="nextstepaction"]
> [Zelf studies voor beginners](./template-tutorial-create-first-template.md)

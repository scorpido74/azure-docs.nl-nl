---
title: Projecten voor visual studio-resourcegroepprojecten & implementeren
description: Gebruik Visual Studio om een Azure-resourcegroepproject te maken en de resources in Azure te implementeren.
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 5127732ac0c33d4b27f70bd616fb23aaec5c871f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152728"
---
# <a name="creating-and-deploying-azure-resource-groups-through-visual-studio"></a>Azure-resourcegroepen maken en implementeren met Visual Studio

Met Visual Studio kunt u een project maken waarmee uw infrastructuur en code in Azure worden geïmplementeerd. U bijvoorbeeld de webhost, website en code voor de website implementeren. Visual Studio biedt veel verschillende startsjablonen om te implementeren in algemene scenario's. In dit artikel implementeert u een web-app.

In dit artikel ziet u hoe u [Visual Studio 2019 of hoger gebruiken met de Azure-ontwikkel- en ASP.NET-workloads geïnstalleerd.](/visualstudio/install/install-visual-studio?view=vs-2019) Als u Visual Studio 2017 gebruikt, is uw ervaring grotendeels hetzelfde.

## <a name="create-azure-resource-group-project"></a>Een Azure-resourcegroepproject maken

In deze sectie maakt u een Azure Resource Group-project met een **web-app-sjabloon.**

1. Kies **in**>Visual Studio Bestand**Nieuw**>**project**.
1. Selecteer de projectsjabloon **Azure Resource Group** en **Volgende**.

    ![Project maken](./media/create-visual-studio-deployment-project/create-project.png)

1. Geef uw project een naam. De andere standaardinstellingen zijn waarschijnlijk prima, maar bekijk ze om ze te laten werken voor uw omgeving. Wanneer u klaar bent, selecteert u **Maken**.

    ![Project maken](./media/create-visual-studio-deployment-project/name-project.png)

1. Kies het sjabloon dat u wilt implementeren in Azure Resource Manager. Er zijn diverse opties beschikbaar op basis van het type project dat u wilt implementeren. Kies voor dit artikel de sjabloon voor de **web-app** en **OK**.

    ![Een sjabloon selecteren](./media/create-visual-studio-deployment-project/select-project.png)

    Het sjabloon dat u kiest is slechts een beginpunt; u kunt resources toevoegen en verwijderen op basis van uw scenario.

1. Visual Studio maakt een implementatieproject voor resourcegroepen voor de web-app. Als u de bestanden voor uw project wilt bekijken, raadpleegt u het knooppunt in het implementatieproject.

    ![Knooppunten weergeven](./media/create-visual-studio-deployment-project/show-items.png)

    Aangezien u de sjabloon voor de web-app hebt gekozen, ziet u de volgende bestanden:

   | Bestandsnaam | Beschrijving |
   | --- | --- |
   | Deploy-AzureResourceGroup.ps1 |Een PowerShell-script waarmee PowerShell-opdrachten worden uitgevoerd om te worden geïmplementeerd in Azure Resource Manager. Visual Studio gebruikt dit PowerShell-script om uw sjabloon te implementeren. |
   | WebSite.json |Het Resource Manager-sjabloon dat de infrastructuur definieert die u in Azure wilt implementeren en de parameters die u kunt opgeven tijdens de implementatie. Hiermee worden ook de afhankelijkheden tussen resources gedefinieerd zodat deze in de juiste volgorde worden geïmplementeerd. |
   | WebSite.parameters.json |Een parameterbestand dat de waarden bevat die nodig zijn voor de sjabloon. U geeft parameterwaarden door om elke implementatie aan te passen. |

    Alle implementatieprojecten voor resourcegroepen bevatten deze algemene bestanden. Andere projecten bevatten mogelijk extra bestanden ter ondersteuning van andere functies.

## <a name="customize-resource-manager-template"></a>Sjabloon Resourcemanager aanpassen

U een implementatieproject aanpassen door de sjabloon Resourcemanager te wijzigen waarin de resources worden beschreven die u wilt implementeren. Zie [Azure Resource Manager-sjablonen samenstellen](template-syntax.md) voor meer informatie over de onderdelen van een Resource Manager-sjabloon.

1. Als u aan uw sjabloon wilt werken, opent u **WebSite.json**.

1. De Visual Studio-editor biedt hulpprogramma's voor het bewerken van het Resource Manager-sjabloon. In het scherm **JSON-overzicht** ziet u eenvoudig welke elementen zijn gedefinieerd in het sjabloon.

   ![Json-overzicht weergeven](./media/create-visual-studio-deployment-project/show-json-outline.png)

1. Selecteer een element in het overzicht om naar dat deel van de sjabloon te gaan.

   ![Navigeren in JSON](./media/create-visual-studio-deployment-project/navigate-json.png)

1. U kunt een nieuwe resource toevoegen aan een sjabloon door op de knop **Resource toevoegen** te klikken boven aan het venster JSON Outline of door met de rechtermuisknop op **Resources** te klikken en **Nieuwe resource toevoegen** te selecteren.

   ![Bron toevoegen](./media/create-visual-studio-deployment-project/add-resource.png)

1. Selecteer **Opslagaccount** en geef het een naam. Geef een naam op die niet meer dan elf tekens (alleen cijfers en kleine letters) omvat.

   ![Opslag toevoegen](./media/create-visual-studio-deployment-project/add-storage.png)

1. U ziet dat niet alleen de resource is toegevoegd, maar ook een parameter voor het type opslagaccount en een variabele voor de naam van het opslagaccount.

   ![Overzicht weergeven](./media/create-visual-studio-deployment-project/show-new-items.png)

1. De parameter voor het type opslagaccount is vooraf gedefinieerd met toegestane typen en een standaardtype. U kunt deze waarden laten staan of ze bewerken voor uw scenario. Als u niemand toestemming wilt geven om een **Premium_LRS**-opslagaccount te implementeren via deze sjabloon, verwijdert u de sjabloon uit de toegestane typen.

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

   ![Toon intellisense](./media/create-visual-studio-deployment-project/show-intellisense.png)

   U **het aantal werknemers** instellen op 1 en het bestand opslaan.

   ```json
   "properties": {
     "name": "[parameters('hostingPlanName')]",
     "numberOfWorkers": 1
   }
   ```

1. Open het bestand **WebSite.parameters.json.** U gebruikt het parametersbestand om waarden door te geven tijdens de implementatie die de geïmplementeerde bron aanpassen. Geef het hostingplan een naam en sla het bestand op.

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

## <a name="deploy-project-to-azure"></a>Project implementeren in Azure

U bent nu klaar om uw project te implementeren in een resourcegroep.

Standaard gebruikt het PowerShell-script (Deploy-AzureResourceGroup.ps1) in het project de AzureRM-module. Als u de AzureRM-module nog steeds hebt geïnstalleerd en deze wilt blijven gebruiken, u dit standaardscript gebruiken. Met dit script u de Visual Studio-interface gebruiken om uw oplossing te implementeren.

Als u echter bent gemigreerd naar de nieuwe [Az-module,](/powershell/azure/new-azureps-module-az)moet u een nieuw script aan uw project toevoegen. Als u een script wilt toevoegen dat de Az-module gebruikt, kopieert u het script [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) en voegt u het toe aan uw project. Als u dit script wilt gebruiken voor implementatie, moet u het uitvoeren vanaf een PowerShell-console, in plaats van de implementatie-interface van Visual Studio te gebruiken.

Beide benaderingen worden weergegeven in dit artikel. In dit artikel wordt verwezen naar het standaardscript als het AzureRM-modulescript en het nieuwe script als het script van de Az-module.

### <a name="az-module-script"></a>Az-modulescript

Open voor het script van de Az-module een PowerShell-console en voer het uitvoeren uit:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory . -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json
```

### <a name="azurerm-module-script"></a>AzureRM-modulescript

Gebruik Visual Studio voor het script van de AzureRM-module:

1. Kies**Nieuw** **implementeren** > in het snelmenu van het knooppunt implementatieproject .

    ![Nieuw implementatiemenu-item](./media/create-visual-studio-deployment-project/deploy.png)

1. Het dialoogvenster **Implementeren in resourcegroep** wordt weergegeven. In de vervolgkeuzelijst **Resourcegroep** kiest u een bestaande resourcegroep of maakt u een nieuwe. Selecteer **Implementeren**.

    ![Dialoogvenster Implementeren in resourcegroep](./media/create-visual-studio-deployment-project/show-deployment.png)

1. U kunt de voortgang van de implementatie bekijken in het venster **Uitvoer**. Wanneer de implementatie is voltooid, ziet u een laatste bericht dat de implementatie is geslaagd en dat er ongeveer als volgt uitziet:

   ```output
   18:00:58 - Successfully deployed template 'website.json' to resource group 'ExampleAppDeploy'.
   ```

## <a name="view-deployed-resources"></a>Geïmplementeerde resources weergeven

Laten we de resultaten bekijken.

1. Open in een browser de [Azure Portal](https://portal.azure.com/) en meld u aan bij uw account. Als u de resourcegroep wilt bekijken, selecteert u **Resourcegroepen** en vervolgens de resourcegroep waarin u hebt geïmplementeerd.

1. U ziet alle geïmplementeerde resources. De naam van het opslagaccount komt niet precies overeen met wat u hebt opgegeven bij het toevoegen van die resource. Het opslagaccount moet uniek zijn. De sjabloon voegt automatisch een reeks tekens toe aan de naam die u hebt opgegeven om een unieke naam te maken.

    ![Resources weergeven](./media/create-visual-studio-deployment-project/show-deployed-resources.png)

## <a name="add-code-to-project"></a>Code toevoegen aan project

U hebt nu de infrastructuur geïmplementeerd voor uw app, maar er is nog geen code geïmplementeerd in uw project.

1. Voeg een project toe aan uw Visual Studio-oplossing. Klik met de rechtermuisknop op de oplossing en selecteer**Nieuw project** **toevoegen** > .

    ![Project toevoegen](./media/create-visual-studio-deployment-project/add-project.png)

1. Voeg een **ASP.NET Kernwebtoepassing toe.**

    ![Web-app toevoegen](./media/create-visual-studio-deployment-project/add-app.png)

1. Geef uw web-app een naam en selecteer **Maken**.

    ![Web-app naam geven](./media/create-visual-studio-deployment-project/name-web-app.png)

1. Selecteer **Webtoepassing** en **Maak**.

    ![Webtoepassing selecteren](./media/create-visual-studio-deployment-project/select-project-type.png)

1. Nadat Visual Studio uw web-app heeft gemaakt, ziet u beide projecten in de oplossing.

    ![Projecten weergeven](./media/create-visual-studio-deployment-project/show-projects.png)

1. Nu moet u nagaan of het nieuwe project gekoppeld is aan uw resourcegroepproject. Ga terug naar uw resourcegroepproject (ExampleAppDeploy). Klik met de rechtermuisknop op **Verwijzingen** en selecteer **Verwijzing toevoegen**.

    ![Verwijzing toevoegen](./media/create-visual-studio-deployment-project/add-new-reference.png)

1. Selecteer het web-app-project dat u hebt gemaakt.

   ![Verwijzing toevoegen](./media/create-visual-studio-deployment-project/add-reference.png)

   Door een verwijzing toe te voegen, koppelt u het project voor de web-app aan het project resourcegroep en stelt u automatisch enkele eigenschappen in. U ziet deze eigenschappen in het venster **Eigenschappen** voor de verwijzing. **Bestandspad toevoegen** bevat het pad waar het pakket wordt gemaakt. Noteer de map (ExampleApp) en het bestand (package.zip). U moet weten wat deze waarden zijn, omdat u ze als parameters moet opgeven tijdens het implementeren van de app.

   ![Zie referentie](./media/create-visual-studio-deployment-project/see-reference.png)

1. Ga terug naar uw sjabloon (WebSite.json) en voeg een bron toe aan de sjabloon.

    ![Bron toevoegen](./media/create-visual-studio-deployment-project/add-resource-2.png)

1. Selecteer deze keer **Webimplementatie voor Web Apps**.

    ![Webdeploy toevoegen](./media/create-visual-studio-deployment-project/add-web-deploy.png)

   Sla uw sjabloon op.

1. Er zijn een aantal nieuwe parameters in uw sjabloon. Ze zijn toegevoegd in de vorige stap. U hoeft geen waarden op te geven voor **_artifactsLocation** of **_artifactsLocationSasToken** omdat deze waarden automatisch worden gegenereerd. U moet echter de map en bestandsnaam instellen op het pad dat het implementatiepakket bevat. De namen van deze parameters eindigen met **PackageFolder** en **PackageFileName**. Het eerste deel van de naam is de naam van de webdeploybron die u hebt toegevoegd. In dit artikel worden ze **ExampleAppPackageFolder** en **ExampleAppPackageFileName**genoemd.

   Open **Website.parameters.json** en stel deze parameters in op de waarden die u in de referentie-eigenschappen hebt gezien. **Stel ExampleAppPackageFolder** in op de naam van de map. **Stel ExampleAppPackageFileName** in op de naam van het zip-bestand.

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

## <a name="deploy-code-with-infrastructure"></a>Code implementeren met infrastructuur

Omdat u code aan het project hebt toegevoegd, is uw implementatie deze keer een beetje anders. Tijdens de implementatie faset u artefacten voor uw project naar een plaats waar Resource Manager toegang toe heeft. De artefacten worden geënsceneerd naar een opslagaccount.

### <a name="az-module-script"></a>Az-modulescript

Er is een kleine wijziging die u moet aanbrengen in uw sjabloon als u het script van de Az-module gebruikt. Dit script voegt een slash toe aan de artefactenlocatie, maar uw sjabloon verwacht die slash niet. Open WebSite.json en zoek de eigenschappen voor de MSDeploy-extensie. Het heeft een eigenschap genaamd **packageUri**. Verwijder de slash tussen de artefactenlocatie en de pakketmap.

Dit ziet er als volgt uit:

```json
"packageUri": "[concat(parameters('_artifactsLocation'), parameters('ExampleAppPackageFolder'), '/', parameters('ExampleAppPackageFileName'), parameters('_artifactsLocationSasToken'))]",
```

In het voorgaande voorbeeld `'/',` is er geen **tussenparameters('_artifactsLocation')** en **parameters('ExampleAppPackageFolder').**

Bouw het project opnieuw op. Als u het project bouwt, zorgt u ervoor dat de bestanden die u moet implementeren, worden toegevoegd aan de map met tijdelijke bestanden.

Open nu een PowerShell-console en voer het:

```powershell
.\Deploy-AzTemplate.ps1 -ArtifactStagingDirectory .\bin\Debug\staging\ExampleAppDeploy -Location centralus -TemplateFile WebSite.json -TemplateParametersFile WebSite.parameters.json -UploadArtifacts -StorageAccountName <storage-account-name>
```

### <a name="azurerm-module-script"></a>AzureRM-modulescript

Gebruik Visual Studio voor het script van de AzureRM-module:

1. Als u opnieuw wilt implementeren, kiest u **Implementeren**en de resourcegroep die u eerder hebt geïmplementeerd.

    ![Project opnieuw implementeren](./media/create-visual-studio-deployment-project/redeploy.png)

1. Selecteer het opslagaccount dat u met deze brongroep hebt geïmplementeerd voor het **Artefact-opslagaccount**.

   ![Webdeploy opnieuw implementeren](./media/create-visual-studio-deployment-project/redeploy-web-app.png)

## <a name="view-web-app"></a>Web-app weergeven

1. Nadat de implementatie is voltooid, selecteert u uw web-app in de portal. Selecteer de URL om naar de site te bladeren.

   ![Site bladeren](./media/create-visual-studio-deployment-project/browse-site.png)

1. De implementatie van de standaard ASP.NET-app is voltooid.

   ![Geïmplementeerde app weergeven](./media/create-visual-studio-deployment-project/show-deployed-app.png)

## <a name="add-operations-dashboard"></a>Operations-dashboard toevoegen

U bent niet beperkt tot alleen de resources die beschikbaar zijn via de Visual Studio-interface. U kunt uw implementatie aanpassen door een aangepaste resource toe te voegen aan uw sjabloon. Als u wilt zien hoe het toevoegen van een resource in zijn werk gaat, voegt u een operationeel dashboard toe voor het beheren van de resource die u hebt geïmplementeerd.

1. Open het bestand WebSite.json en voeg de volgende JSON `]` toe na de bron van het opslagaccount, maar vóór het sluiten van de sectie resources.

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
      "type": "Microsoft.Portal/dashboards",
      "apiVersion": "2015-08-01-preview",
      "name": "[concat('ARM-',resourceGroup().name)]",
      "location": "[resourceGroup().location]",
      "tags": {
        "hidden-title": "[concat('OPS-',resourceGroup().name)]"
      }
    }
   ```

1. Uw project opnieuw implementeren.

1. Nadat de implementatie is voltooid, bekijkt u uw dashboard in de portal. Selecteer **Dashboard** en kies de dashboarddat u hebt geïmplementeerd.

   ![Aangepast dashboard](./media/create-visual-studio-deployment-project/view-custom-dashboards.png)

1. U ziet het aangepaste dashboard.

   ![Aangepast dashboard](./media/create-visual-studio-deployment-project/Ops-DemoSiteGroup-dashboard.png)

U kunt de toegang tot het dashboard beheren met behulp van RBAC-groepen. U kunt ook het uiterlijk van het dashboard aanpassen zodra de implementatie is voltooid. Als u de resourcegroep echter opnieuw implementeert, wordt het dashboard opnieuw ingesteld op de standaardstatus in de sjabloon. Zie [Op programmatische wijze Azure-dashboards maken](../../azure-portal/azure-portal-dashboards-create-programmatically.md) voor meer informatie over het maken van dashboards.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroepen** in het linkermenu in de Azure-portal.

1. Selecteer de naam van de resourcegroep.

1. Selecteer **Brongroep verwijderen** in het bovenste menu.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u sjablonen maken en implementeren met Visual Studio. Zie onze nieuwe beginnerszelfstudiereeks voor meer informatie over sjabloonontwikkeling:

> [!div class="nextstepaction"]
> [Zelfstudies voor beginners](./template-tutorial-create-first-template.md)

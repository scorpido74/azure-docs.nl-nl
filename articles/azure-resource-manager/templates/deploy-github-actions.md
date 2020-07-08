---
title: Resource Manager-sjablonen implementeren met behulp van GitHub-acties
description: Hierin wordt beschreven hoe u Azure Resource Manager-sjablonen implementeert met behulp van GitHub-acties.
ms.topic: conceptual
ms.date: 07/02/2020
ms.openlocfilehash: 313354499901bc69ec6e00f0ba7c385065cae615
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85854737"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>Azure Resource Manager sjablonen implementeren met behulp van GitHub-acties

Met [github acties](https://help.github.com/en/actions) kunt u aangepaste werk stromen voor het ontwikkelen van software ontwikkeling direct in uw github-opslag plaats maken waar de sjablonen van uw Azure Resource Manager (arm) worden opgeslagen. Een [werk stroom](https://help.github.com/actions/reference/workflow-syntax-for-github-actions) wordt gedefinieerd door een yaml-bestand. Werk stromen hebben een of meer taken met elke taak die een set stappen bevat waarmee afzonderlijke taken worden uitgevoerd. Stappen kunnen opdrachten uitvoeren of een actie gebruiken. U kunt uw eigen acties maken of acties die worden gedeeld door de [github-Community](https://github.com/marketplace?type=actions) en deze indien nodig aanpassen. In dit artikel wordt beschreven hoe u [Azure cli-actie](https://github.com/marketplace/actions/azure-cli-action) gebruikt om Resource Manager-sjablonen te implementeren.

De actie Azure CLI heeft twee afhankelijke acties:

- **[Afhandeling](https://github.com/marketplace/actions/checkout)**: Bekijk de opslag plaats zodat de werk stroom toegang heeft tot de opgegeven resource manager-sjabloon.
- **[Azure-aanmelding](https://github.com/marketplace/actions/azure-login)**: Meld u aan met uw Azure-referenties

Een basis werk stroom voor het implementeren van een resource manager-sjabloon kan uit drie stappen bestaan:

1. Bekijk een sjabloon bestand.
2. Meld u aan bij Azure.
3. Een resource manager-sjabloon implementeren

## <a name="prerequisites"></a>Vereisten

U hebt een GitHub-opslag plaats nodig om uw Resource Manager-sjablonen en uw werk stroom bestanden op te slaan. Zie [een nieuwe opslag plaats maken](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)om er een te maken.

## <a name="configure-deployment-credentials"></a>Implementatiereferenties configureren

De actie Azure-aanmelding maakt gebruik van een service-principal voor verificatie bij Azure. Voor de principal van een CI/CD-werk stroom is doorgaans het ingebouwde contributor-recht nodig om Azure-resources te kunnen implementeren.

Het volgende Azure CLI-script laat zien hoe u een Azure-Service-Principal genereert met Inzender machtigingen voor een Azure-resource groep. Deze resource groep is de locatie waar de werk stroom de resources implementeert die zijn gedefinieerd in de Resource Manager-sjabloon.

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

Pas de waarde van **$projectName** en **$Location** in het script aan. De naam van de resource groep is de naam van het project waaraan **RG** is toegevoegd. U moet de naam van de resource groep opgeven in uw werk stroom.

Het script voert een JSON-object uit dat er ongeveer als volgt uitziet:

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

Kopieer de JSON-uitvoer en sla deze op als een GitHub-geheim in uw GitHub-opslag plaats. Zie het [vereiste](#prerequisites) als u nog geen opslag plaats hebt.

1. Selecteer in uw GitHub-opslag plaats het tabblad **instellingen** .
1. Selecteer **geheimen** in het menu links.
1. Voer de volgende waarden in:

    - **Naam**: AZURE_CREDENTIALS
    - **Waarde**: (plak de JSON-uitvoer)
1. Selecteer **geheim toevoegen**.

U moet de geheime naam opgeven in de werk stroom.

## <a name="add-resource-manager-template"></a>Resource Manager-sjabloon toevoegen

Voeg een resource manager-sjabloon toe aan de GitHub-opslag plaats. Als u er nog geen hebt, kunt u de volgende sjabloon gebruiken. Met de sjabloon wordt een opslag account gemaakt.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

U kunt het bestand plaatsen op een wille keurige plaats in de opslag plaats. In het workflow-voor beeld in de volgende sectie wordt ervan uitgegaan dat het sjabloon bestand de naam **azuredeploy.js**heeft en het is opgeslagen in een map met de naam **sjablonen** in de hoofdmap van uw opslag plaats.

## <a name="create-workflow"></a>Werk stroom maken

Het werk stroom bestand moet worden opgeslagen in de map **. github/werk stromen** in de hoofdmap van uw opslag plaats. De extensie van een werk stroom bestand kan **. yml** of **. yaml**.

U kunt een werk stroom bestand maken en het bestand vervolgens pushen/uploaden naar de opslag plaats of de volgende procedure gebruiken:

1. Selecteer in de GitHub-opslag plaats **acties** in het bovenste menu.
1. Selecteer **nieuwe werk stroom**.
1. Selecteer **zelf een werk stroom instellen**.
1. Wijzig de naam van het werk stroom bestand als u de voor keur geeft aan een andere naam dan **Main. yml**. Bijvoorbeeld: **deployStorageAccount. yml**.
1. Vervang de inhoud van het yml-bestand door het volgende:

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    Het werk stroom bestand heeft drie secties:

    - **naam**: de naam van de werk stroom.
    - **op**: de naam van de GitHub-gebeurtenissen die de werk stroom activeren. De werk stroom wordt geactiveerd wanneer er sprake is van een push gebeurtenis in de hoofd vertakking, waardoor ten minste één van de opgegeven bestanden wordt gewijzigd. De twee bestanden zijn het werk stroom bestand en het sjabloon bestand.

        > [!IMPORTANT]
        > Controleer of de twee bestanden en hun bijbehorende paden overeenkomen.
    - **taken**: een uitvoering van een werk stroom bestaat uit een of meer taken. Er is slechts één taak met de naam **Deploy-Storage-account-Temp late**.  Deze taak bestaat uit drie stappen:

        - **Bron code voor uitchecken**.
        - **Meld u aan bij Azure**.

            > [!IMPORTANT]
            > Controleer of de geheime naam overeenkomt met wat u hebt opgeslagen in uw opslag plaats. Zie [implementatie referenties configureren](#configure-deployment-credentials).
        - **Arm-sjabloon implementeren**. Vervang de waarde van **resourceGroupName**.  Als u het Azure CLI-script in [implementatie referenties configureren](#configure-deployment-credentials)hebt gebruikt, is de naam van de gegenereerde resource groep de naam van het project waaraan **RG** is toegevoegd. Controleer de waarde van **templateLocation**.

1. Selecteer **door voeren starten**.
1. Selecteer **rechtstreeks door voeren naar de hoofd vertakking**.
1. Selecteer **nieuw bestand door voeren** (of **wijzigingen door voeren**).

Omdat de werk stroom zo is geconfigureerd dat deze wordt geactiveerd door het werk stroom bestand of het sjabloon bestand dat wordt bijgewerkt, wordt de werk stroom meteen gestart nadat u de wijzigingen hebt doorgevoerd.

## <a name="check-workflow-status"></a>Werk stroom status controleren

1. Selecteer het tabblad **acties** . Er wordt een **deployStorageAccount. yml** -werk stroom voor maken weer gegeven. Het duurt 1-2 minuten om de werk stroom uit te voeren.
1. Selecteer de werk stroom om deze te openen.
1. Selecteer **Deploy-Storage-account-Temp late** (taak naam) in het menu links. De taak naam wordt gedefinieerd in de werk stroom.
1. Selecteer **arm-sjabloon implementeren** (stap naam) om deze uit te vouwen. U ziet het REST API-antwoord.

## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie: uw eerste arm-sjabloon maken en implementeren](template-tutorial-create-first-template.md)voor een stapsgewijze zelf studie waarin u door het proces van het maken van een sjabloon wordt geleid.

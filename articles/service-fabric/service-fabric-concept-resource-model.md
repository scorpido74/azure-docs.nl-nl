---
title: Resource model van Azure Service Fabric-toepassing
description: Dit artikel bevat een overzicht van het beheren van een Azure Service Fabric-toepassing met Azure Resource Manager
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 44073967730d95e803f57d504aa9d8c529250a8d
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751207"
---
# <a name="service-fabric-application-resource-model"></a>Resource model van Service Fabric toepassing

Het is raadzaam dat Service Fabric toepassingen worden geïmplementeerd op uw Service Fabric cluster via Azure Resource Manager. Met deze methode kunnen toepassingen en services in JSON worden beschreven en worden geïmplementeerd in dezelfde resource manager-sjabloon als uw cluster. In plaats van het implementeren en beheren van toepassingen via Power shell of Azure CLI, hoeft u niet te wachten tot het cluster gereed is. Het proces van toepassingsregistratie, -inrichting en -implementatie kan in één stap worden uitgevoerd. Dit is de best practice voor het beheren van de levenscyclus van toepassingen in uw cluster. Zie [Aanbevolen procedures: Infrastructure as code](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources)(Engelstalig) voor meer informatie.

Als dit van toepassing is, kunt u uw toepassingen beheren als Resource Manager-resources om te verbeteren:

* Audittrail: in Resource Manager wordt elke bewerking gecontroleerd en wordt een gedetailleerd *activiteiten logboek* bijgehouden waarmee u wijzigingen kunt traceren die zijn aangebracht in deze toepassingen en uw cluster.
* Op rollen gebaseerd toegangs beheer: het beheren van toegang tot clusters en toepassingen die op het cluster zijn geïmplementeerd, kunnen worden uitgevoerd via dezelfde resource manager-sjabloon.
* Azure Resource Manager (via de Azure Portal) wordt een een-stop-shop voor het beheren van uw cluster en essentiële toepassings implementaties.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Levens cyclus van Service Fabric toepassing met Azure Resource Manager

In dit document leert u het volgende:

> [!div class="checklist"]
>
> * Toepassings resources implementeren met behulp van Azure Resource Manager
> * Toepassings resources bijwerken met behulp van Azure Resource Manager
> * Toepassings bronnen verwijderen

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Toepassings resources implementeren met behulp van Azure Resource Manager

Als u een toepassing en de bijbehorende services wilt implementeren met behulp van het resource model Azure Resource Manager-toepassing, moet u de toepassings code inpakken, het pakket uploaden en vervolgens naar de locatie van het pakket verwijzen in een Azure Resource Manager-sjabloon als een toepassing resource. Bekijk voor meer informatie [package a Application](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg).

Maak vervolgens een Azure Resource Manager sjabloon, werk het parameter bestand bij met toepassings gegevens en implementeer het op het Service Fabric cluster. Raadpleeg [hier](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)voor beelden.

### <a name="create-a-storage-account"></a>Een account voor Opslag maken

Voor het implementeren van een toepassing vanuit een resource manager-sjabloon is een opslag account nodig om de installatie kopie van de toepassing te faseren. U kunt een bestaand opslag account opnieuw gebruiken of een nieuw opslag account maken om uw toepassingen te faseren. Als u een bestaand opslag account wilt gebruiken, kunt u deze stap overs Laan. 

![Maak een opslagaccount][CreateStorageAccount]

### <a name="configure-storage-account"></a>Opslag account configureren

Zodra het opslag account is gemaakt, moet u een BLOB-container maken waarin de toepassingen kunnen worden klaargezet. Ga in het Azure Portal naar het opslag account waarin u uw toepassingen wilt opslaan. Selecteer de Blade **blobs** en klik op de knop **container toevoegen** . Resources in uw cluster kunnen worden beveiligd door het open bare toegangs niveau in te stellen op privé. Toegang kan op verschillende manieren worden verleend:

* [Toegang verlenen tot blobs en wacht rijen met Azure Active Directory](../storage/common/storage-auth-aad-app.md)
* [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC in de Azure-portal](../storage/common/storage-auth-aad-rbac-portal.md)
* [Toegang delegeren met een Shared Access Signature (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature
)

 Voor dit voor beeld gaan we anonieme lees toegang voor blobs blijven gebruiken.

![Blob maken][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>Fase toepassing in een opslag account

Voordat de toepassing kan worden geïmplementeerd, moet deze worden klaargezet in Blob Storage. In deze zelf studie maakt u het toepassings pakket hand matig. deze stap kan echter worden geautomatiseerd.  Bekijk voor meer informatie [package a Application](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). In de volgende stappen wordt de [voorbeeld toepassing voor stem](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) gebruikt.

1. Klik in Visual Studio met de rechter muisknop op het project stem en selecteer pakket.
![pakket toepassing][PackageApplication]  
2. Open de **.\service-Fabric-DotNet-quickstart\Voting\pkg\Debug** -map die zojuist is gemaakt en post de inhoud naar een bestand met de naam **stem. zip** , zodat de ApplicationManifest. XML zich in de hoofdmap van het zip-bestand bevindt.  
![zip-toepassing][ZipApplication]  
3. Wijzig de extensie van. zip in **. sfpkg**.
4. Klik in de Azure Portal in de **app** -container van uw opslag account op **uploaden** en upload **stemmen. sfpkg**.  
App-pakket voor ![uploaden][UploadAppPkg]

De toepassing is nu gefaseerd. U kunt nu de Azure Resource Manager-sjabloon maken om de toepassing te implementeren.

### <a name="create-the-azure-resource-manager-template"></a>De Azure Resource Manager sjabloon maken

De voorbeeld toepassing bevat [Azure Resource Manager sjablonen](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) die kunnen worden gebruikt voor het implementeren van de toepassing. De sjabloon bestanden hebben de naam **UserApp. json** en **UserApp. para meters. json**.

> [!NOTE]
> Het bestand **UserApp. para meters. json** moet worden bijgewerkt met de naam van uw cluster.
>
>

| Parameter              | Beschrijving                                 | Voorbeeld                                                      | Opmerkingen                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | De naam van het cluster waarnaar u wilt implementeren | SF-cluster123                                                |                                                              |
| toepassing            | De naam van de toepassing                 | Hem                                                       |
| applicationTypeName    | De type naam van de toepassing           | VotingType                                                   | Moeten overeenkomen met wat in ApplicationManifest. XML.                 |
| applicationTypeVersion | De versie van het toepassings type         | 1.0.0                                                        | Moeten overeenkomen met wat in ApplicationManifest. XML.                 |
| serviceName            | De naam van de service die de service         | Stemmen ~ VotingWeb                                             | Moet de indeling ApplicationName ~ Service type hebben            |
| serviceTypeName        | De type naam van de service                | VotingWeb                                                    | Moeten overeenkomen met wat in ServiceManifest. XML                 |
| appPackageUrl          | De URL van de Blob-opslag van de toepassing     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | De URL van het toepassings pakket in Blob Storage (de procedure die moet worden ingesteld, wordt hieronder beschreven) |

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>De toepassing implementeren

Als u de toepassing wilt implementeren, voert u de New-AzResourceGroupDeployment uit om te implementeren in de resource groep die het cluster bevat.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Service Fabric toepassing bijwerken met behulp van Azure Resource Manager

Toepassingen die al zijn geïmplementeerd op een Service Fabric cluster, worden bijgewerkt om de volgende redenen:

1. Er wordt een nieuwe service toegevoegd aan de toepassing. Een service definitie moet worden toegevoegd aan het bestand Service-manifest. XML en Application-manifest. XML. Vervolgens moet de versie van het toepassings type worden bijgewerkt van 1.0.0 naar 1.0.1 [UserApp. para meters. json](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)om de nieuwe versie van de toepassing weer te geven.

    ```json
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```

2. Er wordt een nieuwe versie van een bestaande service toegevoegd aan de toepassing. Dit omvat toepassings code wijzigingen en updates voor de versie en naam van het app-type.

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Toepassings bronnen verwijderen

Toepassingen die zijn geïmplementeerd met het resource model van de toepassing in Azure Resource Manager kunnen worden verwijderd uit het cluster met behulp van onderstaande stappen

1) Resource-ID voor toepassing ophalen met [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0):

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

2) Verwijder de toepassings resources met [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0):

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Volgende stappen

Informatie over het resource model van de toepassing ophalen:

* [Een toepassing model leren in Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Toepassings-en service manifesten Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>Zie ook

* [Aanbevolen procedures](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [Toepassingen en services beheren als Azure-resources](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png

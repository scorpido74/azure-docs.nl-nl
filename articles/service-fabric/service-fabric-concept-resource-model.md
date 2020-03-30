---
title: Azure Service Fabric-toepassingsbronmodel
description: In dit artikel vindt u een overzicht van het beheer van een Azure Service Fabric-toepassing met Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: sfrev
ms.openlocfilehash: 69c10b0e9d3b7c29122c8432ab1e4bc06d3a3120
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481113"
---
# <a name="service-fabric-application-resource-model"></a>Resourcemodel servicefabric-toepassing

U hebt meerdere opties voor het implementeren van Azure Service Fabric-toepassingen in uw Service Fabric-cluster. We raden u aan Azure Resource Manager te gebruiken. Als u Resourcebeheer gebruikt, u toepassingen en services in JSON beschrijven en deze vervolgens implementeren in dezelfde resourcemanagersjabloon als uw cluster. In tegenstelling tot het gebruik van PowerShell of Azure CLI om toepassingen te implementeren en te beheren, hoeft u als u Resource Beheer gebruikt niet te wachten tot het cluster klaar is. toepassingsregistratie, inrichting en implementatie kunnen allemaal in één stap plaatsvinden. Het gebruik van Resource Manager is de beste manier om de levenscyclus van de toepassing in uw cluster te beheren. Zie [Aanbevolen procedures: Infrastructuur als code voor](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)meer informatie.

Als u uw toepassingen beheert als resources in Resource Manager, u verbeteringen op deze gebieden bereiken:

* Audit trail: Resource Manager controleert elke bewerking en houdt een gedetailleerd activiteitenlogboek bij. Met een activiteitenlogboek u eventuele wijzigingen in de toepassingen en in uw cluster traceren.
* Op rollen gebaseerd toegangsbeheer: u de toegang tot clusters en toepassingen die op het cluster worden geïmplementeerd beheren met dezelfde resourcebeheersjabloon.
* Beheerefficiëntie: met Resource Manager u één locatie (de Azure-portal) gebruiken voor het beheer van uw cluster- en kritieke toepassingsimplementaties.

In dit document leert u hoe u:

> [!div class="checklist"]
>
> * Toepassingsbronnen implementeren met Resourcebeheer.
> * Upgrade toepassingsbronnen met ResourceBeheer.
> * Toepassingsbronnen verwijderen.

## <a name="deploy-application-resources"></a>Toepassingsbronnen implementeren

De stappen op hoog niveau die u neemt om een toepassing en de bijbehorende services te implementeren met behulp van het resourceresourcemodel resourcemanager, zijn:
1. Pak de toepassingscode.
1. Upload het pakket.
1. Verwijs naar de locatie van het pakket in een resourcemanagersjabloon als toepassingsbron. 

Voor meer informatie, bekijk [Pakket een toepassing](service-fabric-package-apps.md#create-an-sfpkg).

Vervolgens maakt u een resourcebeheersjabloon, werkt u het parametersbestand bij met toepassingsgegevens en implementeert u de sjabloon in het cluster Servicefabric. [Ontdek voorbeelden.](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Als u een toepassing wilt implementeren vanuit een resourcemanagersjabloon, moet u een opslagaccount hebben. Het opslagaccount wordt gebruikt om de toepassingsafbeelding te fasen. 

U een bestaand opslagaccount opnieuw gebruiken of u een nieuw opslagaccount maken voor het fasen van uw toepassingen. Als u een bestaand opslagaccount gebruikt, u deze stap overslaan. 

![Een opslagaccount maken][CreateStorageAccount]

### <a name="configure-your-storage-account"></a>Uw opslagaccount configureren

Nadat het opslagaccount is gemaakt, maakt u een blobcontainer waarin de toepassingen kunnen worden gefaseerd. Ga in de Azure-portal naar het Azure Storage-account waar u uw toepassingen wilt opslaan. Selecteer **Blobs** > **Container toevoegen**. 

Bronnen in uw cluster kunnen worden beveiligd door het openbare toegangsniveau in te stellen op **privé.** U op meerdere manieren toegang verlenen:

* De toegang tot blobs en wachtrijen autoriseren met [Azure Active Directory](../storage/common/storage-auth-aad-app.md).
* Geef toegang tot Azure blob- en wachtrijgegevens met [RBAC in de Azure-portal.](../storage/common/storage-auth-aad-rbac-portal.md)
* Toegang delegeren met behulp van een [handtekening voor gedeelde toegang](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature).

In het voorbeeld in de volgende schermafbeelding wordt gebruik gemaakt van anonieme leestoegang voor blobs.

![Blob maken][CreateBlob]

### <a name="stage-the-application-in-your-storage-account"></a>De toepassing in uw opslagaccount faseeren

Voordat u een toepassing implementeren, moet u de toepassing in blobopslag fase. In deze zelfstudie maken we het toepassingspakket handmatig. Houd er rekening mee dat deze stap kan worden geautomatiseerd. Zie [Een toepassing verpakken](service-fabric-package-apps.md#create-an-sfpkg)voor meer informatie. 

In deze zelfstudie gebruiken we de [toepassing Voorbeeld van stemmen](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart).

1. Klik in Visual Studio met de rechtermuisknop op het project **Stemmen** en selecteer **Pakket**.

   ![Pakkettoepassing][PackageApplication]  
1. Ga naar de map *.\service-fabric-dotnet-quickstart\Voting\pkg\Debug.* Rits de inhoud in een bestand genaamd *Voting.zip*. Het bestand *ApplicationManifest.xml* moet zich bij de hoofdmap in het zip-bestand bevindt.

   ![Zip-toepassing][ZipApplication]  
1. Wijzig de naam van het bestand om de extensie te wijzigen van .zip naar *.sfpkg*.

1. Selecteer in de Azure-portal in de **apps-container** voor uw opslagaccount de optie **Uploaden**en upload **vervolgens Voting.sfpkg**. 

   ![App-pakket uploaden][UploadAppPkg]

Nu is de toepassing gefaseerd en u de sjabloon Resourcemanager maken om de toepassing te implementeren.

### <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken

De voorbeeldtoepassing bevat [Azure Resource Manager-sjablonen](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) die u gebruiken om de toepassing te implementeren. De namen van sjabloonbestanden zijn *UserApp.json* en *UserApp.Parameters.json*.

> [!NOTE]
> Het bestand *UserApp.Parameters.json* moet worden bijgewerkt met de naam van uw cluster.
>
>

| Parameter              | Beschrijving                                 | Voorbeeld                                                      | Opmerkingen                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | De naam van het cluster waarnaar u implementeert | sf-cluster123                                                |                                                              |
| toepassing            | De naam van de toepassing                 | Stemmen                                                       |
| applicationTypeName    | De typenaam van de toepassing           | VotingType                                                   | Moet overeenkomen met ApplicationManifest.xml                 |
| applicationTypeVersion | De versie van het toepassingstype         | 1.0.0                                                        | Moet overeenkomen met ApplicationManifest.xml                 |
| Servicenaam            | De naam van de dienst         | Voting~VotingWeb                                             | Moet in de indeling ApplicationName~ServiceType staan            |
| serviceTypeName        | De typenaam van de service                | VotingWeb                                                    | Moet overeenkomen met ServiceManifest.xml                 |
| appPackageUrl appPackageUrl          | De BLOB-opslag-URL van de toepassing     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | De URL van het toepassingspakket in blob-opslag (de procedure om de URL in te stellen wordt later in het artikel beschreven) |

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

Voer de cmdlet **Nieuw-AzResourceGroupDeployment** uit om de toepassing te implementeren in de brongroep die uw cluster bevat:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-the-service-fabric-application-by-using-resource-manager"></a>De servicefabric-toepassing bijwerken met Resource Manager

U een toepassing die al is geïmplementeerd naar een cluster van Servicefabric upgraden om een van de volgende redenen:

* Er wordt een nieuwe service aan de toepassing toegevoegd. Een servicedefinitie moet worden toegevoegd aan *service-manifest.xml-* en *application-manifest.xml-bestanden* wanneer een service aan de toepassing wordt toegevoegd. Als u een nieuwe versie van een toepassing wilt weergeven, moet u ook de versie van het toepassingstype wijzigen van 1.0.0 naar 1.0.1 in [UserApp.Parameters.json:](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json)

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

* Er wordt een nieuwe versie van een bestaande service aan de toepassing toegevoegd. Voorbeelden hiervan zijn wijzigingen in de toepassingscode en updates voor de versie en naam van het app-type. Update UserApp.Parameters.json als volgt bij voor deze upgrade:

    ```json
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Toepassingsbronnen verwijderen

Ga als u een toepassing verwijderen die is geïmplementeerd met het toepassingsbronmodel in Resourcebeheer:

1. Gebruik de cmdlet [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0) om de resource-id voor de toepassing op te halen:

    ```powershell
    Get-AzResource  -Name <String> | f1
    ```

1. Gebruik de cmdlet [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0) om de toepassingsbronnen te verwijderen:

    ```powershell
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Volgende stappen

Informatie over het toepassingsbronmodel:

* [Een toepassing modelleren in Service Fabric](service-fabric-application-model.md)
* [Service Fabric-applicatie- en servicemanifesten](service-fabric-application-and-service-manifests.md)
* [Aanbevolen procedures: Infrastructuur als code](service-fabric-best-practices-infrastructure-as-code.md#azure-service-fabric-resources)
* [Toepassingen en services beheren als Azure-bronnen](service-fabric-best-practices-infrastructure-as-code.md)


<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png

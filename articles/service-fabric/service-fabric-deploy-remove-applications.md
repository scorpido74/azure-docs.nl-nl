---
title: Azure Service Fabric-implementatie met PowerShell
description: Meer informatie over het verwijderen en implementeren van toepassingen in Azure Service Fabric en hoe u deze acties uitvoeren in Powershell.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: e3fdd194f2949f1246e991968e02b3278f33f7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282508"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Toepassingen implementeren en verwijderen met PowerShell

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [Powershell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric-CLI](service-fabric-application-lifecycle-sfctl.md)
> * [FabricClient-API's](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Zodra een [toepassingstype is verpakt,][10]is het klaar voor implementatie in een Azure Service Fabric-cluster. Implementatie omvat de volgende drie stappen:

1. Upload het toepassingspakket naar de afbeeldingswinkel.
2. Registreer het toepassingstype met het relatieve pad van het afbeeldingsarchief.
3. De toepassingsinstantie maken.

Zodra de geïmplementeerde toepassing niet meer nodig is, u de toepassingsinstantie en het toepassingstype verwijderen. Als u een toepassing volledig uit het cluster wilt verwijderen, worden de volgende stappen gevolgd:

1. Verwijder (of verwijder) de lopende toepassingsinstantie.
2. Meld het toepassingstype uit als u het niet meer nodig hebt.
3. Verwijder het toepassingspakket uit het afbeeldingsarchief.

Als u Visual Studio gebruikt voor het implementeren en debuggen van toepassingen op uw lokale ontwikkelingscluster, worden alle voorgaande stappen automatisch afgehandeld via een PowerShell-script.  Dit script is te vinden in de map *Scripts* van het toepassingsproject. In dit artikel vindt u achtergrondinformatie over wat dat script doet, zodat u dezelfde bewerkingen buiten Visual Studio uitvoeren. 

Een andere manier om een toepassing te implementeren is door externe voorzieningen te gebruiken. Het applicatiepakket kan worden [verpakt als `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) en geüpload naar een externe winkel. In dit geval is uploaden naar de afbeeldingswinkel niet nodig. Implementatie vereist de volgende stappen:

1. Upload `sfpkg` de naar een externe winkel. De externe winkel kan elke winkel zijn die een REST http- of https-eindpunt blootlegt.
2. Registreer het toepassingstype met behulp van de externe download-URI en de informatie over het type toepassing.
2. De toepassingsinstantie maken.

Verwijder de toepassingsinstanties voor opruiming en het uitschrijven van het toepassingstype. Omdat het pakket niet is gekopieerd naar de afbeeldingswinkel, is er geen tijdelijke locatie om op te schonen. Inrichten vanuit de externe winkel is beschikbaar vanaf Service Fabric versie 6.1.

>[!NOTE]
> Visual Studio biedt momenteel geen ondersteuning voor externe voorzieningen.

 

## <a name="connect-to-the-cluster"></a>Verbinding maken met het cluster

Voordat u powershell-opdrachten uitvoert in dit artikel, begint u altijd met [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) om verbinding te maken met het cluster ServiceFabric. Voer het volgende uit om verbinding te maken met het cluster lokale ontwikkeling:

```powershell
Connect-ServiceFabricCluster
```

Zie Verbinding maken met een beveiligd cluster voor een extern cluster of cluster met Azure Active Directory- of X509-certificaten of Windows Active Directory voor voorbeelden van verbinding maken met [een extern](service-fabric-connect-to-secure-cluster.md)cluster of cluster dat is beveiligd met Azure Active Directory-, X509-certificaten of Windows Active Directory.

## <a name="upload-the-application-package"></a>Het toepassingspakket uploaden

Als u het toepassingspakket uploadt, wordt het op een locatie geplaatst die toegankelijk is voor interne Service Fabric-componenten.
Als u het toepassingspakket lokaal wilt verifiëren, gebruikt u de cmdlet [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)

Met de opdracht [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) wordt het toepassingspakket geüpload naar het clusterimagearchief.

Stel dat u een toepassing met de naam *MyApplication* bouwt en verpakt in Visual Studio 2015. Standaard is de naam van het toepassingstype in applicationmanifest.xml "MyApplicationType".  Het toepassingspakket, dat het benodigde toepassingsmanifest, servicemanifesten en code/config/datapakketten bevat, bevindt zich in *C:\Gebruikersgebruikersnaam\<\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

In de volgende opdracht worden de inhoud van het toepassingspakket weergegeven:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Als het toepassingspakket groot is en/of veel bestanden heeft, u [het comprimeren.](service-fabric-package-apps.md#compress-a-package) De compressie vermindert de grootte en het aantal bestanden.
Het neveneffect is dat het registreren en niet registreren van het toepassingstype sneller gaat. De uploadtijd kan momenteel trager zijn, vooral als u de tijd opneemt om het pakket te comprimeren. 

Als u een pakket wilt comprimeren, gebruikt u dezelfde opdracht [Copy-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) Compressie kan afzonderlijk van upload, `SkipCopy` met behulp van de vlag of samen met de uploadbewerking worden uitgevoerd. Het toepassen van compressie op een gecomprimeerdpakket is no-op.
Als u een gecomprimeerde verpakking wilt ontcomprimeren, gebruikt `UncompressPackage` u dezelfde opdracht [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) met de switch.

Met de volgende cmdlet wordt de verpakking gecomprimeerd zonder deze naar het afbeeldingsarchief te kopiëren. Het pakket bevat nu zip-bestanden voor de `Code` en `Config` pakketten. De toepassing en de servicemanifesten worden niet gezipt, omdat ze nodig zijn voor veel interne bewerkingen (zoals het delen van pakketten, de naam van het toepassingstype en de extractie van versies voor bepaalde validaties). Het ritsen van de manifesten zou deze operaties inefficiënt maken.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

Voor grote toepassingspakketten kost de compressie tijd. Voor de beste resultaten, gebruik maken van een snelle SSD-schijf. De compressietijden en de grootte van het gecomprimeerde pakket verschillen ook op basis van de inhoud van het pakket.
Hier zijn bijvoorbeeld compressiestatistieken voor sommige pakketten, die de initiële en de gecomprimeerde pakketgrootte weergeven, met de compressietijd.

|Initiële grootte (MB)|Aantal bestanden|Compressietijd|Gecomprimeerde pakketgrootte (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Zodra een pakket is gecomprimeerd, kan het worden geüpload naar een of meerdere Service Fabric-clusters als dat nodig is. Het implementatiemechanisme is hetzelfde voor gecomprimeerde en niet-gecomprimeerde pakketten. Gecomprimeerde pakketten worden als zodanig opgeslagen in het clusterimagearchief. De pakketten worden niet gecomprimeerd op het knooppunt, voordat de toepassing wordt uitgevoerd.


In het volgende voorbeeld wordt het pakket naar de afbeeldingswinkel geüpload naar een map met de naam "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Als u de parameter *-ApplicationPackagePathInImageStore* niet opgeeft, wordt het toepassingspakket gekopieerd naar de map Foutopsporing in het afbeeldingsarchief.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** detecteert automatisch de juiste verbindingstekenreeks voor het opslaan van afbeeldingen als de PowerShell-sessie is verbonden met een Service Fabric-cluster. Voor Service Fabric-versies ouder dan 5.6 moet het argument **-ImageStoreConnectionString** expliciet worden opgegeven.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>De **get-ImageStoreConnectionStringFromClusterManifest-cmdlet,** die deel uitmaakt van de SDK PowerShell-module servicestructuur, wordt gebruikt om de verbindingstekenreeks voor de afbeeldingswinkel te krijgen.  Voer het als u de SDK-module wilt importeren:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Zie [De verbindingstekenreeks voor afbeeldingsarchiefen begrijpen](service-fabric-image-store-connection-string.md) voor aanvullende informatie over de verbindingstekenreeks voor afbeeldingsopslag en afbeeldingsarchief.
>
>
>

De tijd die nodig is om een pakket te uploaden verschilt afhankelijk van meerdere factoren. Enkele van deze factoren zijn het aantal bestanden in het pakket, de grootte van het pakket en de bestandsgrootte. De netwerksnelheid tussen de bronmachine en het cluster Service Fabric heeft ook invloed op de uploadtijd. De standaard time-out voor [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) is 30 minuten.
Afhankelijk van de beschreven factoren moet u mogelijk de time-out verhogen. Als u het pakket in de kopieeroproep comprimeert, moet u ook rekening houden met de compressietijd.



## <a name="register-the-application-package"></a>Het aanvraagpakket registreren

Het toepassingstype en de versie die in het toepassingsmanifest zijn aangegeven, worden beschikbaar voor gebruik wanneer het toepassingspakket wordt geregistreerd. Het systeem leest het pakket dat in de vorige stap is geüpload, verifieert het pakket, verwerkt de inhoud van het pakket en kopieert het verwerkte pakket naar een interne systeemlocatie.  

Voer de cmdlet [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) uit om het toepassingstype in het cluster te registreren en beschikbaar te maken voor implementatie:

### <a name="register-the-application-package-copied-to-image-store"></a>Het toepassingspakket registreren dat is gekopieerd naar het afbeeldingsarchief

Wanneer een pakket eerder naar het afbeeldingsarchief is gekopieerd, geeft de registerbewerking het relatieve pad in het afbeeldingsarchief op.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" is de map in de afbeeldingswinkel waar het toepassingspakket zich bevindt. Het toepassingstype met de naam "MyApplicationType" en versie "1.0.0" (beide zijn te vinden in het toepassingsmanifest) is nu geregistreerd in het cluster.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Het toepassingspakket registreren dat naar een extern archief is gekopieerd

Vanaf Service Fabric versie 6.1 ondersteunt provision het downloaden van het pakket vanuit een externe winkel. De download URI vertegenwoordigt het pad naar het [ `sfpkg` applicatiepakket](service-fabric-package-apps.md#create-an-sfpkg) van waaruit het applicatiepakket kan worden gedownload met BEHULP van HTTP- of HTTPS-protocollen. Het pakket moet eerder zijn geüpload naar deze externe locatie. De URI moet READ-toegang toestaan, zodat Service Fabric het bestand kan downloaden. Het `sfpkg` bestand moet de extensie ".sfpkg" hebben. De bepalingsoperatie moet de informatie over het type toepassing bevatten, zoals te vinden in het toepassingsmanifest.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

De opdracht [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) retourneert pas nadat het systeem het aanvraagpakket heeft geregistreerd. Hoe lang de registratie duurt, is afhankelijk van de grootte en inhoud van het aanvraagpakket. Indien nodig kan de parameter **-TimeoutSec** worden gebruikt om een langere time-out te geven (de standaardtime-out is 60 seconden).

Als u een groot toepassingspakket hebt of als u time-outs ondervindt, gebruikt u de parameter **-Async.** De opdracht keert terug wanneer het cluster de opdracht register accepteert. De registeroperatie gaat zo nodig door.
Met de opdracht [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) worden de versies van het toepassingstype en de registratiestatus weergegeven. U deze opdracht gebruiken om te bepalen wanneer de registratie is gedaan.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Een toepassingspakket verwijderen uit het afbeeldingsarchief

Als een pakket is gekopieerd naar het afbeeldingsarchief, moet u het verwijderen van de tijdelijke locatie nadat de toepassing is geregistreerd. Als u toepassingspakketten uit de afbeeldingswinkel verwijderde, worden systeembronnen vrijgemaakt. Het bijhouden van ongebruikte toepassingspakketten verbruikt schijfopslag en leidt tot problemen met de prestaties van toepassingen.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>De toepassing maken

U een toepassing van elke versie van het toepassingstype die met succes is geregistreerd, met behulp van de cmdlet [Nieuw-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) instantiëren. De naam van elke toepassing moet beginnen met het *"fabric:"-schema* en moet uniek zijn voor elke toepassingsinstantie. Er worden ook standaardservices gemaakt die zijn gedefinieerd in het toepassingsmanifest van het doeltoepassingstype.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Voor een bepaalde versie van een geregistreerd toepassingstype kunnen meerdere toepassingsexemplaren worden gemaakt. Elke toepassingsinstantie wordt afzonderlijk uitgevoerd, met een eigen werkmap en -proces.

Als u wilt zien welke benoemde apps en services in het cluster worden uitgevoerd, voert u de cmdlets [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) en [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) uit:

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Een toepassing verwijderen

Wanneer een toepassingsinstantie niet meer nodig is, u deze permanent op naam verwijderen met de cmdlet [Remove-ServiceFabricApplication.](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) verwijdert automatisch alle services die deel uitmaken van de toepassing, waardoor alle servicestatus permanent wordt verwijderd. 

> [!WARNING]
> Deze bewerking kan niet worden teruggedraaid en de toepassingsstatus kan niet worden hersteld.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Een toepassingstype uitschrijven

Wanneer een bepaalde versie van een toepassingstype niet meer nodig is, moet u het toepassingstype uitschrijven met de cmdlet [Unregister-ServiceFabricApplicationType.](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) Als u niet-gebruikte toepassingstypen verwijdert, wordt opslagruimte vrijgegeven die door het afbeeldingsarchief wordt gebruikt door de bestanden van het toepassingstype te verwijderen. Als u een toepassingstype uitschrijft, wordt het toepassingspakket dat naar de tijdelijke locatie van het afbeeldingsarchief is gekopieerd, niet verwijderd als kopiëren naar het afbeeldingsarchief is gebruikt. Een toepassingstype kan worden niet geregistreerd zolang er geen toepassingen tegen worden geinstantieerd en er geen toepassingsupgrades in behandeling zijn, waarnaar wordt verwezen.

Voer [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) uit om de toepassingstypen te bekijken die momenteel in het cluster zijn geregistreerd:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Voer [Unregister-ServiceFabricApplicationType uit](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) om een specifiek toepassingstype uit te schrijven:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage vraagt om een ImageStoreConnectionString

De SERVICE Fabric SDK-omgeving moet al de juiste standaardinstellingen hebben ingesteld. Maar indien nodig moet de ImageStoreConnectionString voor alle opdrachten overeenkomen met de waarde die het cluster ServiceFabric gebruikt. U de ImageStoreConnectionString vinden in het clustermanifest, opgehaald met de opdrachten [Get-ServiceClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) en Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

De **get-ImageStoreConnectionStringFromClusterManifest-cmdlet,** die deel uitmaakt van de SDK PowerShell-module servicestructuur, wordt gebruikt om de verbindingstekenreeks voor de afbeeldingswinkel te krijgen.  Voer het als u de SDK-module wilt importeren:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

De ImageStoreConnectionString is te vinden in het clustermanifest:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Zie [De verbindingstekenreeks voor afbeeldingsarchiefen begrijpen](service-fabric-image-store-connection-string.md) voor aanvullende informatie over de verbindingstekenreeks voor afbeeldingsopslag en afbeeldingsarchief.

### <a name="deploy-large-application-package"></a>Groot toepassingspakket implementeren

Probleem: [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) times out voor een groot applicatiepakket (volgorde van GB).
Proberen:
- Geef een grotere time-out op voor de `TimeoutSec` opdracht [Copy-ServiceFabricApplicationPackage,](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) met parameter. Standaard is de time-out 30 minuten.
- Controleer de netwerkverbinding tussen uw bronmachine en cluster. Als de verbinding traag is, u overwegen een machine met een betere netwerkverbinding te gebruiken.
Als de clientmachine zich in een andere regio dan het cluster bevindt, u overwegen een clientmachine te gebruiken in een dichtere of dezelfde regio als het cluster.
- Controleer of u externe beperking raakt. Wanneer het afbeeldingsarchief bijvoorbeeld is geconfigureerd om azure-opslag te gebruiken, kan het uploaden worden beperkt.

Probleem: Uploadpakket succesvol voltooid, maar [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) heeft een keer uit. Proberen:
- [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u kopieert naar het afbeeldingsarchief.
De compressie vermindert de grootte en het aantal bestanden, wat op zijn beurt de hoeveelheid verkeer en werk vermindert die Service Fabric moet uitvoeren. De uploadbewerking kan trager zijn (vooral als u de compressietijd opneemt), maar het registratie en niet-registreren van het toepassingstype gaat sneller.
- Geef een grotere time-out op voor `TimeoutSec` [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) met parameter.
- Schakel `Async` de schakelaar op [voor Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). De opdracht wordt geretourneerd wanneer het cluster de opdracht accepteert en de registratie van het toepassingstype asynchroon wordt voortgezet. Om deze reden is het in dit geval niet nodig om een hogere time-out op te geven. De opdracht [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) bevat alle met succes geregistreerde versies van het toepassingstype en de registratiestatus. U deze opdracht gebruiken om te bepalen wanneer de registratie is gedaan.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Toepassingspakket implementeren met veel bestanden

Probleem: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) geeft een uitkijk voor een aanvraagpakket met veel bestanden (order van duizenden).
Proberen:
- [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u kopieert naar het afbeeldingsarchief. De compressie vermindert het aantal bestanden.
- Geef een grotere time-out op voor `TimeoutSec` [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) met parameter.
- Schakel `Async` de schakelaar op [voor Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). De opdracht wordt geretourneerd wanneer het cluster de opdracht accepteert en de registratie van het toepassingstype asynchroon wordt voortgezet.
Om deze reden is het in dit geval niet nodig om een hogere time-out op te geven. De opdracht [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) bevat alle met succes geregistreerde versies van het toepassingstype en de registratiestatus. U deze opdracht gebruiken om te bepalen wanneer de registratie is gedaan.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Volgende stappen

[Toepassingspakket maken](service-fabric-package-apps.md)

[Upgrade van servicefabric-toepassingen](service-fabric-application-upgrade.md)

[Service Fabric health introduction](service-fabric-health-introduction.md)

[Een servicefabric-service diagnosticeren en oplossen](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Een toepassing modelleren in Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
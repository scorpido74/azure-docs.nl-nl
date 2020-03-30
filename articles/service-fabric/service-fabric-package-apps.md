---
title: Een Azure Service Fabric-app verpakken
description: Meer informatie over het verpakken van een Azure Service Fabric-toepassing en hoe u zich voorbereiden op implementatie naar een cluster.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7c99eec28ac06ecf666d6dda1015f889841a5dbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258341"
---
# <a name="package-an-application"></a>Toepassingspakket maken

In dit artikel wordt beschreven hoe u een Service Fabric-toepassing verpakken en klaar maakt voor implementatie.

## <a name="package-layout"></a>Pakketindeling

Het toepassingsmanifest, een of meer servicemanifesten en andere benodigde pakketbestanden moeten worden georganiseerd in een specifieke lay-out voor implementatie in een Service Fabric-cluster. De voorbeeldmanifesten in dit artikel moeten worden georganiseerd in de volgende directorystructuur:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

De mappen worden benoemd naar de **naamkenmerken** van elk corresponderend element. Als het servicemanifest bijvoorbeeld twee codepakketten bevat met de namen **MyCodeA** en **MyCodeB,** bevatten twee mappen met dezelfde namen de benodigde binaire bestanden voor elk codepakket.

## <a name="use-setupentrypoint"></a>SetupEntryPoint gebruiken

Typische scenario's voor het gebruik van **SetupEntryPoint** zijn wanneer u een uitvoerbare moet uitvoeren voordat de service wordt gestart of wanneer u een bewerking met verhoogde bevoegdheden moet uitvoeren. Bijvoorbeeld:

* Het instellen en initialiseren van omgevingsvariabelen die de service uitvoerbaar heeft. Het is niet beperkt tot alleen executables geschreven via de Service Fabric programmeermodellen. NPM.exe heeft bijvoorbeeld een aantal omgevingsvariabelen nodig die zijn geconfigureerd voor het implementeren van een node.js-toepassing.
* Toegangsbeheer instellen door beveiligingscertificaten te installeren.

Zie Het [beleid configureren voor een ingangspunt voor het instellen van een serviceinstelling](service-fabric-application-runas-security.md) voor meer informatie over het configureren van het **SetupEntryPoint.**

<a id="Package-App"></a>

## <a name="configure"></a>Configureren

### <a name="build-a-package-by-using-visual-studio"></a>Een pakket maken met Visual Studio

Als u Visual Studio hebt gebruikt om uw toepassing te maken, u de opdracht *Pakket* gebruiken om automatisch een pakket te maken dat overeenkomt met de hierboven beschreven lay-out.

Als u een pakket wilt maken, klikt u met de rechtermuisknop op het toepassingsproject in *Solution Explorer* en kiest u de opdracht **Pakket:**

![Een toepassing verpakken met Visual Studio][vs-package-command]

Wanneer de verpakking is voltooid, u de locatie van de verpakking vinden in het **venster Uitvoer.** De verpakkingsstap vindt automatisch plaats wanneer u uw toepassing implementeert of debugt in Visual Studio.

### <a name="build-a-package-by-command-line"></a>Een pakket bouwen op opdrachtregel

Het is ook mogelijk om uw toepassing `msbuild.exe`programmatisch in te verpakken met behulp van . Onder de motorkap draait Visual Studio, zodat de uitvoer hetzelfde is.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Test het pakket

U de pakketstructuur lokaal verifiëren via PowerShell met de opdracht [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)
Deze opdracht controleert op manifeste ontkaderingsproblemen en controleert alle verwijzingen. Deze opdracht controleert alleen de structurele correctheid van de mappen en bestanden in het pakket.
Het controleert geen van de code of de inhoud van het gegevenspakket dan het controleren van of alle noodzakelijke dossiers aanwezig zijn.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Deze fout geeft aan dat het *bestand MySetup.bat* waarnaar wordt verwezen in het servicemanifest **SetupEntryPoint** ontbreekt in het codepakket. Nadat het ontbrekende bestand is toegevoegd, verloopt de verificatie van de toepassing:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Als uw toepassing [toepassingsparameters](service-fabric-manage-multiple-environment-app-configuration.md) heeft gedefinieerd, u deze doorgeven in [Test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) voor de juiste validatie.

Als u het cluster weet waar de toepassing wordt geïmplementeerd, `ImageStoreConnectionString` wordt aanbevolen dat u de parameter passeert. In dit geval wordt het pakket ook gevalideerd ten opzichte van eerdere versies van de toepassing die al in het cluster worden uitgevoerd. De validatie kan bijvoorbeeld detecteren of een pakket met dezelfde versie, maar andere inhoud al is geïmplementeerd.  

Zodra de toepassing correct is verpakt en de validatie is doorstaat, u overwegen het pakket te comprimeren voor snellere implementatiebewerkingen.

## <a name="compress-a-package"></a>Een pakket comprimeren

Wanneer een pakket groot is of veel bestanden heeft, u het comprimeren voor een snellere implementatie. Compressie vermindert het aantal bestanden en de grootte van het pakket.
Voor een gecomprimeerd toepassingspakket kan [het uploaden van het toepassingspakket](service-fabric-deploy-remove-applications.md#upload-the-application-package) langer duren in vergelijking met het uploaden van het niet-gecomprimeerde pakket, vooral als compressie wordt gedaan als onderdeel van kopiëren. Met compressie, [registreren](service-fabric-deploy-remove-applications.md#register-the-application-package) en [niet-registreren van de toepassing type](service-fabric-deploy-remove-applications.md#unregister-an-application-type) zijn sneller.

Het implementatiemechanisme is hetzelfde voor gecomprimeerde en niet-gecomprimeerde pakketten. Als het pakket wordt gecomprimeerd, wordt het als zodanig opgeslagen in het clusterimagearchief en wordt het niet gecomprimeerd op het knooppunt voordat de toepassing wordt uitgevoerd.
De compressie vervangt het geldige Service Fabric-pakket door de gecomprimeerde versie. De map moet schrijfmachtigingen toestaan. Het uitvoeren van compressie op een al gecomprimeerd pakket levert geen wijzigingen op.

U een pakket comprimeren door de Powershell-opdracht `CompressPackage` [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) met schakelaar uit te voeren. U het pakket met dezelfde `UncompressPackage` opdracht decomprimeeren met behulp van de schakelaar.

Met de volgende opdracht comprimeert u het pakket zonder het te kopiëren naar het afbeeldingsarchief. U een gecomprimeerd pakket naar een of meer Service Fabric-clusters kopiëren, `SkipCopy` indien nodig, met [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) zonder de vlag.
Het pakket bevat nu zip-bestanden voor de `code`, `config`, en `data` pakketten. Het toepassingsmanifest en de servicemanifesten worden niet gezipt, omdat ze nodig zijn voor veel interne bewerkingen. Pakketdelen, naam van het toepassingstype en versieextractie voor bepaalde validaties moeten bijvoorbeeld allemaal toegang krijgen tot de manifesten. Het ritsen van de manifesten zou deze operaties inefficiënt maken.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

U het pakket ook comprimeren en kopiëren met [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) in één stap.
Als het pakket groot is, biedt u een time-out die hoog genoeg is om tijd te hebben voor zowel de pakketcompressie als het uploaden naar het cluster.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Intern berekent Service Fabric checksums voor de toepassingspakketten voor validatie. Bij het gebruik van compressie worden de checksums berekend op de ritsversies van elk pakket. Het genereren van een nieuwe zip van hetzelfde toepassingspakket creëert verschillende checksums. Als u validatiefouten wilt voorkomen, gebruikt u [diff provisioning](service-fabric-application-upgrade-advanced.md). Met deze optie worden de ongewijzigde pakketten niet opgenomen in de nieuwe versie. Verwijs ze in plaats daarvan rechtstreeks vanuit het nieuwe servicemanifest.

Als diff provisioning geen optie is en u de pakketten `code` `config`moet `data` opnemen, genereert u nieuwe versies voor de , en pakketten om een mismatch met de checksum te voorkomen. Het genereren van nieuwe versies voor ongewijzigde pakketten is noodzakelijk wanneer een gecomprimeerd pakket wordt gebruikt, ongeacht of de vorige versie compressie gebruikt of niet.

Het pakket is nu correct verpakt, gevalideerd en gecomprimeerd (indien nodig), zodat het klaar is voor [implementatie](service-fabric-deploy-remove-applications.md) naar een of meer Service Fabric-clusters.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Pakketten comprimeren bij implementatie met Visual Studio

U Visual Studio instrueren om pakketten `CopyPackageParameters` bij implementatie te comprimeren door `CompressPackage` het `true`element toe te voegen aan uw publicatieprofiel en het kenmerk in te stellen op.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Maak een sfpkg

Service Fabric begint met versie 6.1 en maakt het mogelijk om vanuit een externe winkel te worden ingericht.
Met deze optie hoeft het toepassingspakket niet naar de afbeeldingswinkel te worden gekopieerd. In plaats daarvan `sfpkg` kunt u een en uploaden naar een externe winkel, dan bieden de download URI naar Service Fabric bij het inrichten. Hetzelfde pakket kan worden ingericht in meerdere clusters. Inrichten vanuit het externe archief bespaart de tijd die nodig is om het pakket naar elk cluster te kopiëren.

Het `sfpkg` bestand is een zip die het initiële toepassingspakket bevat en de extensie ".sfpkg" heeft.
Binnen de rits kan het toepassingspakket worden gecomprimeerd of ongecomprimeerd. De compressie van het applicatiepakket in de zip wordt gedaan op code, config, en data pakket niveaus, zoals [eerder vermeld](service-fabric-package-apps.md#compress-a-package).

Als u `sfpkg`een map wilt maken, begint u met een map die het oorspronkelijke toepassingspakket bevat, al dan niet gecomprimeerd. Gebruik vervolgens elk hulpprogramma om de map te zippen met de extensie ".sfpkg". Gebruik bijvoorbeeld [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

Deze `sfpkg` moet worden geüpload naar de externe winkel buiten de band, buiten Service Fabric. De externe winkel kan elke winkel zijn die een REST http- of https-eindpunt blootlegt. Tijdens het inrichten voert Service Fabric een `sfpkg` GET-bewerking uit om het toepassingspakket te downloaden, dus de winkel moet LEES-toegang voor het pakket toestaan.

Als u het pakket wilt inrichten, gebruikt u externe voorzieningen, waarvoor de download-URI- en toepassingstype-informatie vereist is.

>[!NOTE]
> Inrichten op basis van het relatieve `sfpkg` pad van image store biedt momenteel geen ondersteuning voor bestanden. Daarom mag `sfpkg` deze niet worden gekopieerd naar het afbeeldingsarchief.

## <a name="next-steps"></a>Volgende stappen

[Toepassingen implementeren en verwijderen][10] beschrijft hoe u PowerShell gebruiken om toepassingsinstanties te beheren

[Als u toepassingsparameters voor meerdere omgevingen beheert,][11] wordt beschreven hoe u parameters en omgevingsvariabelen voor verschillende toepassingsinstanties configureert.

[Configureer beveiligingsbeleid voor uw toepassing][12] beschrijft hoe services worden uitgevoerd onder beveiligingsbeleid om de toegang te beperken.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md
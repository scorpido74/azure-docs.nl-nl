---
title: De versie van een zelfstandig cluster upgraden
description: Upgrade de Azure Service Fabric-code waarop een zelfstandig servicefabriccluster wordt uitgevoerd.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 489a90180454e2b4a9dad34730fbd3c4f235a2ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598099"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>De Service Fabric-versie upgraden die op uw cluster wordt uitgevoerd 

Voor elk modern systeem, de mogelijkheid om te upgraden is de sleutel tot het succes op lange termijn van uw product. Een Azure Service Fabric-cluster is een bron waarvan u eigenaar bent. In dit artikel wordt beschreven hoe u de versie van Service Fabric upgraden die op uw zelfstandige cluster wordt uitgevoerd.

> [!NOTE]
> Zorg ervoor dat uw cluster altijd een ondersteunde Service Fabric-versie uitvoert. Wanneer Microsoft de release van een nieuwe versie van Service Fabric aankondigt, wordt de vorige versie gemarkeerd voor het einde van de ondersteuning na een minimum van 60 dagen vanaf de datum van de aankondiging. Nieuwe releases worden aangekondigd [op de Service Fabric team blog](https://blogs.msdn.microsoft.com/azureservicefabric/). De nieuwe release is beschikbaar om te kiezen op dat punt.
>
>

U uw cluster alleen upgraden naar de nieuwe versie als u een nodeconfiguratie in productiestijl gebruikt, waarbij elk servicestructuurknooppunt wordt toegewezen aan een afzonderlijke fysieke of virtuele machine. Als u een ontwikkelcluster hebt, waarbij meer dan één Service Fabric-knooppunt zich op één fysieke of virtuele machine bevindt, moet u het cluster opnieuw maken met de nieuwe versie.

Twee verschillende werkstromen kunnen uw cluster upgraden naar de nieuwste versie of een ondersteunde Service Fabric-versie. Een workflow is voor clusters die connectiviteit hebben om de nieuwste versie automatisch te downloaden. De andere workflow is voor clusters die geen connectiviteit hebben om de nieuwste Service Fabric-versie te downloaden.

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>Automatische upgrade van de Service Fabric-versie van uw cluster inschakelen
Als u wilt instellen dat uw cluster updates van Service `fabricClusterAutoupgradeEnabled` Fabric downloadt wanneer Microsoft een nieuwe versie uitbrengt, stelt u de clusterconfiguratie in *op true.* Als u handmatig een ondersteunde versie van Service Fabric wilt selecteren `fabricClusterAutoupgradeEnabled` waarop uw cluster moet worden ingeschakeld, stelt u de clusterconfiguratie in op *false.*

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Clusters upgraden met connectiviteit om de nieuwste code en configuratie te downloaden
Gebruik deze stappen om uw cluster te upgraden naar een ondersteunde versie als uw clusterknooppunten een internetverbinding hebben met het [Microsoft Download Center.](https://download.microsoft.com)

Voor clusters met connectiviteit met het [Microsoft Download Center](https://download.microsoft.com)controleert Microsoft regelmatig op de beschikbaarheid van nieuwe Service Fabric-versies.

Wanneer een nieuwe Service Fabric-versie beschikbaar is, wordt het pakket lokaal gedownload naar het cluster en ingericht voor een upgrade. Bovendien, om de klant te informeren over deze nieuwe versie, het systeem toont een expliciete cluster status waarschuwing die vergelijkbaar is met de volgende:

"De huidige clusterversie [versie #] ondersteuning eindigt [datum]."

Nadat het cluster de nieuwste versie heeft uitgevoerd, verdwijnt de waarschuwing.

Wanneer u de waarschuwing voor de clusterstatus ziet, u het cluster upgraden:

1. Maak verbinding met het cluster vanaf elke machine die beheerderstoegang heeft tot alle machines die worden vermeld als knooppunten in het cluster. De machine waarop dit script wordt uitgevoerd, hoeft geen deel uit te maken van het cluster.

    ```powershell
    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Download de lijst met Service Fabric-versies waarnaar u upgraden.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    Je moet een output vergelijkbaar met deze:

    ![Service Fabric-versies krijgen][getfabversions]
3. Start een clusterupgrade naar een beschikbare versie met de opdracht [Start-ServiceFabricUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   Als u de voortgang van de upgrade wilt controleren, u Service Fabric Explorer gebruiken of de volgende PowerShell-opdracht uitvoeren:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Als niet aan het clusterstatusbeleid wordt voldaan, wordt de upgrade teruggedraaid. Zie de documentatie voor [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade)als u aangepaste statusbeleidsregels wilt opgeven voor de opdracht Start-ServiceFabricClusterUpgrade.

    Nadat u de problemen hebt opgelost die tot de terugdraaiing hebben geleid, start u de upgrade opnieuw door dezelfde stappen te volgen als eerder beschreven.

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>Clusters upgraden die *geen connectiviteit* hebben om de nieuwste code en configuratie te downloaden
Gebruik deze stappen om uw cluster te upgraden naar een ondersteunde versie als uw clusterknooppunten geen internetverbinding hebben met het [Microsoft Download Center.](https://download.microsoft.com)

> [!NOTE]
> Als u een cluster uitvoert dat niet is verbonden met internet, moet u de blog van het [Service Fabric-team](https://blogs.msdn.microsoft.com/azureservicefabric/) controleren om meer te weten te komen over nieuwe releases. Het systeem geeft geen waarschuwing voor de status van het cluster weer om u te waarschuwen voor nieuwe releases.  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>Automatische inrichting versus handmatige inrichting
Als u automatisch downloaden en registreren voor de nieuwste codeversie wilt inschakelen, stelt u de Service Fabric Update Service in. Zie *Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt* voor instructies in het [standalone pakket](service-fabric-cluster-standalone-package-contents.md).

Volg deze instructies voor het handmatige proces.

Wijzig uw clusterconfiguratie om de volgende eigenschap *op false* in te stellen voordat u een configuratie-upgrade start:

```json
"fabricClusterAutoupgradeEnabled": false,
```

Zie de [powerShell-opdracht Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) voor gebruiksdetails. Zorg ervoor dat u 'clusterConfigurationVersion' in uw JSON bijwerkt voordat u de configuratie-upgrade start.

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>Werkstroom voor clusterupgrade

1. Voer [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) uit vanaf een van de knooppunten in het cluster en noteer de *TargetCodeVersion*.

2. Voer het volgende uit vanaf een machine met internetverbinding om alle versies met upgradecompatibel met de huidige versie weer te geven en het bijbehorende pakket te downloaden van de bijbehorende downloadkoppelingen:

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Maak verbinding met het cluster vanaf elke machine die beheerderstoegang heeft tot alle machines die worden vermeld als knooppunten in het cluster. De machine waarop dit script wordt uitgevoerd, hoeft geen deel uit te maken van het cluster.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. Kopieer het gedownloade pakket naar het clusterimagearchief.

5. Registreer het gekopieerde pakket.

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. Start een clusterupgrade naar een beschikbare versie.

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    U de voortgang van de upgrade in Service Fabric Explorer controleren of u de volgende PowerShell-opdracht uitvoeren:

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    Als niet aan het clusterstatusbeleid wordt voldaan, wordt de upgrade teruggedraaid. Zie de documentatie voor [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade)als u aangepaste statusbeleidsregels wilt opgeven voor de opdracht Start-ServiceFabricClusterUpgrade.

    Nadat u de problemen hebt opgelost die tot de terugdraaiing hebben geleid, start u de upgrade opnieuw door dezelfde stappen te volgen als eerder beschreven.

## <a name="next-steps"></a>Volgende stappen
* [De configuratie van een zelfstandig cluster bijwerken](service-fabric-cluster-config-upgrade-windows-server.md)
* Sommige [clusterinstellingen van ServiceFabric](service-fabric-cluster-fabric-settings.md)aanpassen .
* [Schaal je cluster in en uit.](service-fabric-cluster-scale-up-down.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

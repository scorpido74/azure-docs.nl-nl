---
title: De configuratie van een zelfstandig cluster bijwerken
description: Meer informatie over het upgraden van de configuratie waarop een zelfstandig Service Fabric-cluster wordt uitgevoerd.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 8e7e01dac29cb9ba91c83270dac4e46c73b2089e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610116"
---
# <a name="upgrade-the-configuration-of-a-standalone-cluster"></a>De configuratie van een zelfstandig cluster bijwerken 

Voor elk modern systeem, de mogelijkheid om te upgraden is de sleutel tot het succes op lange termijn van uw product. Een Azure Service Fabric-cluster is een bron waarvan u eigenaar bent. In dit artikel wordt beschreven hoe u de configuratie-instellingen van uw zelfstandige Service Fabric-cluster upgraden.

## <a name="customize-cluster-settings-in-the-clusterconfigjson-file"></a>Clusterinstellingen aanpassen in het clusterconfig.json-bestand
Zelfstandige clusters worden geconfigureerd via het *clusterconfig.json-bestand.* Zie [Configuratie-instellingen voor een zelfstandig Windows-cluster voor](service-fabric-cluster-manifest.md)meer informatie over de verschillende instellingen.

U instellingen toevoegen, bijwerken `fabricSettings` of verwijderen in de sectie onder de sectie [Clustereigenschappen](./service-fabric-cluster-manifest.md#cluster-properties) in *ClusterConfig.json*. 

De volgende JSON voegt bijvoorbeeld een nieuwe instelling *MaxDiskQuotaInMB* toe aan de sectie *Diagnostische gegevens* onder `fabricSettings`:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Nadat u de instellingen in uw ClusterConfig.json-bestand hebt gewijzigd, [test u de clusterconfiguratie](#test-the-cluster-configuration) en [upgradet u de clusterconfiguratie](#upgrade-the-cluster-configuration) om de instellingen op uw cluster toe te passen. 

## <a name="test-the-cluster-configuration"></a>De clusterconfiguratie testen
Voordat u de configuratie-upgrade start, u uw nieuwe clusterconfiguratie JSON testen door het volgende PowerShell-script in het zelfstandige pakket uit te voeren:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File>
```

Of gebruik dit script:

```powershell
TestConfiguration.ps1 -ClusterConfigFilePath <Path to the new Configuration File> -OldClusterConfigFilePath <Path to the old Configuration File> -FabricRuntimePackagePath <Path to the .cab file which you want to test the configuration against>
```

Sommige configuraties kunnen niet worden bijgewerkt, zoals eindpunten, clusternaam, knooppunt-IP, enz. De nieuwe clusterconfiguratie JSON wordt getest op de oude en gooit fouten in het PowerShell-venster als er een probleem is.

## <a name="upgrade-the-cluster-configuration"></a>De clusterconfiguratie bijwerken
Als u de upgrade van de clusterconfiguratie wilt bijwerken, voert u [Start-ServiceFabricClusterConfigurationUpgrade uit](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). De configuratie-upgrade wordt verwerkt upgrade domein door upgrade domein.

```powershell
Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

## <a name="upgrade-cluster-certificate-configuration"></a>Clustercertificaatconfiguratie bijwerken
Een clustercertificaat wordt gebruikt voor verificatie tussen clusterknooppunten. De rollover van het certificaat moet extra voorzichtig worden uitgevoerd omdat een fout de communicatie tussen clusterknooppunten blokkeert.

Vier opties worden ondersteund:  

* Upgrade van één certificaat: het upgradepad is Certificaat A (Primair) -> Certificaat B (Primair) -> Certificaat C (Primair) ->....

* Dubbele certificaatupgrade: het upgradepad is Certificaat A (Primair) -> Certificaat A (primair) en B (secundair) -> Certificaat B (Primair) -> Certificaat B (Primair) en C (Secundair) -> Certificaat C (Primair) ->....

* Upgrade van certificaattype: op duimafdruk gebaseerde certificaatconfiguratie < > op CommonName gebaseerde certificaatconfiguratie. Certificaat Duimafdruk A (Primair) en Duimafdruk B (secundair) -> certificaat CommonName C.

* Duimafdruk-upgrade van de certificaatuitgever: het upgradepad is Certificate CN=A,IssuerThumbprint=IT1 (Primair) -> Certificaat CN=A,IssuerThumbprint=IT1,IT2 (Primair) -> Certificaat CN=A,IssuerThumbprint=IT2 (Primair).


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het aanpassen van bepaalde [clusterinstellingen van Service Fabric](service-fabric-cluster-fabric-settings.md).
* Meer informatie over het [schalen van uw cluster in en uit](service-fabric-cluster-scale-up-down.md).
* Meer informatie over [toepassingsupgrades](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

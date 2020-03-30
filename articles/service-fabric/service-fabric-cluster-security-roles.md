---
title: 'Beveiliging van servicefabric-cluster: clientrollen'
description: In dit artikel worden de twee clientrollen en de machtigingen voor de rollen beschreven.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: abca19e686d39338fcaa2e0b0c8126913135170b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451903"
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Op rollen gebaseerde toegangscontrole voor Service Fabric-clients
Azure Service Fabric ondersteunt twee verschillende typen toegangscontrole voor clients die zijn verbonden met een Service Fabric-cluster: beheerder en gebruiker. Met toegangsbeheer kan de clusterbeheerder de toegang tot bepaalde clusterbewerkingen voor verschillende groepen gebruikers beperken, waardoor het cluster veiliger wordt.  

**Beheerders** hebben volledige toegang tot beheermogelijkheden (inclusief lees-/schrijfmogelijkheden). Standaard hebben **gebruikers** alleen leestoegang tot beheermogelijkheden (bijvoorbeeld querymogelijkheden) en de mogelijkheid om toepassingen en services op te lossen.

U geeft de twee clientrollen (beheerder en client) op het moment van het maken van het cluster op door afzonderlijke certificaten voor elk te verstrekken. Zie [Clusterbeveiliging servicestructuur](service-fabric-cluster-security.md) voor meer informatie over het instellen van een beveiligd Service Fabric-cluster.

## <a name="default-access-control-settings"></a>Instellingen voor standaardtoegangscontrole
Het type toegangsbeheer voor beheerders heeft volledige toegang tot alle FabricClient API's. Het kan alle reads en schrijfbewerkingen uitvoeren op het cluster Service Fabric, inclusief de volgende bewerkingen:

### <a name="application-and-service-operations"></a>Toepassings- en servicebewerkingen
* **CreateService:** servicecreatie                             
* **CreateServiceFromTemplate**: servicecreatie op basis van sjabloon                             
* **UpdateService**: service-updates                             
* **DeleteService**: serviceverwijdering                             
* **ProvisionApplicationType**: inrichting van het toepassingstype                             
* **CreateApplication**: het maken van toepassingen                               
* **DeleteApplication**: verwijderen van toepassingen                             
* **UpgradeToepassing:** toepassingsupgrades starten of onderbreken                             
* **Toepassingstype ontleden:** toepassingstype ontvidieering ongedaan maken                             
* **MoveNextUpgradeDomain:** toepassingsupgrades hervatten met een expliciet updatedomein                             
* **ReportUpgradeHealth**: toepassingsupgrades hervatten met de huidige upgradevoortgang                             
* **ReportHealth**: rapportage van gezondheid                             
* **PredeployPackageToNode**: predeployment API                            
* **CodePackageControl**: codepakketten opnieuw opstarten                             
* **Partitie herstellen:** een partitie herstellen                             
* **Partities**herstellen: partities herstellen                             
* **RecoverServicePartitions:** servicepartities herstellen                             
* **RecoverSystemPartitions**: systeemservicepartities herstellen                             

### <a name="cluster-operations"></a>Clusterbewerkingen
* **ProvisionFabric**: MSI en/of clustermanifestinrichting                             
* **UpgradeFabric:** clusterupgrades starten                             
* **UnprovisionFabric**: MSI en/of clustermanifest niet-inrichten                         
* **MoveNextFabricUpgradeDomain:** clusterupgrades hervatten met een expliciet updatedomein                             
* **ReportFabricUpgradeHealth**: clusterupgrades hervatten met de huidige upgradevoortgang                             
* **StartInfrastructureTask**: infrastructuurtaken starten                             
* **EinddatumInfrastructuurTaak**: infrastructuurtaken voltooien                             
* **InvokeInfrastructureCommand**: opdrachten voor beheer van infrastructuurtaken                              
* **ActivateNode**: een knooppunt activeren                             
* **GedeactiveerdNode**: een knooppunt deactiveren                             
* **NodesBatch deactiveren:** meerdere knooppunten deactiveren                             
* **RemoveNodeDeactivations:** deactivering op meerdere knooppunten terugdraaien                             
* **GetNodeDeactivationStatus**: de status van deactivering controleren                             
* **NodeStateRemoved**: status meldingsknooppunt verwijderd                             
* **ReportFault**: meldingsfout                             
* **FileContent**: overdracht van clientbestanden in image store (extern naar cluster)                             
* **FileDownload**: image store client file download initiatie (extern naar cluster)                             
* **InternalList**: bewerking clientbestandslijst voor image store (intern)                             
* **Verwijderen**: bewerking voor het verwijderen van de client voor image store                              
* **Uploaden**: bewerking voor het uploaden van image store-client                             
* **NodeControl**: knooppunten starten, stoppen en opnieuw starten                             
* **MoveReplicaControl:** replica's verplaatsen van het ene knooppunt naar het andere                             

### <a name="miscellaneous-operations"></a>Diverse verrichtingen
* **Ping:** clientpingen                             
* **Query:** alle query's toegestaan
* **NameExists**: naamgeving URI bestaan controles                             

Het type gebruikerstoegangscontrole is standaard beperkt tot de volgende bewerkingen: 

* **OpsommenSubnamen**: uri-opsomming benoemen                             
* **OpsommenEigenschappen**: naamgeving van de opsomming van onroerend goed                             
* **PropertyReadBatch**: eigenschappenlezen van eigenschappen                             
* **GetServiceBeschrijving**: long-poll service meldingen en het lezen van service beschrijvingen                             
* **ResolveService**: oplossing voor service op basis van klachten                             
* **ResolveNameOwner**: het oplossen van het benoemen van URI-eigenaar                             
* **ResolvePartition**: systeemservices oplossen                             
* **ServiceMeldingen:** servicemeldingen op basis van gebeurtenissen                             
* **GetUpgradeStatus**: upgradestatus polling-toepassing                             
* **GetFabricUpgradeStatus:** status van de upgrade van het pollingcluster                             
* **InvokeInfrastructureQuery**: infrastructuurtaken opvragen                             
* **Lijst**: bewerking clientbestandslijst image store                             
* **ResetPartitionLoad:** belasting opnieuw instellen voor een failover-eenheid                             
* **ToggleVerboseServicePlacementHealthReporting**: toggling verbose service placement health reporting ToggleVerboseServicePlacementHealthReporting : toggling verbose service placement health reporting ToggleVerboseServicePlacementHealthReporting : toggling verbose service placement health reporting Toggle                             

Het beheerderstoegangscontrole heeft ook toegang tot de voorgaande bewerkingen.

## <a name="changing-default-settings-for-client-roles"></a>Standaardinstellingen voor clientrollen wijzigen
In het clustermanifestbestand u de client indien nodig beheerdersmogelijkheden bieden. U de standaardinstellingen wijzigen door tijdens het [maken](service-fabric-cluster-creation-via-portal.md)van het cluster naar de optie **Fabric-instellingen** te gaan en de voorgaande instellingen in de **velden voor de naam,** **beheerder,** **gebruiker**en **waarde** op te geven.

## <a name="next-steps"></a>Volgende stappen
[Beveiliging van het cluster servicefabric](service-fabric-cluster-security.md)

[Het maken van clusters voor servicefabric](service-fabric-cluster-creation-via-portal.md)


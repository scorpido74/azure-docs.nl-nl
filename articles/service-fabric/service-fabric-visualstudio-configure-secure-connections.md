---
title: Beveiligde Azure Service Fabric-clusterverbindingen configureren
description: Meer informatie over het gebruik van Visual Studio om beveiligde verbindingen te configureren die worden ondersteund door het Azure Service Fabric-cluster.
author: cawaMS
ms.topic: conceptual
ms.date: 8/04/2017
ms.author: cawa
ms.openlocfilehash: 11f76153726d3fc92118fb46cc61b4627ab6a1b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464089"
---
# <a name="configure-secure-connections-to-a-service-fabric-cluster-from-visual-studio"></a>Beveiligde verbindingen configureren met een cluster van Servicefabric vanuit Visual Studio
Meer informatie over het gebruik van Visual Studio om veilig toegang te krijgen tot een Azure Service Fabric-cluster met het beheer van toegangsbeheer geconfigureerd.

## <a name="cluster-connection-types"></a>Clusterverbindingstypen
Twee soorten verbindingen worden ondersteund door het Azure Service **Fabric-cluster: niet-beveiligde** verbindingen en **op x509-certificaten gebaseerde** beveiligde verbindingen. (Voor servicefabricclusters die on-premises worden gehost, worden ook **Windows-** en **dSTS-verificaties** ondersteund.) U moet het clusterverbindingstype configureren wanneer het cluster wordt gemaakt. Zodra het is gemaakt, kan het verbindingstype niet meer worden gewijzigd.

De tools Visual Studio Service Fabric ondersteunen alle verificatietypen voor het maken van verbinding met een cluster voor publicatie. Zie [Een cluster servicestructuur instellen vanuit de Azure-portal](service-fabric-cluster-creation-via-portal.md) voor instructies voor het instellen van een beveiligd servicefabriccluster.

## <a name="configure-cluster-connections-in-publish-profiles"></a>Clusterverbindingen configureren in publicatieprofielen
Als u een Service Fabric-project publiceert vanuit Visual Studio, gebruikt u het dialoogvenster **Fabric-toepassing publiceren** om een Azure Service Fabric-cluster te kiezen. Selecteer **onder Verbindingseindpunt**een bestaand cluster onder uw abonnement.

![Het dialoogvenster **Publicatieservicefabrictoepassing** wordt gebruikt om een Service Fabric-verbinding te configureren.][publishdialog]

In het dialoogvenster **Fabric-toepassing publiceren** wordt de clusterverbinding automatisch gevalideerd. Meld u desgevraagd aan bij uw Azure-account. Als de validatie verloopt, betekent dit dat uw systeem de juiste certificaten heeft geïnstalleerd om veilig verbinding te maken met het cluster of dat uw cluster niet beveiligd is. Validatiefouten kunnen worden veroorzaakt door netwerkproblemen of door dat uw systeem niet correct is geconfigureerd om verbinding te maken met een beveiligd cluster.

![Het dialoogvenster **Publicatieservicefabrictoepassing** valideert een bestaande, correct geconfigureerde clusterverbinding voor servicefabric.][selectsfcluster]

### <a name="to-connect-to-a-secure-cluster"></a>Verbinding maken met een beveiligd cluster
1. Zorg ervoor dat u toegang hebt tot een van de clientcertificaten die het doelcluster vertrouwt. Het certificaat wordt meestal gedeeld als een bestand voor het uitwisselen van persoonlijke informatie (.pfx). Zie [Een ServiceFabric-cluster instellen vanuit de Azure-portal](service-fabric-cluster-creation-via-portal.md) voor het configureren van de server om toegang te verlenen aan een client.
2. Installeer het vertrouwde certificaat. Dubbelklik hiervoor op het .pfx-bestand of gebruik het PowerShell-script Import-PfxCertificate om de certificaten te importeren. Installeer het certificaat op **Cert:\LocalMachine\My**. Het is ok om alle standaardinstellingen te accepteren tijdens het importeren van het certificaat.
3. Kies de opdracht **Publiceren...** in het snelmenu van het project om het dialoogvenster **Azure-toepassing publiceren** te openen en selecteer vervolgens het doelcluster. De tool lost de verbinding automatisch op en slaat de parameters voor beveiligde verbindingen op in het publicatieprofiel.
4. Optioneel: u het publicatieprofiel bewerken om een beveiligde clusterverbinding op te geven.
   
   Aangezien u het XML-bestand Publish Profile handmatig bewerkt om de certificaatgegevens op te geven, moet u de naam van het certificaatarchief, de winkellocatie en de duimafdruk van het certificaat noteren. U moet deze waarden opgeven voor de winkelnaam en winkellocatie van het certificaat. Zie [Hoe: De duimafdruk van een certificaat ophalen](https://msdn.microsoft.com/library/ms734695\(v=vs.110\).aspx) voor meer informatie.
   
   U de parameters *ClusterConnectionParameters* gebruiken om de PowerShell-parameters op te geven die u wilt gebruiken wanneer u verbinding maakt met het cluster Servicefabric. Geldige parameters zijn parameters die worden geaccepteerd door de Connect-ServiceFabricCluster-cmdlet. Zie [Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster) voor een lijst met beschikbare parameters.
   
   Als u publiceert naar een extern cluster, moet u de juiste parameters voor dat specifieke cluster opgeven. Het volgende is een voorbeeld van verbinding maken met een niet-beveiligd cluster:
   
   `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`
   
   Hier is een voorbeeld voor het maken van verbinding met een beveiligd cluster op basis van een x509-certificaat:
   
   ```xml
   <ClusterConnectionParameters
   ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
   X509Credential="true"
   ServerCertThumbprint="0123456789012345678901234567890123456789"
   FindType="FindByThumbprint"
   FindValue="9876543210987654321098765432109876543210"
   StoreLocation="CurrentUser"
   StoreName="My" />
   ```
5. Bewerk alle andere noodzakelijke instellingen, zoals upgradeparameters en de locatie van het toepassingsparametervan toepassing, en publiceer uw toepassing vervolgens vanuit het dialoogvenster **Fabric-toepassing publiceren** in Visual Studio.

## <a name="next-steps"></a>Volgende stappen
Zie Uw [cluster visualiseren met Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)voor meer informatie over het openen van clusters van servicefabric.

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png

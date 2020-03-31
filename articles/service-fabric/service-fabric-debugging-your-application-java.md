---
title: Debuging uw toepassing in Eclipse
description: Verbeter de betrouwbaarheid en prestaties van uw services door ze te ontwikkelen en te debuggen in Eclipse op een lokaal ontwikkelingscluster.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614482"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Debuging uw Java Service Fabric applicatie met eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Start een lokaal ontwikkelcluster door de stappen te volgen in [Het instellen van uw servicefabric-ontwikkelomgeving.](service-fabric-get-started-linux.md)

2. Werk entryPoint.sh van de service die u wilt debuggen, zodat het java-proces met externe foutopsporingsparameters wordt gestart. Dit bestand is te vinden `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`op de volgende locatie: . Poort 8001 is in dit voorbeeld voor foutopsporing ingesteld.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Werk het toepassingsmanifest bij door het aantal instantie of het aantal replica's in te stellen voor de service die wordt gedebugged tot 1. Deze instelling voorkomt conflicten voor de poort die wordt gebruikt voor het opsporen van fouten. Stel bijvoorbeeld voor stateless services `InstanceCount="1"` in en stel voor stateful services de doel- en min-replicasetgrootten als volgt in op 1: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. De toepassing implementeren.

5. Selecteer in de Eclipse IDE **Debug->-> eigenschappen van externe Java-toepassing en invoerverbindingseigenschappen** uitvoeren en stel de eigenschappen als volgt in:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Stel breekpunten in op de gewenste punten en debugt de toepassing.

Als de toepassing vastloopt, u ook coredumps inschakelen. Uitvoeren `ulimit -c` in een shell en als het 0 retourneert, worden coredumps niet ingeschakeld. Voer de volgende opdracht uit om `ulimit -c unlimited`onbeperkte coredumps in te schakelen: . U de status ook `ulimit -a`verifiëren met de opdracht.  Als u het pad voor het `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`genereren van coredump wilt bijwerken, voert u uit . 

### <a name="next-steps"></a>Volgende stappen

* [Logboeken verzamelen met Linux Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md).
* [Services lokaal controleren en diagnosticeren.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

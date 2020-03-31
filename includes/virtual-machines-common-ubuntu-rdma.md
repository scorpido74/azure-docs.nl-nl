---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d41b86b902d9a58b144e251e6922fbd95d459031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67671204"
---
1. Installeer dapl, rdmacm, ibverbs en mlx4

   ```bash
   sudo apt-get update

   sudo apt-get install libdapl2 libmlx4-1

   ```

2. Schakel RDMA in /etc/waagent.conf in door de volgende configuratieregels niet te becommentariërd. U hebt roottoegang nodig om dit bestand te bewerken.
  
   ```
   OS.EnableRDMA=y

   OS.UpdateRdmaDriver=y
   ```

3. Voeg de volgende geheugeninstellingen in KB toe of wijzig deze in het bestand /etc/security/limits.conf. U hebt roottoegang nodig om dit bestand te bewerken. Voor testdoeleinden u memlock instellen op onbeperkt. Bijvoorbeeld: `<User or group name>   hard    memlock   unlimited`.

   ```
   <User or group name> hard    memlock <memory required for your application in KB>

   <User or group name> soft    memlock <memory required for your application in KB>
   ```
  
4. Installeer De Intel MPI-bibliotheek. Ofwel [kopen en downloaden](https://software.intel.com/intel-mpi-library/) van de bibliotheek van Intel of download de gratis evaluatie [versie](https://registrationcenter.intel.com/en/forms/?productid=1740).

   ```bash
   wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
   ```
 
   Alleen Intel MPI 5.x runtimes worden ondersteund.
 
   Zie de [installatiehandleiding voor de Intel MPI-bibliotheek voor](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)installatiestappen voor installatiestappen voor installatie.

5. Ptrace inschakelen voor niet-root niet-foutopsporingsprocessen (nodig voor de meest recente versies van Intel MPI).
 
   ```bash
   echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
   ```

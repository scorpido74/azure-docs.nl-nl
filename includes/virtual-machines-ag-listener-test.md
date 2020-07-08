---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2689e81b089147dfc913fb119e0a499d60574b60
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050401"
---
In deze stap test u de listener voor de beschikbaarheids groep met behulp van een client toepassing die wordt uitgevoerd op hetzelfde netwerk.

Client connectiviteit heeft de volgende vereisten:

* Client verbindingen met de listener moeten afkomstig zijn van computers die zich in een andere Cloud service bevinden dan de computer die als host fungeert voor de always on-beschikbaarheids replica's.
* Als de always on-replica's zich in verschillende subnetten bevinden, moeten clients *MultisubnetFailover = True* opgeven in de Connection String. Deze voor waarde resulteert in een parallelle verbinding met replica's in de verschillende subnetten. Dit scenario omvat de implementatie van een permanente beschikbaarheids groep voor meerdere regio's.

Een voor beeld is om verbinding te maken met de listener van een van de Vm's in hetzelfde virtuele Azure-netwerk (maar niet een als host voor een replica). Een eenvoudige manier om deze test te volt ooien, is om SQL Server Management Studio verbinding te maken met de beschikbaarheids groep-listener. Een andere eenvoudige methode is om [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)als volgt uit te voeren:

```console
sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15
```

> [!NOTE]
> Als de EndpointPort-waarde *1433*is, hoeft u deze niet op te geven in de aanroep. Bij de vorige aanroep wordt ook aangenomen dat de client computer lid is van hetzelfde domein en dat de aanroeper machtigingen heeft gekregen voor de data base met behulp van Windows-verificatie.
> 
> 

Wanneer u de listener test, moet u een failover uitvoeren voor de beschikbaarheids groep om ervoor te zorgen dat clients verbinding kunnen maken met de listener voor failovers.


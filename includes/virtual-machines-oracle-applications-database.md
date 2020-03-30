---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361529"
---
### <a name="database-tier"></a>Databaselaag

De laag Database bevat de database-exemplaren voor de toepassing. De database kan een Oracle DB, Oracle RAC of Oracle Exadata Database-systeem zijn. 

Als de keuze is om Oracle DB te gebruiken, kan de database-instantie worden geïmplementeerd op Azure via de Oracle DB-afbeeldingen die beschikbaar zijn op de Azure Marketplace. U ook de interconnect tussen Azure en OCI gebruiken om de Oracle DB te implementeren in een PaaS-model op OCI.

Voor Oracle RAC u Oracle RAC implementeren op Azure CloudSimple in iaas-model of in OCI in PaaS-model. Het wordt aanbevolen om een RAC-systeem met twee nodes te gebruiken. 

Ten slotte, voor Exadata-systemen, gebruik maken van de OCI interconnect en implementeren van de Exadata-systeem in OCI. Het bovenstaande bovenstaande architectuurdiagram toont een Exadata-systeem dat in OCI is geïmplementeerd in twee subnetten.

Implementeer voor productiescenario's meerdere exemplaren van de database in twee beschikbaarheidszones (als deze worden geïmplementeerd in Azure) of twee beschikbaarheidsdomeinen (in OCI). Gebruik Oracle Active Data Guard om de primaire en stand-by databases te synchroniseren.

De databaselaag ontvangt alleen aanvragen van de middelste laag. Het wordt aanbevolen dat u een netwerkbeveiligingsgroep (beveiligingslijst als het implementeren van de database in OCI) instelt om alleen aanvragen toe te staan op poort 1521 vanaf de middelste laag en poort 22 van de bastionserver om administratieve redenen.

Voor databases die in OCI worden geïmplementeerd, moet een apart virtueel cloudnetwerk worden opgezet met een dynamische routeringgateway (DRG) die is verbonden met uw FastConnect-circuit.
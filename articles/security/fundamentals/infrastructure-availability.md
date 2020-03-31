---
title: Beschikbaarheid azure-infrastructuur - Azure-beveiliging
description: In dit artikel vindt u informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen en de maximale beschikbaarheid van gegevens van klanten te bieden.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727220"
---
# <a name="azure-infrastructure-availability"></a>Beschikbaarheid azure-infrastructuur
In dit artikel vindt u informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen en de maximale beschikbaarheid van gegevens van klanten te bieden. Azure biedt robuuste beschikbaarheid, gebaseerd op uitgebreide redundantie die is bereikt met virtualisatietechnologie.

## <a name="temporary-outages-and-natural-disaster"></a>Tijdelijke uitval en natuurramp
Het Microsoft Cloud Infrastructure and Operations-team ontwerpt, bouwt, exploiteert en verbetert de beveiliging van de cloudinfrastructuur. Dit team zorgt ervoor dat de Azure-infrastructuur hoge beschikbaarheid en betrouwbaarheid, hoge efficiëntie en slimme schaalbaarheid biedt. Het team biedt een veiligere, privé- en vertrouwdere cloud.

Ononderbreekbare voedingen en grote oevers van batterijen zorgen ervoor dat de elektriciteit continu blijft als er op korte termijn stroomonderbrekingen optreedt. Noodgeneratoren leveren back-upstroom voor langdurige uitval en gepland onderhoud. Als zich een natuurramp voordoet, kan het datacenter on-site brandstofreserves gebruiken.

Snelle en robuuste glasvezelnetwerken verbinden datacenters met andere grote hubs en internetgebruikers. Compute nodes host workloads dichter bij gebruikers om latentie te verminderen, georedundantie te bieden en de algehele servicetolerantie te verhogen. Een team van ingenieurs werkt de klok rond om ervoor te zorgen diensten zijn voortdurend beschikbaar.

Microsoft zorgt voor een hoge beschikbaarheid door geavanceerde monitoring- en incidentrespons, serviceondersteuning en back-upfailovermogelijkheden. Geografisch verspreide Microsoft-operations centers werken 24/7/365. Het Azure-netwerk is een van de grootste ter wereld. Het glasvezel- en contentdistributienetwerk verbindt datacenters en randknooppunten om hoge prestaties en betrouwbaarheid te garanderen.

## <a name="disaster-recovery"></a>Herstel na noodgeval
Azure houdt uw gegevens duurzaam op twee locaties. U de locatie van de back-upsite kiezen. Op beide locaties onderhoudt Azure voortdurend drie gezonde replica's van uw gegevens.

## <a name="database-availability"></a>Beschikbaarheid van de database
Azure zorgt ervoor dat een database internettoegankelijk is via een internetgateway met langdurige beschikbaarheid van de database. Monitoring beoordeelt de status en status van de actieve databases met een tijdsinterval van vijf minuten.

## <a name="storage-availability"></a>Opslagbeschikbaarheid
Azure levert opslag via een zeer schaalbare en duurzame opslagservice, die verbindingseindpunten biedt. Dit betekent dat een toepassing rechtstreeks toegang heeft tot de opslagservice. De opslagservice verwerkt binnenkomende opslagaanvragen efficiënt, met transactionele integriteit.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over wat Microsoft doet om de Azure-infrastructuur te beveiligen:

- [Azure-faciliteiten, lokalen en fysieke beveiliging](physical-security.md)
- [Onderdelen en grenzen van azure-informatiesysteem](infrastructure-components.md)
- [Azure-netwerkarchitectuur](infrastructure-network.md)
- [Azure-productienetwerk](production-network.md)
- [Beveiligingsfuncties van Azure SQL Database](infrastructure-sql.md)
- [Azure-productiebewerkingen en -beheer](infrastructure-operations.md)
- [Azure-infrastructuurbewaking](infrastructure-monitoring.md)
- [Integriteit van Azure-infrastructuur](infrastructure-integrity.md)
- [Azure-klantgegevensbeveiliging](protection-customer-data.md)

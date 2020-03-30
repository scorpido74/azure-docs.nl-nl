---
title: Monitoring van SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Monitor SAP HANA op een Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 50a6b4f15a7de02533e3bb51e5659f7b4c078b40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617297"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>SAP HANA (grote exemplaren) op Azure controleren

SAP HANA op Azure (Large Instances) verschilt niet van andere IaaS-implementatie - u moet controleren wat het besturingssysteem en de toepassing doen en hoe de toepassingen de volgende resources verbruiken:

- CPU
- Geheugen
- Netwerkbandbreedte
- Schijfruimte

Met Azure Virtual Machines moet u uitzoeken of de hierboven genoemde resourceklassen voldoende zijn of dat ze uitgeput raken. Hier is meer details over elk van de verschillende klassen:

**CPU-bronverbruik:** De verhouding die SAP heeft gedefinieerd voor bepaalde werkbelasting ten opzichte van HANA wordt afgedwongen om ervoor te zorgen dat er voldoende CPU-bronnen beschikbaar zijn om de gegevens die in het geheugen zijn opgeslagen, te verwerken. Niettemin kunnen er gevallen zijn waarin HANA veel CPU's verbruikt die query's uitvoeren als gevolg van ontbrekende indexen of soortgelijke problemen. Dit betekent dat u het cpu-bronverbruik van de HANA-eenheid voor grote instanties en CPU-bronnen die worden verbruikt door de specifieke HANA-services moet controleren.

**Geheugenverbruik:** Is belangrijk om te controleren vanuit HANA, evenals buiten HANA op het apparaat. Controleer binnen HANA hoe de gegevens HANA toegewezen geheugen verbruiken om binnen de vereiste richtlijnen van SAP te blijven. U wilt ook het geheugenverbruik op het niveau van de Grote Instantie controleren om ervoor te zorgen dat extra geïnstalleerde niet-HANA-software niet te veel geheugen verbruikt en dus concurreert met HANA voor geheugen.

**Netwerkbandbreedte:** De Azure VNet-gateway is beperkt in bandbreedte van gegevens die naar het Azure VNet worden verplaatst, dus het is handig om de gegevens te controleren die zijn ontvangen door alle Azure VM's binnen een VNet om erachter te komen hoe dicht u bij de grenzen van de Azure-gateway SKU bent die u hebt geselecteerd. Op de HANA Large Instance-eenheid is het zinvol om ook inkomend en uitgaand netwerkverkeer te monitoren en de volumes bij te houden die in de loop van de tijd worden verwerkt.

**Schijfruimte:** Het verbruik van schijfruimte neemt meestal toe na verloop van tijd. De meest voorkomende oorzaken zijn: het gegevensvolume neemt toe, voert back-ups van transactielogboeken uit, slaat traceerbestanden op en voert opslagmomentopnamen uit. Daarom is het belangrijk om het gebruik van schijfruimte te controleren en de schijfruimte te beheren die is gekoppeld aan de HANA Large Instance-eenheid.

Voor de **Type II SKU's** van de HANA Large Instances wordt de server geleverd met de vooraf geladen systeemdiagnosetools. U deze diagnostische hulpmiddelen gebruiken om de systeemstatuscontrole uit te voeren. Voer de volgende opdracht uit om het logboekbestand voor statuscontrole te genereren bij /var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Wanneer u samenwerkt met het Microsoft Support-team om een probleem op te lossen, wordt u mogelijk ook gevraagd om de logboekbestanden op te geven met behulp van deze diagnostische hulpprogramma's. U het bestand zippen met de volgende opdracht.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Volgende stappen**

- Raadpleeg [Hoe sap HANA (grote exemplaren) op Azure te controleren.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

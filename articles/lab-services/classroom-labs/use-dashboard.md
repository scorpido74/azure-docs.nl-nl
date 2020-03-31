---
title: Dashboard gebruiken voor een klaslokaallab in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het gebruik van dashboard voor een klaslokaallab in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538937"
---
# <a name="dashboard-for-classroom-labs"></a>Dashboard voor klaslokalen
In dit artikel wordt de dashboardweergave van een klaslokaallab in Azure Lab Services beschreven. 

![Dashboard](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Tegel Kosten en facturering
Deze tegel bevat de volgende details van de kostenraming:

| Instelling | Waarde | 
| ------- | ----- | 
| Quota-uren | Het maximum aantal uren dat een gebruiker de VM buiten de geplande uren kan gebruiken. |
| Geplande uren | Uren die worden gemaakt op basis van het schema dat in het lab is ingesteld. Deze waarde is alleen beschikbaar als er een van/tot datum is ingesteld op alle planningsgebeurtenissen. |
| Uren/gebruiker | De som van de quota-uren en de geplande uren. |
| Maximale gebruikers | Maximaal aantal gebruikers in het lab op basis van alle virtuele machines die moeten worden geclaimd. |
| Uren x gebruikers | Uren/gebruiker vermenigvuldigd met het aantal gebruikers. |
| Aangepast contingent | De som van de quota-uren toegevoegd aan specifieke gebruikers. |
| Totaal aantal uren * $/uur | De kosten per uur op basis van de geselecteerde VM-grootte. Dit is gebaseerd op de reguliere betalen als je gaat prijs. |
| Totale geschatte kosten | Dit is de maximale prijs voor dit lab op basis van de huidige instellingen. |

## <a name="template-tile"></a>Sjabloontegel
U ziet de volgende informatie op deze tegel:

- De datum waarop de sjabloon is gemaakt 
- De datum waarop de sjabloon voor het laatst is gepubliceerd 

Het heeft ook een koppeling om naar de **sjabloonpagina** te navigeren waar u [de sjabloon-VM](how-to-create-manage-template.md) voor de klasse beheren. 

## <a name="virtual-machine-pool-tile"></a>Virtuele machinepooltegel

U ziet de volgende informatie op deze tegel:

- Aantal virtuele machines dat aan studenten is toegewezen (gebruikers)
- Aantal virtuele machines dat nog niet aan studenten is toegewezen

Het heeft ook een link om te navigeren naar de **virtuele machine pool** pagina waar u de pool van virtuele machines in het lab [beheren.](how-to-set-virtual-machine-passwords.md) 

## <a name="users-tile"></a>Tegel Gebruikers

U ziet de volgende informatie op deze tegel:

- Aantal gebruikers geregistreerd bij de klasse
- Aantal gebruikers dat aan het lab is toegevoegd, maar niet is geregistreerd in de klas 

Het heeft ook een link om te navigeren naar de **pagina Gebruikers** waar u gebruikers voor het lab [beheren.](how-to-configure-student-usage.md) 

## <a name="schedules-tile"></a>Tegel schema's
U ziet de huidige geplande gebeurtenissen voor het lab op de tegel. Het heeft ook een link om te navigeren naar de pagina **Planning** waar u [schema's](how-to-create-schedules.md)maken en beheren. De tegel toont u details voor slechts twee geplande gebeurtenissen en het aantal resterende geplande gebeurtenissen voor het lab. 

![Geplande gebeurtenissen](../media/use-dashboard/scheduled-events.png)


---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175980"
---
In deze stap test u de listener van de beschikbaarheidsgroep met behulp van een clienttoepassing die op hetzelfde netwerk wordt uitgevoerd.

Clientconnectiviteit heeft de volgende vereisten:

* Clientverbindingen met de listener moeten afkomstig zijn van machines die zich in een andere cloudservice bevinden dan de services die de replica's voor beschikbaarheid always on hosten.
* Als de replica's always on zich in verschillende subnetten bevinden, moeten clients *MultisubnetFailover=True* opgeven in de verbindingstekenreeks. Deze voorwaarde resulteert in parallelle verbinding pogingen om replica's in de verschillende subnetten. Dit scenario bevat een implementatie van always on beschikbaarheidsgroep voor verschillende regio's.

Een voorbeeld is om verbinding te maken met de listener vanuit een van de VM's in hetzelfde Azure virtuele netwerk (maar niet een die een replica host). Een eenvoudige manier om deze test te voltooien is om sql server management studio te verbinden met de authenticiteit van de beschikbaarheidsgroep. Een andere eenvoudige methode is het uitvoeren van [SQLCMD.exe,](https://technet.microsoft.com/library/ms162773.aspx)als volgt:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Als de waarde EndpointPort *1433*is, hoeft u deze niet op te geven in de aanroep. De vorige oproep gaat er ook van uit dat de clientmachine is verbonden met hetzelfde domein en dat de beller machtigingen heeft gekregen voor de database met behulp van Windows-verificatie.
> 
> 

Wanneer u de listener test, moet u ervoor zorgen dat u de beschikbaarheidsgroep niet overneemt om ervoor te zorgen dat clients verbinding kunnen maken met de listener via failovers.


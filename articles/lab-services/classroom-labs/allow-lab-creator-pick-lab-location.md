---
title: De maker van het lab toestaan locatie te kiezen in Azure Lab Services
description: In dit artikel wordt beschreven hoe een beheerder van een labaccount lab makers in staat kan stellen om locaties voor hun labs te kiezen.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444363"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Laat de maker van het lab locatie voor het lab kiezen in Azure Lab Services
In Azure Lab Services kan een eigenaar van een labaccount labmakers (docenten) toestaan een locatie te kiezen voor het lab dat ze maken. Deze locatie kan afwijken van de locatie van het labaccount. Een locatie is een groep Azure-regio's. De locatie van de Verenigde Staten is bijvoorbeeld een groep regio's zoals Oost-VS, West-VS, enzovoort. 

U, als eigenaar van een labaccount, de optie **Lab-maker toestaan om lablocatie te kiezen** wanneer u een labaccount maakt en nadat u het labaccount (of een bestaand labaccount) hebt gemaakt. 

## <a name="at-the-time-of-lab-account-creation"></a>Op het moment van het aanmaken van labaccount
Wanneer u een labaccount maakt, ziet u deze optie op het eerste scherm (tabblad**Basisbeginselen).** 

![De optie inschakelen op het moment van het maken van het lab](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Deze optie is uitgeschakeld als u een virtueel netwerk voor uw labaccount selecteert op het tabblad **Geavanceerd.**  

![Wanneer het virtuele netwerk van peer is ingeschakeld](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Nadat het labaccount is gemaakt
Nadat u het labaccount hebt gemaakt, u deze optie in- of uitschakelen door de volgende stappen te volgen: 

1. Selecteer op de pagina **Lab-account** de optie **Lab-instellingen** in het linkermenu.
2. Selecteer de optie **Lab-maker toestaan om de locatie van het lab te kiezen** als u de maker van het lab een locatie voor het lab wilt laten selecteren. Als het is uitgeschakeld, worden de labs automatisch gemaakt op dezelfde locatie waar het labaccount bestaat. 
    
    Dit veld wordt uitgeschakeld wanneer u een virtueel netwerk selecteert voor het **virtuele netwerkveld Peer.** Het is omdat labs in het lab account moet worden in dezelfde regio als het lab account voor hen om toegang te krijgen tot bronnen in de peer virtuele netwerk. 
1. Selecteer **Opslaan** op de werkbalk. 

    ![Lab-instellingen](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Geen virtuele netwerk- en locatieselectie is niet toegestaan
In dit scenario hebt u de optie **Lab-maker toestaan om de locatievan het lab te kiezen,** niet ingeschakeld. 

![Geen lablocatie](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Vervolgens zien labmakers (docenten) geen optie om een locatie voor het lab te kiezen. Zij zullen de prijs per uur zien voor elke grootte optie beschikbaar voor hen. Wanneer ze een lab maken, wordt het gemaakt in een Azure-regio die zich op dezelfde locatie bevindt als het Azure-gebied waarin hun labaccount zich bevindt. Bijvoorbeeld, als het lab account is in **West US**, dan is het lab kan worden gemaakt in **Zuid-Centraal VS,** maar zou niet worden gemaakt in **Canada East**. We garanderen niets over de regio die we kiezen afgezien van dat het in de locatie. Als een grootte momenteel beperkt is, ziet de maker van het lab een selectievakje waarin ze de groottes kunnen zien die we normaal gesproken ondersteunen, maar momenteel niet beschikbaar zijn. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>In virtuele netwerk- en locatieselectie is niet toegestaan
In dit scenario is de optie **Lab-maker toestaan om de locatie van het lab te kiezen,** uitgeschakeld omdat u een virtueel netwerk met een peer voor het labaccount hebt geselecteerd. Vervolgens zien makers van het lab hetzelfde scherm als bij de vorige optie. Omdat alle VM's zich in dezelfde Azure-regio moeten bevinden als het virtuele netwerk, wordt het lab gemaakt in dezelfde Azure-regio waarin het virtuele netwerk zich bevindt. Als dat specifieke gebied is beperkt voor een grootte, wordt de grootte weergegeven als niet beschikbaar. 

## <a name="location-selection-is-enabled"></a>Locatieselectie is ingeschakeld
Wanneer u **Labmaker toestaan selecteert om de locatie van het lab te kiezen,** zien labmakers (docenten) een optie om een locatie te selecteren bij het maken van een lab. 

![Kies een lablocatie](../media/allow-lab-creator-pick-lab-location/location-selection.png)

Makers van het lab zien het bereik van de prijzen voor alle locaties die de grootte is in, en kan een locatie te kiezen. Het lab wordt gemaakt in elke Azure-regio die naar die locatie wordt toegewezen.

Als een locatie beperkt is, wordt deze standaard niet weergegeven in de lijst. Vouw de vervolgkeuzelijst uit en selecteer **Niet-beschikbare locaties weergeven voor dit formaat**. 

![Niet-beschikbare locaties weergeven](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Kosten
Eerder was de prijs gebaseerd op de VM-grootte die u voor het lab kiest. Nu is de prijs gebaseerd op de combinatie van besturingssysteem (OS), grootte en locatie. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Verbind het netwerk van uw lab met een virtueel netwerk van collega's](how-to-connect-peer-virtual-network.md)
- [Een gedeelde afbeeldingsgalerie aan een lab koppelen](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als eigenaar van een lab](how-to-add-user-lab-owner.md)
- [Firewall-instellingen voor een lab weergeven](how-to-configure-firewall-settings.md)
- [Andere instellingen voor een lab configureren](how-to-configure-lab-accounts.md)
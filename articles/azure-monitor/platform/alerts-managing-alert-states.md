---
title: Statussen van waarschuwingen en slimme groepen beheren
description: De statussen van de waarschuwingen en slimme groepsinstanties beheren
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667513"
---
# <a name="manage-alert-and-smart-group-states"></a>Statussen van waarschuwingen en slimme groepen beheren

Waarschuwingen in Azure Monitor hebben nu een [waarschuwingsstatus en een monitorvoorwaarde](https://aka.ms/azure-alerts-overview) en op dezelfde manier hebben slimme groepen een [slimme groepsstatus.](https://aka.ms/smart-groups) Wijzigingen in de status worden nu vastgelegd in de geschiedenis die is gekoppeld aan de desbetreffende waarschuwing of slimme groep. Dit artikel leidt u door het proces van het veranderen van de status, voor zowel een waarschuwing en een slimme groep.

## <a name="change-the-state-of-an-alert"></a>De status van een waarschuwing wijzigen

1. U de status van een waarschuwing op de volgende verschillende manieren wijzigen: 
    * Klik op de pagina Alle waarschuwingen op het selectievakje naast de waarschuwingen waarvan u de status wilt wijzigen en klik op Status wijzigen.   
    ![Bewaking](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Op de pagina Waarschuwingsgegevens voor een bepaalde waarschuwingsinstantie u op status wijzigen klikken   
    ![Bewaking](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Klik op de pagina Waarschuwingsgegevens voor een specifieke waarschuwingsinstantie in het deelvenster Slimme groep op het selectievakje naast de gewenste waarschuwingen    
    ![Bewaking](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Klik op de pagina Slimme groepsgegevens in de lijst met ledenwaarschuwingen op het selectievakje naast de waarschuwingen waarvan u de status wilt wijzigen en klik op Status wijzigen om de status van en klik op Status wijzigen.   
    ![Bewaking](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Als u op Status wijzigen klikt, wordt er een pop-up geopend waarmee u de status (Nieuw/Erkend/Gesloten) selecteren en indien nodig een opmerking invoeren.   
![Bewaking](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Zodra dit is gebeurd, wordt de statuswijziging geregistreerd in de geschiedenis van de desbetreffende waarschuwing. Dit kan worden bekeken door de respectievelijke pagina Details te openen en de geschiedenissectie te controleren.    
![Bewaking](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>De status van een slimme groep wijzigen
1. U de status van een slimme groep op de volgende manieren wijzigen:
    1. Klik op de lijstpagina Slimme groep op het selectievakje naast de slimme groepen waarvan u de status wilt wijzigen en klik op Status wijzigen  
    ![Bewaking](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Klik op de pagina Slimme groepsgegevens op status wijzigen        
    ![Bewaking](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Als u op Status wijzigen klikt, wordt er een pop-up geopend waarmee u de status (Nieuw/Erkend/Gesloten) selecteren en indien nodig een opmerking invoeren. 
![Controle](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Als u de status van een slimme groep wijzigt, verandert de status van de waarschuwingen van het afzonderlijke lid niet.

1. Zodra dit is gebeurd, wordt de statuswijziging vastgelegd in de geschiedenis van de desbetreffende slimme groep. Dit kan worden bekeken door de respectievelijke pagina Details te openen en de geschiedenissectie te controleren.     
![Bewaking](./media/alerts-managing-alert-states/state-sg-history.jpg)


---
title: Problemen met ServiceNow-synchronisatie hand matig oplossen
description: Stel de verbinding opnieuw in op ServiceNow zodat waarschuwingen in Microsoft Azure opnieuw kunnen worden aangeroepen ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 2a9a8f3fe7422468ff2c8886dff7415322cf70f0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676869"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Problemen met ServiceNow-synchronisatie hand matig oplossen

Azure Monitor kan verbinding maken met ITSM-providers (IT Service Management) van derden. ServiceNow is een van deze providers.

Uit veiligheids overwegingen moet u mogelijk het verificatie token vernieuwen dat voor uw verbinding wordt gebruikt met ServiceNow.
Gebruik het volgende synchronisatie proces om de verbinding opnieuw te activeren en het token te vernieuwen:


1. Zoek naar de oplossing in de bovenste Zoek banner en selecteer vervolgens de relevante oplossingen

    ![Scherm afbeelding met de bovenste Zoek banner en waar u de relevante oplossingen kunt selecteren.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Kies in het scherm oplossing de optie Alles selecteren in het abonnements filter en filter vervolgens op ' Service Desk '

    ![Scherm afbeelding die laat zien waar u selecteert selecteren en waar u wilt filteren op Service Desk.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecteer de oplossing van uw ITSM-verbinding.
1. Selecteer ITSM-verbinding in het linkerdeel blad.

    ![Scherm afbeelding die laat zien waar u ITSM-verbindingen selecteert.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecteer elke connector in de lijst. 
    1. Klik op de naam van de connector om deze te configureren
    1. Alle connectors verwijderen die niet meer worden gebruikt

    1. De velden bijwerken volgens [deze definities](./itsmc-connections.md) op basis van uw partner type

    1. Klik op synchroniseren

       ![Scherm opname van de knop synchroniseren.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klik op opslaan

        ![Nieuwe verbinding](media/itsmc-resync-servicenow/save-8bit.png)

f.    Bekijk de meldingen om na te gaan of het proces is voltooid 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [IT Service Management-verbindingen](itsmc-connections.md)

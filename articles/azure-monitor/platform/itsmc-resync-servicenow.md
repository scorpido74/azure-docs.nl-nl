---
title: Problemen met ServiceNow-synchronisatie hand matig oplossen
description: Stel de verbinding opnieuw in op ServiceNow zodat waarschuwingen in Microsoft Azure opnieuw kunnen worden aangeroepen ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81313671"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Problemen met ServiceNow-synchronisatie hand matig oplossen

Azure Monitor kan verbinding maken met ITSM-providers (IT Service Management) van derden. ServiceNow is een van deze providers.

Uit veiligheids overwegingen moet u mogelijk het verificatie token vernieuwen dat voor uw verbinding wordt gebruikt met ServiceNow.
Gebruik het volgende synchronisatie proces om de verbinding opnieuw te activeren en het token te vernieuwen:


1. Zoek naar de oplossing in de bovenste Zoek banner en selecteer vervolgens de relevante oplossingen

    ![Nieuwe verbinding](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Kies in het scherm oplossing de optie Alles selecteren in het abonnements filter en filter vervolgens op ' Service Desk '

    ![Nieuwe verbinding](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecteer de oplossing van uw ITSM-verbinding.
1. Selecteer ITSM-verbinding in het linkerdeel blad.

    ![Nieuwe verbinding](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecteer elke connector in de lijst. 
    1. Klik op de naam van de connector om deze te configureren
    1. Alle connectors verwijderen die niet meer worden gebruikt

    1. De velden bijwerken volgens [deze definities](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) op basis van uw partner type

    1. Klik op synchroniseren

       ![Nieuwe verbinding](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klik op opslaan

        ![Nieuwe verbinding](media/itsmc-resync-servicenow/save-8bit.png)

f.    Bekijk de meldingen om na te gaan of het proces is voltooid 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [IT Service Management-verbindingen](itsmc-connections.md)

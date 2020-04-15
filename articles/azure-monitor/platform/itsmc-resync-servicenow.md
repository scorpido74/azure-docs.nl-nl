---
title: Synchronisatieproblemen met ServiceNow handmatig oplossen
description: De verbinding met ServiceNow opnieuw instellen, zodat waarschuwingen in Microsoft Azure opnieuw ServiceNow kunnen bellen
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313671"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Synchronisatieproblemen met ServiceNow handmatig oplossen

Azure Monitor kan verbinding maken met ITSM-providers (ITM) van derden. ServiceNow is een van die aanbieders.

Om veiligheidsredenen moet u mogelijk het verificatietoken vernieuwen dat wordt gebruikt voor uw verbinding met ServiceNow.
Gebruik het volgende synchronisatieproces om de verbinding opnieuw te activeren en het token te vernieuwen:


1. Zoek naar de oplossing in de bovenste zoekbanner en selecteer vervolgens de relevante oplossingen

    ![Nieuwe verbinding](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Kies in het scherm Oplossing 'Alles selecteren' in het abonnementsfilter en filter vervolgens op 'ServiceDesk'.

    ![Nieuwe verbinding](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Selecteer de oplossing van uw ITSM-verbinding.
1. Selecteer ITSM-verbinding in de linkerbanner.

    ![Nieuwe verbinding](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Selecteer elke connector in de lijst. 
    1. Klik op de naam connector om deze te configureren
    1. Connectors verwijderen die niet meer in gebruik zijn

    1. De velden bijwerken volgens [deze definities](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) op basis van uw partnertype

    1. Klik op synchronisatie

       ![Nieuwe verbinding](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klik op opslaan

        ![Nieuwe verbinding](media/itsmc-resync-servicenow/save-8bit.png)

f.    Bekijk de meldingen om te zien of het proces met succes is voltooid 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [IT-servicebeheerverbindingen](itsmc-connections.md)

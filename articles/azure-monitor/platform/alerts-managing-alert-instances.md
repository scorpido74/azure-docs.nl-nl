---
title: Waarschuwingsinstanties beheren in Azure Monitor
description: Waarschuwingsinstanties beheren in Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 1910b3b474012365e8117f584379b2b29f8ce3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667615"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Waarschuwingsinstanties beheren met uniforme waarschuwingen

Met de [uniforme waarschuwingservaring](https://aka.ms/azure-alerts-overview) in Azure Monitor u al uw verschillende typen waarschuwingen in Azure bekijken. Dit omvat meerdere abonnementen, in één deelvenster. In dit artikel ziet u hoe u uw waarschuwingsinstanties weergeven en hoe u specifieke waarschuwingsinstanties vinden voor het oplossen van problemen.

> [!NOTE]
> U hebt alleen toegang tot waarschuwingen die in de afgelopen 30 dagen zijn gegenereerd.

## <a name="go-to-the-alerts-page"></a>Ga naar de pagina waarschuwingen

U op een van de volgende manieren naar de waarschuwingspagina gaan:

- Selecteer In de [Azure-portal](https://portal.azure.com/)de optie > **Monitorwaarschuwingen**. **Monitor**  

     ![Schermafbeelding van monitorwaarschuwingen](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Gebruik de context van een specifieke resource. Open een resource, ga naar de sectie **Controle** en kies **Waarschuwingen**. De bestemmingspagina wordt vooraf gefilterd op waarschuwingen op die specifieke bron.

     ![Schermafbeelding van waarschuwingen voor bronbewaking](media/alerts-managing-alert-instances/alert-resource.JPG)

- Gebruik de context van een specifieke resourcegroep. Open een resourcegroep, ga naar de sectie **Controle** en kies **Waarschuwingen**. De bestemmingspagina wordt vooraf gefilterd op waarschuwingen in die specifieke resourcegroep.    

     ![Schermafbeelding van waarschuwingen voor controle van resourcegroepen](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Waarschuwingsinstanties zoeken

De pagina **Overzicht van waarschuwingen** geeft u een overzicht van al uw waarschuwingsinstanties in Azure. U de overzichtsweergave wijzigen door **meerdere abonnementen** te selecteren (maximaal 5) of door te filteren tussen **resourcegroepen,** specifieke **resources**of **tijdsbereiken**. Selecteer **Totaalmeldingen**of een van de ernstbanden om naar de lijstweergave voor uw waarschuwingen te gaan.     

![Schermafbeelding van de pagina Overzicht van waarschuwingen](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Op de pagina **Alle waarschuwingen** worden alle waarschuwingsinstanties in Azure weergegeven. Als u vanuit een waarschuwingsmelding naar de portal komt, u de beschikbare filters gebruiken om die specifieke waarschuwingsinstantie te beperken.

> [!NOTE]
> Als u naar de pagina bent gekomen door een van de ernstbanden te selecteren, wordt de lijst vooraf gefilterd op die ernst.

Naast de filters die beschikbaar zijn op de vorige pagina, u ook filteren op basis van monitorservice (bijvoorbeeld platform voor statistieken), monitorconditie (ontslagen of opgelost), ernst, waarschuwingsstatus (nieuw/erkend/gesloten) of de slimme groeps-ID.

![Schermafbeelding van de pagina Alle waarschuwingen](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Als u naar de pagina bent gekomen door een van de ernstbanden te selecteren, wordt de lijst vooraf gefilterd op die ernst.

Als u een waarschuwingsinstantie selecteert, wordt de pagina **Waarschuwingsdetails** geopend, zodat u meer details over die specifieke waarschuwingsinstantie zien.   

![Schermafbeelding van de pagina Waarschuwingsdetails](media/alerts-managing-alert-instances/alert-details.jpg)  


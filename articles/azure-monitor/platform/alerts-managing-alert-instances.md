---
title: Waarschuwingsexemplaren beheren
description: Waarschuwings instanties in azure beheren
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: dbc7d37ce311d203ee051b5a23632d2bf401e27a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034831"
---
# <a name="manage-alert-instances"></a>Waarschuwingsexemplaren beheren
Met de [Unified Alerts-ervaring](https://aka.ms/azure-alerts-overview) in azure monitor kunt u nu al uw verschillende soorten waarschuwingen in azure zien, waarbij u meerdere abonnementen beslaat, in één glas venster. In dit artikel leest u hoe u uw waarschuwings instanties kunt weer geven en hoe u de portal uitgebreid vindt om specifieke waarschuwings instanties te vinden voor het oplossen van problemen.

> [!NOTE]
   >  Alleen waarschuwingen die in de afgelopen 30 dagen zijn gegenereerd, kunnen worden geopend op de UX of via de REST Api's.

1. Er zijn drie manieren om op de pagina waarschuwingen te gronden

   + Selecteer in de [Portal](https://portal.azure.com/) **bewaken** en klik onder de sectie monitor op **waarschuwingen**.  
     ![Controle](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + U kunt naar waarschuwingen in de context van een specifieke **resource**navigeren. Zodra een resource is geopend, navigeert u door de inhouds opgave naar de sectie bewaking en kiest u **waarschuwingen**, waarbij de landings pagina vooraf wordt gefilterd op waarschuwingen voor die specifieke resource.
   
     ![Bewaking](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + U kunt naar waarschuwingen in de context van een specifieke **resource groep**navigeren. Wanneer een resource groep is geopend, navigeert u door de inhouds opgave naar de sectie bewaking en kiest u **waarschuwingen**, waarbij de landings pagina vooraf wordt gefilterd op waarschuwingen voor die specifieke resource groep.    
   
     ![Bewaking](media/alerts-managing-alert-instances/alert-rg.JPG)

1. U gaat op de pagina **overzicht van waarschuwingen** , waarmee u een overzicht krijgt van alle waarschuwings exemplaren in Azure. U kunt de samenvattings weergave wijzigen door **meerdere abonnementen** te selecteren (Maxi maal 5) of door te filteren op **resource groepen**, specifieke **resources**of **Peri Oden**. Klik op totaal aantal waarschuwingen of een van de ernst banden om naar de lijst weergave voor uw waarschuwingen te gaan.     
   ![Overzicht van waarschuwingen](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. U gaat naar de pagina **alle waarschuwingen** , waar u alle waarschuwings exemplaren in azure vermeld ziet. Als u vanuit een waarschuwings melding naar de portal komt, kunt u de beschik bare filters gebruiken om te beperken op dat specifieke waarschuwings exemplaar. (**Opmerking**: als u op de pagina bent gekomen door te klikken op een van de ernst banden, wordt de lijst vooraf gefilterd op die ernst wanneer u het land hebt). Naast de filters die beschikbaar zijn op de vorige pagina, kunt u nu ook filteren op basis van de bewakende service (bijvoorbeeld platform voor metrische gegevens), de bewaken van de voor waarde (geactiveerd of opgelost), de ernst, de waarschuwings status (nieuw/bevestigd/gesloten) of de ID van de Smart groep.

   ![Alle waarschuwingen](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Als u op de pagina bent gekomen door te klikken op een van de ernst banden, wordt de lijst vooraf gefilterd op die ernst wanneer u op deze pagina landt.
 
1. Als u op een waarschuwings instantie klikt, wordt de pagina **waarschuwings Details** geopend, zodat u meer informatie kunt vinden over dat specifieke waarschuwings exemplaar.   
   ![Waarschuwings Details](media/alerts-managing-alert-instances/alert-details.jpg)  


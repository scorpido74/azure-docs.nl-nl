---
title: App herstellen vanuit een momentopname
description: Meer informatie over het herstellen van uw app vanaf een momentopname. Herstel van onverwacht gegevensverlies in Premium-laag met de automatische schaduwkopieën.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 04/04/2018
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: b17a49535541c8f75f65cdbe9986a895427f3877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255138"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Een app in Azure herstellen vanuit een momentopname
In dit artikel ziet u hoe u een app in [Azure App Service](../app-service/overview.md) herstellen vanuit een momentopname. U uw app herstellen naar een eerdere status, op basis van een van de momentopnamen van uw app. U hoeft geen back-up van momentopnamen in te schakelen, het platform slaat automatisch een momentopname van alle apps op voor gegevenshersteldoeleinden.

Snapshots zijn incrementele schaduwkopieën en bieden verschillende voordelen ten opzichte van reguliere [back-ups:](manage-backup.md)
- Geen fouten in bestandskopie als gevolg van bestandsvergrendelingen.
- Geen beperking van de opslaggrootte.
- Geen configuratie vereist.

Herstellen van momentopnamen is beschikbaar voor apps die in **premium-laag** of hoger worden uitgevoerd. Zie [Een app opschalen in Azure](manage-scale-up.md)voor informatie over het opschalen van uw app.

## <a name="limitations"></a>Beperkingen

- De functie is momenteel in preview.
- U alleen herstellen naar dezelfde app of naar een sleuf die bij die app hoort.
- App Service stopt de doel-app of doelsleuf tijdens het herstellen.
- App Service houdt drie maanden ter waarde van snapshots voor platform data recovery doeleinden.
- U alleen momentopnamen van de afgelopen 30 dagen herstellen.
- App-services die worden uitgevoerd in een App-serviceomgeving, bieden geen ondersteuning voor momentopnamen.
 

## <a name="restore-an-app-from-a-snapshot"></a>Een app herstellen vanuit een momentopname

1. Klik op de pagina **Instellingen** van uw app in de [Azure-portal](https://portal.azure.com)op **Back-ups** om de pagina **Back-ups** weer te geven. Klik vervolgens op **Herstellen** onder de sectie **Momentopname (voorbeeld).**
   
    ![](./media/app-service-web-restore-snapshots/1.png)

2. Selecteer **op** de pagina Herstellen de momentopname die u wilt herstellen.
   
    ![](./media/app-service-web-restore-snapshots/2.png)
   
3. Geef de bestemming op voor het app-herstel in **Bestemming Herstellen**.
   
    ![](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Als u **Overschrijven**kiest, worden alle bestaande gegevens in het huidige bestandssysteem van uw app gewist en overschreven. Voordat u op **OK**klikt, moet u ervoor zorgen dat dit is wat u wilt doen.
   > 
   > 
      
   > [!Note]
   > Vanwege de huidige technische beperkingen u alleen herstellen naar apps in dezelfde schaaleenheid. Deze beperking wordt verwijderd in een toekomstige release.
   > 
   > 
   
    U **Bestaande app** selecteren om te herstellen naar een sleuf. Voordat u deze optie gebruikt, moet u al een sleuf in uw app hebben gemaakt.

4. U ervoor kiezen om uw siteconfiguratie te herstellen.
   
    ![](./media/app-service-web-restore-snapshots/4.png)

5. Klik op **OK**.

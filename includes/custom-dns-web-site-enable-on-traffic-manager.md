---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ddeb46a2c7bc7f24f55c22f446926529cee7b598
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059641"
---
Nadat de records voor uw domein naam zijn door gegeven, kunt u uw browser gebruiken om te controleren of uw aangepaste domein naam kan worden gebruikt om toegang te krijgen tot uw web-app in Azure App Service.

> [!NOTE]
> Het kan enige tijd duren voordat uw CNAME door het DNS-systeem wordt door gegeven. U kunt een service gebruiken, <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> bijvoorbeeld om te controleren of de CNAME beschikbaar is.
> 
> 

Als u uw web-app nog niet hebt toegevoegd als Traffic Manager-eind punt, moet u dit doen voordat de naam omzetting werkt, als de aangepaste domein naam routes naar Traffic Manager. Traffic Manager worden vervolgens doorgestuurd naar uw web-app. Gebruik de informatie in [eind punten toevoegen of verwijderen](../articles/traffic-manager/traffic-manager-endpoints.md) om uw web-app als een eind punt toe te voegen aan uw Traffic Manager profiel.

> [!NOTE]
> Als uw web-app niet wordt vermeld bij het toevoegen van een eind punt, controleert u of deze is geconfigureerd voor de **standaard** modus app service plan. U moet de **standaard** modus voor uw web-app gebruiken om te kunnen werken met Traffic Manager.
> 
> 

1. Open de [Azure-Portal](https://portal.azure.com)in uw browser.
2. Klik op het tabblad **Web apps** op de naam van uw web-app, selecteer **instellingen**en selecteer vervolgens **aangepaste domeinen**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Klik op de Blade **aangepaste domeinen** op **hostnaam toevoegen**.
4. Gebruik de tekstvak **hostname** om de aangepaste domein naam in te voeren die u aan deze web-app wilt koppelen.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Klik op **valideren** om de configuratie van de domein naam op te slaan.
6. Wanneer u op **valideren** klikt, wordt de werk stroom voor domein verificatie gestart. Hiermee wordt gecontroleerd op domein eigendom en de beschik baarheid van hostnamen, en wordt een geslaagde of gedetailleerde fout gerapporteerd met prescriptieve richt lijnen voor het oplossen van de fout.    
7. Zodra de validatie van de knop **hostnaam toevoegen** is voltooid, wordt deze actief en kunt u de hostnaam toewijzen. Ga nu naar uw aangepaste domein naam in een browser. U ziet nu dat uw app wordt uitgevoerd met behulp van uw aangepaste domein naam. 
   
   Zodra de configuratie is voltooid, wordt de aangepaste domein naam weer gegeven in het gedeelte **domein namen** van uw web-app.

Op dit moment moet u de naam van de Traffic Manager domein in uw browser invoeren en zien dat u naar uw web-app kunt gaan.


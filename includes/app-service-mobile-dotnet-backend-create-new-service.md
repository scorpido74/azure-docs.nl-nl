---
title: bestand opnemen
description: bestand opnemen
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325792"
---
1. Meld u aan bij [Azure Portal].

2. Klik op **Een resource maken**.

3. Typ **Web App**in het zoekvak .
    
4. Selecteer **Web-App** in de lijst met resultaten op marktplaats.

5. Selecteer uw **abonnements-** en **resourcegroep** (selecteer een bestaande resourcegroep _of_ maak een nieuwe groep (met dezelfde naam als uw app)).

6. Kies een unieke **naam** van uw web-app.

7. Kies de **standaardoptie Publiceren** als **Code**.

8. In de **runtime-stack**moet u een versie selecteren onder **ASP.NET** of **Knooppunt**. Als u een .NET-backend bouwt, selecteert u een versie onder ASP.NET. Als u anders een op knooppunt gebaseerde toepassing target, selecteert u een van de versie uit Knooppunt.

9. Selecteer het juiste **besturingssysteem,** Linux of Windows. 

10. Selecteer de **regio** waar u deze app wilt implementeren. 

11. Selecteer het juiste **App-serviceplan** en druk op **Controleren en maken**. 

12. Selecteer **onder Resourcegroep**een bestaande resourcegroep _of_ maak een nieuwe groep (met dezelfde naam als uw app).

13. Klik **op Maken**. Wacht enkele minuten tot de service is geïmplementeerd voordat u doorgaat. Houd het meldingspictogram (bel) in de koptekst van de portal in de gaten voor statusupdates.

14. Zodra de implementatie is voltooid, klikt u op de sectie **Implementatiegegevens** en klikt u vervolgens op de bron van type **Microsoft.Web/sites**. Het zal u naar de App Service Web App die u zojuist hebt gemaakt. 

15. Klik op het **configuratieblad** onder **Instellingen** en klik in de **toepassingsinstellingen**op de knop **Nieuwe toepassingsinstelling.**

16. Voer op de pagina **Toepassingsinstelling toevoegen/bewerken** **de naam** in als **MobileAppsManagement_EXTENSION_VERSION** en Waarde als **laatste** en druk op OK.

U bent helemaal klaar om deze nieuw gemaakte App Service Web-app te gebruiken als een mobiele app.

<!-- URLs. -->
[Azure-portal]: https://portal.azure.com/
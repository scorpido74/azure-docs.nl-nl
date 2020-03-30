---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: e087a1db008422aeec8fd4e073a7476eebe4d54b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176717"
---
1. Meld u aan bij [Azure Portal].
2. Selecteer **+NIEUWE** > **Web + Mobiele** > **mobiele app**en geef vervolgens een naam op voor de back-end van uw mobiele apps.
3. Selecteer **voor Resourcegroep**een bestaande resourcegroep of maak een nieuwe groep (met dezelfde naam als uw app). 
4. Voor **app-serviceplan**is het standaardabonnement (in de [standaardlaag)](https://azure.microsoft.com/pricing/details/app-service/)geselecteerd. U kunt ook een ander abonnement selecteren of [een nieuw abonnement maken](../articles/app-service/app-service-plan-manage.md#create-an-app-service-plan). 

   De instellingen van het App Service-abonnement bepalen de [locatie, functies, kosten en rekenbronnen](https://azure.microsoft.com/pricing/details/app-service/) die aan uw app zijn gekoppeld. Zie [Azure App Service-abonnementen voor](../articles/app-service/overview-hosting-plans.md)meer informatie over appserviceplannen en het maken van een nieuw abonnement in een andere prijscategorie en op de gewenste locatie.
   
5. Selecteer **Maken**. Met deze stap wordt de back-end van Mobiele apps aanknopingspunt. 
6. Selecteer **in** het deelvenster Instellingen voor de nieuwe back-end van Mobiele apps de optie **Snel begin** > uw client-app-platform > **Een database verbinden.** 
   
   ![Selecties voor het verbinden van een database](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)
7. Selecteer **SQL-database** > **Een nieuwe database maken**in het deelvenster **Gegevensverbinding toevoegen.** Voer de databasenaam in, kies een prijscategorie en selecteer **Server**. U kunt deze nieuwe database opnieuw gebruiken. Als u al een database op dezelfde locatie hebt, kunt u in plaats daarvan **Een bestaande database gebruiken** kiezen. We raden het gebruik van een database op een andere locatie af vanwege bandbreedtekosten en hogere latentie.
   
   ![Een database selecteren](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)
8. Voer in het **deelvenster Nieuwe server** een unieke servernaam in het vak **Servernaam** in, geef een aanmelding en wachtwoord op, selecteer **Azure-services toestaan om toegang te krijgen tot de server**en selecteer **OK**. Met deze stap wordt de nieuwe database gemaakt.
9. Terug in het deelvenster **Gegevensverbinding toevoegen** selecteert u **Verbindingstekenreeks,** voert u de aanmeldings- en wachtwoordwaarden voor uw database in en selecteert **u OK**. 

   Wacht een paar minuten tot de database met succes is geïmplementeerd voordat u verdergaat.

<!-- URLs. -->
[Azure-portal]: https://portal.azure.com/

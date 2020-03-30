---
title: Upgrading Azure Application Insights Snapshot Debugger
description: Snapshot Debugger upgraden voor .NET-apps naar de nieuwste versie in Azure App Services of via Nuget-pakketten
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6991db6055ca8556adff67f194bac0f4aa04cb06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671389"
---
# <a name="upgrading-the-snapshot-debugger"></a>De momentopnamefoutopsporing upgraden

Om de best mogelijke beveiliging voor uw gegevens te bieden, neemt Microsoft afstand van TLS 1.0 en TLS 1.1, waarvan is aangetoond dat ze kwetsbaar zijn voor vastberaden aanvallers. Als u een oudere versie van de site-extensie gebruikt, is een upgrade nodig om door te kunnen werken. In dit document worden de stappen beschreven die nodig zijn om uw momentopnamefoutopsporing te upgraden naar de nieuwste versie. Er zijn twee primaire upgradepaden, afhankelijk van of u de momentopnamefoutbugger hebt ingeschakeld met behulp van een site-extensie of als u een SDK/Nuget hebt gebruikt die aan uw toepassing is toegevoegd. Beide upgradepaden worden hieronder besproken. 

## <a name="upgrading-the-site-extension"></a>De site-extensie upgraden

> [!IMPORTANT]
> Oudere versies van Application Insights gebruikten een extensie voor clientsinsights, de zogenaamde _Application Insights-extensie voor Azure App Service._ De huidige Application Insights-ervaring wordt ingeschakeld door App-instellingen in te stellen om een vooraf geïnstalleerde site-extensie op te lichten.
> Om conflicten te voorkomen, waardoor uw site niet meer werkt, is het belangrijk om eerst de extensie van de privésite te verwijderen. Zie stap 4 hieronder.

Als u de momentopnamefoutopsporing hebt ingeschakeld met behulp van de siteextensie, u upgraden via de volgende procedure:

1. Meld u aan bij Azure Portal.
2. Navigeer naar uw bron waarop toepassingsinzichten en momentopnamenbugger zijn ingeschakeld. Navigeer bijvoorbeeld voor een web-app naar de bron App Service:

   ![Schermafbeelding van afzonderlijke App Service-bron met de naam DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Zodra u naar uw resource bent genavigeerd, klikt u op het blad Extensies en wacht u tot de lijst met extensies wordt ingevuld:

   ![Schermafbeelding van App Service-extensies met toepassingsinsights-extensie voor Azure App Service geïnstalleerd](./media/snapshot-debugger-upgrade/application-insights-site-extension-to-be-deleted.png)

4. Als er een versie van _de Application Insights-extensie voor Azure App Service_ is geïnstalleerd, selecteert u deze en klikt u op Verwijderen. Bevestig **Ja** om de extensie te verwijderen en wacht tot de delete is voltooid voordat u naar de volgende stap gaat.

   ![Schermafbeelding van App-service-extensies met toepassingsinsights-extensie voor Azure App-service met de knop Verwijderen gemarkeerd](./media/snapshot-debugger-upgrade/application-insights-site-extension-delete.png)

5. Ga naar het overzichtsblad van uw resource en klik op Application Insights:

   ![Schermafbeelding van drie knoppen. Knop Centrum met naam Application Insights is geselecteerd](./media/snapshot-debugger-upgrade/application-insights-button.png)

6. Als dit de eerste keer is dat u het blade Van Application Insights voor deze app-service bekijkt, wordt u gevraagd toepassingsinzichten in te schakelen. Selecteer **Toepassingsinzichten inschakelen**.
 
   ![Schermafbeelding van de eerste ervaring voor het mes Application Insights met de knop Toepassingsinzichten inschakelen gemarkeerd](./media/snapshot-debugger-upgrade/turn-on-application-insights.png)

7. De huidige instellingen voor Application Insights worden weergegeven. Tenzij u van de gelegenheid gebruik wilt maken om uw instellingen te wijzigen, u ze laten zoals het is. De knop **Toepassen** aan de onderkant van het blad is standaard niet ingeschakeld en u moet een van de instellingen schakelen om de knop te activeren. U hoeft geen werkelijke instellingen te wijzigen, maar u de instelling wijzigen en deze onmiddellijk weer wijzigen. We raden u aan de profilerinstelling te schakelen en vervolgens **Toepassen te selecteren.**

   ![Schermafbeelding van de pagina Application Insights App Service-configuratie met knop Toepassen in het rood gemarkeerd](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

8. Zodra u op **Toepassen**klikt, wordt u gevraagd de wijzigingen te bevestigen.

    > [!NOTE]
    > De site wordt opnieuw gestart als onderdeel van het upgradeproces.

   ![Schermafbeelding van de controleprompt van App Service. Tekstvak toont bericht: "We passen nu wijzigingen in uw app-instellingen toe en installeren onze tools om uw Application Insights-bron te koppelen aan de web-app. Hierdoor wordt de site opnieuw opgestart. Wil je doorgaan?"](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

9. Klik **op Ja** om de wijzigingen toe te passen en te wachten tot het proces is voltooid.

De site is nu geüpgraded en is klaar voor gebruik.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Snapshot Debugger upgraden met SDK/Nuget

Als de toepassing een `Microsoft.ApplicationInsights.SnapshotCollector` versie van onder versie 1.3.1 gebruikt, moet deze worden geüpgraded naar een [nieuwere versie](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) om te kunnen blijven werken.

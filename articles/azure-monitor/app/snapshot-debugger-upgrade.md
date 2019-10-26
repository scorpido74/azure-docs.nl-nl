---
title: Azure-toepassing Insights-Snapshot Debugger upgrade voor .NET-Apps | Microsoft Docs
description: Snapshot Debugger upgraden naar de nieuwste versie op Azure-app-Services of via Nuget-pakketten
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: MarioHewardt
ms.author: marioh
ms.date: 03/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: e2b21b7cbb6b04da0c93e73c0cacb8a05c338bde
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899837"
---
# <a name="upgrading-the-snapshot-debugger"></a>De Snapshot Debugger bijwerken

Micro soft gaat over naar de best mogelijke beveiliging voor uw gegevens door de TLS 1,0 en TLS 1,1 te verplaatsen, die zijn aangetoond dat ze kwetsbaar zijn voor bepaalde aanvallers. Als u een oudere versie van de site-uitbrei ding gebruikt, moet er een upgrade worden uitgevoerd om verder te werken. In dit document vindt u een overzicht van de stappen die nodig zijn om uw momentopname fout opsporing te upgraden naar de nieuwste versie. Er zijn twee primaire upgrade paden, afhankelijk van of u de Snapshot Debugger hebt ingeschakeld met behulp van een site-uitbrei ding of als u een SDK-Nuget hebt gebruikt die aan uw toepassing is toegevoegd. Beide upgrade paden worden hieronder besproken. 

## <a name="upgrading-the-site-extension"></a>De site-uitbrei ding bijwerken

Als u het fout opsporingsprogramma voor moment opnamen met de site-uitbrei ding hebt ingeschakeld, kunt u eenvoudig een upgrade uitvoeren met behulp van de volgende procedure:

1. Meld u aan bij Azure Portal.
2. Navigeer naar uw resource waarvoor Application Insights en snap shot-fout opsporing is ingeschakeld. Bijvoorbeeld: voor een web-app gaat u naar de App Service-Resource.

   ![Scherm afbeelding van de afzonderlijke App Service resource met de naam DiagService01](./media/snapshot-debugger-upgrade/app-service-resource.png)

3. Nadat u naar uw resource hebt genavigeerd, klikt u op Application Insights op de Blade overzicht:

   ![Scherm afbeelding van drie knoppen. Knop Centreren met naam Application Insights is geselecteerd](./media/snapshot-debugger-upgrade/application-insights-button.png)

4. Er wordt een nieuwe blade geopend met de huidige instellingen. Tenzij u de kans wilt maken om uw instellingen te wijzigen, kunt u ze ongewijzigd laten. De knop **Toep assen** aan de onderkant van de Blade is niet standaard ingeschakeld en u moet een van de instellingen in-of uitschakelen om de knop te activeren. U hoeft geen echte instellingen te wijzigen. in plaats daarvan kunt u de instelling wijzigen en direct weer wijzigen. Het is raadzaam om de instelling van de Profiler in te scha kelen en vervolgens **Toep assen**te selecteren.

   ![Scherm afbeelding van de pagina configuratie van Application Insights App Service met de knop Toep assen rood gemarkeerd](./media/snapshot-debugger-upgrade/view-application-insights-data.png)

5. Zodra u op **Toep assen**klikt, wordt u gevraagd om de wijzigingen te bevestigen.

    > [!NOTE]
    > De site wordt opnieuw opgestart als onderdeel van het upgrade proces.

   ![Scherm afbeelding van de opdracht prompt voor het Toep assen van App Service. In het tekstvak wordt het volgende bericht weer gegeven: "We zullen nu wijzigingen Toep assen op de app-instellingen en de hulpprogram ma's installeren om uw Application Insights-resource aan de web-app te koppelen. Hiermee wordt de site opnieuw opgestart. Wilt u door gaan? "](./media/snapshot-debugger-upgrade/apply-monitoring-settings.png)

6. Klik op **Ja** om de wijzigingen toe te passen. Tijdens het proces wordt een melding weer gegeven dat de wijzigingen worden toegepast:

   ![Scherm opname van apply-wijzigingen-bericht extensies bijwerken dat wordt weer gegeven in de rechter bovenhoek](./media/snapshot-debugger-upgrade/updating-extensions.png)

Zodra de taak is voltooid **, wordt er** een melding weer gegeven.

   ![Scherm opname van bericht waarin wordt aangegeven dat wijzigingen worden aangebracht](./media/snapshot-debugger-upgrade/changes-are-applied.png)

De site is nu bijgewerkt en is klaar voor gebruik.

## <a name="upgrading-snapshot-debugger-using-sdknuget"></a>Snapshot Debugger bijwerken met SDK-Nuget

Als de toepassing gebruikmaakt van een versie van `Microsoft.ApplicationInsights.SnapshotCollector` onder 1.3.1, moet u een upgrade uitvoeren naar een [nieuwere versie](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) om verder te werken.

---
title: Onderhoudsschema's voor Synapse SQL-pool
description: Onderhoudsplanning stelt klanten in staat om te plannen rond de noodzakelijke geplande onderhoudsgebeurtenissen die Azure Synapse Analytics gebruikt om nieuwe functies, upgrades en patches uit te rollen.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 43fc32e910c51e8b70e15aa49584a18e5b703fca
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631586"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Onderhoudsschema's gebruiken om service-updates en onderhoud te beheren

De functie onderhoudsschema integreert de servicestatus geplande onderhoudsmeldingen, de monitor voor de controle van de resourcestatus en de onderhoudsplanningsservice voor Synapse SQL-pool (datawarehouse) in Azure Synapse Analytics.

U moet onderhoudsplanning gebruiken om een tijdvenster te kiezen wanneer het handig is om nieuwe functies, upgrades en patches te ontvangen. U moet binnen een periode van zeven dagen een primair en secundair onderhoudsvenster kiezen, elk venster moet binnen afzonderlijke dagbereiken zijn.

U bijvoorbeeld een primair venster van zaterdag 22:00 tot zondag 01:00 uur plannen en vervolgens een secundair venster van woensdag 19:00 tot 22:00 uur plannen. Als onderhoud niet kan worden uitgevoerd tijdens uw primaire onderhoudsvenster, zal het het onderhoud opnieuw proberen tijdens uw secundaire onderhoudsvenster. Serviceonderhoud kan soms plaatsvinden tijdens zowel de primaire als de secundaire ramen. Om ervoor te zorgen dat alle onderhoudswerkzaamheden snel worden voltooid, kunnen DW400C en lagere gegevensmagazijnlagen het onderhoud buiten een aangewezen onderhoudsvenster voltooien.

In alle nieuw gemaakte exemplaren van het gegevensmagazijn wordt tijdens de implementatie een systeemgedefinieerd onderhoudsschema toegepast. De planning kan worden bewerkt zodra de implementatie is voltooid.

Hoewel een onderhoudsvenster tussen de drie en acht uur kan zijn, betekent dit niet dat het datawarehouse offline is voor de duur. Onderhoud kan op elk moment plaatsvinden binnen dat venster en u moet verwachten dat een enkele verbinding tijdens die periode van ~ 5 -6 minuten als de service implementeert nieuwe code in uw data warehouse. DW400c en lager kunnen meerdere korte verliezen in connectiviteit op verschillende tijdstippen tijdens het onderhoudsvenster ervaren. Wanneer het onderhoud wordt gestart, worden alle actieve sessies geannuleerd en worden niet-vastgelegde transacties teruggedraaid. Als u de uitvaltijd van bijvoorbeelden wilt minimaliseren, moet u ervoor zorgen dat uw gegevensmagazijn geen langlopende transacties heeft vóór de door u gekozen onderhoudsperiode.

Alle onderhoudsbewerkingen moeten binnen de opgegeven onderhoudsvensters worden uitgevoerd, tenzij we een tijdgevoelige update moeten implementeren. Als uw gegevensmagazijn wordt onderbroken tijdens een gepland onderhoud, wordt het tijdens de hervattingsbewerking bijgewerkt. U ontvangt hiervan onmiddellijk bericht nadat het onderhoud van uw gegevensmagazijn is voltooid.

## <a name="alerts-and-monitoring"></a>Waarschuwingen en bewaking

Integratie met servicestatusmeldingen en de Resource Health Check Monitor stellen klanten in staat om op de hoogte te blijven van dreigende onderhoudsactiviteiten. Deze automatisering maakt gebruik van Azure Monitor. U zelf bepalen hoe u op de hoogte wilt worden gesteld van dreigende onderhoudsgebeurtenissen. U ook kiezen welke geautomatiseerde stromen u zullen helpen downtime te beheren en de operationele impact te minimaliseren.

Een 24-uurs voorafgaande kennisgeving gaat vooraf aan alle onderhoudsgebeurtenissen die niet voor de DWC400c en lagere lagen zijn.

> [!NOTE]
> In het geval dat we een time critical update moeten implementeren, kunnen de geavanceerde meldingstijden aanzienlijk worden verkort.

Als u vooraf een melding hebt ontvangen dat er onderhoud plaatsvindt, maar onderhoud niet kan worden uitgevoerd tijdens de periode in de melding, ontvangt u een annuleringsmelding. Het onderhoud wordt dan hervat tijdens de volgende geplande onderhoudsperiode.

Alle actieve onderhoudsgebeurtenissen worden weergegeven in de sectie **Servicestatus - Gepland onderhoud.** De geschiedenis van de servicestatus bevat een volledig overzicht van gebeurtenissen uit het verleden. U het onderhoud controleren via het azure service statusportaldashboard tijdens een actieve gebeurtenis.

### <a name="maintenance-schedule-availability"></a>Beschikbaarheid onderhoudsschema

Zelfs als onderhoudsplanning niet beschikbaar is in de geselecteerde regio, u uw onderhoudsschema op elk gewenst moment bekijken en bewerken. Wanneer onderhoudsplanning beschikbaar komt in uw regio, wordt het geïdentificeerde schema onmiddellijk actief op uw Synapse SQL-pool.

## <a name="view-a-maintenance-schedule"></a>Een onderhoudsschema weergeven

Standaard zijn alle nieuw gemaakte gegevensmagazijninstanties een primair en secundair onderhoudsvenster van acht uur toegepast tijdens de implementatie. Zoals hierboven aangegeven, u de vensters wijzigen zodra de implementatie is voltooid. Er vindt geen onderhoud plaats buiten de opgegeven onderhoudsperioden zonder voorafgaande kennisgeving.

Voer de volgende stappen uit om het onderhoudsschema weer te geven dat is toegepast op uw Synapse SQL-groep:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer de Synapse SQL-groep die u wilt weergeven.
3. De geselecteerde Synapse SQL-pool wordt geopend op het overzichtsblad. Het onderhoudsschema dat wordt toegepast op het gegevensmagazijn wordt weergegeven onder **Onderhoudsschema**.

![Overzichtsblad](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Een onderhoudsschema wijzigen

Een onderhoudsschema kan op elk gewenst moment worden bijgewerkt of gewijzigd. Als de geselecteerde instantie een actieve onderhoudscyclus doormaakt, worden de instellingen opgeslagen. Ze zullen actief worden tijdens de volgende geïdentificeerde onderhoudsperiode. [Meer informatie](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) over het bewaken van uw datawarehouse tijdens een actief onderhoudsevenement.

## <a name="identifying-the-primary-and-secondary-windows"></a>De primaire en secundaire vensters identificeren

De primaire en secundaire vensters moeten afzonderlijke dagbereiken hebben. Een voorbeeld is een primair venster van dinsdag tot en met donderdag en een secundair venster van zaterdag-zondag.

Voer de volgende stappen uit om het onderhoudsschema voor uw Synapse SQL-groep te wijzigen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer de Synapse SQL-groep die u wilt bijwerken. De pagina wordt geopend op het overzichtsblad.
Open de pagina voor instellingen voor onderhoudsschema's door de koppeling **Onderhoudsschema overzicht** op het overzichtsblad te selecteren. Of selecteer de optie **Onderhoudsschema** in het menu resource aan de linkerkant.

    ![Opties voor het overzichtsblad](./media/maintenance-scheduling/maintenance-change-option.png)

3. Identificeer het gewenste dagbereik voor uw primaire onderhoudsvenster met behulp van de opties boven aan de pagina. Deze selectie bepaalt of uw primaire venster op een doordeweekse dag of in het weekend plaatsvindt. Uw selectie werkt de vervolgkeuzewaarden bij.
Tijdens de preview ondersteunen sommige regio's mogelijk nog niet de volledige set beschikbare **dagopties.**

   ![Blad onderhoudsinstellingen](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Kies de primaire en secundaire onderhoudsvensters van uw voorkeur met behulp van de vervolgkeuzelijsten:
   - **Dag**: Voorkeursdag om onderhoud uit te voeren tijdens het geselecteerde venster.
   - **Begintijd**: Voorkeur begintijd voor het onderhoudsvenster.
   - **Tijdvenster**: Voorkeursduur van uw tijdvenster.

   Het **overzichtsgebied Schema** onder aan het blad wordt bijgewerkt op basis van de waarden die u hebt geselecteerd.
  
5. Selecteer **Opslaan**. Er verschijnt een bericht waarin wordt bevestigd dat uw nieuwe planning nu actief is.

   Als u een planning opslaat in een regio die geen onderhoudsplanning ondersteunt, wordt het volgende bericht weergegeven. Uw instellingen worden opgeslagen en worden actief wanneer de functie beschikbaar komt in uw geselecteerde regio.

   ![Bericht over de beschikbaarheid van regio's](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie](../../azure-monitor/platform/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) over het maken, bekijken en beheren van waarschuwingen met Azure Monitor.
- [Meer informatie](../..//azure-monitor/platform/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) over webhook-acties voor logboekwaarschuwingsregels.
- [Meer weten?](../..//azure-monitor/platform/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Actiegroepen maken en beheren.
- [Meer informatie](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) over Azure Service Health.

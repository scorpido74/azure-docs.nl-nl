---
title: Belangrijke metrische gegevens met Cloudyn-dashboards weergeven in Azure | Microsoft Docs
description: Dit artikel wordt beschreven hoe u belangrijke metrische gegevens met dashboards in Cloudyn kunt bekijken.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: c0651dd006585e4ed5b8b888d6e55974c5c45a1d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990682"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Kosten van metrische gegevens met dashboards weergeven-sleutel

Dashboards in Cloudyn bieden een weergave op hoog niveau van rapporten. Dashboards kunnen u kosten voor belangrijke metrische gegevens weergeven in één weergave. Ze bieden ook zakelijke trend gemarkeerd om te helpen u belangrijke zakelijke beslissingen kunt komen.

Dashboards worden ook gebruikt om weergaven te maken voor mensen met verschillende verantwoordelijkheden in uw organisatie, waaronder:

- Financieel controleur
- Eigenaar van de toepassing of het project
- DevOps-technicus
- Leidinggevenden

Dashboards bestaan uit widgets en elke widget is in feite de miniatuur van een rapport. Klik op een widget om het rapport te openen. Wanneer u rapporten wilt aanpassen, moet u ze opslaat in Mijn rapporten en ze worden toegevoegd aan het dashboard.

Dashboard versies verschillen Management (MSP), Enterprise en Premium Cloudyn-gebruikers. De verschillen worden bepaald door de toegangsniveaus voor entiteiten. Zie voor meer informatie over toegangsniveaus [entiteit toegangsniveaus](tutorial-user-access.md#entity-access-levels).

Beschikbaarheid van het dashboard is afhankelijk van het type van cloud serviceprovider-account dat wordt gebruikt bij het weergeven van dashboards. Het type informatie beschikbaar is en die worden verzameld door Cloudyn is van invloed op rapporten in dashboards. Bijvoorbeeld, als u een AWS-account hebt weergegeven vervolgens niet de S3-tracering in-dashboard. Op dezelfde manier als u Azure Resource Manager-toegang tot Cloudyn niet inschakelt weergegeven vervolgens niet elke Azure-specifieke informatie in de dashboardwidgets optimaliseren.

U kunt een van de vooraf gemaakte dashboards gebruiken of u kunt uw eigen dashboard met aangepaste rapporten maken. Als u niet bekend met Cloudyn-rapporten bent, Zie [rapporten van Cloudyn gebruik](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Een aangepast dashboard maken

Als u wilt snel aan de slag met een aangepast dashboard, kunt u een bestaande resourcegroep voor het gebruik van de eigenschappen ervan te dupliceren. Vervolgens kunt u deze om aan uw behoeften te wijzigen. Klik op het dashboard dat u wilt kopiëren, **OpslaanAls**. U kunt alleen aangepaste dashboards dupliceren — u kunt de dashboards die opgenomen in Cloudyn zijn niet dupliceren.

Een aangepast dashboard maken:

1. Klik op de startpagina, **nieuwe toevoegen +** . De pagina Mijn Dashboard wordt weergegeven.  
    ![Mijn dashboardpagina waar u nieuwe rapporten toevoegen](./media/dashboards/my-dashboard.png)
2. Klik op **toevoegen van nieuwe rapport**. Het rapport toevoegen wordt weergegeven.
3. Selecteer het rapport dat u wilt toevoegen aan het dashboard-object. De widget wordt toegevoegd aan het dashboard.
4. Herhaal de voorgaande stappen totdat het dashboard voltooid is.
5. Wijzig de naam van het dashboard, klikt u op de naam van het dashboard op de startpagina van het Dashboard en typ de nieuwe naam.

## <a name="modify-a-custom-dashboard"></a>Een aangepast dashboard wijzigen

Zoals het maken van een aangepast dashboard, kunt u de dashboards die zijn opgenomen met Cloudyn niet wijzigen. Een aangepast dashboard voor een rapport wijzigen:

1. Zoek in het dashboard, het rapport dat u wilt wijzigen en klik op **bewerken**. Het rapport wordt weergegeven.
2. Aanbrengen van wijzigingen die u wilt aan het rapport en klikt u op **opslaan**. Het rapport wordt bijgewerkt en uw wijzigingen worden weergegeven.

## <a name="share-a-custom-dashboard"></a>Een aangepast dashboard delen

U kunt een aangepast dashboard delen met anderen, _openbare_ of _mijn entiteit_. Wanneer u naar openbaar deelt, kunnen alle gebruikers kunnen het dashboard bekijken. Alleen gebruikers met toegang tot de huidige entiteit kunnen het dashboard weergeven wanneer u deze entiteit deelt. De stappen voor het delen van een aangepast dashboard met openbare en Mijn entiteit zijn vergelijkbaar.

Voor het delen van een aangepast dashboard voor publiek:

1. In een dashboard, klikt u op **Dashboardinstellingen**. De instellingen voor Dashboard wordt weergegeven.  
    ![instellingen voor dashboard voor een aangepast dashboard](./media/dashboards/dashboard-options.png)
2. Klik op de pijl in het Dashboardinstellingen en klik vervolgens op **openbare**. Het dialoogvenster voor bevestiging van openbare Dashboard wordt weergegeven.
3. Klik op **Ja**. Het dashboard is nu beschikbaar voor anderen.

## <a name="delete-a-custom-dashboard-report"></a>Een aangepast dashboard voor een rapport verwijderen

U kunt een aangepast rapport-onderdeel verwijderen vanuit het dashboard. Verwijderen van het rapport vanuit het dashboard verwijderen niet uit de lijst met rapporten. In plaats daarvan het rapport verwijdert, wordt deze vanuit het dashboard alleen.

Als u wilt verwijderen van een onderdeel dashboard, klikt u op de dashboard-component, klikt u op **verwijderen**. Te klikken op **verwijderen** onmiddellijk gewist van het onderdeel van het dashboard.

## <a name="share-a-dashboard-enterprise"></a>Een dashboard (Enterprise)

U kunt aangepaste dashboards voor alle gebruikers in uw organisatie of met de gebruikers van de huidige entiteit delen. Delen van een dashboard kan anderen geeft een overzicht op hoog niveau van de KPI. Wanneer u een dashboard deelt, worden automatisch het dashboard voor al uw Cloudyn entiteiten/klanten repliceert. Wijzigingen aan het gedeelde dashboard worden automatisch bijgewerkt.

Een dashboard delen met alle gebruikers, met inbegrip van subentiteiten:

1. Klik op de startpagina dashboard **bewerken**.
2. Klik op **Share** en selecteer vervolgens **openbare**.
3. Bevestigingsvenster globale openbare Dashboard wordt weergegeven.
4. Klik op **Ja** aan het dashboard instellen als een globale openbare dashboard.

Een dashboard delen met alle gebruikers van een huidige entiteit:

1. Klik op de startpagina Dashboard **bewerken**.
2. Klik op **Share** en selecteer vervolgens **mijn entiteit**.
3. Klik op **Ja** aan het dashboard instellen als een openbare dashboard.

## <a name="duplicate-a-custom-dashboard"></a>Een aangepast dashboard dupliceren

Wanneer u een nieuw dashboard maakt, is het raadzaam om vergelijkbare eigenschappen van een bestaand dashboard te gebruiken. U kunt het dashboard voor het maken van een nieuwe dupliceren.

U kunt alleen aangepaste dashboards dupliceren. U kunt standard dashboards niet dupliceren.

Voor het dupliceren van (gekloond) een aangepast dashboard:

1. Klik op het Dashboard dat u wilt dupliceren, **OpslaanAls**. Een nieuw dashboard wordt geopend met dezelfde naam en een nummer.
2. Wijzig de naam van het dubbele dashboard en als u wilt wijzigen.

\- Of -

1. In de instellingen voor Dashboard, klikt u op **OpslaanAls** op de regel van het dashboard dat u wilt dupliceren.
2. Het dubbele dashboard geopend.
3. Wijzig de naam van het dashboard en als u wilt wijzigen.

## <a name="set-a-default-dashboard"></a>Een standaarddashboard instellen

U kunt een dashboard instellen als standaard. Instellen op de standaard, kunt u als het meest linkse tabblad in de dashboardlijst weergegeven. De standaard-dashboard wordt weergegeven wanneer de Cloudyn-portal te openen.

- Klik op het dashboardtabblad dat u wilt instellen als standaard en klik vervolgens op **standaard** aan de rechterkant.

\- Of -

1. Klik op **Dashboardinstellingen** de lijst met beschikbare dashboards weergeven en selecteren van het dashboard dat u wilt instellen als standaard.  
    ![dashboard-opties voor een standaarddashboard](./media/dashboards/dashboard-options.png)
2. Klik op **standaard** in de regel van het dashboard. Het Dashboard standaard bevestigingsvenster weergegeven.
3. Klik op **Ja**. Het dashboard is ingesteld op standaard.

## <a name="management-dashboard"></a>Management Dashboard
Het beheer (of MSP-dashboard voor MSP-gebruikers) dashboard bevat belangrijke functies van de belangrijkste rapporttypen.  
![Dashboard voor beheer met diverse rapporten](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Overzicht Kostenentiteiten (alleen voor Enterprise)
Deze widget bevat een overzicht van de kosten voor beheerde entiteiten, inclusief het aantal entiteiten en het aantal accounts.
- Klik op de widget om de Details van de Enterprise-rapport te openen.

### <a name="cost-over-time"></a>Na verloop van tijd kosten
Aan de hand van deze widget kunt u kostentrends te spotten. Deze handleiding komen de kosten voor de laatste dag, op basis van de trend van de afgelopen 30 dagen.
- Klik op de widget om te openen van het rapport Actual Cost Over Time om weer te geven en te filteren als u meer details.

### <a name="asset-controller"></a>Assetcontroller
Deze widget ziet u het aantal actieve instanties van de vorige dag, boven de gebruikstrend gedurende de afgelopen 30 dagen.
- Klik op de widget om te openen van het dashboard Assetcontroller.

### <a name="unused-ri-detector"></a>Detectie ongebruikte gereserveerde instanties
Deze widget markeert het nummer van Amazon EC2 niet-gebruikte reserveringen.
- Klik op de widget u opent de Currently Unused Reservations lijst hier u de niet-gebruikte vindt reserveringen die u kunt wijzigen.

### <a name="cost-by-service"></a>Kosten per Service
Deze widget licht afgeschreven kosten door de service voor de afgelopen 30 dagen. Beweeg de muisaanwijzer over de cirkeldiagram in op de kosten per service.
- Klik op de widget om het rapport Actual Cost Analysis te openen.

### <a name="potential-savings"></a>Potentiële besparingen
Deze widget geeft prijsaanbevelingen voor Amazon EC2 en Amazon RDS. exemplaartype
- Klik op de widget open het rapport analyse van de besparingen. Geeft de kosten per exemplaartypen met potentiële besparingen.

### <a name="compute-instances---daily-trend"></a>Rekeninstanties - Trend per dag
Deze widget geeft de actieve exemplaren weer op type, voor de afgelopen 30 dagen.
- Klik op de widget om te openen van het rapport instanties gedurende een periode, waar u een overzicht van alle exemplaren die worden uitgevoerd tijdens de afgelopen 30 dagen kunt bekijken.

### <a name="storage-by-department"></a>Opslag per afdeling
Deze widget geeft de storage-services die worden gebruikt door de afdelingen weer. Beweeg de muisaanwijzer over het cirkeldiagram om te zien van uw opslagverbruik per afdeling.
- Klik op de widget om te openen, het vastleggen van de S3-dashboard.

## <a name="cost-controller-dashboard"></a>Kosten van de Controller-dashboard
De kosten-Controller-dashboard ziet u vooraf ingestelde cost allocation hoogtepunten.  
![Kosten Controller-dashboard, diverse rapporten weergeven](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Na verloop van tijd kosten
Deze widget helpt u kostentrends te spotten. Deze handleiding komen de kosten voor de laatste dag, op basis van de trend van de afgelopen 30 dagen.
- Klik op de widget om te openen van het rapport Actual Cost Over Time om weer te geven en te filteren als u meer details.

### <a name="monthly-cost-trends"></a>Maandelijkse kosten Trends
Deze widget worden belicht afgeschreven geraamde uitgaven en uw werkelijke uitgaven sinds het begin van de maand.
- Klik op het object als het rapport Current Month Projected Cost, waarmee u een overzicht van de kosten voor maand tot heden wilt openen.

Dit rapport worden de kosten vanaf het begin van maand, de kosten van de vorige maand en de huidige maand geschatte kosten. De huidige maand geschatte kosten worden berekend door toe te voegen van de actuele maandelijkse kosten en de projectie. De projectie is gebaseerd op de kosten bewaakt in de afgelopen 30 dagen.

### <a name="12-month-planner"></a>12 maanden Planner
Deze widget markeert de geschatte kosten in de komende 12 maanden en de potentiële besparingen.
- Klik op de widget om het rapport Annual Projected Cost te openen.

### <a name="cost-by-service"></a>Kosten per Service
Deze widget licht afgeschreven kosten door de service voor de afgelopen 30 dagen.
- Beweeg de muisaanwijzer over de cirkeldiagram in op de kosten per service.
- Klik op de widget om het rapport Actual Cost Analysis te openen.

### <a name="cost-by-account"></a>Kosten per Account
Deze widget markeert afgeschreven kosten door account voor de afgelopen 30 dagen.
- Beweeg de muisaanwijzer over de cirkeldiagram in op de kosten per account.
- Klik op de widget om het rapport Actual Cost Analysis te openen.

### <a name="cost-trend-by-day"></a>Kostentrend per dag
Deze widget hoogtepunten in de afgelopen 30 dagen uitgaven.
- Beweeg de muisaanwijzer over het staafdiagram om te zien van de kosten per dag.
- Klik op de widget om het rapport Actual Cost Over Time te openen.

### <a name="cost-trend-by-month---last-6-months"></a>Trend kosten per maand: laatste 6 maanden

Deze widget hoogtepunten in de afgelopen zes maanden uitgaven.
- Beweeg de muisaanwijzer over het staafdiagram om te zien van de kosten per maand.
- Klik op de widget om het rapport Actual Cost Over Time te openen.

## <a name="asset-controller-dashboard"></a>Asset-Controller-dashboard

Dit dashboard geeft het aantal actieve instanties, beschikbare en gebruikte schijven, distributie van exemplaartypen en storage-gegevens weer.  
![Asset-Controller-dashboard met diverse rapporten](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Instanties
Deze widget geeft het aantal instanties op basis van de gebruikstrend gedurende de afgelopen 30 dagen.
- Klik op de widget om het rapport instanties gedurende een periode te openen.

### <a name="disks"></a>Schijven
Deze widget markeert het totale aantal en het volume van de schijven die in gebruik en beschikbaar zijn.
- Klik op de widget om de actieve schijven-rapport te openen.

### <a name="instance-type-distribution"></a>Exemplaar Type distributiepunt
Deze widget markeert de instantietypen in een cirkeldiagram.
- Klik op de widget om het rapport exemplaar distributie, waarmee u een uitsplitsing van uw actieve instanties van de geselecteerde aggregatie te openen.

### <a name="compute-instances---daily-trend"></a>Rekeninstanties - Trend per dag
Deze widget markeert de compute-exemplaren (spot, gereserveerde en op aanvraag) per dag voor de afgelopen 30 dagen.
- Beweeg de muisaanwijzer over de grafiek om het aantal rekenprocessen, per type per dag weer te geven.
- Klik op de widget om het rapport instanties gedurende een periode te openen.

### <a name="all-buckets-s3"></a>Alle Buckets (S3)
Deze widget illustreert de totale S3-opslag en het aantal objecten die zijn opgeslagen.
- Klik op de widget om te openen van de S3-tracering in-Dashboard. Het dashboard kunt u zoeken, analyseren en weergeven van uw huidige gebruik van opslag en trends.

### <a name="sql-db-instances-rds"></a>SQL database-instanties (RDS)
Deze widget ziet u het aantal actieve instanties van Amazon extern bureaublad-services op basis van de trend van de afgelopen 30 dagen.
- Klik op de widget om het rapport extern bureaublad-services-exemplaar gedurende een periode te openen.

## <a name="optimizer-dashboard"></a>Optimalisatie van Dashboard
Dit dashboard geeft de downsizing aanbevelingen, niet-gebruikte resources en potentiële besparingen.  
![Optimalisatie van dashboard diverse rapporten weergeven](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>Gereserveerde instanties Calculator
Deze widget geeft het aantal aanbeveling voor het kopen van gereserveerde instanties weer en markeert u de jaarlijkse besparingen.
- Klik op de widget om te openen van de gereserveerde instantie Rekenmachine waar u wanneer bepalen kunt u on-demand versus gereserveerd prijzen en abonnementen.

### <a name="sizing"></a>Grootte aanpassen
Deze widget worden de aanbevolen grootte en potentiële besparingen gemarkeerd als geïmplementeerd.
- Klik op de widget om het rapport EC2 Cost Effective Sizing Recommendations te openen.

### <a name="unused-ri-detector"></a>Detectie ongebruikte gereserveerde instanties
Deze widget markeert het nummer van Amazon EC2 niet-gebruikte reserveringen.
- Klik op de widget om de Currently Unused Reservations rapport Hier vindt u de niet-gebruikte reserveringen die u kunt aanpassen te openen.

###  <a name="available-disks"></a>Beschikbare schijven
Deze widget ziet u het aantal niet-gekoppelde schijven in uw implementatie.
- Klik op de widget om het rapport niet-gekoppelde schijven te openen.

### <a name="rds-ri-calculator"></a>Extern bureaublad-services RI Calculator
Deze widget markeert het nummer van de reservering aanbevelingen voor uw Amazon RDS-instanties en de potentiële besparingen.
- Klik op de widget om te openen van de extern bureaublad-services RI Buying Recommendations-rapport waarin u de Cloudyn-aanbevelingen voor het gebruik van gereserveerde instanties in plaats van on-demand instanties kunt bekijken.

### <a name="rds-sizing"></a>Grootte van de extern bureaublad-services
Deze widget bevat het nummer van de aanbevelingen van de grootte en de potentiële besparingen.
- Klik op de widget om het rapport extern bureaublad-services Sizing Recommendations, welke geeft gedetailleerde Amazon RDS formaat aanbevelingen te openen.

De optimalisatie aanbevelingen zijn gebaseerd op het gebruik en prestaties gegevens bewaakt in de afgelopen maand.

## <a name="s3-tracker-dashboard"></a>S3-tracering in dashboard
Het vastleggen van de S3-dashboard kunt u zoeken, analyseren en weergeven van uw huidige gebruik van opslag en trends.  
![S3-tracering dashboard, diverse rapporten weergeven](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Alle Buckets
Deze widget markeert de totale grootte van alle uw buckets, in GB, en het totale aantal objecten in de buckets.
- Klik op de widget om de distributie van de grootte van de S3-rapport te openen. Het rapport kunt u de grootte van uw S3 door bucket, op het hoogste niveau map, opslagklasse en versieregistratietstatus kunt analyseren.

### <a name="bucket-properties"></a>Bucket-eigenschappen
Deze widget markeert het totale aantal buckets voor opslag.
- Klik op de widget om de eigenschappen van de S3-Bucket-rapport weer te geven.

### <a name="scan-status"></a>Scanstatus
Deze widget gemarkeerd wanneer de laatste scan voor S3 is uitgevoerd en wanneer het volgende object wordt gestart.
- Klik op de widget om te openen van het statusrapport S3 scannen.

### <a name="storage-by-bucket"></a>Opslag per Bucket
Deze widget markeert het percentage dat elke bucket opslag-klasse wordt gebruikt.
- Klik op de widget om de distributie van de grootte van de S3-rapport te openen. Het rapport kunt u de grootte van uw S3 door bucket, op het hoogste niveau map, opslagklasse en versieregistratietstatus kunt analyseren.

### <a name="number-of-objects-by-bucket"></a>Aantal objecten per Bucket
Deze widget ziet u het aantal objecten per bucket in het werkelijke aantal en percentage. Beweeg de muisaanwijzer over de bucket om te zien van het totale aantal objecten.
- Klik op de widget om de distributie van de grootte van de S3-rapport (Scan op basis van) te openen.

## <a name="cloud-comparison-dashboard"></a>Dashboard van cloud-vergelijking
Het dashboard van de vergelijking van de Cloud helpt bij het vergelijken van de kosten van verschillende cloudproviders op basis van de prijzen, het type van de CPU en RAM-geheugen.  
![Vergelijking van de dashboard met diverse rapporten in de cloud](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Kosten bij Azure door exemplaartype
Deze widget markeert de afgelopen 30 dagen gebruikt is in on-demand-tarieven. De kosten met de huidige Amazon EC2-kosten versus de mogelijke in Azure kosten worden vergeleken.
- Beweeg de muisaanwijzer over de balken kosten per Instantietype vergelijken.
- Klik op de widget als u wilt het overdragen van de implementatie – kostenanalyserapport openen.

### <a name="ec2-cost-in-azure"></a>EC2-Kosten in Azure
Deze widget geeft uw huidige kosten van Amazon EC2 en vergelijkt ze met Azure. De vergelijking is gebaseerd op de afgelopen 30 dagen gebruikt is in on-demand-tarieven.
- Klik op de widget als u wilt het overdragen van de implementatie - kostenanalyserapport openen.

### <a name="ec2azure-instance-type-mapping"></a>Toewijzing van het Type EC2/Azure-instantie
Deze widget markeert de beste toewijzing van elastische rekeneenheden tussen Amazon EC2 en Azure.
- Klik op de widget om het rapport exemplaren Type toewijzing te openen.

## <a name="next-steps"></a>Volgende stappen
- Lees de [rapporten van Cloudyn gebruik](use-reports.md) artikel voor meer informatie over rapporten.

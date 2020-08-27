---
title: Belangrijke metrische gegevens weergeven met Cloudyn-dashboards in Azure
description: In dit artikel wordt beschreven hoe u belangrijke metrische gegevens kunt weergeven met dashboards in Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 03d5e3959b85a2b0bd9ae30744051895ff026b77
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691221"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Belangrijke kosten voor metrische gegevens weergeven met dashboards

Dashboards in Cloudyn bieden rapportweergaven op hoog niveau. Met dashboards kunt u metrische kosten voor sleutels weergeven in één weergave. Ze bieden ook highlights van zakelijke trends waarmee u belangrijke zakelijke beslissingen kunt nemen.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Dashboards worden ook gebruikt voor het maken van weergaven voor personen met verschillende verantwoordelijkheden in uw organisatie. Dit kan onder andere het volgende omvatten:

- Financiële controller
- Toepassing of projecteigenaar
- DevOps-engineer
- Leidinggevenden

Dashboards bestaan uit widgets, en elke widget is in feite een miniatuur van een rapport. Klik op een widget om het bijbehorende rapport te openen. Wanneer u rapporten aanpast, slaat u ze op in Mijn rapporten. Vervolgens worden de rapporten toegevoegd aan het dashboard.

De dashboardversies verschillen voor gebruikers van Management (MSP), Enterprise en Premium Cloudyn. De verschillen worden bepaald door toegangsniveaus van de entiteit. Zie [Toegangsniveaus voor entiteiten](tutorial-user-access.md#entity-access-levels)voor meer informatie over toegangsniveaus.

De beschikbaarheid van dashboards is afhankelijk van het type account van de cloud service provider dat wordt gebruikt bij het weergeven van dashboards. Het type informatie dat beschikbaar is en verzameld wordt door Cloudyn is van invloed op rapporten in dashboards. Als u bijvoorbeeld geen AWS-account hebt, ziet u niet het S3 tracker-dashboard. En als u toegang tot Cloudyn in Azure Resource Manager niet inschakelt, ziet u geen Azure-specifieke informatie in Optimizer-dashboardwidgets.

U kunt elk van de vooraf gemaakte dashboards gebruiken of u kunt uw eigen dashboard maken met aangepaste rapporten. Zie [Cloudyn-rapporten gebruiken](use-reports.md) als u niet bekend bent met Cloudyn-rapporten.

## <a name="create-a-custom-dashboard"></a>Een aangepast dashboard maken

Als u snel aan de slag wilt gaan met een aangepast dashboard, kunt u een bestaande dupliceren om de eigenschappen ervan te gebruiken. U kunt dit vervolgens aanpassen aan uw behoeften. Klik op **Opslaan als** op het dashboard dat u wilt kopiëren. U kunt alleen aangepaste dashboards dupliceren — u kunt de dashboards die zijn opgenomen in Cloudyn niet dupliceren.

Een aangepast dashboard maken:

1. Klik op de startpagina op **Nieuwe toevoegen +**. De pagina Mijn dashboard wordt weergegeven.  
    ![Mijn dashboardpagina waar u nieuwe rapporten toevoegt](./media/dashboards/my-dashboard.png)
2. Klik op **Nieuw rapport toevoegen**. Het vak Rapport toevoegen wordt weergegeven.
3. Selecteer het rapport dat u wilt toevoegen aan de widget van het dashboard. De widget wordt toegevoegd aan het dashboard.
4. Herhaal de voorgaande stappen totdat het dashboard is voltooid.
5. Als u de naam van het dashboard wilt wijzigen, klikt u op de naam van het dashboard op de start pagina van het dashboard en typt u de nieuwe naam.

## <a name="modify-a-custom-dashboard"></a>Een aangepast dashboard wijzigen

Net als bij het maken van een aangepast dashboard kunt u de dashboards die zijn opgenomen in Cloudyn niet wijzigen. Een aangepast dashboardrapport wijzigen:

1. Zoek het rapport dat u wilt wijzigen in het dashboard en klik op **Bewerken**. Het rapport wordt weergegeven.
2. Breng de gewenste wijzigingen aan in het rapport en klik op **Opslaan**. Het rapport wordt bijgewerkt en uw wijzigingen worden weergegeven.

## <a name="share-a-custom-dashboard"></a>Een aangepast dashboard delen

U kunt een aangepast dashboard delen met anderen naar _Openbaar_ of _Mijn entiteit_. Wanneer u deelt naar openbaar, kunnen alle gebruikers het dashboard bekijken. Alleen gebruikers met toegang tot de huidige entiteit kunnen het dashboard bekijken wanneer u deelt via Mijn entiteit. De stappen voor het delen van een aangepast dashboard met Openbaar en Mijn entiteit zijn vergelijkbaar.

Een aangepast dashboard naar Openbaar delen:

1. Klik in een dashboard op **Dashboardinstellingen**. Het dialoogvenster Dashboardinstellingen wordt weergegeven.  
    ![dashboardinstellingen voor een aangepast dashboard](./media/dashboards/dashboard-options.png)
2. Klik in het vak dashboardinstellingen op het pijlsymbool en klik vervolgens op **Openbaar**. Het dialoogvenster Bevestiging van openbaar dashboard wordt weergegeven.
3. Klik op **Ja**. Het dashboard is nu beschikbaar voor anderen.

## <a name="delete-a-custom-dashboard-report"></a>Een aangepast dashboardrapport verwijderen

U kunt een aangepast rapportonderdeel verwijderen van het dashboard. Als u het rapport uit het dashboard verwijdert, wordt het rapport niet uit de lijst met rapporten verwijderd. In plaats daarvan wordt het rapport verwijderd uit het dashboard.

Als u een dashboardonderdeel wilt verwijderen, klikt u in het dashboardonderdeel op **Verwijderen**. Als u op **Verwijderen** klikt, wordt het dashboardonderdeel onmiddellijk verwijderd.

## <a name="share-a-dashboard-enterprise"></a>Een dashboard delen (Enterprise)

U kunt aangepaste dashboards delen voor alle gebruikers in uw organisatie of met de gebruikers van de huidige entiteit. Het delen van een dashboard kan anderen een snelle weergave van uw KPI geven. Wanneer u een dashboard deelt, wordt het dashboard automatisch gerepliceerd naar al uw Cloudyn-entiteiten/-klanten. Wijzigingen in het gedeelde dashboard worden automatisch bijgewerkt.

Een dashboard delen met alle gebruikers, inclusief subentiteiten:

1. Klik op de start pagina van het dashboard op **Bewerken**.
2. Klik op **Delen** en selecteer vervolgens **Openbaar**.
3. Het bevestigingsvenster voor het globale openbare dashboard wordt weergegeven.
4. Klik op **Ja** om het dashboard in te stellen als een globaal openbaar dashboard.

Een dashboard delen met alle gebruikers van een huidige entiteit:

1. Klik op de startpagina van het dashboard op **Bewerken**.
2. Klik op **Delen** en selecteer vervolgens **Mijn entiteit**.
3. Klik op **Ja** om het dashboard in te stellen als een openbaar dashboard.

## <a name="duplicate-a-custom-dashboard"></a>Een aangepast dashboard dupliceren

Wanneer u een nieuw dashboard maakt, wilt u mogelijk vergelijkbare eigenschappen van een bestaand dashboard gebruiken. U kunt het dashboard dupliceren om een nieuwe te maken.

U kunt alleen aangepaste dashboards dupliceren. U kunt geen standaard dashboards dupliceren.

Een aangepast dashboard dupliceren (klonen):

1. Klik op het dashboard dat u wilt dupliceren op **Opslaan als**. Er wordt een nieuw dashboard met dezelfde naam en een nummer geopend.
2. Wijzig de naam van het gedupliceerde dashboard en wijzig het naar wens.

- Of -

1. Klik in Dashboardinstellingen op **Opslaan als** op de regel van het dashboard dat u wilt dupliceren.
2. Het gedupliceerde dashboard wordt geopend.
3. Wijzig de naam van het dashboard en wijzig het naar wens.

## <a name="set-a-default-dashboard"></a>Een standaard dashboard instellen

U kunt elk dashboard instellen als standaard. Als u dit instelt als standaard, wordt het weergegeven als het meest linkse tabblad in de lijst op het dashboard-tabblad. Het standaard dashboard wordt weergegeven wanneer u het Cloudyn-portal opent.

- Klik op het dashboard-tabblad dat u wilt instellen als standaard en klik vervolgens op **Standaard** aan de rechterkant.

- Of -

1. Klik op **Dashboardinstellingen** om de lijst met beschikbare Dash boards weer te geven en selecteer het dashboard dat u wilt instellen als standaard.  
    ![Dashboardopties voor een standaard dashboard](./media/dashboards/dashboard-options.png)
2. Klik op **Standaard** in de regel van het dashboard. Het bevestigingsvenster voor het standaard dashboard wordt weergegeven.
3. Klik op **Ja**. Het dashboard is ingesteld op standaard.

## <a name="management-dashboard"></a>Management Dashboard
Het beheerdashboard (of MSP-dashboard voor MSP-gebruikers) bevat highlights van de belangrijkste rapporttypen.  
![Beheerdashboard met verschillende rapporten](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Samenvatting van kostenentiteit (alleen Enterprise)
Deze widget geeft een overzicht van de beheerde kostenentiteiten, inclusief het aantal entiteiten en het aantal accounts.
- Klik op de widget om het rapport Bedrijfsgegevens te openen.

### <a name="cost-over-time"></a>Kosten in de loop van de tijd
Deze widget kan u helpen bij het opsporen van kostentrends. Hiermee worden de kosten voor de laatste dag gemarkeerd, op basis van de trend van de afgelopen 30 dagen.
- Klik op de widget om het rapport Werkelijke kosten in de loop van de tijd te openen om meer details weer te geven en te filteren.

### <a name="asset-controller"></a>Asset controller
Met deze widget wordt het aantal actieve instanties van de vorige dag gemarkeerd, boven de gebruikstrend in de afgelopen 30 dagen.
- Klik op de widget om het dashboard voor de Asset controller te openen.

### <a name="unused-ri-detector"></a>Niet-gebruikte RI-detector
Met deze widget wordt het aantal ongebruikte reserveringen van Amazon EC2 gemarkeerd.
- Klik op de widget om het rapport van de Momenteel ongebruikte reserveringen te openen, waarin u de ongebruikte reserveringen kunt bekijken die u kunt wijzigen.

### <a name="cost-by-service"></a>Kosten per service
Deze widget markeert de afgeschreven kosten per service voor de afgelopen 30 dagen. Beweeg de muisaanwijzer over het cirkeldiagram om de kosten per service te bekijken.
- Klik op de widget om het rapport met de werkelijke kostenanalyse te openen.

### <a name="potential-savings"></a>Potentiële besparingen
Deze widget toont de prijsaanbevelingen voor het exemplaartype voor Amazon EC2 en Amazon RDS.
- Klik op de widget Open het rapport Besparingen voor analyse. Dit geeft de kosten weer per exemplaartype met mogelijke besparingen.

### <a name="compute-instances---daily-trend"></a>Exemplaren berekenen - Dagelijkse trend
Met deze widget worden de actieve exemplaren op type weergegeven voor de afgelopen 30 dagen.
- Klik op de widget om het rapport Instanties gedurende een periode te openen, waar u een uitsplitsing kunt zien van alle exemplaren die in de afgelopen 30 dagen zijn uitgevoerd.

### <a name="storage-by-department"></a>Opslag per afdeling
Deze widget geeft de opslagservices weer die door afdelingen worden gebruikt. Beweeg de muisaanwijzer over het cirkeldiagram om uw opslagverbruik per afdeling weer te geven.
- Klik op de widget om het S3 Tracker-dashboard te openen.

## <a name="cost-controller-dashboard"></a>Dashboard Kostencontroller
Het dashboard Kostencontroller toont vooraf ingestelde kostentoewijzings-highlights.  
![Kostencontroller-dashboard met verschillende rapporten](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Kosten in de loop van de tijd
Deze widget kan u helpen bij het opsporen van kostentrends. Hiermee worden de kosten voor de laatste dag gemarkeerd, op basis van de trend van de afgelopen 30 dagen.
- Klik op de widget om het rapport Werkelijke kosten in de loop van de tijd te openen om meer details weer te geven en te filteren.

### <a name="monthly-cost-trends"></a>Trend van de maandelijkse kosten
Deze widget markeert de verwachte afgeschreven uitgaven en uw werkelijke uitgaven sinds het begin van de maand.
- Klik op de widget om het rapport geschatte kosten van de huidige maand te openen. Dit biedt een samenvatting van de kosten voor de maand tot heden.

In dit rapport worden de kosten weergegeven van het begin van de maand, de kosten van de vorige maand en de geschatte kosten van de huidige maand. De geschatte kosten van de huidige maand worden berekend door de bijgewerkte maandelijkse kosten en projectie toe te voegen. De projectie is gebaseerd op de kosten die in de afgelopen 30 dagen zijn bewaakt.

### <a name="12-month-planner"></a>Planner voor 12 maanden
Met deze widget worden de geschatte kosten in de komende 12 maanden en de mogelijke besparingen gemarkeerd.
- Klik op de widget om het rapport Jaarlijks verwachte kosten te openen.

### <a name="cost-by-service"></a>Kosten per service
Deze widget markeert de afgeschreven kosten per service voor de afgelopen 30 dagen.
- Beweeg de muisaanwijzer over het cirkeldiagram om de kosten per service te bekijken.
- Klik op de widget om het rapport met de werkelijke kostenanalyse te openen.

### <a name="cost-by-account"></a>Kosten per account
Deze widget markeert de afgeschreven kosten per account voor de afgelopen 30 dagen.
- Beweeg de muisaanwijzer over het cirkeldiagram om de kosten per account te bekijken.
- Klik op de widget om het rapport met de werkelijke kostenanalyse te openen.

### <a name="cost-trend-by-day"></a>Kostentrend per dag
Deze widget markeert de uitgaven gedurende de afgelopen 30 dagen.
- Beweeg de muisaanwijzer over het staafdiagram om de kosten per dag te bekijken.
- Klik op de widget om het rapport Werkelijke kosten in de loop van de tijd te openen.

### <a name="cost-trend-by-month---last-6-months"></a>Kostentrend per maand - laatste 6 maanden

Deze widget markeert de uitgaven in de afgelopen zes maanden.
- Beweeg de muisaanwijzer over het staafdiagram om de kosten per maand te bekijken.
- Klik op de widget om het rapport Werkelijke kosten in de loop van de tijd te openen.

## <a name="asset-controller-dashboard"></a>Dashboard Asset controller

Dit dashboard geeft het aantal actieve exemplaren, beschikbare en in gebruik zijnde schijven, de distributie van exemplaartypen en opslaginformatie weer.  
![Dashboard van de Asset controller met verschillende rapporten](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Rekeninstanties
Met deze widget wordt het aantal actieve exemplaren weergegeven op basis van de gebruikstrend in de afgelopen 30 dagen.
- Klik op de widget om het rapport Exemplaren in de loop van de tijd te openen.

### <a name="disks"></a>Disks
Deze widget markeert het totale aantal en de ruimte van de schijven die in gebruik zijn en beschikbaar zijn.
- Klik op de widget om het rapport Actieve schijven te openen.

### <a name="instance-type-distribution"></a>Distributie van exemplaartype
Met deze widget worden de exemplaartypen in een cirkeldiagram gemarkeerd.
- Klik op de widget om het rapport Exemplaardistributie te openen, waarmee u de geselecteerde aggregatie van uw actieve instanties kunt opsplitsen.

### <a name="compute-instances---daily-trend"></a>Exemplaren berekenen - Dagelijkse trend
Deze widget markeert de rekenexemplaren (spot, gereserveerd en on-demand) per dag voor de afgelopen 30 dagen.
- Beweeg de muisaanwijzer over de grafiek om het aantal rekenexemplaren per type per dag weer te geven.
- Klik op de widget om het rapport Exemplaren in de loop van de tijd te openen.

### <a name="all-buckets-s3"></a>Alle buckets (S3)
Deze widget markeert de totale S3-opslag en het aantal opgeslagen objecten.
- Klik op de widget om het S3 Tracker-dashboard te openen. Het dashboard helpt u bij het zoeken, analyseren en weergeven van uw huidige opslaggebruik en trends.

### <a name="sql-db-instances-rds"></a>SQL DB-exemplaren (RDS)
Deze widget markeert het aantal uitgevoerde Amazon RDS-exemplaren op basis van de trend van de afgelopen 30 dagen.
- Klik op de widget om het rapport RDS-exemplaren in de loop van de tijd te openen.

## <a name="optimizer-dashboard"></a>Optimizer-dashboard
In dit dashboard worden aanbevelingen voor reductie, ongebruikte resources en mogelijke besparingen weergegeven.  
![Optimizer-dashboard met verschillende rapporten](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI Calculator
Deze widget toont het aantal aanbevelingen voor het kopen van RI en markeert de mogelijke jaarlijkse besparing.
- Klik op de widget om de RI Calculator te openen, waar u kunt bepalen wanneer u op aanvraag versus gereserveerde prijsplannen wilt gebruiken.

### <a name="sizing"></a>Grootte aanpassen
Deze widget markeert de aanbevolen grootte en mogelijke besparingen, indien geïmplementeerd.
- Klik op de widget om het rapport EC2 Kosteneffectieve grootte-aanbevelingen te openen.

### <a name="unused-ri-detector"></a>Niet-gebruikte RI-detector
Met deze widget wordt het aantal ongebruikte reserveringen van Amazon EC2 gemarkeerd.
- Klik op de widget om het rapport Momenteel ongebruikte reserveringen te openen, waarin u de ongebruikte reserveringen kunt bekijken die u kunt wijzigen.

###  <a name="available-disks"></a>Beschikbare schijven
Deze widget markeert het aantal niet-gekoppelde schijven in uw implementatie.
- Klik op de widget om het rapport Niet-gekoppelde schijven te openen.

### <a name="rds-ri-calculator"></a>RDS RI Calculator
Deze widget markeert het aantal aanbevelingen voor de reservering voor uw Amazon RDS-instanties en de mogelijke besparing.
- Klik op de widget om het rapport Aanbevelingen voor RDS RI-aankopen te openen, waar u Cloudyn-aanbevelingen kunt zien om gereserveerde instanties te gebruiken in plaats van exemplaren op aanvraag.

### <a name="rds-sizing"></a>RDS-grootte aanpassen
Deze widget toont het aantal aanbevelingen met betrekking tot de grootte en de mogelijke besparingen.
- Klik op de widget om het rapport RDS-grootte-aanbevelingen te openen, waarin gedetailleerde informatie over de aanbevelingen voor Amazon RDS-grootte wordt weergegeven.

De aanbevelingen voor optimalisatie zijn gebaseerd op de gebruiks- en prestatiegegevens van de afgelopen 30 dagen.

## <a name="s3-tracker-dashboard"></a>S3 Tracker-dashboard
Het S3 Tracker-dashboard helpt u bij het zoeken, analyseren en weergeven van uw huidige opslaggebruik en trends.  
![S3 Tracker-dashboard met verschillende rapporten](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Alle buckets
Deze widget markeert de totale grootte van alle buckets, in GB, en het totale aantal objecten in uw buckets.
- Klik op de widget om het rapport Distributie van het S3-formaat te openen. Het rapport helpt u bij het analyseren van de S3-grootte per bucket, map op het hoogste niveau, opslagklasse en versiebeheerstatus.

### <a name="bucket-properties"></a>Bucket-eigenschappen
Deze widget markeert het totale aantal opslagverzamelingen.
- Klik op de widget om het rapport Eigenschappen van de S3-Bucket weer te geven.

### <a name="scan-status"></a>Scanstatus
Deze widget markeert wanneer de laatste S3-scan is uitgevoerd en wanneer de volgende wordt gestart.
- Klik op de widget om het rapport S3-Scanstatus weer te geven.

### <a name="storage-by-bucket"></a>Opslag per Bucket
Deze widget markeert het percentage dat elke bucket-opslagklasse gebruikt.
- Klik op de widget om het rapport Distributie van het S3-formaat te openen. Het rapport helpt u bij het analyseren van de S3-grootte per bucket, map op het hoogste niveau, opslagklasse en versiebeheerstatus.

### <a name="number-of-objects-by-bucket"></a>Aantal objecten per bucket
Deze widget markeert het aantal objecten per bucket in het werkelijke aantal en percentage. Beweeg de muisaanwijzer over de bucket om de totale objecten te zien.
- Klik op de widget om het rapport Distributie van het S3-formaat te openen (op scan-basis).

## <a name="cloud-comparison-dashboard"></a>Dashboard voor cloudvergelijking
Het dashboard voor cloudvergelijking helpt u de kosten van verschillende cloudproviders te vergelijken op basis van de prijzen, het CPU-type en de RAM-grootte.  
![Dashboard voor Cloudvergelijking waarin verschillende rapporten worden weergegeven](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>Kosten voor EC2 in Azure per exemplaartype
Deze widget markeert de laatste 30 dagen van gebruik in tarieven op aanvraag. De kosten worden vergeleken met de huidige EC2-kosten van Amazon, vergeleken met de potentiële kosten in Azure.
- Beweeg de muisaanwijzer over de balken om kosten per exemplaar te vergelijken.
- Klik op de widget om het rapport Porting van uw implementatie - Kostenanalyse te openen.

### <a name="ec2-cost-in-azure"></a>Kosten voor EC2 in Azure
Deze widget toont uw huidige EC2-kosten voor Amazon en vergelijkt deze met Azure. De vergelijking is gebaseerd op de laatste 30 dagen van gebruik in tarieven op aanvraag.
- Klik op de widget om het rapport Porting van uw implementatie - Kostenanalyse te openen.

### <a name="ec2azure-instance-type-mapping"></a>Toewijzing van EC2/Azure-exemplaartype
Deze widget markeert de beste toewijzing van elastische rekeneenheden tussen Amazon EC2 en Azure.
- Klik op de widget om het rapport Typetoewijzing van exemplaren te openen.

## <a name="next-steps"></a>Volgende stappen
- Lees het artikel [Cloudyn-rapporten gebruiken](use-reports.md) voor meer informatie over rapporten.

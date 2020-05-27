---
title: Vraag quota toe en ontvang ondersteuning
description: Een ondersteunings aanvraag maken in de Azure Portal voor Azure Synapse Analytics. Aanvraag quotum verhoogt of krijgt ondersteuning bij het oplossen van problemen.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0788f978fd25356b230a7923def6cbbea3dc305
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835458"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>Vraag quota toe en ontvang ondersteuning voor Azure Synapse Analytics

In dit artikel wordt beschreven hoe u een ondersteunings ticket kunt verzenden in de Azure Portal voor Azure Synapse Analytics. Met dit proces kunt u een quotum verhoging aanvragen of een aanvraag voor technische ondersteuning indienen voor het technische ondersteunings team.

## <a name="create-a-support-ticket"></a>Een ondersteuningsticket maken

Gebruik de volgende stappen om een nieuwe ondersteunings aanvraag te maken op basis van de Azure Portal voor Azure Synapse Analytics.

1. Selecteer **Help en ondersteuning**in het menu [Azure Portal](https://portal.azure.com) .

   ![De koppeling Help en ondersteuning](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. Selecteer **nieuwe ondersteunings aanvraag**in **Help en ondersteuning**.

    ![Een nieuwe ondersteunings aanvraag maken](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Controleer uw [ondersteunings abonnement voor Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/).

   * Ondersteuning voor het **beheren van facturering, quota en abonnementen** is op alle ondersteuningsniveaus beschikbaar.
   * Ondersteuning voor **het oplossen** van problemen wordt geboden via [Developer](https://azure.microsoft.com/support/plans/developer/), [Standard](https://azure.microsoft.com/support/plans/standard/), [Professional direct](https://azure.microsoft.com/support/plans/prodirect/)of [premier](https://azure.microsoft.com/support/plans/premier/) support. Het gaat hierbij om problemen die klanten ondervinden tijdens het gebruik van Azure waarbij er een redelijke verwachting is dat het probleem is veroorzaakt door Microsoft.
   * **Begeleiding van ontwikkelaars** en **adviesdiensten** zijn beschikbaar op de ondersteuningsniveaus [Professional Direct](https://azure.microsoft.com/support/plans/prodirect/) en [Premier](https://azure.microsoft.com/support/plans/premier/).

   Als u een premier-ondersteunings abonnement hebt, kunt u ook problemen met Azure Synapse Analytics melden op de [micro soft Premier Online-Portal](https://premier.microsoft.com/). Raadpleeg [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) voor meer informatie over de verschillende ondersteunings plannen, waaronder bereik, reactie tijden, prijzen enzovoort.  Zie Veelgestelde vragen over [ondersteuning](https://azure.microsoft.com/support/faq/)van Azure voor meer informatie over ondersteuning voor Azure.

1. Selecteer bij **probleem type**het juiste probleem type. Selecteer **Technical**voor problemen oplossen. Voor quota verhogings aanvragen selecteert u **service-en abonnements limieten (quota's)**.

   ![Selecteer een probleem type](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > In deze rest van dit artikel focusses de aanvragen voor quotum verhoging. Maar u kunt hier ook **technisch** selecteren voor ondersteunings aanvragen voor probleem oplossing. Als u **technisch**selecteert, wordt u gevraagd een samen vatting op te geven en vervolgens een probleem type te identificeren door **probleem type selecteren**te selecteren. Mogelijk ziet u oplossingen om het probleem op te lossen. Als het probleem niet wordt opgelost met de oplossingen die worden weer gegeven, selecteert u **volgende: Details** en vult u het formulier in om het ondersteunings ticket in te dienen.

1. Voor quota verhogings aanvragen selecteert u **Azure Synapse Analytics** voor het **quota type**. Selecteer vervolgens **volgende: oplossingen >>**.

   ![Een quotum type selecteren](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. Selecteer in het **detail** venster **Details opgeven** om aanvullende informatie in te voeren.

   ![De koppeling Details opgeven](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>Typen quotum aanvragen

Als u op **Details geven** klikt, wordt het venster **quotum Details** weer gegeven waarin u aanvullende informatie kunt toevoegen. In de volgende secties worden de verschillende quota aanvragen beschreven die beschikbaar zijn voor Azure Synapse Analytics.

### <a name="data-warehouse-units-dwus-per-server"></a>Data Warehouse-eenheden (Dwu's) per server

Gebruik de volgende stappen om een verhoging van de Dwu's per server aan te vragen.

1. Selecteer het quotum type **Data Warehouse (dtu's) per server** .

1. Selecteer in de lijst **resource** de resource die u wilt instellen.

1. Voer in het veld **aanvraag quotum** de nieuwe DWU-limiet in die u aanvraagt.

   ![Details van DWU-quotum](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>Servers per abonnement

Gebruik de volgende stappen om een verhoging van het aantal servers per abonnement aan te vragen.

1. Selecteer het quotum type voor **servers per abonnement** .

1. Selecteer in de lijst **locatie** de Azure-regio die u wilt gebruiken. Het quotum is per abonnement in elke regio.

1. In het veld **nieuw quotum** voert u uw aanvraag in voor het maximum aantal servers in die regio.

   ![Details van servers-quota](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>Abonnements toegang tot een regio inschakelen

Sommige aanbiedings typen zijn niet beschikbaar in elke regio. Er wordt mogelijk een fout bericht weer geven, zoals het volgende:

`This location is not available for subscription`

Als uw abonnement toegang moet hebben tot een bepaalde regio, gebruikt u de optie **andere quotum aanvraag** om toegang aan te vragen. Geef in uw aanvraag de aanbieding-en SKU-Details op die u wilt inschakelen voor de regio. Zie [prijzen voor Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/synapse-analytics/)voor meer informatie over de aanbiedings-en SKU-opties.

![Andere quotum Details](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>De aanvraag verzenden

De laatste stap is het invullen van de resterende Details van de ondersteunings aanvraag van SQL Database. Selecteer vervolgens**Volgende: Controleren en maken > >** en klik na het controleren van de aanvraagdetails op **Maken** om de aanvraag in te dienen.

## <a name="monitor-a-support-ticket"></a>Een ondersteuningsticket bewaken

Nadat u het ondersteunings verzoek hebt ingediend, neemt het ondersteunings team van Azure contact met u op. Als u de aanvraagstatus en -details wilt controleren, klikt u op het dashboard op **Alle ondersteuningsaanvragen**.

![Status controleren](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>Meer informatie

U kunt ook verbinding maken met de Azure Synapse Analytics-Community op [stack overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) of via de [pagina micro soft Q&een vraag voor Azure SQL Data Warehouse](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html).


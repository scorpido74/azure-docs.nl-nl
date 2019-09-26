---
title: Geplande taken maken met Azure Scheduler - Azure Portal | Microsoft Docs
description: Leer hoe u uw eerste geautomatiseerde taak kunt maken, plannen en uitvoeren met Azure Scheduler in de Azure Portal
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 48e03f41fa0a39642330288fc05ef709179b4c62
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300979"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Uw eerste taak plannen en maken met Azure Scheduler - Azure Portal

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, moet u zo snel mogelijk [naar Azure Logic apps worden gemigreerd](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

In deze zelfstudie leert u hoe u gemakkelijk een taak kunt maken en plannen en die taak vervolgens kunt bewaken en beheren. 

Als u nog geen abonnement op Azure hebt, <a href="https://azure.microsoft.com/free/" target="_blank">registreer u dan nu voor een gratis Azure-account</a>.

## <a name="create-job"></a>Taak maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).  

1. Selecteer **Een resource maken** in het hoofdmenu van Azure. Voer in het zoekvak ‘Planner’ in. Selecteer in de lijst met resultaten **Planner** en kies daarna **Maken**.

   ![Scheduler-resource maken](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Maak nu een taak die een GET-aanvraag naar deze URL verzendt: `https://www.microsoft.com/` 

1. Voer onder **Scheduler-taak** deze informatie in:

   | Eigenschap | Voorbeeldwaarde | Description |
   |----------|---------------|-------------| 
   | **Name** | getMicrosoft | De naam van uw taak | 
   | **Jobverzameling** | <*job-collection-name*> | Maak een taakverzameling of selecteer een bestaande verzameling. | 
   | **Abonnement** | <*Azure-subscription-name*> | De naam van uw Azure-abonnement | 
   |||| 

1. Selecteer **Actie-instellingen - Configureren**, verstrek deze informatie en kies vervolgens **OK** als u klaar bent:

   | Eigenschap | Voorbeeldwaarde | Description |
   |----------|---------------|-------------| 
   | **Actie** | **Http** | Het type actie dat moet worden uitgevoerd | 
   | **Methode** | **Get** | De aan te roepen methode | 
   | **URL** | **https://www.microsoft.com** | De doel-URL | 
   |||| 
   
   ![Taak definiëren](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Selecteer **Schema - Configureren**, definieer het schema en selecteer vervolgens **OK** als u klaar bent:

   U kunt maar een taak per keer maken, maar in dit voorbeeld wordt een terugkerend schema ingesteld.

   | Eigenschap | Voorbeeldwaarde | Description |
   |----------|---------------|-------------| 
   | **Terugkeerpatroon** | **Terugkerend** | Voer een eenmalige of terugkerende taak in | 
   | **Beginnen op** | <*datum van vandaag*> | De begindatum van de taak | 
   | **Herhaald elke** | **1 uur** | Het interval en de frequentie voor het terugkeerpatroon | 
   | **Einde** | **Beëindigen op** twee dagen na de huidige datum | De einddatum van de taak | 
   | **UTC-offset** | **UTC +08:00** | Het tijdverschil tussen Coordinated Universal Time (UTC) en de waargenomen tijd op uw locatie | 
   |||| 

   ![Schema definiëren](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Kies **Gereed** als u klaar bent.

   Nadat u uw taak hebt gemaakt, implementeert Azure deze en wordt de taak op het Azure-dashboard weergegeven. 

1. Kies **Vastmaken aan dashboard** als Azure een melding weergeeft dat de implementatie is geslaagd. Kies anders het pictogram **Meldingen** (met de bel) op de Azure-werkbalk en kies vervolgens **Vastmaken aan dashboard**.

## <a name="monitor-and-manage-jobs"></a>Taken controleren en beheren

Kies uw taak op het Azure-dashboard om uw taak te controleren, bewaken en beheren. Onder **Instellingen** staan de gebieden die u kunt controleren en beheren voor uw taak:

![Taakinstellingen](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Selecteer een gebied voor meer informatie over deze gebieden:

* [**Eigenschappen**](#properties)
* [**Actie-instellingen**](#action-settings)
* [**Planning**](#schedule)
* [**Geschiedenis**](#history)
* [**Gebruikers**](#users)

<a name="properties"></a>

### <a name="properties"></a>properties

Selecteer **Eigenschappen** om alleen-lezeneigenschappen te bekijken die de beheermetagegevens voor uw taak beschrijven.

![Taakeigenschappen weergeven](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Actie-instellingen

Selecteer **Actie-instellingen** om de geavanceerde instellingen van uw taak te wijzigen. 

![Actie-instellingen controleren](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Actietype | Description | 
|-------------|-------------| 
| Alle typen | U kunt het **Beleid voor opnieuw proberen** en de instellingen voor **foutacties** wijzigen. | 
| HTTP en HTTPS | U kunt **Methode** wijzigen naar elke toegestane methode. U kunt ook de koptekst en elementaire verificatiegegevens toevoegen, verwijderen of wijzigen. | 
| Opslagwachtrij| U kunt het opslagaccount, de naam van de wachtrij, het SAS-token en de hoofdtekst wijzigen. | 
| Service Bus | U kunt de naamruimte, het onderwerp of wachtrijpad, de verificatie-instellingen, het transporttype, de berichtkenmerken en de hoofdtekst van het bericht wijzigen. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Planning

Als u een schema via de taakwizard instelt, kunt u dat schema wijzigen, zoals de begindatum en -tijd, het terugkeerschema en de einddatum en -tijd voor terugkerende taken.
U kunt ook meer [complexe schema's en geavanceerde terugkeerpatronen bouwen](scheduler-advanced-complexity.md).

Om uw taakschema weer te geven of te wijzigen, selecteert u **Schema**:

![Taakplanning weergeven](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Geschiedenis

Selecteer **Geschiedenis** om metrische gegevens weer te geven voor elke taak uitvoering van een geselecteerde taak. Deze metrische gegevens bieden realtimewaarden over de gezondheid van uw taak, zoals de status, het aantal nieuwe pogingen, het aantal terugkerende acties, de begintijd en de eindtijd.

![Taakgeschiedenis en metrische gegevens weergeven](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Selecteer elke uitvoering onder **Geschiedenis** om de historische gegevens voor elke uitvoering, zoals de volledige respons voor elke uitvoering, weer te geven. 

![Historische gegevens voor een taak weergeven](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Gebruikers

U kunt toegang tot Azure Scheduler voor elke gebruiker op detailniveau beheren door op rollen gebaseerd toegangsbeheer (RBAC) in Azure te gebruiken. Raadpleeg [Toegang beheren met RBAC](../role-based-access-control/role-assignments-portal.md) voor meer informatie over het instellen van toegang op basis van rollen

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [concepten, terminologie en entiteitenhiërarchie](scheduler-concepts-terms.md)
* [Complexe schema's en geavanceerde terugkeerpatronen bouwen](scheduler-advanced-complexity.md)
* Meer informatie over [hoge beschikbaarheid en betrouwbaarheid van Scheduler](scheduler-high-availability-reliability.md)
* Meer informatie over [limieten, quota, standaardwaarden en foutcodes](scheduler-limits-defaults-errors.md)

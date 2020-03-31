---
title: Installatiewaarschuwingen en -meldingen (Azure-portal)
description: Gebruik de Azure-portal om SQL Database-waarschuwingen te maken, die meldingen of automatisering kunnen activeren wanneer aan de door u opgegeven voorwaarden is voldaan.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 67c47b35e84a93d7d9032ad55b425ae2bb6971fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79209499"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Waarschuwingen maken voor Azure SQL Database- en Azure Synapse Analytics-databases met Azure-portal

## <a name="overview"></a>Overzicht

In dit artikel ziet u hoe u waarschuwingen instelt voor afzonderlijke, samengevoegde en gegevensmagazijndatabases in Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL Data Warehouse) met behulp van de Azure-portal. Waarschuwingen kunnen u een e-mail sturen of een webhaak bellen wanneer een statistiek (bijvoorbeeld databasegrootte of CPU-gebruik) de drempelbereikt. In dit artikel vindt u ook aanbevolen procedures voor het instellen van waarschuwingsperioden.

> [!IMPORTANT]
> Deze functie is nog niet beschikbaar in Managed Instance. Als alternatief u SQL Agent gebruiken om e-mailwaarschuwingen te verzenden voor bepaalde statistieken op basis van [dynamische beheerweergaven.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

U een waarschuwing ontvangen op basis van monitoringstatistieken voor of gebeurtenissen op uw Azure-services.

* **Metrische waarden** : de waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven statistiek een drempelwaarde kruist die u in beide richtingen toewijst. Dat wil zeggen, het activeert zowel wanneer de voorwaarde voor het eerst wordt voldaan en daarna wanneer die voorwaarde niet meer wordt voldaan.
* **Gebeurtenissen in het activiteitenlogboek** - Een waarschuwing kan op *elke* gebeurtenis worden geactiveerd, of alleen wanneer een bepaald aantal gebeurtenissen zich voordoen.

U een waarschuwing zo configureren wanneer deze wordt geactiveerd:

* E-mailmeldingen verzenden naar de servicebeheerder en medebeheerders
* Stuur e-mail naar aanvullende e-mails die u opgeeft.
* Een webhook aanroepen

U informatie over waarschuwingsregels configureren en ontvangen met

* [Azure-portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [Powershell](../azure-monitor/platform/alerts-classic-portal.md)
* [command-line interface (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Een waarschuwingsregel maken op een statistiek met de Azure-portal

1. Zoek in de [portal](https://portal.azure.com/)de bron die u wilt controleren en selecteer deze.
2. Selecteer **Waarschuwingen** in de sectie Controle. De tekst en het pictogram kunnen enigszins variëren voor verschillende bronnen.  

   ![Bewaking](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Selecteer de knop **Nieuwe waarschuwingsregel** om de pagina **Regel maken** te openen.
  ![Regel maken](media/sql-database-insights-alerts-portal/create-rule.png)

4. Klik **in** de sectie Voorwaarde op **Toevoegen**.
  ![Voorwaarde definiëren](media/sql-database-insights-alerts-portal/create-rule.png)
5. Selecteer op de pagina **Signaallogica configureren** een signaal.
  ![Selecteer](media/sql-database-insights-alerts-portal/select-signal.png)signaal .
6. Na het selecteren van een signaal, zoals **CPU-percentage,** wordt de pagina **Signaallogica configureren** weergegeven.
  ![Signaallogica configureren](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Configureer op deze pagina dat drempeltype, operator, aggregatietype, drempelwaarde, aggregatiegranulariteit en evaluatiefrequentie. Klik vervolgens op **Gereed**.
8. Selecteer **op**de regel Maken een bestaande **actiegroep** of maak een nieuwe groep. Met een actiegroep u bepalen welke actie moet worden uitgevoerd wanneer er een waarschuwingsvoorwaarde optreedt.
  ![Actiegroep definiëren](media/sql-database-insights-alerts-portal/action-group.png)

9. Definieer een naam voor de regel, geef een optionele beschrijving op, kies een ernstniveau voor de regel, kies of u de regel wilt inschakelen bij het maken van regels en klik vervolgens op **Regelwaarschuwing maken** om de waarschuwing voor de metrische regel te maken.

Binnen 10 minuten is de waarschuwing actief en wordt geactiveerd zoals eerder beschreven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het configureren van webhooks in waarschuwingen](../azure-monitor/platform/alerts-webhooks.md).

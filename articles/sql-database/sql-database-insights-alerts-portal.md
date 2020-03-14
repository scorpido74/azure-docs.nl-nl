---
title: Waarschuwingen en meldingen instellen (Azure Portal)
description: Gebruik de Azure Portal om SQL Database-waarschuwingen te maken, waarmee meldingen of automatisering kan worden geactiveerd wanneer aan de opgegeven voor waarden wordt voldaan.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79209499"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-analytics-databases-using-azure-portal"></a>Waarschuwingen maken voor Azure SQL Database en Azure Synapse Analytics-data bases met behulp van Azure Portal

## <a name="overview"></a>Overzicht

In dit artikel wordt beschreven hoe u met behulp van de Azure Portal waarschuwingen kunt instellen voor afzonderlijke, gepoolde en Data Warehouse-data bases in Azure SQL Database en Azure Synapse Analytics (voorheen Azure SQL Data Warehouse). Waarschuwingen kunnen u een e-mail sturen of een webhook aanroepen wanneer bepaalde metrische gegevens (zoals de grootte van de data base of het CPU-gebruik) de drempel waarde hebben bereikt. Dit artikel bevat ook aanbevolen procedures voor het instellen van waarschuwings perioden.

> [!IMPORTANT]
> Deze functie is nog niet beschikbaar in het beheerde exemplaar. Als alternatief kunt u SQL Agent gebruiken om e-mail waarschuwingen te verzenden voor bepaalde metrische gegevens op basis van [dynamische beheer weergaven](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

U kunt een waarschuwing ontvangen op basis van metrische bewakings gegevens voor of gebeurtenissen op uw Azure-Services.

* **Metrische waarden** : de waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u in een van beide richtingen toewijst. Dat wil zeggen dat het wordt geactiveerd wanneer aan de voor waarde wordt voldaan en daarna wanneer aan deze voor waarde niet meer wordt voldaan.
* **Activiteiten logboek gebeurtenissen** : een waarschuwing kan worden geactiveerd bij *elke* gebeurtenis of, alleen wanneer er een bepaald aantal gebeurtenissen optreedt.

U kunt een waarschuwing configureren om het volgende te doen wanneer deze wordt geactiveerd:

* E-mail meldingen verzenden naar de service beheerder en mede beheerders
* E-mail verzenden naar extra e-mail berichten die u opgeeft.
* Een webhook aanroepen

U kunt informatie over waarschuwings regels configureren en ophalen met behulp van

* [Azure-portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [opdracht regel interface (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Een waarschuwings regel maken op basis van een metriek met de Azure Portal

1. Zoek in de [Portal](https://portal.azure.com/)de resource die u wilt bewaken en selecteer deze.
2. Selecteer **waarschuwingen** in het gedeelte bewaking. De tekst en het pictogram kunnen enigszins verschillen voor verschillende resources.  

   ![Bewaking](media/sql-database-insights-alerts-portal/Alerts.png)
  
3. Selecteer de knop **nieuwe waarschuwings regel** om de pagina **regel maken** te openen.
  regel maken ![](media/sql-database-insights-alerts-portal/create-rule.png)

4. Klik in de sectie **voor waarde** op **toevoegen**.
  voor waarde ![definiëren](media/sql-database-insights-alerts-portal/create-rule.png)
5. Selecteer een signaal op de pagina **signaal logica configureren** .
  ![Selecteer signaal](media/sql-database-insights-alerts-portal/select-signal.png).
6. Na het selecteren van een signaal, zoals **CPU-percentage**, wordt de pagina **signaal logica configureren** weer gegeven.
  signaal logica ![configureren](media/sql-database-insights-alerts-portal/configure-signal-logic.png)
7. Op deze pagina configureert u dat drempel type, operator, aggregatie type, drempel waarde, aggregatie granulatie en frequentie van de evaluatie. Klik vervolgens op **Gereed**.
8. Selecteer op de **regel maken**een bestaande **actie groep** of maak een nieuwe groep. Met een actie groep kunt u de actie definiëren die moet worden uitgevoerd wanneer er een waarschuwings voorwaarde optreedt.
  ![actie groep definiëren](media/sql-database-insights-alerts-portal/action-group.png)

9. Definieer een naam voor de regel, geef een optionele beschrijving op, kies een Ernst niveau voor de regel, kies of u de regel wilt inschakelen bij het maken van de regel en klik vervolgens op **regel waarschuwing maken** om de waarschuwing voor de metrische regel te maken.

De waarschuwing is binnen 10 minuten actief en wordt geactiveerd zoals eerder is beschreven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [configureren van webhooks in waarschuwingen](../azure-monitor/platform/alerts-webhooks.md).

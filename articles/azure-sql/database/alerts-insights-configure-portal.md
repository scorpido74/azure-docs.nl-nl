---
title: Waarschuwingen en meldingen instellen (Azure Portal)
description: Gebruik de Azure Portal om waarschuwingen te maken, waarmee meldingen of automatisering kan worden geactiveerd wanneer aan de opgegeven voor waarden wordt voldaan.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 05/04/2020
ms.openlocfilehash: 0e9b8f7bc4129c07145130f198b738839b0c7b55
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047858"
---
# <a name="create-alerts-for-azure-sql-database-and-azure-synapse-using-azure-portal"></a>Waarschuwingen voor Azure SQL Database en Azure Synapse maken met behulp van Azure Portal
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


## <a name="overview"></a>Overzicht

Dit artikel laat u zien hoe u waarschuwingen instelt voor data bases in Azure SQL Database en Azure Synapse (voorheen Azure SQL Data Warehouse) met behulp van de Azure Portal. Waarschuwingen kunnen u een e-mail sturen of een webhook aanroepen wanneer bepaalde metrische gegevens (zoals de grootte van de data base of het CPU-gebruik) de drempel waarde hebben bereikt. Dit artikel bevat ook aanbevolen procedures voor het instellen van waarschuwings perioden.

> [!NOTE]
> Zie [Create Alerts for Azure SQL Managed instance](../managed-instance/alerts-create.md)(Engelstalig) voor Azure SQL Managed instance-specifieke instructies.

U kunt een waarschuwing ontvangen op basis van metrische bewakings gegevens voor of gebeurtenissen op uw Azure-Services.

* **Metrische waarden** : de waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u in een van beide richtingen toewijst. Dat wil zeggen dat het wordt geactiveerd wanneer aan de voor waarde wordt voldaan en daarna wanneer aan deze voor waarde niet meer wordt voldaan.
* **Activiteiten logboek gebeurtenissen** : een waarschuwing kan worden geactiveerd bij *elke* gebeurtenis of, alleen wanneer er een bepaald aantal gebeurtenissen optreedt.

U kunt een waarschuwing configureren om het volgende te doen wanneer deze wordt geactiveerd:

* E-mail meldingen verzenden naar de service beheerder en mede beheerders
* E-mail verzenden naar extra e-mail berichten die u opgeeft.
* Een webhook aanroepen

U kunt informatie over waarschuwings regels configureren en ophalen met behulp van

* [Azure Portal](../../azure-monitor/platform/alerts-classic-portal.md)
* [PowerShell](../../azure-monitor/platform/alerts-classic-portal.md)
* [opdracht regel interface (CLI)](../../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Een waarschuwings regel maken op basis van een metriek met de Azure Portal

1. Zoek in de [Portal](https://portal.azure.com/)de resource die u wilt bewaken en selecteer deze.
2. Selecteer **waarschuwingen** in het gedeelte bewaking. De tekst en het pictogram kunnen enigszins verschillen voor verschillende resources.  

   ![Bewaking](./media/alerts-insights-configure-portal/Alerts.png)
  
3. Selecteer de knop **nieuwe waarschuwings regel** om de pagina **regel maken** te openen.
  ![Regel maken](./media/alerts-insights-configure-portal/create-rule.png)

4. Klik in de sectie **voor waarde** op **toevoegen**.
  ![Voor waarde definiëren](./media/alerts-insights-configure-portal/create-rule.png)
5. Selecteer een signaal op de pagina **signaal logica configureren** .
  ![Signaal selecteren ](./media/alerts-insights-configure-portal/select-signal.png) .
6. Na het selecteren van een signaal, zoals **CPU-percentage**, wordt de pagina **signaal logica configureren** weer gegeven.
  ![Signaallogica configureren](./media/alerts-insights-configure-portal/configure-signal-logic.png)
7. Op deze pagina configureert u dat drempel type, operator, aggregatie type, drempel waarde, aggregatie granulatie en frequentie van de evaluatie. Klik vervolgens op **gereed**.
8. Selecteer op de **regel maken**een bestaande **actie groep** of maak een nieuwe groep. Met een actie groep kunt u de actie definiëren die moet worden uitgevoerd wanneer er een waarschuwings voorwaarde optreedt.
  ![Actie groep definiëren](./media/alerts-insights-configure-portal/action-group.png)

9. Definieer een naam voor de regel, geef een optionele beschrijving op, kies een Ernst niveau voor de regel, kies of u de regel wilt inschakelen bij het maken van de regel en klik vervolgens op **regel waarschuwing maken** om de waarschuwing voor de metrische regel te maken.

De waarschuwing is binnen 10 minuten actief en wordt geactiveerd zoals eerder is beschreven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [configureren van webhooks in waarschuwingen](../../azure-monitor/platform/alerts-webhooks.md).

---
title: Waarschuwingen en meldingen instellen met behulp van Azure Portal
description: Gebruik de Azure Portal om SQL Database-waarschuwingen te maken, waarmee meldingen of automatisering kan worden geactiveerd wanneer aan de opgegeven voor waarden wordt voldaan.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 11/02/2018
ms.openlocfilehash: d68055bc64ea36bd3ad024123a8d5b7b239858dc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73689744"
---
# <a name="create-alerts-for-azure-sql-database-and-data-warehouse-using-azure-portal"></a>Waarschuwingen voor Azure SQL Database en Data Warehouse maken met behulp van Azure Portal

## <a name="overview"></a>Overzicht
Dit artikel laat u zien hoe u Azure SQL Database-en Data Warehouse-waarschuwingen kunt instellen met behulp van de Azure Portal. Waarschuwingen kunnen u een e-mail sturen of een webhook aanroepen wanneer bepaalde metrische gegevens (zoals de grootte van de data base of het CPU-gebruik) de drempel waarde hebben bereikt. Dit artikel bevat ook aanbevolen procedures voor het instellen van waarschuwings perioden.    

> [!IMPORTANT]
> Deze functie is nog niet beschikbaar in het beheerde exemplaar. Als alternatief kunt u SQL Agent gebruiken om e-mail waarschuwingen te verzenden voor bepaalde metrische gegevens op basis van [dynamische beheer weergaven](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views).

U kunt een waarschuwing ontvangen op basis van metrische bewakings gegevens voor of gebeurtenissen op uw Azure-Services.

* **Metrische waarden** : de waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u in een van beide richtingen toewijst. Dat wil zeggen dat het wordt geactiveerd wanneer aan de voor waarde wordt voldaan en daarna wanneer aan deze voor waarde niet meer wordt voldaan.    
* **Activiteiten logboek gebeurtenissen** : een waarschuwing kan worden geactiveerd bij *elke* gebeurtenis of, alleen wanneer er een bepaald aantal gebeurtenissen optreedt.

U kunt een waarschuwing configureren om het volgende te doen wanneer deze wordt geactiveerd:

* e-mail meldingen verzenden naar de service beheerder en mede beheerders
* E-mail verzenden naar extra e-mail berichten die u opgeeft.
* een webhook aanroepen

U kunt informatie over waarschuwings regels configureren en ophalen met behulp van

* [Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md)
* [PowerShell](../azure-monitor/platform/alerts-classic-portal.md)
* [opdracht regel interface (CLI)](../azure-monitor/platform/alerts-classic-portal.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Een waarschuwings regel maken op basis van een metriek met de Azure Portal
1. Zoek in de [Portal](https://portal.azure.com/)de resource die u wilt bewaken en selecteer deze.
2. Selecteer **waarschuwingen (klassiek)** in het gedeelte bewaking. De tekst en het pictogram kunnen enigszins verschillen voor verschillende resources.  
   
     ![Bewaking](media/sql-database-insights-alerts-portal/AlertsClassicButton.JPG)
  
   - **Alleen SQL DW**: Klik op de **DWU-gebruiks** grafiek. **Klassieke waarschuwingen weer geven** selecteren

3. Selecteer de knop **metrische waarschuwing toevoegen (klassiek)** en vul de velden in.
   
    ![Waarschuwing toevoegen](media/sql-database-insights-alerts-portal/AddDBAlertPageClassic.JPG)
4. Geef uw waarschuwings regel een **naam** en kies een **Beschrijving**die ook in e-mail meldingen wordt weer gegeven.
5. Selecteer de **metrische gegevens** die u wilt bewaken en kies vervolgens een voor **waarde** en **drempel** waarde voor de metriek. Kies ook de tijds **periode** waaraan de metrische regel moet voldoen voordat de waarschuwing wordt geactiveerd. Als u bijvoorbeeld de periode ' PT5M ' gebruikt en uw waarschuwing zoekt naar een CPU van meer dan 80%, wordt de waarschuwing geactiveerd wanneer de **gemiddelde** CPU gedurende vijf minuten meer dan 80% is geweest. Zodra de eerste trigger plaatsvindt, wordt deze opnieuw geactiveerd wanneer de gemiddelde CPU onder 80% meer dan vijf minuten is. De CPU-meting vindt elke 1 minuut plaats. Raadpleeg de onderstaande tabel voor ondersteunde tijd Vensters en het samenvoegings type dat elke waarschuwing gebruikt: niet alle waarschuwingen gebruiken de gemiddelde waarde.   
6. **E-mail eigenaren controleren...** als u wilt dat beheerders en mede beheerders een e-mail ontvangen wanneer de waarschuwing wordt geactiveerd.
7. Als u wilt dat extra e-mails een melding ontvangen wanneer de waarschuwing wordt geactiveerd, voegt u deze toe in het veld **extra beheerder e-mail (s)** . Scheid meerdere e-mail berichten met een punt komma ( *e-mail adres\@contoso. com; email2\@contoso.com* )
8. Plaats een geldige URI in het veld **webhook** als u wilt dat deze wordt aangeroepen wanneer de waarschuwing wordt geactiveerd.
9. Selecteer **OK** wanneer u klaar bent om de waarschuwing te maken.   

Binnen een paar minuten is de waarschuwing actief en worden triggers zoals eerder beschreven.

## <a name="managing-your-alerts"></a>Uw waarschuwingen beheren
Zodra u een waarschuwing hebt gemaakt, kunt u deze selecteren en:

* Een grafiek weer geven met de metrische drempel waarde en de werkelijke waarden van de vorige dag.
* Bewerk of verwijder deze.
* **Schakel deze optie** **in** of uit als u het ontvangen van meldingen voor die waarschuwing tijdelijk wilt stoppen of hervatten.


## <a name="sql-database-alert-values"></a>SQL Database waarschuwings waarden

| Resourcetype | Metrische naam | Beschrijvende naam | Aggregatie type | Venster minimale waarschuwings tijd|
| --- | --- | --- | --- | --- |
| SQL-database | cpu_percent | CPU-percentage | Average | 5 minuten |
| SQL-database | physical_data_read_percent | Gegevens-I/O-percentage | Average | 5 minuten |
| SQL-database | log_write_percent | Logboek-IO-percentage | Average | 5 minuten |
| SQL-database | dtu_consumption_percent | DTU-percentage | Average | 5 minuten |
| SQL-database | opslag | Totale database grootte | Maximum | 30 minuten |
| SQL-database | connection_successful | Geslaagde verbindingen | Totaal | 10 minuten |
| SQL-database | connection_failed | Mislukte verbindingen | Totaal | 10 minuten |
| SQL-database | blocked_by_firewall | Geblokkeerd door de firewall | Totaal | 10 minuten |
| SQL-database | Constateer | Impassen | Totaal | 10 minuten |
| SQL-database | storage_percent | Databaseomvangpercentage | Maximum | 30 minuten |
| SQL-database | xtp_storage_percent | Percentage OLTP-opslag in het geheugen (preview-versie) | Average | 5 minuten |
| SQL-database | workers_percent | Percentage werk nemers | Average | 5 minuten |
| SQL-database | sessions_percent | Percentage sessies | Average | 5 minuten |
| SQL-database | dtu_limit | DTU-limiet | Average | 5 minuten |
| SQL-database | dtu_used | DTU gebruikt | Average | 5 minuten |
||||||
| Elastische pool | cpu_percent | CPU-percentage | Average | 10 minuten |
| Elastische pool | physical_data_read_percent | Gegevens-I/O-percentage | Average | 10 minuten |
| Elastische pool | log_write_percent | Logboek-IO-percentage | Average | 10 minuten |
| Elastische pool | dtu_consumption_percent | DTU-percentage | Average | 10 minuten |
| Elastische pool | storage_percent | Opslag percentage | Average | 10 minuten |
| Elastische pool | workers_percent | Percentage werk nemers | Average | 10 minuten |
| Elastische pool | eDTU_limit | eDTU-limiet | Average | 10 minuten |
| Elastische pool | storage_limit | Opslag limiet | Average | 10 minuten |
| Elastische pool | eDTU_used | eDTU gebruikt | Average | 10 minuten |
| Elastische pool | storage_used | Gebruikte opslag | Average | 10 minuten |
||||||               
| SQL Data Warehouse | cpu_percent | CPU-percentage | Average | 10 minuten |
| SQL Data Warehouse | physical_data_read_percent | Gegevens-I/O-percentage | Average | 10 minuten |
| SQL Data Warehouse | connection_successful | Geslaagde verbindingen | Totaal | 10 minuten |
| SQL Data Warehouse | connection_failed | Mislukte verbindingen | Totaal | 10 minuten |
| SQL Data Warehouse | blocked_by_firewall | Geblokkeerd door de firewall | Totaal | 10 minuten |
| SQL Data Warehouse | service_level_objective | Servicelaag van de data base | Totaal | 10 minuten |
| SQL Data Warehouse | dwu_limit | limiet voor dwu | Maximum | 10 minuten |
| SQL Data Warehouse | dwu_consumption_percent | Percentage DWU | Average | 10 minuten |
| SQL Data Warehouse | dwu_used | DWU gebruikt | Average | 10 minuten |
||||||


## <a name="next-steps"></a>Volgende stappen
* [Bekijk een overzicht van Azure-bewaking](../monitoring-and-diagnostics/monitoring-overview.md) , inclusief de typen gegevens die u kunt verzamelen en controleren.
* Meer informatie over het [configureren van webhooks in waarschuwingen](../azure-monitor/platform/alerts-webhooks.md).
* Bekijk een [overzicht van Diagnostische logboeken](../azure-monitor/platform/resource-logs-overview.md) en verzamel gedetailleerde metrische gegevens over hoge frequentie voor uw service.
* Bekijk een [overzicht van de verzameling metrische gegevens](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) om te controleren of uw service beschikbaar is en reageert.

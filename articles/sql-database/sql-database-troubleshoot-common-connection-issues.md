---
title: Algemene verbindingsfouten oplossen Azure SQL-database
description: Stappen voor het identificeren en oplossen van algemene verbindings fouten voor Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dalechen
ms.author: daleche
ms.reviewer: jrasnik
ms.date: 01/25/2019
ms.openlocfilehash: 6dccfca256239f922c2243e2ebfab5c26835ee98
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566320"
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Verbindings problemen met Azure SQL Database oplossen

Wanneer de verbinding met Azure SQL Database mislukt, ontvangt u [fout berichten](sql-database-develop-error-messages.md). Dit artikel is een gecentraliseerd onderwerp waarmee u Azure SQL Database verbindings problemen kunt oplossen. Hierin worden [de veelvoorkomende oorzaken](#cause) van verbindings problemen beschreven, wordt [een hulp programma voor probleem oplossing](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) aanbevolen waarmee u het probleem kunt verhelpen en stappen voor het oplossen van problemen met [tijdelijke fouten](#troubleshoot-transient-errors) en [permanente of niet-tijdelijke fouten. ](#troubleshoot-persistent-errors). 

Als u de verbindings problemen ondervindt, probeert u de stappen voor probleem oplossing die in dit artikel worden beschreven.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Oorzaak

Verbindings problemen kunnen een van de volgende oorzaken hebben:

* Fout bij het Toep assen van best practices en ontwerp richtlijnen tijdens het ontwerp proces van de toepassing.  Zie [SQL database Development Overview](sql-database-develop-overview.md) om aan de slag te gaan.
* Azure SQL Database opnieuw configureren
* Firewallinstellingen
* Time-out van verbinding
* Onjuiste aanmeldings gegevens
* De maximum limiet is bereikt op enkele Azure SQL Database bronnen

Over het algemeen kunnen verbindings problemen met Azure SQL Database als volgt worden geclassificeerd:

* [Tijdelijke fouten (kort geduurd of tijdelijk)](#troubleshoot-transient-errors)
* [Permanente of niet-tijdelijke fouten (fouten die regel matig worden herhaald)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Probeer de probleem oplosser voor Azure SQL Database verbindings problemen

Als er een specifieke verbindings fout optreedt, kunt u [Dit hulp programma](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)proberen om uw probleem snel te identificeren en op te lossen.

## <a name="troubleshoot-transient-errors"></a>Tijdelijke fouten oplossen

Wanneer een toepassing verbinding maakt met een Azure-SQL database, wordt het volgende fout bericht weer gegeven:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Dit fout bericht is doorgaans tijdelijk (korte duur).
> 
> 

Deze fout treedt op wanneer de data base wordt verplaatst (of opnieuw is geconfigureerd) en de verbinding met de data base verloren is gegaan met de toepassing. Gebeurtenissen voor het opnieuw configureren van de data base vinden plaats vanwege een geplande gebeurtenis (bijvoorbeeld een software-upgrade) of een niet-geplande gebeurtenis (bijvoorbeeld een proces storing of taak verdeling). De meeste opnieuw geconfigureerde gebeurtenissen zijn meestal korte tijd en moeten binnen Maxi maal 60 seconden worden voltooid. Het volt ooien van deze gebeurtenissen kan echter af en toe langer duren, bijvoorbeeld wanneer een grote trans actie een langlopend herstel veroorzaakt.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Stappen voor het oplossen van problemen met de tijdelijke verbinding

1. Controleer het [Microsoft Azure-service dashboard](https://azure.microsoft.com/status) voor eventuele bekende storingen die zijn opgetreden tijdens de periode waarin de fouten zijn gerapporteerd door de toepassing.
2. Toepassingen die verbinding maken met een Cloud service, zoals Azure SQL Database, moeten periodieke herconfiguratie gebeurtenissen verwachten en proberen logica te implementeren om deze fouten af te handelen in plaats van deze te halen als toepassings fouten aan gebruikers. Raadpleeg de sectie [tijdelijke fouten](sql-database-connectivity-issues.md) en de aanbevolen procedures en ontwerp richtlijnen op [SQL database ontwikkelings overzicht](sql-database-develop-overview.md) voor meer informatie en algemene strategieën voor nieuwe pogingen. Bekijk vervolgens code voorbeelden in [verbindings bibliotheken voor SQL database en SQL Server](sql-database-libraries.md) voor specifieke informatie.
3. Wanneer een Data Base de resource limieten nadert, kan dit een probleem met de tijdelijke verbinding zijn. Zie [resource limieten](sql-database-resource-limits-database-server.md#what-happens-when-database-resource-limits-are-reached).
4. Als er verbindings problemen blijven bestaan of als de duur waarvoor uw toepassing zich voordoet de fout groter is dan 60 seconden of als er meerdere exemplaren van de fout in een bepaalde dag worden weer gegeven, moet u een Azure-ondersteunings aanvraag indienen door **ondersteuning** voor Azure in te scha kelen [ ](https://azure.microsoft.com/support/options)Ondersteunings site.

## <a name="troubleshoot-persistent-errors"></a>Problemen met persistente fouten oplossen
Als de toepassing permanent geen verbinding kan maken met Azure SQL Database, duidt dit meestal op een probleem met een van de volgende:

* Firewall configuratie. De Azure SQL database-of firewall aan de client zijde blokkeert verbindingen met Azure SQL Database.
* Herconfiguratie van het netwerk aan de client zijde: bijvoorbeeld een nieuw IP-adres of een proxy server.
* Gebruikers fout: er zijn bijvoorbeeld niet-getypte verbindings parameters, zoals de server naam in de connection string.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Stappen voor het oplossen van problemen met de permanente verbinding
1. Stel [firewall regels](sql-database-configure-firewall-settings.md) in om het client-IP-adres toe te staan. Stel voor tijdelijke test doeleinden een firewall regel in met behulp van 0.0.0.0 als het begin-IP-adres bereik en gebruik 255.255.255.255 als het laatste IP-adres bereik. Hiermee wordt de server op alle IP-adressen geopend. Als uw connectiviteits probleem hiermee wordt opgelost, verwijdert u deze regel en maakt u een firewall regel voor een adequaat beperkt IP-adres of adres bereik. 
2. Controleer op alle firewalls tussen de client en Internet of poort 1433 is geopend voor uitgaande verbindingen. Bekijk [de Windows Firewall configureren om SQL Server toegang](https://msdn.microsoft.com/library/cc646023.aspx) en [hybride identiteit vereiste poorten en protocollen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) toe te staan voor aanvullende verwijzingen die betrekking hebben op extra poorten die u moet openen voor Azure Active Directory verificatie.
3. Controleer uw connection string en andere Verbindings instellingen. Zie de sectie verbindings reeks in het [onderwerp connectiviteits problemen](sql-database-connectivity-issues.md#connections-to-sql-database).
4. Controleer de service status in het dash board. Als u denkt dat er een regionale storing is, raadpleegt u [herstellen van een storing](sql-database-disaster-recovery.md) voor stappen om te herstellen naar een nieuwe regio.

## <a name="next-steps"></a>Volgende stappen
* [Zoek in de documentatie op Microsoft Azure](https://azure.microsoft.com/search/documentation/)
* [De meest recente updates voor de Azure SQL Database-service weer geven](https://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Aanvullende resources
* [Overzicht van SQL Database ontwikkeling](sql-database-develop-overview.md)
* [Algemene richt lijnen voor het afhandelen van tijdelijke fouten](../best-practices-retry-general.md)
* [Verbindings bibliotheken voor SQL Database en SQL Server](sql-database-libraries.md)


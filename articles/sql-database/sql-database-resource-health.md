---
title: Azure Resource Status gebruiken om de databasestatus te controleren
description: Gebruik Azure Resource Health om de SQL-databasestatus te controleren, helpt u bij het diagnosticeren en ondersteuning krijgen wanneer een Azure-probleem gevolgen heeft voor uw SQL-bronnen.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 9e19e904b47d69444b491dd88ffe49ff812aafc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208876"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Resource Health gebruiken om connectiviteitsproblemen voor Azure SQL Database op te lossen

## <a name="overview"></a>Overzicht

[Resourcehealth](../service-health/resource-health-overview.md#get-started) voor SQL Database helpt u bij het diagnosticeren en ondersteuning krijgen wanneer een Azure-probleem gevolgen heeft voor uw SQL-bronnen. Het biedt u informatie over de huidige en eerdere status van uw resources en helpt u problemen verhelpen. Resource Health biedt technische ondersteuning als u hulp nodig heeft bij problemen met Azure-services.

![Overzicht](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Gezondheidscontroles

Resourcehealth bepaalt de status van uw SQL-bron door het succes en het falen van aanmeldingen bij de resource te onderzoeken. Op dit moment onderzoekt Resource Health voor uw SQL DB-bron alleen inlogfouten als gevolg van systeemfouten en niet door gebruikersfouten. De status Resourcestatus wordt elke 1-2 minuten bijgewerkt.

## <a name="health-states"></a>Gezondheidsstaten

### <a name="available"></a>Beschikbaar

Een status van **Beschikbaar** betekent dat Resource Health geen aanmeldingsfouten heeft gedetecteerd als gevolg van systeemfouten op uw SQL-bron.

![Beschikbaar](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Verminderd beschikbaar

Een status van **Gedegradeerd** betekent dat Resource Health een meerderheid van succesvolle aanmeldingen heeft gedetecteerd, maar ook enkele fouten. Dit zijn waarschijnlijk tijdelijke inlogfouten. Als u de impact van verbindingsproblemen als gevolg van tijdelijke inlogfouten wilt verminderen, implementeert u [logica opnieuw proberen](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) in uw code.

![Verminderd beschikbaar](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Niet beschikbaar

Een status van **Niet beschikbaar** betekent dat Resource Health consistente inlogfouten op uw SQL-bron heeft gedetecteerd. Als uw resource gedurende een langere periode in deze status blijft, neemt u contact op met de ondersteuning.

![Niet beschikbaar](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Onbekend

De status van **Onbekend** geeft aan dat Resource Health al meer dan 10 minuten geen informatie over deze bron heeft ontvangen. Hoewel deze status geen definitieve indicatie is van de status van de resource, is het een belangrijk gegevenspunt in het probleemoplossingsproces. Als de resource wordt uitgevoerd zoals verwacht, wordt de status van de resource na enkele minuten gewijzigd in Beschikbaar. Als u problemen ondervindt met de resource, kan de status Onbekende suggereren dat een gebeurtenis in het platform van invloed is op de resource.

![Onbekend](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historische informatie

U hebt toegang tot maximaal 14 dagen gezondheidsgeschiedenis in de sectie Geschiedenis van de gezondheid van Resource Health. De sectie bevat ook de reden voor downtime (indien beschikbaar) voor de downtimes die door Resource Health worden gerapporteerd. Momenteel wordt de downtime voor de SQL database-resource in Azure weergegeven met een granulariteit van twee minuten. De werkelijke downtime is waarschijnlijk minder dan een minuut. Het gemiddelde is 8 sec.

### <a name="downtime-reasons"></a>Downtime redenen

Wanneer uw SQL Database downtime ondervindt, wordt analyse uitgevoerd om een reden te bepalen. Indien beschikbaar wordt de reden voor downtime gerapporteerd in de sectie Statusgeschiedenis van Resource Health. Redenen voor downtime worden doorgaans 30 minuten na een gebeurtenis gepubliceerd.

#### <a name="planned-maintenance"></a>Gepland onderhoud

De Azure-infrastructuur voert periodiek gepland onderhoud uit – upgrade van hardware- of softwarecomponenten in het datacenter. Terwijl de database onderhoud ondergaat, kan SQL sommige bestaande verbindingen beëindigen en nieuwe verbindingen weigeren. De inlogfouten die tijdens gepland onderhoud worden ondervonden, zijn meestal van voorbijgaande aard en [de logica voor opnieuw proberen](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) helpt de impact te verminderen. Als u nog steeds inlogfouten ervaart, u contact opnemen met de ondersteuning.

#### <a name="reconfiguration"></a>Herconfiguratie

Herconfiguraties worden beschouwd als tijdelijke omstandigheden en worden van tijd tot tijd verwacht. Deze gebeurtenissen kunnen worden geactiveerd door taakverdeling of software-/hardwarefouten. Elke clientproductietoepassing die verbinding maakt met een clouddatabase moet een robuuste [logica voor het opnieuw proberen van verbindingen](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)implementeren, omdat dit deze situaties zou helpen beperken en de fouten over het algemeen transparant moet maken voor de eindgebruiker.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logica opnieuw proberen voor tijdelijke fouten](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [SQL-verbindingsfouten opsporen, diagnose vaststellen en problemen oplossen](./sql-database-connectivity-issues.md)
- Meer informatie over [het configureren van waarschuwingen voor resourcestatus](../service-health/resource-health-alert-arm-template-guide.md)
- Een overzicht van [Resource Health](../service-health/resource-health-overview.md)
- [Veelgestelde vragen over Resource Health](../service-health/resource-health-faq.md)

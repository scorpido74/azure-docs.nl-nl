---
title: Azure Resource Health gebruiken om SQL Database status te controleren
description: Gebruik Azure Resource Health om SQL Database status te bewaken, helpt u bij het vaststellen en verkrijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw SQL-resources.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 10ce2da8ae8bcd8a12f0972d437a84a37a8390b9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687502"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Resource Health gebruiken om verbinding met de Azure SQL Database op te lossen

## <a name="overview"></a>Overzicht

[Resource Health](../service-health/resource-health-overview.md#get-started) voor SQL database helpt u bij het vaststellen en verkrijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw SQL-resources. Het biedt u informatie over de huidige en eerdere status van uw resources en helpt u problemen verhelpen. Resource Health biedt technische ondersteuning als u hulp nodig heeft bij problemen met Azure-services.

![Overzicht](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Status controles

Resource Health bepaalt de status van uw SQL-resource door het slagen en mislukken van aanmeldingen bij de resource te controleren. Momenteel onderzoekt Resource Health voor uw SQL DB-resource alleen de aanmeldings fouten vanwege een systeem fout en geen gebruikers fout. De Resource Health status wordt elke 1-2 minuten bijgewerkt.

## <a name="health-states"></a>Statussen

### <a name="available"></a>Beschikbaar

De status **beschikbaar** betekent dat resource Health geen mislukte aanmeldingen heeft gedetecteerd vanwege systeem fouten in uw SQL-resource.

![Beschikbaar](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Verminderd beschikbaar

De status **Gedegradeerd** betekent dat Resource Health een meerderheid aan geslaagde aanmeldingen, maar ook een aantal mislukte aanmeldingen, heeft gedetecteerd. Dit zijn de meest waarschijnlijke tijdelijke aanmeldings fouten. Als u de gevolgen van verbindings problemen die worden veroorzaakt door tijdelijke aanmeldings fouten wilt verminderen, implementeert u de [logica voor opnieuw proberen](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) in uw code.

![Verminderd beschikbaar](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Niet beschikbaar

De status **niet beschikbaar** betekent dat resource Health consistente aanmeldings fouten heeft gedetecteerd in uw SQL-resource. Als uw resource gedurende lange tijd in deze status blijft, neemt u contact op met de ondersteuning.

![Niet beschikbaar](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Onbekend

De status **onbekend** geeft aan dat resource Health meer dan tien minuten geen informatie over deze resource heeft ontvangen. Hoewel deze status geen definitieve indicatie is van de status van de resource, is het een belang rijk gegevens punt in het probleemoplossings proces. Als de resource op de verwachte manier wordt uitgevoerd, wordt de status van de resource na een paar minuten gewijzigd in beschikbaar. Als u problemen ondervindt met de resource, kan de onbekende status Voorst Ellen dat een gebeurtenis in het platform van invloed is op de resource.

![Onbekend](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historische gegevens

U hebt toegang tot de status geschiedenis van Maxi maal 14 dagen in het gedeelte status geschiedenis van Resource Health. De sectie bevat ook de downtime-reden (indien beschikbaar) voor de downtime die door Resource Health worden gerapporteerd. Momenteel wordt de downtime voor de SQL database-resource in Azure weergegeven met een granulariteit van twee minuten. De werkelijke downtime is waarschijnlijk minder dan een minuut. Het gemiddelde is 8 sec.

### <a name="downtime-reasons"></a>Oorzaken van uitval tijd

Wanneer uw SQL Database downtime ondervindt, wordt de analyse uitgevoerd om een reden te bepalen. Wanneer beschikbaar, wordt de downtime-reden gerapporteerd in het gedeelte status geschiedenis van Resource Health. Redenen voor downtime worden doorgaans 30 minuten na een gebeurtenis gepubliceerd.

#### <a name="planned-maintenance"></a>Gepland onderhoud

De Azure-infra structuur voert periodiek gepland onderhoud uit op hardware-of software onderdelen in het Data Center. Terwijl de data base onderhoud ondergaat, kan SQL sommige bestaande verbindingen beëindigen en nieuwe weigeren. De aanmeldings fouten die zijn opgetreden tijdens gepland onderhoud zijn doorgaans tijdelijk en de [logica voor opnieuw proberen](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) vermindert de impact. Neem contact op met de ondersteuning als u aanmeldings fouten blijft ervaren.

#### <a name="reconfiguration"></a>Herconfiguratie

Herconfiguraties worden beschouwd als tijdelijke omstandigheden en worden verwacht van tijd tot tijd. Deze gebeurtenissen kunnen worden geactiveerd door taak verdeling of software/hardwarestoringen. Bij elke client productie toepassing die verbinding maakt met een Cloud database, moet een robuuste logica voor verbindings [poging](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)worden geïmplementeerd, omdat deze de situatie zou kunnen verhelpen en de fouten doorgaans transparant moet maken voor de eind gebruiker.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logica voor opnieuw proberen voor tijdelijke fouten](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [SQL-verbindingsfouten opsporen, diagnose vaststellen en problemen oplossen](./sql-database-connectivity-issues.md)
- Meer informatie over het [configureren van resource Health-waarschuwingen](../service-health/resource-health-alert-arm-template-guide.md)
- Een overzicht van [resource Health](../service-health/resource-health-overview.md) weer geven
- [Veelgestelde vragen over Resource Health](../service-health/resource-health-faq.md)

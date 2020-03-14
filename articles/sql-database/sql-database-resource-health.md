---
title: Azure Resource Health gebruiken om database status te bewaken
description: Azure Resource Health gebruiken voor het bewaken van de status van de SQL-Database, kunt u vaststellen en ondersteuning krijgen wanneer een Azure-probleem gevolgen heeft voor uw SQL-resources.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208876"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Resource Health oplossen met connectiviteit voor Azure SQL Database gebruiken

## <a name="overview"></a>Overzicht

[Resource Health](../service-health/resource-health-overview.md#get-started) voor SQL database helpt u bij het vaststellen en verkrijgen van ondersteuning wanneer een Azure-probleem van invloed is op uw SQL-resources. Het biedt u informatie over de huidige en eerdere status van uw resources en helpt u problemen verhelpen. Resource Health biedt technische ondersteuning als u hulp nodig heeft bij problemen met Azure-services.

![Overzicht](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Statuscontroles

Resource Health bepaalt de status van uw SQL-resource door te controleren geslaagde en mislukte aanmeldingen aan de resource. Op dit moment controleert resourcestatus voor uw SQL-database alleen mislukte aanmeldpogingen bij vanwege een systeemfout is en niet gebruikersfout. De status van de resourcestatus wordt elke 1-2 minuten bijgewerkt.

## <a name="health-states"></a>Statussen

### <a name="available"></a>Beschikbaar

De status **beschikbaar** betekent dat resource Health geen mislukte aanmeldingen heeft gedetecteerd vanwege systeem fouten in uw SQL-resource.

![Beschikbaar](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Verminderd beschikbaar

De status **Gedegradeerd** betekent dat Resource Health een meerderheid aan geslaagde aanmeldingen, maar ook een aantal mislukte aanmeldingen, heeft gedetecteerd. Dit zijn de meest waarschijnlijke tijdelijke aanmeldingsfouten. Als u de gevolgen van verbindings problemen die worden veroorzaakt door tijdelijke aanmeldings fouten wilt verminderen, implementeert u de [logica voor opnieuw proberen](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) in uw code.

![Verminderd beschikbaar](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Niet beschikbaar

De status **niet beschikbaar** betekent dat resource Health consistente aanmeldings fouten heeft gedetecteerd in uw SQL-resource. Als uw resource in deze status voor een lange periode blijft, neem contact op met ondersteuning.

![Niet beschikbaar](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Onbekend

De status **onbekend** geeft aan dat resource Health meer dan tien minuten geen informatie over deze resource heeft ontvangen. Deze status is niet een definitieve indicatie van de status van de resource, is maar het een belangrijk gegevenspunt in het proces voor het oplossen van problemen. Als de resource wordt uitgevoerd zoals verwacht, wordt de status van de resource op beschikbaar wijzigen na een paar minuten. Als u problemen met de resource ondervindt, kan de status onbekend voorstellen dat een gebeurtenis in het platform is die betrekking hebben op de resource.

![Onbekend](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Historische gegevens

U kunt toegang tot maximaal 14 dagen van de geschiedenis van in de sectie van de geschiedenis van status van Resource Health. De sectie bevat ook de reden downtime (indien beschikbaar) voor de downtimes gerapporteerd door Resource Health. Momenteel wordt de downtime voor de SQL database-resource in Azure weergegeven met een granulariteit van twee minuten. De werkelijke downtime is waarschijnlijk minder dan een minuut. Het gemiddelde is 8 sec.

### <a name="downtime-reasons"></a>Oorzaken downtime

Wanneer uw SQL-Database downtime optreedt, uitgevoerd om een reden vast te stellen. Wanneer deze beschikbaar is, wordt de reden downtime gerapporteerd in de sectie Geschiedenis van Resource Health. Redenen voor downtime worden doorgaans 30 minuten na een gebeurtenis gepubliceerd.

#### <a name="planned-maintenance"></a>Gepland onderhoud

De Azure-infrastructuur wordt periodiek uitgevoerd voor gepland onderhoud: upgraden van hardware of software-onderdelen in het datacenter. Terwijl de database onderhoud ondergaat, kan de SQL beëindigen van sommige bestaande verbindingen en de nieuwe weigeren. De aanmeldings fouten die zijn opgetreden tijdens gepland onderhoud zijn doorgaans tijdelijk en de [logica voor opnieuw proberen](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) vermindert de impact. Als u aanmeldingsfouten zich blijft voordoen, neem contact op met ondersteuning.

#### <a name="reconfiguration"></a>Herconfiguratie

Wanneer worden beschouwd als tijdelijke omstandigheden, en van tijd tot tijd worden verwacht. Deze gebeurtenissen kunnen worden geactiveerd door load balancing- of software/hardwarestoringen. Bij elke client productie toepassing die verbinding maakt met een Cloud database, moet een robuuste logica voor verbindings [poging](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)worden geïmplementeerd, omdat deze de situatie zou kunnen verhelpen en de fouten doorgaans transparant moet maken voor de eind gebruiker.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [logica voor opnieuw proberen voor tijdelijke fouten](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [SQL-verbindingsfouten opsporen, diagnose vaststellen en problemen oplossen](./sql-database-connectivity-issues.md)
- Meer informatie over het [configureren van resource Health-waarschuwingen](../service-health/resource-health-alert-arm-template-guide.md)
- Een overzicht van [resource Health](../service-health/resource-health-overview.md) weer geven
- [Veelgestelde vragen over Resource Health](../service-health/resource-health-faq.md)

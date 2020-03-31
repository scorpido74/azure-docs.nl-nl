---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/17/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 7d81799f7fbdb2b41db421daa1a85ec8cde511eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79538829"
---
### <a name="the-data-model-of-the-schema"></a>Het gegevensmodel van het schema

|Veld|Beschrijving|
|----|----|
|**Waarschuwingsnaam**|Naam van de waarschuwingsweergave|
|**Waarschuwingstype**|unieke waarschuwings-id|
|**ConfidenceLevel**|(Optioneel) Het betrouwbaarheidsniveau van deze waarschuwing (Hoog/Laag)|
|**ConfidenceScore**|(Optioneel) Numerieke betrouwbaarheidsindicator van de beveiligingswaarschuwing|
|**Beschrijving**|Beschrijvingstekst voor de waarschuwing|
|**Displayname**|De weergavenaam van de waarschuwing|
|**Eindtijd**|De eindtijd van de waarschuwing (het tijdstip van de laatste gebeurtenis die bijdraagt aan de waarschuwing)|
|**Entiteiten**|Een lijst met entiteiten die verband houden met de waarschuwing. Deze lijst kan een mix bevatten van entiteiten van verschillende|
|**ExtendedLinks**|(Optioneel) Een tas voor alle links met betrekking tot de waarschuwing. Deze zak kan een mix van links bevatten voor verschillende soorten|
|**ExtendedProperties ExtendedProperties**|Een zak met extra velden die relevant zijn voor de waarschuwing|
|**IsIncident IsIncident**|Hiermee bepaalt u of de waarschuwing een incident of een regelmatige waarschuwing is. Een incident is een beveiligingswaarschuwing die meerdere waarschuwingen samenvoegt tot één beveiligingsincident|
|**Verwerkingseindtijd**|UTC-tijdstempel waarin de waarschuwing is gemaakt|
|**ProductComponentNaam**|(Optioneel) De naam van een component in het product dat de waarschuwing heeft gegenereerd.|
|**ProductName**|constant ('Azure Security Center')|
|**ProviderName**|Ongebruikte|
|**Herstelstappen**|Handmatige actie-items die moeten worden genomen om de beveiligingsdreiging te verwerkeren|
|**ResourceId**|Volledige identificatie van de betreffende resource|
|**Ernst**|De alarmernst (Hoog/Gemiddeld/Laag/Informatief)|
|**SourceComputerId**|een unieke GUID voor de getroffen server (als de waarschuwing wordt gegenereerd op de server)|
|**SourceSystem**|Ongebruikte|
|**Starttime**|De begintijd van de waarschuwing (het tijdstip van de eerste gebeurtenis die bijdraagt aan de waarschuwing)|
|**SystemAlertId**|Unieke id van deze beveiligingswaarschuwinginstantie|
|**TenantId**|de id van de bovenliggende Azure Active directory tenant van het abonnement waaronder de gescande bron zich bevindt|
|**TimeGenerated**|UTC-tijdstempel waarop de beoordeling plaatsvond (de scantijd van het Security Center) (identiek aan DiscoveredTimeUTC)|
|**Type**|constant ('SecurityAlert')|
|**Naam van leverancier**|De naam van de leverancier die de waarschuwing heeft verstrekt (bijvoorbeeld 'Microsoft')|
|**VendorOriginalId**|Ongebruikte|
|**WorkspaceResourceGroup**|in het geval dat de waarschuwing wordt gegenereerd op een VM-, server-, virtuelemachineschaalset of app-service-instantie die rapporteert aan een werkruimte, de naam van die werkruimtebrongroep bevat|
|**Werkruimteabonnementid**|in het geval dat de waarschuwing wordt gegenereerd op een VM-, server-, virtuele machineschaalset of app-service-instantie die rapporteert aan een werkruimte, dat abonnement op de werkruimte bevat|
|||

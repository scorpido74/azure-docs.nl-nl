---
title: Azure Backup-ondersteunings matrix voor SQL Server back-up in virtuele machines van Azure
description: Hierin wordt een overzicht gegeven van de ondersteunings instellingen en beperkingen bij het maken van back-ups van SQL Server in azure Vm's met de Azure Backup-service.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: f9594b9157f84a0536ffd4b62f792fd86fb1c243
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84234249"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Ondersteunings matrix voor SQL Server back-up in virtuele machines van Azure

U kunt Azure Backup gebruiken om een back-up te maken van SQL Server-data bases in virtuele Azure-machines die worden gehost op het Microsoft Azure Cloud platform. In dit artikel vindt u een overzicht van de algemene ondersteunings instellingen en-beperkingen voor scenario's en implementaties van SQL Server back-up in azure Vm's.

## <a name="scenario-support"></a>Scenario-ondersteuning

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde implementaties** | SQL Marketplace Azure-VM's en niet-Marketplace-VM's (SQL Server handmatig geïnstalleerd) worden ondersteund.
**Ondersteunde regio’s** | Australië-Zuid-Oost (ASE), Oost-Australië (AE), Australië-centraal (AC), Australië-centraal 2 (AC) <br> Brazilië - zuid (BRS)<br> Canada-centraal (CNC), Canada-oost (CE)<br> Zuid-Azië-oost (zee), Azië-oost (EA) <br> VS-Oost (EUS), VS-Oost 2 (EUS2), VS-West-Centraal (WCUS), VS-West (WUS); VS-West 2 (WUS 2) Noord-Centraal VS (NCUS) centraal VS (CUS) Zuid-Centraal (SCUS) <br> India-centraal (INC), India-Zuid (INS), India-West <br> Japan-Oost (JPE), Japan-West (JPW) <br> Korea-centraal (KRC), Korea-zuid (KRS) <br> Europa-noord (NE), Europa-west <br> UK-zuid (UKS), UK-west (UKW) <br> US Gov-Arizona, US Gov-Virginia, US Gov-Texas, US DoD-centraal, US DoD-oost <br> Duitsland-noord, Duitsland-west-centraal <br> Zwitserland-noord, Zwitserland-west <br> Frankrijk - centraal <br> China-oost, China-oost 2, China-noord, China-noord 2
**Ondersteunde besturingssystemen** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux wordt momenteel niet ondersteund.
**Ondersteunde SQL Server-versies** | SQL Server 2019, SQL Server 2017 zoals beschreven op de [pagina product levenscyclus zoeken](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 en SPS zoals beschreven op de [pagina product levenscyclus zoeken](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2 SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Ondersteunde .NET-versies** | .NET Framework 4.5.2 of later geïnstalleerd op de VM

## <a name="feature-consideration-and-limitations"></a>Overwegingen voor functies en beperkingen

* SQL Server back-up kan worden geconfigureerd in de Azure Portal of **Power shell**. CLI wordt niet ondersteund.
* De oplossing wordt ondersteund op beide soorten [implementaties](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) -Azure Resource Manager vm's en klassieke vm's.
* Voor virtuele machines met SQL Server is Internet verbinding vereist voor toegang tot open bare IP-adressen van Azure.
* SQL Server **-FCI (failover cluster instance)** wordt niet ondersteund.
* Back-up-en herstel bewerkingen voor spiegel databases en database momentopnamen worden niet ondersteund.
* Het gebruik van meerdere back-upoplossingen om een back-up te maken van uw zelfstandige SQL Server exemplaar of de beschikbaarheids groep SQL always kan leiden tot back-upfouten. Dit wordt onthouden.
* Als u een back-up maakt van twee knoop punten van een beschikbaarheids groep met dezelfde of een andere oplossing, kan dit ook leiden tot back-upfouten.
* Azure Backup ondersteunt alleen volledige en alleen-kopiëren volledige back-uptypen voor **alleen-lezen** data bases
* Databases met zeer veel bestanden kunnen niet worden beveiligd. Het maximum aantal ondersteunde bestanden is **~ 1000**.  
* U kunt back-ups maken naar **~ 2000** SQL server data bases in een kluis. U kunt meerdere kluizen maken voor het geval u een groter aantal data bases hebt.
* U kunt in één bezoek back-ups configureren voor Maxi maal **50** data bases. Deze beperking helpt bij het optimaliseren van back-upbelasting.
* Data bases worden ondersteund met een grootte van Maxi maal **2 TB** . voor grootten die groter zijn dan de mogelijke prestatie problemen.
* Om een idee te hebben van het aantal data bases dat per server kan worden beveiligd, kunt u rekening houden met factoren zoals band breedte, VM-grootte, back-upfrequentie, database grootte enzovoort. [Down load](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) de resource Planner om het geschatte aantal data bases te berekenen dat per server kan worden uitgevoerd op basis van de VM-resources en het back-upbeleid.
* Wanneer beschikbaarheids groepen zijn geconfigureerd, worden er back-ups gemaakt van de verschillende knoop punten op basis van een aantal factoren. Hieronder vindt u een overzicht van het back-upgedrag voor een beschikbaarheids groep.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Back-up gedrag met AlwaysOn-beschikbaarheidsgroepen

Het is raadzaam om de back-up op slechts één knoop punt van een beschikbaarheids groep (AG) te configureren. Configureer altijd een back-up in dezelfde regio als het primaire knoop punt. Met andere woorden, u hebt altijd het primaire knoop punt nodig om aanwezig te zijn in de regio waar u de back-up gaat configureren. Als alle knoop punten van de AG zich in dezelfde regio bevinden waar de back-up is geconfigureerd, is er geen bezorgdheid.

#### <a name="for-cross-region-ag"></a>Voor de kruislingse regio AG

* Back-ups worden alleen uitgevoerd op de knoop punten die zich in dezelfde regio bevinden waar de back-up is geconfigureerd, ongeacht de voorkeurs instelling van de back-up. Dit komt doordat cross-regio back-ups niet worden ondersteund. Als u slechts twee knoop punten hebt en het secundaire knoop punt zich in de andere regio bevindt, worden de back-ups nog steeds uitgevoerd vanaf het primaire knoop punt (tenzij uw voor keur voor de back-up ' secundair ' is).
* Als een knoop punt wordt overgeschakeld naar een andere regio dan die waarvoor de back-up is geconfigureerd, mislukken de back-ups van de knoop punten in de regio waarvoor een failover is uitgevoerd.

Afhankelijk van de voor keuren voor back-ups en back-ups (volledig/differentieel/niet volledig), worden back-ups gemaakt van een bepaald knoop punt (primair/secundair).

#### <a name="backup-preference-primary"></a>Back-upvoorkeur: primair

**Back-uptype** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Primair
Alleen-kopiëren is volledig |  Primair

#### <a name="backup-preference-secondary-only"></a>Back-upvoorkeur: alleen secundair

**Back-uptype** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Alleen-kopiëren is volledig |  Secundair

#### <a name="backup-preference-secondary"></a>Back-upvoorkeur: secundair

**Back-uptype** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Alleen-kopiëren is volledig |  Secundair

#### <a name="no-backup-preference"></a>Geen voorkeurs instelling voor back-up

**Back-uptype** | **Knooppunt**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Alleen-kopiëren is volledig |  Secundair

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het maken van een back-up van [een SQL Server-Data Base](backup-azure-sql-database.md) die wordt uitgevoerd op een virtuele machine van Azure.

---
title: Azure Backup-ondersteuningsmatrix voor SQL Server Backup in Azure VM's
description: Biedt een overzicht van ondersteuningsinstellingen en -beperkingen bij het maken van back-ups van SQL Server in Azure VM's met de Azure Backup-service.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409997"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Ondersteuningsmatrix voor SQL Server Backup in Azure VM's

U Azure Backup gebruiken om een back-up te maken van SQL Server-databases in Azure VM's die worden gehost op het Microsoft Azure-cloudplatform. In dit artikel worden de algemene ondersteuningsinstellingen en -beperkingen voor scenario's en implementaties van SQL Server Backup in Azure VM's samengevat.

## <a name="scenario-support"></a>Scenario-ondersteuning

**Ondersteuning** | **Details**
--- | ---
**Ondersteunde implementaties** | SQL Marketplace Azure-VM's en niet-Marketplace-VM's (SQL Server handmatig geïnstalleerd) worden ondersteund.
**Ondersteunde geografische gebieden** | Australië Zuidoost (ASE), Oost-Australië (AE), Australië Centraal (AC), Australië Centraal 2 (AC) <br> Brazilië - zuid (BRS)<br> Canada Central (CNC), Canada East (CE)<br> Zuidoost-Azië (SEA), Oost-Azië (EA) <br> Oost-VS (EUS), East US 2 (EUS2), West Central US (WCUS), West US (WUS); West US 2 (WUS 2) North Central US (NCUS) Central US (CUS) South Central US (SCUS) <br> India Central (INC), India South (INS), India West <br> Japan East (JPE), Japan West (JPW) <br> Korea Centraal (KRC), Korea Zuid (KRS) <br> Noord-Europa (NE), West-Europa <br> UK South (UKS), UK West (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD Central, US DoD East <br> Duitsland Noord, Duitsland West Centraal <br> Zwitserland Noord, Zwitserland West <br> Frankrijk - centraal <br> China East, China East 2, China North, China Noord 2
**Ondersteunde besturingssystemen** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Linux wordt momenteel niet ondersteund.
**Ondersteunde SQL Server-versies** | SQL Server 2019, SQL Server 2017 zoals beschreven op de [pagina Zoekproductlevenscyclus,](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)SQL Server 2016 en SP's zoals beschreven op de [pagina Zoekproductlevenscyclus](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer, Express.
**Ondersteunde .NET-versies** | .NET Framework 4.5.2 of hoger geïnstalleerd op de VM

## <a name="feature-consideration-and-limitations"></a>Overweging van functies en beperkingen

* SQL Server-back-up kan worden geconfigureerd in de Azure-portal of **PowerShell.** We steunen CLI niet.
* De oplossing wordt ondersteund op beide soorten [implementaties](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) - Azure Resource Manager VM's en klassieke VM's.
* VM met SQL Server vereist internetverbinding om toegang te krijgen tot openbare IP-adressen van Azure.
* SQL Server **Failover Cluster Instance (FCI)** wordt niet ondersteund.
* Back-ups maken en bewerkingen herstellen voor spiegeldatabases en databasemomentopnamen worden niet ondersteund.
* Het gebruik van meer dan één back-upoplossingen om een back-up te maken van uw zelfstandige SQL Server-instantie of SQL Always on availability-groep kan leiden tot back-upfouten. zich daarvan onthouden.
* Als u een back-up maakt van twee knooppunten van een beschikbaarheidsgroep afzonderlijk met dezelfde of verschillende oplossingen, kan dit ook leiden tot back-upfouten.
* Azure Backup ondersteunt alleen volledige en copy-only volledige back-uptypen voor **alleen-lezen** databases
* Databases met zeer veel bestanden kunnen niet worden beveiligd. Het maximum aantal bestanden dat wordt ondersteund is **~ 1000**.  
* U een back-up maken van **maximaal ~ 2000** SQL Server-databases in een kluis. U meerdere kluizen maken voor het geval u een groter aantal databases hebt.
* U back-ups voor maximaal **50** databases in één keer configureren. deze beperking helpt bij het optimaliseren van back-upbelastingen.
* We ondersteunen databases tot **2 TB** in grootte; voor maten groter dan dat prestatieproblemen kunnen komen.
* Als u een idee wilt hebben van het aantal databases dat per server kan worden beveiligd, moet u rekening houden met factoren zoals bandbreedte, VM-grootte, back-upfrequentie, databasegrootte, enzovoort. [Download](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) de resourceplanner om het geschatte aantal databases te berekenen dat u per server hebben op basis van de VM-resources en het back-upbeleid.
* Wanneer beschikbaarheidsgroepen zijn geconfigureerd, worden back-ups genomen van de verschillende knooppunten op basis van een aantal factoren. Het back-upgedrag voor een beschikbaarheidsgroep wordt hieronder samengevat.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Back-upgedrag met Beschikbaarheidsgroepen always on

We raden u aan de back-up te configureren op slechts één knooppunt van een beschikbaarheidsgroep (AG). Configureer altijd back-ups in dezelfde regio als het primaire knooppunt. Met andere woorden, u hebt altijd het primaire knooppunt nodig om aanwezig te zijn in de regio waar u de back-up configureert. Als alle knooppunten van de AG zich in hetzelfde gebied bevinden waar de back-up is geconfigureerd, is er geen probleem.

#### <a name="for-cross-region-ag"></a>Voor cross-region AG

* Ongeacht de back-upvoorkeur worden back-ups alleen uitgevoerd vanaf de knooppunten die zich in hetzelfde gebied bevinden waar de back-up is geconfigureerd. Dit komt omdat back-ups in verschillende regio's niet worden ondersteund. Als u slechts twee knooppunten hebt en het secundaire knooppunt zich in de andere regio bevindt, blijven de back-ups vanaf het primaire knooppunt worden uitgevoerd (tenzij uw back-upvoorkeur 'alleen secundair' is).
* Als een knooppunt niet overgaat naar een andere regio dan de regio waar de back-up is geconfigureerd, mislukken back-ups op de knooppunten in het mislukte gebied.

Afhankelijk van de back-upvoorkeur en back-upstypen (volledig/differentieel/log/alleen kopie- en kopievolledig), worden back-ups genomen van een bepaald knooppunt (primair/secundair).

#### <a name="backup-preference-primary"></a>Voorkeur voor back-up: primair

**Type back-up** | **Node**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Primair
Alleen kopiëren volledig |  Primair

#### <a name="backup-preference-secondary-only"></a>Voorkeur voor back-up: alleen secundair

**Type back-up** | **Node**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Alleen kopiëren volledig |  Secundair

#### <a name="backup-preference-secondary"></a>Voorkeur voor back-up: secundair

**Type back-up** | **Node**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Alleen kopiëren volledig |  Secundair

#### <a name="no-backup-preference"></a>Geen voorkeur voor back-up

**Type back-up** | **Node**
--- | ---
Volledig | Primair
Differentiële | Primair
Logboek |  Secundair
Alleen kopiëren volledig |  Secundair

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een back-up van een SQL Server-database](backup-azure-sql-database.md) die wordt uitgevoerd op een Azure-vm.

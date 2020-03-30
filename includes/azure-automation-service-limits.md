---
title: bestand opnemen
description: bestand opnemen
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: d700dfcf5a7b6e9ada2a755335689ffa571e4c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334735"
---
#### <a name="process-automation"></a>Procesautomatisering

| Resource | Limiet |Opmerkingen|
| --- | --- |---|
| Maximaal aantal nieuwe taken die elke 30 seconden per Azure Automation-account kunnen worden ingediend (niet-geplande taken) |100 |Wanneer deze limiet is bereikt, mislukken de volgende aanvragen om een taak te maken. De client ontvangt een foutmelding.|
| Maximaal aantal gelijktijdige lopende taken op hetzelfde tijdsexemplaar per automatiseringsaccount (niet-geplande taken) |200 |Wanneer deze limiet is bereikt, mislukken de volgende aanvragen om een taak te maken. De client ontvangt een foutmelding.|
| Maximale opslaggrootte van taakmetagegevens voor een rolling periode van 30 dagen | 10 GB (ongeveer 4 miljoen banen)|Wanneer deze limiet is bereikt, mislukken de volgende aanvragen om een taak te maken. |
| Maximale limiet voor het aantal vacatures|1 MB|Een enkele stream mag niet groter zijn dan 1 MB.|
| Maximaal aantal modules dat elke 30 seconden per Automatiseringsaccount kan worden geïmporteerd |5 ||
| Maximale grootte van een module |100 MB ||
| Looptijd van de taak, gratis laag |500 minuten per abonnement per kalendermaand ||
| Maximale hoeveelheid schijfruimte toegestaan per sandbox<sup>1</sup> |1 GB |Alleen van toepassing op Azure-zandbakken.|
| Maximale hoeveelheid geheugen gegeven aan een sandbox<sup>1</sup> |400 MB |Alleen van toepassing op Azure-zandbakken.|
| Maximaal aantal netwerksockets toegestaan per sandbox<sup>1</sup> |1000 |Alleen van toepassing op Azure-zandbakken.|
| Maximale runtime toegestaan per runbook<sup>1</sup> |3 uur |Alleen van toepassing op Azure-zandbakken.|
| Maximaal aantal Automatiseringsaccounts in een abonnement |Geen limiet ||
| Maximum aantal hybride werknemersgroepen per automatiseringsaccount|4000||
|Maximaal aantal gelijktijdige taken die kunnen worden uitgevoerd op één hybride runbookworker|50 ||
| Maximale taakparametergrootte runbook   | 512 kilobits||
| Maximale runbook-parameters   | 50|Als u de limiet van 50 parameter bereikt, u een JSON- of XML-tekenreeks doorgeven aan een parameter en deze met het runbook ontleden.|
| Maximale webhook payload grootte |  512 kilobits|
| Maximale dagen dat taakgegevens worden bewaard|30 dagen|
| Maximale grootte van de PowerShell-werkstroomstatus |5 MB| Van toepassing op PowerShell-werkstroomrunboeken wanneer de werkstroom wordt beheerd.|

<sup>1.</sup> Een sandbox is een gedeelde omgeving die door meerdere taken kan worden gebruikt. Taken die dezelfde sandbox gebruiken, zijn gebonden aan de resourcebeperkingen van de sandbox.

#### <a name="change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris

In de volgende tabel worden de bijgehouden itemlimieten per machine voor het bijhouden van wijzigingen weergegeven.

| **Resource** | **Limiet**| **Opmerkingen** |
|---|---|---|
|File|500||
|Register|250||
|Windows-software|250|Bevat geen software-updates.|
|Linux pakketten|1,250||
|Services|250||
|Daemon|250||

#### <a name="update-management"></a>Updatebeheer

In de volgende tabel worden de limieten voor updatebeheer weergegeven.

| **Resource** | **Limiet**| **Opmerkingen** |
|---|---|---|
|Aantal machines per update-implementatie|1000||
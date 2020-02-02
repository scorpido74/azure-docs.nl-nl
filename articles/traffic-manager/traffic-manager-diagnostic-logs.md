---
title: Diagnostische logboek registratie inschakelen in azure Traffic Manager
description: Meer informatie over het inschakelen van diagnostische logboek registratie voor uw Traffic Manager profiel en het openen van de logboek bestanden die als gevolg hiervan worden gemaakt.
services: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: rohink
ms.openlocfilehash: 0ed2ecef86795f62aa3fe5798dcd0d07adbaf9cc
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938678"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Diagnostische logboek registratie inschakelen in azure Traffic Manager

In dit artikel wordt beschreven hoe u diagnostische logboek registratie inschakelt en logboek gegevens opent voor een Traffic Manager profiel.

Diagnostische logboeken van Azure Traffic Manager kunnen inzicht geven in het gedrag van de Traffic Manager-profiel bron. U kunt bijvoorbeeld de logboek gegevens van het profiel gebruiken om te bepalen waarom er een time-out optreedt voor individuele tests op een eind punt.

## <a name="enable-diagnostic-logging"></a>Bijhouden van diagnostische gegevens inschakelen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de opdrachten uitvoeren die volgen in de [Azure Cloud shell](https://shell.azure.com/powershell), of door Power shell uit te voeren vanaf uw computer. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u Power shell vanaf uw computer uitvoert, hebt u de Azure PowerShell module 1.0.0 of hoger nodig. U kunt `Get-Module -ListAvailable Az` uitvoeren om de geïnstalleerde versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u Power shell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om u aan te melden bij Azure.

1. **Het Traffic Manager profiel ophalen:**

    Als u diagnostische logboek registratie wilt inschakelen, hebt u de ID van een Traffic Manager profiel nodig. Haal het Traffic Manager profiel op waarvoor u diagnostische logboek registratie wilt inschakelen met [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). De uitvoer bevat de ID-gegevens van het Traffic Manager profiel.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Diagnostische logboek registratie inschakelen voor het Traffic Manager profiel:**

    Schakel diagnostische logboek registratie in voor het Traffic Manager profiel met de ID die u in de vorige stap hebt verkregen met [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Met de volgende opdracht worden uitgebreide logboeken voor het Traffic Manager profiel opgeslagen in een opgegeven Azure Storage-account. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Controleer de diagnostische instellingen:**

      Controleer de diagnostische instellingen voor het Traffic Manager profiel met behulp van [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). Met de volgende opdracht worden de categorieën weer gegeven die zijn geregistreerd voor een resource.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Zorg ervoor dat alle logboek categorieën die zijn gekoppeld aan de bron van het Traffic Manager-profiel, worden weer gegeven als ingeschakeld. Controleer ook of het opslag account correct is ingesteld.

## <a name="access-log-files"></a>Toegang tot logboek bestanden
1. Meld u aan bij de [Azure Portal](https://portal.azure.com). 
1. Navigeer naar uw Azure Storage-account in de portal.
2. Klik op de pagina **overzicht** van uw Azure Storage-account onder **Services** op **blobs**.
3. Voor **containers**selecteert u **Insights-logs-probehealthstatusevents**en navigeert u omlaag naar het bestand PT1H. json en klikt u op **downloaden** om een kopie van dit logboek bestand te downloaden en op te slaan.

    ![Toegang tot logboek bestanden van uw Traffic Manager profiel vanuit een Blob-opslag](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Traffic Manager-logboek schema

Alle Diagnostische logboeken die beschikbaar zijn via Azure Monitor, delen een gemeen schappelijk schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen te verzenden. Zie [ondersteunde services, schema's en categorieën voor Azure Diagnostic logs](../azure-monitor/platform/tutorial-dashboards.md)(Engelstalig) voor Diagnostische logboeken op het hoogste niveau.

De volgende tabel bevat logboeken die specifiek zijn voor de Azure Traffic Manager-profiel bron.

|||||
|----|----|---|---|
|**Veldnaam**|**Veld type**|**Definitie**|**Voorbeeld**|
|EndpointName|Tekenreeks|De naam van het Traffic Manager-eind punt waarvan de integriteits status wordt vastgelegd.|*myPrimaryEndpoint*|
|Status|Tekenreeks|De integriteits status van het Traffic Manager-eind punt dat is gecontroleerd. De status **kan een of meer** zijn **.**|**Gevouwen**|
|||||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Traffic Manager bewaking](traffic-manager-monitoring.md)


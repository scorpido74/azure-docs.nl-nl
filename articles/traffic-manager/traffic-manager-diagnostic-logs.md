---
title: Diagnostische logboekregistratie inschakelen in Azure Traffic Manager
description: Meer informatie over het inschakelen van diagnostische logboekregistratie voor uw Traffic Manager-profiel en toegang krijgen tot de logboekbestanden die als gevolg daarvan zijn gemaakt.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938678"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Diagnostische logboekregistratie inschakelen in Azure Traffic Manager

In dit artikel wordt beschreven hoe u diagnostische logboekregistratie en toegang tot logboekgegevens inschakelt voor een Traffic Manager-profiel.

Diagnostische logboeken van Azure Traffic Manager kunnen inzicht geven in het gedrag van de profielbron Traffic Manager. U bijvoorbeeld de logboekgegevens van het profiel gebruiken om te bepalen waarom afzonderlijke sondes een time-out hebben opgemaakt ten opzichte van een eindpunt.

## <a name="enable-diagnostic-logging"></a>registratie in het diagnoselogboek inschakelen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell vanaf uw computer uitvoert, hebt u de Azure PowerShell-module, 1.0.0 of hoger, nodig. U kunt `Get-Module -ListAvailable Az` uitvoeren om de geïnstalleerde versie te vinden. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet `Login-AzAccount` u ook uitvoeren om u aan te melden bij Azure.

1. **Haal het profiel Verkeersbeheer op:**

    Als u diagnostische logboekregistratie wilt inschakelen, hebt u de id van een Traffic Manager-profiel nodig. Haal het traffic manager-profiel op waarvoor u diagnostische logboekregistratie wilt inschakelen met [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). De uitvoer bevat de ID-gegevens van het Traffic Manager-profiel.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Diagnostische logboekregistratie inschakelen voor het profiel Traffic Manager:**

    Diagnostische logboekregistratie inschakelen voor het profiel Verkeersbeheer met behulp van de id die in de vorige stap is verkregen met [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). In de volgende opdracht worden verbose-logboeken voor het Traffic Manager-profiel opgeslagen in een opgegeven Azure Storage-account. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Diagnostische instellingen controleren:**

      Controleer diagnostische instellingen voor het profiel Traffic Manager met [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). In de volgende opdracht worden de categorieën weergegeven die zijn aangemeld voor een resource.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Controleer of alle logboekcategorieën die zijn gekoppeld aan de resource van het Traffic Manager-profiel worden weergegeven als ingeschakeld. Controleer ook of het opslagaccount correct is ingesteld.

## <a name="access-log-files"></a>Logboekbestanden openen
1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Navigeer naar uw Azure Storage-account in de portal.
2. Selecteer **Blobs**op de **pagina Overzicht** van uw Azure-opslagaccount onder **Services** .
3. Selecteer voor **Containers** **insights-logs-probehealthstatussevents**en navigeer naar het PT1H.json-bestand en klik op **Downloaden** om een kopie van dit logboekbestand te downloaden en op te slaan.

    ![Logboekbestanden van uw Traffic Manager-profiel openen vanuit een blob-opslag](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Logboekschema verkeersbeheer

Alle diagnostische logboeken die beschikbaar zijn via Azure Monitor delen een algemeen schema op het hoogste niveau, met flexibiliteit voor elke service om unieke eigenschappen voor hun eigen gebeurtenissen uit te zenden. Zie [Programma's voor ondersteunde services, schema's en categorieën voor diagnostische logboeken op](../azure-monitor/platform/tutorial-dashboards.md)het hoogste niveau .

In de volgende tabel worden logboeken bevat die specifiek zijn voor de Azure Traffic Manager-profielbron.

|||||
|----|----|---|---|
|**Veldnaam**|**Veldtype**|**Definitie**|**Voorbeeld**|
|EndpointName|Tekenreeks|De naam van het eindpunt Van de Verkeersbeheerder waarvan de status wordt geregistreerd.|*myPrimaryEndpoint*|
|Status|Tekenreeks|De status van het eindpunt Verkeersmanager dat is onderzocht. De status kan **omhoog** of **omlaag**zijn.|**Omhoog**|
|||||

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Verkeerscontrole Monitoring](traffic-manager-monitoring.md)


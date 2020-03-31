---
title: Logboekanalyse van Windows Virtual Desktop-diagnostische gegevenslogboeken - Azure
description: Logboekanalyse gebruiken met de functie Diagnostische gegevens van Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 355acb081afef8c78cdf971c7a82acdb91ab5593
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127972"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Log Analytics gebruiken voor de diagnostische functie

Windows Virtual Desktop biedt een diagnostische functie waarmee de beheerder problemen kan identificeren via één interface. Deze functie registreert diagnostische gegevens wanneer iemand de functie Windows Virtual Desktop heeft toegewezen. Elk logboek bevat informatie over welke Windows Virtual Desktop-rol betrokken was bij de activiteit, foutberichten die tijdens de sessie worden weergegeven, tenantgegevens en gebruikersinformatie. Met de diagnostische functie worden activiteitslogboeken voor zowel gebruikers- als administratieve acties aangenomen. Elk activiteitenlogboek valt onder drie hoofdcategorieën: 

- Feed-abonnementsactiviteiten: wanneer een gebruiker verbinding probeert te maken met zijn feed via Microsoft Remote Desktop-toepassingen.
- Verbindingsactiviteiten: wanneer een gebruiker verbinding probeert te maken met een bureaublad of RemoteApp via Microsoft Remote Desktop-toepassingen.
- Beheeractiviteiten: wanneer een beheerder beheerbewerkingen uitvoert op het systeem, zoals het maken van hostpools, het toewijzen van gebruikers aan app-groepen en het maken van roltoewijzingen.

Verbindingen die Windows Virtual Desktop niet bereiken, worden niet weergegeven in diagnostische resultaten omdat de diagnostische rolservice zelf deel uitmaakt van Windows Virtual Desktop. Er kunnen problemen optreden met de verbindingsproblemen van Windows Virtual Desktop wanneer de gebruiker problemen ondervindt met de netwerkverbinding.

## <a name="why-you-should-use-log-analytics"></a>Waarom u Log Analytics moet gebruiken

We raden u aan Log Analytics te gebruiken om diagnostische gegevens in de Azure-client te analyseren die verder gaan dan het oplossen van problemen met één gebruiker. Zoals u vm-prestatiemeteritems inzamelen voor Log Analytics, beschikt u over één tool om informatie te verzamelen voor uw implementatie.

## <a name="before-you-get-started"></a>Voordat u aan de slag gaat

Voordat u Log Analytics gebruiken met de diagnostische functie, moet u [een werkruimte maken.](../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace)

Nadat u uw werkruimte hebt gemaakt, volgt u de instructies in [Windows-computers verbinden met Azure Monitor](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key) om de volgende informatie op te vragen: 

- De werkruimte-id
- De primaire sleutel van uw werkruimte

U hebt deze informatie later in het installatieproces nodig.

## <a name="push-diagnostics-data-to-your-workspace"></a>Diagnostische gegevens naar uw werkruimte pushen 

U diagnostische gegevens van uw Windows Virtual Desktop-tenant naar de Logboekanalyse voor uw werkruimte pushen. U deze functie meteen instellen wanneer u uw tenant voor het eerst maakt door uw werkruimte aan uw tenant te koppelen, of u deze later instellen met een bestaande tenant.

Als u uw tenant wilt koppelen aan uw Logboekanalysewerkruimte terwijl u uw nieuwe tenant instelt, voert u de volgende cmdlet uit om u aan te melden bij Windows Virtual Desktop met uw TenantCreator-gebruikersaccount: 

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com 
```

Als u een bestaande tenant wilt koppelen in plaats van een nieuwe tenant, voert u deze cmdlet in plaats daarvan uit: 

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

U moet deze cmdlets uitvoeren voor elke tenant die u wilt koppelen aan Log Analytics. 

>[!NOTE]
>Als u de werkruimte Log Analytics niet wilt koppelen wanneer `New-RdsTenant` u een tenant maakt, voert u de cmdlet in plaats daarvan uit. 

## <a name="cadence-for-sending-diagnostic-events"></a>Cadans voor het verzenden van diagnostische gebeurtenissen

Diagnostische gebeurtenissen worden verzonden naar Log Analytics wanneer deze zijn voltooid.  

## <a name="example-queries"></a>Voorbeelden van query's

In de volgende voorbeeldquery's ziet u hoe de diagnostische functie een rapport genereert voor de meest voorkomende activiteiten in uw systeem:

In dit eerste voorbeeld worden verbindingsactiviteiten weergegeven die zijn geïnitieerd door gebruikers met ondersteunde extern bureaublad-clients:

```powershell
WVDActivityV1_CL 

| where Type_s == "Connection" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```

In dit volgende voorbeeldwordt de beheeractiviteiten van beheerders op tenants weergegeven:

```powershell
WVDActivityV1_CL 

| where Type_s == "Management" 

| join kind=leftouter ( 

    WVDErrorV1_CL 

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g   

| join  kind=leftouter (  

    WVDCheckpointV1_CL 

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g 

    ) on $left.Id_g  == $right.ActivityId_g  

|project-away ActivityId_g, ActivityId_g1 
```
 
## <a name="stop-sending-data-to-log-analytics"></a>Stoppen met het verzenden van gegevens naar Log Analytics 

Als u wilt stoppen met het verzenden van gegevens van een bestaande tenant naar Log Analytics, voert u de volgende cmdlet uit en stelt u lege tekenreeksen in:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String> 
```

U moet deze cmdlet uitvoeren voor elke tenant van wie u wilt stoppen met het verzenden van gegevens. 

## <a name="next-steps"></a>Volgende stappen 

Zie [Problemen identificeren en diagnosticeren](diagnostics-role-service.md#common-error-scenarios)als u veelvoorkomende foutscenario's wilt controleren die de diagnostische functie voor u kan identificeren.

---
title: Inleiding tot het oplossen van problemen met resources
titleSuffix: Azure Network Watcher
description: Op deze pagina vindt u een overzicht van de mogelijkheden voor het oplossen van problemen met de netwerkwatcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 199b4fc762919c2e3988f477c14d09fc23b0136b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840686"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Inleiding tot het oplossen van problemen met resources in Azure Network Watcher

Virtual Network Gateways bieden connectiviteit tussen on-premises bronnen en andere virtuele netwerken binnen Azure. Het bewaken van gateways en hun verbindingen is essentieel om te zorgen dat de communicatie niet wordt vebroken. Network Watcher biedt de mogelijkheid om gateways en verbindingen op te lossen. De mogelijkheid kan worden aangeroepen via de portal, PowerShell, Azure CLI of REST API. Wanneer network watcher wordt aangeroepen, stelt hij een diagnose van de status van de gateway of verbinding en geeft het de juiste resultaten terug. De aanvraag is een langlopende transactie. De resultaten worden geretourneerd zodra de diagnose is voltooid.

![portal][2]

## <a name="results"></a>Resultaten

De voorlopige resultaten terug geven een algemeen beeld van de gezondheid van de bron. Diepere informatie kan worden verstrekt voor bronnen zoals weergegeven in de volgende sectie:

De volgende lijst zijn de waarden die worden geretourneerd met de API voor problemen oplossen:

* **startTime** - Deze waarde is de tijd dat de API-aanroep voor problemen wordt opgelost.
* **endTime** - Deze waarde is het moment waarop de probleemoplossing is beëindigd.
* **code** - Deze waarde is niet-gezond als er een enkele diagnosefout optreedt.
* **resultaten** - Resultaten is een verzameling resultaten die zijn geretourneerd op de verbinding of de virtuele netwerkgateway.
    * **id** - Deze waarde is het type fout.
    * **samenvatting** - Deze waarde is een samenvatting van de fout.
    * **gedetailleerd** - Deze waarde geeft een gedetailleerde beschrijving van de fout.
    * **recommendedActions** - Deze eigenschap is een verzameling aanbevolen acties om te ondernemen.
      * **actionText** - Deze waarde bevat de tekst waarin wordt beschreven welke actie u moet ondernemen.
      * **actionUri** - Deze waarde biedt de URI documentatie over hoe te handelen.
      * **actionUriText** - Deze waarde is een korte beschrijving van de actietekst.

In de volgende tabellen worden de verschillende fouttypen weergegeven (id onder resultaten uit de voorgaande lijst) die beschikbaar zijn en als de fout logboeken maakt.

### <a name="gateway"></a>Gateway

| Fouttype | Reden | Logboek|
|---|---|---|
| NoFault | Wanneer er geen fout wordt gedetecteerd |Ja|
| GatewayNotFound | Kan gateway of gateway niet vinden is niet voorzien |Nee|
| PlannedMaintenance |  Gateway-instantie wordt onderhouden  |Nee|
| UserDrivenUpdate | Deze fout treedt op wanneer een gebruikersupdate wordt uitgevoerd. De update kan een wijziging van het formaat zijn. | Nee |
| VipUnResponsive | Deze fout treedt op wanneer het primaire exemplaar van de gateway niet kan worden bereikt als gevolg van een fout in de statustest. | Nee |
| PlatformInActive | Er is een probleem met het platform. | Nee|
| ServiceNotRunning | De onderliggende service wordt niet uitgevoerd. | Nee|
| NoConnectionsFoundForGateway | Er bestaan geen verbindingen op de gateway. Deze fout is slechts een waarschuwing.| Nee|
| VerbindingenNiet verbonden | Verbindingen zijn niet verbonden. Deze fout is slechts een waarschuwing.| Ja|
| GatewayCPUUsageExceeded | Het huidige gateway CPU-gebruik is > 95%. | Ja |

### <a name="connection"></a>Verbinding

| Fouttype | Reden | Logboek|
|---|---|---|
| NoFault | Wanneer er geen fout wordt gedetecteerd |Ja|
| GatewayNotFound | Kan gateway of gateway niet vinden is niet voorzien |Nee|
| PlannedMaintenance | Gateway-instantie wordt onderhouden  |Nee|
| UserDrivenUpdate | Deze fout treedt op wanneer een gebruikersupdate wordt uitgevoerd. De update kan een wijziging van het formaat zijn.  | Nee |
| VipUnResponsive | Deze fout treedt op wanneer het primaire exemplaar van de gateway niet kan worden bereikt als gevolg van een fout in de statustest. | Nee |
| ConnectionEntityNotFound | Verbindingsconfiguratie ontbreekt | Nee |
| Verbinding is verbroken losgekoppeld | De verbinding is gemarkeerd als 'losgekoppeld' |Nee|
| ConnectionNotConfiguredOnGateway | De onderliggende service heeft de verbinding niet geconfigureerd. | Ja |
| VerbindingGemarkeerdDoor | De onderliggende service is gemarkeerd als stand-by.| Ja|
| Authentication | Vooraf gedeelde sleutelmismatch | Ja|
| PeerReachability | De peer gateway is niet bereikbaar. | Ja|
| IkePolicyMismatch | De peer gateway heeft IKE-beleid dat niet wordt ondersteund door Azure. | Ja|
| WfpParse- fout | Er is een fout opgetreden bij het ontzeggen van het WFP-logboek. |Ja|

## <a name="supported-gateway-types"></a>Ondersteunde gatewaytypen

In de volgende tabel wordt vermeld welke gateways en verbindingen worden ondersteund met het oplossen van problemen met Network Watcher:

|  |  |
|---------|---------|
|**Gatewaytypen**   |         |
|VPN      | Ondersteund        |
|ExpressRoute | Niet ondersteund |
|**VPN-typen** | |
|Route gebaseerd | Ondersteund|
|Beleid gebaseerd | Niet ondersteund|
|**Verbindingstypen**||
|IPsec| Ondersteund|
|VNet2Vnet| Ondersteund|
|ExpressRoute| Niet ondersteund|
|VPNClient| Niet ondersteund|

## <a name="log-files"></a>Logboekbestanden

De logboekbestanden voor het oplossen van problemen met resources worden opgeslagen in een opslagaccount nadat het oplossen van problemen met de bron is voltooid. In de volgende afbeelding wordt de voorbeeldinhoud van een aanroep weergegeven die heeft geleid tot een fout.

![zip-bestand][1]

> [!NOTE]
> In sommige gevallen wordt alleen een subset van de logboekbestanden naar de opslag geschreven.

Raadpleeg aan [de slag met Azure Blob-opslag met .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)voor instructies voor het downloaden van bestanden uit azure-opslagaccounts. Een ander hulpmiddel dat kan worden gebruikt is Storage Explorer. Meer informatie over Storage Explorer vindt u hier via de volgende link: [Storage Explorer](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Het **bestand ConnectionStats.txt** bevat algemene statistieken van de verbinding, inclusief invallen en uitgaande bytes, verbindingsstatus en het tijdstip van de verbinding.

> [!NOTE]
> Als de aanroep naar de API voor het oplossen van problemen in orde wordt weergegeven, is het enige dat in het zip-bestand wordt geretourneerd een **ConnectionStats.txt-bestand.**

De inhoud van dit bestand is vergelijkbaar met het volgende voorbeeld:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt CPUStats.txt

Het **CPUStats.txt-bestand** bevat CPU-gebruik en geheugen dat beschikbaar is op het moment van testen.  De inhoud van dit bestand is vergelijkbaar met het volgende voorbeeld:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

Het **IKEErrors.txt-bestand** bevat alle IKE-fouten die tijdens de controle zijn gevonden.

In het volgende voorbeeld ziet u de inhoud van een IKEErrors.txt-bestand. Uw fouten kunnen verschillen, afhankelijk van het probleem.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Geschrobd-wfpdiag.txt

Het **logbestand Scrubbed-wfpdiag.txt** bevat het wfp-logboek. Dit logboek bevat logboekregistratie van pakketdrop- en IKE/AuthIP-fouten.

In het volgende voorbeeld ziet u de inhoud van het bestand Scrubbed-wfpdiag.txt. In dit voorbeeld was de gedeelde sleutel van een verbinding niet correct, zoals vanaf de derde regel vanaf de onderkant kan worden gezien. Het volgende voorbeeld is slechts een fragment van het hele logboek, omdat het logboek lang kan zijn, afhankelijk van het probleem.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt.som

Het **wfpdiag.txt.sbestand** is een logboek met de verwerkte buffers en gebeurtenissen.

Het volgende voorbeeld is de inhoud van een wfpdiag.txt.sum-bestand.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="next-steps"></a>Volgende stappen

Zie [Communicatieproblemen tussen netwerken diagnosticeren voor](diagnose-communication-problem-between-networks.md)meer informatie over het diagnosticeren van een probleem met een gateway of gatewayverbinding.
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png

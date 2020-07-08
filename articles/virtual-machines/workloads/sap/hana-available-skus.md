---
title: Sku's voor SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Sku's voor SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7a487b105c8edc34d9427de3b8ca6738da1855a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84672099"
---
# <a name="available-skus-for-hli"></a>Beschikbare SKU's voor HLI

SAP HANA op Azure (grote exemplaren) service op basis van alleen revisie 3 stem pels, is beschikbaar in verschillende configuraties in de Azure-regio's van:

- Australië - oost
- Australië - zuidoost
- Japan - oost
- Japan - west

SAP HANA op Azure (grote exemplaren) service op basis van revisie 4 stem pels is beschikbaar in verschillende configuraties in de Azure-regio's van:

- VS - west 2
- VS - oost
- VS - oost 2
- VS - zuid-centraal
- Europa -west
- Europa - noord



[SAP Hana gecertificeerde sku's van Hana-grote instanties](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lijst, zoals:

| SAP-oplossing | Model | Geheugen | Storage | Beschikbaarheid |
| --- | --- | --- | --- | --- |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA op Azure S96<br /> – 2 x Intel® Xeon® processor E7-8890 v4 <br /> 48 CPU-kernen en 96 CPU-threads |  768 GB |  3,0 TB | Beschikbaar |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA op Azure S224<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kernen en 224 CPU-threads |  3,0 TB |  6,3 TB | Beschikbaar |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA op Azure S224m<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kernen en 224 CPU-threads |  6,0 TB |  10,5 TB | Beschikbaar |
| [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA op Azure S384<br /> – 8 x Intel® Xeon® processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  4,0 TB |  16 TB | Beschikbaar |
|[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080)| SAP HANA op Azure S384xm<br /> – 8 x Intel® Xeon® processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  6,0 TB |  18 TB | Beschikbaar (alleen Rev 4) |
| TDIv5 | SAP HANA op Azure S384m<br /> – 8 x Intel® Xeon® processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  12,0 TB |  28 TB | Beschikbaar |
|[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA op Azure S384xm<br /> – 8 x Intel® Xeon® processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  8,0 TB |  22 TB |  Beschikbaar  |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA op Azure S576m<br /> – 12 x Intel® Xeon® processor E7-8890 v4<br /> 288 CPU-kernen en 576 CPU-threads |  12,0 TB |  28 TB | Beschikbaar (alleen Rev 4) |
| TDIv5 | SAP HANA op Azure S576xm<br /> – 12 x Intel® Xeon® processor E7-8890 v4<br /> 288 CPU-kernen en 576 CPU-threads |  18,0 TB |  41 TB | Beschikbaar |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA op Azure S768m<br /> – 16 x Intel® Xeon® processor E7-8890 v4<br /> 384 CPU-kernen en 768 CPU-threads |  16,0 TB |  36 TB | Beschikbaar (alleen Rev 4) |
| TDIv5 | SAP HANA op Azure S768xm<br /> – 16 x Intel® Xeon® processor E7-8890 v4<br /> 384 CPU-kernen en 768 CPU-threads |  24,0 TB |  56 TB | Beschikbaar |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA op Azure S960m<br /> – 20 x Intel® Xeon® processor E7-8890 v4<br /> 480 CPU-kernen en 960 CPU-threads |  20,0 TB |  46 TB | Beschikbaar (alleen Rev 4) |
| [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA op Azure S896m<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kernen en 896 CPU-threads | 24,0 TB | 35,8 TB | Beschikbaar (alleen Rev 4) |

- CPU-kernen = som van niet-Hyper-threaded CPU-kernen van de som van de processors van de server eenheid.
- CPU-threads = som van Compute-threads die worden verschaft door de Hyper-threaded CPU-kernen van de som van de processors van de server eenheid. De meeste eenheden zijn standaard geconfigureerd voor het gebruik van Hyper-Threading-technologie.
- Op basis van de aanbevelingen van de leverancier S768m, S768xm en S960m zijn niet geconfigureerd voor het gebruik van Hyper-Threading voor het uitvoeren van SAP HANA.


Onder SAP HANA TDIv5 maakt SAP klanten-specifieke omvang en klantspecifieke projecten, die kunnen leiden tot server configuraties, die niet worden vermeld als gecertificeerd in:

- [SAP HANA gecertificeerde apparaten](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

In veel gevallen hebben deze klantspecifieke server configuraties meer geheugen dan de server eenheden die met SAP zijn gecertificeerd. Bij het werken met SAP hebben klanten de mogelijkheid om SAP-ondersteuning te verkrijgen en te certificeren voor hun klantspecifieke, geomvangte server configuraties. 

Daarnaast zijn de volgende standaard-Sku's voor instanties, maar nog niet gecertificeerd met SAP, beschikbaar en in de micro soft-prijs lijst voor aankoop:

 | Model | Totaal geheugen | DRAM-geheugen | Geheugen Optane | Storage | Beschikbaarheid |
| --- | --- | --- | --- | --- | --- |
| SAP HANA op Azure S224oo<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kernen en 224 CPU-threads | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Beschikbaar |
| SAP HANA op Azure S224om<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kernen en 224 CPU-threads | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Beschikbaar |
| SAP HANA op Azure S224ooo<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kernen en 224 CPU-threads | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Beschikbaar |
| SAP HANA op Azure S224oom<br /> – 4 x Intel® Xeon® Platinum 8276-processor <br /> 112 CPU-kernen en 224 CPU-threads | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Beschikbaar |
| SAP HANA op Azure S448<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kernen en 448 CPU-threads | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S448m<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kernen en 448 CPU-threads | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S448oo<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kernen en 448 CPU-threads | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S448om<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kernen en 448 CPU-threads | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S448ooo<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kernen en 448 CPU-threads | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S448oom<br /> – 8 x Intel® Xeon® Platinum 8276-processor <br /> 224 CPU-kernen en 448 CPU-threads | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S672<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kernen en 672 CPU-threads | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S672m<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kernen en 672 CPU-threads | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S672oo<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kernen en 672 CPU-threads | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S672om<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kernen en 672 CPU-threads | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S672ooo<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kernen en 672 CPU-threads | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S672oom<br /> – 12 x Intel® Xeon® Platinum 8276-processor <br /> 336 CPU-kernen en 672 CPU-threads | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S896<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kernen en 896 CPU-threads | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S896oo<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kernen en 896 CPU-threads | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S896om<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kernen en 896 CPU-threads | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S896ooo<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kernen en 896 CPU-threads | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Beschikbaar (alleen Rev 4) |
| SAP HANA op Azure S896oom<br /> – 16 x Intel® Xeon® Platinum 8276-processor <br /> 448 CPU-kernen en 896 CPU-threads | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Beschikbaar (alleen Rev 4) |


> [!IMPORTANT]
> De volgende Sku's, hoewel nog steeds worden ondersteund, kunnen niet meer worden aangeschaft: S72, S72m, S144, S144m, S192 en S192m 

De gekozen specifieke configuraties zijn afhankelijk van de werk belasting, de CPU-resources en het gewenste geheugen. Het is mogelijk dat de OLTP-werk belasting de Sku's gebruikt die zijn geoptimaliseerd voor de OLAP-werk belasting. 

De hardware-basis voor de aanbiedingen, met uitzonde ring van eenheden voor klantspecifieke project formaat, zijn SAP HANA TDI-gecertificeerd. Er zijn twee verschillende klassen van hardware die de Sku's delen in:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 en S224m, S224oo, S224om, S224ooo, S224oom worden aangeduid als de klasse ' type I ' van Sku's.
- Alle andere Sku's worden aangeduid als de ' type II-klasse ' van Sku's.
- Als u geïnteresseerd bent in Sku's die nog niet in de SAP-hardware lijst staan, neemt u contact op met uw Microsoft-account-team voor meer informatie. 


Een volledige HANA-stempel van een grote instantie wordt niet exclusief toegewezen aan één klant&#39;en gebruik. Dit feit is ook van toepassing op de rekken van reken-en opslag resources die zijn verbonden via een netwerk infrastructuur die in Azure is geïmplementeerd. De infra structuur van een grote instantie, zoals Azure, implementeert verschillende &quot; tenants &quot; voor klanten die zijn geïsoleerd van elkaar op de volgende drie niveaus:

- **Netwerk**: isolatie via virtuele netwerken binnen de Hana grote instantie stempel.
- **Opslag**: isolatie door virtuele opslag machines met opslag volumes toe te wijzen en opslag volumes tussen tenants te isoleren.
- **Compute**: toegewezen toewijzing van server eenheden aan één Tenant. Geen harde of zachte partitionering van server eenheden. Er kan geen enkele server of host-eenheid worden gedeeld tussen tenants. 

De implementaties van HANA grote instantie-eenheden tussen verschillende tenants zijn niet zichtbaar voor elkaar. HANA grote instantie-eenheden die in verschillende tenants zijn geïmplementeerd, kunnen niet rechtstreeks communiceren met elkaar op het stempel niveau van de grootschalige grote instantie. Alleen HANA grote instantie-eenheden binnen één Tenant kunnen met elkaar communiceren op het niveau van de HANA-grote instantie stempel.

Een geïmplementeerde Tenant in de stempel van een groot exemplaar wordt toegewezen aan één Azure-abonnement voor facturerings doeleinden. Voor een netwerk is het toegankelijk vanuit virtuele netwerken van andere Azure-abonnementen binnen dezelfde Azure-inschrijving. Als u met een ander Azure-abonnement in dezelfde Azure-regio implementeert, kunt u er ook voor kiezen om een gescheiden HANA-Tenant voor grote instanties te vragen.

Er zijn belang rijke verschillen tussen het uitvoeren van SAP HANA op HANA grote instanties en SAP HANA die worden uitgevoerd op Vm's die in azure zijn geïmplementeerd:

- Er is geen virtualisatiehost voor SAP HANA op Azure (grote exemplaren). U krijgt de prestaties van de onderliggende bare-metal hardware.
- In tegens telling tot Azure is de SAP HANA op de Azure-server (grote exemplaren) toegewezen aan een specifieke klant. Het is niet mogelijk dat een server eenheid of-host hard of zacht gepartitioneerd is. Als gevolg hiervan wordt een HANA grote instantie-eenheid gebruikt als een geheel aan een Tenant en aan u. Het opnieuw opstarten of afsluiten van de server leidt niet automatisch naar het besturings systeem en SAP HANA op een andere server worden geïmplementeerd. (Voor Sku's van het type I-klasse is de enige uitzonde ring als een server problemen ondervindt en de herimplementatie moet worden uitgevoerd op een andere server.)
- In tegens telling tot Azure, waarbij typen host-processors worden geselecteerd voor de beste prijs-prestatie verhouding, zijn de processor typen die zijn gekozen voor SAP HANA op Azure (grote instanties) de hoogste uitvoering van de Intel E7v3-en E7v4-processor lijn.

**Volgende stappen**
- Raadpleeg de [HLI-grootte](hana-sizing.md)

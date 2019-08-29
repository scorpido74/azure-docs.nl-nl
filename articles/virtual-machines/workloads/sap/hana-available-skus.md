---
title: Sku's voor SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Sku's voor SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 987f84301608129075b55769f886912ec2354cbb
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099903"
---
# <a name="available-skus-for-hli"></a>Beschikbare SKU's voor HLI

SAP HANA op Azure (grote exemplaren) service op basis van revisie 3 stem pels is beschikbaar in verschillende configuraties in de Azure-regio's van:

- US - west
- East US
- Australië - oost
- Australië - zuidoost
- Europa -west
- Europa - noord
- Japan - oost
- Japan - west

SAP HANA op Azure (grote exemplaren) service op basis van revisie 4 stem pels is beschikbaar in verschillende configuraties in de Azure-regio's van:

- US - west 2
- East US
- Europa -west
- Europa - noord



[SAP Hana gecertificeerde sku's van Hana-grote instanties](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lijst, zoals:

| SAP-oplossing | CPU | Geheugen | Storage | Beschikbaarheid |
| --- | --- | --- | --- | --- |
| Geoptimaliseerd voor OLAP: SAP BW, BW/4HANA<br /> of SAP HANA voor algemene OLAP-werk belasting | SAP HANA op Azure S72<br /> – 2 x Intel® Xeon® processor E7-8890 v3<br /> 36 CPU-kernen en 72 CPU-threads |  768 GB |  3 TB | Niet meer aangeboden |
| --- | SAP HANA op Azure S144<br /> – 4 x Intel® Xeon® processor E7-8890 v3<br /> 72 CPU-kernen en 144 CPU-threads |  1,5 TB |  6 TB | Niet meer aangeboden |
| --- | SAP HANA op Azure S192<br /> – 4 x Intel® Xeon® processor E7-8890 v4<br /> 96 CPU-kernen en 192 CPU-threads |  2,0 TB |  8 TB | Beschikbaar |
| --- | SAP HANA op Azure S384<br /> – 8 x Intel® Xeon® processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  4,0 TB |  16 TB | Beschikbaar |
| Geoptimaliseerd voor OLTP: SAP Business Suite<br /> op SAP HANA of S/4HANA (OLTP),<br /> algemene OLTP | SAP HANA op Azure S72m<br /> – 2 x Intel® Xeon® processor E7-8890 v3<br /> 36 CPU-kernen en 72 CPU-threads |  1,5 TB |  6 TB | Niet meer aangeboden |
|---| SAP HANA op Azure S144m<br /> – 4 x Intel® Xeon® processor E7-8890 v3<br /> 72 CPU-kernen en 144 CPU-threads |  3,0 TB |  12 TB | Niet meer aangeboden |
|---| SAP HANA op Azure S192m<br /> – 4 x Intel® Xeon® processor E7-8890 v4<br /> 96 CPU-kernen en 192 CPU-threads  |  4,0 TB |  16 TB | Beschikbaar |
|---| SAP HANA op Azure S384m<br /> – 8 x Intel® Xeon® processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  6,0 TB |  18 TB | Beschikbaar |
|---| SAP HANA op Azure S384xm<br /> – 8 x Intel® Xeon® processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  8,0 TB |  22 TB |  Beschikbaar |
|---| SAP HANA op Azure S576m<br /> – 12 x Intel® Xeon® processor E7-8890 v4<br /> 288 CPU-kernen en 576 CPU-threads |  12,0 TB |  28 TB | Beschikbaar |
|---| SAP HANA op Azure S768m<br /> – 16 x Intel® Xeon® processor E7-8890 v4<br /> 384 CPU-kernen en 768 CPU-threads |  16,0 TB |  36 TB | Beschikbaar |
|---| SAP HANA op Azure S960m<br /> – 20 x Intel® Xeon® processor E7-8890 v4<br /> 480 CPU-kernen en 960 CPU-threads |  20,0 TB |  46 TB | Beschikbaar |


Onder SAP HANA TDIv5 maakt SAP klanten-specifieke omvang en klantspecifieke projecten, die kunnen leiden tot server configuraties, die niet worden vermeld als gecertificeerd in:

- [SAP HANA gecertificeerde apparaten](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

In veel gevallen hebben deze klantspecifieke server configuraties meer geheugen dan de server eenheden die met SAP zijn gecertificeerd. Bij het werken met SAP hebben klanten de mogelijkheid om SAP-ondersteuning te verkrijgen en te certificeren voor hun klantspecifieke, geomvangte server configuraties. In azure zijn de volgende HANA-standaard-Sku's (grote exemplaren) beschikbaar en in de micro soft-prijs lijst voor dergelijke TDIv5.

| SKU|CPU | Geheugen | Storage | Beschikbaarheid |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA op Azure S96<br /> – 2 x Intel® Xeon® processor E7-8890 v4<br /> 48 CPU-kernen en 96 CPU-threads |  768 GB |  3 TB | Beschikbaar |


| Oorspronkelijke SKU die kan worden <br /> uitgebreid in het geheugen | CPU | Geheugen | Storage | Beschikbaarheid |
| --- | --- | --- | --- | --- |
| S192m kan worden uitgebreid naar | SAP HANA op Azure S192xm<br /> – 4 x Intel® Xeon® processor E7-8890 v4<br /> 96 CPU-kernen en 192 CPU-threads |  6,0 TB |  16 TB | Beschikbaar |
| S384xm kan worden uitgebreid naar | SAP HANA op Azure S384xxm<br /> – 8 x Intel® Xeon® processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  12,0 TB |  28 TB | Beschikbaar |
| S576m kan worden uitgebreid naar | SAP HANA op Azure S576xm<br /> – 12 x Intel® Xeon® processor E7-8890 v4<br /> 288 CPU-kernen en 576 CPU-threads |  18,0 TB |  41 TB | Beschikbaar |
| S768m kan worden uitgebreid naar | SAP HANA op Azure S768xm<br /> – 16 x Intel® Xeon® processor E7-8890 v4<br /> 384 CPU-kernen en 768 CPU-threads |  24,0 TB |  56 TB | Beschikbaar |

- CPU-kernen = som van niet-Hyper-threaded CPU-kernen van de som van de processors van de server eenheid.
- CPU-threads = som van Compute-threads die worden verschaft door de Hyper-threaded CPU-kernen van de som van de processors van de server eenheid. De meeste eenheden zijn standaard geconfigureerd voor het gebruik van Hyper-Threading-technologie.
- Op basis van de aanbevelingen van de leverancier S768m, S768xm en S960m zijn niet geconfigureerd voor het gebruik van Hyper-Threading voor het uitvoeren van SAP HANA.


De gekozen specifieke configuraties zijn afhankelijk van de werk belasting, de CPU-resources en het gewenste geheugen. Het is mogelijk dat de OLTP-werk belasting de Sku's gebruikt die zijn geoptimaliseerd voor de OLAP-werk belasting. 

De hardware-basis voor de aanbiedingen, met uitzonde ring van eenheden voor klantspecifieke project formaat, zijn SAP HANA TDI-gecertificeerd. Er zijn twee verschillende klassen van hardware die de Sku's delen in:

- S72, S72m, S96, S144, S144m, S192, S192m en S192xm, die worden aangeduid als de ' type I-klasse ' van Sku's.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm en S960m, die worden aangeduid als de ' type II-klasse ' van Sku's.

Een volledige HANA-stempel met grote instanties wordt niet exclusief toegewezen aan&#39;één klant. Dit feit is ook van toepassing op de rekken van reken-en opslag resources die zijn verbonden via een netwerk infrastructuur die in Azure is geïmplementeerd. De infra structuur van een grote instantie, zoals Azure, implementeert &quot;verschillende&quot; tenants voor klanten die zijn geïsoleerd van elkaar op de volgende drie niveaus:

- **Netwerk**: Isolatie via virtuele netwerken binnen de HANA grote instantie stempel.
- **Opslag**: Isolatie door virtuele opslag machines met opslag volumes toe te wijzen en opslag volumes tussen tenants te isoleren.
- **Berekenen**: Toegewezen toewijzing van server eenheden aan één Tenant. Geen harde of zachte partitionering van server eenheden. Er kan geen enkele server of host-eenheid worden gedeeld tussen tenants. 

De implementaties van HANA grote instantie-eenheden tussen verschillende tenants zijn niet zichtbaar voor elkaar. HANA grote instantie-eenheden die in verschillende tenants zijn geïmplementeerd, kunnen niet rechtstreeks communiceren met elkaar op het stempel niveau van de grootschalige grote instantie. Alleen HANA grote instantie-eenheden binnen één Tenant kunnen met elkaar communiceren op het niveau van de HANA-grote instantie stempel.

Een geïmplementeerde Tenant in de stempel van een groot exemplaar wordt toegewezen aan één Azure-abonnement voor facturerings doeleinden. Voor een netwerk is het toegankelijk vanuit virtuele netwerken van andere Azure-abonnementen binnen dezelfde Azure-inschrijving. Als u met een ander Azure-abonnement in dezelfde Azure-regio implementeert, kunt u er ook voor kiezen om een gescheiden HANA-Tenant voor grote instanties te vragen.

Er zijn belang rijke verschillen tussen het uitvoeren van SAP HANA op HANA grote instanties en SAP HANA die worden uitgevoerd op Vm's die in azure zijn geïmplementeerd:

- Er is geen virtualisatiehost voor SAP HANA op Azure (grote exemplaren). U krijgt de prestaties van de onderliggende bare-metal hardware.
- In tegens telling tot Azure is de SAP HANA op de Azure-server (grote exemplaren) toegewezen aan een specifieke klant. Het is niet mogelijk dat een server eenheid of-host hard of zacht gepartitioneerd is. Als gevolg hiervan wordt een HANA grote instantie-eenheid gebruikt als een geheel aan een Tenant en aan u. Het opnieuw opstarten of afsluiten van de server leidt niet automatisch naar het besturings systeem en SAP HANA op een andere server worden geïmplementeerd. (Voor Sku's van het type I-klasse is de enige uitzonde ring als een server problemen ondervindt en de herimplementatie moet worden uitgevoerd op een andere server.)
- In tegens telling tot Azure, waarbij typen host-processors worden geselecteerd voor de beste prijs-prestatie verhouding, zijn de processor typen die zijn gekozen voor SAP HANA op Azure (grote instanties) de hoogste uitvoering van de Intel E7v3-en E7v4-processor lijn.

**Volgende stappen**
- Raadpleeg de [HLI-grootte](hana-sizing.md)

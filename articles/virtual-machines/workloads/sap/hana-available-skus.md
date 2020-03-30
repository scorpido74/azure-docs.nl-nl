---
title: SKU's voor SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: SKU's voor SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad4e3ff3df8b4aeecbbbee7883ba383b9fd0d9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617269"
---
# <a name="available-skus-for-hli"></a>Beschikbare SKU's voor HLI

SAP HANA op Azure -service (Large Instances) op basis van revisie 3-stempels is beschikbaar in verschillende configuraties in de Azure-regio's van:

- VS - west
- VS - oost
- Australië - oost
- Australië - zuidoost
- Europa -west
- Europa - noord
- Japan - oost
- Japan - west

SAP HANA op Azure -service (Large Instances) op basis van revisie 4-stempels is beschikbaar in verschillende configuraties in de Azure-regio's van:

- VS - west 2
- VS - oost
- VS - zuid-centraal
- Europa -west
- Europa - noord



[SAP HANA gecertificeerde SKU's van HANA grote Instances](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lijst als:

| SAP-oplossing | CPU | Geheugen | Storage | Beschikbaarheid |
| --- | --- | --- | --- | --- |
| Geoptimaliseerd voor OLAP: SAP BW, BW/4HANA<br /> of SAP HANA voor generieke OLAP-werkbelasting | SAP HANA op Azure S72<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 CPU-cores en 72 CPU-threads |  768 GB |  3 TB | Niet meer aangeboden |
| --- | SAP HANA op Azure S144<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 CPU-cores en 144 CPU-threads |  1,5 TB |  6 TB | Niet meer aangeboden |
| --- | SAP HANA op Azure S192<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 CPU-cores en 192 CPU-threads |  2,0 TB |  8 TB | Niet meer aangeboden |
| --- | SAP HANA op Azure S224<br /> – 4 x Intel® Xeon® Platinum 8276 processor (ook bekend als Cascade lake)<br /> 112 CPU-cores en 224 CPU-threads |  3,0 TB |  6,3 TB | Beschikbaar in revisie3 en Revision4 stempels  |
| --- | SAP HANA op Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-cores en 384 CPU-threads |  4,0 TB |  16 tb | Beschikbaar in Revision4 stempels |
| Geoptimaliseerd voor OLTP: SAP Business Suite<br /> op SAP HANA of S/4HANA (OLTP),<br /> generieke OLTP | SAP HANA op Azure S72m<br /> – 2 x Intel® Xeon® Processor E7-8890 v3<br /> 36 CPU-cores en 72 CPU-threads |  1,5 TB |  6 TB | Niet meer aangeboden |
|---| SAP HANA op Azure S144m<br /> – 4 x Intel® Xeon® Processor E7-8890 v3<br /> 72 CPU-cores en 144 CPU-threads |  3,0 TB |  12 TB | Niet meer aangeboden |
|---| SAP HANA op Azure S192m<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 CPU-cores en 192 CPU-threads  |  4,0 TB |  16 tb | Niet meer aangeboden |
| --- | SAP HANA op Azure S224m<br /> – 4 x Intel® Xeon® Platinum 8276 processor (ook bekend als Cascade lake)<br /> 112 CPU-cores en 224 CPU-threads |  6,0 TB |  10,5 TB | Beschikbaar in revisie3 en Revision4 stempels  |
|---| SAP HANA op Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-cores en 384 CPU-threads |  6,0 TB |  18 TB | Beschikbaar in Revision4 stempels|
|---| SAP HANA op Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-cores en 384 CPU-threads |  8,0 TB |  22 TB |  Beschikbaar in Revision4 stempels |
|---| SAP HANA op Azure S576m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-cores en 576 CPU-threads |  12,0 TB |  28 TB | Beschikbaar in Revision4 stempels|
|---| SAP HANA op Azure S768m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-cores en 768 CPU-threads |  16,0 TB |  36 TB | Beschikbaar in Revision4 stempels|
|---| SAP HANA op Azure S960m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 CPU-cores en 960 CPU-threads |  20,0 TB |  46 TB | Beschikbaar in Revision4 stempels|


Onder SAP HANA TDIv5 maakt SAP klantspecifieke dimensionerings- en klantspecifieke projecten mogelijk, die kunnen leiden tot serverconfiguraties, die niet worden vermeld als gecertificeerd in:

- [SAP HANA-gecertificeerde apparaten](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA gecertificeerde IaaS-platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

In veel gevallen bevatten deze klantspecifieke serverconfiguraties meer geheugen dan de servereenheden die zijn gecertificeerd met SAP. In samenwerking met SAP hebben klanten de mogelijkheid om SAP-ondersteuning te krijgen en te certificeren voor hun klantspecifieke serverconfiguraties. In Azure zijn de volgende HANA-standaardSku's beschikbaar en in de Microsoft-prijslijst voor dergelijke TDIv5-klantspecifieke grootteprojecten.

| SKU|CPU | Geheugen | Storage | Beschikbaarheid |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA op Azure S96<br /> – 2 x Intel® Xeon® Processor E7-8890 v4<br /> 48 CPU-cores en 96 CPU-threads |  768 GB |  3 TB | Beschikbaar in revisie3 en Revision4 stempels|


| Originele SKU die kan worden <br /> uitgebreid in geheugen | CPU | Geheugen | Storage | Beschikbaarheid |
| --- | --- | --- | --- | --- |
| S192m kan worden uitgebreid tot | SAP HANA op Azure S192xm<br /> – 4 x Intel® Xeon® Processor E7-8890 v4<br /> 96 CPU-cores en 192 CPU-threads |  6,0 TB |  16 tb | Niet meer aangeboden |
| S384xm kan worden uitgebreid tot | SAP HANA op Azure S384xxm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-cores en 384 CPU-threads |  12,0 TB |  28 TB | Beschikbaar in Revision4 stempels |
| S576m kan worden uitgebreid tot | SAP HANA op Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-cores en 576 CPU-threads |  18,0 TB |  41 TB | Beschikbaar in Revision4 stempels|
| S768m kan worden uitgebreid tot | SAP HANA op Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-cores en 768 CPU-threads |  24,0 TB |  56 TB | Beschikbaar in Revision4 stempels |

- CPU-cores = som van niet-hyper-threaded CPU-cores van de som van de processors van de server-eenheid.
- CPU-threads = som van compute threads die worden geleverd door hyper-threaded CPU-cores van de som van de processors van de server-eenheid. De meeste eenheden zijn standaard geconfigureerd om Hyper Threading-technologie te gebruiken.
- Op basis van leveranciersaanbevelingen zijn De S768m, S768xm en S960m niet geconfigureerd om Hyper-Threading te gebruiken voor het uitvoeren van SAP HANA.


De gekozen specifieke configuraties zijn afhankelijk van workload, CPU-bronnen en gewenst geheugen. Het is mogelijk dat de OLTP-workload de SKU's gebruikt die zijn geoptimaliseerd voor de OLAP-workload. 

De hardwarebasis voor de aanbiedingen, met uitzondering van eenheden voor klantspecifieke maatprojecten, is SAP HANA TDI-gecertificeerd. Twee verschillende klassen van hardware verdelen de SKU's in:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 en S224m die worden aangeduid als de "Type I klasse" van SKU's.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm en S960m, die worden aangeduid als de "Type II klasse" van SKU's.
- Als u geïnteresseerd bent in andere S224 SKU's die met Optane van 4,5 TB tot 9 TB worden aangeboden, neemt u contact op met uw Microsoft-accountteam voor meer informatie. 


Een complete HANA Large Instance stempel wordt niet exclusief toegewezen aan één klant&#39;het gebruik. Dit feit is ook van toepassing op de racks van reken- en opslagbronnen die zijn verbonden via een netwerkfabric die ook in Azure is geïmplementeerd. Hana Large Instance-infrastructuur, zoals Azure,&quot; implementeert verschillende klanttenants &quot;die op de volgende drie niveaus van elkaar zijn geïsoleerd:

- **Netwerk**: Isolatie via virtuele netwerken binnen de HANA Large Instance stempel.
- **Opslag:** Isolatie via opslagvirtuele machines waarop opslagvolumes zijn toegewezen en opslagvolumes tussen tenants isoleren.
- **Compute:** Toegewezen toewijzing van servereenheden aan één tenant. Geen harde of zachte partitionering van serverunits. Geen enkele server of hostunit tussen tenants delen. 

De implementaties van HANA Large Instance units tussen verschillende tenants zijn niet zichtbaar voor elkaar. HANA Large Instance units die in verschillende tenants worden ingezet, kunnen niet rechtstreeks met elkaar communiceren op het stempelniveau van HANA Large Instance. Alleen HANA Large Instance units binnen één tenant kunnen met elkaar communiceren op het NIVEAU van de HANA Large Instance stempel.

Een geïmplementeerde tenant in het stempel Grote instantie is toegewezen aan één Azure-abonnement voor factureringsdoeleinden. Voor een netwerk is het toegankelijk via virtuele netwerken van andere Azure-abonnementen binnen dezelfde Azure-inschrijving. Als u implementeert met een ander Azure-abonnement in dezelfde Azure-regio, u er ook voor kiezen om een gescheiden HANA-tenant voor grote instanties te vragen.

Er zijn aanzienlijke verschillen tussen het uitvoeren van SAP HANA op HANA Large Instance en SAP HANA die wordt uitgevoerd op VM's die in Azure zijn geïmplementeerd:

- Er is geen virtualisatielaag voor SAP HANA op Azure (Large Instances). U krijgt de prestaties van de onderliggende bare-metal hardware.
- In tegenstelling tot Azure is de SAP HANA op Azure (Large Instances) server gewijd aan een specifieke klant. Er is geen mogelijkheid dat een servereenheid of host hard of zacht is. Als gevolg hiervan wordt een HANA Large Instance-eenheid gebruikt als geheel toegewezen aan een huurder en daarmee aan u. Een reboot of uitschakeling van de server leidt niet automatisch tot het besturingssysteem en SAP HANA wordt geïmplementeerd op een andere server. (Voor SKU's van klasse Type I is de enige uitzondering als een server problemen ondervindt en herschikking op een andere server moet worden uitgevoerd.)
- In tegenstelling tot Azure, waar hostprocessortypen worden geselecteerd voor de beste prijs-prestatieverhouding, zijn de processortypen die zijn gekozen voor SAP HANA op Azure (Large Instances) de best presterende van de Intel E7v3- en E7v4-processorlijn.

**Volgende stappen**
- [HLI-formaat doorverwijzen](hana-sizing.md)

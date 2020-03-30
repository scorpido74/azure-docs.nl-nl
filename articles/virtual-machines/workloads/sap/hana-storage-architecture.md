---
title: Opslagarchitectuur van SAP HANA op Azure (grote exemplaren) | Microsoft Documenten
description: Opslagarchitectuur voor het implementeren van SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a12c454906d6c6ff702b7f635a91361bbe3994c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616893"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA (Large Instances) opslagarchitectuur

De opslagindeling voor SAP HANA op Azure (Large Instances) is door SAP HANA geconfigureerd op basis van het klassieke implementatiemodel per door SAP aanbevolen richtlijnen. De richtlijnen zijn vastgelegd in de [whitepaper van SAP HANA storage requirements.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)

De HANA Large Instance van de klasse Type I wordt geleverd met vier keer het geheugenvolume als opslagvolume. Voor de klasse Type II van HANA Large Instance-eenheden is de opslag niet vier keer meer. De eenheden worden geleverd met een volume dat is bedoeld voor het opslaan van HANA transactielog back-ups. Zie [SAP HANA (Large Instances) installeren en configureren op Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie.

Zie de volgende tabel in termen van opslagtoewijzing. De tabel geeft een overzicht van de ruwe capaciteit voor de verschillende volumes die bij de verschillende HANA Large Instance-eenheden zijn geleverd.

| HANA Large Instance SKU | hana/data | hana/log | hana/gedeeld | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 (S72) | 1.280 GB | 512 GB | 768 GB | 512 GB |
| S72m S72m | 3.328 GB | 768 GB |1.280 GB | 768 GB |
| S96 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4.608 GB | 1.024 GB | 1.536 GB | 1.024 GB |
| S192m | 11.520 GB | 1.536 GB | 1.792 GB | 1.536 GB |
| S192xm S192xm |  11.520 GB |  1.536 GB |  1.792 GB |  1.536 GB |
| S224 |  4.224 GB |  512 GB |  1.024 GB |  512 GB |
| S224m |  8.448 GB |  512 GB |  1.024 GB |  512 GB |
| S384 | 11.520 GB | 1.536 GB | 1.792 GB | 1.536 GB |
| S384m | 12.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xm S384xm | 16.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xxm |  20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S576m | 20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S576xm S576xm | 31.744 GB | 4.096 GB | 2.048 GB | 4.096 GB |
| S768m | 28.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S768xm S768xm | 40.960 GB | 6.144 GB | 4.096 GB | 6.144 GB |
| S960m | 36.000 GB | 4.100 GB | 2.050 GB | 4.100 GB |


Werkelijke geïmplementeerde volumes kunnen variëren op basis van implementatie en het gereedschap dat wordt gebruikt om de volumegroottes weer te geven.

Als u een HANA Large Instance SKU onderverdelen, kunnen een paar voorbeelden van mogelijke divisiestukken eruit zien als:

| Geheugenpartitie in GB | hana/data | hana/log | hana/gedeeld | hana/log/back-up |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1.792 GB | 640 GB | 1.024 GB | 640 GB |
| 1536 | 3.328 GB | 768 GB | 1.280 GB | 768 GB |


Deze groottes zijn ruwe volumeaantallen die enigszins kunnen variëren op basis van implementatie en de tools die worden gebruikt om naar de volumes te kijken. Er zijn ook andere partitieformaten, zoals 2,5 TB. Deze opslaggroottes worden berekend met een formule die vergelijkbaar is met die voor de vorige partities. De term "partities" betekent niet dat het besturingssysteem, geheugen of CPU-bronnen op enigerlei wijze zijn verdeeld. Het geeft opslagpartities aan voor de verschillende HANA-exemplaren die u mogelijk wilt implementeren op één HANA-eenheid voor grote instanties. 

Misschien heb je meer opslagruimte nodig. U opslag toevoegen door extra opslagruimte in eenheden van 1 TB aan te schaffen. Deze extra opslag kan worden toegevoegd als extra volume. Het kan ook worden gebruikt om een of meer van de bestaande volumes uit te breiden. Het is niet mogelijk om de grootte van de volumes zoals oorspronkelijk geïmplementeerd en meestal gedocumenteerd door de vorige tabellen te verminderen. Het is ook niet mogelijk om de namen van de volumes te wijzigen of namen te monteren. De eerder beschreven opslagvolumes zijn als NFS4-volumes gekoppeld aan de HANA Large Instance-eenheden.

U opslagmomentopnamen gebruiken voor back-up- en herstel- en disaster recovery-doeleinden. Zie [SAP HANA (Large Instances) hoge beschikbaarheid en herstel na noodgevallen op Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie.

Doorverwijzen [HLI ondersteunde scenario's](hana-supported-scenario.md) voor opslaglay-out details voor uw scenario.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Meerdere SAP HANA-exemplaren uitvoeren op één HANA-eenheid voor grote instanties

Het is mogelijk om meer dan één actieve SAP HANA-instantie te hosten op HANA Large Instance-eenheden. Om de mogelijkheden van opslagmomentopnamen en disaster recovery te bieden, vereist een dergelijke configuratie een volumeset per exemplaar. Momenteel kunnen HANA Large Instance-eenheden als volgt worden onderverdeeld:

- **S72, S72m, S96, S144, S192**: In stappen van 256 GB, met 256 GB de kleinste starteenheid. Verschillende stappen zoals 256 GB en 512 GB kunnen worden gecombineerd tot het maximum van het geheugen van het apparaat.
- **S144m en S192m**: In stappen van 256 GB, met 512 GB de kleinste eenheid. Verschillende stappen zoals 512 GB en 768 GB kunnen worden gecombineerd tot het maximum van het geheugen van het apparaat.
- **Klasse II**: In stappen van 512 GB, met de kleinste starteenheid van 2 TB. Verschillende stappen zoals 512 GB, 1 TB en 1,5 TB kunnen worden gecombineerd tot het maximum van het geheugen van het apparaat.

Enkele voorbeelden van het uitvoeren van meerdere SAP HANA-exemplaren kunnen er als volgt uitzien.

| SKU | Geheugengrootte | Opslaggrootte | Formaten met meerdere databases |
| --- | --- | --- | --- |
| S72 (S72) | 768 GB | 3 TB | 1x768-GB HANA-exemplaar<br /> of 1x512 GB exemplaar + 1x256 GB instantie<br /> of exemplaren van 3x256 GB | 
| S72m S72m | 1,5 TB | 6 TB | 3x512GB HANA-exemplaren<br />of 1x512 GB exemplaar + 1x1 TB instantie<br />of 6x256 GB-exemplaren<br />of 1x1,5 TB instantie | 
| S192m | 4 TB | 16 tb | Exemplaren van 8x512 GB<br />of 4x1-TB-exemplaren<br />of 4x512 GB exemplaren + 2x1-TB exemplaren<br />of exemplaren van 4x768 GB + exemplaren van 2x512 GB<br />of 1x4-TB instantie |
| S384xm S384xm | 8 TB | 22 TB | 4x2-TB exemplaren<br />of 2x4-TB-exemplaren<br />of 2x3-TB exemplaren + 1x2-TB exemplaren<br />of 2x2,5-TB exemplaren + 1x3-TB exemplaren<br />of 1x8-TB instantie |


Er zijn ook andere variaties. 

## <a name="encryption-of-data-at-rest"></a>Versleuteling van gegevens in rust
De opslag die wordt gebruikt voor HANA Large Instance maakt gebruik van transparante versleuteling voor de gegevens, omdat deze sinds eind 2018 op de schijven is opgeslagen. In eerdere implementaties u ervoor kiezen om de volumes versleuteld te krijgen. Als u tegen die optie hebt besloten, u vragen om de volumes online te versleutelen. De overgang van niet-versleutelde naar versleutelde volumes is transparant en vereist geen downtime. 

Met de klasse Type I van SKU's wordt het volume waarop de boot LUN is opgeslagen, versleuteld. In revision 3 HANA Large Instance-stempels moet u met behulp van de klasse Type II van SKU's van HANA Large Instance de boot LUN versleutelen met OS-methoden. In revision 4 HANA Large Instance stempels, met behulp van Type II eenheden het volume van de boot LUN wordt opgeslagen en is versleuteld in rust standaard ook. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Vereiste instellingen voor grotere HANA-exemplaren op HANA Large Instances
De opslag die wordt gebruikt in HANA Large Instances heeft een beperking van de bestandsgrootte. De [groottebeperking is 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) per bestand. In tegenstelling tot bestandsgroottebeperkingen in de EXT3-bestandssystemen, is HANA zich impliciet niet bewust van de opslagbeperking die wordt afgedwongen door de OPSLAG van HANA Large Instances. Als gevolg hiervan zal HANA niet automatisch een nieuw gegevensbestand maken wanneer de bestandsgroottelimiet van 16 TB is bereikt. Als HANA probeert om het bestand te groeien dan 16 TB, HANA zal fouten melden en de index server zal crashen aan het einde.

> [!IMPORTANT]
> Om te voorkomen dat HANA gegevensbestanden probeert te laten groeien boven de 16 TB-bestandsgroottelimiet van HANA Large Instance-opslag, moet u de volgende parameters instellen in het global.ini-configuratiebestand van HANA
> 
> - datavolume_striping=waar
> - datavolume_striping_size_gb = 15000
> - Zie ook SAP note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Wees je bewust van SAP-notitie [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Volgende stappen**
- [Ondersteunde scenario's doorverwijzen voor HANA-grote instanties](hana-supported-scenario.md)
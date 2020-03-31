---
title: StorSimple 8000-serie hardwarecomponenten en -status | Microsoft Documenten
description: Meer informatie over het bewaken van de hardwarecomponenten van uw StorSimple-apparaat via de StorSimple Device Manager-service.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: a987239669e7437a179f5f24034f4dbe45535663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254740"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>De StorSimple Device Manager-service gebruiken om hardwareonderdelen en status te controleren

## <a name="overview"></a>Overzicht
Dit artikel beschrijft de verschillende fysieke en logische componenten in uw on-premises StorSimple 8000-serie apparaat. Het legt ook uit hoe u de status van de apparaatcomponent controleren met behulp van het beheer van de **status status status en de hardwarestatus** in de StorSimple Device Manager-service.

Het verloop van de status en de **hardwarestatus** toont de hardwarestatus van alle StorSimple-apparaatonderdelen.

Onder de lijst van componenten voor 8100 staan drie secties die beschrijven:

* **Gedeelde componenten** – Deze maken geen deel uit van de controllers, zoals schijven, behuizings-, energie- en koelmodulecomponenten en PCM-temperatuur, lijnspanning en lijnstroomsensoren.
* **Controller 0 Componenten** – De componenten die zich op Controller 0 bevinden, zoals controller, SAS expander en connector, temperatuursensoren voor de controller en de verschillende netwerkinterfaces.
* **Controller 1 Componenten** – De componenten die controller 1 vormen, vergelijkbaar met die voor Controller 0.

Een 8600-apparaat heeft extra componenten die overeenkomen met de Extended Bunch of Disks (EBOD)-behuizing. Onder de lijst van componenten staan vijf secties. Hiervan zijn er drie secties die de componenten in de primaire behuizing bevatten en identiek zijn aan de secties die voor 8100 zijn beschreven. Er zijn twee extra secties voor de EBOD-behuizing die beschrijven:

* **EBOD Controller 0 Componenten** – De componenten die zich op EBOD-behuizing 0 bevinden, zoals de EBOD-controller, SAS-expander en -connector en temperatuursensoren voor de controller.
* **EBOD Controller 1 Componenten** – De componenten die EBOD-behuizing 1 vormen, vergelijkbaar met de componenten die zijn beschreven voor EBOD-behuizing 0.
* **EBOD-behuizing Gedeelde componenten** – De componenten die aanwezig zijn in de EBOD-behuizing en PCM die geen deel uitmaken van de EBOD-controller.

> [!NOTE]
> **De hardwarestatus is niet beschikbaar voor een StorSimple Cloud Appliance (8010/8020).**


## <a name="monitor-the-hardware-status"></a>De hardwarestatus controleren
Voer de volgende stappen uit om de hardwarestatus van een apparaatcomponent weer te geven:

1. Navigeer naar **apparaten,** selecteer een specifiek StorSimple-apparaat. Ga naar **Monitor > Hardware-status**.

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. Zoek de sectie **Hardware-onderdelen** en kies uit de beschikbare componenten. Klik op het componentlabel om de lijst uit te vouwen en de status van de verschillende apparaatonderdelen weer te geven. Zie de [gedetailleerde componentlijst voor de primaire bijlage](#component-list-for-primary-enclosure-of-storsimple-device) en de gedetailleerde [componentlijst voor de EBOD-bijlage](#component-list-for-ebod-enclosure-of-storsimple-device).

    ![](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. Gebruik het volgende kleurencoderingsschema om de componentstatus te interpreteren:
   
   * **Groene controle** : duidt op een gezonde component met **de OK-status.**
   * **Geel** : duidt een afgebroken component aan in **de status Waarschuwing.**
   * **Rode uitroeptekens** : duidt op een mislukte component met de **status Fout.**
   * **Wit met zwarte tekst** – geeft een component aan die niet aanwezig is.
   
   In de volgende schermafbeelding wordt een apparaat weergegeven met onderdelen in de status **OK,** **Waarschuwing**en **Fout.**
       
   ![](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   Door de **lijst met gedeelde componenten**uit te breiden, kunnen we zien dat de NVRAM en het cluster zijn afgebroken.

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   Als we de lijst **met onderdelen van Controller 1** uitbreiden, kunnen we zien dat het clusterknooppunt is mislukt.  

   ![](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. Als u een component tegenkomt die niet **in de status Gezond** staat, neemt u contact op met Microsoft Support. Als waarschuwingen zijn ingeschakeld op uw apparaat, ontvangt u een e-mailmelding. Zie [StorSimple hardwarecomponentvervanging](storsimple-hardware-component-replacement.md)als u een mislukte hardwarecomponent moet vervangen.

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>Componentlijst voor primaire behuizing van StorSimple-apparaat
In de volgende tabel worden de fysieke en logische componenten in de primaire behuizing (aanwezig in 8100 en 8600) van uw on-premises StorSimple-apparaat beschreven.

| Onderdeel | Module | Type | Locatie | Veldvervangbare eenheid (FRU)? | Beschrijving |
| --- | --- | --- | --- | --- | --- |
| Station in sleuf [0-11] |Schijven |Fysiek |Gedeeld |Ja |Voor elk van de SSD-schijven of de HDD-schijven in de primaire behuizing wordt één regel gepresenteerd. |
| Omgevingstemperatuursensor |Behuizing |Fysiek |Gedeeld |Nee |Meet de temperatuur in het chassis. |
| Middenvlaktemperatuursensor |Behuizing |Fysiek |Gedeeld |Nee |Meet de temperatuur van het middenvlak. |
| Hoorbaar alarm |Behuizing |Fysiek |Gedeeld |Nee |Geeft aan of het subsysteem voor hoorbaar alarm in het chassis functioneel is. |
| Behuizing |Behuizing |Fysiek |Gedeeld |Ja |Geeft de aanwezigheid van een chassis aan. |
| Instellingen voor behuizing |Behuizing |Fysiek |Gedeeld |Nee |Verwijst naar het voorpaneel van het chassis. |
| Lijnspanningssensoren |PCM |Fysiek |Gedeeld |Nee |Talrijke lijnspanningssensoren hebben hun staat weergegeven, wat aangeeft of de gemeten spanning binnen tolerantie is. |
| Lijnstroomsensoren |PCM |Fysiek |Gedeeld |Nee |Talrijke lijn huidige sensoren hebben hun staat weergegeven, die aangeeft of de gemeten stroom is binnen tolerantie. |
| Temperatuursensoren in PCM |PCM |Fysiek |Gedeeld |Nee |Tal van temperatuursensoren zoals Inlaat- en Hotspot-sensoren hebben hun toestand weergegeven, wat aangeeft of de gemeten temperatuur binnen de tolerantie valt. |
| Voeding [0-1] |PCM |Fysiek |Gedeeld |Ja |Voor elk van de voedingen in de twee PCM's aan de achterkant van het apparaat wordt één regel gepresenteerd. |
| Koeling [0-1] |PCM |Fysiek |Gedeeld |Ja |Een lijn wordt gepresenteerd voor elk van de vier koelventilatoren die in de twee PCM's. |
| Batterij [0-1] |PCM |Fysiek |Gedeeld |Ja |Voor elk van de back-upbatterijmodules die in het PCM zitten, wordt één regel gepresenteerd. |
| Metis |N.v.t. |Logisch |Gedeeld |N.v.t. |Geeft de status van de batterijen weer: of ze moeten worden opgeladen en naderen het einde van de levensduur. |
| Cluster |N.v.t. |Logisch |Gedeeld |N.v.t. |Hiermee geeft u de status weer van het cluster dat is gemaakt tussen de twee geïntegreerde controllermodules. |
| Clusterknooppunt |N.v.t. |Logisch |Gedeeld |N.v.t. |Geeft de status van de controller aan als onderdeel van het cluster. |
| Clusterquorum |N.v.t. |Logisch | |N.v.t. |Geeft de aanwezigheid aan van het meerderheidslidmaatschap van de schijf in de HDD-opslaggroep. |
| HDD-gegevensruimte |N.v.t. |Logisch |Gedeeld |N.v.t. |De opslagruimte die wordt gebruikt voor gegevens in de opslaggroep harde schijf (HDD). |
| HDD-beheerruimte |N.v.t. |Logisch |Gedeeld |N.v.t. |De ruimte die is gereserveerd in de HDD-opslaggroep voor beheertaken. |
| HDD-quorumruimte |N.v.t. |Logisch |Gedeeld |N.v.t. |De ruimte die is gereserveerd in de HDD-opslaggroep voor clusterquorum. |
| HDD vervangende ruimte |N.v.t. |Logisch |Gedeeld |N.v.t. |De ruimte gereserveerd in de HDD-opslagpool voor vervanging van de controller. |
| SSD-gegevensruimte |N.v.t. |Logisch |Gedeeld |N.v.t. |De opslagruimte die wordt gebruikt voor gegevens in de ssd-opslaggroep (solid state drive). |
| SSD NVRAM-ruimte |N.v.t. |Logisch |Gedeeld |N.v.t. |De opslagruimte in de SSD-opslaggroep die is toegewezen aan NVRAM-logica. |
| HDD-opslaggroep |N.v.t. |Logisch |Gedeeld |N.v.t. |Hiermee geeft u de status weer van de logische opslaggroep die is gemaakt met apparaat-hdd's. |
| SSD-opslaggroep |N.v.t. |Logisch |Gedeeld |N.v.t. |Hiermee geeft u de status weer van de logische opslaggroep die is gemaakt met apparaat-SSD's. |
| Controller [0-1] [staat] |I/o |Fysiek |Controller |Ja |Hiermee geeft u de status van de controller weer en of deze zich in de actieve of stand-bymodus in het chassis bevindt. |
| Temperatuursensoren in controller |I/o |Fysiek |Controller |Nee |Tal van temperatuursensoren zoals I/O-module, CPU-temperatuur, DIMM en PCIe-sensoren hebben hun status weergegeven, wat aangeeft of de aangetroffen temperatuur binnen de tolerantie valt. |
| SAS expander |I/o |Fysiek |Controller |Nee |Geeft de status aan van de seriële gekoppelde SCSI-expander (SAS), die wordt gebruikt om de geïntegreerde opslag op de controller aan te sluiten. |
| SAS-connector [0-1] |I/o |Fysiek |Controller |Nee |Geeft de status aan van elke SAS-connector, die wordt gebruikt om geïntegreerde opslag aan te sluiten op de SAS-expander. |
| SBB mid-plane interconnect |I/o |Fysiek |Controller |Nee |Geeft de status van de middenvlakconnector aan, die wordt gebruikt om elke controller aan te sluiten op het middenvlak. |
| Processorkern |I/o |Fysiek |Controller |Nee |Geeft de status van de processorkernen binnen elke controller aan. |
| Behuizing elektronica vermogen |I/o |Fysiek |Controller |Nee |Geeft de status aan van het stroomsysteem dat door de behuizing wordt gebruikt. |
| Behuizing elektronica diagnostiek |I/o |Fysiek |Controller |Nee |Geeft de status aan van de diagnostische subsystemen die door de controller worden geleverd. |
| Baseboard Management Controller (BMC) |I/o |Fysiek |Controller |Nee |Geeft de status van de baseboard management controller (BMC) aan, een gespecialiseerde serviceprocessor die het hardwareapparaat controleert via sensoren en communiceert met de systeembeheerder via een onafhankelijke verbinding. |
| Ethernet |I/o |Fysiek |Controller |Nee |Geeft de status van elk van de netwerkinterfaces aan, dat wil zeggen het beheer en de gegevenspoorten die op de controller worden geleverd. |
| Nvram |I/o |Fysiek |Controller |Nee |Geeft de status van NVRAM aan, een niet-vluchtig willekeurig toegangsgeheugen dat wordt ondersteund door de batterij die dient om toepassingskritieke informatie te bewaren in het geval van stroomuitval. |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>Componentlijst voor EBOD-behuizing van StorSimple-apparaat
In de volgende tabel worden de fysieke en logische componenten in de EBOD-behuizing (alleen aanwezig in 8600-model) van uw on-premises StorSimple-apparaat beschreven.

| Onderdeel | Module | Type | Locatie | Fru? | Beschrijving |
| --- | --- | --- | --- | --- | --- |
| Station in sleuf [0-11] |Schijven |Fysiek |Gedeeld |Ja |Voor elk van de HDD-schijven aan de voorzijde van de EBOD-behuizing wordt één lijn gepresenteerd. |
| Omgevingstemperatuursensor |Behuizing |Fysiek |Gedeeld |Nee |Meet de temperatuur in het chassis. |
| Middenvlaktemperatuursensor |Behuizing |Fysiek |Gedeeld |Nee |Meet de temperatuur van het middenvlak. |
| Hoorbaar alarm |Behuizing |Fysiek |Gedeeld |Nee |Geeft aan of het subsysteem voor hoorbaar alarm in het chassis functioneel is. |
| Behuizing |Behuizing |Fysiek |Gedeeld |Ja |Geeft de aanwezigheid van een chassis aan. |
| Instellingen voor behuizing |Behuizing |Fysiek |Gedeeld |Nee |Verwijst naar de OPS of het voorpaneel van het chassis. |
| Lijnspanningssensoren |PCM |Fysiek |Gedeeld |Nee |Talrijke lijnspanningssensoren hebben hun staat weergegeven, wat aangeeft of de gemeten spanning binnen tolerantie is. |
| Lijnstroomsensoren |PCM |Fysiek |Gedeeld |Nee |Talrijke lijn huidige sensoren hebben hun staat weergegeven, die aangeeft of de gemeten stroom is binnen tolerantie. |
| Temperatuursensoren in PCM |PCM |Fysiek |Gedeeld |Nee |Tal van temperatuursensoren zoals Inlaat- en Hotspot-sensoren hebben hun toestand weergegeven, wat aangeeft of de gemeten temperatuur binnen de tolerantie valt. |
| Voeding [0-1] |PCM |Fysiek |Gedeeld |Ja |Voor elk van de voedingen in de twee PCM's aan de achterkant van het apparaat wordt één regel gepresenteerd. |
| Koeling [0-1] |PCM |Fysiek |Gedeeld |Ja |Een lijn wordt gepresenteerd voor elk van de vier koelventilatoren die in de twee PCM's. |
| Lokale opslag [HDD] |N.v.t. |Logisch |Gedeeld |N.v.t. |Hiermee geeft u de status weer van de logische opslaggroep die is gemaakt met apparaat-hdd's. |
| Controller [0-1] [staat] |I/o |Fysiek |Controller |Ja |Hiermee geeft u de status van de controllers weer in de EBOD-module. |
| Temperatuursensoren in EBOD |I/o |Fysiek |Controller |Nee |Talrijke temperatuursensoren van elke controller hebben hun toestand weergegeven, wat aangeeft of de aangetroffen temperatuur binnen de tolerantie valt. |
| SAS expander |I/o |Fysiek |Controller |Nee |Geeft de status aan van de SAS-expander, die wordt gebruikt om de geïntegreerde opslag op de controller aan te sluiten. |
| SAS-connector [0-2] |I/o |Fysiek |Controller |Nee |Geeft de status aan van elke SAS-connector, die wordt gebruikt om geïntegreerde opslag aan te sluiten op de SAS-expander. |
| SBB mid-plane interconnect |I/o |Fysiek |Controller |Nee |Geeft de status van de middenvlakconnector aan, die wordt gebruikt om elke controller aan te sluiten op het middenvlak. |
| Behuizing elektronica vermogen |I/o |Fysiek |Controller |Nee |Geeft de status aan van het stroomsysteem dat door de behuizing wordt gebruikt. |
| Behuizing elektronica diagnostiek |I/o |Fysiek |Controller |Nee |Geeft de status aan van de diagnostische subsystemen die door de controller worden geleverd. |
| Verbinding met apparaatcontroller |I/o |Fysiek |Controller |Nee |Geeft de status van de verbinding tussen de EBOD I/O-module en de apparaatcontroller aan. |

## <a name="next-steps"></a>Volgende stappen
* Als u de StorSimple Device Manager-service wilt gebruiken om uw apparaat te beheren, gaat u [naar de StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)
* Als u een apparaatcomponent met een gedegradeerde of mislukte status moet oplossen, raadpleegt u [de bewakingsindicatoren van StorSimple.](storsimple-monitoring-indicators.md)
* Zie [StorSimple hardwarecomponentvervanging](storsimple-hardware-component-replacement.md)als u een mislukte hardwarecomponent wilt vervangen.
* Als u apparaatproblemen blijft ondervinden, [neemt u contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md).


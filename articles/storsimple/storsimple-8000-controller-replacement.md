---
title: Een StorSimple 8000-serie apparaatcontroller vervangen | Microsoft Documenten
description: Hier wordt uitgelegd hoe u een of beide controllermodules op uw StorSimple 8000-serie verwijderen en vervangen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dd2f6fcc9b2f5d716566e91e89487969613d1005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267922"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Een controllermodule op uw StorSimple-apparaat vervangen
## <a name="overview"></a>Overzicht
In deze zelfstudie wordt uitgelegd hoe u een of beide controllermodules in een StorSimple-apparaat verwijderen en vervangen. Het bespreekt ook de onderliggende logica voor de single en dual controller vervanging scenario's.

> [!NOTE]
> Voordat u een controllervervanging uitvoert, raden we u aan de firmware van uw controller altijd bij te werken naar de nieuwste versie.
> 
> Om schade aan uw StorSimple-apparaat te voorkomen, moet u de controller niet uitwerpen totdat de LED's als een van de volgende gegevens worden weergegeven:
> 
> * Alle lichten zijn uit.
> * LED ![3, Green](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png)check ![pictogram,](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) en Rode kruis pictogram knipperen, en LED 0 en LED 7 zijn **aan**.


In de volgende tabel worden de vervangingsscenario's voor ondersteunde controller weergegeven.

| Case | Vervangingsscenario | Toepasselijke procedure |
|:--- |:--- |:--- |
| 1 |De ene controller is in een mislukte staat, de andere controller is gezond en actief. |[Vervanging van](#replace-a-single-controller)één controller , die de [logica achter een enkele controllervervanging](#single-controller-replacement-logic)beschrijft , evenals de [vervangingsstappen](#single-controller-replacement-steps). |
| 2 |Beide controllers zijn mislukt en moeten worden vervangen. Het chassis, de schijven en de schijfbehuizing zijn in orde. |[Dual controller vervanging](#replace-both-controllers), die de [logica achter een dubbele controller vervanging](#dual-controller-replacement-logic)beschrijft , evenals de vervanging [stappen](#dual-controller-replacement-steps). |
| 3 |Controllers van hetzelfde apparaat of van verschillende apparaten worden verwisseld. Het chassis, de schijven en de schijfbehuizingen zijn in orde. |Er verschijnt een waarschuwingsbericht voor de fout in de sleuf. |
| 4 |Een controller ontbreekt en de andere controller mislukt. |[Dual controller vervanging](#replace-both-controllers), die de [logica achter een dubbele controller vervanging](#dual-controller-replacement-logic)beschrijft , evenals de vervanging [stappen](#dual-controller-replacement-steps). |
| 5 |Een of beide controllers zijn mislukt. U hebt geen toegang tot het apparaat via de seriële console of Windows PowerShell remoting. |[Neem contact op met Microsoft Support](storsimple-8000-contact-microsoft-support.md) voor een handmatige vervangingsprocedure voor de controller. |
| 6 |De controllers hebben een andere build versie, die kan te wijten zijn aan:<ul><li>Controllers hebben een andere softwareversie.</li><li>Controllers hebben een andere firmware-versie.</li></ul> |Als de softwareversies van de controller verschillend zijn, detecteert de vervangingslogica dat en werkt de softwareversie op de vervangende controller bij.<br><br>Als de firmwareversies van de controller verschillend zijn en de oude firmwareversie **niet** automatisch kan worden geüderd, verschijnt er een waarschuwingsbericht in de Azure-portal. U moet scannen op updates en installeer de firmware-updates.</br></br>Als de firmwareversies van de controller verschillend zijn en de oude firmwareversie automatisch kan worden geüpdatet, detecteert de logica voor het vervangen van de controller dit en nadat de controller is gestart, wordt de firmware automatisch bijgewerkt. |

U moet een controllermodule verwijderen als deze is mislukt. Een of beide controller modules kunnen mislukken, wat kan resulteren in een enkele controller vervanging of dual controller vervanging. Zie voor vervangingsprocedures en de logica erachter het volgende:

* [Eén controller vervangen](#replace-a-single-controller)
* [Beide controllers vervangen](#replace-both-controllers)
* [Een controller verwijderen](#remove-a-controller)
* [Een controller invoegen](#insert-a-controller)
* [De actieve controller op uw apparaat identificeren](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Voordat u een controller verwijdert en vervangt, controleert u de veiligheidsinformatie in [StorSimple-hardwarecomponentvervanging.](storsimple-8000-hardware-component-replacement.md)
> 
> 

## <a name="replace-a-single-controller"></a>Eén controller vervangen
Wanneer een van de twee controllers op het Microsoft Azure StorSimple-apparaat is mislukt, defect is of ontbreekt, moet u één controller vervangen.

### <a name="single-controller-replacement-logic"></a>Logica voor het vervangen van één controller
Bij één controllervervanging moet u eerst de defecte controller verwijderen. (De resterende controller in het apparaat is de actieve controller.) Wanneer u de vervangende controller invoegt, vinden de volgende acties plaats:

1. De vervangende controller begint onmiddellijk te communiceren met het StorSimple-apparaat.
2. Een momentopname van de virtuele harde schijf (VHD) voor de actieve controller wordt gekopieerd op de vervangende controller.
3. De momentopname wordt zo gewijzigd dat wanneer de vervangende controller vanaf deze VHD begint, deze wordt herkend als een stand-by controller.
4. Wanneer de wijzigingen zijn voltooid, start de vervangende controller als de stand-by controller.
5. Wanneer beide controllers worden uitgevoerd, komt het cluster online.

### <a name="single-controller-replacement-steps"></a>Vervangingsstappen voor één controller
Voer de volgende stappen uit als een van de controllers in uw Microsoft Azure StorSimple-apparaat uitvalt. (De andere controller moet actief en actief zijn. Als beide controllers uitvallen of defect zijn, gaat u naar [de vervangingsstappen van de dubbele controller](#dual-controller-replacement-steps).)

> [!NOTE]
> Het kan 30 – 45 minuten duren voordat de controller opnieuw is opgestart en volledig herstelt van de vervangingsprocedure voor één controller. De totale tijd voor de gehele procedure, inclusief het bevestigen van de kabels, is ongeveer 2 uur.


#### <a name="to-remove-a-single-failed-controller-module"></a>Een mislukte controllermodule verwijderen
1. Ga in de Azure-portal naar de StorSimple Device Manager-service, klik op **Apparaten**en klik vervolgens op de naam van het apparaat dat u wilt controleren.
2. Ga naar **Monitor > Hardware-status**. De status van Controller 0 of Controller 1 moet rood zijn, wat duidt op een storing.
   
   > [!NOTE]
   > De defecte controller in een enkele controller vervanging is altijd een stand-by controller.
   
3. Gebruik figuur 1 en de volgende tabel om de defecte controllermodule te zoeken.
   
    ![Backplane van primaire behuizingsmodules van het apparaat](./media/storsimple-controller-replacement/IC740994.png)
   
    **Figuur 1** Achterkant van StorSimple-apparaat
   
   | Label | Beschrijving |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controller 0 |
   | 4 |Controller 1 |
4. Verwijder op de defecte controller alle aangesloten netwerkkabels uit de gegevenspoorten. Als u een 8600-model gebruikt, verwijdert u ook de SAS-kabels die de controller aansluiten op de EBOD-controller.
5. Volg de stappen in [het verwijderen van een controller](#remove-a-controller) om de defecte controller te verwijderen.
6. Installeer de fabrieksvervanging in dezelfde sleuf waaruit de defecte controller is verwijderd. Dit activeert de logica voor het vervangen van één controller. Zie logica [voor het vervangen van één controller](#single-controller-replacement-logic)voor meer informatie .
7. Terwijl de logica voor het vervangen van één controller op de achtergrond vordert, sluit u de kabels opnieuw aan. Zorg ervoor dat alle kabels precies op dezelfde manier worden aangesloten als voor de vervanging.
8. Controleer de **status Controller** en de **clusterstatus** in de Azure-portal nadat de controller opnieuw is opgestart, om te controleren of de controller weer in orde is en in de stand-bymodus staat.

> [!NOTE]
> Als u het apparaat controleert via de seriële console, ziet u mogelijk meerdere opnieuw opstarten terwijl de controller herstelt van de vervangingsprocedure. Wanneer het seriële consolemenu wordt gepresenteerd, weet u dat de vervanging is voltooid. Neem contact op [met Microsoft Support](storsimple-8000-contact-microsoft-support.md)als het menu niet binnen twee uur na het starten van de controllervervanging wordt weergegeven.
>
> Vanaf update 4 u de `Get-HCSControllerReplacementStatus` cmdlet in de Windows PowerShell-interface van het apparaat ook gebruiken om de status van het vervangingsproces van de controller te controleren.
> 

## <a name="replace-both-controllers"></a>Beide controllers vervangen
Wanneer beide controllers op het Microsoft Azure StorSimple-apparaat zijn mislukt, defect zijn of ontbreken, moet u beide controllers vervangen. 

### <a name="dual-controller-replacement-logic"></a>Logica voor vervanging van dubbele controller
Bij een vervanging van de dubbele controller verwijdert u eerst beide defecte controllers en voegt u vervangingen in. Wanneer de twee vervangende controllers worden ingevoegd, vinden de volgende acties plaats:

1. De vervangende voor de verwerking verantwoordelijke in vak 0 controleert het volgende:
   
   1. Is het met behulp van de huidige versies van de firmware en software?
   2. Maakt het deel uit van het cluster?
   3. Wordt de peer controller uitgevoerd en is deze geclusterd?
      
      Als geen van deze voorwaarden waar is, zoekt de controller naar de nieuwste dagelijkse back-up (in de **nonDOMstorage** op station S). De controller kopieert de nieuwste momentopname van de VHD van de back-up.
2. De controller in sleuf 0 gebruikt de momentopname om zichzelf te afbeelding.
3. Ondertussen wacht de controller in slot 1 op controller 0 om de beeldvorming te voltooien en te starten.
4. Nadat controller 0 is gestart, detecteert controller 1 het cluster dat is gemaakt door controller 0, waardoor de logica voor de vervanging van één controller wordt geactiveerd. Zie logica [voor het vervangen van één controller](#single-controller-replacement-logic)voor meer informatie .
5. Daarna worden beide controllers uitgevoerd en komt het cluster online.

> [!IMPORTANT]
> Na een vervanging van een dubbele controller is het essentieel dat u na het apparaat storSimple een handmatige back-up van het apparaat maakt. Dagelijkse apparaatconfiguratieback-ups worden pas geactiveerd nadat 24 uur zijn verstreken. Werk samen met [Microsoft Support](storsimple-8000-contact-microsoft-support.md) om een handmatige back-up van uw apparaat te maken.


### <a name="dual-controller-replacement-steps"></a>Vervangingsstappen voor dubbele controller
Deze werkstroom is vereist wanneer beide controllers in uw Microsoft Azure StorSimple-apparaat zijn mislukt. Dit kan gebeuren in een datacenter waarin het koelsysteem niet meer werkt, waardoor beide controllers binnen korte tijd uitvallen. Afhankelijk van of het StorSimple-apparaat is uitgeschakeld of ingeschakeld en of u een 8600- of een 8100-model gebruikt, is een andere set stappen vereist.

> [!IMPORTANT]
> Het kan 45 minuten tot 1 uur duren voordat de controller opnieuw is opgestart en volledig kan herstellen van een dubbele controllervervangingsprocedure. De totale tijd voor de gehele procedure, inclusief het bevestigen van de kabels, is ongeveer 2,5 uur.

#### <a name="to-replace-both-controller-modules"></a>Beide controllermodules vervangen
1. Als het apparaat is uitgeschakeld, slaat u deze stap over en gaat u door naar de volgende stap. Als het apparaat is ingeschakeld, schakelt u het apparaat uit.
   
   1. Als u een 8600-model gebruikt, schakelt u eerst de primaire behuizing uit en schakelt u de EBOD-behuizing uit.
   2. Wacht tot het apparaat volledig is uitgeschakeld. Alle LED's aan de achterkant van het apparaat zijn uitgeschakeld.
2. Verwijder alle netwerkkabels die zijn aangesloten op de datapoorten. Als u een 8600-model gebruikt, verwijdert u ook de SAS-kabels die de primaire behuizing aansluiten op de EBOD-behuizing.
3. Verwijder beide controllers van het StorSimple-apparaat. Zie [een controller verwijderen](#remove-a-controller)voor meer informatie.
4. Plaats eerst de fabrieksvervanging voor Controller 0 en voeg vervolgens Controller 1 in. Zie [een controller invoegen](#insert-a-controller)voor meer informatie. Dit activeert de dubbele controller vervanging logica. Zie logica [voor vervanging van twee controlleren](#dual-controller-replacement-logic)voor meer informatie .
5. Terwijl de logica voor het vervangen van de controller op de achtergrond vordert, sluit u de kabels opnieuw aan. Zorg ervoor dat alle kabels precies op dezelfde manier worden aangesloten als voor de vervanging. Bekijk de gedetailleerde instructies voor uw model in de sectie Kabel van uw apparaat om [uw StorSimple 8100-apparaat](storsimple-8100-hardware-installation.md) te installeren of [installeer uw StorSimple 8600-apparaat.](storsimple-8600-hardware-installation.md)
6. Schakel het StorSimple-apparaat in. Als u een 8600-model gebruikt:
   
   1. Zorg ervoor dat de EBOD-behuizing eerst is ingeschakeld.
   2. Wacht tot de EBOD-behuizing actief is.
   3. Schakel de primaire behuizing in.
   4. Nadat de eerste controller opnieuw is opgestart en in een gezonde staat verkeert, wordt het systeem uitgevoerd.
      
      > [!NOTE]
      > Als u het apparaat controleert via de seriële console, ziet u mogelijk meerdere opnieuw opstarten terwijl de controller herstelt van de vervangingsprocedure. Wanneer het seriële consolemenu wordt weergegeven, weet u dat de vervanging is voltooid. Neem contact op [met Microsoft Support](storsimple-8000-contact-microsoft-support.md)als het menu niet binnen 2,5 uur na het starten van de controller wordt weergegeven.
     
## <a name="remove-a-controller"></a>Een controller verwijderen
Gebruik de volgende procedure om een defecte controllermodule van uw StorSimple-apparaat te verwijderen.

> [!NOTE]
> De volgende illustraties zijn voor controller 0. Voor controller 1 zouden deze worden teruggedraaid.


#### <a name="to-remove-a-controller-module"></a>Een controllermodule verwijderen
1. Pak de modulevergrendeling tussen duim en wijsvinger.
2. Knijp voorzichtig uw duim en wijsvinger samen om de controllervergrendeling los te laten.
   
    ![Vergrendeling voor de controller loslaten](./media/storsimple-controller-replacement/IC741047.png)
   
    **Figuur 2** Vergrendeling voor de controller loslaten
3. Gebruik de vergrendeling als handvat om de controller uit het chassis te schuiven.
   
    ![Schuifcontroller uit chassis](./media/storsimple-controller-replacement/IC741048.png)
   
    **Figuur 3** De controller uit het chassis schuiven

## <a name="insert-a-controller"></a>Een controller invoegen
Gebruik de volgende procedure om een in de fabriek geleverde controllermodule te installeren nadat u een defecte module van uw StorSimple-apparaat hebt verwijderd.

#### <a name="to-install-a-controller-module"></a>Een controllermodule installeren
1. Controleer of er schade is aan de interfaceconnectoren. Installeer de module niet als een van de connectorpennen beschadigd of gebogen is.
2. Schuif de controllermodule in het chassis terwijl de vergrendeling volledig is losgelaten.
   
    ![Schuifcontroller in chassis](./media/storsimple-controller-replacement/IC741053.png)
   
    **Figuur 4** Schuifcontroller in het chassis
3. Als de controllermodule is geplaatst, begint u met het sluiten van de vergrendeling terwijl u de controllermodule in het chassis blijft duwen. De vergrendeling zal worden aangegaan om de controller op zijn plaats te begeleiden.
   
    ![Vergrendeling van de sluitcontroller](./media/storsimple-controller-replacement/IC741054.png)
   
    **Figuur 5** De vergrendeling van de controller sluiten
4. Je bent klaar als de vergrendeling op zijn plaats klikt. De **OK** LED moet nu aan.
   
   > [!NOTE]
   > Het kan tot 5 minuten duren voordat de controller en de LED geactiveerd zijn.
  
5. Als u wilt controleren of de vervanging succesvol is, gaat u in de Azure-portal naar uw apparaat en navigeert u vervolgens naar de**status Hardware** **controleren** > en controleert u of zowel controller 0 als controller 1 in orde zijn (status is groen).

## <a name="identify-the-active-controller-on-your-device"></a>De actieve controller op uw apparaat identificeren
Er zijn veel situaties, zoals de registratie van nieuwe apparaten of vervanging van de controller, waarvoor u de actieve controller op een StorSimple-apparaat moet lokaliseren. De actieve controller verwerkt alle schijffirmware- en netwerkbewerkingen. U een van de volgende methoden gebruiken om de actieve controller te identificeren:

* [De Azure-portal gebruiken om de actieve controller te identificeren](#use-the-azure-portal-to-identify-the-active-controller)
* [Windows PowerShell voor StorSimple gebruiken om de actieve controller te identificeren](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Controleer het fysieke apparaat om de actieve controller te identificeren](#check-the-physical-device-to-identify-the-active-controller)

Elk van deze procedures wordt hierna beschreven.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>De Azure-portal gebruiken om de actieve controller te identificeren
Navigeer in de Azure-portal naar uw apparaat en ga vervolgens naar**De status Hardware** **controleren** > en blader naar de sectie **Controllers.** Hier u controleren welke controller actief is.

![Actieve controller identificeren in Azure-portal](./media/storsimple-controller-replacement/IC752072.png)

**Figuur 6** Azure-portal met de actieve controller

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Windows PowerShell voor StorSimple gebruiken om de actieve controller te identificeren
Wanneer u uw apparaat via de seriële console benadert, wordt een bannerbericht weergegeven. Het bannerbericht bevat basisapparaatinformatie zoals het model, de naam, de geïnstalleerde softwareversie en de status van de controller die u gebruikt. In de volgende afbeelding ziet u een voorbeeld van een bannerbericht:

![Seriële bannerbericht](./media/storsimple-controller-replacement/IC741098.png)

**Figuur 7** Bannerbericht met controller 0 als Actief

U het bannerbericht gebruiken om te bepalen of de controller met wie u bent verbonden actief of passief is.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Controleer het fysieke apparaat om de actieve controller te identificeren
Als u de actieve controller op uw apparaat wilt identificeren, zoekt u de blauwe LED boven de DATA 5-poort op de achterkant van de primaire behuizing.

Als deze LED knippert, is de controller actief en staat de andere controller in de stand-bymodus. Gebruik het volgende diagram en de volgende tabel als hulpmiddel.

![Primaire backplane van apparaatbehuizing met datapoorten](./media/storsimple-controller-replacement/IC741055.png)

**Figuur 8** Achterkant van primaire behuizing met datapoorten en monitoring-LED's

| Label | Beschrijving |
|:--- |:--- |
| 1-6 |DATA 0 – 5 netwerkpoorten |
| 7 |Blauwe LED |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [het vervangen van StorSimple-hardwarecomponenten](storsimple-8000-hardware-component-replacement.md).


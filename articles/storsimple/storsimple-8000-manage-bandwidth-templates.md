---
title: Bandbreedtesjablonen beheren voor de StorSimple 8000-serie | Microsoft Documenten
description: Beschrijft hoe u Bandbreedtesjablonen van StorSimple beheert, waarmee u het bandbreedteverbruik beheren.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 13a3e57bb27c075fc045e87790dbe13369ed9f8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254896"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>De StorSimple Device Manager-service gebruiken om bandbreedtesjablonen van StorSimple te beheren

## <a name="overview"></a>Overzicht

Met bandbreedtesjablonen u het gebruik van netwerkbandbreedte configureren in meerdere tijdsschema's om de gegevens van het StorSimple-apparaat naar de cloud te brengen.

Met bandbreedtebeperkingsschema's u:

* Geef aangepaste bandbreedteschema's op, afhankelijk van het gebruik van het workloadnetwerk.
* Centraliseer het beheer en hergebruik van de schema's op meerdere apparaten op een eenvoudige en naadloze manier.

> [!NOTE]
> Deze functie is alleen beschikbaar voor Fysieke Apparaten van StorSimple (modellen 8100 en 8600) en niet voor StorSimple Cloud Appliances (modellen 8010 en 8020).


## <a name="the-bandwidth-templates-blade"></a>Het blade van de bandbreedtesjablonen

Het **blade met bandbreedtesjablonen** heeft alle bandbreedtesjablonen voor uw service in een tabelindeling en bevat de volgende informatie:

* **Naam** : een unieke naam die is toegewezen aan de bandbreedtesjabloon toen deze werd gemaakt.
* **Planning** : het aantal schema's in een bepaalde bandbreedtesjabloon.
* **Gebruikt door** – Het aantal volumes met behulp van de bandbreedtesjablonen.

U ook aanvullende informatie vinden om bandbreedtesjablonen te configureren in:

* [Vragen en antwoorden over bandbreedtesjablonen](#questions-and-answers-about-bandwidth-templates)
* [Aanbevolen procedures voor bandbreedtesjablonen](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Een bandbreedtesjabloon toevoegen

Voer de volgende stappen uit om een nieuwe bandbreedtesjabloon te maken.

#### <a name="to-add-a-bandwidth-template"></a>Een bandbreedtesjabloon toevoegen

1. Ga naar uw StorSimple Device Manager-service, klik op **Bandbreedtesjablonen** en klik op **+ Bandbreedtesjabloon toevoegen.**

    ![Klik op + Bandbreedtesjabloon toevoegen](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Ga in het **sjabloonblad Bandbreedte toevoegen** de volgende stappen uit:
   
    1. Geef een unieke naam op voor uw bandbreedtesjabloon.
    2. Definieer een bandbreedteschema. Ga als lid van het programma:
   
        1. Kies in de vervolgkeuzelijst de **dagen** van de week waarvoor het schema is geconfigureerd. U meerdere dagen selecteren.        
        
        2. Voer een **begintijd** in _hh:mm-indeling_ in. Dit is wanneer het schema zal beginnen.

        3. Voer een **eindtijd** in _hh:mm-indeling_ in. Dit is wanneer het schema zal stoppen.
      
           > [!NOTE]
           > Overlappende schema's zijn niet toegestaan. Als de begin- en eindtijden resulteren in een overlappend schema, ziet u een foutbericht in die zin.

        4. Geef de **bandbreedtesnelheid op**. Dit is de bandbreedte in Megabits per seconde (Mbps) die door uw StorSimple-apparaat wordt gebruikt in bewerkingen waarbij de cloud betrokken is (zowel uploads als downloads). Geef voor dit veld een waarde op tussen 1 en 1000.

            ![Bandbreedteschema definiëren](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Herhaal de bovenstaande stappen om meerdere schema's voor uw sjabloon te definiëren totdat u klaar bent.

        5. Klik **op Toevoegen** om een bandbreedtesjabloon te maken. De gemaakte sjabloon wordt toegevoegd aan de lijst met bandbreedtesjablonen.
      

## <a name="edit-a-bandwidth-template"></a>Een bandbreedtesjabloon bewerken

Voer de volgende stappen uit om een bandbreedtesjabloon te bewerken.

### <a name="to-edit-a-bandwidth-template"></a>Een bandbreedtesjabloon bewerken

1. Ga naar uw StorSimple Device Manager-service en klik op **Bandbreedtesjablonen**.
2. Selecteer in de lijst met bandbreedtesjablonen de sjabloon die u wilt verwijderen. Klik met de rechtermuisknop en selecteer in het contextmenu **Verwijderen**.
3. Klik op **OK**wanneer u om bevestiging wordt gevraagd. Hiermee moet de bandbreedtesjabloon worden verwijderd. 
4. De lijst met bandbreedtesjablonen-updates om de verwijdering weer te geven.

> [!NOTE]
> U uw wijzigingen niet opslaan als het bewerkte schema overlapt met een bestaand schema in de bandbreedtesjabloon die u wijzigt.

## <a name="delete-a-bandwidth-template"></a>Een bandbreedtesjabloon verwijderen

Voer de volgende stappen uit om een bandbreedtesjabloon te verwijderen.

#### <a name="to-delete-a-bandwidth-template"></a>Een bandbreedtesjabloon verwijderen

1. Ga naar uw StorSimple Device Manager-service en klik op **Bandbreedtesjablonen**.
2. Selecteer in de lijst met bandbreedtesjablonen de sjabloon die u wilt verwijderen. Klik met de rechtermuisknop en selecteer Verwijderen in het contextmenu.
3. Klik op **OK**wanneer u om bevestiging wordt gevraagd. Hiermee moet de bandbreedtesjabloon worden verwijderd.
4. De lijst met bandbreedtesjablonen-updates om de verwijdering weer te geven.

Als de sjabloon door een volume(en) wordt gebruikt, mag u deze niet verwijderen. Er ziet u een foutbericht dat aangeeft dat de sjabloon in gebruik is. Er verschijnt een foutberichtdialoogvenster waarin wordt geadviseerd om alle verwijzingen naar de sjabloon te verwijderen.

U alle verwijzingen naar de sjabloon verwijderen door toegang te krijgen tot de pagina **Volumecontainers** en de volumecontainers te wijzigen die deze sjabloon gebruiken, zodat ze een andere sjabloon gebruiken of een aangepaste of onbeperkte bandbreedteinstelling gebruiken. Wanneer alle verwijzingen zijn verwijderd, u de sjabloon verwijderen.

## <a name="use-a-default-bandwidth-template"></a>Een standaardbandbreedtesjabloon gebruiken

Er wordt standaard bandbreedtesjabloon meegeleverd en standaard door volumecontainers gebruikt om bandbreedtebesturingselementen af te dwingen bij het openen van de cloud. De standaardsjabloon dient ook als een kant-en-klare referentie voor gebruikers die hun eigen sjablonen maken. De details van deze standaardsjabloon zijn:

* **Naam** – Onbeperkte nachten en weekenden
* **Schema** - Een enkel schema van maandag tot en met vrijdag dat een bandbreedtesnelheid van 1 Mbps van toepassing tussen 8 uur en 17:00 apparaat tijd. De bandbreedte is ingesteld op Unlimited voor de rest van de week.

De standaardsjabloon kan worden bewerkt. Het gebruik van deze sjabloon (inclusief bewerkte versies) wordt bijgehouden.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Een bandbreedtesjabloon maken die op een bepaald tijdstip begint

Volg deze procedure om een schema te maken dat op een bepaald tijdstip begint en de hele dag wordt uitgevoerd. In het voorbeeld begint het schema om 9 uur 's ochtends en loopt het tot 9 uur de volgende ochtend. Het is belangrijk op te merken dat de begin- en eindtijden voor een bepaald schema beide volgens hetzelfde 24-uursschema moeten zijn opgenomen en niet meerdere dagen kunnen duren. Als u bandbreedtesjablonen moet instellen die meerdere dagen duren, moet u meerdere schema's gebruiken (zoals in het voorbeeld wordt weergegeven).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Een bandbreedtesjabloon voor de hele dag maken

1. Maak een schema dat begint om 9 uur in de ochtend en loopt tot middernacht.
2. Voeg een ander schema toe. Configureer het tweede schema dat loopt van middernacht tot 9 uur 's ochtends.
3. Sla de bandbreedtesjabloon op.

Het samengestelde schema begint dan op een tijdstip naar keuze en wordt de hele dag uitgevoerd.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Vragen en antwoorden over bandbreedtesjablonen

**V**. Wat gebeurt er met bandbreedtebesturingselementen als u zich tussen de schema's bevindt? (Een schema is afgelopen en een andere is nog niet begonnen.)

**A.** In dergelijke gevallen worden geen bandbreedtecontroles gebruikt. Dit betekent dat het apparaat onbeperkte bandbreedte kan gebruiken bij het tieren van gegevens naar de cloud.

**V**. u bandbreedtesjablonen wijzigen op een offline apparaat?

**A.** U bandbreedtesjablonen voor volumescontainers niet wijzigen als het desbetreffende apparaat offline is.

**V**. u een bandbreedtesjabloon bewerken die is gekoppeld aan een volumecontainer wanneer de gekoppelde volumes offline zijn?

**A.** U een bandbreedtesjabloon wijzigen die is gekoppeld aan een volumecontainer waarvan de volumes offline zijn. Houd er rekening mee dat wanneer volumes offline zijn, er geen gegevens worden gelaagd van het apparaat naar de cloud.

**V**. u een standaardsjabloon verwijderen?

**A.** Hoewel u een standaardsjabloon verwijderen, is het geen goed idee om dit te doen. Het gebruik van een standaardsjabloon, inclusief bewerkte versies, wordt bijgehouden. De trackinggegevens worden geanalyseerd en worden in de loop van de tijd gebruikt om de standaardsjabloon te verbeteren.

**V**. Hoe bepaalt u dat uw bandbreedtesjablonen moeten worden gewijzigd?

**A.** Een van de tekenen dat u de bandbreedtesjablonen moet wijzigen, is wanneer u het netwerk meerdere keren per dag begint te vertragen of te stikken. Als dit gebeurt, bewaakt u het opslag- en gebruiksnetwerk door te kijken naar de i/o-prestatie- en netwerkdoorvoerdiagrammen.

Identificeer op basis van de netwerkdoorvoergegevens het tijdstip van de dag en de volumecontainers waarin het netwerkknelpunt optreedt. Als dit gebeurt wanneer gegevens worden gelaagd naar de cloud (haal deze informatie uit I/O-prestaties voor alle volumecontainers voor apparaat naar cloud), moet u de bandbreedtesjablonen wijzigen die zijn gekoppeld aan uw volumecontainers.

Nadat de gewijzigde sjablonen in gebruik zijn, moet u het netwerk opnieuw controleren op aanzienlijke latencies. Als deze nog bestaan, moet u uw bandbreedtesjablonen opnieuw bekijken.

**V**. Wat gebeurt er als meerdere volumecontainers op mijn apparaat schema's hebben die elkaar overlappen, maar er verschillende limieten op elk van hen gelden?

**A.** Laten we aannemen dat u een apparaat hebt met 3 volumecontainers. De schema's die aan deze containers zijn gekoppeld, overlappen elkaar volledig. Voor elk van deze containers zijn de gebruikte bandbreedtelimieten respectievelijk 5, 10 en 15 Mbps. Wanneer I/O op al deze containers tegelijkertijd plaatsvindt, kan het minimum van de 3 bandbreedtelimieten worden toegepast: in dit geval 5 Mbps omdat deze uitgaande I/O-verzoeken dezelfde wachtrij delen.

## <a name="best-practices-for-bandwidth-templates"></a>Aanbevolen procedures voor bandbreedtesjablonen

Volg deze aanbevolen procedures voor uw StorSimple-apparaat:

* Configureer bandbreedtesjablonen op uw apparaat om variabele beperking van de netwerkdoorvoer door het apparaat op verschillende tijdstippen van de dag mogelijk te maken. Deze bandbreedtesjablonen die worden gebruikt met back-upschema's kunnen tijdens daluren effectief gebruik maken van extra netwerkbandbreedte voor cloudbewerkingen.
* Bereken de werkelijke bandbreedte die nodig is voor een bepaalde implementatie op basis van de grootte van de implementatie en de vereiste hersteltijddoelstelling (RTO).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)


---
title: Volumes beheren op StorSimple Virtual Array | Microsoft Documenten
description: Beschrijft de StorSimple Device Manager en legt uit hoe u het gebruiken om volumes op uw StorSimple Virtual Array te beheren.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 2dbbe6bcd4957a108cc3eae4d41816b130cf8f07
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633869"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Gebruik de StorSimple-apparaatbeheerfunctie om volumes te beheren in de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt uitgelegd hoe u de StorSimple Device Manager-service gebruiken om volumes op uw StorSimple Virtual Array te maken en te beheren.

De StorSimple Device Manager-service is een extensie in de Azure-portal waarmee u uw StorSimple-oplossing vanuit één webinterface beheren. Naast het beheren van aandelen en volumes, u de StorSimple Device Manager-service gebruiken om apparaten te bekijken en te beheren, waarschuwingen te bekijken en back-upbeleid en de back-upcatalogus te bekijken en te beheren.

## <a name="volume-types"></a>Volumetypen

StorSimple volumes kunnen zijn:

* **Lokaal vastgemaakt:** gegevens in deze volumes blijven te allen tijde op de array staan en worden niet naar de cloud gelekt.
* **Gelaagd:** Gegevens in deze volumes kunnen naar de cloud worden gelekt. Wanneer u een gelaagd volume maakt, wordt ongeveer 10 % van de ruimte op de lokale laag ingericht en wordt 90 % van de ruimte in de cloud ingericht. Als u bijvoorbeeld een volume van 1 TB hebt ingericht, bevindt 100 GB zich in de lokale ruimte en wordt 900 GB in de cloud gebruikt wanneer de gegevenslagen worden gebruikt. Dit houdt op zijn beurt in dat als u geen ruimte meer hebt op het apparaat, u geen gelaagd volume inrichten (omdat de vereiste 10 % op de lokale laag niet beschikbaar is).

### <a name="provisioned-capacity"></a>Voorzieningen
Raadpleeg de volgende tabel voor de maximale ingerichte capaciteit voor elk volumetype.

| **Limiet-id**                                       | **Limiet**     |
|------------------------------------------------------------|---------------|
| Minimumgrootte van een gelaagd volume                            | 500 GB        |
| Maximale grootte van een gelaagd volume                            | 5 TB          |
| Minimale grootte van een lokaal vastgemaakt volume                    | 50 GB         |
| Maximale grootte van een lokaal vastgemaakt volume                    | 200 GB        |

## <a name="the-volumes-blade"></a>Het volumesblad
In het menu **Volumes** op uw StorSimple-serviceoverzichtsblad wordt de lijst met opslagvolumes op een bepaalde StorSimple-array weergegeven en u deze beheren.

![Volumes blad](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Een volume bestaat uit een reeks attributen:

* **Volumenaam** – Een beschrijvende naam die uniek moet zijn en helpt bij het identificeren van het volume.
* **Status** – Kan online of offline zijn. Als een volume offline is, is het niet zichtbaar voor initiatiefnemers (servers) die toegang hebben tot het volume.
* **Tekst:** geeft aan of het volume **is getierd** (de standaardinstelling) of **Lokaal vastgemaakt**.
* **Capaciteit** – geeft de hoeveelheid gebruikte gegevens op in vergelijking met de totale hoeveelheid gegevens die door de initiator (server) kan worden opgeslagen.
* **Back-up** - In het geval van de StorSimple Virtual Array worden alle volumes automatisch ingeschakeld voor back-up.
* **Verbonden hosts** : hiermee geeft u de initiators (servers) op die toegang hebben tot dit volume.

![Details van de volumes](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Gebruik de instructies in deze zelfstudie om de volgende taken uit te voeren:

* Een volume toevoegen
* Een volume wijzigen
* Een volume offline halen
* Een volume verwijderen

## <a name="add-a-volume"></a>Een volume toevoegen

1. Klik in het overzichtsblad van StorSimple op **+ Volume toevoegen** vanaf de opdrachtbalk. Hiermee opent u het **volumeblad toevoegen.**
   
    ![Volume toevoegen](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Ga als volgt te werk in het **volumeblad Toevoegen:**
   
   * Voer in het veld **Volumenaam** een unieke naam voor uw volume in. De naam moet een tekenreeks zijn die 3 tot 127 tekens bevat.
   * Geef in de vervolgkeuzelijst **Type** op of u een **trapsgeweneerd** of **lokaal vastgemaakt** volume wilt maken. Selecteer **Lokaal vastgemaakt volume**voor workloads waarvoor lokale garanties, lage latencies en hogere prestaties nodig zijn. Selecteer **Laagvolume** voor alle andere gegevens.
   * Geef **in** het veld Capaciteit de grootte van het volume op. Een gelaagd volume moet tussen 500 GB en 5 TB liggen en een lokaal vastgemaakt volume moet tussen 50 GB en 500 GB liggen.
   * * Klik **op Verbonden hosts,** selecteer een access control record (ACR) die overeenkomt met de iSCSI-initiator die u met dit volume wilt verbinden en klik vervolgens op **Selecteren**.
3. Als u een nieuwe verbonden host wilt toevoegen, klikt u op **Nieuw toevoegen,** voert u een naam in voor de host en de iSCSI-gekwalificeerde naam (IQN) en klikt u vervolgens op **Toevoegen**.
   
    ![Volume toevoegen](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Wanneer u klaar bent met het configureren van uw volume, klikt u op **Maken**. Er wordt een volume gemaakt met de opgegeven instellingen en u ziet een melding over de succesvolle creatie van hetzelfde. Standaard wordt back-up ingeschakeld voor het volume.
5. Ga naar het blad **Volumes** om te controleren of het volume is gemaakt. U ziet het vermelde volume.
   
    ![Volume zorgt voor succes](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Een volume wijzigen

Wijzig een volume wanneer u de hosts moet wijzigen die toegang hebben tot het volume. De andere kenmerken van een volume kunnen niet worden gewijzigd nadat het volume is gemaakt.

#### <a name="to-modify-a-volume"></a>Een volume wijzigen

1. Selecteer in de instelling **Volumes** op het storSimple-serviceoverzichtsblad de virtuele array waarop het volume dat u wilt wijzigen, zich bevindt.
2. **Selecteer** het volume en klik op **Verbonden hosts** om de momenteel verbonden host weer te geven en deze te wijzigen naar een andere server.
   
    ![Volume bewerken](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Sla de wijzigingen op door op de opdrachtbalk **opslaan** te klikken. Uw opgegeven instellingen worden toegepast en u ziet een melding.

## <a name="take-a-volume-offline"></a>Een volume offline halen

Mogelijk moet u een volume offline halen wanneer u van plan bent het volume te wijzigen of te verwijderen. Wanneer een volume offline is, is het niet beschikbaar voor lees-schrijftoegang. U moet het volume offline halen op de host en op het apparaat.

#### <a name="to-take-a-volume-offline"></a>Een volume offline halen

1. Zorg ervoor dat het volume in kwestie niet in gebruik is voordat u het offline haalt.
2. Haal eerst het volume offline op de host. Dit elimineert elk potentieel risico van gegevensbeschadiging op het volume. Raadpleeg voor specifieke stappen de instructies voor uw hostbesturingssysteem.
3. Nadat het volume op de host offline is, neemt u het volume op de array offline door de volgende stappen uit te voeren:
   
   * Selecteer **in** de instelling Volumes op het storSimple-serviceoverzichtsblad de virtuele array waarop het volume dat u offline wilt halen, zich bevindt.
   * **Selecteer** het volume en klik **op ...** (klik afwisselend met de rechtermuisknop in deze rij) en selecteer **offline nemen**in het contextmenu.
     
        ![Offline volume](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Bekijk de informatie in het offline blad **verwijderen** en bevestig uw acceptatie van de bewerking. Klik **op Offline halen** om het volume offline te halen. U ziet een melding van de lopende bewerking.
   * Ga naar het blad **Volumes** om te controleren of het volume offline is gehaald. U moet de status van het volume als offline zien.
     
       ![Offline volumebevestiging](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Een volume verwijderen

> [!IMPORTANT]
> U een volume alleen verwijderen als het offline is.
> 
> 

Voer de volgende stappen uit om een volume te verwijderen.

#### <a name="to-delete-a-volume"></a>Een volume verwijderen

1. Selecteer in de instelling **Volumes** op het recordblad van de StorSimple-serviceoverzicht de virtuele array waarop het volume dat u wilt verwijderen, zich bevindt.
2. **Selecteer** het volume en klik **op ...** (klik afwisselend met de rechtermuisknop in deze rij) en selecteer Verwijderen in het contextmenu **.**
   
    ![Volume verwijderen](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Controleer de status van het volume dat u wilt verwijderen. Als het volume dat u wilt verwijderen niet offline is, neemt u het eerst offline, volg de stappen in [Een volume offline uitvoeren.](#take-a-volume-offline)
4. Accepteer de bevestiging en klik op **Verwijderen**wanneer u om bevestiging wordt gevraagd in het blad **Verwijderen.** Het volume wordt nu verwijderd en het **blade Volumes** toont de bijgewerkte lijst met volumes binnen de virtuele array.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [klonen van een StorSimple-volume.](storsimple-virtual-array-clone.md)


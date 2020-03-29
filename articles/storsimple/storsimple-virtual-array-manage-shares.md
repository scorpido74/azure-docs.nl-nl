---
title: StorSimple Virtual Array-aandelen beheren | Microsoft Documenten
description: Beschrijft de StorSimple Device Manager en legt uit hoe u deze gebruiken om aandelen op uw StorSimple Virtual Array te beheren.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 82a6cdb6c9a39a0d196049a7ba662681ea06b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62116863"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Gebruik de StorSimple-apparaatbeheerfunctie om shares te beheren in de virtuele StorSimple-matrix

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt uitgelegd hoe u de StorSimple Device Manager-service gebruiken om shares op uw StorSimple Virtual Array te maken en te beheren.

De StorSimple Device Manager-service is een extensie in de Azure-portal waarmee u uw StorSimple-oplossing vanuit één webinterface beheren. Naast het beheren van aandelen en volumes, u de StorSimple Device Manager-service gebruiken om apparaten te bekijken en te beheren, waarschuwingen te bekijken, back-upbeleid te beheren en de back-upcatalogus te beheren.

## <a name="share-types"></a>Typen delen

StorSimple aandelen kunnen zijn:

* **Lokaal vastgemaakt**: Gegevens in deze shares blijven te allen tijde op de array staan en worden niet naar de cloud gelekt.
* **Gelaagd**: Gegevens in deze aandelen kunnen naar de cloud worden gelekt. Wanneer u een gedifferentieerd aandeel maakt, wordt ongeveer 10 % van de ruimte op de lokale laag ingericht en wordt 90 % van de ruimte in de cloud ingericht. Als u bijvoorbeeld een aandeel van 1 TB hebt ingericht, bevindt 100 GB zich in de lokale ruimte en wordt 900 GB in de cloud gebruikt wanneer de gegevenslagen worden gebruikt. Dit houdt op zijn beurt in dat als u geen ruimte meer hebt op het apparaat, u geen gedifferentieerd aandeel inrichten (omdat de vereiste 10 % op de lokale laag niet beschikbaar is).

### <a name="provisioned-capacity"></a>Voorzieningen

Raadpleeg de volgende tabel voor de maximale ingerichte capaciteit voor elk aandelentype.

| **Limiet-id** | **Limiet** |
| --- | --- |
| Minimale grootte van een gedifferentieerd aandeel |500 GB |
| Maximale grootte van een gelaagd aandeel |20 TB |
| Minimale grootte van een lokaal vastgemaakt aandeel |50 GB |
| Maximale grootte van een lokaal vastgemaakt aandeel |2 TB |

## <a name="the-shares-blade"></a>Het aandelenblad

In het menu **Aandelen** op uw StorSimple-serviceoverzichtsblad wordt de lijst met opslagshares op een bepaalde StorSimple-array weergegeven en u deze beheren.

![Aandelen blad](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Een aandeel bestaat uit een reeks attributen:

* **Naam delen** – Een beschrijvende naam die uniek moet zijn en helpt bij het identificeren van het aandeel.
* **Status** – Kan online of offline zijn. Als een share offline is, hebben gebruikers van het aandeel er geen toegang toe.
* **Tekst:** geeft aan of het aandeel **is getierd** (de standaardinstelling) of **Lokaal vastgemaakt**.
* **Capaciteit** : hiermee geeft u de hoeveelheid gegevens op die wordt gebruikt in vergelijking met de totale hoeveelheid gegevens die op het aandeel kan worden opgeslagen.
* **Beschrijving** : een optionele instelling waarmee u het aandeel beschrijven.
* **Machtigingen** : de NTFS-machtigingen voor het aandeel dat kan worden beheerd via Windows Explorer.
* **Back-up** - In het geval van de StorSimple Virtual Array worden alle shares automatisch ingeschakeld voor back-up.

![Deelt details](./media/storsimple-virtual-array-manage-shares/share-details.png)

Gebruik de instructies in deze zelfstudie om de volgende taken uit te voeren:

* Een share toevoegen
* Een aandeel wijzigen
* Een aandeel offline nemen
* Een share verwijderen

## <a name="add-a-share"></a>Een share toevoegen

1. Klik in het overzichtsblad van StorSimple op **+ Delen toevoegen** vanaf de opdrachtbalk. Dit opent het **aandeel toevoegen.**

    ![Aandeel toevoegen](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Ga als volgt te werk in het **mes van** delen toevoegen:
   
   1. Voer in het veld **Naam delen** een unieke naam voor uw aandeel in. De naam moet een tekenreeks zijn die 3 tot 127 tekens bevat.

   2. Een optionele **beschrijving** voor het aandeel. De beschrijving helpt bij het identificeren van de eigenaren van aandelen.

   3. Geef in de vervolgkeuzelijst **Type** op of u een **gedeeld met een niveau** of lokaal **vastgemaakt** aandeel wilt maken. Selecteer **Lokaal vastgemaakt delen**voor workloads waarvoor lokale garanties, lage latencies en hogere prestaties nodig zijn. Selecteer **Trapsgewijs** delen voor alle andere gegevens.

   4. Geef **in** het veld Capaciteit de grootte van het aandeel op. Een gedifferentieerd aandeel moet tussen 500 GB en 20 TB liggen en een lokaal vastgemaakt aandeel moet tussen de 50 GB en 2 TB liggen.

   5. Wijs in het veld **Standaardmachtigingen voor volledige machtigingen instellen** toe aan de gebruiker of de groep die toegang heeft tot dit aandeel. Geef de naam van de gebruiker _john@contoso.com_ of de gebruikersgroep op in indeling. We raden u aan een gebruikersgroep (in plaats van één gebruiker) te gebruiken om beheerdersrechten toe te staan toegang te krijgen tot deze shares. Als u de bevoegdheden hier hebt toegewezen, kunt u Verkenner gebruiken om de bevoegdheden te wijzigen.
3. Wanneer u klaar bent met het configureren van uw aandeel, klikt u op **Maken**. Er wordt een deel gemaakt met de opgegeven instellingen en u ziet een melding. Standaard wordt back-up ingeschakeld voor het aandeel.
4. Ga naar het blad **Aandelen** om te controleren of het aandeel is gemaakt. U ziet het aandeel dat wordt vermeld.
   
    ![Delen creëren succes](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Een aandeel wijzigen

Wijzig een aandeel wanneer u de beschrijving van het aandeel moet wijzigen. Geen andere eigenschappen van delen kunnen worden gewijzigd zodra het aandeel is gemaakt.

#### <a name="to-modify-a-share"></a>Een aandeel wijzigen

1. Selecteer in de instelling **Aandelen** op het overzichtsblad van de StorSimple-serviceoverzicht de virtuele array waarop het aandeel dat u wilt wijzigen, zich bevindt.
2. **Selecteer** het aandeel om de huidige beschrijving weer te geven en deze te wijzigen.
3. Sla de wijzigingen op door op de opdrachtbalk **opslaan** te klikken. Uw opgegeven instellingen worden toegepast en u ziet een melding.
   
    ![ Delen bewerken](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Een aandeel offline nemen

Mogelijk moet u een aandeel offline halen wanneer u van plan bent deze te wijzigen of te verwijderen. Wanneer een aandeel offline is, is het niet beschikbaar voor lees-schrijftoegang. Je moet het aandeel offline halen op de host en op het apparaat.

#### <a name="to-take-a-share-offline"></a>Een aandeel offline nemen

1. Zorg ervoor dat het aandeel in kwestie niet in gebruik is voordat u het offline haalt.
2. Neem het aandeel op de array offline door de volgende stappen uit te voeren:
   
    1. Selecteer in de instelling **Aandelen** op het overzichtsblad van de StorSimple-servicedes de virtuele array waarop het aandeel waarop u offline wilt nemen, zich bevindt.

    2. **Selecteer** de share en Klik **...** (klik afwisselend met de rechtermuisknop in deze rij) en selecteer **offline nemen**in het contextmenu.
     
        ![Offline delen](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Bekijk de informatie in het offline blad **verwijderen** en bevestig uw acceptatie van de bewerking. Klik **op Offline halen** om het aandeel offline te halen. U ziet een melding van de lopende bewerking.

    4. Ga naar het blad **Aandelen** om te controleren of het aandeel met succes offline is gehaald. U ziet de status van het aandeel als offline.

## <a name="delete-a-share"></a>Een share verwijderen

> [!IMPORTANT]
> U een aandeel alleen verwijderen als deze offline is.


Voer de volgende stappen uit om een aandeel te verwijderen.

#### <a name="to-delete-a-share"></a>Een aandeel verwijderen

1. Selecteer in de instelling **Aandelen** op het overzichtsblad van de StorSimple-serviceoverzicht de virtuele array waarop het aandeel dat u wilt verwijderen zich bevindt.
2. **Selecteer** de share en Klik **op ...** (klik afwisselend met de rechtermuisknop in deze rij) en selecteer Verwijderen in het contextmenu **.**
   
    ![Aandeel verwijderen](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Controleer de status van het aandeel dat u wilt verwijderen. Als het aandeel dat u wilt verwijderen niet offline is, neemt u het eerst offline. Volg de stappen in [Een aandeel offline uitvoeren](#take-a-share-offline).
4. Accepteer de bevestiging en klik op **Verwijderen**wanneer u om bevestiging wordt gevraagd in het blad **Verwijderen.** Het aandeel wordt nu verwijderd en het **aandelenblad** toont de bijgewerkte lijst met aandelen binnen de virtuele array.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [klonen van een StorSimple-aandeel.](storsimple-virtual-array-clone.md)


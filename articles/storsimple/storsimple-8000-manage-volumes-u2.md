---
title: StorSimple-volumes beheren (update 3)
description: Hiermee wordt uitgelegd hoe u StorSimple-volumes toevoegt, wijzigt, controleert en verwijdert en hoe u deze indien nodig offline halen.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f32f8925bca33d90afa48071d0c0944ba63861cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254766"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>De StorSimple Device Manager-service gebruiken om volumes te beheren (update 3 of hoger)

## <a name="overview"></a>Overzicht

In deze zelfstudie wordt uitgelegd hoe u de StorSimple Device Manager-service gebruiken om volumes te maken en te beheren op de apparaten uit de StorSimple 8000-serie met Update 3 en hoger.

De StorSimple Device Manager-service is een extensie in de Azure-portal waarmee u uw StorSimple-oplossing vanuit één webinterface beheren. Gebruik de Azure-portal om volumes op al uw apparaten te beheren. U ook StorSimple-services maken en beheren, apparaten, back-upbeleid en back-upcatalogus beheren en waarschuwingen weergeven.

## <a name="volume-types"></a>Volumetypen

StorSimple volumes kunnen zijn:

* **Lokaal vastgemaakte volumes**: Gegevens in deze volumes blijven te allen tijde op het lokale StorSimple-apparaat staan.
* **Gelaagde volumes**: Gegevens in deze volumes kunnen naar de cloud worden gelekt.

Een archiefvolume is een type gelaagd volume. De grotere deduplicatie brok grootte die wordt gebruikt voor archiveringsvolumes kan het apparaat om grotere segmenten van gegevens over te dragen aan de cloud.

Indien nodig u het volumetype wijzigen van lokaal naar trapsgewijze of van gelaagd naar lokaal. Ga voor meer informatie naar [Het volumetype wijzigen.](#change-the-volume-type)

### <a name="locally-pinned-volumes"></a>Lokaal vastgemaakte volumes

Lokaal vastgemaakte volumes zijn volledig ingerichte volumes die geen gegevens naar de cloud brengen, waardoor lokale garanties voor primaire gegevens worden gegarandeerd, onafhankelijk van cloudconnectiviteit. Gegevens over lokaal vastgemaakte volumes worden niet gededupliceerd en gecomprimeerd; Momentopnamen van lokaal vastgemaakte volumes worden echter gedupliceerd. 

Lokaal vastgemaakte volumes zijn volledig ingericht; Daarom moet u voldoende ruimte op uw apparaat hebben wanneer u ze maakt. U lokaal vastgemaakte volumes tot een maximale grootte van 8 TB inrichten op het StorSimple 8100-apparaat en 20 TB op het 8600-apparaat. StorSimple reserveert de resterende lokale ruimte op het apparaat voor momentopnamen, metagegevens en gegevensverwerking. U de grootte van een lokaal vastgemaakt volume vergroten tot de maximale beschikbare ruimte, maar u de grootte van een volume dat eenmaal is gemaakt niet verkleinen.

Wanneer u een lokaal vastgemaakt volume maakt, wordt de beschikbare ruimte voor het maken van gelaagde volumes verkleind. Het omgekeerde is ook waar: als u bestaande gelaagde volumes hebt, is de beschikbare ruimte voor het maken van lokaal vastgemaakte volumes lager dan de hierboven vermelde maximumlimieten. Raadpleeg voor meer informatie over lokale volumes de [veelgestelde vragen over lokaal vastgemaakte volumes.](storsimple-8000-local-volume-faq.md)

### <a name="tiered-volumes"></a>Gelaagd volumes

Gelaagde volumes zijn dun ingerichte volumes waarin de vaak geopende gegevens lokaal op het apparaat blijven en minder vaak gebruikte gegevens automatisch naar de cloud worden gegelaagdeerd. Dunne inrichting is een virtualisatietechnologie waarbij de beschikbare opslag de fysieke resources lijkt te overschrijden. In plaats van vooraf voldoende opslagruimte te reserveren, gebruikt StorSimple dunne provisioning om net genoeg ruimte toe te wijzen om aan de huidige vereisten te voldoen. Het elastische karakter van cloudopslag vergemakkelijkt deze aanpak omdat StorSimple de cloudopslag kan verhogen of verlagen om aan veranderende eisen te voldoen.

Als u het gelaagde volume gebruikt voor archiefgegevens, schakelt u het selectievakje **Dit volume gebruiken voor minder vaak geopende archiefgegevens** in om de grootte van de duplicatievan den duplicatie voor uw volume te wijzigen in 512 KB. Als u deze optie niet selecteert, wordt met het bijbehorende gelaagde volume een segmentgrootte van 64 KB gebruikt. Een grotere chunkgrootte voor de ontdubbeling zorgt ervoor dat het apparaat sneller grotere archiveringsgegevens naar de cloud kan overzetten.


### <a name="provisioned-capacity"></a>Voorzieningen

Raadpleeg de volgende tabel voor de maximale ingerichte capaciteit voor elk apparaat en volumetype. (Houd er rekening mee dat lokaal vastgemaakte volumes niet beschikbaar zijn op een virtueel apparaat.)

|  | Maximale gelaagde volumegrootte | Maximale lokaal vastgemaakte volumegrootte |
| --- | --- | --- |
| **Fysieke apparaten** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuele apparaten** | | |
| 8010 |30 TB |N.v.t. |
| 8020 |64 TB |N.v.t. |

## <a name="the-volumes-blade"></a>Het volumesblad

Met het blade **Volumes** u de opslagvolumes beheren die zijn ingericht op het Microsoft Azure StorSimple-apparaat voor uw initiators (servers). Het toont de lijst met volumes op de StorSimple-apparaten die zijn aangesloten op uw service.

 ![Pagina Volumes](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Een volume bestaat uit een reeks attributen:

* **Volumenaam** – Een beschrijvende naam die uniek moet zijn en helpt bij het identificeren van het volume. Deze naam wordt ook gebruikt in bewakingsrapporten wanneer u filtert op een bepaald volume. Zodra het volume is gemaakt, kan de naam niet worden gewijzigd.
* **Status** – Kan online of offline zijn. Als een volume offline is, is het niet zichtbaar voor initiatiefnemers (servers) die toegang hebben tot het volume.
* **Capaciteit** – geeft de totale hoeveelheid gegevens op die door de initiator (server) kan worden opgeslagen. Lokaal vastgemaakte volumes zijn volledig ingericht en bevinden zich op het StorSimple-apparaat. Gelaagde volumes zijn dun ingericht en de gegevens worden gededupliceerd. Met dun ingerichte volumes wijst uw apparaat fysieke opslagcapaciteit intern of in de cloud niet vooraf toe op basis van geconfigureerde volumecapaciteit. De volumecapaciteit wordt toegewezen en verbruikt op aanvraag.
* **Tekst:** geeft aan of het volume **is getierd** (de standaardinstelling) of **Lokaal vastgemaakt**.

Gebruik de instructies in deze zelfstudie om de volgende taken uit te voeren:

* Een volume toevoegen 
* Een volume wijzigen 
* Het volumetype wijzigen
* Een volume verwijderen 
* Een volume offline halen 
* Een volume controleren 

## <a name="add-a-volume"></a>Een volume toevoegen

U [hebt een volume gemaakt](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume) tijdens de implementatie van uw StorSimple 8000-serieapparaat. Het toevoegen van een volume is een vergelijkbare procedure.

#### <a name="to-add-a-volume"></a>Een volume toevoegen

1. Selecteer en klik op uw apparaat in de lijst in tabelvorm met apparaten op de blade **Apparaten**. Klik op **Volume toevoegen**.

    ![Een nieuw volume toevoegen](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. Doe het volgende op de blade **Volume**:
   
    1. In het veld **Apparaat selecteren** wordt automatisch uw huidige apparaat ingevuld.

    2. Selecteer in de vervolgkeuzelijst de volumecontainer waaraan u een volume wilt toevoegen.

    3.  een **naam** voor het volume. Zodra het volume is gemaakt, u de naam van het volume niet wijzigen.

    4. Selecteer in de vervolgkeuzelijst het **Type** voor het volume. Voor workloads waarvoor lokale garanties, lage latenties en betere prestaties vereist zijn, selecteert u een **lokaal vastgemaakt** volume. Voor alle overige gegevens selecteert u een **gelaagd** volume. Als u dit volume gebruikt voor de archivering van gegevens, schakelt u **Dit volume gebruiken voor minder frequent gebruikte gearchiveerde gegevens** in.
      
       Een gelaagd volume is dun ingericht (thin provisioning) en kan zeer snel worden gemaakt. Als u voor een gelaagd volume dat is bedoeld voor gegevens archivering de optie **Dit volume gebruiken voor minder vaak gebruikte gegevensarchivering** selecteert, verandert de chunkgrootte voor de ontdubbeling voor het volume in 512 KB. Als dit veld niet is ingeschakeld, gebruikt het betreffende gelaagde volume een chunkgrootte van 64 KB. Een grotere chunkgrootte voor de ontdubbeling zorgt ervoor dat het apparaat sneller grotere archiveringsgegevens naar de cloud kan overzetten.
       
       Een lokaal vastgemaakt volume is compact ingericht en zorgt ervoor dat de primaire gegevens op het volume lokaal op het apparaat blijven en niet naar de cloud worden gelekt.  Als u een lokaal vastgemaakt volume maakt, controleert het apparaat de beschikbare ruimte op de lokale lagen om ervoor te zorgen dat het volume het juiste formaat heeft. Het maken van een lokaal vastgemaakt volume heeft als risico dat bestaande gegevens van het apparaat naar de cloud worden gelekt. Ook duurt het maken van het volume mogelijk lang. De totale tijd is afhankelijk van de grootte van het betreffende volume, de beschikbare netwerkbandbreedte en de gegevens op uw apparaat.

    5. Geef de **ingerichte capaciteit** voor het volume op. Noteer de beschikbare capaciteit op basis van het volumetype dat is geselecteerd. De grootte van het opgegeven volume mag niet groter zijn dan de beschikbare ruimte.
      
       U kunt lokaal vastgemaakte volumes inrichten tot maximaal 8,5 TB, of gelaagde volumes op het 8100-apparaat tot 200 TB. Op een groter 8600-apparaat kunt u lokaal vastgemaakte volumes inrichten tot 22,5 TB en gelaagde volumes tot maximaal 500 TB. Aangezien lokale ruimte op het apparaat is vereist om de werkset van gelaagde volumes te hosten, heeft het maken van lokaal vastgemaakte volumes invloed op de beschikbare schijfruimte voor het inrichten van gelaagde volumes. Als u dus een lokaal vastgemaakt volume maakt, wordt de beschikbare schijfruimte voor het maken van gelaagde volumes verminderd. En andersom: als een gelaagd volume wordt gemaakt, wordt de beschikbare ruimte voor het maken van lokaal vastgemaakte volumes verminderd.
      
       Als u een lokaal vastgemaakt volume van 8,5 TB (maximaal toegestane grootte) op uw 8100-apparaat inricht, hebt u de lokale ruimte die beschikbaar is op het apparaat volledig gebruikt. Vanaf dat moment kunt u geen gelaagd volume maken, omdat er geen lokale ruimte op het apparaat is voor het hosten van de werkset van het gelaagde volume. Bestaande gelaagde volumes zijn ook van invloed op de beschikbare ruimte. Als u bijvoorbeeld een 8100-apparaat hebt met reeds gelaagde volumes van circa 106 TB, is er nog maar 4 TB ruimte beschikbaar voor lokaal vastgemaakte volumes.

    6. Klik in het veld **Verbonden hosts** op de pijl. Kies in het **blade van Verbonden hosts** een bestaande ACR of voeg een nieuwe ACR toe. Als u een nieuwe ACR kiest en vervolgens een **naam** voor uw ACR levert, geeft u de **iSCSI-gekwalificeerde naam** (IQN) van uw Windows-host op. Als u het IQN niet hebt, gaat u naar Het IQN van een Windows Server-host ophalen. Klik **op Maken**. Er wordt een volume gemaakt met de opgegeven instellingen.

        ![Klik op Maken](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Uw nieuwe volume is nu klaar voor gebruik.

> [!NOTE]
> Als u een lokaal vastgemaakt volume maakt en vervolgens onmiddellijk daarna een ander lokaal vastgemaakt volume maakt, worden de taken voor het maken van volume opeenvolgend uitgevoerd. De eerste taak voor het maken van volume moet worden voltooid voordat de volgende taak voor het maken van volume kan beginnen.

## <a name="modify-a-volume"></a>Een volume wijzigen

Wijzig een volume wanneer u het moet uitbreiden of de hosts die toegang hebben tot het volume moet wijzigen.

> [!IMPORTANT]
> * Als u de volumegrootte op het apparaat wijzigt, moet de volumegrootte ook op de host worden gewijzigd.
> * De hier beschreven stappen aan de hostzijde zijn voor Windows Server 2012 (2012R2). De procedures voor Linux of andere hostbesturingssystemen zullen anders zijn. Raadpleeg de instructies van uw hostbesturingssysteem wanneer u het volume wijzigt op een host waarop een ander besturingssysteem wordt uitgevoerd.

#### <a name="to-modify-a-volume"></a>Een volume wijzigen

1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de tabeltabelvermelding van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik **op Instellingen > Volumes**.

    ![Ga naar Volumes blade](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Selecteer in de tabeltabellijst met volumes het volume en klik met de rechtermuisknop om het contextmenu aan te roepen. Selecteer **Offline gaan** om het volume dat u offline wijzigt, offline te halen.

    ![Volume offline selecteren en offline halen](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Bekijk in het **offlineblad nemen** de impact van het offline halen van het volume en schakel het bijbehorende selectievakje in. Zorg ervoor dat het bijbehorende volume op de host eerst offline is. Raadpleeg de specifieke instructies van het besturingssysteem voor informatie over het offline halen van een volume op uw hostserver die is verbonden met StorSimple. Klik **op Offline halen**.

    ![Bekijk de impact van het offline halen van volume](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Nadat het volume offline is (zoals blijkt uit de volumestatus), selecteert u het volume en klikt u met de rechtermuisknop om het contextmenu aan te roepen. Selecteer **Volume wijzigen**.

    ![Volume wijzigen selecteren](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. In het **volumeblad wijzigen** u de volgende wijzigingen aanbrengen:
   
   1. De **volumenaam** kan niet worden bewerkt.
   2. **Converteer** het type van lokaal vastgemaakt naar gelaagd of van gelaagd naar lokaal vastgemaakt (zie [Het volumetype wijzigen](#change-the-volume-type) voor meer informatie).
   3. Verhoging van de **ingerichte capaciteit**. De **ingerichte capaciteit** kan alleen worden verhoogd. U een volume niet krimpen nadat het is gemaakt.
   4. Onder **Verbonden hosts**u de ACR wijzigen. Als u een ACR wilt wijzigen, moet het volume offline zijn.

       ![Bekijk de impact van het offline halen van volume](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Klik **op Opslaan** om de wijzigingen op te slaan. Klik op **Ja** als u om bevestiging wordt gevraagd. In de Azure-portal wordt een volumebericht bijgewerkt. Het wordt een succesbericht weergegeven wanneer het volume is bijgewerkt.

    ![Bekijk de impact van het offline halen van volume](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Als u een volume uitbreidt, voert u de volgende stappen uit op uw Windows-hostcomputer:
   
   1. Ga naar **Computermanagement** ->**schijfbeheer**.
   2. Klik met de rechtermuisknop op **Schijfbeheer** en selecteer **Schijven opnieuw scannen**.
   3. Selecteer in de lijst met schijven het volume dat u hebt bijgewerkt en klik met de rechtermuisknop op **Volume uitbreiden**. De wizard Volume uitbreiden wordt gestart. Klik op **Volgende**.
   4. Voltooi de wizard en accepteer de standaardwaarden. Nadat de wizard is voltooid, moet het volume de grotere grootte weergeven.
      
      > [!NOTE]
      > Als u een lokaal vastgemaakt volume uitvouwt en vervolgens onmiddellijk daarna een ander lokaal vastgemaakt volume uitvouwt, worden de volumeuitbreidingstaken achtereenvolgens uitgevoerd. De eerste volumeuitbreidingstaak moet worden voltooid voordat de volgende volumeuitbreidingstaak kan beginnen.
      

## <a name="change-the-volume-type"></a>Het volumetype wijzigen

U het volumetype wijzigen van gelaagd naar lokaal vastgemaakt of van lokaal vastgemaakt naar gelaagd. Deze conversie mag echter niet vaak voorkomen.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Gelaagd naar overwegingen voor lokale volumeconversie

Enkele redenen om een volume om te zetten van trapsgewijs naar lokaal vastgemaakt zijn:

* Lokale garanties met betrekking tot de beschikbaarheid en prestaties van gegevens
* Eliminatie van cloudlatencies en cloudconnectiviteitsproblemen.

Dit zijn meestal kleine bestaande volumes die u vaak wilt openen. Een lokaal vastgemaakt volume is volledig ingericht wanneer het wordt gemaakt. 

Als u een gelaagd volume converteert naar een lokaal vastgemaakt volume, controleert StorSimple of u voldoende ruimte op uw apparaat hebt voordat de conversie wordt gestart. Als u onvoldoende ruimte hebt, ontvangt u een foutmelding en wordt de bewerking geannuleerd. 

> [!NOTE]
> Voordat u begint met een conversie van gelaagd naar lokaal vastgemaakt, moet u rekening houden met de ruimtevereisten van uw andere workloads. 

Conversie van een gelaagd naar een lokaal vastgemaakt volume kan de prestaties van het apparaat negatief beïnvloeden. Bovendien kunnen de volgende factoren de tijd die nodig is om de conversie te voltooien, verhogen:

* Er is onvoldoende bandbreedte.
* Er is geen huidige back-up.

Ga als belangrijkste voor het minimaliseren van de effecten die deze factoren kunnen hebben:

* Bekijk uw bandbreedtebeperkingsbeleid en zorg ervoor dat er een specifieke bandbreedte van 40 Mbps beschikbaar is.
* Plan de conversie voor daluren.
* Maak een cloudmomentopname voordat u de conversie start.

Als u meerdere volumes converteert (ondersteuning voor verschillende workloads), moet u prioriteit geven aan de volumeconversie, zodat volumes met een hogere prioriteit eerst worden geconverteerd. U moet bijvoorbeeld volumes converteren die virtuele machines (VM's) of volumes hosten met SQL-workloads voordat u volumes converteert met bestandsshare-workloads.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Overwegingen voor lokale en gedifferentieerde volumeconversie

U een lokaal vastgemaakt volume wijzigen in een gelaagd volume als u extra ruimte nodig hebt om andere volumes in te richten. Wanneer u het lokaal vastgemaakte volume omzet in gelaagde, wordt de beschikbare capaciteit op het apparaat groter met de grootte van de vrijgegeven capaciteit. Als verbindingsproblemen de conversie van een volume van het lokale type naar het gelaagde type verhinderen, worden in het lokale volume eigenschappen van een gelaagd volume weergegeven totdat de conversie is voltooid. Dit komt omdat sommige gegevens mogelijk naar de cloud zijn gelekt. Deze gelekte gegevens blijven lokale ruimte op het apparaat innemen die niet kan worden vrijgegeven totdat de bewerking opnieuw is gestart en voltooid.

> [!NOTE]
> Het converteren van een volume kan enige tijd duren en u een conversie niet annuleren nadat deze is gestart. Het volume blijft online tijdens de conversie en u back-ups maken, maar u het volume niet uitbreiden of herstellen terwijl de conversie plaatsvindt.


#### <a name="to-change-the-volume-type"></a>Het volumetype wijzigen

1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de tabeltabelvermelding van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik **op Instellingen > Volumes**.

    ![Ga naar Volumes blade](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Selecteer in de tabeltabellijst met volumes het volume en klik met de rechtermuisknop om het contextmenu aan te roepen. Selecteer **Wijzigen**.

    ![Wijzigen selecteren in contextmenu](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Wijzig op het **volumeblad wijzigen** het volumetype door het nieuwe type te selecteren in de vervolgkeuzelijst **Type.**
   
   * Als u het type wijzigt in **Lokaal vastgemaakt,** controleert StorSimple of er voldoende capaciteit is.
   * Als u het type wijzigt in **Trapsgewijs** en dit volume wordt gebruikt voor archiefgegevens, schakelt u het selectievakje **Dit volume gebruiken in voor minder vaak geopende archiefgegevens.**
   * Als u een lokaal vastgemaakt volume configureert als gelaagd of _omgekeerd,_ wordt het volgende bericht weergegeven.
   
     ![Bericht van volumetype wijzigen](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Klik op **Opslaan** om de wijzigingen op te slaan. Klik op **Ja** om het conversieproces te starten wanneer u om bevestiging wordt gevraagd. 

    ![Opslaan en bevestigen](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. De Azure-portal geeft een melding weer voor het maken van banen die het volume zou bijwerken. Klik op de melding om de status van de volumeconversietaak te controleren.

    ![Taak voor volumeconversie](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Een volume offline halen

Mogelijk moet u een volume offline halen wanneer u van plan bent het volume te wijzigen of te verwijderen. Wanneer een volume offline is, is het niet beschikbaar voor lees-schrijftoegang. U moet het volume offline halen op de host en het apparaat.

#### <a name="to-take-a-volume-offline"></a>Een volume offline halen

1. Zorg ervoor dat het volume in kwestie niet in gebruik is voordat u het offline haalt.
2. Haal eerst het volume offline op de host. Dit elimineert elk potentieel risico van gegevensbeschadiging op het volume. Raadpleeg voor specifieke stappen de instructies voor uw hostbesturingssysteem.
3. Nadat de host offline is, neemt u het volume op het apparaat offline door de volgende stappen uit te voeren:
   
    1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de tabeltabelvermelding van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik **op Instellingen > Volumes**.

        ![Ga naar Volumes blade](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Selecteer in de tabeltabellijst met volumes het volume en klik met de rechtermuisknop om het contextmenu aan te roepen. Selecteer **Offline gaan** om het volume dat u offline wijzigt, offline te halen.

        ![Volume offline selecteren en offline halen](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Bekijk in het **offlineblad nemen** de impact van het offline halen van het volume en schakel het bijbehorende selectievakje in. Klik **op Offline halen**. 

    ![Bekijk de impact van het offline halen van volume](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      U krijgt een melding wanneer het volume offline is. De volumestatus wordt ook bijgewerkt naar Offline.
      
4. Nadat een volume offline is, als u het volume selecteert en met de rechtermuisknop klikt, wordt de optie **Online brengen** beschikbaar in het contextmenu.

> [!NOTE]
> Met de opdracht **Offline nemen** wordt een verzoek naar het apparaat verstuurt om het volume offline te halen. Als hosts het volume nog steeds gebruiken, resulteert dit in verbroken verbindingen, maar het offline halen van het volume mislukt niet.

## <a name="delete-a-volume"></a>Een volume verwijderen

> [!IMPORTANT]
> U een volume alleen verwijderen als het offline is.

Voer de volgende stappen uit om een volume te verwijderen.

#### <a name="to-delete-a-volume"></a>Een volume verwijderen

1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de tabeltabelvermelding van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik **op Instellingen > Volumes**.

    ![Ga naar Volumes blade](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Controleer de status van het volume dat u wilt verwijderen. Als het volume dat u wilt verwijderen niet offline is, neemt u het eerst offline. Volg de stappen in [Een volume offline uitvoeren](#take-a-volume-offline).
4. Nadat het volume offline is, selecteert u het volume, klikt u met de rechtermuisknop om het contextmenu aan te roepen en selecteert u **Verwijderen**.

    ![Verwijderen uit contextmenu selecteren](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Controleer en schakel in het blad **Verwijderen** het selectievakje in tegen de impact van het verwijderen van een volume. Wanneer u een volume verwijdert, gaan alle gegevens die zich op het volume bevinden, verloren. 

    ![Wijzigingen opslaan en bevestigen](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Nadat het volume is verwijderd, wordt de tabeltabellijst met volumes bijgewerkt om de verwijdering aan te geven.

    ![Bijgewerkte volumelijst](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Als u een lokaal vastgemaakt volume verwijdert, wordt de beschikbare ruimte voor nieuwe volumes mogelijk niet onmiddellijk bijgewerkt. De StorSimple Device Manager Service werkt de beschikbare lokale ruimte periodiek bij. We raden u aan een paar minuten te wachten voordat u het nieuwe volume probeert te maken.
   >
   > Als u bovendien een lokaal vastgemaakt volume verwijdert en vervolgens onmiddellijk daarna een ander lokaal vastgemaakt volume verwijdert, worden de taken voor het verwijderen van het volume achtereenvolgens uitgevoerd. De eerste taak voor het verwijderen van het volume moet worden voltooid voordat de volgende taak voor het verwijderen van het volume kan beginnen.

## <a name="monitor-a-volume"></a>Een volume controleren

Met volumebewaking u I/O-gerelateerde statistieken verzamelen voor een volume. Controle is standaard ingeschakeld voor de eerste 32 volumes die u maakt. Het controleren van extra volumes is standaard uitgeschakeld. 

> [!NOTE]
> Controle van gekloonde volumes is standaard uitgeschakeld.


Voer de volgende stappen uit om de bewaking voor een volume in of uit te schakelen.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Volumebewaking in- of uitschakelen

1. Ga naar de StorSimple-apparaatbeheerfunctie en klik op **Apparaten**. Selecteer in de tabeltabelvermelding van de apparaten het apparaat met het volume dat u wilt wijzigen. Klik **op Instellingen > Volumes**.
2. Selecteer in de tabeltabellijst met volumes het volume en klik met de rechtermuisknop om het contextmenu aan te roepen. Selecteer **Wijzigen**.
3. Selecteer in het **volumeblad wijzigen** voor **Controle** selecteren **in-** of **uitschakelen** om bewaking in- of uit te schakelen.

    ![Bewaking uitschakelen](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Klik **op Opslaan** en klik wanneer u om bevestiging wordt gevraagd op **Ja**. De Azure-portal geeft een melding weer voor het bijwerken van het volume en vervolgens een succesbericht, nadat het volume is bijgewerkt.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [klonen van een StorSimple-volume.](storsimple-8000-clone-volume-u2.md)
* Meer informatie over het [gebruik van de StorSimple Device Manager-service om uw StorSimple-apparaat te beheren.](storsimple-8000-manager-service-administration.md)


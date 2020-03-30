---
title: Waarschuwingen voor storSimple 8000-serie apparaten weergeven en beheren
description: Beschrijft de waarschuwingsvoorwaarden en ernst van StorSimple, hoe u waarschuwingsmeldingen configureert en hoe u de StorSimple Device Manager-service gebruiken om waarschuwingen te beheren.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: ff50836e1438b8d35f26ddfdf165084406f52faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267818"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>De StorSimple Device Manager-service gebruiken om StorSimple-waarschuwingen weer te geven en te beheren

## <a name="overview"></a>Overzicht

Het **Alerts-blad** in de StorSimple Device Manager-service biedt u een manier om StorSimple-apparaatgerelateerde waarschuwingen op een realtime basis te bekijken en te wissen. Vanuit dit blad u de gezondheidsproblemen van uw StorSimple-apparaten en de algemene Microsoft Azure StorSimple-oplossing centraal controleren.

In deze zelfstudie worden veelvoorkomende waarschuwingsvoorwaarden, waarschuwingsernstniveaus beschreven en hoe u waarschuwingsmeldingen configureren. Daarnaast bevat het waarschuwingssnelreferentietabellen, waarmee u snel een specifieke waarschuwing vinden en adequaat reageren.

![Pagina Waarschuwingen](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Algemene waarschuwingsvoorwaarden

Uw StorSimple-apparaat genereert waarschuwingen als reactie op verschillende omstandigheden. De volgende zijn de meest voorkomende typen waarschuwingsvoorwaarden:

* **Hardwareproblemen** – Deze waarschuwingen vertellen u over de status van uw hardware. Ze laten u weten of firmware-upgrades nodig zijn, als een netwerkinterface problemen heeft, of als er een probleem is met een van uw gegevensstations.
* **Verbindingsproblemen** – Deze waarschuwingen treden op wanneer het moeilijk is om gegevens over te dragen. Communicatieproblemen kunnen optreden tijdens de overdracht van gegevens van en naar het Azure-opslagaccount of door een gebrek aan connectiviteit tussen de apparaten en de StorSimple Device Manager-service. Communicatie problemen zijn enkele van de moeilijkste op te lossen, omdat er zo veel punten van mislukking. Controleer altijd eerst of netwerkconnectiviteit en internettoegang beschikbaar zijn voordat u verdergaat met meer geavanceerde probleemoplossing. Ga voor hulp bij het oplossen van problemen naar [Probleemoplossen met de cmdlet Test-Connection.](storsimple-8000-troubleshoot-deployment.md)
* **Prestatieproblemen** : deze waarschuwingen worden veroorzaakt wanneer uw systeem niet optimaal presteert, bijvoorbeeld wanneer het onder zware belasting staat.

Bovendien ziet u mogelijk waarschuwingen met betrekking tot beveiliging, updates of taakfouten.

## <a name="alert-severity-levels"></a>Ernstniveaus voor waarschuwingen

Waarschuwingen hebben verschillende ernstniveaus, afhankelijk van de impact die de waarschuwingssituatie zal hebben en de noodzaak van een reactie op de waarschuwing. Dit zijn de vijf ernstniveaus:

* **Kritiek** : deze waarschuwing is een reactie op een aandoening die de succesvolle prestaties van uw systeem beïnvloedt. Er is actie nodig om ervoor te zorgen dat de StorSimple-service niet wordt onderbroken.
* **Waarschuwing** : deze voorwaarde kan kritiek worden als deze niet is opgelost. U moet de situatie onderzoeken en alle maatregelen nemen die nodig zijn om het probleem op te lossen.
* **Informatie** – Deze waarschuwing bevat informatie die nuttig kan zijn bij het volgen en beheren van uw systeem.

## <a name="configure-alert-settings"></a>Waarschuwingsinstellingen configureren

U kiezen of u per e-mail op de hoogte wilt worden gesteld van de waarschuwingsvoorwaarden voor elk van uw StorSimple-apparaten. Bovendien u andere ontvangers van waarschuwingen identificeren door hun e-mailadressen in te voeren in het vak **Andere e-mailontvangers,** gescheiden door puntkomma's.

> [!NOTE]
> U maximaal 20 e-mailadressen per apparaat invoeren.

Nadat u e-mailmelding voor een apparaat hebt ingeschakeld, ontvangen leden van de meldingslijst elke keer dat er een kritieke waarschuwing plaatsvindt een e-mailbericht. De berichten worden verzonden vanuit *storsimple-alerts-noreply\@mail.windowsazure.com* en beschrijven de waarschuwingstoestand. Ontvangers kunnen op **Afmelden** klikken om zichzelf uit de lijst met e-mailmeldingen te verwijderen.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>E-mailmelding van waarschuwingen voor een apparaat inschakelen
1. Ga naar uw StorSimple-apparaatbeheerservice. Selecteer en klik in de lijst met apparaten op het apparaat dat u wilt configureren.
2. Ga naar **Instellingen** > **Algemeen** voor het apparaat.

   ![Alarmblad](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. Ga in het blad **Algemene instellingen** naar **Waarschuwingsinstellingen** en stel het volgende in:
   
   1. Selecteer **JA**in het veld **E-mailmelding verzenden** .
   2. Selecteer in het veld **E-mailservicebeheerders** de optie **JA** om de servicebeheerder en alle medebeheerders de waarschuwingsmeldingen te laten ontvangen.
   3. Voer in het veld **Andere e-mailontvangers** de e-mailadressen in van alle andere ontvangers die de waarschuwingsmeldingen moeten ontvangen. Voer namen in de notatie in *die iemand\@somewhere.com*. Gebruik puntkomma's om de e-mailadressen te scheiden. U maximaal 20 e-mailadressen per apparaat configureren. 
      
3. Als u een e-mailmelding voor een test wilt verzenden, klikt u op **Test-e-mail verzenden**. De StorSimple Device Manager-service geeft statusberichten weer terwijl deze de testmelding doorstuurt.

    ![Waarschuwingsinstellingen](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. U ziet een melding wanneer de teste-mail wordt verzonden. 
   
    ![Waarschuwingen test melding e-mail verzonden](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Als het testmeldingsbericht niet kan worden verzonden, wordt er met de StorSimple Device Manager-service een geschikt foutbericht weergegeven. Wacht een paar minuten en probeer vervolgens uw testmeldingopnieuw te verzenden. 

5. Nadat u de configuratie hebt voltooid, klikt u op **Opslaan**. Klik op **Ja** als u om bevestiging wordt gevraagd.

     ![Waarschuwingen test melding e-mail verzonden](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Waarschuwingen weergeven en bijhouden

Het storSimple Device Manager-serviceoverzichtsblad biedt u een snelle blik op het aantal waarschuwingen op uw apparaten, gerangschikt op ernstniveau.

![Dashboard Waarschuwingen](./media/storsimple-8000-manage-alerts/device-summary4.png)

Als u op het ernstniveau klikt, wordt het **zwaard Waarschuwingen** geopend. De resultaten omvatten alleen de waarschuwingen die overeenkomen met dat ernstniveau.

Als u op een waarschuwing in de lijst klikt, ziet u aanvullende details voor de waarschuwing, waaronder de laatste keer dat de waarschuwing is gerapporteerd, het aantal exemplaren van de waarschuwing op het apparaat en de aanbevolen actie om de waarschuwing op te lossen. Als het een hardwarewaarschuwing is, wordt ook de hardwarecomponent geïdentificeerd.

![Voorbeeld van hardwarewaarschuwing](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

U de waarschuwingsgegevens naar een tekstbestand kopiëren als u de informatie naar Microsoft Support wilt verzenden. Nadat u de aanbeveling hebt opgevolgd en de waarschuwingstoestand on-premises hebt opgelost, moet u de waarschuwing van het apparaat wissen door de waarschuwing in het **hoofd van waarschuwingen** te selecteren en op **Wissen**te klikken. Als u meerdere waarschuwingen wilt wissen, selecteert u elke waarschuwing, klikt u op een kolom behalve de **kolom Waarschuwing** en klikt u vervolgens op **Wissen** nadat u alle waarschuwingen hebt geselecteerd die moeten worden gewist. Houd er rekening mee dat sommige waarschuwingen automatisch worden gewist wanneer het probleem is opgelost of wanneer het systeem de waarschuwing bijwerkt met nieuwe informatie.

Wanneer u op **Wissen**klikt, krijgt u de mogelijkheid om opmerkingen te geven over de waarschuwing en de stappen die u hebt genomen om het probleem op te lossen. Sommige gebeurtenissen worden door het systeem gewist als een andere gebeurtenis wordt geactiveerd met nieuwe informatie. In dat geval ziet u het volgende bericht.

![Waarschuwingsbericht wissen](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Waarschuwingen sorteren en controleren

Mogelijk is het efficiënter om rapporten over waarschuwingen uit te voeren, zodat u ze in groepen bekijken en wissen. Bovendien kan het **waarschuwingsblad** maximaal 250 waarschuwingen weergeven. Als u dat aantal waarschuwingen hebt overschreden, worden niet alle waarschuwingen weergegeven in de standaardweergave. U de volgende velden combineren om aan te passen welke waarschuwingen worden weergegeven:

* **Status** : u **actieve** of **gewiste** waarschuwingen weergeven. Actieve waarschuwingen worden nog steeds geactiveerd op uw systeem, terwijl gewiste waarschuwingen handmatig zijn gewist door een beheerder of programmatisch zijn gewist omdat het systeem de waarschuwingstoestand heeft bijgewerkt met nieuwe informatie.
* **Ernst** : u waarschuwingen weergeven van alle ernstniveaus (kritiek, waarschuwing, informatie) of slechts een bepaalde ernst, zoals alleen kritieke waarschuwingen.
* **Bron** : u waarschuwingen van alle bronnen weergeven of de waarschuwingen beperken tot meldingen die afkomstig zijn van de service of een of alle apparaten.
* **Tijdsbereik** : door de **van-** en naar-datums en tijdstempels **op te** geven, u waarschuwingen bekijken gedurende de periode waarin u geïnteresseerd bent.

![Lijst met waarschuwingen](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Snelle naslaginformatie waarschuwingen

In de volgende tabellen worden enkele microsoft Azure StorSimple-waarschuwingen vermeld die u tegenkomen, evenals aanvullende informatie en aanbevelingen waar beschikbaar. StorSimple-apparaatwaarschuwingen vallen in een van de volgende categorieën:

* [Waarschuwingen voor cloudconnectiviteit](#cloud-connectivity-alerts)
* [Clusterwaarschuwingen](#cluster-alerts)
* [Waarschuwingen voor herstel na noodgevallen](#disaster-recovery-alerts)
* [Hardwarewaarschuwingen](#hardware-alerts)
* [Waarschuwingen voor het mislukken](#job-failure-alerts)
* [Lokaal vastgemaakte volumewaarschuwingen](#locally-pinned-volume-alerts)
* [Netwerkwaarschuwingen](#networking-alerts)
* [Prestatiewaarschuwingen](#performance-alerts)
* [Beveiligingswaarschuwingen](#security-alerts)
* [Waarschuwingen voor ondersteuningspakketten](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Waarschuwingen voor cloudconnectiviteit

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Connectiviteit met <*naam van cloudreferenties*> kan niet worden vastgesteld. |Kan geen verbinding maken met het opslagaccount. |Het lijkt erop dat er mogelijk een verbindingsprobleem is met uw apparaat. Voer de `Test-HcsmConnection` cmdlet uit vanuit de Windows PowerShell-interface voor StorSimple op uw apparaat om het probleem te identificeren en op te lossen. Als de instellingen juist zijn, kan het probleem zijn met de referenties van het opslagaccount waarvoor de waarschuwing is gegenereerd. Gebruik in dit `Test-HcsStorageAccountCredential` geval de cmdlet om te bepalen of er problemen zijn die u oplossen.<ul><li>Controleer uw netwerkinstellingen.</li><li>Controleer uw opslagaccountreferenties.</li></ul> |
| We hebben de afgelopen <> *minuten* geen hartslag van uw apparaat ontvangen. |Kan geen verbinding maken met het apparaat. |Het lijkt erop dat er een verbindingsprobleem is met uw apparaat. Gebruik de `Test-HcsmConnection` cmdlet van de Windows PowerShell Interface voor StorSimple op uw apparaat om het probleem te identificeren en op te lossen of neem contact op met uw netwerkbeheerder. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple-gedrag wanneer cloudconnectiviteit mislukt

Wat gebeurt er als cloudconnectiviteit mislukt voor mijn StorSimple-apparaat dat in productie wordt uitgevoerd?

Als cloudconnectiviteit mislukt op uw StorSimple-productieapparaat, kan, afhankelijk van de status van uw apparaat, het volgende optreden:

* **Voor de lokale gegevens op uw apparaat**: Sinds enige tijd zal er geen verstoring en leest zal blijven worden geserveerd. Echter, als het aantal uitstaande IOs toeneemt en overschrijdt een limiet, de leest zou kunnen beginnen te mislukken.

    Afhankelijk van de hoeveelheid gegevens op uw apparaat, blijven de schrijfbewerkingen ook de eerste uren na de onderbreking in de cloudconnectiviteit plaatsvinden. De schrijfbewerkingen vertragen dan en beginnen uiteindelijk te mislukken als de cloudconnectiviteit enkele uren wordt verstoord. (Er is tijdelijke opslag op het apparaat voor gegevens die naar de cloud moeten worden gepusht. Dit gebied wordt gespoeld wanneer de gegevens worden verzonden. Als de connectiviteit mislukt, worden gegevens in dit opslaggebied niet naar de cloud gepusht en mislukt IO.)
* **Voor de gegevens in de cloud**: Voor de meeste fouten in de cloudverbinding wordt een fout geretourneerd. Zodra de verbinding is hersteld, worden de IOs hervat zonder dat de gebruiker het volume online hoeft te brengen. In zeldzame gevallen kan tussenkomst van de gebruiker nodig zijn om het volume online terug te brengen van de Azure-portal.
* **Voor cloudsnapshots in uitvoering**: De bewerking wordt binnen 4-5 uur een paar keer opnieuw geprobeerd en als de verbinding niet is hersteld, mislukken de cloudmomentopnamen.

### <a name="cluster-alerts"></a>Clusterwaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Apparaat is niet overgegaan naar <*naam van het apparaat*>. |Het apparaat bevindt zich in de onderhoudsmodus. |Apparaat is mislukt vanwege het in- of uitrijden van de onderhoudsmodus. Dit is normaal en er is geen actie nodig. Nadat u deze waarschuwing hebt herkend, moet u deze wissen van de waarschuwingspagina. |
| Apparaat is niet overgegaan naar <*naam van het apparaat*>. |Apparaatfirmware of -software is zojuist bijgewerkt. |Er was een clusterfailover als gevolg van een update. Dit is normaal en er is geen actie nodig. Nadat u deze waarschuwing hebt herkend, moet u deze wissen van de waarschuwingspagina. |
| Apparaat is niet overgegaan naar <*naam van het apparaat*>. |Controller is uitgeschakeld of opnieuw opgestart. |Het apparaat is mislukt omdat de actieve controller is uitgeschakeld of opnieuw is gestart door een beheerder. Er is geen actie nodig. Nadat u deze waarschuwing hebt herkend, moet u deze wissen van de waarschuwingspagina. |
| Apparaat is niet overgegaan naar <*naam van het apparaat*>. |Geplande failover. |Controleer of dit een geplande failover was. Nadat u de juiste actie hebt ondernomen, moet u deze waarschuwing wissen op de pagina waarschuwingen. |
| Apparaat is niet overgegaan naar <*naam van het apparaat*>. |Ongeplande failover. |StorSimple is gebouwd om automatisch te herstellen van ongeplande failovers. Als u een groot aantal van deze waarschuwingen ziet, neemt u contact op met Microsoft Support. |
| Apparaat is niet overgegaan naar <*naam van het apparaat*>. |Andere/onbekende oorzaak. |Als u een groot aantal van deze waarschuwingen ziet, neemt u contact op met Microsoft Support. Nadat het probleem is opgelost, moet u deze waarschuwing wissen van de pagina waarschuwingen. |
| Een kritieke apparaatservice rapporteert de status als mislukt. |Gegevenspadservice mislukt. |Neem contact op met Microsoft Support voor hulp. |
| Virtueel IP-adres voor netwerkinterface <*GEGEVENS #*> rapporten status als mislukt. |Andere/onbekende oorzaak. |Soms kunnen tijdelijke omstandigheden deze waarschuwingen veroorzaken. Als dit het geval is, wordt deze waarschuwing na enige tijd automatisch gewist. Als het probleem blijft bestaan, neemt u contact op met Microsoft Support. |
| Virtueel IP-adres voor netwerkinterface <*GEGEVENS #*> rapporten status als mislukt. |Interfacenaam: <*GEGEVENS* # `<IP address>`> IP-adres kan niet online worden gebracht omdat er een duplicaat IP-adres op het netwerk is gedetecteerd. |Controleer of het dubbele IP-adres uit het netwerk wordt verwijderd of configureer de interface opnieuw met een ander IP-adres. |

### <a name="disaster-recovery-alerts"></a>Waarschuwingen voor herstel na noodgevallen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Herstelbewerkingen kunnen niet alle instellingen voor deze service herstellen. Apparaatconfiguratiegegevens zijn voor sommige apparaten in een inconsistente status. |Gegevensinconsistentie gedetecteerd na herstel na noodgevallen. |Versleutelde gegevens op de service worden niet gesynchroniseerd met die op het apparaat. Autoisr het apparaat <*de naam* van het apparaat> van StorSimple Device Manager om het synchronisatieproces te starten. Gebruik de Windows PowerShell-interface voor `Restore-HcsmEncryptedServiceData` StorSimple om de *naam* <van het apparaat op het apparaat uit te voeren> cmdlet, waardoor het oude wachtwoord wordt verstrekt als invoer voor deze cmdlet om het beveiligingsprofiel te herstellen. Voer vervolgens `Invoke-HcsmServiceDataEncryptionKeyChange` de cmdlet uit om de versleutelingssleutel voor servicegegevens bij te werken. Nadat u de juiste actie hebt ondernomen, moet u deze waarschuwing wissen op de pagina waarschuwingen. |

### <a name="hardware-alerts"></a>Hardwarewaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Hardwarecomponent <*component-ID*> rapporteert de status als <*status*>. | |Soms kunnen tijdelijke omstandigheden deze waarschuwingen veroorzaken. Als dat zo is, wordt deze waarschuwing na enige tijd automatisch gewist. Als het probleem blijft bestaan, neemt u contact op met Microsoft Support. |
| Passieve controller defect. |De passieve (secundaire) controller functioneert niet. |Uw apparaat is operationeel, maar een van uw controllers is defect. Probeer die controller opnieuw op te starten. Als het probleem niet is opgelost, neemt u contact op met Microsoft Support. |

### <a name="job-failure-alerts"></a>Waarschuwingen voor het mislukken

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Back-up van <*bronvolumegroep-id*> mislukt. |Back-uptaak is mislukt. |Verbindingsproblemen kunnen voorkomen dat de back-upbewerking wordt voltooid. Als er geen verbindingsproblemen zijn, hebt u mogelijk het maximum aantal back-ups bereikt. Verwijder back-ups die niet meer nodig zijn en probeer de bewerking opnieuw. Nadat u de juiste actie hebt ondernomen, moet u deze waarschuwing wissen op de pagina waarschuwingen. |
| Kloon van <*bronback-upelement-id's*> <serienummers van *het doelvolume*> is mislukt. |Kloontaak mislukt. |Vernieuw de back-uplijst om te controleren of de back-up nog steeds geldig is. Als de back-up geldig is, is het mogelijk dat problemen met de cloudverbinding voorkomen dat de kloonbewerking wordt voltooid. Als er geen verbindingsproblemen zijn, hebt u mogelijk de opslaglimiet bereikt. Verwijder back-ups die niet meer nodig zijn en probeer de bewerking opnieuw. Nadat u de juiste actie hebt ondernomen om het probleem op te lossen, moet u deze waarschuwing wissen op de pagina waarschuwingen. |
| Herstel van <*bronback-upelement-id's* is> mislukt. |Hersteltaak is mislukt. |Vernieuw de back-uplijst om te controleren of de back-up nog steeds geldig is. Als de back-up geldig is, is het mogelijk dat problemen met de cloudverbinding voorkomen dat de herstelbewerking wordt voltooid. Als er geen verbindingsproblemen zijn, hebt u mogelijk de opslaglimiet bereikt. Verwijder back-ups die niet meer nodig zijn en probeer de bewerking opnieuw. Nadat u de juiste actie hebt ondernomen om het probleem op te lossen, moet u deze waarschuwing wissen op de pagina waarschuwingen. |

### <a name="locally-pinned-volume-alerts"></a>Lokaal vastgemaakte volumewaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Het maken van lokale <*volumenaam*> mislukt. |De taak voor het maken van volume is mislukt. <*Foutbericht dat overeenkomt met de mislukte foutcode*>. |Verbindingsproblemen kunnen voorkomen dat de bewerking voor het maken van ruimte met succes wordt voltooid. Lokaal vastgemaakte volumes zijn dik ingericht en het proces van het maken van ruimte omvat het morsen van gelaagde volumes naar de cloud. Als er geen verbindingsproblemen zijn, hebt u mogelijk de lokale ruimte op het apparaat uitgeput. Bepaal of er ruimte op het apparaat is voordat u deze bewerking opnieuw probeert. |
| Uitbreiding van het lokale volume <*volumenaam*> mislukt. |De taak voor volumewijziging is mislukt als gevolg van <*foutbericht dat overeenkomt met de mislukte foutcode*>. |Verbindingsproblemen kunnen voorkomen dat de volumeuitbreidingsbewerking wordt voltooid. Lokaal vastgemaakte volumes zijn dik ingericht en het proces van uitbreiding van de bestaande ruimte omvat het morsen van gelaagde volumes naar de cloud. Als er geen verbindingsproblemen zijn, hebt u mogelijk de lokale ruimte op het apparaat uitgeput. Bepaal of er ruimte op het apparaat is voordat u deze bewerking opnieuw probeert. |
| Conversie van volume <*volumenaam*> mislukt. |De volumeconversietaak om het volumetype om te zetten van lokaal vastgemaakt naar trapsgeweneerd, is mislukt. |De conversie van het volume van lokaal vastgemaakt type naar trapsgewijs kan niet worden voltooid. Zorg ervoor dat er geen verbindingsproblemen zijn waardoor de bewerking niet kan worden voltooid. Voor het oplossen van verbindingsproblemen gaat u naar [Probleem oplossen met de cmdlet Test-HcsmConnection.](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)<br>Het oorspronkelijke lokaal vastgemaakte volume is nu gemarkeerd als een gelaagd volume, omdat sommige gegevens van het lokaal vastgemaakte volume tijdens de conversie naar de cloud zijn gelekt. Het resulterende gelaagde volume neemt nog steeds lokale ruimte in op het apparaat dat niet kan worden teruggewonnen voor toekomstige lokale volumes.<br>Los verbindingsproblemen op, schakel de waarschuwing uit en zet dit volume terug naar het oorspronkelijke lokaal vastgemaakte volumetype om ervoor te zorgen dat alle gegevens lokaal weer beschikbaar worden gesteld. |
| Conversie van volume <*volumenaam*> mislukt. |De volumeconversietaak om het volumetype om te zetten van gelaagd naar lokaal vastgemaakt, is mislukt. |De conversie van het volume van het type trapsgewijs naar lokaal vastgemaakt kan niet worden voltooid. Zorg ervoor dat er geen verbindingsproblemen zijn waardoor de bewerking niet kan worden voltooid. Voor het oplossen van verbindingsproblemen gaat u naar [Probleem oplossen met de cmdlet Test-HcsmConnection.](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet)<br>Het oorspronkelijke gelaagde volume dat nu is gemarkeerd als een lokaal vastgemaakt volume als onderdeel van het conversieproces, blijft gegevens in de cloud hebben, terwijl de dik ingerichte ruimte op het apparaat voor dit volume niet langer kan worden teruggewonnen voor toekomstige lokale volumes.<br>Los verbindingsproblemen op, schakel de waarschuwing uit en zet dit volume weer om naar het oorspronkelijke trapsgewijze volumetype om ervoor te zorgen dat de lokale ruimte die op het apparaat is ingericht, kan worden teruggevorderd. |
| Lokaal ruimteverbruik voor lokale momentopnamen van <naam van *de volumegroep* naderen> |Lokale momentopnamen voor het back-upbeleid kunnen binnenkort zonder ruimte komen te zitten en worden ongeldig gemaakt om hostschrijffouten te voorkomen. |Frequente lokale momentopnamen naast een hoog gegevensverloop in de volumes die aan deze back-upbeleidsgroep zijn gekoppeld, zorgen ervoor dat lokale ruimte op het apparaat snel wordt verbruikt. Verwijder alle lokale momentopnamen die niet meer nodig zijn. Werk ook uw lokale momentopnameschema's voor dit back-upbeleid bij om minder frequente lokale momentopnamen te maken en ervoor te zorgen dat er regelmatig cloudsnapshots worden gemaakt. Als deze acties niet worden uitgevoerd, kan de lokale ruimte voor deze momentopnamen binnenkort uitgeput zijn en zal het systeem ze automatisch verwijderen om ervoor te zorgen dat hostschrijfbewerkingen met succes worden verwerkt. |
| Lokale momentopnamen voor <*> volumegroepnaam* zijn ongeldig verklaard. |De lokale momentopnamen voor <*naam van de volumegroep*> zijn ongeldig gemaakt en vervolgens verwijderd omdat ze de lokale ruimte op het apparaat overschreden. |Als u ervoor wilt zorgen dat dit zich in de toekomst niet meer voordoet, bekijkt u de lokale momentopnameschema's voor dit back-upbeleid en verwijdert u alle lokale momentopnamen die niet meer nodig zijn. Frequente lokale momentopnamen naast een hoog gegevensverloop in de volumes die aan deze back-upbeleidsgroep zijn gekoppeld, kunnen ertoe leiden dat lokale ruimte op het apparaat snel wordt verbruikt. |
| Herstel van <*bronback-upelement-id's* is> mislukt. |De hersteltaak is mislukt. |Als u lokaal vastgemaakt of een mix van lokaal vastgemaakte en gelaagde volumes in dit back-upbeleid hebt, vernieuwt u de back-uplijst om te controleren of de back-up nog steeds geldig is. Als de back-up geldig is, is het mogelijk dat problemen met de cloudverbinding voorkomen dat de herstelbewerking wordt voltooid. De lokaal vastgemaakte volumes die als onderdeel van deze momentopnamegroep zijn hersteld, hebben niet al hun gegevens naar het apparaat gedownload en als u een mix van gelaagde en lokaal vastgemaakte volumes in deze momentopnamegroep hebt, worden ze niet gesynchroniseerd met elkaar. Als u de herstelbewerking wilt voltooien, neemt u de volumes in deze groep offline op de host en probeert u de herstelbewerking opnieuw. Houd er rekening mee dat eventuele wijzigingen in de volumegegevens die tijdens het herstelproces zijn uitgevoerd, verloren gaan. |

### <a name="networking-alerts"></a>Netwerkwaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Kon storSimple service(s) niet starten. |Gegevenspadfout |Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Support. |
| Duplicaat IP-adres gedetecteerd voor 'Data0'. | |Het systeem heeft een conflict ontdekt voor IP-adres '10.0.0.1'. De netwerkbron 'Data0' op het * \<apparaat1>* is offline. Controleer of dit IP-adres niet wordt gebruikt door een andere entiteit in dit netwerk. Als u netwerkproblemen wilt oplossen, gaat u naar [Problemen oplossen met de cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Neem contact op met uw netwerkbeheerder voor hulp bij het oplossen van dit probleem. Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Support. |
| IPv4 (of IPv6) adres voor 'Data0' is offline. | |De netwerkbron 'Data0' met IP-adres '10.0.0.1.' en voorvoegsellengte '22' op het * \<apparaat1>* offline is. Zorg ervoor dat de schakelpoorten waarop deze interface is verbonden, operationeel zijn. Als u netwerkproblemen wilt oplossen, gaat u naar [Problemen oplossen met de cmdlet Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Kon geen verbinding maken met de verificatieservice. |Gegevenspadfout |De URL die wordt gebruikt om te verifiëren is niet bereikbaar. Zorg ervoor dat uw firewallregels de URL-patronen bevatten die zijn opgegeven voor het StorSimple-apparaat. Ga voor meer informatie over URL-patronen\/in azure portal naar https: /aka.ms/ss-8000-network-reqs. Als u Azure Government Cloud gebruikt, gaat\/u naar de URL-patronen in https: /aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Prestatiewaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties | |
|:--- |:--- |:--- | --- |
| De apparaatbelasting heeft> <*drempel* overschreden. |Langzamer dan verwacht responstijden. |Uw apparaat rapporteert het gebruik onder een zware invoer/uitvoerbelasting. Dit kan ertoe leiden dat uw apparaat niet zo goed werkt als het zou moeten. Bekijk de workloads die u aan het apparaat hebt gekoppeld en bepaal of er workloads zijn die naar een ander apparaat kunnen worden verplaatst of die niet langer nodig zijn.|
| Kon storSimple service(s) niet starten. |Gegevenspadfout |Als het probleem zich blijft voordoen, neemt u contact op met Microsoft Support. |

### <a name="security-alerts"></a>Beveiligingswaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| De Microsoft Support-sessie is begonnen. |Externe ondersteuningssessie. |Bevestig dat deze toegang is geautoriseerd. Nadat u de juiste actie hebt ondernomen, moet u deze waarschuwing wissen op de pagina waarschuwingen. |
| Wachtwoord voor <*element*> verloopt in <*>.* |Het verlopen van wachtwoorden nadert. |Wijzig uw wachtwoord voordat het verloopt. |
| Beveiligingsconfiguratiegegevens ontbreken voor <*element-ID->.* | |De volumes die aan deze volumecontainer zijn gekoppeld, kunnen niet worden gebruikt om uw StorSimple-configuratie te repliceren. Om ervoor te zorgen dat uw gegevens veilig worden opgeslagen, raden we u aan de volumecontainer en de volumes die aan de volumecontainer zijn gekoppeld, te verwijderen. Nadat u de juiste actie hebt ondernomen, moet u deze waarschuwing wissen op de pagina waarschuwingen. |
| <*nummer*> inlogpogingen is mislukt voor <*element ID->.* |Meerdere mislukte aanmeldingspogingen. |Uw apparaat wordt mogelijk aangevallen of een geautoriseerde gebruiker probeert verbinding te maken met een onjuist wachtwoord.<ul><li>Neem contact op met uw geautoriseerde gebruikers en controleer of deze pogingen afkomstig zijn van een legitieme bron. Als u nog steeds grote aantallen mislukte inlogpogingen ziet, u overwegen extern beheer uit te schakelen en contact op te nemen met uw netwerkbeheerder. Nadat u de juiste actie hebt ondernomen, moet u deze waarschuwing wissen op de pagina waarschuwingen.</li><li>Controleer of uw snapshotmanager-exemplaren zijn geconfigureerd met het juiste wachtwoord. Nadat u de juiste actie hebt ondernomen, moet u deze waarschuwing wissen op de pagina waarschuwingen.</li></ul>Ga voor meer informatie naar [Een verlopen apparaatwachtwoord wijzigen.](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password) |
| Er zijn een of meer fouten opgetreden tijdens het wijzigen van de versleutelingssleutel voor servicegegevens. | |Er zijn fouten opgetreden tijdens het wijzigen van de versleutelingssleutel voor servicegegevens. Nadat u de foutvoorwaarden hebt `Invoke-HcsmServiceDataEncryptionKeyChange` verholpen, voert u de cmdlet uit van de Windows PowerShell-interface voor StorSimple op uw apparaat om de service bij te werken. Als dit probleem zich blijft voordoen, neemt u contact op met microsoft-ondersteuning. Nadat u het probleem hebt opgelost, moet u deze waarschuwing wissen op de pagina waarschuwingen. |

### <a name="support-package-alerts"></a>Waarschuwingen voor ondersteuningspakketten

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Het maken van ondersteuningspakket is mislukt. |StorSimple kon het pakket niet genereren. |Probeer deze bewerking opnieuw. Als het probleem blijft bestaan, neemt u contact op met Microsoft Support. Nadat u het probleem hebt opgelost, moet u deze waarschuwing wissen op de pagina waarschuwingen. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [StorSimple-fouten en het oplossen van problemen met apparaatimplementatie.](storsimple-8000-troubleshoot-deployment.md)


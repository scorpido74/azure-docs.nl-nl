---
title: Waarschuwingen voor StorSimple Virtual Array weergeven en beheren
description: Beschrijft de waarschuwingsvoorwaarden en ernst van StorSimple Virtual Array en hoe u de StorSimple Manager-service gebruiken om waarschuwingen te beheren.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7c469be4a1d8aba23857b1ba52ee829c126a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267428"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>StorSimple-apparaatbeheer gebruiken om waarschuwingen voor de StorSimple Virtual Array te beheren

## <a name="overview"></a>Overzicht

De waarschuwingen functie in de StorSimple Device Manager service biedt een manier voor u om te controleren en duidelijk waarschuwingen met betrekking tot StorSimple Virtual Arrays op een real-time basis. U de waarschuwingen op het **onderhoudsoverzichtsblad** gebruiken om de gezondheidsproblemen van uw StorSimple Virtual Arrays en de algemene Microsoft Azure StorSimple-oplossing centraal te controleren.

In deze zelfstudie wordt beschreven hoe u waarschuwingsmeldingen, algemene waarschuwingsvoorwaarden, waarschuwingsernstniveaus en het weergeven en bijhouden van waarschuwingen configureren. Daarnaast bevat het waarschuwingssnelreferentietabellen, waarmee u snel een specifieke waarschuwing vinden en adequaat reageren.

![Pagina Waarschuwingen](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Waarschuwingsinstellingen configureren

U kiezen of u per e-mail op de hoogte wilt worden gesteld van de waarschuwingsvoorwaarden voor elk van uw StorSimple Virtual Arrays. Bovendien u andere ontvangers van waarschuwingen identificeren door hun e-mailadressen in te voeren in het vak **Extra e-mailontvangers,** gescheiden door puntkomma's.

> [!NOTE]
> U maximaal 20 e-mailadressen per virtuele array invoeren.

Nadat u e-mailmelding inschakelt voor een virtuele array, ontvangen leden van de meldingslijst elke keer dat er een kritieke waarschuwing plaatsvindt een e-mailbericht. De berichten worden verzonden vanuit *storsimple-alerts-noreply\@mail.windowsazure.com* en beschrijven de waarschuwingstoestand. Ontvangers kunnen op **Afmelden** klikken om zichzelf uit de lijst met e-mailmeldingen te verwijderen.

#### <a name="to-enable-email-notification-for-alerts"></a>E-mailmelding inschakelen voor waarschuwingen

1. Ga naar uw StorSimple Device Manager-service en selecteer en klik in de sectie **Beheer** op **Apparaten**. Selecteer en klik op uw apparaat in de lijst met weergegeven apparaten.
   
    ![waarschuwingsinstellingen](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Hiermee wordt het **mes Instellingen** geopend. **Selecteer**Algemeen in de sectie **Apparaatinstellingen** . Hiermee opent u het **mes Algemene instellingen.**
   
    ![meldingen meldingsconfiguratie](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. Ga in het blad **Algemene instellingen** naar de sectie **Instellingen waarschuwen** en stel het volgende in:
   
   1. Selecteer **JA**in het veld **E-mailmelding inschakelen** .
   2. Selecteer in het veld **E-mailservicebeheerders** de optie **JA** als u de servicebeheerder wilt hebben en alle medebeheerders de waarschuwingsmeldingen ontvangen.
   3. Voer in het veld **Extra e-mailontvangers** de e-mailadressen in van alle andere ontvangers die de waarschuwingsmeldingen moeten ontvangen. Voer namen in de notatie in *die iemand\@somewhere.com*. Gebruik puntkomma's om de e-mailadressen te scheiden. U maximaal 20 e-mailadressen per virtueel apparaat configureren.
      
       ![meldingen meldingsconfiguratie](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Als u een e-mailmelding voor een test wilt verzenden, klikt u op **Test-e-mail verzenden**. De StorSimple Device Manager-service geeft statusberichten weer terwijl deze de testmelding doorstuurt.
      
       ![Waarschuwingen test melding e-mail verzonden](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Als het testmeldingsbericht niet kan worden verzonden, wordt er met de StorSimple Device Manager-service een geschikt bericht weergegeven. Klik **op OK,** wacht een paar minuten en probeer vervolgens het testmeldingsbericht opnieuw te verzenden.
      >
      >
   5. Klik onder aan de pagina op **Opslaan** om uw configuratie op te slaan. Klik op **Ja** als u om bevestiging wordt gevraagd.
      
      ![Waarschuwingen test melding e-mail verzonden](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Algemene waarschuwingsvoorwaarden

Uw StorSimple Virtual Array genereert waarschuwingen in reactie op verschillende omstandigheden. De volgende zijn de meest voorkomende typen waarschuwingsvoorwaarden:

* **Verbindingsproblemen** – Deze waarschuwingen treden op wanneer het moeilijk is om gegevens over te dragen. Communicatieproblemen kunnen optreden tijdens de overdracht van gegevens van en naar het Azure-opslagaccount of door een gebrek aan connectiviteit tussen de virtuele apparaten en de StorSimple Device Manager-service. Communicatie problemen zijn enkele van de moeilijkste op te lossen, omdat er zo veel punten van mislukking. Controleer altijd eerst of netwerkconnectiviteit en internettoegang beschikbaar zijn voordat u verdergaat met meer geavanceerde probleemoplossing. Ga voor informatie over poorten en firewall-instellingen naar [de systeemvereisten van StorSimple Virtual Array.](storsimple-ova-system-requirements.md) Ga voor hulp bij het oplossen van problemen naar [Probleemoplossen met de cmdlet Test-Connection.](storsimple-troubleshoot-deployment.md)
* **Prestatieproblemen** : deze waarschuwingen worden veroorzaakt wanneer uw systeem niet optimaal presteert, bijvoorbeeld wanneer het onder zware belasting staat.

Bovendien ziet u mogelijk waarschuwingen met betrekking tot beveiliging, updates of taakfouten.

## <a name="alert-severity-levels"></a>Ernstniveaus voor waarschuwingen

Waarschuwingen hebben verschillende ernstniveaus, afhankelijk van de impact die de waarschuwingssituatie zal hebben en de noodzaak van een reactie op de waarschuwing. Dit zijn de vijf ernstniveaus:

* **Kritiek** : deze waarschuwing is een reactie op een aandoening die de succesvolle prestaties van uw systeem beïnvloedt. Er is actie nodig om ervoor te zorgen dat de StorSimple-service niet wordt onderbroken.
* **Waarschuwing** : deze voorwaarde kan kritiek worden als deze niet is opgelost. U moet de situatie onderzoeken en alle maatregelen nemen die nodig zijn om het probleem op te lossen.
* **Informatie** – Deze waarschuwing bevat informatie die nuttig kan zijn bij het volgen en beheren van uw systeem.

## <a name="view-and-track-alerts"></a>Waarschuwingen weergeven en bijhouden

Het storSimple Device Manager-serviceoverzichtsblad biedt u een snelle blik op het aantal waarschuwingen op uw virtuele apparaten, gerangschikt op ernstniveau.

![Dashboard Waarschuwingen](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klik op het ernstniveau om het **zwaard van waarschuwingen** te openen. De resultaten omvatten alleen de waarschuwingen die overeenkomen met dat ernstniveau.

![Waarschuwingen rapport scoped to alert type Alerts](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Klik op een waarschuwing in de lijst om aanvullende details voor de waarschuwing te krijgen, inclusief de laatste keer dat de waarschuwing is gerapporteerd, het aantal exemplaren van de waarschuwing op het apparaat en de aanbevolen actie om de waarschuwing op te lossen.

![Lijst met waarschuwingen en details](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

U de waarschuwingsgegevens naar een tekstbestand kopiëren als u de informatie naar Microsoft Support wilt verzenden. Nadat u de aanbeveling hebt opgevolgd en de waarschuwingsvoorwaarde on-premises hebt opgelost, moet u de waarschuwing uit de lijst wissen. Selecteer de waarschuwing in de lijst en klik op **Wissen**. Als u meerdere waarschuwingen wilt wissen, selecteert u elke waarschuwing, klikt u op een kolom behalve de **kolom Waarschuwing** en klikt u vervolgens op **Wissen** nadat u alle waarschuwingen hebt geselecteerd die moeten worden gewist.

Wanneer u op **Wissen**klikt, krijgt u de mogelijkheid om opmerkingen te geven over de waarschuwing en de stappen die u hebt genomen om het probleem op te lossen.

![waarschuwingsopmerkingen](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Sommige gebeurtenissen worden door het systeem gewist als een andere gebeurtenis wordt geactiveerd met nieuwe informatie.

## <a name="sort-and-review-alerts"></a>Waarschuwingen sorteren en controleren

Het **waarschuwingsblad** kan maximaal 250 waarschuwingen weergeven. Als u dat aantal waarschuwingen hebt overschreden, worden niet alle waarschuwingen weergegeven in de standaardweergave. U de volgende velden combineren om aan te passen welke waarschuwingen worden weergegeven:

* **Status** : u **actieve** of **gewiste** waarschuwingen weergeven. Actieve waarschuwingen worden nog steeds geactiveerd op uw systeem, terwijl gewiste waarschuwingen handmatig zijn gewist door een beheerder of programmatisch zijn gewist omdat het systeem de waarschuwingstoestand heeft bijgewerkt met nieuwe informatie.
* **Ernst** : u waarschuwingen weergeven van alle ernstniveaus (kritiek, waarschuwing, informatie) of slechts een bepaalde ernst, zoals alleen kritieke waarschuwingen.
* **Bron** : u waarschuwingen van alle bronnen weergeven of de waarschuwingen beperken tot meldingen die afkomstig zijn van de service of een of alle virtuele apparaten.
* **Tijdsbereik** : door de **van-** en naar-datums en tijdstempels **op te** geven, u waarschuwingen bekijken gedurende de periode waarin u geïnteresseerd bent.

## <a name="alerts-quick-reference"></a>Snelle naslaginformatie waarschuwingen

In de volgende tabellen worden enkele van de StorSimple-waarschuwingen vermeld die u tegenkomen, evenals aanvullende informatie en aanbevelingen indien beschikbaar. StorSimple Virtual Array-waarschuwingen vallen in een van de volgende categorieën:

* [Waarschuwingen voor cloudconnectiviteit](#cloud-connectivity-alerts)
* [Configuratiewaarschuwingen](#configuration-alerts)
* [Waarschuwingen voor het mislukken](#job-failure-alerts)
* [Prestatiewaarschuwingen](#performance-alerts)
* [Beveiligingswaarschuwingen](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Waarschuwingen voor cloudconnectiviteit

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Apparaat <*apparaatnaam*> is niet verbonden met de cloud. |Het benoemde apparaat kan geen verbinding maken met de cloud. |Kon geen verbinding maken met de cloud. Dit kan een van de volgende oorzaken hebben:<ul><li>Er kan een probleem zijn met de netwerkinstellingen op uw apparaat.</li><li>Er kan een probleem zijn met de referenties van de opslagaccount.</li></ul>Ga voor meer informatie over het oplossen van verbindingsproblemen naar de [lokale webgebruikersinterface](storsimple-ova-web-ui-admin.md) van het apparaat. |

### <a name="configuration-alerts"></a>Configuratiewaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| On-premises virtuele apparaatconfiguratie wordt niet ondersteund. |Trage prestaties. |De huidige configuratie kan leiden tot prestatiedegradatie. Zorg ervoor dat uw server voldoet aan de minimale configuratievereisten. Ga voor meer informatie naar [StorSimple Virtual Array Requirements](storsimple-ova-system-requirements.md). |
| U hebt geen ingerichte schijfruimte meer op <naam\>van het *apparaat.* |Schijfruimtewaarschuwing. |U hebt bijna geen ingerichte schijfruimte meer. Als u ruimte wilt vrijmaken, u overwegen workloads naar een ander volume te verplaatsen of gegevens te delen of te verwijderen. |

### <a name="job-failure-alerts"></a>Waarschuwingen voor het mislukken

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Back-up van <*naam* \> van het apparaat kan niet worden voltooid. |Back-up taak fout. |Kon geen back-up maken. Overweeg een van de volgende opties:<ul><li>Verbindingsproblemen kunnen voorkomen dat de back-upbewerking wordt voltooid. Zorg ervoor dat er geen verbindingsproblemen zijn. Ga voor meer informatie over het oplossen van verbindingsproblemen naar de [lokale web-gebruikersinterface](storsimple-ova-web-ui-admin.md) voor uw virtuele apparaat.</li><li>U hebt de beschikbare opslaglimiet bereikt. Als u ruimte wilt vrijmaken, u overwegen back-ups te verwijderen die niet langer nodig zijn.</li></ul> Los de problemen op, schakel de waarschuwing uit en probeer de bewerking opnieuw. |
| Kloon van <*apparaatnaam* \> kon niet worden voltooid. |Kloon baan falen. |Kon geen kloon maken. Overweeg een van de volgende opties:<ul><li>Uw back-uplijst is mogelijk niet geldig. Vernieuw de lijst om te controleren of deze nog steeds geldig is.</li><li>Verbindingsproblemen kunnen voorkomen dat de kloonbewerking wordt voltooid. Zorg ervoor dat er geen verbindingsproblemen zijn.</li><li>U hebt de beschikbare opslaglimiet bereikt. Als u ruimte wilt vrijmaken, u overwegen back-ups te verwijderen die niet langer nodig zijn.</li></ul>Los de problemen op, schakel de waarschuwing uit en probeer de bewerking opnieuw. |

### <a name="networking-alerts"></a>Netwerkwaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Kon geen verbinding maken met de verificatieservice. |Gegevenspadfout |De URL die wordt gebruikt om te verifiëren is niet bereikbaar. Zorg ervoor dat uw firewallregels de URL-patronen bevatten die zijn opgegeven voor het StorSimple-apparaat. Ga voor meer informatie over URL-patronen in azure portal naar [StorSimple Virtual Array-netwerkvereisten](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Prestatiewaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| U ondervindt onverwachte vertragingen in de gegevensoverdracht. |Langzame gegevensoverdracht. |Er treden fouten op als u de schaalbaarheidsdoelen van een opslagservice overschrijdt. De opslagservice doet dit om ervoor te zorgen dat geen enkele client of tenant de service kan gebruiken ten koste van anderen. Ga voor meer informatie over het oplossen van problemen met uw Azure-opslagaccount naar [Controleren, diagnosticeren en problemen oplossen van Microsoft Azure Storage.](../storage/common/storage-monitoring-diagnosing-troubleshooting.md) |
| U hebt bijna geen ruimte voor lokale reserveringsschijf op <*apparaatnaam.*\> |Trage reactietijd. |10% van de totale ingerichte grootte voor <*apparaatnaam* \> is gereserveerd op het lokale apparaat en u hebt nu bijna geen gereserveerde ruimte meer. De werkbelasting op <*apparaatnaam* \> genereert een hoger verlooptarief of u hebt onlangs een grote hoeveelheid gegevens gemigreerd. Dit kan leiden tot verminderde prestaties. Overweeg een van de volgende acties om dit op te lossen:<ul><li>Verhoog de bandbreedte in de cloud naar dit apparaat.</li><li>Workloads verminderen of verplaatsen naar een ander volume of aandeel.</li></ul> |

### <a name="security-alerts"></a>Beveiligingswaarschuwingen

| Waarschuwingstekst | Gebeurtenis | Meer informatie / aanbevolen acties |
|:--- |:--- |:--- |
| Wachtwoord voor <*apparaatnaam* \> verloopt binnen <*aantaldagen.* \> |Wachtwoordwaarschuwing. |Uw wachtwoord verloopt binnen <*aantaldagen.* \> Overweeg uw wachtwoord te wijzigen. Ga voor meer informatie naar [Het wachtwoord van de storSimple Virtual Array-apparaatbeheerder wijzigen.](storsimple-virtual-array-change-device-admin-password.md) |

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over de StorSimple Virtual Array](storsimple-ova-overview.md).

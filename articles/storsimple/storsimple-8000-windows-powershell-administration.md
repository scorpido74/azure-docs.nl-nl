---
title: PowerShell voor StorSimple-apparaatbeheer
description: Meer informatie over het gebruik van Windows PowerShell voor StorSimple om uw StorSimple-apparaat te beheren.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7d59f00d655bc7b2395c46713a56f52c61ffa42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277108"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Een apparaat beheren met Windows PowerShell voor StorSimple

## <a name="overview"></a>Overzicht

Windows PowerShell voor StorSimple biedt een command-line interface die u gebruiken om uw Microsoft Azure StorSimple-apparaat te beheren. Zoals de naam al doet vermoeden, is het een Op Windows PowerShell gebaseerde, command-line interface die is gebouwd in een beperkte runspace. Vanuit het perspectief van de gebruiker op de opdrachtregel wordt een beperkte runspace weergegeven als een beperkte versie van Windows PowerShell. Met behoud van een aantal van de basismogelijkheden van Windows PowerShell, deze interface heeft extra speciale cmdlets die zijn gericht op het beheer van uw Microsoft Azure StorSimple apparaat.

In dit artikel worden de functies van Windows PowerShell voor StorSimple beschreven, inclusief hoe u verbinding maken met deze interface en bevat het koppelingen naar stapsgewijze procedures of werkstromen die u met deze interface uitvoeren. De werkstromen omvatten het registreren van uw apparaat, het configureren van de netwerkinterface op uw apparaat, het installeren van updates waarvoor het apparaat in de onderhoudsmodus moet staan, de apparaatstatus wijzigen en eventuele problemen die u ondervinden, moeten oplossen.

Na het lezen van dit artikel, zult u in staat zijn om:

* Maak verbinding met uw StorSimple-apparaat met Windows PowerShell voor StorSimple.
* Beheer uw StorSimple-apparaat met Windows PowerShell voor StorSimple.
* Hulp krijgen in Windows PowerShell voor StorSimple.

> [!NOTE]
> * Met Windows PowerShell voor StorSimple-cmdlets u uw StorSimple-apparaat beheren vanaf een seriële console of op afstand via Windows PowerShell-remoting. Voor meer informatie over elk van de afzonderlijke cmdlets die in deze interface kunnen worden gebruikt, gaat u naar [cmdlet-verwijzing voor Windows PowerShell voor StorSimple.](https://technet.microsoft.com/library/dn688168.aspx)
> * De Azure PowerShell StorSimple-cmdlets zijn een andere verzameling cmdlets waarmee u StorSimple-serviceniveau- en migratietaken vanaf de opdrachtregel automatiseren. Ga voor meer informatie over de Azure PowerShell-cmdlets voor StorSimple naar de [azure StorSimple-cmdlet-verwijzing](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


U hebt toegang tot de Windows PowerShell voor StorSimple met een van de volgende methoden:

* [Verbinding maken met storSimple-apparaatseriële console](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Op afstand verbinding maken met StorSimple met Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Verbinding maken met Windows PowerShell voor StorSimple via de seriële console van het apparaat

U [PuTTY](https://www.putty.org/) of vergelijkbare terminalemulatiesoftware downloaden om verbinding te maken met Windows PowerShell voor StorSimple. U moet PuTTY specifiek configureren om toegang te krijgen tot het Microsoft Azure StorSimple-apparaat. De volgende onderwerpen bevatten gedetailleerde stappen over het configureren van PuTTy en verbinding maken met het apparaat. Verschillende menu-opties in de seriële console worden ook uitgelegd.

### <a name="putty-settings"></a>Instellingen voor PuTTY

Zorg ervoor dat u de volgende PuTTY-instellingen gebruikt om verbinding te maken met de Windows PowerShell-interface vanaf de seriële console.

#### <a name="to-configure-putty"></a>PuTTY configureren

1. Selecteer Toetsenbord in het dialoogvenster **PuTTY Herconfiguratie** in het deelvenster **Categorie** . **Keyboard**
2. Zorg ervoor dat de volgende opties zijn geselecteerd (dit zijn de standaardinstellingen wanneer u een nieuwe sessie start).
   
   | Toetsenborditem | Selecteer |
   | --- | --- |
   | Backspace |Controle-? (127) |
   | Toetsen thuis en einde |Standard |
   | Functietoetsen en toetsenbord |ESC[n~ |
   | Initiële status van cursortoetsen |Normaal |
   | Initiële status van numeriek toetsenblok |Normaal |
   | Extra toetsenbordfuncties inschakelen |Control-Alt is anders dan AltGr |
   
    ![Ondersteunde putty-instellingen](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klik op **Toepassen**.
4. Selecteer **Vertalen**in het deelvenster **Categorie** .
5. Selecteer **UTF-8**in het **lijstmetje Voortekenset** voor extern tekens .
6. Selecteer **onder Het verwerken van tekens voor lijntekenen**de optie **Unicode-lijntekencodepunten gebruiken**. De volgende screenshot toont de juiste PuTTY selecties.
   
    ![UTF-putty-instellingen](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klik op **Toepassen**.

U PuTTY nu gebruiken om verbinding te maken met de seriële console van het apparaat door de volgende stappen uit te voeren.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Over de seriële console

Wanneer u via de seriële console de Windows PowerShell-interface van uw StorSimple-apparaat opent, wordt een bannerbericht gepresenteerd, gevolgd door menuopties.

Het bannerbericht bevat basisgegevens van storsimple-apparaten, zoals het model, de naam, de geïnstalleerde softwareversie en de status van de controller die u gebruikt. In de volgende afbeelding ziet u een voorbeeld van een bannerbericht.

![Seriële bannerbericht](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> U het bannerbericht gebruiken om te bepalen of de controller met wie u bent verbonden _actief_ of _passief_is.

In de volgende afbeelding ziet u de verschillende runspace-opties die beschikbaar zijn in het seriële consolemenu.

![Registreer uw apparaat 2](./media/storsimple-windows-powershell-administration/IC740906.png)

U kiezen uit de volgende instellingen:

1. **Log in met volledige toegang** Met deze optie u (met de juiste referenties) verbinding maken met de **Runspace van SSAdminConsole** op de lokale controller. (De lokale controller is de controller die u momenteel gebruikt via de seriële console van uw StorSimple-apparaat.) Deze optie kan ook worden gebruikt om Microsoft Support toegang te geven tot onbeperkte runspace (een ondersteuningssessie) om eventuele apparaatproblemen op te lossen. Nadat u optie 1 hebt gebruikt om u aan te melden, u de Microsoft Support-technicus toegang geven tot onbeperkte runspace door een specifieke cmdlet uit te voeren. Zie voor meer informatie [een ondersteuningssessie starten](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Inloggen bij peer controller met volledige toegang** Deze optie is hetzelfde als optie 1, behalve dat u verbinding maken (met de juiste referenties) met de **Runspace van SSAdminConsole** op de peer controller. Omdat het StorSimple-apparaat een apparaat met hoge beschikbaarheid is met twee controllers in een actief-passieve configuratie, verwijst peer naar de andere controller in het apparaat dat u via de seriële console gebruikt).
   Net als bij optie 1 kan deze optie ook worden gebruikt om Microsoft Support toegang te geven tot onbeperkte runspace op een peer controller.

3. **Verbinding maken met beperkte toegang** Deze optie wordt gebruikt om toegang te krijgen tot de Windows PowerShell-interface in beperkte modus. U wordt niet gevraagd om toegangsreferenties. Deze optie maakt verbinding met een beperktere runspace in vergelijking met opties 1 en 2.  Enkele van de taken die beschikbaar zijn via optie 1 die **niet kan* worden uitgevoerd in deze runspace zijn:
   
   * Opnieuw instellen op de fabrieksinstellingen
   * Het wachtwoord wijzigen
   * Ondersteuningstoegang in- of uitschakelen
   * Updates toepassen
   * Hotfixes installeren

     > [!NOTE]
     > Dit is de voorkeursoptie als u het wachtwoord van de apparaatbeheerder bent vergeten en geen verbinding maken via optie 1 of 2.

4. **Taal wijzigen** Met deze optie u de weergavetaal wijzigen op de Windows PowerShell-interface. De ondersteunde talen zijn Engels, Japans, Russisch, Frans, Zuid-Koreaans, Spaans, Italiaans, Duits, Chinees en Braziliaans Portugees.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Op afstand verbinding maken met StorSimple met Windows PowerShell voor StorSimple

U Windows PowerShell remoting gebruiken om verbinding te maken met uw StorSimple-apparaat. Wanneer u op deze manier verbinding maakt, ziet u geen menu. (U ziet alleen een menu als u de seriële console op het apparaat gebruikt om verbinding te maken. Op afstand verbinden brengt u rechtstreeks naar het equivalent van "optie 1 – volledige toegang" op de seriële console.) Met Windows PowerShell remoting maakt u verbinding met een specifieke runspace. U ook de weergavetaal opgeven.

De weergavetaal is onafhankelijk van de taal die u instelt met de optie **Taal wijzigen** in het seriële consolemenu. Remote PowerShell pikt automatisch de landintuur op van het apparaat waarvan u verbinding maakt als er geen is opgegeven.

> [!NOTE]
> Als u werkt met virtuele hosts van Microsoft Azure en StorSimple Cloud Appliances, u Windows PowerShell remoting en de virtuele host gebruiken om verbinding te maken met het cloudtoestel. Als u een sharelocatie op de host hebt ingesteld waarop u informatie uit de Windows PowerShell-sessie opslaan, moet u zich ervan bewust zijn dat de _principal van Iedereen_ alleen geverifieerde gebruikers bevat. Daarom, als u het aandeel hebt ingesteld om toegang door _Iedereen_ toe te staan en u verbinding maakt zonder referenties op te geven, wordt de niet-geverifieerde Anonieme principal gebruikt en ziet u een fout. Om dit probleem op te lossen, moet u op de sharehost het gastaccount inschakelen en vervolgens het gastaccount volledige toegang geven tot het aandeel of moet u geldige referenties opgeven, samen met de Windows PowerShell-cmdlet.


U HTTP of HTTPS gebruiken om verbinding te maken via Windows PowerShell remoting. Gebruik de instructies in de volgende zelfstudies:

* [Op afstand verbinding maken via HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Op afstand verbinding maken met HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Overwegingen voor verbindingsbeveiliging

Wanneer u beslist hoe u verbinding maakt met Windows PowerShell voor StorSimple, houdt u het volgende in:

* Rechtstreeks verbinding maken met de seriële console van het apparaat is veilig, maar verbinding maken met de seriële console via netwerkswitches is dat niet. Wees voorzichtig met het beveiligingsrisico bij het maken van verbinding met apparaatseriële via netwerkswitches.
* Verbinding maken via een HTTP-sessie biedt mogelijk meer beveiliging dan verbinding maken via de seriële console via het netwerk. Hoewel dit niet de meest veilige methode is, is het acceptabel op vertrouwde netwerken.
* Verbinding maken via een HTTPS-sessie is de meest veilige en de aanbevolen optie.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Beheer uw StorSimple-apparaat met Windows PowerShell voor StorSimple

In de volgende tabel ziet u een overzicht van alle algemene beheertaken en complexe werkstromen die kunnen worden uitgevoerd binnen de Windows PowerShell-interface van uw StorSimple-apparaat. Klik op het juiste item in de tabel voor meer informatie over elke werkstroom.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell voor StorSimple-werkstromen

| Als u dit wilt doen ... | Gebruik deze procedure. |
| --- | --- |
| Uw apparaat registreren |[Het apparaat configureren en registreren met Windows PowerShell voor StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Webproxy configureren</br>Webproxy-instellingen weergeven |[Webproxy configureren voor uw StorSimple-apparaat](storsimple-8000-configure-web-proxy.md) |
| Instellingen voor gegevens0-netwerkinterface op uw apparaat wijzigen |[Gegevens 0-netwerkinterface voor uw StorSimple-apparaat wijzigen](storsimple-8000-modify-data-0.md) |
| Een controller stoppen </br> Een controller opnieuw starten of uitschakelen </br> Een apparaat uitschakelen</br>Het apparaat terugzetten naar de fabrieksinstellingen |[Apparaatcontrollers beheren](storsimple-8000-manage-device-controller.md) |
| Updates en hotfixes in de onderhoudsmodus installeren |[Uw apparaat bijwerken](storsimple-update-device.md) |
| Voer de onderhoudsmodus in </br>Onderhoudsmodus afsluiten |[StorSimple-apparaatmodi](storsimple-8000-device-modes.md) |
| Een ondersteuningspakket maken</br>Een ondersteuningspakket decoderen en bewerken |[Een ondersteuningspakket maken en beheren](storsimple-8000-create-manage-support-package.md) |
| Een ondersteuningssessie starten</br> |[Een ondersteuningssessie starten in Windows PowerShell voor StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Hulp krijgen in Windows PowerShell voor StorSimple

In Windows PowerShell voor StorSimple is cmdlet Help beschikbaar. Er is ook een online, up-to-date versie van deze Help beschikbaar, waarmee u de Help op uw systeem bijwerken.

Hulp krijgen in deze interface is vergelijkbaar met die in Windows PowerShell, en de meeste Help-gerelateerde cmdlets werken. U Help voor Windows PowerShell online vinden: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Het volgende is een korte beschrijving van de typen Help voor deze Windows PowerShell-interface, inclusief hoe u de Help bijwerken.

### <a name="to-get-help-for-a-cmdlet"></a>Om hulp te krijgen voor een cmdlet

* Als u Help wilt krijgen voor een cmdlet of functie, gebruikt u de volgende opdracht:`Get-Help <cmdlet-name>`
* Gebruik de vorige cmdlet met de `-Online` parameter om online Help voor elke cmdlet te krijgen:`Get-Help <cmdlet-name> -Online`
* Voor volledige Help `–Full` u de parameter gebruiken en `–Examples` bijvoorbeeld de parameter gebruiken.

### <a name="to-update-help"></a>Help bijwerken

U de Help eenvoudig bijwerken in de Windows PowerShell-interface. Voer de volgende stappen uit om de Help op uw systeem bij te werken.

#### <a name="to-update-cmdlet-help"></a>Cmdlet Help bijwerken
1. Start Windows PowerShell met de optie **Uitvoeren als administrator.**
2. Typ bij de opdrachtprompt:`Update-Help`
3. De bijgewerkte Help-bestanden worden geïnstalleerd.
4. Nadat de Help-bestanden zijn `Get-Help Get-Command`geïnstalleerd, typt u: . Hierziet een lijst met cmdlets waarvoor Help beschikbaar is.

> [!NOTE]
> Als u een lijst wilt maken met alle beschikbare cmdlets in een `Get-Command` runspace, logt u in bij de bijbehorende menuoptie en voert u de cmdlet uit.


## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt met uw StorSimple-apparaat bij het uitvoeren van een van de bovenstaande werkstromen, raadpleegt u [Extra voor het oplossen van problemen met StorSimple-implementaties.](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)


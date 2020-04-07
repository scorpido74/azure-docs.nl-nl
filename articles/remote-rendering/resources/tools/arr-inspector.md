---
title: De ArrInspector inspectietool
description: Gebruikershandleiding van het gereedschap ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680074"
---
# <a name="the-arrinspector-inspection-tool"></a>De ArrInspector inspectietool

De ArrInspector is een webgebaseerd hulpmiddel dat wordt gebruikt om een uitvoerende Azure Remote Rendering-sessie te inspecteren. Het is bedoeld om te worden gebruikt voor foutopsporingsdoeleinden, om de structuur van de scène die wordt weergegeven te inspecteren, de logboekberichten weer te geven en de live prestaties op de server te controleren.

![ArrInspector ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Verbinding maken met de ArrInspector

Zodra u de hostnaam `mixedreality.azure.com`(eindigend in) van uw ARR-server hebt verkregen, maakt u verbinding via [ConnectToArrInspectorAsync.](../../how-tos/frontend-apis.md#connect-to-arr-inspector) Met deze `StartArrInspector.html` functie wordt een op het apparaat waarop de toepassing wordt uitgevoerd. Als u ArrInspector wilt starten, opent u dat bestand met een browser (Edge, Firefox of Chrome) op een pc. Het bestand is slechts 24 uur geldig.

Als de app `ConnectToArrInspectorAsync` die aanbelt al op een pc wordt uitgevoerd:

* Als u de Unity-integratie gebruikt, kan deze automatisch voor u worden gestart.
* Anders vindt u het bestand in *\\Gebruikersmappen\\LocalAppData\\[your_app] AC\\Temp.*

Als de app op een HoloLens wordt uitgevoerd:

1. Toegang tot de HoloLens via de [Windows Device Portal](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
1. Ga naar *System > File Explorer*.
1. Navigeer naar *LocalAppData\\\\voor gebruikersmappen [your_app]\\\\AC Temp*.
1. Sla *StartArrInspector.html op* uw pc op.
1. Open *StartArrInspector.html* om de ArrInspector van de sessie te laden.

## <a name="the-performance-panel"></a>Het deelvenster Prestaties

![Het prestatiepaneel](./media/performance-panel.png)

In dit deelvenster ziet u grafieken van alle prestatiewaarden per frame die door de server worden weergegeven. De waarden omvatten momenteel de frametijd, FPS, CPU en geheugengebruik, geheugenstatistieken zoals het totale RAM-gebruik, objecttellingen, enz.

Als u een van deze parameters wilt visualiseren, klikt u op de knop **Nieuw toevoegen** en selecteert u een van de beschikbare waarden in het dialoogvenster. Met deze actie wordt een nieuwe schuifgrafiek aan het deelvenster toegevoegd, waarin de waarden in realtime worden getraceerd. Rechts ziet u de *minimale,* *maximale* en *actuele* waarde.

U de grafiek pannen door de inhoud ervan met de muis te slepen, maar horizontaal pannen is alleen mogelijk wanneer ArrInspector zich in de onderbroken status bevindt.

Als u Ctrl ingedrukt houdt terwijl u sleept, u inzoomen. Horizontale zoom kan ook worden geregeld met de schuifregelaar aan de onderkant.

Het verticale bereik wordt standaard berekend op basis van de waarden die momenteel worden weergegeven en min- en max-waarden worden weergegeven in de tekstvakken aan de rechterkant. Wanneer de waarden handmatig worden ingesteld, door ze rechtstreeks in het tekstvak te typen of door te pannen/zoomen, gebruikt de grafiek deze waarden. Als u de automatische verticale framing wilt herstellen, klikt u op het pictogram in de rechterbovenhoek.

![verticaal bereik](./media/vertical-range.png)

## <a name="the-log-panel"></a>Het deelvenster Logboek

![Logboekpaneel](./media/log-panel.png)

Het logboekpaneel toont een lijst met logboekberichten die aan de serverzijde zijn gegenereerd. Op verbinding zal het tot 200 vorige logboekberichten tonen, en zullen nieuwe als zij gebeuren afdrukken.

U de lijst filteren `[Error/Warning/Info/Debug]` op basis van het logboektype met behulp van de knoppen bovenaan.
![Knopen voor logboekfilter](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Het deelvenster Tijdgegevens vastleggen

![Timing Gegevens vastleggen](./media/timing-data-capture.png)

Dit paneel wordt gebruikt om timinginformatie van de server vast te leggen en te downloaden. Het bestand maakt gebruik van de [JSON-indeling voor Chrome Tracing.](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit) Als u de gegevens wilt `Chrome://tracing` inspecteren, opent u Chrome op de URL en sleept u het gedownloade bestand naar de pagina. De timinggegevens worden continu verzameld in een ringbuffer van vaste grootte. Wanneer uitgeschreven, de vangst bevat alleen informatie over het onmiddellijke verleden, wat betekent dat een paar seconden tot enkele minuten.

## <a name="the-scene-inspection-panel"></a>Het scèneinspectiepaneel

![Scèneinspectiepaneel](./media/scene-inspection-panel.png)

Dit paneel toont de structuur van de gerenderde scène. De objecthiërarchie bevindt zich aan de linkerkant, de inhoud van het geselecteerde object bevindt zich aan de rechterkant. Het paneel is alleen-lezen en wordt in realtime bijgewerkt.

## <a name="the-vm-debug-information-panel"></a>Het deelvenster Foutopsporingsgegevens van vm's

![Vm-foutopsporingsgegevenspaneel](./media/state-debugger-panel.png)

Dit paneel biedt een aantal foutopsporingsfunctionaliteit.

### <a name="restart-service"></a>Service opnieuw starten

Met de knop **Service opnieuw opstarten** wordt de runtime opnieuw gestart op de virtuele machine waarmee arrInspector is verbonden. Elke bijgevoegde client wordt losgekoppeld en de arrInspector-pagina moet opnieuw worden geladen om verbinding te maken met de opnieuw gestarte service.

### <a name="collect-debug-information"></a>Foutopsporingsgegevens verzamelen

Met de knop **Foutopsporingsgegevens voor VM verzamelen** wordt een dialoogvenster geopend waarmee u de ARR-instantie activeren om foutopsporingsgegevens op de VM te verzamelen:

![Dialoogvenster Foutopsporingsgegevens van vm's](./media/state-debugger-dialog.png)

Foutopsporingsgegevens helpen het Azure Remote Rendering-team bij het analyseren van eventuele problemen die zich voordoen in een arr-exemplaar. Het dialoogvenster heeft een tekstveld om aanvullende details te geven, bijvoorbeeld stappen om een probleem te reproduceren.

Nadat u op de knop **Verzamelen starten** hebt geklikt, wordt het dialoogvenster gesloten en wordt het verzamelingsproces gestart. Het verzamelen van de informatie over de VM kan enkele minuten duren.

![VM-foutopsporingsgegevens verzamelen in uitvoering](./media/state-debugger-panel-in-progress.png)

Zodra de collectie is voltooid, ontvangt u een melding in het arrinspector-venster. Deze melding bevat een id die deze specifieke verzameling identificeert. Zorg ervoor dat u deze id opslaat om deze door te geven aan het Azure Remote Rendering-team.

![VM Debug Information collection succes](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> U geen VM-foutopsporingsgegevens downloaden of op een andere manier openen. Alleen het Azure Remote Rendering-team heeft toegang tot de verzamelde gegevens. U moet contact met ons opnemen en de collecte-id meesturen, om het probleem dat u ziet te onderzoeken.

## <a name="pause-mode"></a>Pauzemodus

In de rechterbovenhoek u met een schakelaar de live-update van de panelen pauzeren. Deze modus kan handig zijn om een specifieke toestand zorgvuldig te inspecteren.

![Pauzemodus](./media/pause-mode.png)

Wanneer de live-update opnieuw wordt in- en bijgewerkt, worden alle panelen opnieuw ingesteld.

## <a name="host-configuration"></a>Hostconfiguratie

Het hulpprogramma maakt standaard verbinding met de ARR-server die wordt uitgevoerd op dezelfde host die de ArrInspector bedient. U deze echter configureren om een andere server te inspecteren, ervan uitgaande dat er een ARR-exemplaar wordt uitgevoerd met de toolingpoort geopend.

Ga hiervoor naar het hoofdmenu links van de kopbalk en selecteer *Host-configuratie*. Klik **op Nieuwe host toevoegen**en voer de naam en hostnaam in. Voor *hostname* gebruikt u alleen `.mixedreality.azure.com`de hostnaam `http://` die eindigt op , geen of een poort.

![Hostconfiguratie](./media/host-configuration.png)

Als u snel van de ene host naar de andere wilt schakelen, gebruikt u de vervolgkeuzelijst rechtsboven.

![Host-combo](./media/host-switch-combo.png)

De hostlijst wordt opgeslagen in de lokale opslag van de browser, zodat deze wordt bewaard wanneer deze dezelfde browser opnieuw opent.

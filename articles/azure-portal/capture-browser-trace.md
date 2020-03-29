---
title: Een browsertracering vastleggen voor het oplossen van problemen | Microsoft Documenten
description: Leg netwerkgegevens vast uit een browsertracering om problemen met de Azure-portal op te lossen.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310693"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Een browsertracering vastleggen om problemen op te lossen

Als u een probleem met de Azure-portal oplost en u contact moet opnemen met microsoft-ondersteuning, raden we u aan eerst een browsertracering en wat aanvullende informatie vast te leggen. De informatie die u verzamelt, kan belangrijke details geven over de portal op het moment dat het probleem zich voordoet. Volg de stappen in dit artikel voor de hulpprogramma's voor ontwikkelaars in de browser die u gebruikt: Google Chrome of Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML) of Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome en Microsoft Edge (Chromium)

Google Chrome en Microsoft Edge (Chromium) zijn beide gebaseerd op het [Chromium open source project.](https://www.chromium.org/Home) De volgende stappen laten zien hoe de hulpprogramma's voor ontwikkelaars te gebruiken, die zeer vergelijkbaar zijn in de twee browsers. Zie [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) en [Microsoft Edge (Chromium) Developer Tools](/microsoft-edge/devtools-guide-chromium)voor meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Het is belangrijk om je aan te melden _voordat_ u het traceerspoor start, zodat het traceerspoor geen gevoelige informatie bevat met betrekking tot uw aanmelding. 

1. Begin met het opnemen van de stappen die u in de portal neemt met [Steps Recorder.](https://support.microsoft.com/help/22878/windows-10-record-steps)

1. Navigeer in de portal naar de stap vlak voordat het probleem zich voordoet.

1. Druk op F12 of ![selecteer](media/capture-browser-trace/chromium-icon-settings.png) > Screenshot van het pictogram browserinstellingen**Meer hulpprogramma's** > **Voor ontwikkelaars**.

1. Standaard bewaart de browser alleen traceergegevens voor de pagina die momenteel is geladen. Stel de volgende opties in, zodat de browser alle traceergegevens bijhoudt, zelfs als uw repro naar meer dan één pagina moet gaan:

    1. Selecteer het tabblad **Netwerk** en selecteer **Logboek behouden**.

          ![Schermafbeelding van 'Logboek behouden'](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Selecteer het tabblad **Console,** selecteer **Console-instellingen**en selecteer **Logboek behouden**. Selecteer **opnieuw Console-instellingen** om het instellingenvenster te sluiten.

          ![Schermafbeelding van 'Logboek behouden'](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selecteer het tabblad **Netwerk** en selecteer **Het registratienetwerklogboek stoppen** en **Wissen**.

    ![Schermafbeelding van 'Stop opname netwerklogboek' en 'Wissen'](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selecteer **Netwerklogboek opnemen**en reproduceert het probleem vervolgens in de portal.

    ![Schermafbeelding van "Profiling session starten"](media/capture-browser-trace/chromium-start-session.png)

    U ziet sessie-uitvoer vergelijkbaar met de volgende afbeelding.

    ![Schermafbeelding van de resultaten van browsertraceringsresultaten](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Nadat u het onverwachte portalgedrag hebt gereproduceerd, selecteert u **Het registratienetwerklogboek stoppen,** selecteert u **HAR exporteren** en slaat u het bestand op.

    ![Schermafbeelding van "Export HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Stop Steps Recorder en sla het bestand op.

1. Selecteer terug in het deelvenster Hulpmiddelen voor browserontwikkelaars het tabblad **Console.** Klik met de rechtermuisknop en selecteer **Opslaan als...** en sla de console-uitvoer op in een tekstbestand.

    ![Schermafbeelding van console-uitvoer](media/capture-browser-trace/chromium-console-select.png)

1. Verpak het HAR-bestand, de console-uitvoer en de schermopname in een gecomprimeerde indeling zoals .zip en deel dat met Microsoft-ondersteuning.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

In de volgende stappen wordt uitgelegd hoe u de hulpprogramma's voor ontwikkelaars gebruiken in Microsoft Edge (EdgeHTML). Zie [Microsoft Edge (EdgeHTML) Developer Tools voor](/microsoft-edge/devtools-guide)meer informatie .

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Het is belangrijk om je aan te melden _voordat_ u het traceerspoor start, zodat het traceerspoor geen gevoelige informatie bevat met betrekking tot uw aanmelding. 

1. Begin met het opnemen van de stappen die u in de portal neemt met [Steps Recorder.](https://support.microsoft.com/help/22878/windows-10-record-steps)

1. Navigeer in de portal naar de stap vlak voordat het probleem zich voordoet.

1. Druk op F12 of ![selecteer](media/capture-browser-trace/edge-icon-settings.png) > Screenshot van het pictogram browserinstellingen**Meer hulpprogramma's** > **Voor ontwikkelaars**.

1. Standaard bewaart de browser alleen traceergegevens voor de pagina die momenteel is geladen. Stel de volgende opties in, zodat de browser alle traceergegevens bijhoudt, zelfs als uw repro naar meer dan één pagina moet gaan:

    1. Selecteer het tabblad **Netwerk** en schakel de optie **Items wissen bij navigeren uit.**

          ![Schermafbeelding van 'Berichten wissen bij navigeren'](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selecteer het tabblad **Console** en selecteer **Logboek behouden**.

          ![Schermafbeelding van 'Logboek behouden'](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selecteer het tabblad **Netwerk** en selecteer **Vervolgens Profileringssessie stoppen** en Sessie **wissen**.

    ![Schermafbeelding van 'Profiling-sessie stoppen' en 'Sessie wissen'](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selecteer **Profileringssessie starten**en reproduceert het probleem vervolgens in de portal.

    ![Schermafbeelding van "Profiling session starten"](media/capture-browser-trace/edge-start-session.png)

    U ziet sessie-uitvoer vergelijkbaar met de volgende afbeelding.

    ![Schermafbeelding van de resultaten van browsertraceringsresultaten](media/capture-browser-trace/edge-browser-trace-results.png)

1. Nadat u het onverwachte portalgedrag hebt gereproduceerd, selecteert u **De profileringssessie stoppen**en selecteert u **Exporteren als HAR** en slaat u het bestand op.

    ![Schermafbeelding van "Exporteren als HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Stop Steps Recorder en sla het bestand op.

1. Selecteer het tabblad **Console** en vouw het venster uit in het deelvenster Hulpmiddelen voor browserontwikkelaars. Plaats de cursor aan het begin van de console-uitvoer en sleep en selecteer de volledige inhoud van de uitvoer. Klik met de rechtermuisknop, selecteer **Kopiëren**en sla de console-uitvoer op in een tekstbestand.

    ![Schermafbeelding van console-uitvoer](media/capture-browser-trace/edge-console-select.png)

1. Verpak het HAR-bestand, de console-uitvoer en de schermopname in een gecomprimeerde indeling zoals .zip en deel dat met Microsoft-ondersteuning.

## <a name="apple-safari"></a>Apple Safari

De volgende stappen laten zien hoe u de hulpprogramma's voor ontwikkelaars in Apple Safari gebruiken. Zie [overzicht van Safari Developer Tools voor](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)meer informatie.

1. Schakel de hulpprogramma's voor ontwikkelaars in Apple Safari in:

    1. Selecteer **Safari**en selecteer **Voorkeuren**.

        ![Schermafbeelding van Safari-voorkeuren](media/capture-browser-trace/safari-preferences.png)

    1. Selecteer het tabblad **Geavanceerd** en selecteer **Het menu Ontwikkelen weergeven in de menubalk**.

        ![Schermafbeelding van geavanceerde voorkeuren voor Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Het is belangrijk om je aan te melden _voordat_ u het traceerspoor start, zodat het traceerspoor geen gevoelige informatie bevat met betrekking tot uw aanmelding. 

1. Begin met het opnemen van de stappen die u neemt in de portal. Zie [Het scherm opnemen op uw Mac](https://support.apple.com/HT208721)voor meer informatie.

1. Navigeer in de portal naar de stap vlak voordat het probleem zich voordoet.

1. Selecteer **Ontwikkelen**en selecteer **Webcontrole weergeven**.

    ![Schermafbeelding van 'Webcontrole weergeven'](media/capture-browser-trace/safari-show-web-inspector.png)

1. Standaard bewaart de browser alleen traceergegevens voor de pagina die momenteel is geladen. Stel de volgende opties in, zodat de browser alle traceergegevens bijhoudt, zelfs als uw repro naar meer dan één pagina moet gaan:

    1. Selecteer het tabblad **Netwerk** en selecteer **Logboek behouden**.

          ![Schermafbeelding van 'Logboek behouden'](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selecteer het tabblad **Console** en selecteer **Logboek behouden**.

          ![Schermafbeelding van 'Logboek behouden'](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selecteer het tabblad **Netwerk** en selecteer **Netwerkitems wissen**.

    ![Schermafbeelding van 'Netwerkitems wissen'](media/capture-browser-trace/safari-clear-session.png)

1. Reproduceren van het probleem in het portaal. U ziet sessie-uitvoer vergelijkbaar met de volgende afbeelding.

    ![Schermafbeelding van de resultaten van browsertraceringsresultaten](media/capture-browser-trace/safari-browser-trace-results.png)

1. Nadat u het onverwachte portalgedrag hebt gereproduceerd, selecteert u **Exporteren** en het bestand opslaan.

    ![Schermafbeelding van 'Exporteren'](media/capture-browser-trace/safari-network-export-har.png)

1. Stop de schermrecorder en sla het bestand op.

1. Selecteer het tabblad **Console** en vouw het venster uit in het deelvenster Hulpmiddelen voor browserontwikkelaars. Plaats de cursor aan het begin van de console-uitvoer en sleep en selecteer de volledige inhoud van de uitvoer. Gebruik Command-C om de uitvoer te kopiëren en op te slaan in een tekstbestand.

    ![Schermafbeelding van console-uitvoer](media/capture-browser-trace/safari-console-select.png)

1. Verpak het HAR-bestand, de console-uitvoer en de schermopname in een gecomprimeerde indeling zoals .zip en deel dat met Microsoft-ondersteuning.

## <a name="next-steps"></a>Volgende stappen

[Overzicht van de Azure Portal](azure-portal-overview.md)
---
title: Een browser tracering vastleggen voor probleem oplossing | Microsoft Docs
description: Leg netwerk gegevens vast van een browser tracering om problemen met de Azure Portal op te lossen.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310693"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>Een browser tracering vastleggen voor het oplossen van problemen

Als u een probleem met de Azure Portal oplost en u contact moet opnemen met micro soft-ondersteuning, raden we u aan eerst een browser tracering en enige aanvullende informatie vast te leggen. De gegevens die u verzamelt, kunnen belang rijke informatie geven over de portal op het moment dat het probleem optreedt. Volg de stappen in dit artikel voor de ontwikkel hulpprogramma's in de browser die u gebruikt: Google Chrome of micro soft Edge (chroom), micro soft Edge (EdgeHTML) of Apple Safari.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome en micro soft Edge (zeswaardig)

Google Chrome en micro soft Edge (chroom) zijn beide gebaseerd op het [open-source-project](https://www.chromium.org/Home)van Chrome. De volgende stappen laten zien hoe u de hulpprogram ma's voor ontwikkel aars gebruikt, die vergelijkbaar zijn in de twee browsers. Zie [Chrome devtools](https://developers.google.com/web/tools/chrome-devtools) en [micro soft Edge (chroom) Ontwikkelhulpprogramma's](/microsoft-edge/devtools-guide-chromium)voor meer informatie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). Het is belang rijk dat u zich aanmeldt _voordat_ u de tracering start, zodat de tracering geen gevoelige informatie bevat met betrekking tot uw aanmelding. 

1. Begin met het vastleggen van de stappen die u in de portal uitvoert, met behulp van de [stappen beschrijving](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Navigeer in de portal naar de stap vlak voordat het probleem zich voordoet.

1. Druk op F12 of selecteer ![scherm opname van het pictogram browser instellingen](media/capture-browser-trace/chromium-icon-settings.png) > **meer hulp middelen** > **ontwikkel hulpprogramma's**.

1. De browser houdt standaard alleen tracerings informatie bij voor de pagina die momenteel wordt geladen. Stel de volgende opties zodanig in dat de browser alle tracerings gegevens houdt, zelfs als uw reproduceren naar meer dan één pagina moet gaan:

    1. Selecteer het tabblad **netwerk** en selecteer vervolgens **logboek bewaren**.

          ![Scherm opname van ' logboek bewaren '](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. Selecteer het tabblad **console** , selecteer **console-instellingen**en selecteer vervolgens **logboek bewaren**. Selecteer de **console-instellingen** opnieuw om het deel venster instellingen te sluiten.

          ![Scherm opname van ' logboek bewaren '](media/capture-browser-trace/chromium-console-preserve-log.png)

1. Selecteer het tabblad **netwerk** en selecteer vervolgens **opname van netwerk logboek stoppen** en **wissen**.

    ![Scherm opname van het opnemen van het netwerk logboek stoppen en wissen](media/capture-browser-trace/chromium-stop-clear-session.png)

1. Selecteer **netwerk logboek opnemen**en reproduceer vervolgens het probleem in de portal.

    ![Scherm opname van de "profilerings sessie starten"](media/capture-browser-trace/chromium-start-session.png)

    U ziet de uitvoer van de sessie, vergelijkbaar met de volgende afbeelding.

    ![Scherm opname van resultaten van browser tracering](media/capture-browser-trace/chromium-browser-trace-results.png)

1. Nadat u het onverwachte gedrag van de portal hebt gereproduceerd, selecteert u **netwerk logboek opname stoppen**en selecteert u vervolgens **exporteren har** en slaat u het bestand op.

    ![Scherm afbeelding van "uitvoer HAR"](media/capture-browser-trace/chromium-network-export-har.png)

1. Stop de stappen opnemen en sla het bestand op.

1. Selecteer in het deel venster ontwikkel hulpprogramma's van de browser het tabblad **console** . Klik met de rechter muisknop, selecteer vervolgens **Opslaan als...** en sla de uitvoer van de console op in een tekst bestand.

    ![Scherm opname van console-uitvoer](media/capture-browser-trace/chromium-console-select.png)

1. Pak het HAR-bestand, de console-uitvoer en de scherm opname in een gecomprimeerde indeling zoals. zip en share die met micro soft ondersteuning.

## <a name="microsoft-edge-edgehtml"></a>Micro soft Edge (EdgeHTML)

De volgende stappen laten zien hoe u de hulpprogram ma's voor ontwikkel aars in micro soft Edge (EdgeHTML) gebruikt. Zie [micro soft Edge (EdgeHTML) Ontwikkelhulpprogramma's](/microsoft-edge/devtools-guide)voor meer informatie.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). Het is belang rijk dat u zich aanmeldt _voordat_ u de tracering start, zodat de tracering geen gevoelige informatie bevat met betrekking tot uw aanmelding. 

1. Begin met het vastleggen van de stappen die u in de portal uitvoert, met behulp van de [stappen beschrijving](https://support.microsoft.com/help/22878/windows-10-record-steps).

1. Navigeer in de portal naar de stap vlak voordat het probleem zich voordoet.

1. Druk op F12 of selecteer ![scherm opname van het pictogram browser instellingen](media/capture-browser-trace/edge-icon-settings.png) > **meer hulp middelen** > **ontwikkel hulpprogramma's**.

1. De browser houdt standaard alleen tracerings informatie bij voor de pagina die momenteel wordt geladen. Stel de volgende opties zodanig in dat de browser alle tracerings gegevens houdt, zelfs als uw reproduceren naar meer dan één pagina moet gaan:

    1. Selecteer het tabblad **netwerk** en schakel vervolgens de optie **vermeldingen wissen bij navigeren**uit.

          ![Scherm opname van "vermeldingen wissen bij navigeren"](media/capture-browser-trace/edge-network-clear-entries.png)

    1. Selecteer het tabblad **console** en selecteer vervolgens **logboek bewaren**.

          ![Scherm opname van ' logboek bewaren '](media/capture-browser-trace/edge-console-preserve-log.png)

1. Selecteer het tabblad **netwerk** en selecteer vervolgens **profilerings sessie stoppen** en **sessie wissen**.

    ![Scherm opname van de ' profilerings sessie stoppen ' en ' sessie wissen '](media/capture-browser-trace/edge-stop-clear-session.png)

1. Selecteer de **profilerings sessie starten**en reproduceer vervolgens het probleem in de portal.

    ![Scherm opname van de "profilerings sessie starten"](media/capture-browser-trace/edge-start-session.png)

    U ziet de uitvoer van de sessie, vergelijkbaar met de volgende afbeelding.

    ![Scherm opname van resultaten van browser tracering](media/capture-browser-trace/edge-browser-trace-results.png)

1. Nadat u het onverwachte gedrag van de portal hebt gereproduceerd, selecteert u de optie **profilerings sessie stoppen**en selecteert u vervolgens **exporteren als har** en slaat u het bestand op.

    ![Scherm afbeelding van "exporteren als HAR"](media/capture-browser-trace/edge-network-export-har.png)

1. Stop de stappen opnemen en sla het bestand op.

1. Ga terug naar het deel venster ontwikkel hulpprogramma's van de browser, selecteer het tabblad **console** en vouw het venster uit. Plaats de cursor aan het begin van de console-uitvoer en sleep en selecteer de volledige inhoud van de uitvoer. Klik met de rechter muisknop, selecteer **kopiëren**en sla de console-uitvoer op in een tekst bestand.

    ![Scherm opname van console-uitvoer](media/capture-browser-trace/edge-console-select.png)

1. Pak het HAR-bestand, de console-uitvoer en de scherm opname in een gecomprimeerde indeling zoals. zip en share die met micro soft ondersteuning.

## <a name="apple-safari"></a>Apple Safari

De volgende stappen laten zien hoe u de hulpprogram ma's voor ontwikkel aars in Apple Safari kunt gebruiken. Zie [overzicht van Safari Ontwikkelhulpprogramma's](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)voor meer informatie.

1. De hulpprogram ma's voor ontwikkel aars in Apple Safari inschakelen:

    1. Selecteer **Safari**en selecteer vervolgens **voor keuren**.

        ![Scherm opname van Safari-voor keuren](media/capture-browser-trace/safari-preferences.png)

    1. Selecteer het tabblad **Geavanceerd** en selecteer vervolgens het **menu ontwikkelen weer geven in de menu balk**.

        ![Scherm opname van geavanceerde voor keuren voor Safari](media/capture-browser-trace/safari-show-develop-menu.png)

1. Meld u aan bij de [Azure Portal](https://portal.azure.com). Het is belang rijk dat u zich aanmeldt _voordat_ u de tracering start, zodat de tracering geen gevoelige informatie bevat met betrekking tot uw aanmelding. 

1. Begin met het vastleggen van de stappen die u in de portal uitvoert. Zie [het scherm op uw Mac vastleggen](https://support.apple.com/HT208721)voor meer informatie.

1. Navigeer in de portal naar de stap vlak voordat het probleem zich voordoet.

1. Selecteer **ontwikkelen**en selecteer vervolgens **Web-Inspector weer geven**.

    ![Scherm opname van ' webinspector weer geven '](media/capture-browser-trace/safari-show-web-inspector.png)

1. De browser houdt standaard alleen tracerings informatie bij voor de pagina die momenteel wordt geladen. Stel de volgende opties zodanig in dat de browser alle tracerings gegevens houdt, zelfs als uw reproduceren naar meer dan één pagina moet gaan:

    1. Selecteer het tabblad **netwerk** en selecteer vervolgens **logboek bewaren**.

          ![Scherm opname van ' logboek bewaren '](media/capture-browser-trace/safari-network-preserve-log.png)

    1. Selecteer het tabblad **console** en selecteer vervolgens **logboek bewaren**.

          ![Scherm opname van ' logboek bewaren '](media/capture-browser-trace/safari-console-preserve-log.png)

1. Selecteer het tabblad **netwerk** en selecteer vervolgens **netwerk items wissen**.

    ![Scherm opname van ' netwerk items wissen '](media/capture-browser-trace/safari-clear-session.png)

1. Reproduceer het probleem in de portal. U ziet de uitvoer van de sessie, vergelijkbaar met de volgende afbeelding.

    ![Scherm opname van resultaten van browser tracering](media/capture-browser-trace/safari-browser-trace-results.png)

1. Nadat u het onverwachte gedrag van de portal hebt gemaakt, selecteert u **exporteren** en slaat u het bestand op.

    ![Scherm opname van "exporteren"](media/capture-browser-trace/safari-network-export-har.png)

1. Stop de scherm opname en sla het bestand op.

1. Ga terug naar het deel venster ontwikkel hulpprogramma's van de browser, selecteer het tabblad **console** en vouw het venster uit. Plaats de cursor aan het begin van de console-uitvoer en sleep en selecteer de volledige inhoud van de uitvoer. Gebruik opdracht-C om de uitvoer te kopiëren en op te slaan in een tekst bestand.

    ![Scherm opname van console-uitvoer](media/capture-browser-trace/safari-console-select.png)

1. Pak het HAR-bestand, de console-uitvoer en de scherm opname in een gecomprimeerde indeling zoals. zip en share die met micro soft ondersteuning.

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Azure Portal](azure-portal-overview.md)
---
title: Problemen met Azure Data Factory UX oplossen
description: Meer informatie over het oplossen van problemen met Azure Data Factory UX.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567949"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Problemen met Azure Data Factory UX oplossen
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden voor Azure Data Factory UX besproken.

## <a name="adf-ux-not-loading"></a>ADF UX wordt niet geladen

> [!NOTE]
> De Azure Data Factory UX heeft officiële ondersteuning voor micro soft Edge en Google Chrome. Het gebruik van andere webbrowsers kan leiden tot onverwacht of niet-gedocumenteerd gedrag.

### <a name="third-party-cookies-blocked"></a>Cookies van derden geblokkeerd

ADF UX maakt gebruik van browser cookies om gebruikers sessie te behouden en interactieve ontwikkel-en bewakings ervaringen mogelijk te maken. Het is mogelijk dat uw browser cookies van derden blokkeert omdat u een incognito-sessie gebruikt of een AD-blok kering hebt ingeschakeld. Het blok keren van cookies van derden kan problemen veroorzaken bij het laden van de portal, zoals wordt omgeleid naar een lege pagina https://adf.azure.com/accesstoken.html of een waarschuwing ontvangen waarin staat dat cookies van derden zijn geblokkeerd. Als u dit probleem wilt oplossen, kunt u via de volgende stappen opties voor cookies van derden inschakelen in uw browser:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Alle cookies toestaan

1. Ga naar **Chrome://settings/cookies** in uw browser.
1. Selecteer **de optie alle cookies toestaan** selecteren ![ Chrome-alle cookies toestaan](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Vernieuw ADF UX en probeer het opnieuw.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Alleen ADF UX toestaan cookies te gebruiken
Als u niet alle cookies wilt toestaan, kunt u eventueel gewoon ADF UX toestaan:
1. Ga naar **Chrome://settings/cookies**.
1. Klik op **toevoegen** onder **sites die altijd cookies kunnen gebruiken** optie ![ ADF UX toevoegen aan toegestane sites](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Voeg **ADF.Azure.com** -site toe, Controleer **de optie alle cookies** en sla het bestand op. ![Alle cookies van de website ADF UX toestaan](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Vernieuw ADF UX en probeer het opnieuw.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Ga naar **Edge://Settings/content/cookies** in uw browser.
1. Zorg ervoor dat **sites toestaan om cookie gegevens op te slaan en te lezen** zijn ingeschakeld en dat de optie cookies van derden **blok keren** is uitgeschakeld ![ alle cookies in de rand toestaan](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Vernieuw ADF UX en probeer het opnieuw.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Alleen ADF UX toestaan cookies te gebruiken

Als u niet alle cookies wilt toestaan, kunt u eventueel gewoon ADF UX toestaan:

1. Ga naar **Edge://Settings/content/cookies**.
1. Klik onder sectie **toestaan** op **toevoegen** en **ADF.Azure.com** site toevoegen. ![ADF-UX toevoegen aan toegestane sites](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Vernieuw ADF UX en probeer het opnieuw.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

* [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-Video's](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A-vragenpagina](https://docs.microsoft.com/answers/topics/azure-data-factory.html)

---
title: Problemen met Gegevens kopiëren-hulp programma in Azure Data Factory oplossen
description: Meer informatie over het oplossen van problemen met Gegevens kopiëren hulpprogramma's in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388373"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>Problemen met Gegevens kopiëren-hulp programma in Azure Data Factory oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden besproken voor Gegevens kopiëren-hulp programma in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Veelvoorkomende fouten en berichten

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>Fout code: kan niet valideren in Gegevens kopiëren-hulp programma

- **Symptomen**: in de eerste stap van gegevens kopiëren tool wordt het volgende waarschuwings bericht over ' kan niet valideren ' aangetroffen.
- **Oorzaken**: dit kan gebeuren wanneer alle cookies van derden zijn uitgeschakeld.
- **Oplossing**: 
    - Gebruik Internet Explorer of micro soft Edge-browser.
    - Als u Chrome browser gebruikt, volgt u de onderstaande instructies om een uitzonde ring voor cookies voor *microsoftonline.com* en *Windows.net*toe te voegen.
        1.  Open de Chrome-browser.
        2.  Klik op de moersleutel of drie regels aan de rechter kant (Google Chrome aanpassen en beheren).
        3.  Klik op **Instellingen**.
        4.  Zoek **cookies** of ga naar **Privacy** onder Geavanceerde instellingen.
        5.  Selecteer **inhouds instellingen**.    
        6.  Cookies moeten worden ingesteld zodat **lokale gegevens kunnen worden ingesteld (aanbevolen)**.
        7.  Klik op **uitzonde ringen beheren**. Onder het- **hostname-patroon** voert u het volgende in en zorgt u ervoor dat het gedrag is ingesteld op **toestaan** .
            - login.microsoftonline.com
            - login.windows.net
        8.  Sluit de browser en start opnieuw.
    - Als u de Firefox-browser gebruikt, volgt u de onderstaande instructies om een uitzonde ring voor cookies toe te voegen.
        1. Ga in het menu van Firefox naar **extra**  >  **Opties**.
        2. Onder **Privacy**  >  **geschiedenis**van privacy ziet u mogelijk dat de huidige instelling **aangepaste instellingen voor geschiedenis gebruikt**.
        3. In **cookies**van derden accepteren is uw huidige instelling mogelijk **nooit**. Klik vervolgens op **uitzonde ringen** aan de rechter kant om de volgende sites toe te voegen.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Sluit de browser en start opnieuw. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Fout code: kan aanmeldings pagina niet openen en wacht woord invoeren

- **Symptomen**: met gegevens kopiëren hulp programma wordt u omgeleid naar de aanmeldings pagina, maar de aanmeldings pagina wordt niet correct weer gegeven.
- **Oorzaken**: dit probleem kan zich voordoen als u de netwerk omgeving van het bedrijfs netwerk hebt gewijzigd in het thuis netwerk. Browsers bevatten enkele caches. 
- **Oplossing**: 
    1.  Sluit de browser en probeer het opnieuw. Ga naar de volgende stap als het probleem zich nog steeds voordoet.   
    2.  Als u Internet Explorer browser gebruikt, probeert u het te openen in de privé modus (druk op CTRL + SHIFT + P). Als u Chrome browser gebruikt, probeert u het te openen in de Incognito-modus (druk op CTRL + SHIFT + N). Ga naar de volgende stap als het probleem zich nog steeds voordoet. 
    3.  Probeer een andere browser te gebruiken. 


## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:
*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A-vragenpagina](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestatie gids voor gegevens stromen van ADF-toewijzing](concepts-data-flow-performance.md)

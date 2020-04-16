---
title: Azure Synapse Studio oplossen (voorbeeld)
description: Problemen met Azure Synapse Studio oplossen
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431317"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Azure Synapse Studio (preview) probleemoplossing

Deze handleiding voor het oplossen van problemen geeft instructies over welke informatie u moet verstrekken bij het openen van een ondersteuningsticket voor problemen met de netwerkconnectiviteit. Met de juiste informatie kunnen we het probleem mogelijk sneller oplossen.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>SQL on-demand (preview) serviceconnectiviteitsprobleem

### <a name="symptom-1"></a>Symptoom 1

De optie 'SQL on-demand' wordt grijs weergegeven in de vervolgkeuzelijst 'Verbinden met'.

![symptoom1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>Symptoom 2

Als u de query uitvoert met 'SQL on-demand', krijgt u het foutbericht 'Kan geen verbinding met de server tot stand worden gebracht'.

![symptoom2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

> [!NOTE] 
>    De volgende stappen voor probleemoplossing zijn voor Chromium Edge en Chrome. U andere browsers (zoals FireFox) gebruiken met dezelfde stappen voor het oplossen van problemen, maar het venster 'Hulpprogramma voor ontwikkelaars' kan een andere indeling hebben dan de schermafbeeldingen in deze TSG. Gebruik indien mogelijk geen klassieke Edge voor het oplossen van problemen, omdat het in bepaalde situaties onjuiste informatie kan weergeven.

Open het deelvenster Diagnostische gegevens en selecteer de knop Diagnostische gegevens downloaden. Bewaar de gedownloade informatie voor foutrapportage. U in plaats daarvan de Sessie-ID kopiëren en deze bij voegen bij het openen van het ondersteuningsticket.

![diagnostische informatie](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

Als u problemen wilt oplossen, probeert u de bewerking die u hebt uitgevoerd in Azure Synapse Studio opnieuw.

- Selecteer voor symptoom 1 de knop Vernieuwen rechts van de vervolgkeuzelijst 'Database gebruiken' op het tabblad 'SQL-script' en controleer of u 'SQL on-demand' zien.
- Probeer voor symptoom 2 de query opnieuw uit te voeren om te zien of deze met succes wordt uitgevoerd.

Als het probleem nog steeds bestaat, drukt u op F12 in uw browser om 'Extra ontwikkelaarstools' (DevTools) te openen.

Schakel in het venster 'Hulpmiddelen voor ontwikkelaars' over naar het deelvenster Netwerk. Selecteer indien nodig de knop 'Wissen' op de werkbalk in het deelvenster Netwerk.
Controleer of 'Cache uitschakelen' in het deelvenster Netwerk is ingeschakeld.

Probeer de bewerking die u hebt uitgevoerd opnieuw in Azure Synapse Studio. Mogelijk worden er nieuwe items weergegeven in de lijst Netwerk in 'Hulpmiddelen voor ontwikkelaars'. Noteer uw huidige systeemtijd om te bieden in het ondersteuningsticket.

![netwerkpaneel](media/troubleshooting-synapse-studio/network-panel.png)

Zoek het item waarvan de url-kolom overeenkomt met het volgende patroon:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Waar [*A*] de naam van uw werkruimte is en "-ondemand" "sqlod" zou kunnen zijn en waar [*B*] een databasenaam moet zijn, zoals "master". Er moeten hoogstens twee items met dezelfde URL-waarde zijn, maar verschillende methodewaarden; OPTIES EN POST. Controleer of deze twee items "200" of "20x" hebben onder de statuskolom, waar "x" een enkel cijfer kan zijn.

Als een van hen heeft iets anders dan "20x" en:

- status begint met '(mislukt)",, verbreed de kolom Status of plaats de aanwijzer boven de statustekst om de volledige tekst weer te geven. Voeg de tekst en/of screenshot toe bij het openen van het ondersteuningsticket.

    ![statustekst](media/troubleshooting-synapse-studio/status-text.png)

    - Als u ERR_NAME_NOT_RESOLVED ziet en u uw werkruimte binnen 10 minuten hebt gemaakt, wacht u 10 minuten en probeert u opnieuw te zien of het probleem nog bestaat.
    - Als u ERR_INTERNET_DISCONNECTED of ERR_NETWORK_CHANGED ziet, kan dit erop wijzen dat uw pc-netwerkverbinding problemen ondervindt. Controleer uw netwerkverbinding en probeer de bewerking opnieuw uit.
    - Als u ziet ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR of andere foutcodes met 'SSL', kan dit erop wijzen dat uw lokale SSL-configuratie problemen ondervindt of heeft uw netwerkbeheerder de toegang tot de SQL on-demandserver geblokkeerd. Open een ondersteuningsticket en voeg de foutcode toe aan de beschrijving.
    - Als u ERR_NETWORK_ACCESS_DENIED ziet, moet u mogelijk contact opnemen met de beheerder of uw lokale firewallbeleid de toegang tot het domein *.database.windows.net of externe poort 1443 heeft geblokkeerd.
    - Probeer eventueel onmiddellijk dezelfde bewerking op een andere machine en/of netwerkomgeving om een probleem met de netwerkconfiguratie op uw pc uit te sluiten.

- status is "40x", "50x" of andere getallen, selecteer op het item(s) om de details te zien. U ziet de details van het item aan de rechterkant. Zoek de sectie 'Antwoordkoptekst'; controleer vervolgens of er een item met de naam "access-control-allow-origin" bestaat. Controleer zo ja, controleer of het een van de volgende waarden heeft:

    - `*`(enkel sterretje)
    - https://web.azuresynapse.net/(of een andere waarde waarmee de tekst in de adresbalk van uw browser begint)

Als de antwoordkop een van de bovenstaande waarden bevat, betekent dit dat we de foutinformatie al hadden moeten verzamelen. U indien nodig een ondersteuningsticket openen en optioneel de schermafbeelding van de itemdetails toevoegen.

Als u de koptekst niet zien of als de koptekst geen van de hierboven vermelde waarden heeft, koppelt u een schermafbeelding van de itemdetails bij wanneer u het ticket opent.

![objectdetails](media/troubleshooting-synapse-studio/item-details.png)

Als de bovenstaande stappen uw probleem niet oplossen, moet u mogelijk een ondersteuningsticket openen. Vermeld bij het indienen van uw ondersteuningsticket de 'Sessie-ID' of 'Diagnostische informatie' die aan het begin van deze handleiding is gedownload.

Wanneer u het probleem meldt, u optioneel een screenshot maken van het tabblad 'Console' in de 'Hulpmiddelen voor ontwikkelaars' en het koppelen aan het ondersteuningsticket. Scroll door de inhoud en maak indien nodig meer dan één screenshot om het hele bericht vast te leggen.

![developer-tool-console](media/troubleshooting-synapse-studio/developer-tool-console.png)

Als u screenshots koppelt, geeft u de tijd (of een geschat tijdsbereik) op van wanneer u de screenshots hebt gemaakt. Het zal ons helpen bij het onderzoeken van het probleem.

Bepaalde browsers ondersteunen het weergeven van tijdstempels op het tabblad Console. Open voor Chromium Edge/Chrome het dialoogvenster 'Instellingen' in 'Hulpmiddelen voor ontwikkelaars' en schakel op het tabblad 'Tijdstempels weergeven' in 'Voorkeuren'.

![instellingen voor ontwikkelaars-tool-console](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![show-time-stempel](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>Volgende stappen
Als de vorige stappen helpen om het probleem op te lossen [Een ondersteuningsticket maken](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

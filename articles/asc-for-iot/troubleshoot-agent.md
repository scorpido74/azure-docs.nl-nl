---
title: Azure Security Center for IoT Linux security agent troubleshooting guide| Microsoft Documenten
description: Problemen oplossen met het werken met Azure Security Center voor IoT-beveiligingsagents voor Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 7f3bd4be3ef927f73643146a457bc551ef86a450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68600564"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Handleiding voor probleemoplossing voor beveiligingsagenten (Linux)

In dit artikel wordt uitgelegd hoe u potentiële problemen in het opstartproces van beveiligingsagenten oplossen.

Azure Security Center voor IoT-agent start direct na de installatie. Het opstartproces van de agent omvat het lezen van lokale configuratie, het verbinden met Azure IoT Hub en het ophalen van de externe dubbele configuratie. Als u in een van deze stappen niet optreedt, kan de beveiligingsagent uitvallen.

In deze handleiding voor probleemoplossing leert u hoe u:
> [!div class="checklist"]
> * Valideren of de beveiligingsagent actief is
> * Fouten in beveiligingsagent's opvragen
> * Fouten in beveiligingsagent begrijpen en herstellen 

## <a name="validate-if-the-security-agent-is-running"></a>Valideren of de beveiligingsagent actief is

1. Als u wilt valideren is dat de beveiligingsagent wordt uitgevoerd, wacht u enkele minuten na het installeren van de agent en voert u de volgende opdracht uit. 
     <br>

    **C-agent**
    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```
    **C# agent**
    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```
2. Als de opdracht een lege regel retourneert, kan de beveiligingsagent niet meer starten.    

## <a name="force-stop-the-security-agent"></a>Force stop de beveiligingsagent 
In gevallen waarin de beveiligingsagent niet kan starten, stopt u de agent met de volgende opdracht en gaat u verder naar de onderstaande fouttabel:

```bash
systemctl stop ASCIoTAgent.service
```
## <a name="get-security-agent-errors"></a>Fouten in beveiligingsagent's opvragen
1. Haal de fout(en) van beveiligingsagent op door de volgende opdracht uit te voeren:
    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```
2. Met de foutopdracht 'Beveiligingsagent ophalen worden alle logboeken opgehaald die zijn gemaakt door de Azure Security Center for IoT-agent. Gebruik de volgende tabel om de fouten te begrijpen en de juiste stappen voor herstel te nemen. 

> [!Note]
> Foutlogboeken worden in chronologische volgorde weergegeven. Let op de tijdstempel van elke fout om uw herstel te helpen. 

## <a name="restart-the-agent"></a>De agent opnieuw starten

1. Probeer de agent opnieuw te starten nadat u een fout van een beveiligingsagent hebt gevonden en opgelost door de volgende opdracht uit te voeren. 
    ```bash
    systemctl restart ASCIoTAgent.service
    ```
1. Herhaal het vorige proces om stop op te halen en de fouten op te halen als de agent het opstartproces blijft mislukken. 

## <a name="understand-security-agent-errors"></a>Fouten in beveiligingsagent begrijpen

De meeste fouten van de beveiligingsagent worden weergegeven in de volgende indeling: 
```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```
| Foutcode | Subcode voor fout | Foutdetails | Saneren C | Saneren C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Lokale configuratie | Ontbrekende configuratie | Er ontbreekt een configuratie in het lokale configuratiebestand. In de foutmelding moet worden vermeld welke sleutel ontbreekt. | Voeg de ontbrekende sleutel toe aan het bestand /var/LocalConfiguration.json, zie de [cs-localconfig-referentie](azure-iot-security-local-configuration-c.md) voor meer informatie.| Voeg de ontbrekende sleutel toe aan het bestand General.config, zie de [c#-localconfig-referentie](azure-iot-security-local-configuration-csharp.md) voor meer informatie. |
| Lokale configuratie | Cant Parse-configuratie | Een configuratiewaarde kan niet worden ontleed. In het foutbericht moet worden vermeld welke sleutel niet kan worden ontleed. Een configuratiewaarde kan niet worden geparseerd omdat de waarde niet in het verwachte type zit of omdat de waarde buiten bereik is. | Fix the value of the key in /var/LocalConfiguration.json file so that it match the LocalConfiguration schema, zie de [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) voor meer informatie. |  Fix the value of the key in General.config file so that it match the schema, see the [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) for details.|
| Lokale configuratie | Bestandsindeling | Kan configuratiebestand niet ontsmetten. | Het configuratiebestand is beschadigd, download de agent en installeer opnieuw. | |
| Externe configuratie | Time-out | De agent kon de azureiotsecurity module twin niet binnen de time-outperiode ophalen. | Zorg ervoor dat de verificatieconfiguratie correct is en probeer het opnieuw. | De agent kon de azureiotsecurity module twin niet ophalen binnen time-outperiode. | Zorg ervoor dat de verificatieconfiguratie correct is en probeer het opnieuw. |
| Authentication | Bestand bestaat niet | Het bestand in het opgegeven pad bestaat niet. | Controleer of het bestand in het opgegeven pad bestaat of ga naar het bestand **LocalConfiguration.json** en wijzig de **FilePath-configuratie.** | Controleer of het bestand in het opgegeven pad bestaat of ga naar het bestand **Authentication.config** en wijzig de **filePath-configuratie.**|
| Authentication | Bestandsmachtigingen | De agent beschikt niet over voldoende machtigingen om het bestand te openen. | Geef de **asciotagent** gebruiker lees machtigingen op het bestand in het gegeven pad. | Controleer of het bestand toegankelijk is. |
| Authentication | Bestandsindeling | Het opgegeven bestand is niet in de juiste indeling. | Controleer of het bestand in de juiste indeling is. De ondersteunde bestandstypen zijn .pfx en .pem. | Controleer of het bestand een geldig certificaatbestand is. |
| Authentication | Niet geautoriseerd | De agent kon zich niet verifiëren tegen IoT Hub met de opgegeven referenties. | Valideer de verificatieconfiguratie in het bestand LocalConfiguration, doorga de verificatieconfiguratie en zorg ervoor dat alle details correct zijn, valideer dat het geheim in het bestand overeenkomt met de geverifieerde identiteit. | Valideer de verificatieconfiguratie in Authentication.config, doorloop de verificatieconfiguratie en zorg ervoor dat alle details correct zijn en valideer vervolgens dat het geheim in het bestand overeenkomt met de geverifieerde identiteit.
| Authentication | Niet gevonden | Het apparaat / module werd gevonden. | Verificatieconfiguratie valideren - zorg ervoor dat de hostnaam correct is, het apparaat bestaat in IoT Hub en heeft een azureiotsecurity twin-module. |  Verificatieconfiguratie valideren - zorg ervoor dat de hostnaam correct is, het apparaat bestaat in IoT Hub en heeft een azureiotsecurity twin-module. |
| Authentication | Ontbrekende configuratie | Er ontbreekt een configuratie in het bestand *Authentication.config.* In de foutmelding moet worden vermeld welke sleutel ontbreekt. | Voeg de ontbrekende sleutel toe aan het bestand *LocalConfiguration.json.*| Voeg de ontbrekende sleutel toe aan het *bestand Authentication.config,* zie de [c#-localconfig-referentie](azure-iot-security-local-configuration-csharp.md) voor meer informatie. |
| Authentication | Cant Parse-configuratie | Een configuratiewaarde kan niet worden ontleed. In het foutbericht moet worden vermeld welke sleutel niet kan worden ontleed. Een configuratiewaarde kan niet worden ontleed omdat de waarde niet van het verwachte type is of omdat de waarde buiten bereik is. |De waarde van de sleutel in het bestand **LocalConfiguration.json** herstellen. |Fix the value of the key in **Authentication.config** file to match the schema, see the [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) for details.|
|

## <a name="restart-the-agent"></a>De agent opnieuw starten
1. Nadat u een fout van een beveiligingsagent hebt opgelost, start u de agent opnieuw door de volgende opdracht uit te voeren:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```
2. Indien nodig herhaalt u de vorige processen om de agent te stoppen en de fouten op te halen als de agent het opstartproces blijft mislukken. 

## <a name="next-steps"></a>Volgende stappen
- Lees het [overzicht](overview.md) van de Azure Security Center for IoT-service
- Meer informatie over Azure Security Center voor [IoT-architectuur](architecture.md)
- De Azure Security Center for IoT-service [inschakelen](quickstart-onboard-iot-hub.md)
- Lees de veelgestelde vragen over azure security center voor [IoT-service](resources-frequently-asked-questions.md)
- Meer informatie over hoe u toegang krijgt tot [ruwe beveiligingsgegevens](how-to-security-data-access.md)
- Aanbevelingen [begrijpen](concept-recommendations.md)
- [Beveiligingswaarschuwingen begrijpen](concept-security-alerts.md)

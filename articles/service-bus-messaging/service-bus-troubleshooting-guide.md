---
title: Handleiding voor probleemoplossing voor Azure Service Bus | Microsoft Documenten
description: In dit artikel vindt u een lijst met uitzonderingen op Azure Service Bus-berichten en worden acties voorgesteld die moeten worden uitgevoerd wanneer de uitzondering optreedt.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887773"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Handleiding voor probleemoplossing voor Azure Service Bus
In dit artikel vindt u tips en aanbevelingen voor het oplossen van problemen voor een aantal problemen die u mogelijk ziet bij het gebruik van Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemen met connectiviteit, certificaten of time-outs
De volgende stappen kunnen u helpen bij het oplossen van problemen met connectiviteit/certificaat/time-out voor alle services onder *.servicebus.windows.net. 

- Bladeren naar of [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Het helpt bij het controleren of u IP-filtering of virtuele netwerk- of certificaatketenproblemen hebt (het meest voorkomende bij het gebruik van java SDK).

    Een voorbeeld van een geslaagd bericht:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Een voorbeeld van foutfoutbericht:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Voer de volgende opdracht uit om te controleren of een poort op de firewall is geblokkeerd. Gebruikte poorten zijn 443 (HTTPS), 5671 (AMQP) en 9354 (Net Messaging/SBMP). Afhankelijk van de bibliotheek die u gebruikt, worden ook andere poorten gebruikt. Hier is de voorbeeldopdracht die controleert of de 5671-poort is geblokkeerd. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Op Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Wanneer er met tussenpozen verbindingsproblemen zijn, voert u de volgende opdracht uit om te controleren of er gevallen pakketten zijn. Deze opdracht probeert om 25 verschillende TCP-verbindingen elke 1 seconde met de service tot stand te brengen. Vervolgens u controleren hoeveel van hen zijn geslaagd/mislukt en u ook de latentie van de TCP-verbinding zien. U `psping` de tool downloaden vanaf [hier.](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    U gelijkwaardige opdrachten gebruiken als u andere `tnc` `ping`hulpprogramma's gebruikt, zoals, enzovoort. 
- Verkrijg een netwerktracering als de vorige stappen niet helpen en analyseren met behulp van tools zoals [Wireshark.](https://www.wireshark.org/) Neem indien nodig contact op met [Microsoft Support.](https://support.microsoft.com/) 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemen die zich kunnen voordoen bij service-upgrades/opnieuw opstarten
Back-endservice-upgrades en opnieuw opstarten kunnen de volgende gevolgen hebben voor uw toepassingen:

- Verzoeken kunnen tijdelijk worden beperkt.
- Er kan een daling van de inkomende berichten / verzoeken.
- Het logboekbestand kan foutmeldingen bevatten.
- De toepassingen kunnen enkele seconden van de service worden losgekoppeld.

Als de toepassingscode SDK gebruikt, is het beleid voor opnieuw proberen al ingebouwd en actief. De toepassing maakt opnieuw verbinding zonder noemenswaardige gevolgen voor de toepassing/workflow.

## <a name="unauthorized-access-send-claims-are-required"></a>Ongeautoriseerde toegang: Claims verzenden is vereist
Deze fout wordt mogelijk weergegeven wanneer u probeert toegang te krijgen tot een servicebusonderwerp vanuit Visual Studio op een on-premises computer met behulp van een door de gebruiker toegewezen beheerde identiteit met verzendmachtigingen.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

Installeer de [Microsoft.Azure.Services.AppAuthentication-bibliotheek](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) als u deze fout wilt oplossen.  Zie [Verificatie voor lokale ontwikkeling](..\key-vault\service-to-service-authentication.md#local-development-authentication)voor meer informatie . 

Zie [Een beheerde identiteit verifiëren met Azure Active Directory voor](service-bus-managed-service-identity.md)meer informatie over het toewijzen van machtigingen aan rollen.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen: 

- [Uitzonderingen op Azure Resource Manager](service-bus-resource-manager-exceptions.md). Hierworden uitzonderingen vermeld die zijn gegenereerd bij interactie met Azure Service Bus met Azure Resource Manager (via sjablonen of directe oproepen).
- [Uitzonderingen op berichten](service-bus-messaging-exceptions.md). Het bevat een lijst met uitzonderingen die worden gegenereerd door .NET Framework voor Azure Service Bus. 


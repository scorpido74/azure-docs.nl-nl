---
title: Overzicht van status controle voor de Azure-toepassing-gateway
description: Azure-toepassing gateway controleert de status van alle resources in de back-end-pool en verwijdert automatisch alle bronnen die worden beschouwd als een slechte status van de groep.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 01/28/2020
ms.author: victorh
ms.openlocfilehash: 5c25f591d1011d2efd66851cafd67ceef8b56637
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766835"
---
# <a name="application-gateway-health-monitoring-overview"></a>Overzicht van Application Gateway status controle

Azure-toepassing gateway controleert standaard de status van alle resources in de back-end-groep en verwijdert automatisch alle bronnen die worden beschouwd als een slechte status van de groep. Application Gateway blijft de beschadigde instanties bewaken en voegt deze weer toe aan de gezonde back-end-pool zodra deze beschikbaar komen en reageren op status controles. Application Gateway verzendt de status tests met dezelfde poort die is gedefinieerd in de back-end-HTTP-instellingen. Deze configuratie zorgt ervoor dat de test wordt uitgevoerd op dezelfde poort die door klanten zou worden gebruikt om verbinding te maken met de back-end. 

Het bron-IP-adres Application Gateway gebruikt voor status tests is afhankelijk van de back-end-groep:
 
- Als de back-end-pool een openbaar eind punt is, is het bron adres het open bare IP-adres van de Application Gateway-frontend.
- Als de back-end-pool een persoonlijk eind punt is, is het IP-bron adres van de toepassings gateway van het subnet persoonlijke IP-adres ruimte.


![test voorbeeld van Application Gateway][1]

Naast het gebruik van de standaard controle van de status test, kunt u ook de status test aanpassen aan de vereisten van uw toepassing. In dit artikel worden zowel standaard als aangepaste status controles behandeld.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Standaard status test

Een toepassings gateway configureert automatisch een standaard status test wanneer u geen aangepaste test configuratie instelt. Het bewakings gedrag werkt door een HTTP-aanvraag in te stellen voor de IP-adressen die zijn geconfigureerd voor de back-end-pool. Voor standaard tests als de back-end-http-instellingen voor HTTPS zijn geconfigureerd, gebruikt de test HTTPS en de test status van de back-ends.

Bijvoorbeeld: u configureert uw toepassings gateway voor het gebruik van back-endservers A, B en C om HTTP-netwerk verkeer te ontvangen op poort 80. De standaard status bewaking test de drie servers om de 30 seconden voor een gezonde HTTP-reactie. Een gezond HTTP-antwoord heeft een [status code](https://msdn.microsoft.com/library/aa287675.aspx) tussen 200 en 399.

Als de standaard controle van de test voor Server A mislukt, wordt deze door de toepassings gateway verwijderd uit de back-end-pool en wordt het netwerk verkeer niet meer naar deze server gestroomt. De standaard test blijft altijd elke 30 seconden controleren op server. Als server A reageert op een aanvraag van een standaard status test, wordt deze als gezond toegevoegd aan de back-end-pool en wordt het verkeer opnieuw naar de server verzonden.

### <a name="probe-matching"></a>Testen vergelijken

Standaard wordt een HTTP (S)-antwoord met de status code tussen 200 en 399 als gezond beschouwd. Aangepaste Health-tests ondersteunen daarnaast twee overeenkomende criteria. Overeenkomende criteria kunnen worden gebruikt voor het aanpassen van de standaard interpretatie van wat een gezonde reactie doet.

Hier volgen de overeenkomende criteria: 

- **Status code overeenkomst van http-antwoord** : test matching criterium voor het accepteren van door de gebruiker opgegeven HTTP-antwoord code of het bereik van antwoord codes. Afzonderlijke door komma's gescheiden antwoord status codes of een bereik met status code wordt ondersteund.
- **Overeenkomst voor de hoofd tekst van het HTTP-antwoord die overeenkomt** met het criterium van de HTTP-reactie en overeenkomt met een door de gebruiker opgegeven teken reeks. De overeenkomst zoekt alleen naar de aanwezigheid van de door de gebruiker opgegeven teken reeks in de tekst van het antwoord en is geen volledige reguliere expressie.

U kunt match criteria opgeven met behulp van de cmdlet `New-AzApplicationGatewayProbeHealthResponseMatch`.

Bijvoorbeeld:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Zodra de match criteria zijn opgegeven, kan deze worden gekoppeld aan de test configuratie met behulp van een `-Match` para meter in Power shell.

### <a name="default-health-probe-settings"></a>Standaard instellingen voor status controle

| Probe-eigenschap | Waarde | Beschrijving |
| --- | --- | --- |
| Test-URL |http://127.0.0.1:\<port\>/ |URL-pad |
| Interval |30 |De hoeveelheid tijd in seconden die moet worden gewacht voordat de volgende status test wordt verzonden.|
| Time-out |30 |De hoeveelheid tijd in seconden die de toepassings gateway wacht op een test reactie voordat de test wordt gemarkeerd als beschadigd. Als een test wordt geretourneerd als in orde, wordt de bijbehorende back-end direct gemarkeerd als in orde.|
| Drempel waarde voor onjuiste status |3 |Bepaalt hoeveel tests er moeten worden verzonden als er een fout is opgetreden van de normale status test. Deze aanvullende Health-tests worden snel achter elkaar verzonden om de status van de back-end snel te bepalen en niet te wachten op het test interval. De back-endserver is gemarkeerd wanneer het aantal opeenvolgende test fouten de drempel waarde voor de onjuiste status bereikt. |

> [!NOTE]
> De poort is dezelfde poort als de back-end-HTTP-instellingen.

De standaard test zoekt alleen op http:\//127.0.0.1:\<poort\> om de status te bepalen. Als u de status test moet configureren om naar een aangepaste URL te gaan of andere instellingen te wijzigen, moet u aangepaste tests gebruiken.

### <a name="probe-intervals"></a>Sonde-intervallen

Alle exemplaren van Application Gateway testen de back-end onafhankelijk van elkaar. Dezelfde test configuratie is van toepassing op elk Application Gateway-exemplaar. Als de test configuratie bijvoorbeeld elke 30 seconden status controles verzendt en de toepassings gateway twee exemplaren heeft, worden beide instanties elke 30 seconden de status test verzonden.

Als er meerdere listeners zijn, controleert elke listener de back-end onafhankelijk van elkaar. Als er bijvoorbeeld twee listeners zijn die verwijzen naar dezelfde back-end-groep op twee verschillende poorten (geconfigureerd met twee back-end-http-instellingen), wordt de dezelfde back-end onafhankelijk door elke listener gecontroleerd. In dit geval zijn er twee tests van elk toepassings gateway-exemplaar voor de twee listeners. Als er in dit scenario twee exemplaren van de toepassings gateway zijn, ziet u op de virtuele machine van de back-end vier tests volgens het geconfigureerde test interval.

## <a name="custom-health-probe"></a>Aangepaste status test

Met aangepaste tests kunt u de controle over de status controle nauw keuriger maken. Wanneer u aangepaste tests gebruikt, kunt u het test interval, de URL en het pad dat moet worden getest en het aantal mislukte antwoorden dat moet worden geaccepteerd, configureren voordat de back-end-pool instantie als beschadigd wordt gemarkeerd.

### <a name="custom-health-probe-settings"></a>Instellingen voor aangepaste Health probe

De volgende tabel bevat definities voor de eigenschappen van een aangepaste status test.

| Probe-eigenschap | Beschrijving |
| --- | --- |
| Name |De naam van de test. Deze naam wordt gebruikt om te verwijzen naar de test in back-end-HTTP-instellingen. |
| Protocol |Het protocol dat wordt gebruikt om de test te verzenden. De test gebruikt het protocol dat is gedefinieerd in de back-end-HTTP-instellingen |
| Host |De hostnaam voor het verzenden van de test. Alleen van toepassing als multi-site is geconfigureerd op Application Gateway, anders ' 127.0.0.1 ' gebruiken. Deze waarde wijkt af van de naam van de VM-host. |
| Pad |Het relatieve pad van de test. Het geldige pad wordt gestart vanaf/. |
| Interval |Test interval in seconden. Deze waarde is het tijds interval tussen twee opeenvolgende tests. |
| Time-out |Time-out van de test (in seconden). Als er binnen deze time-outperiode geen geldig antwoord wordt ontvangen, wordt de test als mislukt gemarkeerd.  |
| Drempel waarde voor onjuiste status |Aantal nieuwe pogingen testen. De back-endserver is gemarkeerd wanneer het aantal opeenvolgende test fouten de drempel waarde voor de onjuiste status bereikt. |

> [!IMPORTANT]
> Als Application Gateway voor één site is geconfigureerd, moet de hostnaam standaard worden opgegeven als 127.0.0.1, tenzij anders geconfigureerd in de aangepaste test.
> Als referentie voor een aangepaste test wordt verzonden naar \<protocol\>://\<host\>:\<poort\>\<pad\>. De poort die wordt gebruikt, is de poort die is gedefinieerd in de back-end-HTTP-instellingen.

## <a name="nsg-considerations"></a>NSG overwegingen

Als er een netwerk beveiligings groep (NSG) is op een Application Gateway-subnet, moet poort bereik 65503-65534 worden geopend op het toepassings gateway-subnet voor binnenkomend verkeer. Deze poorten zijn vereist voor een goede werking van de back-end-API.

Daarnaast kan uitgaande internet connectiviteit niet worden geblokkeerd en moet binnenkomend verkeer dat afkomstig is van het label AzureLoadBalancer, worden toegestaan.

## <a name="next-steps"></a>Volgende stappen
Nadat u hebt gebruikgemaakt van Application Gateway status controle, kunt u een [aangepaste Health probe](application-gateway-create-probe-portal.md) in de Azure portal of een [aangepaste status test](application-gateway-create-probe-ps.md) configureren met behulp van power shell en het Azure Resource Manager-implementatie model.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png

---
title: KEUR Toegang tot de Azure DC/OS-container-app inschakelen
description: Het inschakelen van open bare toegang tot containers voor DC/OS in Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "61457334"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>KEUR Open bare toegang tot een Azure Container Service-toepassing inschakelen

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Een DC/OS-container in de [open bare ACS-agent groep](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) wordt automatisch beschikbaar gemaakt op internet. Poorten **80**, **443**, **8080** worden standaard geopend en alle (open bare) containers die Luis teren op deze poorten zijn toegankelijk. Dit artikel laat u zien hoe u meer poorten voor uw toepassingen kunt openen in Azure Container Service.

## <a name="open-a-port-portal"></a>Een poort openen (Portal)
Eerst moeten we de gewenste poort openen.

1. Meld u aan bij de portal.
2. Zoek de resource groep waarop u de Azure Container Service hebt geïmplementeerd.
3. Selecteer de agent load balancer (deze heeft de naam vergelijkbaar met **xxxx-agent-lb-xxxx**).
   
    ![load balancer Azure container service](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klik op **tests** en vervolgens op **toevoegen**.
   
    ![load balancer tests van Azure container service](./media/container-service-enable-public-access/add-probe.png)
5. Vul het test formulier in en klik op **OK**.
   
   | Veld | Beschrijving |
   | --- | --- |
   | Naam |Een beschrijvende naam van de test. |
   | Poort |De poort van de container die u wilt testen. |
   | Pad |(In HTTP-modus) Het relatieve website pad dat moet worden getest. HTTPS wordt niet ondersteund. |
   | Interval |De hoeveelheid tijd tussen de test pogingen, in seconden. |
   | Drempel waarde voor onjuiste status |Aantal opeenvolgende test pogingen voordat de container een slechte status heeft. |
6. Ga terug naar de eigenschappen van de agent load balancer, klik op **taakverdelings regels** en vervolgens op **toevoegen**.
   
    ![load balancer regels voor Azure container service](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Vul het load balancer formulier in en klik op **OK**.
   
   | Veld | Beschrijving |
   | --- | --- |
   | Naam |Een beschrijvende naam van de load balancer. |
   | Poort |De open bare binnenkomende poort. |
   | Backend-poort |De interne en open bare poort van de container voor het routeren van verkeer naar. |
   | Back-endpool |De containers in deze groep zijn het doel voor deze load balancer. |
   | Test |De test die wordt gebruikt om te bepalen of een doel in de **back-end-groep** in orde is. |
   | Sessiepersistentie |Hiermee wordt bepaald hoe verkeer van een client voor de duur van de sessie moet worden afgehandeld.<br><br>**Geen**: opeenvolgende aanvragen van dezelfde client kunnen worden verwerkt door elke container.<br>**Client-IP**: opeenvolgende aanvragen van hetzelfde client-IP worden verwerkt door dezelfde container.<br>**Client-IP en-protocol**: opeenvolgende aanvragen van dezelfde combi natie van client-IP en-protocol worden door dezelfde container verwerkt. |
   | Time-out voor inactiviteit |(Alleen TCP) In enkele minuten is de tijd om een TCP/HTTP-client geopend te houden zonder dat er wordt gebruikgemaakt van *Keep-Alive-* berichten. |

## <a name="add-a-security-rule-portal"></a>Een beveiligings regel toevoegen (Portal)
We moeten nu een beveiligings regel toevoegen die verkeer via de firewall routeert van onze open poort.

1. Meld u aan bij de portal.
2. Zoek de resource groep waarop u de Azure Container Service hebt geïmplementeerd.
3. Selecteer de netwerk beveiligings groep voor de **open bare** agent (met de naam vergelijkbaar met **xxxx-agent-Public-NSG-xxxx**).
   
    ![Azure container service-netwerk beveiligings groep](./media/container-service-enable-public-access/agent-nsg.png)
4. Selecteer **binnenkomende beveiligings regels** en voeg deze vervolgens **toe**.
   
    ![Regels voor netwerk beveiligings groepen van Azure container service](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Vul de firewall regel in om uw open bare poort toe te staan en klik op **OK**.
   
   | Veld | Beschrijving |
   | --- | --- |
   | Naam |Een beschrijvende naam van de firewall regel. |
   | Prioriteit |Prioriteits positie voor de regel. Hoe lager het getal hoe hoger de prioriteit is. |
   | Bron |Beperk het binnenkomende IP-adres bereik dat moet worden toegestaan of geweigerd door deze regel. Gebruik **any** om geen beperking op te geven. |
   | Service |Selecteer een set vooraf gedefinieerde services waarvoor deze beveiligings regel geldt. Gebruik anders **aangepast** om uw eigen te maken. |
   | Protocol |Beperk het verkeer op basis van **TCP** of **UDP**. Gebruik **any** om geen beperking op te geven. |
   | Poortbereik |Wanneer **Service** de service **aangepast**is, geeft het poort bereik op waarop deze regel van toepassing is. U kunt één poort gebruiken, zoals **80**, of een bereik zoals **1024-1500**. |
   | Bewerking |Verkeer toestaan of weigeren dat voldoet aan de criteria. |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het verschil tussen [open bare en persoonlijke DC/OS-agents](container-service-dcos-agents.md).

Lees meer informatie over [het beheren van uw DC/OS-containers](container-service-mesos-marathon-ui.md).


---
title: (AFGESCHAFT) Toegang tot Azure DC/OS-container-app inschakelen
description: Openbare toegang tot DC/OS-containers inschakelen in Azure Container Service.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61457334"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(AFGESCHAFT) Openbare toegang tot een Azure Container Service-toepassing inschakelen

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Elke DC/OS-container in de [ACS-openbare agentpool](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) wordt automatisch blootgesteld aan het internet. Standaard worden poorten **80**, **443**, **8080** geopend en zijn alle (openbare) containerluisteren op die poorten toegankelijk. In dit artikel ziet u hoe u meer poorten voor uw toepassingen opent in Azure Container Service.

## <a name="open-a-port-portal"></a>Een poort openen (portal)
Eerst moeten we de haven openen die we willen.

1. Meld u aan bij de portal.
2. Zoek de brongroep aan waarmee u de Azure Container Service hebt geïmplementeerd.
3. Selecteer de agent load balancer (die is vernoemd naar **XXXX-agent-lb-XXXX**).
   
    ![Laadbalans van Azure-containerservice](./media/container-service-enable-public-access/agent-load-balancer.png)
4. Klik **op Probes** en **voeg .**
   
    ![Azure container service load balancer probes](./media/container-service-enable-public-access/add-probe.png)
5. Vul het sondeformulier in en klik op **OK**.
   
   | Veld | Beschrijving |
   | --- | --- |
   | Name |Een beschrijvende naam van de sonde. |
   | Poort |De haven van de container om te testen. |
   | Pad |(Wanneer in de HTTP-modus) Het relatieve websitepad om te sonden. HTTPS wordt niet ondersteund. |
   | Interval |De hoeveelheid tijd tussen sondepogingen, in seconden. |
   | Ongezonde drempelwaarde |Aantal opeenvolgende sondepogingen voordat de container als ongezond wordt gezien. |
6. Terug bij de eigenschappen van de verlosser van de agent, klik op **Taakverdelingsregels** en **voeg vervolgens Toe .**
   
    ![Azure-regels voor containerserviceloadbalancer](./media/container-service-enable-public-access/add-balancer-rule.png)
7. Vul het formulier load balancer in en klik op **OK**.
   
   | Veld | Beschrijving |
   | --- | --- |
   | Name |Een beschrijvende naam van de load balancer. |
   | Poort |De openbare inkomende haven. |
   | Backend-poort |De intern-openbare haven van de container om verkeer naar te leiden. |
   | Back-endpool |De containers in deze pool zijn het doel voor deze load balancer. |
   | Test |De sonde gebruikt om te bepalen of een doel in de **Backend pool** gezond is. |
   | Sessiepersistentie |Hiermee bepaalt u hoe verkeer van een client moet worden verwerkt voor de duur van de sessie.<br><br>**Geen:** Opeenvolgende aanvragen van dezelfde client kunnen door elke container worden afgehandeld.<br>**Client-IP**: Opeenvolgende aanvragen van hetzelfde client-IP-adres worden door dezelfde container afgehandeld.<br>**Client-IP en -protocol**: Opeenvolgende aanvragen van dezelfde client-IP- en protocolcombinatie worden door dezelfde container afgehandeld. |
   | Niet-actieve time-out |(alleen TCP) Binnen enkele minuten is het tijd om een TCP/HTTP-client open te houden zonder te vertrouwen op *keep-alive* berichten. |

## <a name="add-a-security-rule-portal"></a>Een beveiligingsregel toevoegen (portal)
Vervolgens moeten we een beveiligingsregel toevoegen die verkeer van onze geopende poort door de firewall leidt.

1. Meld u aan bij de portal.
2. Zoek de brongroep aan waarmee u de Azure Container Service hebt geïmplementeerd.
3. Selecteer de **openbare** agent netwerk beveiligingsgroep (die is vernoemd naar **XXXX-agent-public-nsg-XXXX**).
   
    ![Beveiligingsgroep Azure-containerservicenetwerk](./media/container-service-enable-public-access/agent-nsg.png)
4. Selecteer **Binnenkomende beveiligingsregels** en **voeg .**
   
    ![Regels voor azure-containerservicenetwerkbeveiligingsgroep](./media/container-service-enable-public-access/add-firewall-rule.png)
5. Vul de firewallregel in om uw openbare poort toe te staan en klik op **OK**.
   
   | Veld | Beschrijving |
   | --- | --- |
   | Name |Een beschrijvende naam van de firewallregel. |
   | Prioriteit |Prioriteitsrang voor de regel. Hoe lager het getal, hoe hoger de prioriteit. |
   | Bron |Beperk het binnenkomende IP-adresbereik dat door deze regel moet worden toegestaan of geweigerd. Gebruik **Any** om geen beperking op te geven. |
   | Service |Selecteer een set vooraf gedefinieerde services waar deze beveiligingsregel voor is. Gebruik **anders Aangepast** om uw eigen te maken. |
   | Protocol |Verkeer beperken op basis van **TCP** of **UDP**. Gebruik **Any** om geen beperking op te geven. |
   | Poortbereik |Wanneer **Service** **aangepast**is, geeft u het bereik van poorten op waarop deze regel van invloed is. U één poort gebruiken, zoals **80**of een bereik zoals **1024-1500.** |
   | Actie |Verkeer toestaan of weigeren dat aan de criteria voldoet. |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het verschil tussen [openbare en private DC/OS-agents](container-service-dcos-agents.md).

Lees meer informatie over [het beheren van uw DC/OS-containers.](container-service-mesos-marathon-ui.md)


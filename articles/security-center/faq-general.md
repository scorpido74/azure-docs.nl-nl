---
title: Veelgestelde vragen over Azure Security Center-algemene vragen
description: Veelgestelde vragen over Azure Security Center, een product dat u helpt bedreigingen te voor komen, te detecteren en erop te reageren
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: b13e5e0000cdd8e33f459400cc4c24d107ccaba3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604789"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Veelgestelde vragen: algemene vragen over Azure Security Center

## <a name="what-is-azure-security-center"></a>Wat is Azure Security Center?
Azure Security Center helpt u bedreigingen te voor komen, te detecteren en erop te reageren met verbeterde zicht baarheid en controle over de beveiliging van uw resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

Security Center gebruikt de micro soft Monitoring Agent voor het verzamelen en opslaan van gegevens. Zie [gegevens verzameling in azure Security Center](security-center-enable-data-collection.md)voor gedetailleerde informatie.


## <a name="how-do-i-get-azure-security-center"></a>Hoe krijg ik Azure Security Center?
Azure Security Center is ingeschakeld met uw Microsoft Azure-abonnement en is toegankelijk via de [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Meld u aan bij [de portal](https://portal.azure.com), selecteer **Bladeren**en schuif naar **Security Center**om het te openen.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Welke Azure-resources worden bewaakt door Azure Security Center?
De volgende Azure-resources worden bewaakt door Azure Security Center:

* Virtuele machines (Vm's) (inclusief [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Virtuele-machineschaalsets
* Virtuele netwerken van Azure
* Azure SQL-service
* Azure-opslagaccount
* Azure-Web Apps (in [app service Environment](../app-service/environment/intro.md))
* Oplossingen van partners die zijn geïntegreerd met uw Azure-abonnement, zoals een firewall voor webtoepassingen op virtuele machines en op App Service-omgeving

Daarnaast kunnen niet-Azure-computers (met inbegrip van on-premises) ook worden bewaakt door Azure Security Center (zowel [Windows-computers](./quick-onboard-windows-computer.md) als [Linux-computers](./quick-onboard-linux-computer.md) worden ondersteund)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hoe kan ik de huidige beveiligingsstatus van mijn Azure-resources bekijken?
Op de pagina **overzicht van Security Center** ziet u de algemene beveiligings postuur van uw omgeving, onderverdeeld op basis van compute-, netwerk-, opslag-& gegevens en toepassingen. Elk resourcetype heeft een indicator die laat zien als alle mogelijke beveiligingsproblemen zijn geïdentificeerd. Te klikken op elke tegel geeft een lijst van beveiligingsproblemen die zijn geïdentificeerd door Security Center, samen met een overzicht van de resources in uw abonnement.



## <a name="what-is-a-security-policy"></a>Wat is er een beveiligingsbeleid?
Een beveiligingsbeleid bepaalt welke set besturingselementen die worden aanbevolen voor resources binnen het opgegeven abonnement. In Azure Security Center definieert u beleid voor uw Azure-abonnementen op basis van de beveiligingsvereisten van uw bedrijf en het type toepassingen of de vertrouwelijkheid van de gegevens in elk abonnement.

Het beveiligingsbeleid ingeschakeld in Azure Security Center station beveiligingsaanbevelingen en bewaking. Zie [beveiligings status controleren in azure Security Center](security-center-monitoring.md)voor meer informatie over beveiligings beleidsregels.


## <a name="who-can-modify-a-security-policy"></a>Wie kan een beveiligingsbeleid alleen wijzigen?
Als u wilt een beveiligingsbeleid alleen wijzigen, moet u een beveiligingsbeheerder of een eigenaar of bijdrager van het abonnement zijn.

Zie [beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md)voor meer informatie over het configureren van een beveiligings beleid.


## <a name="what-is-a-security-recommendation"></a>Wat is een beveiligingsaanbeveling?
Azure Security Center analyseert de beveiligingsstatus van uw Azure-bronnen. Wanneer de potentiële beveiligingsproblemen worden geïdentificeerd, worden aanbevelingen worden gemaakt. De aanbevelingen begeleiden u bij het proces van het configureren van het vereiste besturingselement. Een aantal voorbeelden:

* Inrichting van anti-malware om te identificeren en verwijderen van schadelijke software
* [Netwerk beveiligings groepen](../virtual-network/security-overview.md) en-regels voor het beheren van verkeer naar virtuele machines
* Het inrichten van een web application firewall om te beschermen tegen aanvallen die gericht is op uw web-apps
* Implementatie van ontbrekende systeemupdates
* Aanpassing van besturingssysteemconfiguraties die niet overeenkomen met de aanbevolen basislijnen

Alleen de aanbevelingen die in de beleidsregels voor veiligheid zijn ingeschakeld, worden hier weergegeven.



## <a name="what-triggers-a-security-alert"></a>Wat wordt een beveiligingswaarschuwing geactiveerd?
Azure Security Center automatisch verzamelt, analyseert en worden logboekgegevens van uw Azure-resources, het netwerk en partneroplossingen zoals antimalware en firewalls. Wanneer er dreigingen worden gedetecteerd, wordt een beveiligingswaarschuwing gemaakt. Voorbeelden zijn detectie van:

* Geïnfecteerde virtuele machines die communiceren met bekende schadelijke IP-adressen
* Geavanceerde malware is gedetecteerd met behulp van Windows Foutrapportage
* Beveiligingsaanvallen op virtuele machines
* Beveiligingswaarschuwingen van geïntegreerde partneroplossingen beveiliging zoals Anti-Malware of Web Application Firewalls


## Waarom zijn beveiligde Score waarden gewijzigd? <a name="secure-score-faq"></a>
Vanaf februari 2019 heeft Security Center de Score van enkele aanbevelingen aangepast, zodat deze beter past bij de ernst. Als gevolg van deze aanpassing kunnen de algemene waarden voor beveiligde scores worden gewijzigd.  Zie [Secure Score Calculation](security-center-secure-score.md)(Engelstalig) voor meer informatie over beveiligde scores.


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Wat is het verschil tussen bedreigingen gedetecteerd en waarschuwingen over door Microsoft Security Response Center ten opzichte van Azure Security Center?
De Microsoft Security Response Center (MSRC) wordt uitgevoerd selecteert u beveiligingsbewaking van de Azure-netwerk en de infrastructuur en threat intelligence en misbruik klachten ontvangt van een derde partij. MSRC wordt wanneer op de hoogte dat klantgegevens zijn geopend door een partij onwettig of niet-geautoriseerde of dat gebruik van Azure van de klant niet aan de voorwaarden voor aanvaardbaar gebruik voldoet, een incident beveiligingsbeheer informeert de klant. Melding treedt meestal op door een e-mailbericht verzenden naar het security contactpersonen in Azure Security Center of de eigenaar van de Azure-abonnement moet worden opgegeven als een contactpersoon voor beveiliging is niet opgegeven.

Security Center is een Azure-service waarmee continu gecontroleerd van de klant Azure-omgeving en analytics voor het automatisch detecteren van een groot aantal mogelijk schadelijke activiteit is van toepassing. Deze detecties worden opgehaald als beveiligingswaarschuwingen in Security Center-dashboard.
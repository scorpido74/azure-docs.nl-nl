---
title: Veelgestelde vragen over Azure Security Center - Algemene vragen
description: Veelgestelde algemene vragen over Azure Security Center, een product waarmee u bedreigingen voorkomen, detecteren en erop reageren
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
ms.openlocfilehash: 54263a8be900ed381d8450b460e5cf9e6117ac54
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436121"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Veelgestelde vragen - Algemene vragen over Azure Security Center

## <a name="what-is-azure-security-center"></a>Wat is Azure Security Center?
Azure Security Center helpt u bedreigingen te voorkomen, detecteren en erop te reageren met meer inzicht in en controle over de beveiliging van uw resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw abonnementen, helpt bedreigingen te detecteren die anders onopgemerkt zouden blijven, en werkt met een uitgebreid ecosysteem van beveiligingsoplossingen.

Security Center gebruikt de Log Analytics-agent om gegevens te verzamelen en op te slaan. Zie [Gegevensverzameling in Azure Security Center](security-center-enable-data-collection.md)voor uitgebreide details.


## <a name="how-do-i-get-azure-security-center"></a>Hoe krijg ik Azure Security Center?
Azure Security Center is ingeschakeld met uw Microsoft Azure-abonnement en toegankelijk vanuit de [Azure-portal.](https://azure.microsoft.com/features/azure-portal/) Als u deze wilt openen, [meldt u zich aan bij de portal,](https://portal.azure.com)selecteert u **Bladeren**en schuiven naar **Beveiligingscentrum**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Welke Azure-resources worden gecontroleerd door Azure Security Center?
Azure Security Center bewaakt de volgende Azure-bronnen:

* Virtuele machines (VM's) (inclusief [Cloud Services)](../cloud-services/cloud-services-choose-me.md)
* Virtuele-machineschaalsets
* Virtuele netwerken van Azure
* Containers
* Azure SQL-service
* Azure Storage-account
* Azure Web Apps (in [app-serviceomgeving)](../app-service/environment/intro.md)
* Partneroplossingen geïntegreerd met uw Azure-abonnement, zoals een firewall voor webtoepassingen op VM's en op de App-serviceomgeving

Bovendien kunnen niet-Azure -machines (inclusief on-premises) machines ook worden gecontroleerd door Azure Security Center. Zowel [Windows-machines](./quick-onboard-windows-computer.md) als [Linux-machines](./quick-onboard-linux-computer.md) worden ondersteund.


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hoe kan ik de huidige beveiligingsstatus van mijn Azure-bronnen zien?
De pagina **Overzicht van het Beveiligingscentrum** toont de algemene beveiligingshouding van uw omgeving, onderverdeeld naar Compute, Networking, Storage & gegevens en toepassingen. Elk resourcetype heeft een indicator die geïdentificeerde beveiligingsproblemen weergeeft. Als u op elke tegel klikt, wordt een lijst weergegeven met beveiligingsproblemen die door Security Center zijn geïdentificeerd, samen met een inventaris van de bronnen in uw abonnement.



## <a name="what-is-a-security-policy"></a>Wat is een veiligheidsbeleid?
Een beveiligingsbeleid definieert de set besturingselementen die worden aanbevolen voor resources binnen het opgegeven abonnement. In Azure Security Center definieert u beleidsregels voor uw Azure-abonnementen op basis van de beveiligingsvereisten van uw bedrijf en het type toepassingen of de gevoeligheid van de gegevens in elk abonnement.

Het beveiligingsbeleid dat is ingeschakeld in Azure Security Center stimuleert beveiligingsaanbevelingen en -bewaking. Zie [Beveiligingsstatusbewaking in Azure Security Center](security-center-monitoring.md)voor meer informatie over beveiligingsbeleid.


## <a name="who-can-modify-a-security-policy"></a>Wie kan een beveiligingsbeleid wijzigen?
Als u een beveiligingsbeleid wilt wijzigen, moet u een beveiligingsbeheerder of eigenaar of bijdrager van dat abonnement zijn.

Zie [Beveiligingsbeleid instellen in Azure Security Center](tutorial-security-policy.md)voor meer informatie over het configureren van een beveiligingsbeleid.


## <a name="what-is-a-security-recommendation"></a>Wat is een beveiligingsaanbeveling?
Azure Security Center analyseert de beveiligingsstatus van uw Azure-bronnen. Wanneer potentiële beveiligingsproblemen worden geïdentificeerd, worden aanbevelingen gemaakt. De aanbevelingen begeleiden u bij het configureren van de benodigde besturing. Een aantal voorbeelden:

* Inrichting van anti-malware om schadelijke software te identificeren en te verwijderen
* [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md) en -regels om verkeer naar virtuele machines te beheren
* Inrichting van een webapplicatie firewall om te helpen verdedigen tegen aanvallen gericht op uw webapplicaties
* Implementatie van ontbrekende systeemupdates
* Aanpassing van besturingssysteemconfiguraties die niet overeenkomen met de aanbevolen basislijnen

Alleen aanbevelingen die zijn ingeschakeld in beveiligingsbeleid worden hier weergegeven.



## <a name="what-triggers-a-security-alert"></a>Wat activeert een beveiligingsalarm?
Azure Security Center verzamelt, analyseert en fuseert automatisch logboekgegevens van uw Azure-bronnen, het netwerk en partneroplossingen zoals antimalware en firewalls. Wanneer er dreigingen worden gedetecteerd, wordt een beveiligingswaarschuwing gemaakt. Voorbeelden zijn detectie van:

* Geïnfecteerde virtuele machines die communiceren met bekende schadelijke IP-adressen
* Geavanceerde malware gedetecteerd met behulp van Windows-foutrapportage
* Beveiligingsaanvallen op virtuele machines
* Beveiligingswaarschuwingen van geïntegreerde oplossingen voor partnerbeveiliging, zoals antimalware of firewalls voor webtoepassingen


## <a name="why-did-secure-score-values-change"></a>Waarom zijn de waarden van Secure Score gewijzigd? <a name="secure-score-faq"></a>
Vanaf februari 2019 heeft Security Center de score van een aantal aanbevelingen aangepast om beter aan hun ernst te voldoen. Als gevolg van deze aanpassing kunnen er wijzigingen zijn in de algemene waarde van de beveiligde score.  Zie Berekening van de [beveiligde score](security-center-secure-score.md)voor meer informatie over de beveiligde score.


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Wat is het verschil tussen bedreigingen gedetecteerd en gewaarschuwd door Microsoft Security Response Center versus Azure Security Center?
Het Microsoft Security Response Center (MSRC) voert geselecteerde beveiligingsbewaking uit van het Azure-netwerk en de infrastructuur en ontvangt klachten over bedreigingsinformatie en misbruik van derden. Wanneer MSRC zich ervan bewust wordt dat klantgegevens zijn geopend door een onwettige of onbevoegde partij of dat het gebruik van Azure door de klant niet voldoet aan de voorwaarden voor acceptabel gebruik, waarschuwt een beveiligingsincidentmanager de klant. Melding gebeurt meestal door een e-mail te verzenden naar de beveiligingscontactpersonen die zijn opgegeven in Azure Security Center of de azure-abonnementseigenaar als een beveiligingscontactpersoon niet is opgegeven.

Security Center is een Azure-service die continu de Azure-omgeving van de klant bewaakt en analyses toepast om automatisch een breed scala aan mogelijk schadelijke activiteiten te detecteren. Deze detecties worden weergegeven als beveiligingswaarschuwingen in het dashboard van het Beveiligingscentrum.
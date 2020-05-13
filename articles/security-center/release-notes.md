---
title: Release opmerkingen voor Azure Security Center
description: Een beschrijving van wat er nieuw is en gewijzigd in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/11/2020
ms.author: memildin
ms.openlocfilehash: bfe1e5d6a0c4171a262b36387f02be356fb1d72d
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210894"
---
# <a name="whats-new-in-azure-security-center"></a>Wat is er nieuw in Azure Security Center?

Azure-beveiliging is in actieve ontwikkeling en ontvangt voortdurend verbeteringen. Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt deze pagina informatie over:

- Nieuwe functies
- Opgeloste fouten
- Afgeschafte functionaliteit

Deze pagina wordt regel matig bijgewerkt. Ga daarom vaak opnieuw te werk. Als u op zoek bent naar items die ouder zijn dan zes maanden, kunt u deze vinden in het [Archief voor wat er nieuw is in azure Security Center](release-notes-archive.md).


## <a name="may-2020"></a>Mei 2020

### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Wijzigingen in de just-in-time-toegang van virtuele machines (VM)

Security Center bevat een optionele functie waarmee u de beheer poorten van uw virtuele machines kunt beveiligen. Dit biedt een verdediging tegen de meest voorkomende vorm van beveiligings aanvallen.

Met deze update worden de volgende wijzigingen aangebracht in deze functie:

- De aanbeveling waarmee u wordt geadviseerd om JIT in te scha kelen voor een VM, is hernoemd. Voorheen moet ' just-in-time-netwerk toegangs beheer worden toegepast op virtuele machines ' nu: ' beheer poorten van virtuele machines moeten worden beveiligd met Just-in-time-netwerk toegangs beheer '.

- De aanbeveling is zo ingesteld dat deze alleen wordt geactiveerd als er open beheer poorten zijn.

Meer [informatie over de JIT-toegangs functie](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk beveiligings beheer

Een van de beveiligings besturings elementen die zijn geïntroduceerd in de verbeterde beveiligde Score is ' Implementeer beveiligings best practices '. Aangepaste aanbevelingen die zijn gemaakt voor uw abonnementen, worden automatisch in dat besturings element geplaatst. 

Om u te helpen uw aangepaste aanbevelingen gemakkelijker te vinden, zijn deze naar een speciaal beveiligings beheer, ' aangepaste aanbevelingen ', verplaatst. Dit besturings element heeft geen invloed op uw beveiligde Score.

Meer informatie over beveiligings controles in [Enhanced Secure Score (preview) in azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Toevoegen/verbergen om aanbevelingen in besturings elementen of als een platte lijst weer te geven

Beveiligings controles zijn logische groepen van gerelateerde beveiligings aanbevelingen. Ze zijn gebaseerd op uw kwets bare aanvals oppervlakken. Een besturings element is een reeks beveiligings aanbevelingen met instructies die u helpen bij het implementeren van deze aanbevelingen.

Als u onmiddellijk wilt zien hoe goed uw organisatie een afzonderlijke kwets baarheid beveiligt, controleert u de scores voor elk beveiligings beheer.

Uw aanbevelingen worden standaard weer gegeven in de beveiligings controles. Vanuit deze update kunt u ze ook weer geven als een lijst. Als u deze wilt weer geven als een eenvoudige lijst, gesorteerd op de status van de betrokken resources, gebruikt u de nieuwe wissel knop Group by Controls. De wissel knop bevindt zich boven de lijst in de portal.

De beveiligings besturings elementen-en deze wissel knop maken deel uit van de nieuwe beveiligde Score ervaring. Vergeet niet om ons uw feedback te sturen vanuit de portal.

Meer informatie over beveiligings controles in [Enhanced Secure Score (preview) in azure Security Center](secure-score-security-controls.md).


### <a name="account-security-recommendations-moved-to-security-best-practices-security-control"></a>Aanbevelingen voor account beveiliging worden verplaatst naar beveiligings beheer voor aanbevolen beveiligings procedures

Een van de beveiligings besturings elementen die zijn geïntroduceerd in de verbeterde beveiligde Score is ' best practices voor beveiliging '. Wanneer een aanbeveling zich in dit besturings element bevindt, heeft dit geen invloed op de beveiligde Score. 

Met deze update zijn drie aanbevelingen verplaatst van de besturings elementen waarin deze oorspronkelijk zijn geplaatst, en in dit best practices-besturings element. We hebben deze stap doorgevoerd omdat er is vastgesteld dat het risico van deze drie aanbevelingen lager is dan oorspronkelijk werd aangenomen.

De aanbevelingen zijn:

- MFA moet zijn ingeschakeld voor accounts met lees machtigingen voor uw abonnement (oorspronkelijk in het besturings element MFA inschakelen)
- Externe accounts met lees machtigingen moeten worden verwijderd uit uw abonnement (oorspronkelijk in het besturings element toegang en machtigingen beheren)
- Er moeten Maxi maal drie eigen aren worden opgegeven voor uw abonnement (oorspronkelijk in het besturings element toegang en machtigingen beheren)

Meer informatie over beveiligings controles in [Enhanced Secure Score (preview) in azure Security Center](secure-score-security-controls.md).


### <a name="custom-policies-with-custom-metadata-generally-available"></a>Aangepaste beleids regels met aangepaste meta gegevens algemeen beschikbaar

Aangepaste beleids regels maken nu deel uit van de Security Center aanbevelingen, beveiligde scores en het dash board nalevings normen voor regelgeving. Deze functie is nu algemeen beschikbaar en biedt u de mogelijkheid om de beveiligings beoordelings dekking van uw organisatie in Security Center uit te breiden. 

Maak een aangepaste initiatief in azure Policy, voeg er beleids regels aan toe en bewaart dit voor Azure Security Center, en visualiseer het als aanbevelingen.

We hebben nu ook de optie toegevoegd voor het bewerken van de aangepaste aanbevelings-meta gegevens. Meta gegevens opties zijn ernst, herstel stappen, informatie over bedreigingen en meer.  

Meer informatie [over het verbeteren van uw aangepaste aanbevelingen met gedetailleerde informatie](custom-security-policies.md#enhancing-your-custom-recommendations-with-detailed-information).


## <a name="april-2020"></a>April 2020

### <a name="dynamic-compliance-packages-now-generally-available"></a>Dynamische nalevings pakketten zijn nu algemeen beschikbaar

Het dash board voor nalevings vereisten van Azure Security Center bevat nu **dynamische nalevings pakketten** (nu algemeen beschikbaar) voor het bijhouden van aanvullende industrie-en regelgevings normen.

Dynamische nalevings pakketten kunnen worden toegevoegd aan uw abonnement of beheer groep op de pagina Security Center beveiligings beleid. Wanneer u een Standard-of Bench Mark hebt voor bereid, wordt de standaard weer gegeven in het dash board nalevings beleid met alle gekoppelde nalevings gegevens die zijn toegewezen als evaluaties. Een samenvattings rapport voor een van de beschik bare standaarden kan worden gedownload.

U kunt nu standaarden toevoegen zoals:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK-officiële en UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nieuw)** (dit is een meer volledige weer gave van Azure CIS 1.1.0)

Daarnaast hebben we onlangs de **Azure Security-Bench Mark**toegevoegd, de door micro soft geschreven specifieke Azure-richt lijnen voor de aanbevolen procedures voor beveiliging en naleving op basis van algemene nalevings kaders. Er worden extra standaarden ondersteund in het dash board, zodra deze beschikbaar komen.  
 
[Meer informatie over het aanpassen van de set normen in uw nalevings dashboard](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Aanbevelingen voor de identiteit nu opgenomen in Azure Security Center gratis laag

Beveiligings aanbevelingen voor identiteit en toegang op de laag Azure Security Center gratis zijn nu algemeen beschikbaar. Dit maakt deel uit van de inspanningen om de CSPM-functies (Cloud Security postuur Management) gratis te maken. Tot nu toe zijn deze aanbevelingen alleen beschikbaar in de prijs categorie Standard.

Voor beelden van aanbevelingen voor identiteits-en toegangs rechten zijn:

- "Multi-factor Authentication moet worden ingeschakeld voor accounts met eigenaars machtigingen voor uw abonnement."
- "Maxi maal drie eigen aren moeten worden opgegeven voor uw abonnement."
- "Afgeschafte accounts moeten worden verwijderd uit uw abonnement."

Als u abonnementen hebt op de prijs categorie gratis, worden hun beveiligde scores beïnvloed door deze wijziging omdat ze nooit werden beoordeeld voor hun identiteits-en toegangs beveiliging.

Meer [informatie over aanbevelingen voor identiteit en toegang](recommendations-reference.md#recs-identity).
Meer [informatie over het bewaken van identiteit en toegang](security-center-identity-access.md).


## <a name="march-2020"></a>Maart 2020

### <a name="workflow-automation-is-now-generally-available"></a>Werk stroom automatisering is nu algemeen beschikbaar

De functie werk stroom automatisering van Azure Security Center is nu algemeen beschikbaar. Gebruik deze functie om automatisch Logic Apps te activeren voor beveiligings waarschuwingen en aanbevelingen. Daarnaast zijn er hand matige triggers beschikbaar voor waarschuwingen en worden alle aanbevelingen met de optie snel herstellen beschikbaar.

Elk beveiligings programma bevat meerdere werk stromen voor reactie op incidenten. Deze processen kunnen het melden van relevante belanghebbenden, het starten van een wijzigings beheer proces en het Toep assen van specifieke herbemiddelings stappen zijn. Beveiligings experts raden u aan zo veel mogelijk stappen van deze procedures te automatiseren. Automation vermindert de overhead en kan de beveiliging verbeteren door ervoor te zorgen dat de proces stappen snel, consistent en volgens uw vooraf gedefinieerde vereisten worden uitgevoerd.

Zie [werk stroom automatisering](workflow-automation.md)voor meer informatie over de mogelijkheden voor automatische en hand matige Security Center voor het uitvoeren van uw werk stromen.

Zie [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)voor meer informatie over het maken van Logic apps.


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integratie van Azure Security Center met Windows-beheer centrum

Het is nu mogelijk om uw on-premises Windows-servers rechtstreeks naar de Azure Security Center te verplaatsen vanuit het Windows-beheer centrum. Security Center wordt vervolgens uw enige glas venster om beveiligings gegevens weer te geven voor alle resources van uw Windows-beheer centrum, waaronder on-premises servers, virtuele machines en extra PaaS-workloads.

Nadat u een server hebt verplaatst van het Windows-beheer centrum naar Azure Security Center, kunt u het volgende doen:

- Beveiligings waarschuwingen en aanbevelingen weer geven in de uitbrei ding Security Center van het Windows-beheer centrum.
- Bekijk de beveiligings postuur en haal meer gedetailleerde informatie op over uw door Windows-beheer centrum beheerde servers in de Security Center in het Azure Portal (of via een API).

Meer informatie over het [integreren van Azure Security Center met het Windows-beheer centrum](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Beveiliging voor de Azure Kubernetes-service

Azure Security Center breidt de beveiligings functies van de container uit om Azure Kubernetes service (AKS) te beveiligen.

De populaire open-source platform Kubernetes is zodanig aangenomen dat het nu een industrie standaard is voor container indeling. Ondanks deze wijde implementatie is er nog steeds geen informatie over het beveiligen van een Kubernetes-omgeving. Voor het beschermen van de kwets baarheid van een container toepassing is expertise vereist om te garanderen dat de infra structuur veilig is geconfigureerd en voortdurend wordt bewaakt voor mogelijke dreigingen.

De Security Center verdediging omvat:

- **Detectie en zicht baarheid** : doorlopende detectie van beheerde AKS-instanties binnen de abonnementen die zijn geregistreerd voor Security Center.
- **Aanbevelingen voor beveiliging** -beschik bare aanbevelingen om u te helpen te voldoen aan de aanbevolen procedures voor beveiliging voor AKS. Deze aanbevelingen zijn opgenomen in uw beveiligde Score om ervoor te zorgen dat ze worden weer gegeven als onderdeel van de beveiligings postuur van uw organisatie. Een voor beeld van een AKS-aanbeveling die u kunt zien, is ' op rollen gebaseerd toegangs beheer moet worden gebruikt om de toegang tot een Kubernetes-service cluster te beperken '.
- **Bedreigings beveiliging** : Security Center u wordt gewaarschuwd voor bedreigingen en schadelijke activiteiten die zijn gedetecteerd op het niveau van de host en het AKS-cluster.

Meer [informatie over de integratie van Azure Kubernetes Services met Security Center](azure-kubernetes-service-integration.md).
Meer [informatie over de beveiligings functies van de container in Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Verbeterde just-in-time-ervaring

De functies, bewerking en gebruikers interface voor het Azure Security Center van just-in-time hulpprogram ma's die uw beheer poorten beveiligen, zijn als volgt verbeterd: 

- **Veld motivering** : als u via de just-in-time pagina van de Azure portal toegang tot een virtuele machine (VM) wilt aanvragen, is er een nieuw optioneel veld beschikbaar om een reden voor de aanvraag in te voeren. Gegevens die in dit veld worden ingevoerd, kunnen in het activiteiten logboek worden bijgehouden. 
- **Automatisch opschonen van redundante just-in-time (JIT)-regels** : wanneer u een JIT-beleid bijwerkt, wordt automatisch een opschoon programma uitgevoerd om de geldigheid van de volledige ruleset te controleren. Het hulp programma zoekt naar verschillen tussen regels in uw beleid en regels in het NSG. Als het hulp programma voor opschonen een niet-overeenkomend item detecteert, wordt de oorzaak bepaald en, wanneer het veilig is om dit te doen, verwijdert u ingebouwde regels die niet meer nodig zijn. De verruiming verwijdert nooit regels die u hebt gemaakt. 

Meer [informatie over de JIT-toegangs functie](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Twee beveiligings aanbevelingen voor afgeschafte webtoepassingen

Twee beveiligings aanbevelingen met betrekking tot webtoepassingen worden afgeschaft: 

- De regels voor webtoepassingen op IaaS Nsg's moeten worden gehard.
    (Gerelateerd beleid: de Nsg's-regels voor webtoepassingen op IaaS moeten worden gehard)

- Toegang tot App Services moet worden beperkt.
    (Gerelateerd beleid: toegang tot App Services moet worden beperkt [Preview])

Deze aanbevelingen worden niet meer weer gegeven in de Security Center lijst met aanbevelingen. Het gerelateerde beleid wordt niet meer opgenomen in het initiatief ' Security Center default '.

Meer [informatie over beveiligings aanbevelingen](recommendations-reference.md).

## <a name="february-2020"></a>Februari 2020

### <a name="fileless-attack-detection-for-linux-is-now-in-preview"></a>Aanvals detectie met een bestand voor Linux is nu beschikbaar als preview-versie

Als aanvallers stealthier Azure Security Center-methoden gebruiken om detectie te voor komen, wordt in aanvulling op Windows de detectie van de aanval van bestanden voor Linux uitgebreid. Aanvallen waarbij misbruik wordt gemaakt van software, schadelijke nettoladingen in goed aardige systeem processen injecteren en in het geheugen verbergen. Deze technieken:

- traceringen van malware op schijf minimaliseren of elimineren
- de kans op detectie door op schijf gebaseerde scan oplossingen op basis van malware aanzienlijk beperken

Als u deze bedreiging wilt bemeteren, Azure Security Center u de aanvals detectie van bestanden voor Windows in oktober 2018 vrijgegeven en nu ook uitgebreide aanvals detectie op Linux. 


## <a name="january-2020"></a>Januari 2020

### <a name="enhanced-secure-score"></a>Verbeterde beveiligde Score

Een verbeterde versie van de functie beveiligde Score van Azure Security Center is nu beschikbaar als preview. In deze versie zijn meerdere aanbevelingen onderverdeeld in beveiligings controles die beter aansluiten op uw kwets bare aanvallen (bijvoorbeeld de toegang tot beheer poorten beperken).

Lees meer over de wijzigingen in de beveiligde score tijdens de preview-fase en bepaal andere herstel bewerkingen waarmee u uw omgeving verder kunt beveiligen.

Meer informatie vindt u in de [verbeterde beveiligde Score (preview) in azure Security Center](secure-score-security-controls.md).
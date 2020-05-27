---
title: Archief van wat is er nieuw in Azure Security Center
description: Een beschrijving van wat er nieuw is en is gewijzigd in Azure Security Center van zes maanden geleden en eerder.
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
ms.openlocfilehash: 253ea748462ec09cd9cf4eab14c05be678cfb82d
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860899"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Wilt u archiveren wat er nieuw is in Azure Security Center?

De belangrijkste [nieuwe functies in azure Security Center?](release-notes.md) de pagina release opmerkingen bevat updates voor de afgelopen zes maanden, terwijl deze pagina oudere items bevat.

Op deze pagina vindt u informatie over:

- Nieuwe functies
- Opgeloste fouten
- Afgeschafte functionaliteit

## <a name="november-2019"></a>November 2019

### <a name="threat-protection-for-azure-key-vault-in-public-preview-in-north-america-regions"></a>Bedreigings beveiliging voor Azure Key Vault in de open bare preview in Noord-Amerika regio's

Azure Key Vault is een essentiële service voor het beveiligen van gegevens en het verbeteren van de prestaties van Cloud toepassingen door de mogelijkheid te bieden om sleutels, geheimen, cryptografische sleutels en beleids regels centraal te beheren in de Cloud. Omdat Azure Key Vault gevoelige en bedrijfskritische gegevens opslaat, is er maximale beveiliging vereist voor de sleutel kluizen en de gegevens die erin zijn opgeslagen.

Azure Security Center ondersteuning voor beveiliging tegen bedreigingen voor Azure Key Vault biedt een extra beveiligingslaag die ongebruikelijke en mogelijk schadelijke pogingen van toegang tot of misbruik van sleutel kluizen detecteert. Met deze nieuwe beveiligingslaag kunnen klanten bedreigingen tegen hun sleutel kluizen aanpakken zonder een beveiligings expert of systemen voor beveiligings bewaking te beheren. De functie bevindt zich in de open bare preview van Noord-Amerika regio's.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Bedreigings beveiliging voor Azure Storage omvat het controleren van malware-reputatie

Bedreigings beveiliging voor Azure Storage biedt nieuwe detecties met micro soft Threat Intelligence voor het detecteren van malware-uploads naar Azure Storage met behulp van hash-reputatie analyse en verdachte toegang vanaf een actieve Tor-afsluit knooppunt (een anoniem-proxy). U kunt nu gedetecteerde malware voor opslag accounts weer geven met behulp van Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Werk stroom automatisering met Logic Apps (preview-versie)

Organisaties met centraal beheerde beveiliging en IT/bewerkingen implementeren interne werk stroom processen om de vereiste actie binnen de organisatie te testen wanneer er discrepanties worden gedetecteerd in hun omgevingen. In veel gevallen kunnen deze werk stromen Herhaal bare processen en automatisering de processen binnen de organisatie aanzienlijk stroom lijnen.

Vandaag introduceren we een nieuwe mogelijkheid in Security Center waarmee klanten automatiserings configuraties kunnen maken met behulp van Azure Logic Apps en om beleids regels te maken waarmee ze automatisch worden geactiveerd op basis van specifieke ASC-bevindingen zoals aanbevelingen of waarschuwingen. De Azure Logic-app kan worden geconfigureerd voor het uitvoeren van aangepaste acties die worden ondersteund door de enorme community van Logic app-connectors, of om een van de sjablonen te gebruiken die worden geboden door Security Center, zoals het verzenden van een e-mail bericht of het openen van een ServiceNow™-ticket.

Zie [werk stroom automatisering](workflow-automation.md)voor meer informatie over de mogelijkheden voor automatische en hand matige Security Center voor het uitvoeren van uw werk stromen.

Zie [Azure Logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)voor meer informatie over het maken van Logic apps.


### <a name="quick-fix-for-bulk-resources-generally-available"></a>Snelle oplossing voor bulk bronnen algemeen beschikbaar

Met de vele taken die een gebruiker krijgt als onderdeel van een beveiligde Score, kan het lastig zijn om problemen in een grote vloot te herstellen.

Voor het vereenvoudigen van het herstel van onjuiste beveiligings configuraties en voor het snel oplossen van aanbevelingen voor een groot aantal resources en het verbeteren van uw beveiligde Score, gebruikt u snel herstel herstellen.

Met deze bewerking kunt u de resources selecteren waarop u het herstel wilt Toep assen en een herstel actie starten waarmee de instelling namens u wordt geconfigureerd.

Snelle correctie is in het algemeen beschikbaar als onderdeel van de pagina met Security Center aanbevelingen.

Zie in de [Naslag Gids voor beveiligings aanbevelingen voor](recommendations-reference.md)meer informatie over de aanbevelingen waarvoor snelle oplossing is ingeschakeld.


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Container installatie kopieën scannen op beveiligings problemen (preview-versie)

Azure Security Center kunt nu container installatie kopieën in Azure Container Registry scannen op beveiligings problemen.

De scan van de installatie kopie werkt door het parseren van het container installatie kopie bestand en vervolgens te controleren of er bekende beveiligings problemen zijn (mogelijk gemaakt door Qualys).

De scan zelf wordt automatisch geactiveerd wanneer nieuwe container installatie kopieën naar Azure Container Registry worden gepusht. Gevonden beveiligings problemen worden weer gegeven als Security Center aanbevelingen en opgenomen in de beveiligde Score van Azure, samen met informatie over hoe u deze kunt patchen om de kwets baarheid voor aanvallen te verminderen.


### <a name="additional-regulatory-compliance-standards-preview"></a>Aanvullende normen voor naleving van regelgeving (preview-versie)

Het nalevings Dashboard van de regelgeving biedt inzicht in uw nalevings postuur op basis van Security Center Beoordelingen. Het dash board laat zien hoe uw omgeving voldoet aan de besturings elementen en vereisten die zijn aangewezen door specifieke regelgevings normen en industrie benchmarks en biedt voorschrijvende aanbevelingen voor het oplossen van deze vereisten.

Het nalevings Dashboard van de regelgeving heeft tot nu toe vier ingebouwde standaarden ondersteund: Azure CIS 1.1.0, PCI-DSS, ISO 27001 en SOC-TSP. We kondigen nu de open bare preview-versie van aanvullende ondersteunde standaarden: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM en UK ambtenaar samen met UK NHS. We hebben ook een bijgewerkte versie van Azure CIS 1.1.0 uitgebracht, waardoor er meer besturings elementen van de standaard en uitbreid baarheid worden uitgebreid.

[Meer informatie over het aanpassen van de set normen in uw nalevings dashboard](update-regulatory-compliance-packages.md).


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Bedreigings beveiliging voor Azure Kubernetes service (preview)

Kubernetes is snel de nieuwe standaard voor het implementeren en beheren van software in de Cloud. Enkele mensen hebben uitgebreide ervaring met Kubernetes en veel hebben alleen aandacht besteed aan de algemene engineering en het beheer van de beveiliging. De Kubernetes-omgeving moet zorgvuldig worden geconfigureerd om te worden beveiligd, waardoor er geen toegang is tot de containers die zijn gericht op de aanvals oppervlakte, voor aanvallers open wordt gesteld. Security Center wordt de ondersteuning in de container ruimte uitgebreid naar een van de snelste groeiende Services in azure-Azure Kubernetes service (AKS).

De nieuwe mogelijkheden in deze open bare preview-versie zijn onder andere:

- **Detectie & zicht baarheid** : doorlopende detectie van beheerde AKS-instanties binnen de geregistreerde abonnementen van Security Center.
- **Veilige Score aanbevelingen** : items die kunnen worden gebruikt om te helpen bij het voldoen aan de aanbevolen procedures voor beveiliging in AKS als onderdeel van de beveiligde Score van de klant, zoals "op rollen gebaseerd Access Control moeten worden toegepast om de toegang tot een Kubernetes-service cluster te beperken".
- **Detectie van bedreigingen** -host-en cluster-analyses, zoals ' een geprivilegieerde container gedetecteerd '.


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Evaluatie van beveiligings problemen met virtuele machines (preview-versie)

Toepassingen die in virtuele machines worden geïnstalleerd, hebben vaak beveiligings problemen die kunnen leiden tot een schending van de virtuele machine. We kondigen dat de Security Center Standard-laag geïntegreerde evaluatie van beveiligings problemen bevat voor virtuele machines zonder extra kosten. Met de evaluatie van beveiligings problemen, mogelijk gemaakt door Qualys in de open bare preview, kunt u voortdurend alle geïnstalleerde toepassingen op een virtuele machine scannen om te zoeken naar kwets bare toepassingen en de bevindingen presen teren in de ervaring van de Security Center Portal. Security Center zorgt voor alle implementatie bewerkingen, zodat er geen extra werk van de gebruiker is vereist. We zijn van plan om opties voor evaluatie van beveiligings problemen op te geven voor het ondersteunen van de unieke bedrijfs behoeften van onze klanten.

Meer [informatie over de evaluatie van beveiligings problemen voor uw Azure-virtual machines](security-center-vulnerability-assessment-recommendations.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Geavanceerde gegevens beveiliging voor SQL-servers in azure Virtual Machines (preview-versie)

De ondersteuning van Azure Security Center voor beveiliging tegen bedreigingen en evaluatie van beveiligings problemen voor SQL-Db's op IaaS Vm's is nu beschikbaar als preview-versie.

[Evaluatie van beveiligings problemen](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) is een eenvoudig te configureren service waarmee u mogelijke beveiligings problemen met een Data Base kunt detecteren, bijhouden en helpen oplossen. Het biedt inzicht in uw beveiligings postuur als onderdeel van een beveiligde Azure-Score en bevat de stappen voor het oplossen van beveiligings problemen en het verbeteren van uw data base-Fortifications.

[Geavanceerde bedreigingen beveiliging](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot uw SQL-Server of deze misbruiken. Het controleert uw data base voortdurend op verdachte activiteiten en biedt actie gerichte beveiligings waarschuwingen voor afwijkende database toegangs patronen. Deze waarschuwingen bieden informatie over verdachte activiteiten en aanbevolen acties voor het onderzoeken en oplossen van de dreiging.


### <a name="support-for-custom-policies-preview"></a>Ondersteuning voor aangepast beleid (preview-versie)

Azure Security Center ondersteunt nu aangepaste beleids regels (in preview-versie).

Onze klanten willen hun huidige dekking van beveiligings beoordelingen in Security Center uitbreiden met hun eigen beveiligings beoordelingen op basis van beleids regels die ze in Azure Policy maken. Met ondersteuning voor aangepast beleid is dit nu mogelijk.

Deze nieuwe beleids regels zullen deel uitmaken van de Security Center aanbevelingen, een beveiligde Score en het dash board nalevings normen. Met de ondersteuning voor aangepast beleid kunt u nu een aangepast initiatief maken in Azure Policy, het vervolgens als beleid toevoegen in Security Center en dit als een aanbeveling visualiseren.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Azure Security Center dekking uitbreiden met platform voor community en partners

Gebruik Security Center om aanbevelingen te ontvangen die niet alleen van micro soft zijn, maar ook van bestaande oplossingen van partners, zoals Check Point, Tenable en CyberArk met veel meer integraties.  Met de eenvoudige stroom voor onboarding van Security Center kan uw bestaande oplossingen worden verbonden met Security Center, zodat u uw aanbevelingen voor beveiligings postuur op één plek kunt bekijken, Unified rapporten moet uitvoeren en alle mogelijkheden van Security Center kunt benutten voor zowel ingebouwde als partner aanbevelingen. U kunt ook Security Center aanbevelingen exporteren naar partner producten.

Meer [informatie over intelligente beveiligings koppelingen van micro soft](https://www.microsoft.com/security/partnerships/intelligent-security-association).



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Geavanceerde integraties met export van aanbevelingen en waarschuwingen (preview-versie)

Om scenario's op ondernemings niveau boven op Security Center in te scha kelen, is het nu mogelijk om Security Center waarschuwingen en aanbevelingen op extra plaatsen te gebruiken, met uitzonde ring van de Azure Portal of API. Deze kunnen rechtstreeks worden geëxporteerd naar een event hub en naar Log Analytics-werk ruimten. Hier volgen enkele werk stromen die u kunt maken rond deze nieuwe mogelijkheden:

- Met exporteren naar Log Analytics werk ruimte kunt u aangepaste Dash boards maken met Power BI.
- Met exporteren naar Event hub kunt u Security Center waarschuwingen en aanbevelingen exporteren naar uw Siem's van derden, naar een oplossing van derden in real-time of Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>On-premises servers onboarden naar Security Center vanuit het Windows-beheer centrum (preview-versie)

Windows-beheer centrum is een beheer portal voor Windows-servers die niet zijn geïmplementeerd in Azure en die geen verschillende Azure-beheer mogelijkheden, zoals back-up-en systeem updates, aanbieden. We hebben onlangs een mogelijkheid toegevoegd om deze niet-Azure-servers te laten beveiligen door deze rechtstreeks vanuit de Windows-beheer centrum-ervaring op te ruimen.

Met deze nieuwe ervaring hebben gebruikers een WAC-server voor Azure Security Center en kunnen ze de beveiligings waarschuwingen en aanbevelingen rechtstreeks in de Windows-beheer centrum-ervaring weer geven.


## <a name="september-2019"></a>September 2019

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Regels beheren met verbeteringen voor besturings elementen voor adaptieve toepassingen

De ervaring van het beheren van regels voor virtuele machines met behulp van adaptieve toepassings besturings elementen is verbeterd. Met de adaptieve toepassings besturings elementen van Azure Security Center kunt u bepalen welke toepassingen kunnen worden uitgevoerd op uw virtuele machines. Naast een algemene verbetering van regel beheer, kunt u met een nieuw voor deel bepalen welke bestands typen worden beveiligd wanneer u een nieuwe regel toevoegt.

Meer [informatie over adaptieve toepassings besturings elementen](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Aanbevelingen voor controle container beveiliging met behulp van Azure Policy

De aanbeveling van Azure Security Center voor het oplossen van beveiligings problemen in container beveiliging kan nu worden ingeschakeld of uitgeschakeld via Azure Policy.

Als u uw ingeschakelde beveiligings beleid wilt bekijken, opent u de pagina beveiligings beleid vanuit Security Center.


## <a name="august-2019"></a>Augustus 2019

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Just-in-time-VM-toegang voor Azure Firewall 

Just-in-time-VM-toegang voor Azure Firewall is nu algemeen beschikbaar. Gebruik het om uw Azure Firewall beveiligde omgevingen te beveiligen naast uw beveiligde NSG-omgevingen.

JIT-VM-toegang vermindert de bloot stelling aan netwerk volumetrische aanvallen door alleen beheerde toegang tot Vm's te bieden wanneer dit nodig is, met behulp van uw NSG-en Azure Firewall regels.

Wanneer u JIT voor uw virtuele machines inschakelt, kunt u een beleid maken dat bepaalt welke poorten moeten worden beveiligd, hoe lang de poorten open blijven en goedgekeurde IP-adressen van waaruit deze poorten toegankelijk zijn. Dit beleid helpt u de controle te houden over wat gebruikers kunnen doen wanneer ze toegang aanvragen.

Aanvragen worden vastgelegd in het Azure-activiteiten logboek, zodat u de toegang eenvoudig kunt bewaken en controleren. De just-in-time pagina helpt u ook snel bestaande Vm's te identificeren waarvoor JIT is ingeschakeld en Vm's waarvoor JIT wordt aanbevolen.

Meer [informatie over Azure firewall](https://docs.microsoft.com/azure/firewall/overview).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Eén Klik op herbemiddeling om uw beveiligings postuur te verbeteren (preview-versie)

Secure Score is een hulp programma waarmee u de beveiligings postuur van uw werk belasting kunt beoordelen. Hiermee worden uw beveiligings aanbevelingen beoordeeld en worden prioriteiten voor u gegeven, zodat u weet welke aanbevelingen het eerst moeten worden uitgevoerd. Zo kunt u de meest ernstige beveiligings problemen vinden om het onderzoek te priori teren.

We hebben een nieuwe mogelijkheid toegevoegd waarmee u een aanbeveling kunt oplossen voor een groot aantal resources met één klik om het herstel van de configuratie van de beveiliging te vereenvoudigen en u te helpen uw beveiligde score snel te verbeteren.

Met deze bewerking kunt u de resources selecteren waarop u het herstel wilt Toep assen en een herstel actie starten waarmee de instelling namens u wordt geconfigureerd.

Zie in de [Naslag Gids voor beveiligings aanbevelingen voor](recommendations-reference.md)meer informatie over de aanbevelingen waarvoor snelle oplossing is ingeschakeld.


### <a name="cross-tenant-management"></a>Beheer in meerdere tenants

Security Center ondersteunt nu cross-Tenant beheer scenario's als onderdeel van Azure Lighthouse. Op die manier krijgt u inzicht in de beveiligings postuur van meerdere tenants in Security Center. 

Meer [informatie over de ervaring voor cross-Tenant beheer](security-center-cross-tenant-management.md).


## <a name="july-2019"></a>Juli 2019

### <a name="updates-to-network-recommendations"></a>Updates voor netwerk aanbevelingen

Azure Security Center (ASC) heeft nieuwe aanbevelingen van het netwerk gelanceerd en een aantal bestaande verbeteringen verbeterd. Nu met behulp van Security Center hebt u nog meer netwerk beveiliging voor uw resources. 

Meer [informatie over aanbevelingen voor het netwerk](recommendations-reference.md#recs-network).


## <a name="june-2019"></a>Juni 2019

### <a name="adaptive-network-hardening---generally-available"></a>Adaptieve netwerk beveiliging: algemeen beschikbaar

Een van de grootste kwets bare Opper vlakken voor werk belastingen die worden uitgevoerd in de publieke Cloud, is verbindingen van en naar het open bare Internet. Onze klanten vinden het moeilijk om te weten welke NSG-regels (netwerk beveiligings groep) moeten worden uitgevoerd om ervoor te zorgen dat Azure-workloads alleen beschikbaar zijn voor de vereiste bronbereiken. Met deze functie leert Security Center het netwerk verkeer en de verbindings patronen van Azure-workloads en NSG regel aanbevelingen voor Internet gerichte virtuele machines. Zo kan onze klant beter hun netwerk toegangs beleid configureren en de bloot stelling aan aanvallen beperken. 

Meer [informatie over adaptieve netwerk beveiliging](security-center-adaptive-network-hardening.md).

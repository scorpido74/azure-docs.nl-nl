---
title: Azure Security Bench Mark v2-respons op incident
description: Reactie van Azure Security Bench Mark v2-incident
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172607a7f8f036bbfb68e8d15e77b2a3e3fb5377
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326385"
---
# <a name="security-control-v2-incident-response"></a>Beveiligings controle v2: reactie op incidenten

Reactie op incidenten heeft betrekking op besturings elementen in de levens cyclus van incidenten-voor bereiding, detectie en analyse, insluiting en activiteiten na incidenten. Dit geldt ook voor het gebruik van Azure-Services, zoals Azure Security Center en Sentinel, om het respons proces van het incident te automatiseren.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: voor bereiding-respons proces van incidenten bijwerken voor Azure

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

Zorg ervoor dat uw organisatie processen heeft om te reageren op beveiligings incidenten, dat deze processen voor Azure zijn bijgewerkt en dat ze regel matig worden uitgevoerd om de gereedheid te garanderen.

- [Implementeer beveiliging in de bedrijfs omgeving](https://aka.ms/AzSec4)

- [Naslag Gids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informatie over bedreigingen](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: voor bereiding-incident melding instellen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| IR-2 | 19,5 | IR-4, IR-5, IR-6, IR-8 |

Contact gegevens voor beveiligings incidenten in Azure Security Center instellen. Deze contact gegevens worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. U hebt ook opties voor het aanpassen van incident waarschuwingen en meldingen in verschillende Azure-Services op basis van de behoeften van uw incident respons. 

- [De Azure Security Center Security-contact persoon instellen](../../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| IR-3 | 19,6 | IR-4, IR-5 |

Zorg ervoor dat u een proces hebt om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u lessen uit eerdere incidenten ontdekken en waarschuwingen voor analisten prioriteiten geven, zodat ze geen tijd verspillen op valse positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden gebouwd op basis van ervaringen van eerdere incidenten, gevalideerde community-bronnen en hulpprogram ma's die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaal bronnen te weigeren en te correleren. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC data connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. Met Azure Sentinel kunt u geavanceerde waarschuwings regels maken om incidenten automatisch te genereren voor een onderzoek. 

Exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de export functie om Risico's voor Azure-resources te identificeren. Waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren.

- [Exporteren configureren](../../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informatie over bedreigingen](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detectie en analyse: een incident onderzoeken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

Zorg ervoor dat analisten verschillende gegevens bronnen kunnen opvragen en gebruiken bij het onderzoeken van mogelijke incidenten, om een volledig overzicht te maken van wat er is gebeurd. Diverse logboeken moeten worden verzameld om de activiteiten van een mogelijke aanvaller in de Kill-keten te volgen om blinde vlekken te voor komen.  U moet er ook voor zorgen dat inzichten en informatie worden vastgelegd voor andere analisten en voor toekomstige historische Naslag informatie.  

De gegevens bronnen voor onderzoek bevatten de gecentraliseerde logboek registratie bronnen die al worden verzameld van de services binnen het bereik en het uitvoeren van systemen, maar kan ook het volgende omvatten:

- Netwerk gegevens: gebruik stroom logboeken voor netwerk beveiligings groepen, Azure Network Watcher en Azure Monitor om netwerk stroom logboeken en andere analyse-informatie vast te leggen. 

- Moment opnamen van actieve systemen: 

    - Gebruik de functie voor moment opnamen van de virtuele machine van Azure om een moment opname te maken van de schijf waarop het systeem wordt uitgevoerd. 

    - Gebruik de systeem eigen geheugen dump functie van het besturings systeem om een moment opname te maken van het actieve systeem geheugen.

    - Gebruik de functie snap shot van de Azure-Services of de eigen mogelijkheid van uw software om moment opnamen van de actieve systemen te maken.

Azure Sentinel biedt uitgebreide gegevens analyse voor vrijwel elke logboek bron en een case beheer Portal om de volledige levens duur van incidenten te beheren. Informatie over Intelligence tijdens een onderzoek kan worden gekoppeld aan een incident voor het bijhouden en rapporteren van de doel einden. 

- [Moment opname maken van de schijf van een Windows-computer](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Moment opname maken van de schijf van een Linux-machine](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure ondersteuning voor diagnostische gegevens en geheugen dump verzameling](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidenten onderzoeken met Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informatie over bedreigingen](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detectie en analyse: de prioriteit van incidenten bepalen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| IR-5 | 19,8 | CA-2, IR-4 |

Bied de context aan analisten waarop incidenten zich richten op basis van ernst van waarschuwingen en de gevoeligheid van een activa. 

Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevings systeem maken om Azure-resources te identificeren en categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [Labels gebruiken om uw Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informatie over bedreigingen](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: insluiting, uitroeiing en herstel: de verwerking van incidenten automatiseren

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP800-53 R4-ID ('s) |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

Automatiseer hand matige terugkerende taken om de reactie tijd te versnellen en de overhead voor analisten te verlagen. Het uitvoeren van hand matige taken duurt langer om uit te voeren, elk incident te vertragen en te verminderen hoeveel incidenten een analist kan verwerken. Hand matige taken verg Roten ook analisten intensief, waardoor het risico van menselijke fout wordt verg root dat vertragingen veroorzaakt, en de mogelijkheid van analisten om effectief te best Eden aan complexe taken te degraderen. Gebruik werk stroom automatiserings functies in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een Playbook uit te voeren om te reageren op binnenkomende beveiligings waarschuwingen. De Playbook neemt acties, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werk stroom automatisering configureren in Security Center](../../security-center/workflow-automation.md)

- [Automatische reacties op dreigingen instellen in Azure Security Center](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Automatische bedreigings reacties instellen in azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)

**Verantwoordelijkheid**: klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beveiligingsbewerkingen](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [Incidentvoorbereiding](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [Informatie over bedreigingen](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)


---
title: Aanbevolen procedures voor veilige ontwikkeling op Microsoft Azure
description: Aanbevolen procedures om u te helpen veiligere code te ontwikkelen en een veiligere toepassing in de cloud te implementeren.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934868"
---
# <a name="secure-development-best-practices-on-azure"></a>Aanbevolen procedures voor veilige ontwikkeling op Azure
In deze serie artikelen worden beveiligingsactiviteiten en besturingselementen gepresenteerd om rekening mee te houden wanneer u toepassingen voor de cloud ontwikkelt. De fasen van de Microsoft Security Development Lifecycle (SDL) en beveiligingsvragen en -concepten die u tijdens elke fase van de levenscyclus moet overwegen, komen aan bod. Het doel is om u te helpen bij het definiëren van activiteiten en Azure-services die u in elke fase van de levenscyclus gebruiken voor het ontwerpen, ontwikkelen en implementeren van een veiligere toepassing.

De aanbevelingen in de artikelen komen voort uit onze ervaring met Azure-beveiliging en uit de ervaringen van onze klanten. U deze artikelen gebruiken als referentie voor wat u moet overwegen tijdens een specifieke fase van uw ontwikkelingsproject, maar we raden u aan om ook alle artikelen van begin tot eind minstens één keer te lezen. Het lezen van alle artikelen laat u kennismaken met concepten die u mogelijk hebt gemist in eerdere fasen van uw project. Door deze concepten te implementeren voordat u uw product uitbrengt, u veilige software bouwen, voldoen aan de vereisten voor beveiligingscompliance en de ontwikkelingskosten verlagen.

Deze artikelen zijn bedoeld als een bron voor softwareontwerpers, ontwikkelaars en testers op alle niveaus die beveiligde Azure-toepassingen bouwen en implementeren.

## <a name="overview"></a>Overzicht

Beveiliging is een van de belangrijkste aspecten van elke toepassing, en het is niet een eenvoudig ding om goed te krijgen. Gelukkig biedt Azure veel services waarmee u uw toepassing in de cloud beveiligen. Deze artikelen hebben betrekking op activiteiten en Azure-services die u implementeren in elke fase van uw levenscyclus van softwareontwikkeling om u te helpen veiligere code te ontwikkelen en een veiligere toepassing in de cloud te implementeren.

## <a name="security-development-lifecycle"></a>Security Development Lifecycle

Het volgen van best practices voor veilige softwareontwikkeling vereist het integreren van beveiliging in elke fase van de levenscyclus van softwareontwikkeling, van vereisteanalyse tot onderhoud, ongeacht de projectmethodologie[(waterval,](https://en.wikipedia.org/wiki/Waterfall_model) [agile](https://en.wikipedia.org/wiki/Agile_software_development)of [DevOps).](https://en.wikipedia.org/wiki/DevOps) In het kielzog van high-profile datalekken en de exploitatie van operationele beveiligingsfouten, meer ontwikkelaars zijn begrijpen dat de beveiliging moet worden aangepakt tijdens het ontwikkelingsproces.

Hoe later u een probleem oplost in uw ontwikkelingslevenscyclus, hoe meer die oplossing u kost. Beveiligingsproblemen zijn geen uitzondering. Als u beveiligingsproblemen negeert in de vroege fasen van uw softwareontwikkeling, kan elke volgende fase de kwetsbaarheden van de vorige fase erven. Uw eindproduct heeft meerdere beveiligingsproblemen en de mogelijkheid van een inbreuk op zich verzameld. Door beveiliging in elke fase van de ontwikkelingslevenscyclus in te bouwen, u problemen vroegtijdig opvangen en u uw ontwikkelingskosten verlagen.

We volgen de fasen van de Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) om activiteiten en Azure-services te introduceren die u gebruiken om te voldoen aan veilige softwareontwikkelingspraktijken in elke fase van de levenscyclus.

De SDL-fasen zijn:

  - Training
  - Vereisten
  - Ontwerp
  - Implementatie
  - Verificatie
  - Release
  - Antwoord

![Security Development Lifecycle](./media/secure-dev-overview/01-sdl-phase.png)

In deze artikelen groeperen we de SDL-fasen in het ontwerpen, ontwikkelen en implementeren.

## <a name="engage-your-organizations-security-team"></a>Schakel het beveiligingsteam van uw organisatie in

Uw organisatie heeft mogelijk een formeel toepassingsbeveiligingsprogramma dat u helpt bij beveiligingsactiviteiten van begin tot eind tijdens de ontwikkelingslevenscyclus. Als uw organisatie beveiligings- en nalevingsteams heeft, moet u deze inschakelen voordat u begint met het ontwikkelen van uw toepassing. Vraag ze in elke fase van de SDL of er taken zijn die je hebt gemist.

We begrijpen dat veel lezers mogelijk geen beveiligings- of nalevingsteam hebben om deel te nemen. Deze artikelen kunnen u helpen bij de beveiligingsvragen en -beslissingen die u in elke fase van de SDL moet overwegen.

## <a name="resources"></a>Resources

Gebruik de volgende bronnen voor meer informatie over het ontwikkelen van beveiligde toepassingen en om uw toepassingen op Azure te beveiligen:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – De SDL is een softwareontwikkelingsproces van Microsoft dat ontwikkelaars helpt veiligere software te bouwen. Het helpt u om aan de vereisten voor naleving van de beveiliging te voldoen en tegelijkertijd de ontwikkelingskosten te verlagen.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP is een online community die vrij beschikbare artikelen, methodologieën, documentatie, tools en technologieën produceert op het gebied van webapplicatiebeveiliging.

[Pushing Left, Like a Boss](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – A series of online articles that outlines different types of application security activities that developers should complete to create more secure code.

[Microsoft-identiteitsplatform](../../active-directory/develop/index.yml) – Het Microsoft-identiteitsplatform is een evolutie van de Azure AD-identiteitsservice en het ontwikkelaarsplatform. Het is een volledig uitgerust platform dat bestaat uit een verificatieservice, open-sourcebibliotheken, applicatieregistratie en -configuratie, volledige documentatie van ontwikkelaars, codevoorbeelden en andere ontwikkelaarsinhoud. Het Microsoft-identiteitsplatform ondersteunt industriestandaardprotocollen zoals OAuth 2.0 en OpenID Connect.

[Aanbevolen procedures voor beveiliging voor Azure-oplossingen](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – een verzameling aanbevolen procedures voor beveiliging die u gebruiken wanneer u cloudoplossingen ontwerpt, implementeert en beheert met Azure. Dit document is bedoeld als een bron voor IT-professionals. Dit kunnen ontwerpers, architecten, ontwikkelaars en testers zijn die beveiligde Azure-oplossingen bouwen en implementeren.

[Blueprints voor beveiliging en naleving op Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – Azure Security and Compliance Blueprints zijn bronnen waarmee u cloudgestuurde toepassingen bouwen en starten die voldoen aan strenge regelgeving en normen.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen raden we beveiligingsbesturingselementen en activiteiten aan die u kunnen helpen bij het ontwerpen, ontwikkelen en implementeren van beveiligde toepassingen.

- [Veilige toepassingen ontwerpen](secure-design.md)
- [Veilige toepassingen ontwikkelen](secure-develop.md)
- [Veilige toepassingen implementeren](secure-deploy.md)

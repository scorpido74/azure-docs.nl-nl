---
title: Bedreigings informatie gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van bedreigings informatie gegevens met Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: cbdf05c714971db5a618ca2a8bb35fe286d6804c
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240676"
---
# <a name="connect-data-from-threat-intelligence-providers---preview"></a>Verbinding maken met gegevens van de providers van bedreigings informatie-preview

> [!IMPORTANT]
> Bedreigings informatie in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met Azure Sentinel kunt u de risico indicatoren importeren die uw organisatie gebruikt, waardoor de mogelijkheid van uw beveiligings analisten kan worden verbeterd om bekende bedreigingen te detecteren en te priori teren. Verschillende functies van Azure Sentinel worden beschikbaar of zijn verbeterd:

- **Analytics** bevat een set geplande regel sjablonen die u kunt inschakelen om waarschuwingen en incidenten te genereren die zijn gebaseerd op overeenkomsten van logboek gebeurtenissen van uw bedreigings indicatoren.

- **Werkmappen** bieden een overzicht van de bedreigings indicatoren die worden geïmporteerd in azure Sentinel en eventuele waarschuwingen die worden gegenereerd door analyse regels die overeenkomen met uw bedreigings indicatoren.

- Met **jagers** query's kunnen beveiligings onderzoekers risico indicatoren gebruiken binnen de context van veelvoorkomende jacht-scenario's.

- **Notebooks** kunnen bedreigings indicatoren gebruiken bij het onderzoeken van afwijkingen en het zoeken naar schadelijke gedragingen.

U kunt bedreigings indicatoren streamen naar Azure Sentinel door gebruik te maken van een van de TIP-producten (Integrated Threat Intelligence platform) die worden vermeld in de volgende sectie of door directe integratie met de [Microsoft Graph Security TIINDICATORS API](https://aka.ms/graphsecuritytiindicators)te gebruiken.

## <a name="integrated-threat-intelligence-platform-products"></a>Geïntegreerde platform producten voor bedreigings informatie

- [MISP open source Threat Intelligence-platform](https://www.misp-project.org/)
    
    Voor een voorbeeld script dat clients bevat met MISP-instanties voor het migreren van bedreigings indicatoren naar de Microsoft Graph-beveiligings-API, raadpleegt [u het MISP to Microsoft Graph Security script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto-netwerken MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Zie [IOCs verzenden naar de Microsoft Graph-beveiligings-API met behulp van MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)voor begeleide instructies.

- [ThreatConnect-platform](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>Vereisten  

- De Azure AD-rol van globale beheerder of beveiligings beheerder om machtigingen te verlenen aan uw TIP-product of een aangepaste toepassing die gebruikmaakt van directe integratie met de Microsoft Graph Security tiIndicators API.

- Lees-en schrijf machtigingen voor de Azure Sentinel-werk ruimte om uw bedreigings indicatoren op te slaan.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Azure-Sentinel verbinden met uw Threat Intelligence-provider

1. [Registreer een toepassing](/graph/auth-v2-service#1-register-your-app) in azure Active Directory om een toepassings-id, toepassings geheim en Azure Active Directory Tenant-id op te halen. U hebt deze waarden nodig voor wanneer u uw geïntegreerde TIP-product of-app configureert die gebruikmaakt van directe integratie met Microsoft Graph Security tiIndicators-API.

2. [API-machtigingen](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) voor de geregistreerde toepassing configureren: Voeg de Microsoft Graph-toepassings machtiging **ThreatIndicators. readwrite. OwnedBy** aan uw geregistreerde toepassing toe.

3. Vraag uw Azure Active Directory Tenant beheerder om toestemming van de beheerder te verlenen aan de geregistreerde toepassing voor uw organisatie. Van de Azure Portal: **Azure Active Directory** > **app-registraties** ***app*-naam>weergaveAPI-machtigingen verlenen beheerders toestemming voor\<**  >  >  >  ***Tenant naam*.> \<**

4. Configureer uw TIP-product of-app die gebruikmaakt van directe integratie met Microsoft Graph Security tiIndicators API om indica toren naar Azure Sentinel te verzenden door het volgende op te geven:
    
    a. De waarden voor de geregistreerde toepassings-ID, het geheim en de Tenant-ID.
    
    b. Geef voor het doel product Azure Sentinel op.
    
    c. Geef voor de actie waarschuwing op.

5. Navigeer in het Azure Portal naar **Azure Sentinel** > **Data connectors** en selecteer vervolgens de connector **Threat Intelligence platforms (preview)** .

6. Selecteer de **pagina connector openen**en vervolgens **verbinding maken**.

7. Als u de bedreigings indicatoren wilt weer geven die in azure Sentinel zijn geïmporteerd, gaat u naar **Azure Sentinel-logs-logboeken** > **SecurityInsights**en vouwt u vervolgens **ThreatIntelligenceIndicator**uit.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u uw Threat Intelligence-provider verbindt met Azure Sentinel. Raadpleeg de volgende artikelen voor meer informatie over de Azure-Sentinel.

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag [met het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats.md).
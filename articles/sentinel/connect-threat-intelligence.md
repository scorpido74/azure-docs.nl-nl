---
title: Gegevens over bedreigingsinformatie koppelen aan Azure Sentinel| Microsoft Documenten
description: Meer informatie over het verbinden van gegevens over bedreigingsinformatie met Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 5c79642d287224cd15531701d7cc87ebfd72eb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588039"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Gegevens van aanbieders van bedreigingsinformatie verbinden

> [!IMPORTANT]
> De Threat Intelligence-gegevensconnectors in Azure Sentinel zijn momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Met Azure Sentinel u de bedreigingsindicatoren importeren die uw organisatie gebruikt, waardoor uw beveiligingsanalisten beter in staat zijn bekende bedreigingen te detecteren en te prioriteren. Verschillende functies van Azure Sentinel worden vervolgens beschikbaar of worden verbeterd:

- **Analytics** bevat een set geplande regelsjablonen die u inschakelen om waarschuwingen en incidenten te genereren op basis van overeenkomsten met logboekgebeurtenissen op basis van uw bedreigingsindicatoren.

- **Werkmappen** bevatten samengevatte informatie over de bedreigingsindicatoren die zijn geïmporteerd in Azure Sentinel en eventuele waarschuwingen die worden gegenereerd uit analyseregels die overeenkomen met uw bedreigingsindicatoren.

- **Jachtquery's** stellen beveiligingsonderzoekers in staat om dreigingsindicatoren te gebruiken in de context van veelvoorkomende jachtscenario's.

- **Notitieblokken** kunnen bedreigingsindicatoren gebruiken wanneer u afwijkingen onderzoekt en op kwaadwillig gedrag jaagt.

U bedreigingsindicatoren streamen naar Azure Sentinel met behulp van een van de geïntegreerde TIP-producten (Threat Intelligence Platform) die in de volgende sectie worden vermeld, verbinding maken met TAXII-servers of door directe integratie met de [Microsoft Graph Security tiIndicators API](https://aka.ms/graphsecuritytiindicators)te gebruiken.

## <a name="integrated-threat-intelligence-platform-products"></a>Geïntegreerde producten van het platform voor bedreigingsinformatie

- [MISP Open Source Threat Intelligence Platform](https://www.misp-project.org/)
    
    Zie de [MISP naar Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP)voor een voorbeeldscript dat clients misp-exemplaren biedt om bedreigingsindicatoren naar de Microsoft Graph Security API te migreren.

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Zie [IoC's naar de Microsoft Graph Security API verzenden met MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540)voor instructies met instructies voor begeleide instructies.

- [ThreatConnect-platform](https://threatconnect.com/solution/)

    Zie [ThreatConnect-integraties](https://threatconnect.com/integrations/) voor informatie en zoek naar Microsoft Graph Security API op de pagina.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Azure Sentinel verbinden met uw platform voor bedreigingsinformatie

## <a name="prerequisites"></a>Vereisten  

- Azure AD-rol van globale beheerder of beveiligingsbeheerder om machtigingen te verlenen aan uw TIP-product of aangepaste toepassing die directe integratie met de Microsoft Graph Security tiIndicators API gebruikt.

- Lees- en schrijfmachtigingen voor de Azure Sentinel-werkruimte om uw bedreigingsindicatoren op te slaan.

## <a name="instructions"></a>Instructies

1. [Registreer een toepassing](/graph/auth-v2-service#1-register-your-app) in Azure Active Directory om een toepassings-id, toepassingsgeheim en Azure Active Directory-tenant-id op te halen. U hebt deze waarden nodig voor wanneer u uw geïntegreerde TIP-product of -app configureert die directe integratie met microsoft graph security tiIndicators API gebruikt.

2. [API-machtigingen configureren](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) voor de geregistreerde toepassing: voeg de Microsoft Graph Application permission **ThreatIndicators.ReadWrite.OwnedBy** toe aan uw geregistreerde toepassing.

3. Vraag uw Azure Active Directory-tenantbeheerder om beheerderstoestemming te verlenen voor de geregistreerde toepassing voor uw organisatie. Vanuit de Azure-portal: **Azure Active Directory** > **App-registraties** > **\<_app-naam_>** > **View API Permissions** > Grant admin consent for** \<tenant _name_>**.

4. Configureer uw TIP-product of -app die directe integratie met Microsoft Graph Security tiIndicators API gebruikt om indicatoren naar Azure Sentinel te verzenden door het volgende op te geven:
    
    a. De waarden voor de id, het geheim en de tenant-id van de geregistreerde toepassing.
    
    b. Geef Azure Sentinel op voor het doelproduct.
    
    c. Geef voor de actie een waarschuwing op.

5. Navigeer in de **Azure-portal** > naar Azure Sentinel**Data-connectors** en selecteer vervolgens de **Threat Intelligence Platforms (Preview)-connector.**

6. Selecteer **De pagina Connector openen**openen en maak vervolgens **Verbinding**.

7. Als u de bedreigingsindicatoren wilt bekijken die zijn geïmporteerd in Azure Sentinel, navigeert u naar **Azure Sentinel - Logs** > **SecurityInsights**en vouwt u **ThreatIntelligenceIndicator**uit.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Azure Sentinel verbinden met TAXII-servers

## <a name="prerequisites"></a>Vereisten  

- Lees- en schrijfmachtigingen voor de Azure Sentinel-werkruimte om uw bedreigingsindicatoren op te slaan.

- TAXII 2.0 server URI en Collection ID.

## <a name="instructions"></a>Instructies

1. Navigeer in de **Azure-portal** > naar Azure Sentinel**Data-connectors** en selecteer vervolgens de **Threat Intelligence - TAXII (Preview)-connector.**

2. Selecteer **Connectorpagina openen**.

3. Geef de vereiste en optionele informatie op in de tekstvakken.

4. Selecteer **Toevoegen** om de verbinding met de TAXII 2.0-server in te schakelen.

5. Als u extra TAXII 2.0-servers hebt: Herhaal stap 3 en 4.

6. Als u de bedreigingsindicatoren wilt bekijken die zijn geïmporteerd in Azure Sentinel, navigeert u naar **Azure Sentinel - Logs** > **SecurityInsights**en vouwt u **ThreatIntelligenceIndicator**uit.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u uw provider voor bedreigingsinformatie verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel.

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en potentiële bedreigingen.](quickstart-get-visibility.md)
- Aan de slag met [het detecteren van bedreigingen met Azure Sentinel.](tutorial-detect-threats.md)

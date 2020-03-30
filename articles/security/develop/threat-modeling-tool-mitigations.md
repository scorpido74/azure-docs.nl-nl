---
title: Mitigaties - Microsoft Threat Modeling Tool - Azure | Microsoft Documenten
description: Mitigatiepagina voor de Microsoft Threat Modeling Tool waarin mogelijke oplossingen voor de meest blootgestelde gegenereerde bedreigingen worden gemarkeerd.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728034"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Beperking van Microsoft Threat Modeling Tool

De Threat Modeling Tool is een kernelement van de Microsoft Security Development Lifecycle (SDL). Het stelt softwarearchitecten in staat om potentiële beveiligingsproblemen vroegtijdig te identificeren en te beperken, wanneer ze relatief eenvoudig en kosteneffectief zijn om op te lossen. Als gevolg daarvan vermindert het de totale ontwikkelingskosten sterk. Ook hebben we de tool ontworpen met niet-beveiligingsexperts in het achterhoofd, waardoor bedreigingsmodellering voor alle ontwikkelaars gemakkelijker wordt door duidelijke richtlijnen te bieden voor het maken en analyseren van bedreigingsmodellen.

Bezoek de **[Threat Modeling Tool](threat-modeling-tool.md)** om vandaag nog aan de slag te gaan!

## <a name="mitigation-categories"></a>Mitigatiecategorieën

De beperking van de Threat Modeling Tool wordt gecategoriseerd volgens het webtoepassingsbeveiligingsframe, dat bestaat uit:

| Categorie | Beschrijving |
| -------- | ----------- |
| **[Controle en logboekregistratie](threat-modeling-tool-auditing-and-logging.md)** | Wie deed wat en wanneer? Controle en logboekregistratie verwijzen naar hoe uw toepassing beveiligingsgerelateerde gebeurtenissen registreert |
| **[Verificatie](threat-modeling-tool-authentication.md)** | Wie ben jij? Verificatie is het proces waarbij een entiteit de identiteit van een andere entiteit bewijst, meestal via referenties, zoals een gebruikersnaam en wachtwoord |
| **[Vergunning](threat-modeling-tool-authorization.md)** | Wat kun je doen? Autorisatie is hoe uw toepassing toegangscontroles biedt voor resources en bewerkingen |
| **[Communicatiebeveiliging](threat-modeling-tool-communication-security.md)** | Met wie praat je? Communicatiebeveiliging zorgt ervoor dat alle communicatie zo veilig mogelijk is |
| **[Configuration Management](threat-modeling-tool-configuration-management.md)** | Wie loopt uw aanvraag als? Met welke databases maakt het verbinding? Hoe wordt uw aanvraag beheerd? Hoe worden deze instellingen beveiligd? Configuratiebeheer verwijst naar hoe uw toepassing met deze operationele problemen omgaat |
| **[Cryptografie](threat-modeling-tool-cryptography.md)** | Hoe houd je geheimen (vertrouwelijkheid)? Hoe bent u knoeien-proofing uw gegevens of bibliotheken (integriteit)? Hoe bent u het verstrekken van zaden voor willekeurige waarden die cryptografisch sterk moeten zijn? Cryptografie verwijst naar hoe uw toepassing vertrouwelijkheid en integriteit afdwingt |
| **[Uitzonderingsbeheer](threat-modeling-tool-exception-management.md)** | Wat doet uw toepassing wanneer een methodeaanroep in uw toepassing mislukt? Hoeveel geef je aan? Geeft u vriendelijke foutinformatie terug aan eindgebruikers? Geeft u waardevolle uitzonderingsinformatie terug aan de beller? Is uw toepassing niet sierlijk? |
| **[Invoervalidatie](threat-modeling-tool-input-validation.md)** | Hoe weet u dat de invoer die uw toepassing ontvangt geldig en veilig is? Invoervalidatie verwijst naar de manier waarop uw toepassing invoer filtert, schroeert of afwijst voordat deze wordt verwerkt. Overweeg de invoer te beperken via ingangspunten en de uitvoer via uitgangspunten te coderen. Vertrouwt u gegevens uit bronnen zoals databases en bestandsshares? |
| **[Gevoelige gegevens](threat-modeling-tool-sensitive-data.md)** | Hoe gaat uw toepassing om met gevoelige gegevens? Gevoelige gegevens verwijzen naar hoe uw toepassing omgaat met gegevens die moeten worden beschermd in het geheugen, via het netwerk of in permanente opslag |
| **[Sessiebeheer](threat-modeling-tool-session-management.md)** | Hoe verwerkt en beschermt uw toepassing gebruikerssessies? Een sessie verwijst naar een reeks gerelateerde interacties tussen een gebruiker en uw webtoepassing |

Dit helpt u bij het identificeren van:

* Waar worden de meest gemaakte fouten gemaakt
* Waar zijn de meest bruikbare verbeteringen

Als gevolg hiervan gebruikt u deze categorieën om uw beveiligingswerk te concentreren en prioriteit te geven, zodat als u weet dat de meest voorkomende beveiligingsproblemen optreden in de categorieën invoervalidatie, verificatie en autorisatie, u daar beginnen. Voor meer informatie bezoek ** [deze patent link](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Volgende stappen

Ga naar **[Threat Modeling Tool Bedreigingen](threat-modeling-tool-threats.md)** voor meer informatie over de bedreigingscategorieën die de tool gebruikt om mogelijke ontwerpbedreigingen te genereren.
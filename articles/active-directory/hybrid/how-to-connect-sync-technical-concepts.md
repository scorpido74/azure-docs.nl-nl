---
title: 'Azure AD Connect-synchronisatie: technische concepten | Microsoft Documenten'
description: Hiermee worden de technische concepten van Azure AD Connect-synchronisatie uitgelegd.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ec4a6100cfbb4419d7e30f4b97589113b88939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347568"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Azure AD Connect-synchronisatie: technische concepten
Dit artikel is een samenvatting van het onderwerp [Understanding architectuur](how-to-connect-sync-technical-concepts.md).

Azure AD Connect-synchronisatie bouwt voort op een solide metadirectorysynchronisatieplatform.
In de volgende secties worden de concepten voor metadirectorysynchronisatie geïntroduceerd.
Voortbouwend op MIIS, ILM en FIM biedt de Azure Active Directory Sync Services het volgende platform voor het verbinden met gegevensbronnen, het synchroniseren van gegevens tussen gegevensbronnen en het inrichten en deprovisioneren van identiteiten.

![Technische concepten](./media/how-to-connect-sync-technical-concepts/scenario.png)

In de volgende secties vindt u meer informatie over de volgende aspecten van de FIM-synchronisatieservice:

* Connector
* Kenmerkstroom
* Verbindingsruimte
* Metaverse
* Inrichten

## <a name="connector"></a>Connector
De codemodules die worden gebruikt om te communiceren met een verbonden map worden connectors genoemd (voorheen bekend als management agents (MAs)).

Deze zijn geïnstalleerd op de computer waarop Azure AD Connect wordt gesynchroniseerd. De connectors bieden de agentless mogelijkheid om te converseren met behulp van externe systeemprotocollen in plaats van te vertrouwen op de inzet van gespecialiseerde agents. Dit betekent minder risico' s en implementatietijden, vooral bij het omgaan met kritieke toepassingen en systemen.

In de afbeelding hierboven is de connector synoniem met de connectorruimte, maar omvat alle communicatie met het externe systeem.

De connector is verantwoordelijk voor alle import- en exportfunctionaliteit naar het systeem en bevrijdt ontwikkelaars van het moeten begrijpen hoe ze verbinding kunnen maken met elk systeem native bij het gebruik van declaratieve inrichting om gegevenstransformaties aan te passen.

Import en export vinden alleen plaats wanneer gepland, waardoor verdere isolatie van veranderingen binnen het systeem mogelijk wordt, omdat wijzigingen niet automatisch worden doorgevoerd naar de verbonden gegevensbron. Daarnaast kunnen ontwikkelaars ook hun eigen connectors maken om verbinding te maken met vrijwel elke gegevensbron.

## <a name="attribute-flow"></a>Kenmerkstroom
De metaverse is de geconsolideerde weergave van alle samengevoegde identiteiten van naburige verbindingsruimten. In de bovenstaande afbeelding wordt de kenmerkstroom weergegeven door lijnen met pijlpunten voor zowel inkomende als uitgaande stroom. Kenmerkstroom is het proces van het kopiëren of transformeren van gegevens van het ene systeem naar het andere en alle kenmerkstromen (binnenkomend of uitgaand).

De kenmerkstroom vindt plaats tussen de verbindingsruimte en de metaverse bidirectioneel wanneer synchronisatiebewerkingen (volledige of delta)bewerkingen zijn gepland om uit te voeren.

Kenmerkstroom treedt alleen op wanneer deze synchronisaties worden uitgevoerd. Kenmerkstromen worden gedefinieerd in synchronisatieregels. Deze kunnen binnenkomend zijn (ISR in de afbeelding hierboven) of uitgaand (OSR in de afbeelding hierboven).

## <a name="connected-system"></a>Verbonden systeem
Verbonden systeem (aka verbonden directory) verwijst naar het externe systeem azure AD Connect sync heeft aangesloten op en het lezen en schrijven van identiteitsgegevens van en naar.

## <a name="connector-space"></a>Verbindingsruimte
Elke verbonden gegevensbron wordt weergegeven als een gefilterde subset van de objecten en kenmerken in de verbindingsruimte.
Hierdoor kan de synchronisatieservice lokaal werken zonder dat u contact hoeft op te nemen met het externe systeem wanneer u de objecten synchroniseert en de interactie beperkt tot alleen import en export.

Wanneer de gegevensbron en de connector de mogelijkheid hebben om een lijst met wijzigingen (een delta-import) te bieden, neemt de operationele efficiëntie drastisch toe omdat alleen wijzigingen worden aangebracht sinds de laatste stemcyclus wordt uitgewisseld. De verbindingsruimte isoleert de verbonden gegevensbron van wijzigingen die automatisch worden verspreid door te eisen dat de connectorschema wordt geïmporteerd en geëxporteerd. Deze toegevoegde verzekering geeft u gemoedsrust tijdens het testen, bekijken of bevestigen van de volgende update.

## <a name="metaverse"></a>Metaverse
De metaverse is de geconsolideerde weergave van alle samengevoegde identiteiten van naburige verbindingsruimten.

Aangezien identiteiten met elkaar zijn verbonden en de autoriteit voor verschillende kenmerken wordt toegewezen via importstroomtoewijzingen, begint het centrale metaverseobject informatie uit meerdere systemen te verzamelen. Vanuit deze objectkenmerkstroom worden in toewijzingen informatie naar uitgaande systemen uitgevoerd.

Objecten worden gemaakt wanneer een gezaghebbend systeem ze projecteert in het metaverse. Zodra alle verbindingen zijn verwijderd, wordt het metaverse object verwijderd.

Objecten in de metaverse kunnen niet rechtstreeks worden bewerkt. Alle gegevens in het object moeten worden bijgedragen via de attribuutstroom. De metaverse behoudt permanente connectoren met elke connectorruimte. Deze connectors vereisen geen herwaardering voor elke synchronisatierun. Dit betekent dat Azure AD Connect-synchronisatie niet telkens het overeenkomende externe object hoeft te vinden. Dit voorkomt de noodzaak van dure agenten om wijzigingen in attributen die normaal verantwoordelijk zou zijn voor het correleren van de objecten te voorkomen.

Bij het ontdekken van nieuwe gegevensbronnen die mogelijk bestaande objecten hebben die moeten worden beheerd, gebruikt Azure AD Connect-synchronisatie een proces dat een join-regel wordt genoemd, om potentiële kandidaten te evalueren waarmee een koppeling kan worden gemaakt.
Zodra de koppeling is ingesteld, treedt deze evaluatie niet opnieuw op en kan de normale attribuutstroom plaatsvinden tussen de externe verbonden gegevensbron en de metaverse.

## <a name="provisioning"></a>Inrichten
Wanneer een gezaghebbende bron een nieuw object in de metaverse projecteert, kan een nieuw connectorruimteobject worden gemaakt in een andere connector die een downstream-verbonden gegevensbron vertegenwoordigt.

Dit vormt inherent een koppeling en de attribuutstroom kan bidirectioneel verlopen.

Wanneer een regel bepaalt dat een nieuw connectorruimteobject moet worden gemaakt, wordt dit inprovisioning genoemd. Omdat deze bewerking echter alleen binnen de verbindingsruimte plaatsvindt, wordt deze niet overgedragen naar de verbonden gegevensbron totdat een export is uitgevoerd.

## <a name="additional-resources"></a>Aanvullende resources
* [Synchronisatie van Azure AD Connect: synchronisatieopties aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png

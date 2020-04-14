---
title: Een regelsengine gebruiken om HTTPS af te dwingen in standaard Azure CDN | Microsoft Documenten
description: Gebruik de rules engine voor Microsoft Standard Azure Content Delivery Network (Azure CDN) om aan te passen hoe Azure CDN omgaat met HTTP-aanvragen, waaronder het blokkeren van de levering van bepaalde soorten inhoud, het definiëren van een caching-beleid en het wijzigen van HTTP-headers. In dit artikel leert u hoe u een regel maakt om gebruikers door te verwijzen naar HTTPS.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 91a442573139bf4fdd09978290bf2380c8bcb97e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259919"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>De standaardregelsengine instellen voor Azure CDN

In dit artikel wordt beschreven hoe u de standaardregelsengine voor Azure Content Delivery Network (Azure CDN) instelt en gebruikt.

## <a name="standard-rules-engine"></a>Standaard regels motor

U de standaardregelsengine voor Azure CDN gebruiken om aan te passen hoe HTTP-aanvragen worden behandeld. U bijvoorbeeld de engine voor regels gebruiken om de levering van inhoud af te dwingen om specifieke protocollen te gebruiken, om een cachingbeleid te definiëren of om een HTTP-header te wijzigen. In dit artikel wordt uitgelegd hoe u een regel maakt die gebruikers automatisch doorverwijst naar HTTPS. 

> [!NOTE]
> De regelsengine die in dit artikel wordt beschreven, is alleen beschikbaar voor Standaard Azure CDN van Microsoft. 

## <a name="redirect-users-to-https"></a>Gebruikers doorverwijzen naar HTTPS

1. Ga in uw Microsoft-profielen naar Azure Content Delivery Network.

1. Selecteer op de **profielpagina van CDN** het eindpunt waarvoor u regels wilt maken.
  
1. Selecteer het tabblad **Rules Engine.**
   
    Het deelvenster **Rules Engine** wordt geopend en toont de lijst met beschikbare algemene regels. 
   
    [![Pagina nieuwe regels azure CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > De volgorde waarin meerdere regels worden weergegeven, is van invloed op de manier waarop regels worden behandeld. De acties die in een regel zijn opgegeven, kunnen worden overschreven door een volgende regel.
   >

1. Selecteer **Regel toevoegen** en voer een regelnaam in. Regelnamen moeten beginnen met een letter en mogen alleen cijfers en letters bevatten.

1. Als u wilt bepalen op welk type aanvragen de regel van toepassing is, maakt u een overeenkomstvoorwaarde:
    1. Selecteer **Voorwaarde toevoegen**en selecteer vervolgens de voorwaarde **Protocolovereenkomst aanvragen.**
    1. Selecteer voor **operator** de optie **Is gelijk aan**.
    1. Selecteer **HTTP**voor **waarde.**
   
   [![Azure CDN-regel overeenkomen voorwaarde](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > U kiezen uit meerdere wedstrijdvoorwaarden in de vervolgkeuzelijst **Voorwaarde toevoegen.** Zie [Wedstrijdvoorwaarden in de engine standaardregels voor](cdn-standard-rules-engine-match-conditions.md)een gedetailleerde lijst met wedstrijdvoorwaarden.
   
1. Selecteer de actie die u wilt toepassen op de aanvragen die voldoen aan de wedstrijdvoorwaarde:
   1. Selecteer **Actie toevoegen**en selecteer vervolgens **URL-omleiding**.
   1. Selecteer Gevonden **(302)** voor **Tekst**.
   1. Bij **Protocol** selecteert u **HTTPS**.
   1. Laat alle andere velden leeg om binnenkomende waarden te gebruiken.
   
   [![Azure CDN-regelactie](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > U kiezen uit meerdere acties in de vervolgkeuzelijst **Actie toevoegen.** Zie Acties in de [engine standaardregels voor](cdn-standard-rules-engine-actions.md)een gedetailleerde lijst met acties.

6. Selecteer **Opslaan** om de nieuwe regel op te slaan. De regel is nu beschikbaar om te gebruiken.
   
   > [!IMPORTANT]
   > Regelwijzigingen kunnen tot 15 minuten duren voordat u zich voortplant via Azure CDN.
   >
   

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Naslaginformatie over standaardregelengine](cdn-standard-rules-engine-reference.md)
- [Overeenkomen met de voorwaarden in de standaardregelsmotor](cdn-standard-rules-engine-match-conditions.md)
- [Acties in de standaardregels-engine](cdn-standard-rules-engine-actions.md)

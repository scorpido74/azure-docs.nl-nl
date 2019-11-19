---
title: Gebruik een regel Engine om HTTPS af te dwingen in de standaard Azure CDN | Microsoft Docs
description: Gebruik de regel engine voor micro soft Standard Azure Content Delivery Network (Azure CDN) om aan te passen hoe Azure CDN HTTP-aanvragen verwerkt, zoals het blok keren van de levering van bepaalde soorten inhoud, het definiëren van een cache beleid en het aanpassen van HTTP-headers. In dit artikel leert u hoe u een regel kunt maken om gebruikers om te leiden naar HTTPS.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 724861305d7a25db409072200ac2bc3bd83f0682
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171576"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>De Standard Rules engine instellen voor Azure CDN

In dit artikel wordt beschreven hoe u de Standard Rules engine voor Azure Content Delivery Network (Azure CDN) instelt en gebruikt.

## <a name="standard-rules-engine"></a>Engine Standard-regels

U kunt de standaard regels-engine voor Azure CDN gebruiken om aan te passen hoe HTTP-aanvragen worden verwerkt. U kunt bijvoorbeeld de engine regels gebruiken om de levering van inhoud af te dwingen voor het gebruik van specifieke protocollen, het definiëren van een cache beleid of het wijzigen van een HTTP-header. In dit artikel wordt beschreven hoe u een regel maakt waarmee gebruikers automatisch worden omgeleid naar HTTPS. 

> [!NOTE]
> De regel engine die in dit artikel wordt beschreven, is alleen beschikbaar voor standaard Azure CDN van micro soft. 

## <a name="redirect-users-to-https"></a>Gebruikers omleiden naar HTTPS

1. Ga in uw micro soft-profielen naar Azure Content Delivery Network.

1. Selecteer op de pagina **CDN-profiel** het eind punt waarvoor u regels wilt maken.
  
1. Selecteer het tabblad **regel engine** .
   
    Het deel venster **regel engine** wordt geopend en toont de lijst met beschik bare algemene regels. 
   
    [pagina Azure CDN nieuwe regels ![](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > De volg orde waarin meerdere regels worden weer gegeven, is van invloed op hoe regels worden verwerkt. De acties die zijn opgegeven in een regel, kunnen worden overschreven door een volgende regel.
   >

1. Selecteer **regel toevoegen** en voer een regel naam in. Regel namen moeten beginnen met een letter en mogen alleen cijfers en letters bevatten.

1. Om het type aanvragen te identificeren waarop de regel van toepassing is, maakt u een matching-voor waarde:
    1. Selecteer **voor waarde toevoegen**en selecteer vervolgens de voor waarde **aanvraag protocol** match.
    1. Selecteer voor **operator** **is gelijk aan**.
    1. Selecteer voor **waarde** **http**.
   
   [voor waarde ![Azure CDN regel](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > U kunt kiezen uit meerdere match-voor waarden in de vervolg keuzelijst **voor waarde toevoegen** . Zie voor een gedetailleerde lijst met match-voor waarden [overeenkomst in de standaard regels-engine](cdn-standard-rules-engine-match-conditions.md).
   
1. Selecteer de actie die moet worden toegepast op de aanvragen die voldoen aan de voor waarde match:
   1. Selecteer **actie toevoegen**en selecteer vervolgens **URL-omleiding**.
   1. Selecteer bij **type**de optie **gevonden (302)** .
   1. Bij **Protocol** selecteert u **HTTPS**.
   1. Laat alle andere velden leeg om binnenkomende waarden te gebruiken.
   
   [Azure CDN regel actie ![](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > U kunt kiezen uit meerdere acties in de vervolg keuzelijst **actie toevoegen** . Zie [acties in de Standard Rules engine](cdn-standard-rules-engine-actions.md)voor een gedetailleerde lijst met acties.

6. Selecteer **Opslaan** om de nieuwe regel op te slaan. De regel is nu beschikbaar voor gebruik.
   
   > [!IMPORTANT]
   > Het kan tot vijf tien minuten duren voordat regel wijzigingen door Azure CDN worden door gegeven.
   >
   

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Naslag informatie voor standaard regels-engine](cdn-standard-rules-engine-reference.md)
- [Voldoen aan de voor waarden in de standaard regels-engine](cdn-standard-rules-engine-match-conditions.md)
- [Acties in de standaard regels-engine](cdn-standard-rules-engine-actions.md)

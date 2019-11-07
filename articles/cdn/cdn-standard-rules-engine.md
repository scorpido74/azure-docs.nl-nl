---
title: HTTPS afdwingen met Azure CDN Standard Rules engine | Microsoft Docs
description: Met de standaard regels-engine kunt u aanpassen hoe HTTP-aanvragen worden verwerkt door Azure CDN van micro soft, zoals het blok keren van de levering van bepaalde soorten inhoud, het opgeven van een cache beleid en het aanpassen van HTTP-headers.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615921"
---
# <a name="azure-cdn-standard-rules-engine"></a>Engine voor Azure CDN standaard regels

> [!NOTE]
> De standaard regels-engine is alleen beschikbaar voor Azure CDN van micro soft. 

Met de Azure CDN Standard Rules engine kunt u aanpassen hoe HTTP-aanvragen worden verwerkt. Bijvoorbeeld het afdwingen van inhouds levering via specifieke protocollen, het definiëren van een cache beleid of het wijzigen van een HTTP-header. In deze zelf studie ziet u hoe u een regel maakt waarmee uw gebruikers automatisch naar HTTPS worden omgeleid. 


## <a name="tutorial"></a>Zelfstudie

1. Selecteer op de pagina **CDN-profiel** op Azure CDN van micro soft-profielen het eind punt waarvoor u regels wilt configureren.
  
2. Selecteer het tabblad **regel engine** aan de linkerkant.
   
    De Blade regel engine wordt weer gegeven met de globale regel. 
   
    [pagina met nieuwe regels voor ![CDN](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > De volg orde waarin meerdere regels worden weer gegeven, is van invloed op de manier waarop ze worden verwerkt. Een volgende regel kan de acties overschrijven die door een vorige regel zijn opgegeven.
   >

3. Klik op de knop **regel toevoegen** en geef een regel naam op. Regel namen moeten beginnen met een letter en mogen alleen cijfers en letters bevatten.

4. Identificeer het type aanvragen waarop de regel van toepassing is. Gebruik de vervolg keuzelijst om de overeenkomst voor het **aanvraag protocol** te selecteren en gebruik de waarde **is gelijk aan** **http**.
   
   [voor waarde voor overeenkomst met CDN-regel ![](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > Er zijn meerdere match voorwaarden beschikbaar in de vervolg keuzelijst. Voor een gedetailleerde lijst met match-voor waarden raadpleegt u de [voor waarden van regels engine](cdn-standard-rules-engine-match-conditions.md).
   
5. Kies de actie die wordt toegepast op de geïdentificeerde aanvragen. Gebruik de vervolg keuzelijst om de **URL-omleidings** actie te selecteren en gebruik de waarde **gevonden (302)** voor type en **https** voor protocol. Laat alle andere velden leeg om de binnenkomende waarden te gebruiken.
   
   [de actie voor de CDN-regel ![](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Er zijn meerdere acties beschikbaar in de vervolg keuzelijst. Zie [regels engine acties](cdn-standard-rules-engine-actions.md)voor een gedetailleerde lijst met acties.

6. Selecteer **Opslaan** om de nieuwe regel op te slaan.  De nieuwe regel wordt nu geïmplementeerd.
   
   > [!IMPORTANT]
   > Het kan tot vijf tien minuten duren voordat de wijzigingen zijn doorgevoerd in Azure CDN.
   >
   

## <a name="see-also"></a>Zie ook

- [Overzicht van Azure CDN](cdn-overview.md)
- [Naslag informatie voor standaard regels-engine](cdn-standard-rules-engine-reference.md)
- [Voldoen aan de standaard regels voor de engine](cdn-standard-rules-engine-match-conditions.md)
- [Engine acties voor standaard regels](cdn-standard-rules-engine-actions.md)

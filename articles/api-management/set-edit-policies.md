---
title: Azure API-beheerbeleid instellen of bewerken | Microsoft Documenten
description: In dit onderwerp wordt uitgelegd hoe u Azure API Management-beleidsregels instelt of bewerkt.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 2df57477ae5270405a1774b7a4f04ed185fea396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70071705"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Azure API Management-beleid instellen of bewerken

De beleidsdefinitie is een XML-document dat een reeks inkomende en uitgaande instructies beschrijft. De XML kan rechtstreeks in het definitievenster worden bewerkt. U ook een vooraf gedefinieerd beleid selecteren in de lijst die rechts van het beleidsvenster wordt weergegeven. De instructies die van toepassing zijn op het huidige bereik worden ingeschakeld en gemarkeerd. Als u op een ingeschakelde instructie klikt, wordt de juiste XML toegevoegd op de locatie van de cursor in de definitieweergave. 

Zie [Beleid in Azure API Management](api-management-howto-policies.md)voor meer informatie over beleidsregels.

## <a name="set-or-edit-a-policy"></a>Een beleid instellen of bewerken

Voer de volgende stappen uit om een beleid in te stellen of te bewerken:

1. Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .
2. Blader naar de APIM-instantie.
3. Klik op het tabblad **API's**.

    ![Beleid bewerken](./media/set-edit-policies/code-editor.png)

4. Selecteer een van de API's die u eerder hebt geïmporteerd.
5. Selecteer het tabblad **Ontwerpen**.
6. Selecteer een bewerking waarop u het beleid wilt toepassen. Als u het beleid op alle bewerkingen wilt toepassen, selecteert u **Alle bewerkingen**.
7. Selecteer **</>** het pictogram (codeeditor) in de sectie **Inkomende verwerking** of **Uitgaande verwerking.**
8. Plak de gewenste beleidscode in een van de juiste blokken.

    ```XML
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>Bereik configureren

Beleidsregels kunnen wereldwijd of binnen het bereik van een product, API of bewerking worden geconfigureerd. Als u wilt beginnen met het configureren van een beleid, moet u eerst het bereik selecteren waarop het beleid moet worden toegepast.

Beleidsscopes worden in de volgende volgorde geëvalueerd:

1. Wereldwijde scope
2. Productbereik
3. API-bereik
4. Werkingsgebied

De verklaringen binnen beleid worden geëvalueerd `base` op basis van de plaatsing van het element, als het aanwezig is. Globaal beleid heeft geen bovenliggend `<base>` beleid en het gebruik van het element daarin heeft geen effect.

Als u het beleid in het huidige bereik in de beleidseditor wilt bekijken, klikt u op **Effectief beleid voor geselecteerde scope opnieuw berekenen**.

### <a name="global-scope"></a>Wereldwijde scope

Globale scope is geconfigureerd voor **alle API's** in uw APIM-exemplaar.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) en navigeer naar uw APIM-exemplaar.
2. Klik op **Alle API's**.

    ![Wereldwijde scope](./media/api-management-howto-policies/global-scope.png)

3. Klik op het driehoekspictogram.
4. Selecteer **Code-editor**.
5. Beleid toevoegen of bewerken.
6. Klik op **Opslaan**. 

    De wijzigingen worden onmiddellijk doorgevoerd naar de API Management-gateway.

### <a name="product-scope"></a>Productbereik

De productscope is geconfigureerd voor het geselecteerde product.

1. Klik op **Producten**.

    ![Productbereik](./media/api-management-howto-policies/product-scope.png)

2. Selecteer het product waarop u beleid wilt toepassen.
3. Klik **op Beleid**.
4. Beleid toevoegen of bewerken.
5. Klik op **Opslaan**. 

### <a name="api-scope"></a>API-bereik

API-scope is geconfigureerd voor **alle bewerkingen** van de geselecteerde API.

1. Selecteer de **API** waarop u beleid wilt toepassen.

    ![API-bereik](./media/api-management-howto-policies/api-scope.png)

2. Selecteer **Alle bewerkingen**
3. Klik op het driehoekspictogram.
4. Selecteer **Code-editor**.
5. Beleid toevoegen of bewerken.
6. Klik op **Opslaan**. 

### <a name="operation-scope"></a>Werkingsgebied 

Het bereik van de bewerking is geconfigureerd voor de geselecteerde bewerking.

1. Selecteer een **API**.
2. Selecteer de bewerking waarop u beleid wilt toepassen.

    ![Werkingsgebied](./media/api-management-howto-policies/operation-scope.png)

3. Klik op het driehoekspictogram.
4. Selecteer **Code-editor**.
5. Beleid toevoegen of bewerken.
6. Klik op **Opslaan**. 

## <a name="next-steps"></a>Volgende stappen

Zie de volgende gerelateerde onderwerpen:

+ [API's transformeren](transform-api.md)
+ [Beleidsreferentie](api-management-policy-reference.md) voor een volledige lijst met beleidsoverzichten en hun instellingen
+ [Beleidsvoorbeelden](policy-samples.md)

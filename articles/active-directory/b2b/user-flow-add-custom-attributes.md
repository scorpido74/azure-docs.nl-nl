---
title: Aangepaste kenmerken toevoegen voor Azure AD-gebruikers stromen
description: Meer informatie over het aanpassen van de kenmerken voor uw Self-service registratie gebruikers stromen.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b0acd84112e9fd997cb0d60a914da9528cffd9a
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673034"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Aangepaste kenmerken definiëren voor gebruikers stromen (preview-versie)
|     |
| --- |
| De functie aangepaste gebruikers kenmerken is een open bare preview-functie van Azure Active Directory. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over Previews.|
|     |

Voor elke toepassing hebt u mogelijk verschillende vereisten voor de gegevens die u wilt verzamelen tijdens de registratie. Azure AD wordt geleverd met een ingebouwde set met informatie die is opgeslagen in kenmerken, zoals de naam, de voor waarde, de plaats en de post code. Met Azure AD kunt u de set met kenmerken die zijn opgeslagen op een gast account uitbreiden wanneer de externe gebruiker zich aanmeldt via een gebruikers stroom.

U kunt aangepaste kenmerken in de Azure Portal maken en deze gebruiken in uw eigen service-aanmeld gebruikers stromen. U kunt deze kenmerken ook lezen en schrijven met behulp van de [Microsoft Graph-API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). Microsoft Graph-API ondersteunt het maken en bijwerken van een gebruiker met extensie kenmerken. Extensie kenmerken in de Graph API worden genoemd met behulp van de Conventie `extension_<aad-extensions-app-id>_attributename` . Bijvoorbeeld:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

De `<aad-extensions-app-id>` is specifiek voor uw Tenant. Om deze id te vinden, gaat u naar Azure Active Directory > App-registraties > alle toepassingen. Zoek de app die begint met ' Aad-Extensions-app ' en selecteer deze. Op de overzichts pagina van de app ziet u de toepassings-ID (client).

## <a name="create-a-custom-attribute"></a>Een aangepast kenmerk maken

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Onder **Azure-Services**selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **aangepaste gebruikers kenmerken (preview-versie)**. De beschik bare gebruikers kenmerken worden weer gegeven.

   ![Gebruikers kenmerken selecteren voor registratie](media/user-flow-add-custom-attributes/user-attributes.png)

5. Selecteer **toevoegen**om een kenmerk toe te voegen.
6. Voer in het deel venster **een kenmerk toevoegen** de volgende waarden in:

   - **Naam** : Geef een naam op voor het aangepaste kenmerk (bijvoorbeeld ' Shoesize ').
   - **Gegevens type** : Kies een gegevens type (**teken reeks**, **Booleaanse waarde**of **int**).
   - **Beschrijving** : Voer eventueel een beschrijving in van het aangepaste kenmerk voor intern gebruik. Deze beschrijving is niet zichtbaar voor de gebruiker.

   ![Een kenmerk toevoegen](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Selecteer **Maken**.

Het aangepaste kenmerk is nu beschikbaar in de lijst met gebruikers kenmerken en voor gebruik in uw gebruikers stromen. Een aangepast kenmerk wordt alleen gemaakt wanneer het voor de eerste keer wordt gebruikt in een gebruikers stroom en niet wanneer u het toevoegt aan de lijst met gebruikers kenmerken.

Zodra u een nieuwe gebruiker hebt gemaakt met behulp van een gebruikers stroom die gebruikmaakt van het zojuist gemaakte aangepaste kenmerk, kan het object in [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)worden opgevraagd. U ziet nu **ShoeSize** in de lijst met kenmerken die zijn verzameld tijdens het registreren van het gebruikers object. U kunt de Graph API vanuit uw toepassing aanroepen om de gegevens op te halen uit dit kenmerk nadat het is toegevoegd aan het gebruikers object.

## <a name="next-steps"></a>Volgende stappen

[Een self-service-aanmeldings stroom voor een gebruiker toevoegen aan een app](self-service-sign-up-user-flow.md)

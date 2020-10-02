---
title: Kenmerk editor voor het inrichten van Clouds Azure AD Connect
description: In dit artikel wordt beschreven hoe u de kenmerk editor gebruikt.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58b7aa0bf8c82990a00b4e41041145a67ee2f02b
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91637218"
---
# <a name="azure-ad-connect-cloud-provisioning-attibute-mapping"></a>Kenmerk-toewijzing voor Cloud inrichting Azure AD Connect

Azure AD Connect Cloud inrichting heeft een nieuwe functie geïntroduceerd, waarmee u eenvoudig kenmerken kunt toewijzen tussen uw on-premises gebruikers-en groeps objecten en de objecten in azure AD.  Deze functie is toegevoegd aan de configuratie voor de inrichting van de Cloud.

U kunt de standaard kenmerk toewijzingen aanpassen op basis van de behoeften van uw bedrijf. U kunt dus bestaande kenmerk toewijzingen wijzigen of verwijderen, of nieuwe kenmerk toewijzingen maken.  Zie [kenmerken die zijn gesynchroniseerd](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)voor een lijst met kenmerken die worden gesynchroniseerd.

## <a name="understanding-attribute-mapping-types"></a>Informatie over kenmerk toewijzings typen
Met kenmerk toewijzingen bepaalt u hoe kenmerken worden ingevuld in azure AD.
Er worden vier verschillende toewijzings typen ondersteund:

- **Direct** : het doel kenmerk wordt gevuld met de waarde van een kenmerk van het gekoppelde object in AD.
- **Constante** : het doel kenmerk wordt ingevuld met een specifieke teken reeks die u hebt opgegeven.
- **Expressie** -het doel kenmerk wordt ingevuld op basis van het resultaat van een script achtige expressie.
  Zie [expressies schrijven voor kenmerk toewijzingen](reference-expressions.md)voor meer informatie.
- **Geen** -het doel kenmerk blijft ongewijzigd. Als het doel kenmerk echter ooit leeg is, wordt het ingevuld met de standaard waarde die u opgeeft.

Naast deze vier basis typen ondersteunen aangepaste kenmerk toewijzingen het concept van een optionele **standaard** waarde voor toewijzingen. De toewijzing van de standaard waarde zorgt ervoor dat een doel kenmerk wordt gevuld met een waarde als er geen waarde in azure AD of op het doel object is. De meest voorkomende configuratie is om dit leeg te laten.

## <a name="understanding-attribute-mapping-properties"></a>Informatie over kenmerk toewijzings eigenschappen

In de vorige sectie hebt u al een kenmerk-toewijzings type-eigenschap geïntroduceerd.
Naast deze eigenschap ondersteunen kenmerk toewijzingen ook de volgende kenmerken:

- **Bron kenmerk** : het gebruikers kenmerk van het bron systeem (voor beeld: Active Directory).
- **Doel kenmerk** : het gebruikers kenmerk in het doel systeem (voor beeld: Azure Active Directory).
- **Standaard waarde indien Null (optioneel)** : de waarde die wordt door gegeven aan het doel systeem als het bron kenmerk null is. Deze waarde wordt alleen ingericht wanneer een gebruiker wordt gemaakt. De ' standaard waarde wanneer null ' wordt niet ingericht bij het bijwerken van een bestaande gebruiker.  
- **Deze toewijzing Toep assen**
  - **Altijd** : deze toewijzing Toep assen op acties voor zowel het maken van de gebruiker als bij het bijwerken.
  - **Alleen tijdens het maken** : pas deze toewijzing alleen toe op acties voor het maken van een gebruiker.

> [!NOTE]
> In dit document wordt beschreven hoe u de Azure Portal gebruikt om kenmerken toe te wijzen.  Zie trans [formaties](how-to-transformation.md) voor meer informatie over het gebruik van Graph

## <a name="using-attribute-mapping"></a>Kenmerk toewijzing gebruiken
Volg de onderstaande stappen om de nieuwe functie te gebruiken.

 1.  Selecteer in Azure Portal **Azure Active Directory**.
 2.  Selecteer **Azure AD Connect**.
 3.  Selecteer **inrichting beheren**.

   ![Inrichting beheren](media/how-to-configure/manage1.png)
 
 4. Selecteer uw configuratie onder **configuratie**.
 5. Selecteer **klikken om toewijzingen te bewerken**.  Hiermee wordt het scherm kenmerk toewijzing geopend.

 ![Kenmerken toevoegen](media/how-to-attribute-mapping/mapping6.png)
 6.  Klik op **kenmerk toevoegen**.

 ![Toewijzings type](media/how-to-attribute-mapping/mapping1.png)
 
 7. Selecteer het **toewijzings type**.  In dit voor beeld gebruiken we Expression.
 8.  Voer de expressie in het vak in.  Voor dit voor beeld gebruiken we: `Replace([mail], "@contoso.com", , ,"", ,).`
 9.  Voer het doel kenmerk in.  In dit voor beeld gebruiken we ExtensionAttribute15.
 10. Selecteer wanneer u dit wilt Toep assen en klik vervolgens op **Toep assen**
   
   ![Toewijzingen bewerken](media/how-to-attribute-mapping/mapping2a.png)
 11. Terug in het scherm kenmerk toewijzing ziet u de nieuwe kenmerk toewijzing.  
 12. Klik op **schema opslaan**.

 ![Schema opslaan](media/how-to-attribute-mapping/mapping3.png)

## <a name="test-your-attribute-mapping"></a>De kenmerk toewijzing testen
Als u de kenmerk toewijzing wilt testen, kunt u [inrichten op aanvraag](how-to-on-demand-provision.md)gebruiken.  Van de 

1.  Selecteer in Azure Portal **Azure Active Directory**.
2.  Selecteer **Azure AD Connect**.
3.  Selecteer **inrichting beheren**.
4. Selecteer uw configuratie onder **configuratie**.
5. Klik onder **valideren** op de knop **een gebruiker inrichten** . 
6. In het inrichtings scherm op aanvraag.  Voer de **DN-naam** van een gebruiker of groep in en klik op de knop **inrichten** .  
7. Zodra het proces is voltooid, ziet u een geslaagd scherm en vier groene selectie vakjes die aangeven dat het is ingericht.  
  ![Geslaagd voor inrichten](media/how-to-attribute-mapping/mapping4.png)
1. Klik onder **actie uitvoeren** op **Details weer geven**.  Aan de rechter kant ziet u het nieuwe kenmerk syncrhonized en de toegepaste expressie.

  ![Actie uitvoeren](media/how-to-attribute-mapping/mapping5.png)

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
- [Expressies schrijven voor kenmerk toewijzingen](reference-expressions.md)
- [Kenmerken die worden gesynchroniseerd](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
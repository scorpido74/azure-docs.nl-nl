---
title: Nieuwe agent configuratie voor het inrichten van de Cloud Azure AD Connect
description: In dit artikel wordt beschreven hoe u Cloud inrichting installeert.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91628882"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Een nieuwe configuratie maken voor Azure AD Connect inrichting op basis van de Cloud

Nadat u de agent hebt geïnstalleerd, moet u zich aanmelden bij de Azure Portal en Azure Active Directory (Azure AD) Connect Cloud Provisioning configureren. Volg deze stappen om de agent in te scha kelen.

## <a name="configure-provisioning"></a>Inrichting configureren
Volg deze stappen voor het configureren van de inrichting.

 1. Selecteer in het Azure Portal **Azure Active Directory**
 2. Selecteer **Azure AD Connect**.
 3. Selecteer **inrichting beheren**.

 ![Inrichting beheren](media/how-to-configure/manage1.png)
 
 4. Selecteer **nieuwe configuratie**.
 5. Selecteer uw domein op het scherm configuratie en geef aan of wachtwoord-hash-synchronisatie moet worden ingeschakeld.  Klik op **maken**.  
 
 ![Nieuwe configuratie maken](media/how-to-configure/configure1.png)


 6.  Het scherm inrichtings configuratie bewerken wordt geopend.

   ![Configuratie bewerken](media/how-to-configure/configure2.png)

 7. Voer een **e-mail melding**in. Deze e-mail wordt gewaarschuwd wanneer het inrichten niet in orde is.
 8. Verplaats de selector om deze in te scha kelen en selecteer Opslaan.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Scopeing inrichten voor specifieke gebruikers en groepen
U kunt het bereik van de agent voor het synchroniseren van specifieke gebruikers en groepen met behulp van on-premises Active Directory groepen of organisatie-eenheden. U kunt geen groepen en organisatie-eenheden configureren binnen een configuratie. 

 1.  Selecteer in Azure Portal **Azure Active Directory**.
 2. Selecteer **Azure AD Connect**.
 3. Selecteer **inrichting beheren (preview)**.
 4. Selecteer uw configuratie onder **configuratie**.

 ![Configuratie sectie](media/how-to-configure/scope1.png)
 
 5. Selecteer onder **configureren**de optie **bereik filters bewerken** om het bereik van de configuratie regel te wijzigen.
 ![Bereik bewerken](media/how-to-configure/scope3.png)
 7. Aan de rechter kant kunt u het bereik wijzigen.  Klik op **gereed**  en **Sla** op wanneer u klaar bent.
 8. Nadat u het bereik hebt gewijzigd, moet u de [inrichting opnieuw opstarten](#restart-provisioning) om een onmiddellijke synchronisatie van de wijzigingen te initiëren.

## <a name="attribute-mapping"></a>Kenmerktoewijzing
Met Azure AD Connect Cloud inrichting kunt u eenvoudig kenmerken toewijzen tussen uw on-premises gebruikers-en groeps objecten en de objecten in azure AD.  U kunt de standaard kenmerk toewijzingen aanpassen op basis van de behoeften van uw bedrijf. U kunt dus bestaande kenmerk toewijzingen wijzigen of verwijderen, of nieuwe kenmerk toewijzingen maken.  Zie [kenmerk toewijzing](how-to-attribute-mapping.md)voor meer informatie.

## <a name="on-demand-provisioning"></a>Inrichting op aanvraag
Met Azure AD Connect Cloud inrichting kunt u configuratie wijzigingen testen door deze wijzigingen toe te passen op één gebruiker of groep.  U kunt dit gebruiken om te valideren en te controleren of de wijzigingen die zijn aangebracht in de configuratie correct zijn toegepast en correct worden gesynchroniseerd met Azure AD.  Zie [on-demand inrichten](how-to-on-demand-provision.md)voor meer informatie.

## <a name="restart-provisioning"></a>Inrichting opnieuw starten 
Als u niet wilt wachten op de volgende geplande uitvoering, moet u de inrichtings uitvoering activeren met behulp van de knop **inrichting opnieuw opstarten** . 
 1.  Selecteer in Azure Portal **Azure Active Directory**.
 2. Selecteer **Azure AD Connect**.
 3.  Selecteer **inrichting beheren (preview)**.
 4. Selecteer uw configuratie onder **configuratie**.

   ![Configuratie selectie voor het opnieuw opstarten van de inrichting](media/how-to-configure/scope1.png)

 5. Selecteer aan de bovenkant de optie **inrichting opnieuw opstarten**.

## <a name="remove-a-configuration"></a>Een configuratie verwijderen
Voer de volgende stappen uit om een configuratie te verwijderen.

 1.  Selecteer in Azure Portal **Azure Active Directory**.
 2. Selecteer **Azure AD Connect**.
 3. Selecteer **inrichting beheren (preview)**.
 4. Selecteer uw configuratie onder **configuratie**.
   
   ![Configuratie selectie voor het verwijderen van de configuratie](media/how-to-configure/scope1.png)

 5. Selecteer boven aan het configuratie scherm de optie **verwijderen**.

>[!IMPORTANT]
>Er is geen bevestiging voordat een configuratie wordt verwijderd. Zorg ervoor dat dit de actie is die u wilt uitvoeren voordat u **verwijderen**selecteert.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)

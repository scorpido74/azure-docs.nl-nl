---
title: Azure AD Connect cloud provisioning nieuwe agent configuratie
description: In dit artikel wordt beschreven hoe u cloudprovisioning installeert.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620975"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Een nieuwe configuratie maken voor Azure AD Connect cloudgebaseerde inrichting

Nadat u de agent hebt geïnstalleerd, moet u zich aanmelden bij de Azure-portal en Azure Active Directory (Azure AD) Connect-cloudinrichting configureren. Volg deze stappen om de agent in te schakelen.

## <a name="configure-provisioning"></a>Inrichten configureren
Voer deze stappen uit om provisioning te configureren.

1.  Selecteer in de Azure-portal **Azure Active Directory**.
1.  Selecteer **Azure AD Connect**.
1.  Selecteer **Inrichting beheren (voorbeeld)**.

    ![Inrichten beheren (voorbeeld)](media/how-to-configure/manage1.png)

1.  Selecteer **Nieuwe configuratie**.
1.  In het configuratiescherm is het on-premises domein vooraf ingevuld.
1.  Voer een **e-mail met melding in**. Deze e-mail wordt op de hoogte gebracht wanneer de inrichting niet in orde is.
1.  Verplaats de kiezer naar **Inschakelen**en selecteer **Opslaan**.

    ![Azure AD-inrichting (voorbeeld)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Scope voorziening voor specifieke gebruikers en groepen
U de agent scopen om specifieke gebruikers en groepen te synchroniseren met behulp van on-premises Active Directory-groepen of organisatie-eenheden. U geen groepen en organisatie-eenheden configureren binnen een configuratie. 

1.  Selecteer in de Azure-portal **Azure Active Directory**.
1.  Selecteer **Azure AD Connect**.
1.  Selecteer **Inrichting beheren (voorbeeld)**.
1.  Selecteer **onder Configuratie**uw configuratie.

    ![Configuratiesectie](media/how-to-configure/scope1.png)

1.  Selecteer **onder Configureren**alle **gebruikers** selecteren om het bereik van de configuratieregel te wijzigen.

    ![Alle gebruikers optie](media/how-to-configure/scope2.png)

1. Aan de rechterkant u het bereik wijzigen om alleen beveiligingsgroepen op te nemen. Voer de gedistingeerde naam van de groep in en selecteer **Toevoegen**.

    ![Optie Geselecteerde beveiligingsgroepen](media/how-to-configure/scope3.png)

1.  U het bereik wijzigen om alleen specifieke organisatie-eenheden op te nemen. Selecteer **Gereed** en **Opslaan**.  
2.  Zodra u het bereik hebt gewijzigd, moet u [de inrichting opnieuw starten](#restart-provisioning) om een onmiddellijke synchronisatie van de wijzigingen te starten.

    ![Optie Geselecteerde organisatie-eenheden](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Inrichting opnieuw opstarten 
Als u niet wilt wachten op de volgende geplande uitvoering, activeert u de inprovisioning met de knop **Inrichting opnieuw starten.** 
1.  Selecteer in de Azure-portal **Azure Active Directory**.
1.  Selecteer **Azure AD Connect**.
1.  Selecteer **Inrichting beheren (voorbeeld)**.
1.  Selecteer **onder Configuratie**uw configuratie.

    ![Configuratieselectie om de inrichting opnieuw te starten](media/how-to-configure/scope1.png)

1.  Selecteer bovenaan **Inrichting opnieuw starten**.

## <a name="remove-a-configuration"></a>Een configuratie verwijderen
Voer deze stappen uit om een configuratie te verwijderen.

1.  Selecteer in de Azure-portal **Azure Active Directory**.
1.  Selecteer **Azure AD Connect**.
1.  Selecteer **Inrichting beheren (voorbeeld)**.
1.  Selecteer **onder Configuratie**uw configuratie.

    ![Configuratieselectie om configuratie te verwijderen](media/how-to-configure/scope1.png)

1.  Selecteer Boven aan het configuratiescherm **delete**.

    ![De knop Verwijderen](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Er is geen bevestiging voorafgaand aan het verwijderen van een configuratie. Zorg ervoor dat dit de actie is die u wilt uitvoeren voordat u **Verwijderen**selecteert.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)

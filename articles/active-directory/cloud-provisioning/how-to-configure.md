---
title: Nieuwe agent configuratie voor het inrichten van de Cloud Azure AD Connect
description: In dit onderwerp wordt beschreven hoe u Cloud inrichting installeert.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f08a1359cfd8a2793d92315a6b03567b0b3f847d
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997119"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Nieuwe configuratie voor het inrichten van Cloud Azure AD Connect

Nadat u de agent hebt geïnstalleerd, moet u zich aanmelden bij de Azure Portal en inrichten configureren.  Gebruik de volgende stappen om de agent in te scha kelen.

## <a name="configure-provisioning"></a>Inrichting configureren
Als u inrichting wilt configureren, gebruikt u de volgende stappen:

1.  Klik in de Azure AD-Portal op **Azure Active Directory**
2.  Klik op **Azure AD Connect**
3.  Selecteer **inrichtings beheer (preview)** 
![](media/how-to-configure/manage1.png)

4.  Klik op **nieuwe configuratie**.
5.  Op het scherm configuratie is het on-premises domein vooraf ingevuld
6. Voer een **e-mail melding**in. Deze e-mail wordt gewaarschuwd wanneer het inrichten niet in orde is.  
8. Verplaats de selector om deze in te **scha kelen** en klik op **Opslaan**.
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>Scopeing inrichten voor specifieke gebruikers en groepen
Als u de agent wilt bereiken om alleen specifieke gebruikers en groepen te synchroniseren, kunt u dit doen. U kunt bereiken met behulp van on-premises AD-groepen of organisatie-eenheden. U kunt geen groepen en organisatie-eenheden configureren binnen een configuratie. 

1.  Klik in de Azure AD-Portal op **Azure Active Directory**
2.  Klik op **Azure AD Connect**
3.  Selecteer **inrichting beheren (preview-versie)**
4.  Klik onder **configuratie** op uw configuratie.  
![](media/how-to-configure/scope1.png)

5.  Selecteer onder **configureren** **alle gebruikers** om het bereik van de configuratie regel te wijzigen.
![](media/how-to-configure/scope2.png)

6. Aan de rechter kant kunt u het bereik wijzigen zodat alleen beveiligings groepen worden opgenomen door de DN-naam van de groep in te voeren en op **toevoegen**te klikken.
![](media/how-to-configure/scope3.png)

7. Of wijzig deze zodat alleen specifieke organisatie-eenheden worden meegenomen. Klik op **gereed** en **Sla**het bestand op.
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Inrichting opnieuw starten 
Als u niet wilt wachten op de volgende geplande uitvoering, kunt u de inrichtings uitvoering activeren met behulp van de knop inrichting opnieuw opstarten. 
1.  Klik in de Azure AD-Portal op **Azure Active Directory**
2.  Klik op **Azure AD Connect**
3.  Selecteer **inrichting beheren (preview-versie)**
4.  Klik onder **configuratie** op uw configuratie.  
![](media/how-to-configure/scope1.png)

5.  Klik bovenaan op **inrichting opnieuw opstarten**.

## <a name="removing-a-configuration"></a>Een configuratie verwijderen
Als u een configuratie wilt verwijderen, kunt u dit doen met behulp van de volgende stappen.

1.  Klik in de Azure AD-Portal op **Azure Active Directory**
2.  Klik op **Azure AD Connect**
3.  Selecteer **inrichting beheren (preview-versie)**
4.  Klik onder **configuratie** op uw configuratie.  
![](media/how-to-configure/scope1.png)

5.  Klik bovenaan op **verwijderen**.
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Er is geen bevestiging voordat een configuratie wordt verwijderd. Zorg er dus voor dat dit de actie is die u wilt uitvoeren voordat u op **verwijderen**klikt.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)

---
title: Kenmerken synchroniseren met Azure AD voor toewijzing | Microsoft Documenten
description: Meer informatie over het synchroniseren van kenmerken van uw on-premises Active Directory naar Azure AD. Wanneer u gebruikersinrichting voor SaaS-apps configureert, gebruikt u de functie directoryextensie om bronkenmerken toe te voegen die niet standaard zijn gesynchroniseerd.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mimart
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09d1efaf54bee65bd3274987e68e643f887baade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522268"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Een kenmerk synchroniseren van uw on-premises Active Directory met Azure AD voor inrichten op een toepassing

Wanneer u kenmerktoewijzingen aanpast voor gebruikersinrichting, wordt mogelijk voorkomen dat het kenmerk dat u wilt toewijzen, niet wordt weergegeven in de lijst **met bronkenmerken.** In dit artikel ziet u hoe u het ontbrekende kenmerk toevoegt door het te synchroniseren van uw on-premises Active Directory (AD) naar Azure Active Directory (Azure AD).

Azure AD moet alle gegevens bevatten die nodig zijn om een gebruikersprofiel te maken bij het inrichten van gebruikersaccounts van Azure AD naar een SaaS-app. In sommige gevallen hebt u mogelijk synchronisatiekenmerken van uw on-premises AD naar Azure AD nodig om de gegevens beschikbaar te maken. Azure AD Connect synchroniseert automatisch bepaalde kenmerken met Azure AD, maar niet alle kenmerken. Bovendien worden sommige kenmerken (zoals SAMAccountName) die standaard worden gesynchroniseerd, mogelijk niet weergegeven met de Microsoft Graph API. In deze gevallen u de functie Azure AD Connect-directoryextensie gebruiken om het kenmerk te synchroniseren met Azure AD. Op die manier is het kenmerk zichtbaar voor de Microsoft Graph API en de Azure AD-inrichtingsservice.

Als de gegevens die u nodig hebt voor de inrichting in Active Directory staan, maar niet beschikbaar zijn voor inlevering vanwege de hierboven beschreven redenen, voert u deze stappen uit.
 
## <a name="sync-an-attribute"></a>Een kenmerk synchroniseren 

1. Open de wizard Azure AD Connect, kies Taken en kies **synchronisatieopties aanpassen**.

   ![Wizard Extra taken in Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Meld u aan als Algemeen Azure AD-beheerder. 

3. Selecteer op de pagina **Optionele onderdelen** de **optie Adressynchronisatie van directory-extensie**.
 
   ![Pagina Met optionele functies van azure Active Directory Connect-wizard](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecteer het kenmerk(en) dat u wilt uitbreiden naar Azure AD.
   > [!NOTE]
   > De zoekopdracht onder **Beschikbare kenmerken** is hoofdlettergevoelig.

   ![Selectiepagina van de wizard Directory-extensies van Azure Active Directory](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Voltooi de wizard Azure AD Connect en laat een volledige synchronisatiecyclus uitvoeren. Wanneer de cyclus is voltooid, wordt het schema uitgebreid en worden de nieuwe waarden gesynchroniseerd tussen uw on-premises AD en Azure AD.
 
6. In de Azure-portal bevat de lijst **met** [gebruikerskenmerken](customize-application-attributes.md)in de Azure-portal het toegevoegde `<attributename> (extension_<appID>_<attributename>)`kenmerk in de indeling. Selecteer het kenmerk en wijs het toe aan de doeltoepassing voor inrichten.

   ![Selectiepagina van de wizard Directory-extensies van Azure Active Directory](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> De mogelijkheid om referentiekenmerken van on-premises AD in te richten, zoals **beheerd door** of **DN/DistinguishedName,** wordt vandaag niet ondersteund. U deze functie aanvragen op [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="next-steps"></a>Volgende stappen

* [Bepalen wie in het vermogen van de inrichting](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

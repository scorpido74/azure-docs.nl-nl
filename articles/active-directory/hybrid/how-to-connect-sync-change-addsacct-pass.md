---
title: 'Azure AD Connect-synchronisatie: het wachtwoord van het AD DS-account wijzigen | Microsoft Documenten'
description: In dit onderwerpdocument wordt beschreven hoe u Azure AD Connect bijwerken nadat het wachtwoord van het AD DS-account is gewijzigd.
services: active-directory
keywords: AD DS-account, Active Directory-account, wachtwoord
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35e04be046e20883f60c576745a29342add68a81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60241592"
---
# <a name="changing-the-ad-ds-account-password"></a>Het wachtwoord voor het AD DS-account wijzigen
Het AD DS-account verwijst naar het gebruikersaccount dat wordt gebruikt door Azure AD Connect om te communiceren met on-premises Active Directory. Als u het wachtwoord van het AD DS-account wijzigt, moet u Azure AD Connect Synchronization Service bijwerken met het nieuwe wachtwoord. Anders kan de synchronisatie niet meer correct synchroniseren met de on-premises Active Directory en worden de volgende fouten ervaren:

* In de synchronisatieservicemanager mislukt elke import- of exportbewerking met on-premises AD met fout **met geen beginreferenties.**

* Onder Windows Event Viewer bevat het logboek van de toepassingsgebeurtenis een fout met **gebeurtenis-id 6000** en het bericht **'De beheeragent 'contoso.com' kan niet worden uitgevoerd omdat de referenties ongeldig waren.**


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>De synchronisatieservice bijwerken met een nieuw wachtwoord voor AD DS-account
Ga als volgende over de synchronisatieservice met het nieuwe wachtwoord:

1. Start de Synchronisatieservicebeheer (START → Synchronisatieservice).
</br>![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. Ga naar het tabblad **Connectors.**

3. Selecteer de **AD-connector** die overeenkomt met het AD DS-account waarvoor het wachtwoord is gewijzigd.

4. Selecteer **Eigenschappen**onder **Acties**.

5. Selecteer verbinding maken met **Active Directory Forest**in het pop-updialoogvenster:

6. Voer het nieuwe wachtwoord van het AD DS-account in het tekstvak **Wachtwoord** in.

7. Klik op **OK** om het nieuwe wachtwoord op te slaan en het pop-updialoogvenster te sluiten.

8. Start de Azure AD Connect-synchronisatieservice opnieuw onder Windows Service Control Manager. Dit is om ervoor te zorgen dat elke verwijzing naar het oude wachtwoord wordt verwijderd uit de geheugencache.

## <a name="next-steps"></a>Volgende stappen
**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)

* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

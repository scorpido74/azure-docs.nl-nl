---
title: Een organisatie verlaten als gastgebruiker - Azure Active Directory
description: Laat zien hoe een Azure AD B2B-gastgebruiker een organisatie kan verlaten met behulp van het Access-paneel.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bce67c81b924d768826402b707c41c085b7767b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272500"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Een organisatie verlaten als gastgebruiker

Een Azure Active Directory (Azure AD) B2B-gastgebruiker kan op elk gewenst moment besluiten een organisatie te verlaten als hij geen apps meer uit die organisatie hoeft te gebruiken of een koppeling hoeft te onderhouden. Een gebruiker kan een organisatie alleen verlaten, zonder contact op te nemen met een beheerder.

> [!NOTE]
> Een gastgebruiker kan een organisatie niet verlaten als zijn account is uitgeschakeld in de woningtenant of de resourcetenant. Als hun account is uitgeschakeld, moet de gastgebruiker contact opnemen met de tenantbeheerder, die het gastaccount kan verwijderen of het gastaccount kan inschakelen, zodat de gebruiker de organisatie kan verlaten.

## <a name="leave-an-organization"></a>Een organisatie verlaten

Als u een organisatie wilt verlaten, voert u deze stappen uit.

1. Ga naar de pagina Profiel van het Access-deelvenster door een van de volgende stappen uit te voeren:
   
   - Klik in de [Azure-portal](https://portal.azure.com)op uw naam rechtsboven en selecteer **Account weergeven**.
   - Open het [toegangspaneel,](https://myapps.microsoft.com)klik op uw naam rechtsboven en selecteer naast **Organisaties**het pictogram Instellingen (versnelling).
 
   ![Schermafbeelding van gebruikersinstellingen in het Access-deelvenster](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Als u nog niet bent aangemeld bij de organisatie die u wilt verlaten, klikt u onder **Organisaties**op de **koppeling Aanmelden om de organisatiekoppeling** naast de naam van de organisatie te verlaten. Nadat u bent aangemeld, klikt u opnieuw op uw naam rechtsboven en naast **Organisaties,** selecteert u het pictogram Instellingen (versnelling).

3. Zoek **onder Organisaties**de organisatie die u wilt verlaten en selecteer Organisatie **verlaten**.

   ![Schermafbeelding van de optie Organisatie verlaten in de gebruikersinterface](media/leave-the-organization/LeaveOrg.png)

4. Wanneer u wordt gevraagd om te bevestigen, selecteert u **Verlaten**. 

## <a name="account-removal"></a>Account verwijderen

Wanneer een gebruiker een organisatie verlaat, wordt het gebruikersaccount 'soft verwijderd' in de map. Het gebruikersobject wordt standaard verplaatst naar het gebied **Verwijderde gebruikers** in Azure AD, maar wordt gedurende 30 dagen niet permanent verwijderd. Met deze zachte verwijdering kan de beheerder het gebruikersaccount herstellen (inclusief groepen en machtigingen), als de gebruiker een verzoek doet om het account binnen de periode van 30 dagen te herstellen.

Indien gewenst kan een tenantbeheerder het account op elk gewenst moment gedurende de periode van 30 dagen permanent verwijderen. Om dit te doen:

1. Selecteer Azure Active Directory in de [Azure-portal](https://portal.azure.com). **Azure Active Directory**
2. Onder **Beheren**, selecteer **Gebruikers**.
3. Selecteer **Verwijderde gebruikers**.
4. Schakel het selectievakje naast een verwijderde gebruiker in en selecteer **Definitief verwijderen**.

Als u een gebruiker permanent verwijdert, is deze actie onherroepelijk.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie Wat is Azure AD B2B-samenwerking voor een overzicht van Azure AD [B2B-samenwerking?](what-is-b2b.md)




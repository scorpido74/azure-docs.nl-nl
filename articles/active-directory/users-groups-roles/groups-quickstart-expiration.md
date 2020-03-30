---
title: Snel start beleid voor het verlopen van groepen - Azure AD | Microsoft Documenten
description: Verloop voor Office 365-groepen - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16b6e4f521568c89e415e6e9fa621175052cf34d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74026900"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Snelstart: Office 365-groepen voor verloop instellen in Azure Active Directory

In deze snelstart stelt u het verloopbeleid in voor uw Office 365-groepen. Wanneer gebruikers hun eigen groepen kunnen instellen, kunnen niet-gebruikte groepen zich vermenigvuldigen. Eén manier om niet-gebruikte groepen te beheren, is door verloopbeleid voor deze groepen in te stellen om het handmatig verwijderen van groepen te verminderen.

Verloopbeleid maken is eenvoudig:

- Groepen met gebruikersactiviteiten worden automatisch verlengd naarmate de vervaldatum nadert
- Groepseigenaren worden op de hoogte gesteld als een groep moet worden vernieuwd die op het punt staat te verlopen
- Een groep die niet wordt vernieuwd, wordt verwijderd
- Een verwijderde Office 365-groep kan binnen dertig dagen door een groepseigenaar of een Azure AD-beheerder worden hersteld

> [!NOTE]
> Groepen gebruiken nu Azure AD-intelligentie om automatisch te worden vernieuwd op basis van de vraag of ze recent zijn gebruikt. Deze verlengingsbeslissing is gebaseerd op gebruikersactiviteiten in groepen in Office 365-services zoals Outlook, SharePoint, Teams, Yammer en anderen.

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisite"></a>Vereiste

 De minst bevoorrechte rol die nodig is om groepsvervaldatum in te stellen, is gebruikersbeheerder in de organisatie.

## <a name="turn-on-user-creation-for-groups"></a>Maken van gebruikers voor groepen inschakelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een gebruikersbeheerdersaccount.

2. Selecteer **Groepen** en vervolgens **Algemeen**.
  
   ![Pagina Instellingen voor selfservicegroepen](./media/groups-quickstart-expiration/self-service-settings.png)

3. Stel **Users can create Office 365 groups** in op **Ja**.

4. Selecteer **Opslaan** om de groepsinstellingen op te slaan als u klaar bent.

## <a name="set-group-expiration"></a>Verloopdatum van de groep instellen

1. Meld u aan bij de [Azure-portal,](https://portal.azure.com) > selecteer Azure Active **Azure Active Directory** > **Directory-groepen****die verlopen** om de vervaldatum-instellingen te openen.
  
   ![Pagina Met de instellingen voor de verwerking voor groep](./media/groups-quickstart-expiration/expiration-settings.png)

2. Stel het verloopinterval in. Selecteer een vooraf ingestelde waarde of voer een aangepaste waarde van meer dan 31 dagen in. 

3. Geef een e-mailadres op waar meldingen over verlooptijden naartoe moeten worden gestuurd als een groep geen eigenaar heeft.

4. Stel voor deze snelstart **Enable expiration for these Office 365 groups** in op **Alle**.

5. Selecteer **Opslaan** om de instellingen voor het verloop op te slaan als u klaar bent.

Dat is alles. In deze snelstart hebt u het verloopbeleid ingesteld voor de geselecteerde Office 365-groepen.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="to-remove-the-expiration-policy"></a>Verloopbeleid verwijderen

1. Zorg dat u bent aangemeld bij de [Azure-portal](https://portal.azure.com) met een account van een globale beheerder voor de tenant.
2. Selecteer de**vervaldatum**van Azure Active**Directory-groepen** >  **Azure Active Directory** > .
3. Stel **Enable expiration for these Office 365 groups** in op **Geen**.

### <a name="to-turn-off-user-creation-for-groups"></a>Maken van gebruikers voor groepen uitschakelen

1. Selecteer **Algemeen Azure Active Directory** > **Groups** > **.** 
2. Stel **Users can create Office 365 groups in Azure portals** in op **Nee**.

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor meer informatie over het verlopen, inclusief PowerShell-instructies en technische beperkingen:

> [!div class="nextstepaction"]
> [Verloopbeleid PowerShell](groups-lifecycle.md)

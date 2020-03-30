---
title: B2B-samenwerkingsgebruikers toevoegen aan de Azure-portal - Azure AD
description: Laat zien hoe een beheerder gastgebruikers kan toevoegen aan hun directory vanuit een partnerorganisatie met Azure Active Directory (Azure AD) B2B-samenwerking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf2ca3bde0cfee97adee8bb875df1522e95b1f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263502"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Active Directory B2B-samenwerkingsgebruikers toevoegen aan de Azure-portal

Als gebruiker aan wie een van de beperkte beheerdersmaprollen is toegewezen, u de Azure-portal gebruiken om Gebruikers van B2B-samenwerkingsinstellingen uit te nodigen. U gastgebruikers uitnodigen voor de directory, voor een groep of voor een toepassing. Nadat u een gebruiker via een van deze methoden hebt uitgenodigd, wordt het account van de uitgenodigde gebruiker toegevoegd aan Azure Active Directory (Azure AD), met een gebruikerstype *Gast*. De gastgebruiker moet vervolgens zijn uitnodiging inwisselen om toegang te krijgen tot bronnen. Een uitnodiging van een gebruiker vervalt niet.

Nadat u een gastgebruiker aan de directory hebt toegevoegd, u de gastgebruiker een directe link naar een gedeelde app sturen of de gastgebruiker kan op de inwisselings-URL in de uitnodigingse-mail klikken. Zie [B2B-uitnodigingsuitnodigingen](redemption-experience.md)voor Samenwerking voor meer informatie over het inwisselproces.

> [!IMPORTANT]
> Volg de stappen in [How-to: Voeg de privacygegevens van uw organisatie toe in Azure Active Directory](https://aka.ms/adprivacystatement) om de URL van de privacyverklaring van uw organisatie toe te voegen. Als onderdeel van het eerste keer inwisselproces voor uitnodigingen moet een uitgenodigde gebruiker toestemming geven voor uw privacyvoorwaarden om door te gaan. 

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat de externe samenwerkingsinstellingen van uw organisatie zo zijn geconfigureerd dat u gasten uitnodigen. Standaard kunnen alle gebruikers en beheerders gasten uitnodigen. Het externe samenwerkingsbeleid van uw organisatie kan echter worden geconfigureerd om te voorkomen dat bepaalde typen gebruikers of beheerders gasten uitnodigen. Zie Externe samenwerking inschakelen en beheren [wie gasten kan uitnodigen](delegate-invitations.md)voor meer informatie over het bekijken en instellen van dit beleid.

## <a name="add-guest-users-to-the-directory"></a>Gastgebruikers toevoegen aan de map

Voer de volgende stappen uit om Gebruikers van B2B-samenwerking toe te voegen aan de map:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als gebruiker aan wie een beperkte beheerdersmaprol of de rol Gastuitnodigingsuitnodiging heeft gekregen.
2. Zoeken naar en selecteer **Azure Active Directory** op elke pagina.
3. Onder **Beheren**, selecteer **Gebruikers**.
4. Selecteer **Nieuwe gastgebruiker**.

   ![Ziet waar nieuwe gastgebruiker zich bevindt in de gebruikersinterface](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
   > [!NOTE]
   > De optie **Nieuwe gastgebruiker** is ook beschikbaar op de pagina **Organisatierelaties.** Selecteer **organisatierelaties**in **Azure Active Directory**onder **Beheren**.

5. Selecteer op de pagina **Nieuw gebruiker** de optie **Gebruiker uitnodigen** en voeg vervolgens de gegevens van de gastgebruiker toe. 

    > [!NOTE]
    > Groepse-mailadressen worden niet ondersteund. voer het e-mailadres voor een individu in. Ook kunnen gebruikers met sommige e-mailproviders een plussymbool (+) en extra tekst toevoegen aan hun e-mailadressen om te helpen bij bijvoorbeeld het filteren van inboxen. Azure AD biedt momenteel echter geen ondersteuning voor plussymbolen in e-mailadressen. Als u leveringsproblemen wilt voorkomen, laat u het plussymbool en eventuele tekens na het symbool @weg.

   - **Naam.** De voor- en achternaam van de gastgebruiker.
   - **E-mailadres (vereist)**. Het e-mailadres van de gastgebruiker.
   - **Persoonlijk bericht (optioneel)** Voeg een persoonlijk welkomstbericht toe aan de gastgebruiker.
   - **Groepen**: U de gastgebruiker toevoegen aan een of meer bestaande groepen, of u dit later doen.
   - **Maprol:** als u Azure AD-beheermachtigingen voor de gebruiker nodig hebt, u deze toevoegen aan een Azure AD-rol. 

7. Selecteer **Uitnodigen** voor het automatisch verzenden van de uitnodiging voor de gastgebruiker. 
 
Nadat u de uitnodiging verzendt, wordt het gebruikersaccount automatisch toegevoegd aan de map als gast.


![Toont B2B-gebruiker met gastgebruikerstype](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Gastgebruikers toevoegen aan een groep
Als u gebruikers van B2B-samenwerking handmatig aan een groep wilt toevoegen, voert u de volgende stappen uit:

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Zoeken naar en selecteer **Azure Active Directory** op elke pagina.
3. Selecteer **Groepen** **onder Beheren**.
4. Selecteer een groep (of klik op **Nieuwe groep** om een nieuwe groep te maken). Het is een goed idee om in de groepsbeschrijving op te nemen dat de groep B2B-gastgebruikers bevat.
5. Selecteer **Leden**. 
6. Voer een van de volgende handelingen uit:
   - Als de gastgebruiker al in de map aanwezig is, zoekt u naar de B2B-gebruiker. Selecteer de gebruiker en klik op **Selecteren** om de gebruiker aan de groep toe te voegen.
   - Als de gastgebruiker nog niet in de map aanwezig is, nodigt u hem of haar uit voor de groep door zijn/haar e-mailadres in het zoekvak te typen, een optioneel persoonlijk bericht te typen en vervolgens op **Selecteren**te klikken. De uitnodiging gaat automatisch uit naar de uitgenodigde gebruiker.
     
     ![Knop Uitnodigen toevoegen om gastleden toe te voegen](./media/add-users-administrator/GroupInvite.png)
   
U ook dynamische groepen gebruiken met Azure AD B2B-samenwerking. Zie [Dynamische groepen en Azure Active Directory B2B-samenwerking](use-dynamic-groups.md)voor meer informatie.

## <a name="add-guest-users-to-an-application"></a>Gastgebruikers toevoegen aan een toepassing

Voer de volgende stappen uit om gebruikers van B2B-samenwerkingsgebruikers toe te voegen aan een toepassing:

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Zoeken naar en selecteer **Azure Active Directory** op elke pagina.
3. Selecteer **onder Beheren**de optie Alle toepassingen **van** > **All applications**Ondernemingen selecteren .
4. Selecteer de toepassing waaraan u gastgebruikers wilt toevoegen.
5. Selecteer Op het dashboard van de toepassing **Totaal aantal gebruikers** om het deelvenster Gebruikers en **groepen** te openen.

    ![Knop Totaal aantal gebruikers om geopende gebruikers en groepen toe te voegen](./media/add-users-administrator/AppUsersAndGroups.png)

6. Selecteer **Gebruiker toevoegen**.
7. Selecteer **Onder Toewijzing toevoegen**de optie Gebruiker en **groepen**.
8. Voer een van de volgende handelingen uit:
   - Als de gastgebruiker al in de map aanwezig is, zoekt u naar de B2B-gebruiker. Selecteer de gebruiker, klik op **Selecteren**en klik vervolgens op **Toewijzen** om de gebruiker aan de app toe te voegen.
   - Als de gastgebruiker nog niet bestaat in de map, onder **Lid selecteren of een externe gebruiker uitnodigen,** typt u het e-mailadres van de gebruiker. Typ in het berichtvak een optioneel persoonlijk bericht. Klik onder het berichtvak op **Uitnodigen**.
           
       ![Knop Uitnodigen toevoegen om gastleden toe te voegen](./media/add-users-administrator/AppInviteUsers.png)
   
      Klik **op Selecteren**en klik vervolgens op **Toewijzen** om de gebruiker aan de app toe te voegen. Een uitnodiging gaat automatisch uit naar de uitgenodigde gebruiker.

9. De gastgebruiker wordt weergegeven in de lijst **Gebruikers en groepen** van de toepassing met de toegewezen rol **standaardtoegang**. Als u de rol wilt wijzigen, gaat u als volgt te werk:
   - Selecteer de gastgebruiker en selecteer **Bewerken**. 
   - Klik **onder Toewijzing bewerken**op Rol **selecteren**en selecteer de rol die u aan de geselecteerde gebruiker wilt toewijzen.
   - Klik **op Selecteren**.
   - Klik op **Toewijzen**.
 
## <a name="resend-invitations-to-guest-users"></a>Uitnodigingen voor gastgebruikers opnieuw verzenden

Als een gastgebruiker zijn uitnodiging nog niet heeft ingewisseld, u de uitnodigingse-mail opnieuw verzenden.

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Zoeken naar en selecteer **Azure Active Directory** op elke pagina.
3. Onder **Beheren**, selecteer **Gebruikers**.
5. Selecteer het gebruikersaccount.
6. Selecteer **Onder Beheren**selecteren , Selecteer **Profiel**.
7. Als de gebruiker de uitnodiging nog niet heeft geaccepteerd, is er een **uitnodigingsoptie opnieuw verzenden** beschikbaar. Selecteer deze knop die u opnieuw wilt verzenden.

   ![De optie Uitnodiging opnieuw verzenden in het gebruikersprofiel](./media/add-users-administrator/b2b-user-resend-invitation.png)

> [!NOTE]
> Als u een uitnodiging verzendt die de gebruiker oorspronkelijk naar een specifieke app heeft geleid, moet u begrijpen dat de koppeling in de nieuwe uitnodiging de gebruiker in plaats daarvan naar het access-panel op het hoogste niveau brengt.

## <a name="next-steps"></a>Volgende stappen

- Zie [Hoe informatiewerkers B2B-samenwerkingsgebruikers toevoegen](add-users-information-worker.md) voor meer informatie over het toevoegen van niet-Azure-AD-beheerders?
- Zie De elementen van [de e-mail met uitnodigingsuitnodiging voor B2B.](invitation-email-elements.md)


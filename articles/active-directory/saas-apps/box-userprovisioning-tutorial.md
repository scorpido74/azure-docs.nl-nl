---
title: 'Zelfstudie: Vak configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Box .
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 1c959595-6e57-4954-9c0d-67ba03ee212b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1397b4189a9c2c15e3878687ea8c67c1da7567f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77058566"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Zelfstudie: Vak configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen weer te geven die u moet uitvoeren in Box en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar Box.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Box hebt u de volgende items nodig:

- Een Azure AD-tenant
- Een Box Business plan of beter

> [!NOTE]
> Wanneer u de stappen in deze zelfstudie test, raden we u aan *geen* productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Gebruikers toewijzen aan Vak 

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Box-app. Eenmaal besloten, u deze gebruikers toewijzen aan uw Box-app door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Gebruikers en groepen toewijzen
Met het tabblad **Vak > gebruikers en groepen** in de Azure-portal u opgeven welke gebruikers en groepen toegang moeten krijgen tot Box. Toewijzing van een gebruiker of groep zorgt ervoor dat de volgende dingen optreden:

* Azure AD staat de toegewezen gebruiker (via directe toewijzing of groepslidmaatschap) toe om zich te verifiëren naar Box. Als een gebruiker niet is toegewezen, staat Azure AD niet toe zich aan te melden bij Box en geeft het een fout op de aanmeldingspagina van Azure AD.
* Er wordt een app-tegel voor Box toegevoegd aan het [startprogramma voor toepassingen](../manage-apps/end-user-experiences.md)van de gebruiker.
* Als automatische inrichting is ingeschakeld, worden de toegewezen gebruikers en/of groepen toegevoegd aan de inrichtingswachtrij die automatisch moet worden ingericht.
  
  * Als alleen gebruikersobjecten zijn geconfigureerd om te worden ingericht, worden alle direct toegewezen gebruikers in de inrichtingswachtrij geplaatst en worden alle gebruikers die lid zijn van toegewezen groepen in de inrichtingswachtrij geplaatst. 
  * Als groepsobjecten zijn geconfigureerd om te worden ingericht, worden alle toegewezen groepsobjecten in box ingericht en alle gebruikers die lid zijn van die groepen. De groeps- en gebruikerslidmaatschappen worden bewaard nadat ze naar Box zijn geschreven.

U het tabblad **Kenmerken > Enkele aanmelding** gebruiken om te configureren welke gebruikerskenmerken (of claims) aan Box worden gepresenteerd tijdens SAML-verificatie en het tabblad **Kenmerken > Inrichting** om te configureren hoe gebruikers- en groepskenmerken tijdens inrichtingsbewerkingen van Azure AD naar Box stromen.

### <a name="important-tips-for-assigning-users-to-box"></a>Belangrijke tips voor het toewijzen van gebruikers aan Box 

*   Het wordt aanbevolen dat één Azure AD-gebruiker die aan Box is toegewezen, de inrichtingsconfiguratie test. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

*   Wanneer u een gebruiker aan een vak toewijst, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichten.

## <a name="enable-automated-user-provisioning"></a>Geautomatiseerde gebruikersinrichting inschakelen

In deze sectie wordt uw Azure AD verbonden met de API voor het inrichten van het gebruikersaccount van Box en wordt de inrichtingsservice geconfigureerd om toegewezen gebruikersaccounts in Box te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

Als automatische inrichting is ingeschakeld, worden de toegewezen gebruikers en/of groepen toegevoegd aan de inrichtingswachtrij die automatisch moet worden ingericht.
    
 * Als alleen gebruikersobjecten zijn geconfigureerd om te worden ingericht, worden direct toegewezen gebruikers in de inrichtingswachtrij geplaatst en worden alle gebruikers die lid zijn van toegewezen groepen in de inrichtingswachtrij geplaatst. 
    
 * Als groepsobjecten zijn geconfigureerd om te worden ingericht, worden alle toegewezen groepsobjecten in box ingericht en alle gebruikers die lid zijn van die groepen. De groeps- en gebruikerslidmaatschappen worden bewaard nadat ze naar Box zijn geschreven.

> [!TIP] 
> U er ook voor kiezen om SAML-gebaseerde single sign-On for Box in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-account-provisioning"></a>Ga als een automatisch beheer van het gebruikersaccount:

Het doel van deze sectie is om te schetsen hoe u de inrichting van Active Directory-gebruikersaccounts naar Box inschakelen.

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

2. Als u Box al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van Vak met behulp van het zoekveld. Selecteer Anders **Toevoegen** en zoeken naar **Vak** in de toepassingsgalerie. Selecteer Vak in de zoekresultaten en voeg het toe aan uw lijst met toepassingen.

3. Selecteer uw instantie van Vak en selecteer vervolgens het tabblad **Inrichten.**

4. Stel de **inrichtingsmodus** in op **Automatisch**. 

    ![Provisioning](./media/box-userprovisioning-tutorial/provisioning.png)

5. Klik onder de sectie **Beheerdersreferenties** op **Autoriseren** om een aanmeldingsdialoogvenster voor een vak te openen in een nieuw browservenster.

6. Geef **op de pagina Aanmelden toegang tot de** pagina Vak op en klik op **Autoriseren.** 
   
    ![Automatische gebruikersvoorziening inschakelen](./media/box-userprovisioning-tutorial/IC769546.png "Automatische gebruikersvoorziening inschakelen")

7. Klik **op Toegang verlenen tot Box** om deze bewerking te autoriseren en terug te keren naar de Azure-portal. 
   
    ![Automatische gebruikersvoorziening inschakelen](./media/box-userprovisioning-tutorial/IC769549.png "Automatische gebruikersvoorziening inschakelen")

8. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Box-app. Als de verbinding mislukt, moet u ervoor zorgen dat uw Box-account machtigingen voor teambeheer heeft en probeert u de stap **'Autoriseren'** opnieuw.

9. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fouten moet ontvangen in het veld **E-mail melden** en schakel het selectievakje in.

10. Klik **op Opslaan.**

11. Selecteer Azure **Active Directory-gebruikers synchroniseren naar vak** onder de sectie Toewijzingen.

12. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Box. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Vak voor updatebewerkingen te matchen. Selecteer de knop Opslaan om wijzigingen door te voeren.

13. Als u de Azure AD-inrichtingsservice voor Vak wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie Instellingen

14. Klik **op Opslaan.**

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan Box in de sectie Gebruikers en groepen. De eerste synchronisatie duurt langer om uit te voeren dan de daaropvolgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in uw Box-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

In de boxtenant worden gesynchroniseerde gebruikers weergegeven onder **Beheerde gebruikers** in de **beheerconsole**.

![Integratiestatus](./media/box-userprovisioning-tutorial/IC769556.png "Integratiestatus")


## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eén aanmelding configureren](box-tutorial.md)

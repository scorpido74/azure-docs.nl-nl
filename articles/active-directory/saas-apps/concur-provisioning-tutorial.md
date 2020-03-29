---
title: 'Zelfstudie: Concur configureren voor automatische gebruikersvoorziening met Azure Active Directory| Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Concur configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441aa9805f2a453e22f207238315125d2a281838
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60280403"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Zelfstudie: Akkoord configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in Concur en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar Concur.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

*   Een Azure Active directory tenant.
*   Een contract met één aanmelding ingeschakeld.
*   Een gebruikersaccount in Concur met machtigingen voor teambeheerder.

## <a name="assigning-users-to-concur"></a>Gebruikers toewijzen aan Concur

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Concur-app. Eenmaal besloten, u deze gebruikers toewijzen aan uw Concur-app door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Belangrijke tips voor het toewijzen van gebruikers aan Concur

*   Het wordt aanbevolen dat één Azure AD-gebruiker aan Concur wordt toegewezen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

*   Wanneer u een gebruiker aan Concur toewijs, moet u een geldige gebruikersrol selecteren. De rol 'Standaardtoegang' werkt niet voor inrichten.

## <a name="enable-user-provisioning"></a>Gebruikersvoorziening inschakelen

In deze sectie u uw Azure AD verbinden met de API voor het inrichten van het gebruikersaccount van Concur en de inrichtingsservice configureren om toegewezen gebruikersaccounts in Concur te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!Tip] 
> U er ook voor kiezen om SAML-gebaseerde single sign-On voor Concur in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-user-account-provisioning"></a>Ga als een te meer meting in de inrichting van gebruikersaccounts:

Het doel van deze sectie is om te schetsen hoe u de inrichting van Active Directory-gebruikersaccounts voor Concur inschakelen.

Om apps in de onkostenservice in te schakelen, moet er een goede installatie en het gebruik van een Web Service Admin-profiel zijn. Voeg de ws-beheerrol niet toe aan uw bestaande beheerdersprofiel dat u gebruikt voor T-&E-beheerfuncties.

Concur Consultants of de clientbeheerder moeten een duidelijk Web Service Administrator-profiel maken en de clientbeheerder moet dit profiel gebruiken voor de webservicesbeheerdersfuncties (bijvoorbeeld het inschakelen van apps). Deze profielen moeten gescheiden worden gehouden van het dagelijkse T-&E-beheerdersprofiel van de clientbeheerder (het T-&E-beheerdersprofiel mag de WSAdmin-rol niet hebben toegewezen).

Wanneer u het profiel maakt dat moet worden gebruikt om de app in te schakelen, voert u de naam van de clientbeheerder in in de gebruikersprofielvelden. Dit wijst eigendom toe aan het profiel. Zodra een of meer profielen zijn aangemaakt, moet de client zich aanmelden met dit profiel om op de knop*Inschakelen*voor een partner-app in het menu van Web Services te klikken.

Om de volgende redenen mag deze actie niet worden uitgevoerd met het profiel dat zij gebruiken voor een normale T&E-administratie.

* De client moet degene zijn die op *"Ja"* klikt op het dialoogvenster dat wordt weergegeven nadat een app is ingeschakeld. Met deze klik wordt erkend dat de client bereid is dat de partnertoepassing toegang heeft tot hun gegevens, zodat u of de Partner niet op die ja-knop klikken.

* Als een clientbeheerder die een app heeft ingeschakeld met het T-&E-beheerdersprofiel het bedrijf verlaat (waardoor het profiel wordt geïnactiveerd), werken alle apps die zijn ingeschakeld met dat profiel niet totdat de app is ingeschakeld met een ander actief WS-beheerdersprofiel. Dit is de reden waarom je wordt verondersteld om verschillende WS Admin profielen te maken.

* Als een beheerder het bedrijf verlaat, kan de naam die is gekoppeld aan het WS-beheerdersprofiel indien gewenst worden gewijzigd in de vervangende beheerder zonder dat dit gevolgen heeft voor de ingeschakelde app, omdat dat profiel niet hoeft te worden geactiveerd.

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u **Concur** aan bij uw Concur-huurder.

2. Selecteer **Webservices** in **Web Services**het menu Beheer .
   
    ![De huurder van Concur](./media/concur-provisioning-tutorial/IC721729.png "De huurder van Concur")

3. Selecteer aan de linkerkant in het deelvenster **Webservices** de optie **Partnertoepassing inschakelen**.
   
    ![Partnertoepassing inschakelen](./media/concur-provisioning-tutorial/ic721730.png "Partnertoepassing inschakelen")

4. Selecteer in de lijst **Toepassing inschakelen** de optie Azure **Active Directory**en klik op **Inschakelen**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Klik **op Ja** om het dialoogvenster Actie **bevestigen** te sluiten.
   
    ![Actie bevestigen](./media/concur-provisioning-tutorial/ic721732.png "Actie bevestigen")

6. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

7. Als u Concur al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van Concur via het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **Akkoord** in de toepassingsgalerie. Selecteer Akkoord in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

8. Selecteer uw exemplaar van Concur en selecteer vervolgens het tabblad **Inrichten.**

9. Stel de **inrichtingsmodus** in op **Automatisch**. 
 
    ![Provisioning](./media/concur-provisioning-tutorial/provisioning.png)

10. Voer onder de sectie **Beheerdersreferenties** de **gebruikersnaam** en het **wachtwoord** van uw Concur-beheerder in.

11. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Concur-app. Als de verbinding mislukt, moet u ervoor zorgen dat uw Concur-account machtigingen voor teambeheer heeft.

12. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fouten moet ontvangen in het veld **E-mail melden** en schakel het selectievakje in.

13. Klik **op Opslaan.**

14. Selecteer Azure **Active Directory-gebruikers synchroniseren met Akkoord** onder de sectie Toewijzingen.

15. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar Concur. De kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Concur te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

16. Als u de Azure AD-inrichtingsservice voor Concur wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen**

17. Klik **op Opslaan.**

U nu een testaccount aanmaken. Wacht tot 20 minuten om te controleren of het account is gesynchroniseerd met Concur.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eén aanmelding configureren](concur-tutorial.md)


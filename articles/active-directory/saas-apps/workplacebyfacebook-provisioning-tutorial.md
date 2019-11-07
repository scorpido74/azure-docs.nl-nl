---
title: 'Zelf studie: werk plek configureren via Facebook voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Workplace by Facebook configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee091d1c8f0f477354f6bb422d041278ec5668e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73574252"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Zelf studie: werk plek op Facebook configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren op de werk plek van Facebook en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en uit te voeren op de werk plek van Facebook.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met Workplace by Facebook:

- Een Azure AD-abonnement
- Een werk plek met een Facebook-abonnement dat is ingeschakeld voor eenmalige aanmelding

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de  productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u nog geen proefversie van Azure AD hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) een proefversie van één maand aanvragen.

## <a name="assigning-users-to-workplace-by-facebook"></a>Gebruikers toewijzen aan werk plek via Facebook

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw werk plek via Facebook app. Nadat u hebt besloten, kunt u deze gebruikers aan uw werk plek toewijzen via de Facebook-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Belang rijke tips voor het toewijzen van gebruikers aan werk plek via Facebook

*   U wordt aangeraden één Azure AD-gebruiker toe te wijzen op werk plek via Facebook om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

*   Wanneer u een gebruiker toewijst aan werk plek via Facebook, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="enable-user-provisioning"></a>Gebruikers inrichten inschakelen

In deze sectie wordt u begeleid bij het verbinden van uw Azure AD-werk plek met de inrichtings-API van de gebruikers account van Facebook en het configureren van de inrichtings service om toegewezen gebruikers accounts in werk plek te maken, bij te werken en uit te scha kelen op basis van gebruikers en groepen toewijzing in azure AD.

>[!Tip]
>U kunt ook op SAML gebaseerde eenmalige aanmelding inschakelen voor werk plek via Facebook, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Configureren van het inrichten van gebruikers accounts op werk plek via Facebook in azure AD:

Het doel van deze sectie is het maken van een overzicht van het inrichten van Active Directory gebruikers accounts op werk plek via Facebook.

Azure AD biedt ondersteuning voor de mogelijkheid om automatisch de account gegevens van toegewezen gebruikers te synchroniseren met Facebook. Deze automatische synchronisatie maakt werk plek via Facebook mogelijk om de benodigde gegevens op te halen voor toegang, voordat ze zich voor de eerste keer proberen aan te melden. Ook worden gebruikers van werk plek door Facebook verwijderd wanneer de toegang is ingetrokken in azure AD.

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory** > **Enter prise-apps** > **alle toepassingen** .

2. Als u de werk plek al hebt geconfigureerd door Facebook voor eenmalige aanmelding, zoekt u naar uw exemplaar van de werk plek via Facebook met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **werk plek op Facebook** in de toepassings galerie. Selecteer werk plek op Facebook in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw instantie van werk plek op Facebook en selecteer vervolgens het tabblad **inrichten** .

4. Stel de **inrichtings modus** in op **automatisch**. 

    ![inrichtings](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Voer in het gedeelte **beheerders referenties** het toegangs token van uw werk plek in via Facebook-beheerder en stel de waarde van de TENANT-URL in op `https://www.facebook.com/scim/v1/`. Zie deze [instructies](https://developers.facebook.com/docs/workplace/integrations/custom-integrations/apps) voor het maken van een toegangs token voor werk plek. 

6. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw werk plek via de Facebook-app. Als de verbinding mislukt, zorgt u ervoor dat uw werk plek op Facebook-account team beheerders machtigingen heeft.

7. Voer het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen in het veld **e-mail melding** en schakel het selectie vakje in.

8. Klik op **opslaan.**

9. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers op de werk plek synchroniseren met Facebook.**

10. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD op werk plek via Facebook. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om de gebruikers accounts in werk plek van Facebook te vergelijken voor update bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

11. Als u de Azure AD-inrichtings service voor werk plek wilt inschakelen op Facebook, wijzigt **u de** **inrichtings status** in in het gedeelte **instellingen**

12. Klik op **opslaan.**

Zie [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers) voor meer informatie over het configureren van automatische inrichting

U kunt nu een test account maken. Wacht Maxi maal 20 minuten om te controleren of het account is gesynchroniseerd op werk plek via Facebook.

> [!NOTE]
> We werken nauw samen met de werk plek van het Facebook-team om te controleren of de Azure AD-toepassing is goedgekeurd en voldoet aan de nieuwe richt lijnen. De deadline voor de werk plek op Facebook is 16 december en we verwachten dat. Er wordt op dat moment geen werk van klanten verwacht. Op 28-februari-2020 moeten klanten overstappen op de nieuwe integratie. Er wordt hier een bericht weer gegeven zodra het migratie traject beschikbaar is.    

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](workplacebyfacebook-tutorial.md)

---
title: 'Zelfstudie: Velpic configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor Velpic.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b7a6c2c9b7ecb0b160f7481d95f7682f3f7a109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064118"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Zelfstudie: Velpic configureren voor automatische gebruikersvoorziening

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in Velpic en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar Velpic.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure Active Directory-tenant
* Een Velpic huurder met het [Enterprise plan](https://www.velpic.com/pricing.html) of beter ingeschakeld
* Een gebruikersaccount in Velpic met beheerdersmachtigingen

## <a name="assigning-users-to-velpic"></a>Gebruikers toewijzen aan Velpic

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd. 

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Velpic-app. Eenmaal besloten, u deze gebruikers toewijzen aan uw Velpic app door het volgen van de instructies hier:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Belangrijke tips voor het toewijzen van gebruikers aan Velpic

* Het wordt aanbevolen om één Azure AD-gebruiker aan Velpic toe te wijzen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker aan Velpic toewijst, moet u de rol **Gebruiker** of een andere geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Houd er rekening mee dat de functie **Standaardtoegang** niet werkt voor het inrichten en dat deze gebruikers worden overgeslagen.

## <a name="configuring-user-provisioning-to-velpic"></a>Gebruikersvoorziening configureren voor Velpic

In deze sectie u uw Azure AD verbinden met de API voor het inrichten van velpic's gebruikersaccount en de inrichtingsservice configureren om toegewezen gebruikersaccounts in Velpic te maken, bij te werken en uit te schakelen op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-On voor Velpic in te schakelen, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Ga als een automatisch gebruikersaccount voor Velpic in Azure AD:

1. Blader in de [Azure-portal](https://portal.azure.com)naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

2. Als u Velpic al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw voorbeeld van Velpic via het zoekveld. Selecteer Anders **Velpic** **toevoegen** en zoeken in de toepassingsgalerie. Selecteer Velpic in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw instantie Velpic en selecteer het tabblad **Inrichten.**

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![Velpic Provisioning](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Voer onder de sectie **Beheerdersreferenties** de **URL van de tenant in&Secret Token** van Velpic. (U deze waarden vinden onder uw Velpic account: **Manage** > **Integration** > **Plugin** > **SCIM)**

    ![Autorisatiewaarden](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Klik in de Azure-portal op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Velpic-app. Als de verbinding mislukt, zorg er dan voor dat uw Velpic-account beheerdersmachtigingen heeft en probeer stap 5 opnieuw.

7. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fouten moet ontvangen in het veld **E-mail melden** en schakel het selectievakje hieronder in.

8. Klik op **Opslaan**.

9. Selecteer Azure **Active Directory Users synchroniseren met Velpic**onder de sectie Toewijzingen .

10. Controleer in de sectie **Toewijzingen van kenmerken** de gebruikerskenmerken die worden gesynchroniseerd van Azure AD naar Velpic. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **eigenschappen matching** worden gebruikt om de gebruikersaccounts in Velpic te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

11. Als u de Azure AD-inrichtingsservice voor Velpic wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen**

12. Klik op **Opslaan**.

Hiermee wordt gestart met de eerste synchronisatie van gebruikers en/of groepen die aan Velpic zijn toegewezen in de sectie Gebruikers en groepen. Houd er rekening mee dat de eerste synchronisatie langer duurt dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitenrapporten te volgen, waarin alle acties van de inrichtingsservice worden beschreven.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)
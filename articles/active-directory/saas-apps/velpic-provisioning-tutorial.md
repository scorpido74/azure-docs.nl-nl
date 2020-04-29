---
title: 'Zelf studie: Velpic configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op Velpic.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77064118"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Zelf studie: Velpic configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in Velpic en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en te deactiveren naar Velpic.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een Azure Active Directory-tenant
* Een Velpic-Tenant met het [ondernemings plan](https://www.velpic.com/pricing.html) of beter ingeschakeld
* Een gebruikers account in Velpic met beheerders machtigingen

## <a name="assigning-users-to-velpic"></a>Gebruikers toewijzen aan Velpic

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd. 

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Velpic-app. Nadat u hebt besloten, kunt u deze gebruikers toewijzen aan uw Velpic-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Belang rijke tips voor het toewijzen van gebruikers aan Velpic

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan Velpic om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan Velpic, moet u **de gebruikersrol** of een andere geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Houd er rekening mee dat de rol **standaard toegang** niet werkt voor inrichten en dat deze gebruikers worden overgeslagen.

## <a name="configuring-user-provisioning-to-velpic"></a>Gebruikers inrichten configureren voor Velpic

In deze sectie vindt u instructies voor het verbinden van uw Azure AD-Velpic en het configureren van de inrichtings service om toegewezen gebruikers accounts in Velpic te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor Velpic, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor Velpic in azure AD:

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

2. Als u Velpic al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van Velpic met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **Velpic** in de toepassings galerie. Selecteer Velpic in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

3. Selecteer uw exemplaar van Velpic en selecteer vervolgens het tabblad **inrichten** .

4. Stel de **inrichtings modus** in op **automatisch**.

    ![Velpic-inrichting](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Geef in het gedeelte **beheerders referenties** de **Tenant-URL op&geheime token** van Velpic. (U kunt deze waarden vinden onder uw Velpic-account:**Integration** > -**invoeg toepassing** >  **beheren** > **scim**)

    ![Autorisatie waarden](./media/velpic-provisioning-tutorial/Velpic2.png)

6. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Velpic-app. Als de verbinding mislukt, zorgt u ervoor dat uw Velpic-account beheerders machtigingen heeft en voert u stap 5 opnieuw uit.

7. Voer het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen in het veld **e-mail melding** en schakel het selectie vakje hieronder in.

8. Klik op **Opslaan**.

9. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met Velpic**.

10. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die worden gesynchroniseerd vanuit Azure AD naar Velpic. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om te voldoen aan de gebruikers accounts in Velpic voor bijwerk bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

11. Als u de Azure AD-inrichtings service voor Velpic wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte **instellingen**

12. Klik op **Opslaan**.

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan Velpic in de sectie gebruikers en groepen. Houd er rekening mee dat de initiële synchronisatie langer duurt dan volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar de inrichtings activiteiten rapporten te volgen. hierin worden alle acties beschreven die worden uitgevoerd door de inrichtings service.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)
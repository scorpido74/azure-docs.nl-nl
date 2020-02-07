---
title: 'Zelf studie: gebruikers inrichten voor LinkedIn-uitbrei ding-Azure AD'
description: Informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op LinkedIn-bevoegdheden.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa0a26eaeac431ed2c78c5bd938bbbe7dff14e0e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057410"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Zelf studie: LinkedIn-verhoging configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in LinkedIn-uitbrei ding en Azure AD om automatisch gebruikers accounts in te richten vanuit Azure AD naar LinkedIn.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een Azure Active Directory-tenant
* Een LinkedIn-Tenant met verhoogde bevoegdheid
* Een Administrator-account in LinkedIn-uitbrei ding met toegang tot het LinkedIn-account centrum

> [!NOTE]
> Azure Active Directory integreert met LinkedIn-verhoging met behulp van het [scim](http://www.simplecloud.info/) -protocol.

## <a name="assigning-users-to-linkedin-elevate"></a>Gebruikers toewijzen aan LinkedIn-verhoging

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot LinkedIn. Eenmaal besloten, kunt u deze gebruikers toewijzen aan LinkedIn-bevoegdheden door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Belang rijke tips voor het toewijzen van gebruikers aan LinkedIn-bevoegdheden

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan LinkedIn-bevoegdheden om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan LinkedIn-verhoging, moet **u de gebruikersrol** selecteren in het dialoog venster toewijzing. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Gebruikers inrichting configureren voor LinkedIn-verhoging

In deze sectie wordt u begeleid bij het verbinden van de inrichtings-API van uw Azure AD to LinkedIn-SCIM en het configureren van de inrichtings service om toegewezen gebruikers accounts te maken, bij te werken en uit te scha kelen in LinkedIn verhogen op basis van de gebruikers-en groeps toewijzing in azure AD.

**Tip:** U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding in te scha kelen voor een verhoging van het LinkedIn-niveau, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor LinkedIn-bevoegdheden in azure AD:

De eerste stap bestaat uit het ophalen van uw LinkedIn-toegangs token. Als u een Enter prise-beheerder bent, kunt u een toegangs token zelf inrichten. Ga in uw account centrum naar **instellingen &gt; globale instellingen** en open het **configuratie venster scim** .

> [!NOTE]
> Als u het account centrum direct opent in plaats van via een koppeling, kunt u het bereiken met behulp van de volgende stappen.

1. Meld u aan bij het account centrum.

2. Selecteer **beheerders &gt; beheerders instellingen** .

3. Klik op **Geavanceerde integraties** op de zijbalk links. U wordt omgeleid naar het account centrum.

4. Klik op **+ nieuwe scim-configuratie toevoegen** en volg de procedure door elk veld in te vullen.

    > [!NOTE]
    > Wanneer het automatisch toewijzen van licenties niet is ingeschakeld, betekent dit dat alleen gebruikers gegevens worden gesynchroniseerd.

    ![Inrichting voor LinkedIn-verhoging](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Wanneer de toewijzing van de licentie is ingeschakeld, moet u het toepassings exemplaar en het licentie type noteren. Licenties worden toegewezen op basis van de eerste die eerst worden uitgevoerd voordat alle licenties worden genomen.

    ![Inrichting voor LinkedIn-verhoging](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Klik op **token genereren**. U ziet de weer gave van het toegangs token onder het veld **toegangs token** .

6. Sla uw toegangs token op uw klem bord of computer op voordat u de pagina verlaat.

7. Meld u vervolgens aan bij de [Azure Portal](https://portal.azure.com)en blader naar de sectie **Azure Active Directory > enter prise apps > alle toepassingen** .

8. Als u LinkedIn-verhoging al hebt geconfigureerd voor eenmalige aanmelding, zoekt u met het zoek veld naar uw exemplaar van LinkedIn-verhoging. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **LinkedIn-verhoging** in de toepassings galerie. Selecteer een uitbrei ding op LinkedIn in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

9. Selecteer uw exemplaar van LinkedIn-uitbrei ding en selecteer vervolgens het tabblad **inrichten** .

10. Stel de **inrichtings modus** in op **automatisch**.

    ![Inrichting voor LinkedIn-verhoging](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Vul de volgende velden in onder **beheerders referenties** :

    * Voer `https://api.linkedin.com`in het veld **Tenant-URL** in.

    * In het veld **geheim token** voert u het toegangs token in dat u in stap 1 hebt gegenereerd en klikt u op **verbinding testen** .

    * U ziet een geslaagde melding aan de upperright-zijde van uw portal.

12. Voer het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen in het veld **e-mail melding** en schakel het selectie vakje hieronder in.

13. Klik op **Opslaan**.

14. Controleer in de sectie **kenmerk toewijzingen** de gebruikers-en groeps kenmerken die worden gesynchroniseerd vanuit Azure AD naar LinkedIn. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om te voldoen aan de gebruikers accounts en-groepen in LinkedIn-uitbrei ding voor update bewerkingen. Selecteer de knop Opslaan om door te voeren van eventuele wijzigingen.

    ![Inrichting voor LinkedIn-verhoging](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Als u de Azure AD-inrichtings service voor LinkedIn wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte **instellingen**

16. Klik op **Opslaan**.

Hiermee wordt de eerste synchronisatie gestart van gebruikers en/of groepen die zijn toegewezen aan LinkedIn-verhoging in de sectie gebruikers en groepen. Houd er rekening mee dat de initiële synchronisatie langer duurt dan volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen voor het inrichtings logboek, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtings service op uw LinkedIn-verhoogde app.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

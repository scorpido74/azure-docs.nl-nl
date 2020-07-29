---
title: 'Zelf studie: gebruikers inrichten-LinkedIn Sales Navigator, Azure AD'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op de LinkedIn-verkoop Navigator.
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
ms.openlocfilehash: 48b9f2dc64d1d3ddd8253a253dcab8ef972032f9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81869748"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Zelf studie: LinkedIn Sales Navigator configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in LinkedIn Sales Navigator en Azure AD om gebruikers accounts van Azure AD naar LinkedIn Sales Navigator automatisch in te richten en te deactiveren.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

* Een Azure Active Directory-tenant
* Een LinkedIn-Tenant voor verkoop Navigator 
* Een beheerders account in LinkedIn Sales Navigator met toegang tot het LinkedIn-account centrum

> [!NOTE]
> Azure Active Directory integreert met LinkedIn Sales Navigator met behulp van het [scim](http://www.simplecloud.info/) -protocol.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Gebruikers toewijzen aan de LinkedIn-verkoop Navigator

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot de LinkedIn-verkoop Navigator. Eenmaal besloten, kunt u deze gebruikers toewijzen aan de LinkedIn-verkoop Navigator door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Belang rijke tips voor het toewijzen van gebruikers aan de LinkedIn-verkoop Navigator

* Het is raadzaam dat één Azure AD-gebruiker wordt toegewezen aan de LinkedIn Sales Navigator om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan LinkedIn Sales Navigator, moet **u de gebruikersrol** selecteren in het dialoog venster toewijzing. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Gebruikers inrichten configureren voor de LinkedIn-verkoop Navigator

In deze sectie wordt u begeleid bij het verbinden van de inrichtings-API voor de SCIM-gebruikers account van Azure AD naar LinkedIn en het configureren van de inrichtings service om toegewezen gebruikers accounts te maken, bij te werken en uit te scha kelen in LinkedIn Sales Navigator op basis van de gebruikers-en groeps toewijzing in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding voor LinkedIn Sales navigator in te scha kelen, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Automatische toewijzing van gebruikers accounts configureren voor LinkedIn Sales navigator in azure AD:

De eerste stap bestaat uit het ophalen van uw LinkedIn-toegangs token. Als u een Enter prise-beheerder bent, kunt u een toegangs token zelf inrichten. Ga in uw account centrum naar **instellingen &gt; globale instellingen** en open het **configuratie venster scim** .

> [!NOTE]
> Als u het account centrum direct opent in plaats van via een koppeling, kunt u het bereiken met behulp van de volgende stappen.

1. Meld u aan bij het account centrum.

2. Selecteer **beheerders &gt; instellingen** voor de beheerder.

3. Klik op **Geavanceerde integraties** op de zijbalk links. U wordt omgeleid naar het account centrum.

4. Klik op **+ nieuwe scim-configuratie toevoegen** en volg de procedure door elk veld in te vullen.

    > [!NOTE]
    > Wanneer het automatisch toewijzen van licenties niet is ingeschakeld, betekent dit dat alleen gebruikers gegevens worden gesynchroniseerd.

    ![Het inrichten van de LinkedIn-verkoop Navigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Wanneer de toewijzing van de licentie is ingeschakeld, moet u het toepassings exemplaar en het licentie type noteren. Licenties worden toegewezen op basis van de eerste die eerst worden uitgevoerd voordat alle licenties worden genomen.

    ![Het inrichten van de LinkedIn-verkoop Navigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Klik op **token genereren**. U ziet de weer gave van het toegangs token onder het veld **toegangs token** .

6. Sla uw toegangs token op uw klem bord of computer op voordat u de pagina verlaat.

7. Meld u vervolgens aan bij de [Azure Portal](https://portal.azure.com)en blader naar de sectie **Azure Active Directory > enter prise apps > alle toepassingen** .

8. Als u de LinkedIn Sales Navigator al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw exemplaar van LinkedIn Sales Navigator met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **LinkedIn Sales Navigator** in de toepassings galerie. Selecteer LinkedIn Sales navigator in de zoek resultaten en voeg dit toe aan uw lijst met toepassingen.

9. Selecteer uw exemplaar van LinkedIn Sales Navigator en selecteer vervolgens het tabblad **inrichten** .

10. Stel de **inrichtings modus** in op **automatisch**.

    ![Het inrichten van de LinkedIn-verkoop Navigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Vul de volgende velden in onder **beheerders referenties** :

    * Voer in het veld **Tenant-URL** in https://developer.linkedin.com .

    * In het veld **geheim token** voert u het toegangs token in dat u in stap 1 hebt gegenereerd en klikt u op **verbinding testen** .

    * U ziet een geslaagde melding aan de upperright-zijde van uw portal.

12. Voer het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen in het veld **e-mail melding** en schakel het selectie vakje hieronder in.

13. Klik op **Opslaan**.

14. Controleer in de sectie **kenmerk toewijzingen** de gebruikers-en groeps kenmerken die worden gesynchroniseerd vanuit Azure AD naar LinkedIn Sales Navigator. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om te voldoen aan de gebruikers accounts en-groepen in de LinkedIn Sales Navigator voor bijwerk bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

    ![Het inrichten van de LinkedIn-verkoop Navigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Als u de Azure AD-inrichtings service voor LinkedIn Sales Navigator wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte **instellingen**

16. Klik op **Opslaan**.

Hiermee wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn toegewezen aan de LinkedIn Sales navigator in de sectie gebruikers en groepen. Houd er rekening mee dat de initiële synchronisatie langer duurt dan volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie gegevens** gebruiken voor het bewaken van de voortgang en het volgen van koppelingen naar de inrichtings activiteiten Logboeken. hierin worden alle acties beschreven die worden uitgevoerd door de inrichtings service in uw LinkedIn-verkoop Navigator-app.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

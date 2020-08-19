---
title: 'Zelf studie: MerchLogix configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op MerchLogix.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 6fe256660dc73aa411cf06e2b56ce9ef26934e2f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547993"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Zelf studie: MerchLogix configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het demonstreren van de stappen die moeten worden uitgevoerd in MerchLogix en Azure Active Directory (Azure AD) om Azure AD te configureren voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers en/of groepen in MerchLogix.

> [!NOTE]
> In deze zelf studie wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie [Gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md)voor belang rijke informatie over de werking van deze service, hoe deze werkt en veelgestelde vragen.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u al beschikt over de volgende vereisten:

* Een Azure AD-Tenant
* Een MerchLogix-Tenant
* Een technische contact persoon bij MerchLogix die de SCIM-eind punt-URL en het geheime token dat vereist is voor het inrichten van gebruikers, kunnen opgeven

## <a name="adding-merchlogix-from-the-gallery"></a>MerchLogix toevoegen uit de galerie

Voordat u MerchLogix configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u MerchLogix van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om MerchLogix toe te voegen vanuit de Azure AD-toepassings galerie:**

1. Klik in het **[Azure Portal](https://portal.azure.com)**, in het navigatie venster aan de linkerkant, op het pictogram **Azure Active Directory** . 

    ![De knop Azure Active Directory][1]

2. Ga naar **Enterprise applications**  >  **alle toepassingen**in bedrijfs toepassingen.

    ![De sectie bedrijfs toepassingen][2]

3. Als u MerchLogix wilt toevoegen, klikt u op de knop **nieuwe toepassing** aan de bovenkant van het dialoog venster.

    ![De knop Nieuwe toepassing][3]

4. Typ **MerchLogix**in het zoekvak.

5. Selecteer in het deel venster resultaten **MerchLogix**en klik vervolgens op de knop **toevoegen** om MerchLogix toe te voegen aan uw lijst met SaaS-toepassingen.

    ![MerchLogix-inrichting][4]

## <a name="assigning-users-to-merchlogix"></a>Gebruikers toewijzen aan MerchLogix

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers, worden alleen de gebruikers en/of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd. 

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u beslissen welke gebruikers en/of groepen in azure AD toegang nodig hebben tot MerchLogix. Eenmaal besloten, kunt u deze gebruikers en/of groepen toewijzen aan MerchLogix door de volgende instructies te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Belang rijke tips voor het toewijzen van gebruikers aan MerchLogix

* U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan MerchLogix om de initiële configuratie van de automatische gebruikers inrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen nadat de tests zijn voltooid.

* Wanneer u een gebruiker toewijst aan MerchLogix, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster toewijzing. Gebruikers met de rol **standaard toegang** worden uitgesloten van het inrichten.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Automatische gebruikers inrichting configureren voor MerchLogix 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers en/of groepen in MerchLogix te maken, bij te werken en uit te scha kelen op basis van gebruikers-en/of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor MerchLogix, gevolgd door de instructies in de [MerchLogix-zelf studie voor eenmalige aanmelding](merchlogix-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikers inrichting worden geconfigureerd, hoewel deze twee functies elkaar behoeven.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Automatische gebruikers inrichting configureren voor MerchLogix in azure AD:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en blader naar **Azure Active Directory > bedrijfs toepassingen > alle toepassingen**.

2. Selecteer MerchLogix in de lijst met SaaS-toepassingen.

3. Selecteer het tabblad **inrichten** .

4. Stel de **inrichtings modus** in op **automatisch**.

    ![MerchLogix-inrichting](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Klik onder de sectie **beheerders referenties** op:

    * In het veld **Tenant-URL** voert u de scim-eind punt-URL in die u hebt opgegeven door uw technische contact persoon van MerchLogix.

    * Voer in het veld **geheim token** een geheim token in dat is opgegeven door uw technische contact persoon van MerchLogix.

6. Klik bij het invullen van de velden die worden weer gegeven in stap 5 op **verbinding testen** om te controleren of Azure AD verbinding kan maken met MerchLogix. Als de verbinding mislukt, zorg er dan voor dat uw MerchLogix-account beheerders machtigingen heeft en probeer het opnieuw.

7. Voer in het veld **e-mail melding** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel het selectie vakje in om **een e-mail bericht te verzenden wanneer er een fout optreedt**.

8. Klik op **Opslaan**.

9. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory gebruikers synchroniseren met MerchLogix**.

10. Controleer de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar MerchLogix in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in MerchLogix voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

11. Selecteer in de sectie **toewijzingen** de optie **Azure Active Directory groepen synchroniseren met MerchLogix**.

12. Controleer de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar MerchLogix in de sectie **kenmerk toewijzing** . De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt om de groepen in MerchLogix te vergelijken voor bijwerk bewerkingen. Selecteer de knop **Opslaan** om eventuele wijzigingen door te voeren.

13. Als u de Azure AD-inrichtings service voor **MerchLogix wilt inschakelen, wijzigt u de** **inrichtings status** in in het gedeelte **instellingen** .

14. Wanneer u klaar bent om in te richten, klikt u op **Opslaan**.

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die in het **bereik** zijn gedefinieerd in de sectie **instellingen** . Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten optreden, zolang de Azure AD-inrichtings service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen naar het rapport inrichtings activiteiten te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op MerchLogix.

Zie [rapportage over het automatisch inrichten van gebruikers accounts](../app-provisioning/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png

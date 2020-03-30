---
title: 'Zelfstudie: MerchLogix configureren voor automatische gebruikersvoorziening met Azure Active Directory | Microsoft Documenten'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4aa60fb565552961a3c85346c39c318a90c8adc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061292"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Zelfstudie: MerchLogix configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om de stappen aan te tonen die moeten worden uitgevoerd in MerchLogix en Azure Active Directory (Azure AD) om Azure AD te configureren om gebruikers en/of groepen automatisch in te richten en te de-provisionen naar MerchLogix.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebouwd bovenop de Azure AD User Provisioning Service. Zie Gebruikersinrichting en deprovisioning voor SaaS-toepassingen automatiseren voor belangrijke details over wat deze service doet, hoe deze werkt en veelgestelde vragen, zie [Gebruikersinrichting automatiseren en deprovisioning voor SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende vereisten hebt:

* Een Azure AD-tenant
* Een MerchLogix huurder
* Een technisch contact bij MerchLogix die de SCIM-eindpunt-URL en het geheime token kan opgeven die nodig zijn voor het inrichten van gebruikers

## <a name="adding-merchlogix-from-the-gallery"></a>MerchLogix toevoegen vanuit de galerie

Voordat u MerchLogix configureert voor automatische gebruikersvoorziening met Azure AD, moet u MerchLogix uit de Azure AD-toepassingsgalerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

**Voer de volgende stappen uit om MerchLogix toe te voegen vanuit de Azure AD-toepassingsgalerie:**

1. Klik in de **[Azure-portal](https://portal.azure.com)** in het linkernavigatiedeelvenster op het Azure **Active Directory-pictogram.** 

    ![De knop Azure Active Directory][1]

2. Navigeer naar **Enterprise-toepassingen** > **Alle toepassingen**.

    ![De sectie Enterprise-toepassingen][2]

3. Als u MerchLogix wilt toevoegen, klikt u boven aan het dialoogvenster op de knop **Nieuwe toepassing.**

    ![De knop Nieuwe toepassing][3]

4. Typ **MerchLogix**in het zoekvak .

5. Selecteer **MerchLogix**in het resultatenpaneel en klik op de knop **Toevoegen** om MerchLogix toe te voegen aan uw lijst met SaaS-toepassingen.

    ![MerchLogix Provisioning][4]

## <a name="assigning-users-to-merchlogix"></a>Gebruikers toewijzen aan MerchLogix

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersinrichting worden alleen de gebruikers en/of groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd. 

Voordat u automatische gebruikersinrichting configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD toegang tot MerchLogix nodig hebben. Eenmaal besloten, u deze gebruikers en/of groepen toewijzen aan MerchLogix door de instructies hier te volgen:

* [Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Belangrijke tips voor het toewijzen van gebruikers aan MerchLogix

* Het wordt aanbevolen dat één Azure AD-gebruiker aan MerchLogix wordt toegewezen om uw initiële automatische configuratie van gebruikersinrichting te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen zodra de tests zijn geslaagd.

* Wanneer u een gebruiker aan MerchLogix toewijst, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het toewijzingsdialoogvenster. Gebruikers met de **functie Standaardtoegang** zijn uitgesloten van inrichten.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Automatische gebruikersvoorziening configureren voor MerchLogix 

In deze sectie u de Azure AD-inrichtingsservice configureren om gebruikers en/of groepen in MerchLogix te maken, bij te werken en uit te schakelen op basis van gebruikers- en/of groepstoewijzingen in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-on in te schakelen voor MerchLogix, volgens de instructies in de [MerchLogix single sign-on tutorial](merchlogix-tutorial.md). Eenmalige aanmelding kan onafhankelijk van automatische gebruikersinrichting worden geconfigureerd, hoewel deze twee functies elkaar complimenteren.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Ga als een te meer met de automatische gebruikersinrichting voor MerchLogix in Azure AD:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en blader naar **Azure Active Directory > Enterprise-toepassingen > Alle toepassingen**.

2. Selecteer MerchLogix in uw lijst met SaaS-toepassingen.

3. Selecteer het tabblad **Inrichten.**

4. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![MerchLogix Provisioning](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. Ga als onderdeel van de sectie **Beheerdersreferenties:**

    * Voer in het veld **URL van tenant** de URL van het SCIM-eindpunt in dat wordt verstrekt door uw technische contactpersoon van MerchLogix.

    * Voer in het veld **Secret Token** een geheim token in dat wordt verstrekt door uw technische contactpersoon van MerchLogix.

6. Klik op **Verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met MerchLogix wanneer u de velden in stap 5 bevolken. Als de verbinding mislukt, moet u ervoor zorgen dat uw MerchLogix-account beheerdersmachtigingen heeft en het opnieuw proberen.

7. Voer in het veld **Meldingse-e-mail** het e-mailadres in van een persoon of groep die de meldingen van provisioning-fouten moet ontvangen en schakel het selectievakje in - **Stuur een e-mailmelding wanneer er een fout optreedt**.

8. Klik op **Opslaan**.

9. Selecteer Azure **Active Directory-gebruikers synchroniseren met MerchLogix**onder de sectie **Toewijzingen** .

10. Controleer de gebruikerskenmerken die zijn gesynchroniseerd van Azure AD naar MerchLogix in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de gebruikersaccounts in MerchLogix te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

11. Selecteer Azure **Active Directory-groepen synchroniseren met MerchLogix**onder de sectie **Toewijzingen** .

12. Controleer de groepskenmerken die zijn gesynchroniseerd van Azure AD naar MerchLogix in de sectie **Kenmerktoewijzing.** De kenmerken die zijn geselecteerd als **Eigenschappen matching** worden gebruikt om de groepen in MerchLogix te matchen voor updatebewerkingen. Selecteer de knop **Opslaan** om wijzigingen door te voeren.

13. Als u de Azure AD-inrichtingsservice voor MerchLogix wilt inschakelen, wijzigt u de **ininrichtingsstatus** in **Aan** in de sectie **Instellingen.**

14. Wanneer u klaar bent voor inlevering, klikt u op **Opslaan.**

Met deze bewerking wordt de eerste synchronisatie gestart van alle gebruikers en/of groepen die zijn gedefinieerd in **Bereik** in de sectie **Instellingen.** De eerste synchronisatie duurt langer om uit te voeren dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de Azure AD-inrichtingsservice wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het installatieactiviteitenrapport te volgen, waarin alle acties worden beschreven die zijn uitgevoerd door de Azure AD-inrichtingsservice op MerchLogix.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het bekijken van logboeken en het verzamelen van rapporten over inrichtingsactiviteiten](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png

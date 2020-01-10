---
title: 'Zelf studie: een harnas configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van Azure Active Directory voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers accounts op de harnas.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 9d00024351c18789e26120cc2af006b9aac4232d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767837"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Zelf studie: harnas configureren voor automatische gebruikers inrichting

In dit artikel vindt u informatie over het configureren van Azure Active Directory (Azure AD) voor het automatisch inrichten en ongedaan maken van de inrichting van gebruikers of groepen.

> [!NOTE]
> In dit artikel wordt een connector beschreven die boven op de Azure AD User Provisioning-Service is gebouwd. Zie Gebruikers inrichten en de inrichting ongedaan maken voor [SaaS-toepassingen met Azure Active Directory](../manage-apps/user-provisioning.md)voor belang rijke informatie over deze service en antwoorden op veelgestelde vragen.
>
> Deze connector is momenteel in preview. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in dit artikel wordt beschreven, wordt ervan uitgegaan dat u de volgende vereisten al hebt:

* Een Azure AD-Tenant
* [Een harnas Tenant](https://harness.io/pricing/)
* Een gebruikers account in boom structuur met *beheerders* machtigingen

## <a name="assign-users-to-harness"></a>Gebruikers toewijzen aan harnas

Azure Active Directory gebruikt een concept met de naam *toewijzingen* om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers worden alleen de gebruikers of groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u automatische gebruikers inrichting configureert en inschakelt, moet u bepalen welke gebruikers of groepen in azure AD toegang nodig hebben tot harnas. U kunt deze gebruikers of groepen vervolgens toewijzen aan een harnas door de instructies in [een gebruiker of groep toewijzen aan een bedrijfs-app](../manage-apps/assign-user-or-group-access-portal.md)te volgen.

## <a name="important-tips-for-assigning-users-to-harness"></a>Belang rijke tips voor het toewijzen van gebruikers aan harnas

* We raden u aan één Azure AD-gebruiker toe te wijzen om de configuratie van automatische gebruikers inrichting te testen. Extra gebruikers of groepen kunnen later worden toegewezen.

* Wanneer u een gebruiker toewijst aan de harnas, moet u een geldige toepassingsspecifieke rol (indien beschikbaar) selecteren in het dialoog venster **toewijzing** . Gebruikers met de rol *standaard toegang* worden uitgesloten van het inrichten.

## <a name="set-up-harness-for-provisioning"></a>Harnas instellen voor inrichting

1. Meld u aan bij uw [harnas-beheer console](https://app.harness.io/#/login)en ga vervolgens naar **continue beveiliging** > **toegangs beheer**.

    ![Console voor harnas beheer](media/harness-provisioning-tutorial/admin.png)

1. Selecteer de **API-sleutels**.

    ![Koppeling van de API-sleutels voor harnas](media/harness-provisioning-tutorial/apikeys.png)

1. Selecteer **API-sleutel toevoegen**. 

    ![API-sleutel koppeling voor harnas toevoegen](media/harness-provisioning-tutorial/addkey.png)

1. Ga als volgt te werk in het deel venster **API-sleutel toevoegen** :

    ![Deel venster API-sleutel voor harnas toevoegen](media/harness-provisioning-tutorial/title.png)
   
   a. Geef in het vak **naam** een naam op voor de sleutel.  
   b. Selecteer een optie in de vervolg keuzelijst **met machtigingen die zijn overgenomen uit** . 
   
1. Selecteer **Indienen**.

1. Kopieer de **sleutel** voor later gebruik in deze zelf studie.

    ![Token voor het maken van harnas](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Harnas toevoegen vanuit de galerie

Voordat u een harnas configureert voor het automatisch inrichten van gebruikers met Azure AD, moet u een harnas van de Azure AD-toepassings galerie toevoegen aan uw lijst met beheerde SaaS-toepassingen.

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**.

    ![De knop Azure Active Directory](common/select-azuread.png)

1. Selecteer **Bedrijfstoepassingen** > **Alle toepassingen**.

    ![De koppeling alle toepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **nieuwe toepassing** boven aan het deel venster.

    ![De knop ' nieuwe toepassing '](common/add-new-app.png)

1. Voer in het zoekvak **harnas**in, Selecteer in de lijst met resultaten de optie **harnas** en selecteer vervolgens de knop **toevoegen** om de toepassing toe te voegen.

    ![Harnas in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Automatische gebruikers inrichting configureren voor harnas 

In deze sectie wordt u begeleid bij de stappen voor het configureren van de Azure AD-inrichtings service om gebruikers of groepen in harnas te maken, bij te werken en uit te scha kelen op basis van gebruikers-of groeps toewijzingen in azure AD.

> [!TIP]
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor harnas door de instructies in de [zelf studie eenmalige aanmelding](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)te volgen. U kunt eenmalige aanmelding configureren, onafhankelijk van het automatisch inrichten van gebruikers, hoewel deze twee functies elkaar aanvullen.

> [!NOTE]
> Zie het artikel over de harnas [API-sleutels](https://docs.harness.io/article/smloyragsm-api-keys) voor meer informatie over het scim-eind punt.

Ga als volgt te werk om het automatisch inrichten van gebruikers voor harnas in azure AD te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com) **bedrijfs toepassingen** > **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **harnas**.

    ![De harnas koppeling in de lijst met toepassingen](common/all-applications.png)

1. Selecteer **inrichting**.

    ![De inrichtings knop](common/provisioning.png)

1. Selecteer in de vervolg keuzelijst **inrichtings modus** de optie **automatisch**.

    ![De vervolg keuzelijst ' inrichtings modus '](common/provisioning-automatic.png)

1. Ga als volgt te werk onder **beheerders referenties**:

    ![Tenant-URL + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Voer **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** in het vak **Tenant-URL** in.  
   b. Voer in het vak **geheim token** de waarde voor het scim-verificatie token in dat u hebt opgeslagen in stap 6 van de sectie ' harnas instellen voor het inrichten '.  
   c. Selecteer **verbinding testen** om te controleren of Azure AD verbinding kan maken met de harnas. Als de verbinding mislukt, zorg er dan voor dat uw harnas account *beheerders* machtigingen heeft en probeer het opnieuw.

1. Voer in het vak **e-mail bericht** het e-mail adres in van een persoon of groep die de inrichtings fout meldingen moet ontvangen en schakel vervolgens het selectie vakje **e-mail melding verzenden wanneer een fout optreedt** in.

    ![Het vak ' e-mail bericht '](common/provisioning-notification-email.png)

1. Selecteer **Opslaan**.

1. Selecteer onder **toewijzingen**de optie **synchronisatie Azure Active Directory gebruikers om te profiteren**van.

    ![De koppeling ' synchronisatie van Azure Active Directory gebruikers naar een harnas ' harnas](media/harness-provisioning-tutorial/usermappings.png)

1. Controleer onder **kenmerk toewijzingen**de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de harnas. De kenmerken die zijn geselecteerd als *match* , worden gebruikt voor de gebruikers accounts in combi natie met update bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    ![Deel venster voor het toewijzen van kenmerken aan gebruikers](media/harness-provisioning-tutorial/userattributes.png)

1. Selecteer onder **toewijzingen**de optie **Azure Active Directory groepen synchroniseren om te profiteren**.

    ![De koppeling voor het synchroniseren van Azure Active Directory groepen op elkaar bundelen](media/harness-provisioning-tutorial/groupmappings.png)

1. Controleer onder **kenmerk toewijzingen**de groeps kenmerken die zijn gesynchroniseerd vanuit Azure AD naar de harnas. De kenmerken die zijn geselecteerd als *overeenkomende* eigenschappen worden gebruikt om de groepen in harnas te vergelijken voor bijwerk bewerkingen. Selecteer **Opslaan** om eventuele wijzigingen door te voeren.

    ![Deel venster kenmerk toewijzingen van harnas groep](media/harness-provisioning-tutorial/groupattributes.png)

1. Zie op [kenmerken gebaseerde toepassing inrichten met bereik filters](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md)voor het configureren van bereik filters.

1. Onder **instellingen**, om de Azure AD-inrichtings service voor harnas in te scha kelen, schakelt u de **inrichtings status** in **op aan**.

    ![De inrichtings status switch is in-of uitgeschakeld](common/provisioning-toggle-on.png)

1. Selecteer onder **instellingen**in de vervolg keuzelijst **bereik** hoe u de gebruikers of groepen die u wilt inrichten, wilt synchroniseren.

    ![Inrichtings bereik](common/provisioning-scope.png)

1. Wanneer u klaar bent om in te richten, selecteert u **Opslaan**.

    ![De knop Opslaan inrichten](common/provisioning-configuration-save.png)

Met deze bewerking wordt de eerste synchronisatie gestart van de gebruikers of groepen die u wilt inrichten. Het duurt langer voordat de initiële synchronisatie is uitgevoerd. Synchronisaties vinden ongeveer elke 40 minuten plaats, mits de Azure AD-inrichtings service wordt uitgevoerd. Als u de voortgang wilt bewaken, gaat u naar de sectie **synchronisatie Details** . U kunt ook koppelingen volgen naar een rapport met inrichtings activiteiten, waarin alle acties worden beschreven die worden uitgevoerd door de Azure AD Provisioning-Service op harnas.

Zie [rapport over automatische toewijzing van gebruikers accounts](../manage-apps/check-status-user-account-provisioning.md)voor meer informatie over het lezen van de Azure AD-inrichtings Logboeken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Het inrichten van gebruikers accounts beheren voor zakelijke apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het controleren van Logboeken en het ophalen van rapporten over de inrichtings activiteit](../manage-apps/check-status-user-account-provisioning.md)

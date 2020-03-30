---
title: 'Zelfstudie: Azure Active Directory-integratie met Amazon Web Services (AWS) om meerdere accounts te verbinden | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure AD en meerdere accounts van Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: edd54352b1328c95ae2c3e466003b64eaa0fcfde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367994"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Zelfstudie: Azure Active Directory-integratie met meerdere AWS-accounts (Amazon Web Services)

In deze zelfstudie leert u hoe u Azure Active Directory (Azure AD) integreert met meerdere accounts van Amazon Web Services (AWS).

De integratie van Amazon Web Services (AWS) met Azure AD heeft de volgende voordelen:

- U kunt in Azure AD beheren wie toegang heeft tot Amazon Web Services (AWS).
- U uw gebruikers in staat stellen om automatisch aangemeld te worden bij Amazon Web Services (AWS) (Single Sign-On) met hun Azure AD-accounts.
- U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Als u meer informatie wilt over de integratie van De SaaS-app met Azure AD, raadpleegt u [wat toepassingstoegang en eenmalige aanmelding is met Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) in de resultatenlijst](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Let op: het koppelen van één AWS-app aan al uw AWS-accounts is niet onze aanbevolen aanpak. In plaats daarvan raden we u aan [deze](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) aanpak te gebruiken om meerdere exemplaren van AWS-account te configureren naar meerdere exemplaren van AWS-apps in Azure AD.

**Houd er rekening mee dat we deze aanpak niet om de volgende redenen moeten gebruiken:**

* U moet de Microsoft Graph Explorer-benadering gebruiken om alle rollen naar de app te patchen. We raden het gebruik van de manifestbestandsbenadering af.

* We hebben gezien klanten melden dat na het toevoegen van ~ 1200 app rollen voor een enkele AWS app, elke operatie op de app begonnen met het gooien van de fouten met betrekking tot grootte. Er is een harde limiet van grootte op het toepassingsobject.

* Je moet handmatig bijwerken van de rol als de rollen worden toegevoegd in een van de rekeningen, dat is een aanpak Vervangen en niet toevoegen helaas. Ook als uw accounts groeien dan wordt dit n x n relatie met accounts en rollen.

* Alle AWS-accounts gebruiken hetzelfde XML-bestand met Federatiemetadata en op het moment van de rollover van certificaten moet u deze enorme oefening aansturen om het certificaat op alle AWS-accounts tegelijkertijd bij te werken

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met Amazon Web Services (AWS):

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een Amazon Web Services (AWS) abonnement met eenmalige aanmelding ingeschakeld

> [!NOTE]
> Als u de stappen in deze zelfstudie wilt testen, is het raadzaam om niet de productieomgeving te gebruiken.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Amazon Web Services (AWS) ondersteunt door **SP en IDP** gestarte SSO

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) toevoegen uit de galerie

Om de integratie van Amazon Web Services (AWS) met Azure AD te configureren, moet u Amazon Web Services (AWS) vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Amazon Web Services (AWS) toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Amazon Web Services (AWS)** in het zoekvak, selecteer **Amazon Web Services (AWS)** in het resultatenvenster, en klik op **Toevoegen ** om de toepassing toe te voegen.

     ![Amazon Web Services (AWS) in de resultatenlijst](common/search-new-app.png)

5. Zodra de toepassing is toegevoegd, gaat u naar de pagina **Eigenschappen** en kopieert u de **object-id**.

    ![Amazon Web Services (AWS) in de resultatenlijst](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Amazon Web Services (AWS) op basis van een testgebruiker genaamd "Britta Simon".

Voor eenmalige aanmelding aan het werk moet Azure AD weten wat de tegenhangergebruiker in Amazon Web Services (AWS) is voor een gebruiker in Azure AD. Met andere woorden, er moet een koppeling worden gemaakt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Amazon Web Services (AWS).

Wijs in Amazon Web Services (AWS) de waarde van de **gebruikersnaam** in Azure AD toe als de waarde van de **gebruikersnaam** om de koppelingsrelatie vast te stellen.

U moet de volgende bouwstenen voltooien om Azure AD Single Sign-On met Amazon Web Services (AWS) te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Amazon Web Services (AWS) configureren](#configure-amazon-web-services-aws-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u Azure AD single sign-on in de Azure-portal in en configureert u eenmalige aanmelding in uw Aws-toepassing (Amazon Web Services).

**Voer de volgende stappen uit om eenmalige aanmelding met Azure AD te configureren voor Amazon Web Services (AWS):**

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **Amazon Web Services (AWS)** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![installatiekopie](common/preintegrated.png)

5. Amazon Web Services (AWS) verwacht de SAML-asserties in een specifieke notatie. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken en claims** op de integratiepagina van de toepassing-beheren. Klik op de pagina **Eén aanmelding instellen met SAML** op Knop **Bewerken** om **gebruikerskenmerken & het** dialoogvenster Claims te openen.

    ![installatiekopie](common/edit-attribute.png)

6. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    | Name  | Bronkenmerk  | Naamruimte |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rol            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "Geef een waarde op tussen 900 seconden (15 minuten) en 43200 seconden (12 uur)" |  https://aws.amazon.com/SAML/Attributes |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](common/new-save-attribute.png)

    ![installatiekopie](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Typ in het tekstvak **Naamruimte** de waarde Naamruimte die voor die rij wordt weergegeven.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

7. Klik op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** op **Downloaden** om de XML **met federatiemetagegevens** te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Eenmalige aanmelding bij Amazon Web Services (AWS) configureren

1. Meld u in een ander browservenster aan als beheerder bij uw Amazon Web Services (AWS) bedrijfssite.

2. Klik op **AWS Home**.

    ![Startpagina voor configureren van eenmalige aanmelding][11]

3. Klik op **Identity and Access Management**.

    ![Identiteit voor eenmalige aanmelding configureren][12]

4. Klik op **Identity Providers** en vervolgens op **Create Provider**.

    ![Provider voor eenmalige aanmelding configureren][13]

5. Voer op de dialoogpagina **Configure Provider** de volgende stappen uit:

    ![Dialoogvenster Configure Single Sign-On][14]

    a. Selecteer **SAML** als **Provider Type**.

    b. Typ in het tekstvak **Providernaam** een providernaam *(bijvoorbeeld: WAAD*).

    c. Klik op **Choose File** om het uit de Azure-portal gedownloade **metagegevensbestand** te uploaden.

    d. Klik op **Next Step**.

6. Klik op de dialoogpagina **Verify Provider Information** op **Create**.

    ![Verificatie voor het configureren van eenmalige aanmelding][15]

7. Klik op **Roles** en vervolgens op **Create role**.

    ![Rollen voor eenmalige aanmelding configureren][16]

8. Voer op de pagina **Create role** de volgende stappen uit:  

    ![Vertrouwensrelatie voor eenmalige aanmelding configureren][19]

    a. Selecteer **SAML 2.0 federation** onder **Select type of trusted entity**.

    b. Selecteer **onder Een SAML 2.0-providersectie kiezen,** selecteer de **SAML-provider** die u eerder hebt gemaakt (bijvoorbeeld: *WAAD*)

    c. Selecteer **Allow programmatic and AWS Management Console access**.
  
    d. Klik **op Volgende: Machtigingen**.

9. Voeg in het dialoogvenster **Attach Permissions Policies** het juiste beleid voor uw organisatie toe. Klik **op Volgende: Controleren**.  

    ![Beleid voor eenmalige aanmelding configureren][33]

10. Voer in het dialoogvenster **Review** de volgende stappen uit:

    ![Configuratie van eenmalige aanmelding controleren][34]

    a. Voer in het tekstvak **Role name** de naam van uw rol in.

    b. Voer in het tekstvak **Role description** de omschrijving van de rol in.

    c. Klik op **Create Role**.

    d. Maak zoveel rollen als nodig is en wijs ze toe aan de id-provider.

11. Meld u af bij het huidige AWS-account en log in bij een ander account waar u eenmalige aanmelding wilt configureren bij Azure AD.

12. Voer stap-2 uit naar stap-10 om meerdere rollen te maken die u voor dit account wilt instellen. Als je meer dan twee accounts hebt, voer je dezelfde stappen uit voor alle accounts om rollen voor ze te maken.

13. Zodra alle rollen in de accounts zijn gemaakt, worden ze weergegeven in de lijst **Rollen** voor die accounts.

    ![Rollen instellen](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. We moeten alle rol ARN en Vertrouwde entiteiten vastleggen voor alle rollen in alle accounts, die we handmatig moeten toewijzen met Azure AD-toepassing.

15. Klik op de rollen om de waarden **rol ARN** en **Vertrouwde entiteiten te** kopiëren. U hebt deze waarden nodig voor alle rollen die u moet maken in Azure AD.

    ![Rollen instellen](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Voer de bovenstaande stap uit voor alle rollen in alle accounts en sla ze allemaal op in indeling **Rol ARN, Vertrouwde entiteiten** in een notitieblok.

17. Open [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) in een ander venster.

    a. Meld u aan bij de site Graph Explorer met de referenties globale beheerders/medebeheerders voor uw tenant.

    b. U moet voldoende machtigingen hebben om de rollen te maken. Klik op **machtigingen wijzigen** om de vereiste machtigingen te krijgen.

    ![Dialoogvenster Grafiekverkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Selecteer de volgende machtigingen in de lijst (als u deze nog niet hebt) en klik op 'Machtigingen wijzigen'. 

    ![Dialoogvenster Grafiekverkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Dit zal u vragen om opnieuw in te loggen en de toestemming te accepteren. Nadat u de toestemming hebt geaccepteerd, bent u opnieuw aangemeld bij de Grafiekverkenner.

    e. Wijzig de vervolgkeuzelijst van de versie in **bèta.** Als u alle serviceprincipals uit uw tenant wilt ophalen, gebruikt u de volgende query:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Als u meerdere mappen gebruikt, u het volgende patroon gebruiken, dat uw primaire domein in het`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialoogvenster Grafiekverkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Haal in de lijst met serviceprincipals degene die u moet wijzigen. U de Ctrl+F ook gebruiken om de toepassing te doorzoeken vanuit alle vermelde ServicePrincipals. U de volgende query gebruiken met behulp van de **object-id** die u hebt gekopieerd van de pagina Azure AD-eigenschappen om naar de desbetreffende serviceprincipal te gaan.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialoogvenster Grafiekverkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Haal de eigenschap appRoles uit het hoofdobject van de service.

    ![Dialoogvenster Grafiekverkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. U moet nu nieuwe rollen genereren voor uw toepassing. 

    i. Hieronder JSON is een voorbeeld van appRoles object. Maak een vergelijkbaar object om de gewenste rollen voor uw toepassing toe te voegen.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > U alleen nieuwe rollen toevoegen nadat de **msiam_access** voor de patchbewerking. U ook zoveel rollen toevoegen als u wilt per uw organisatiebehoefte. Azure AD verzendt de **waarde** van deze rollen als claimwaarde in SAML-respons.

    j. Ga terug naar Microsoft Graph Explorer en wijzig de methode van **GET** naar **PATCH.** Patch het object Service principal om gewenste rollen te hebben door de eigenschap appRollen bij te werken, vergelijkbaar met het object dat hierboven in het voorbeeld wordt weergegeven. Klik **op Query uitvoeren** om de patchbewerking uit te voeren. Een succesbericht bevestigt de creatie van de rol voor uw Amazon Web Services-toepassing.

    ![Dialoogvenster Microsoft Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. Nadat de serviceprincipal is gepatcht met meer rollen, u gebruikers/groepen toewijzen aan de desbetreffende rollen. Dit kan worden gedaan door naar portal en navigeren naar de Amazon Web Services applicatie. Klik bovenaan op het tabblad **Gebruikers en groepen.**

19. We raden u aan om nieuwe groepen te maken voor elke AWS-rol, zodat u die specifieke rol in die groep toewijzen. Houd er rekening mee dat dit één op één toewijzing is voor één groep op één rol. U vervolgens de leden toevoegen die tot die groep behoren.

20. Zodra de groepen zijn gemaakt, selecteert u de groep en wijst u de toepassing toe.

    ![Voeg toevoegen bij één aanmelding configureren](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Geneste groepen worden niet ondersteund bij het toewijzen van groepen.

21. Als u de rol aan de groep wilt toewijzen, selecteert u de rol en klikt u onder aan de pagina op **Toewijzen.**

    ![Voeg toevoegen bij één aanmelding configureren](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Houd er rekening mee dat u uw sessie in de Azure-portal moet vernieuwen om nieuwe rollen te kunnen zien.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Amazon Web Services (AWS) klikt in het Access Panel, moet u de toepassingpagina van Amazon Web Services (AWS) met optie krijgen om de rol te selecteren.

![Voeg toevoegen bij één aanmelding configureren](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

U ook het SAML-antwoord verifiëren om te zien dat de rollen worden doorgegeven als claims.

![Voeg toevoegen bij één aanmelding configureren](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Zie [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichten configureren met Microsoft Graph API's](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/

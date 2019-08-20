---
title: 'Zelfstudie: Azure Active Directory-integratie met Amazon Web Services (AWS) | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 672a3571202b92232bd45a42254a43019f6a9796
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617334"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Zelfstudie: Amazon Web Services (AWS) integreren met Azure Active Directory

In deze zelf studie leert u hoe u Amazon Web Services (AWS) integreert met Azure Active Directory (Azure AD). Wanneer u AWS integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot AWS.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij AWS met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie, het Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

![Diagram van Azure AD en AWS-relatie](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

U kunt meerdere id's voor meerdere exemplaren configureren. Bijvoorbeeld:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Met deze waarden verwijdert Azure AD de waarde van **#** en verzendt de juiste waarde `https://signin.aws.amazon.com/saml` als de URL van de doel groep in het SAML-token.

We raden deze methode aan om de volgende redenen:

- Elke toepassing biedt u een uniek x509-certificaat. Elk exemplaar van een AWS-app-exemplaar kan vervolgens een andere verloop datum voor het certificaat hebben, die kan worden beheerd op basis van een afzonderlijk AWS-account. De algehele rollover van het certificaat is in dit geval gemakkelijker.

- U kunt het inrichten van gebruikers met een AWS-app in azure AD inschakelen, en vervolgens worden alle rollen uit dat AWS-account opgehaald. U hoeft de AWS-rollen in de app niet handmatig toe te voegen of bij te werken.

- U kunt de app-eigenaar voor de app afzonderlijk toewijzen. Deze persoon kan de app rechtstreeks in azure AD beheren.

> [!Note]
> Zorg ervoor dat u alleen een galerie toepassing gebruikt.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een AWS-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. AWS ondersteunt door **SP en IDP** geïnitieerde SSO.

## <a name="add-aws-from-the-gallery"></a>AWS toevoegen vanuit de galerie

Als u de integratie van AWS in azure AD wilt configureren, moet u AWS uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het linkerdeel venster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **Amazon Web Services (AWS)** in het zoekvak.
1. Selecteer **Amazon Web Services (AWS)** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configureren en Azure AD eenmalige aanmelding testen

Azure AD SSO met AWS configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AWS.

Als u Azure AD SSO wilt configureren en testen met AWS, voltooit u de volgende bouw stenen:

1. **Configureer Azure AD SSO** zodat uw gebruikers deze functie kunnen gebruiken.
2. **CONFIGUREER AWS** om de SSO-instellingen aan de kant van de toepassing te configureren.
3. **Maak een Azure AD-test gebruiker** om Azure AD SSO te testen met B. Simon.
4. **Wijs de gebruiker van Azure AD-test** toe om B. Simon in te scha kelen voor gebruik met Azure AD SSO.
5. **Maak een AWS-test gebruiker** die een equivalent van B. Simon heeft in AWS die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **Test SSO** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Amazon Web Services (AWS)** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Op de pagina **eenmalige aanmelding met SAML instellen** selecteert u het pictogram voor de pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Scherm afbeelding van het instellen van eenmalige aanmelding met de SAML-pagina, met het pictogram van een pen gemarkeerd](common/edit-urls.png)

4. In de sectie **basis configuratie van SAML** is de toepassing vooraf geconfigureerd en zijn de benodigde url's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door **Opslaan**te selecteren.

5. Als u meer dan één exemplaar configureert, geeft u een id-waarde op. Gebruik vanaf tweede exemplaar de volgende indeling, inclusief een **#** teken om een unieke SPN-waarde op te geven.

    `https://signin.aws.amazon.com/saml#2`

6. De AWS-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven. Selecteer het pictogram pen om het dialoog venster gebruikers kenmerken te openen.

    ![Scherm afbeelding van gebruikers kenmerken in het dialoog venster met het pictogram van de pen gemarkeerd](common/edit-attribute.png)

7. Naast de voor gaande kenmerken verwacht de AWS-toepassing nog enkele kenmerken die in het SAML-antwoord weer worden door gegeven. In het gedeelte **gebruikers claims** van het dialoog venster **gebruikers kenmerken** voert u de volgende stappen uit om het SAML-token kenmerk toe te voegen.

    | Name  | Bronkenmerk  | Naamruimte |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "Geef een waarde op tussen 900 seconden (15 minuten) en 43200 seconden (12 uur)" |  https://aws.amazon.com/SAML/Attributes |

    a. Selecteer **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![Scherm afbeelding van de sectie gebruikers claims met nieuwe claim toevoegen en gemarkeerd opslaan](common/new-save-attribute.png)

    ![Scherm afbeelding van het dialoog venster gebruikers claims beheren](common/new-attribute-details.png)

    b. In **naam**, typ de naam van het kenmerk dat voor die rij wordt weer gegeven.

    c. In **naam ruimte**typt u de waarde van de naam ruimte die voor die rij wordt weer gegeven.

    d. Selecteer in **bron** **kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Selecteer **OK**.

    g. Selecteer **Opslaan**.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML**. Selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![Scherm afbeelding van het certificaat van het SAML-ondertekening, met de markering gedownload](common/metadataxml.png)

1. Kopieer in het gedeelte **set up Amazon Web Services (AWS)** de juiste url's, op basis van uw vereiste.

   ![Scherm opname van de sectie Amazon Web Services instellen (AWS) met configuratie-Url's gemarkeerd](common/copy-configuration-urls.png)

### <a name="configure-aws"></a>AWS configureren

1. Meld u in een ander browser venster aan bij uw AWS-bedrijfs site als beheerder.

2. Selecteer **AWS Home**.

    ![Scherm opname van AWS bedrijfs site, met AWS start pictogram gemarkeerd][11]

3. Selecteer **identiteits-en toegangs beheer**.

    ![Scherm opname van de pagina AWS Services, met IAM gemarkeerd][12]

4. Selecteer **id-providers** > **maken provider**.

    ![Scherm afbeelding van IAM-pagina met id-providers en gemaakte provider gemarkeerd][13]

5. Voer de volgende stappen uit op de pagina **provider configureren** :

    ![Scherm opname van provider configureren][14]

    a. Selecteer voor **provider type**de optie **SAML**.

    b. Typ bij **provider naam**een provider naam (bijvoorbeeld: *WAAD*).

    c. Als u het gedownloade **META gegevensbestand** wilt uploaden uit de Azure Portal, selecteert u **bestand kiezen**.

    d. Selecteer **volgende stap**.

6. Selecteer op de pagina **provider gegevens verifiëren** de optie **maken**.

    ![Scherm opname van provider gegevens controleren, met gemarkeerd maken][15]

7. Selecteer **rollen** > **maken rol**.

    ![Scherm afbeelding van de pagina rollen][16]

8. Voer op de pagina **Create role** de volgende stappen uit:  

    ![Scherm afbeelding van de pagina voor het maken van een rol][19]

    a. Selecteer onder **type vertrouwde entiteit selecteren**de optie **SAML 2,0-Federatie**.

    b. Selecteer onder **een saml 2,0-provider kiezen**de **SAML-provider** die u eerder hebt gemaakt (bijvoorbeeld: *WAAD*).

    c. Selecteer **Allow programmatic and AWS Management Console access**.
  
    d. Selecteer **Volgende: Permissions**.

9. In het dialoog venster **machtigings beleid koppelen** voegt u het juiste beleid toe, afhankelijk van uw organisatie. Selecteer **volgende: Review**.  

    ![Scherm afbeelding van het dialoog venster machtigings beleid koppelen][33]

10. Voer in het dialoog venster **controleren** de volgende stappen uit:

    ![Scherm afbeelding van het dialoog venster controleren][34]

    a. Voer in **rolnaam**de naam van uw rol in.

    b. In **Beschrijving van rol**voert u de beschrijving in.

    c. Selecteer **rol maken**.

    d. Maak zoveel rollen als nodig en wijs ze toe aan de ID-provider.

11. Gebruik de referenties van het AWS-service account voor het ophalen van de functies uit het AWS-account in azure AD-gebruikers inrichting. Open hiervoor de startpagina van de AWS-console.

12. Selecteer **Services**. Selecteer onder **beveiliging, identiteit & naleving**de optie **iam**.

    ![Scherm opname van de start pagina van de AWS-console, met Services en IAM gemarkeerd](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Selecteer in de sectie IAM de optie **beleids regels**.

    ![Scherm afbeelding van IAM sectie met beleids regels gemarkeerd](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Maak een nieuw beleid door **beleid maken** te selecteren voor het ophalen van de functies van het AWS-account in azure AD-gebruikers inrichting.

    ![Scherm afbeelding van de pagina rol maken, met de instelling beleid maken gemarkeerd](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Maak uw eigen beleid om alle rollen op te halen uit AWS-accounts.

    ![Scherm afbeelding van de pagina beleid maken, met gemarkeerde JSON](./media/amazon-web-service-tutorial/policy1.png)

    a. Selecteer in **beleid maken**het tabblad **JSON** .

    b. Voeg in het beleids document de volgende JSON toe:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Selecteer **controle beleid** om het beleid te valideren.

    ![Scherm afbeelding van de pagina beleid maken](./media/amazon-web-service-tutorial/policy5.png)

16. Definieer het nieuwe beleid.

    ![Scherm afbeelding van de pagina beleid maken, waarin de velden naam en beschrijving zijn gemarkeerd](./media/amazon-web-service-tutorial/policy2.png)

    a. Voer bij **naam** **AzureAD_SSOUserRole_Policy**in.

    b. Voer voor **Beschrijving** **dit beleid in om de functies van AWS-accounts op te halen**.

    c. Selecteer **beleid maken**.

17. Maak een nieuw gebruikers account in de AWS IAM-service.

    a. Selecteer in de AWS IAM-console de optie **gebruikers**.

    ![Scherm opname van de AWS IAM-console, met gemarkeerde gebruikers](./media/amazon-web-service-tutorial/policy3.png)

    b. Selecteer **gebruiker toevoegen**om een nieuwe gebruiker te maken.

    ![Scherm afbeelding van de knop gebruiker toevoegen](./media/amazon-web-service-tutorial/policy4.png)

    c. In de sectie **gebruiker toevoegen** :

    ![Scherm afbeelding van de pagina gebruiker toevoegen, met de gebruikers naam en het toegangs type gemarkeerd](./media/amazon-web-service-tutorial/adduser1.png)

    * Voer de naam van de gebruiker in als **AzureADRoleManager**.

    * Selecteer **programmatische toegang**voor het toegangs type. Op deze manier kan de gebruiker de Api's aanroepen en de rollen ophalen van het AWS-account.

    * Selecteer **volgende machtigingen**.

18. Maak een nieuw beleid voor deze gebruiker.

    ![Scherm opname van gebruiker toevoegen](./media/amazon-web-service-tutorial/adduser2.png)

    a. Selecteer **bestaande beleids regels rechtstreeks koppelen**.

    b. Zoek het zojuist gemaakte beleid op in de filtersectie **AzureAD_SSOUserRole_Policy**.

    c. Selecteer het beleid en selecteer **vervolgens volgende: Review**.

19. Controleer het beleid op de gekoppelde gebruiker.

    ![Scherm afbeelding van de pagina gebruiker toevoegen met gemarkeerd door gebruiker maken](./media/amazon-web-service-tutorial/adduser3.png)

    a. Controleer de gebruikersnaam, het toegangstype en het beleid dat is toegewezen aan de gebruiker.

    b. Selecteer **gebruiker maken**.

20. De gebruikers referenties van een gebruiker downloaden.

    ![Scherm opname van gebruiker toevoegen](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopieer de **Access key ID** en **Secret access key** van de gebruiker.

    b. Voer deze referenties in het gedeelte Azure AD-gebruikers inrichten in om de rollen op te halen uit de AWS-console.

    c. Selecteer **Sluiten**.

21. Ga in de Azure AD-beheer Portal naar **inrichten**in de AWS-app.

    ![Scherm opname van de AWS-app, waarbij de inrichting is gemarkeerd](./media/amazon-web-service-tutorial/provisioning.png)

22. Voer respectievelijk de toegangs sleutel en het geheim in de velden **clientsecret** en **geheim-token** in.

    ![Scherm afbeelding van het dialoog venster beheerders referenties](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Voer de gebruikerstoegangssleutel van AWS in het veld **Clientgeheim** in.

    b. Voer het gebruikersgeheim van AWS in het veld **Token voor geheim** in.

    c. Selecteer **verbinding testen**.

    d. Sla de instelling op door **Opslaan**te selecteren.

23. Selecteer **aan**in de sectie **instellingen** voor de inrichtings **status**. Selecteer vervolgens **Opslaan**.

    ![Scherm afbeelding van de sectie instellingen, met bij gemarkeerd](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker, B. Simon, in de Azure Portal.

1. Selecteer **Azure Active Directory** > gebruikersvan > **alle gebruikers**in het linkerdeel venster van de Azure Portal.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   
   a. Voer in het veld **Naam** `B.Simon` in.  
   b. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.   
   c. Selecteer **wacht woord weer geven**en schrijf dit op. Ten slotte selecteert u **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om Azure SSO te gebruiken door toegang te verlenen aan AWS.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Amazon Web Services (AWS)** in de lijst toepassingen.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Scherm opname van de sectie beheren met gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Scherm opname van gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** de optie **B. Simon**. Kies vervolgens **selecteren**.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **selecteren**.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Wanneer u de tegel AWS in het toegangs venster selecteert, wordt u automatisch aangemeld bij de AWS waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="known-issues"></a>Bekende problemen

 * De Subsectie **toewijzingen** in de sectie **inrichten** bevat een ' laden... ' en worden nooit de kenmerk toewijzingen weer gegeven. De enige werk stroom voor inrichting die nu wordt ondersteund, is het importeren van functies uit AWS in azure AD voor selectie tijdens een gebruikers-of groeps toewijzing. De kenmerk toewijzingen voor dit zijn vooraf bepaald en kunnen niet worden geconfigureerd.
 
 * De sectie **Inrichten** ondersteunt alleen de invoer van één set referenties per AWS-tenant tegelijk. Alle geïmporteerde rollen worden geschreven naar de `appRoles` eigenschap van het Azure AD [ `servicePrincipal` -object](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) voor de AWS-Tenant. 
 
   Meerdere AWS-tenants (vertegenwoordigd `servicePrincipals`door) kunnen worden toegevoegd aan Azure AD vanuit de galerie voor het inrichten van. Er is echter een bekend probleem waarbij niet alle geïmporteerde rollen automatisch kunnen worden geschreven van de meerdere AWS `servicePrincipals` die worden gebruikt voor het inrichten van de enkelvoudige `servicePrincipal` voor SSO. 
   
   Als tijdelijke oplossing kunt u de Microsoft Graph- [API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) gebruiken om alle geïmporteerde in elke `appRoles` AWS `servicePrincipal` op te halen waarbij het inrichten is geconfigureerd. U kunt deze functie teken reeksen vervolgens toevoegen aan de `servicePrincipal` AWS waar SSO is geconfigureerd.
 
* Rollen moeten voldoen aan de volgende vereisten om in aanmerking te komen voor importeren vanuit AWS in azure AD:

  * Rollen moeten precies één SAML-provider hebben gedefinieerd in AWS

  * De gecombineerde lengte van de ARN van de rol en de SAML-provider ARN voor een rol die wordt geïmporteerd, mag Maxi maal 119 tekens bevatten

## <a name="additional-resources"></a>Aanvullende resources

- [Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

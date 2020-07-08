---
title: 'Zelf studie: integratie Azure Active Directory met Amazon Web Services (AWS) om meerdere accounts te verbinden | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure AD en Amazon Web Services (AWS) (verouderde zelf studie).
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
ms.topic: article
ms.date: 04/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51be98654950ba290fa83f77eccdae4d6f549891
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81603826"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws-legacy-tutorial"></a>Zelf studie: integratie Azure Active Directory met Amazon Web Services (AWS) (verouderde zelf studie)

In deze zelf studie leert u hoe u Azure Active Directory (Azure AD) integreert met Amazon Web Services (AWS) (verouderde zelf studie).

De integratie van Amazon Web Services (AWS) met Azure AD heeft de volgende voordelen:

- U kunt in Azure AD beheren wie toegang heeft tot Amazon Web Services (AWS).
- U kunt uw gebruikers in staat stellen om automatisch te worden aangemeld bij Amazon Web Services (AWS) (eenmalige aanmelding) met hun Azure AD-accounts.
- U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](../manage-apps/what-is-single-sign-on.md)als u meer wilt weten over SaaS-app-integratie met Azure AD.

![Amazon Web Services (AWS) in de resultatenlijst](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Let op: het koppelen van één AWS-app aan al uw AWS-accounts is niet onze aanbevolen benadering. In plaats daarvan raden we u aan [deze](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) aanpak te gebruiken om meerdere exemplaren van AWS-account te configureren voor meerdere exemplaren van AWS-apps in azure AD. U moet deze aanpak alleen gebruiken als u weinig AWS-accounts en-rollen hebt. dit model is niet schaalbaar omdat de AWS-accounts en-rollen in deze accounts groeien. Deze benadering maakt geen gebruik van de functie voor het importeren van AWS met Azure AD-gebruikers inrichten, dus u moet de rollen hand matig toevoegen/bijwerken/verwijderen. Zie de onderstaande informatie voor andere beperkingen voor deze aanpak.

**U wordt aangeraden deze methode niet te gebruiken om de volgende redenen:**

* U moet de Microsoft Graph Explorer-benadering gebruiken om alle functies in de app te patchen. Het gebruik van de methode van het manifest bestand wordt niet aanbevolen.

* Er zijn klanten die rapporteren dat ze na het toevoegen van ~ 1200 app-rollen voor één AWS-app hebben gezien dat elke bewerking in de app is gestart met het activeren van de fouten met betrekking tot de grootte. Er is een vaste limiet van grootte op het toepassings object.

* U moet de rol hand matig bijwerken als de rollen worden toegevoegd aan een van de accounts. Dit is een vervangende benadering en niet toevoegen. Ook als uw accounts groeien, wordt de n x n-relatie met accounts en rollen.

* Alle AWS-accounts maken gebruik van hetzelfde XML-bestand met federatieve meta gegevens en op het moment van de certificaat overschakeling moet u deze enorme oefening voor het bijwerken van het certificaat op alle AWS-accounts op hetzelfde moment aansturen

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
* Nadat u Amazon Web Services (AWS) hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Amazon Web Services (AWS) toevoegen uit de galerie

Om de integratie van Amazon Web Services (AWS) met Azure AD te configureren, moet u Amazon Web Services (AWS) vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ **Amazon Web Services (AWS)** in het zoekvak in het gedeelte **Toevoegen uit de galerie**.
1. Selecteer **Amazon Web Services (AWS)** in het resultatenvenster en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

1. Zodra de toepassing is toegevoegd, gaat u naar **Eigenschappen** pagina en kopieert u de **object-id**.

    ![Amazon Web Services (AWS) in de resultatenlijst](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Amazon Web Services (AWS) op basis van een test gebruiker met de naam ' Julia Simon '.

Voor gebruik van eenmalige aanmelding moet Azure AD weten wat de tegen gebruiker in Amazon Web Services (AWS) is voor een gebruiker in azure AD. Met andere woorden, een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Amazon Web Services (AWS) moet tot stand worden gebracht.

In Amazon Web Services (AWS) wijst u de waarde van de **gebruikers naam** in azure AD toe als de waarde van de **naam** van de gebruiker om de koppelings relatie tot stand te brengen.

U moet de volgende bouwstenen voltooien om Azure AD Single Sign-On met Amazon Web Services (AWS) te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Amazon Web Services (AWS) configureren](#configure-amazon-web-services-aws-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u eenmalige aanmelding voor Azure AD in de Azure Portal en configureert u eenmalige aanmelding in uw Amazon Web Services-toepassing (AWS).

**Voer de volgende stappen uit om eenmalige aanmelding met Azure AD te configureren voor Amazon Web Services (AWS):**

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de overzichtspagina van de integratie voor **Amazon Web Services (AWS)** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **basis configuratie van SAML** hoeft de gebruiker geen stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure en op **Opslaan**klikt.

5. Amazon Web Services (AWS) verwacht de SAML-asserties in een specifieke notatie. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken en claims** op de integratiepagina van de toepassing-beheren. Klik op de pagina **eenmalige aanmelding met SAML instellen** op de knop **bewerken** om **gebruikers kenmerken** te openen & dialoog venster claims.

    ![image](common/edit-attribute.png)

6. In de sectie **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** configureert u het kenmerk van het SAML-token zoals wordt weergegeven in de bovenstaande afbeelding en voert u de volgende stappen uit:

    | Naam  | Bronkenmerk  | Naamruimte |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Rol            | user.assignedroles |  `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration             | "Geef een waarde op tussen 900 seconden (15 minuten) en 43200 seconden (12 uur)" |  `https://aws.amazon.com/SAML/Attributes` |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Typ in het tekstvak **naam ruimte** de waarde van de naam ruimte die voor die rij wordt weer gegeven.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

7. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op **downloaden** om de **federatieve meta gegevens-XML** te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Eenmalige aanmelding bij Amazon Web Services (AWS) configureren

1. Meld u in een ander browservenster aan als beheerder bij uw Amazon Web Services (AWS) bedrijfssite.

1. Klik op **AWS Home**.

    ![Startpagina voor configureren van eenmalige aanmelding][11]

1. Klik op **Identity and Access Management**.

    ![Identiteit voor eenmalige aanmelding configureren][12]

1. Klik op **Identity Providers** en vervolgens op **Create Provider**.

    ![Provider voor eenmalige aanmelding configureren][13]

1. Voer op de dialoogpagina **Configure Provider** de volgende stappen uit:

    ![Dialoogvenster Configure Single Sign-On][14]

    a. Selecteer **SAML** als **Provider Type**.

    b. Typ in het tekstvak **naam van provider** een provider naam (bijvoorbeeld: *WAAD*).

    c. Klik op **Choose File** om het uit de Azure-portal gedownloade **metagegevensbestand** te uploaden.

    d. Klik op **Next Step**.

1. Klik op de dialoogpagina **Verify Provider Information** op **Create**.

    ![Verificatie voor het configureren van eenmalige aanmelding][15]

1. Klik op **Roles** en vervolgens op **Create role**.

    ![Rollen voor eenmalige aanmelding configureren][16]

1. Voer op de pagina **Create role** de volgende stappen uit:  

    ![Vertrouwensrelatie voor eenmalige aanmelding configureren][19]

    a. Selecteer **SAML 2.0 federation** onder **Select type of trusted entity**.

    b. Onder **Kies een saml 2,0-provider sectie**selecteert u de **SAML-provider** die u eerder hebt gemaakt (bijvoorbeeld: *WAAD*)

    c. Selecteer **Allow programmatic and AWS Management Console access**.
  
    d. Klik op **volgende: machtigingen**.

1. Zoek in de zoek balk naar **Administrator-toegang** en schakel het selectie vakje **AdministratorAccess** in en klik vervolgens op **volgende: Tags**.

    ![Beheerders toegang selecteren](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Voer de volgende stappen uit in de sectie **Tags toevoegen (optioneel)** :

    ![Beheerders toegang selecteren](./media/aws-multi-accounts-tutorial/config2.png)

    a. Voer in het tekstvak **sleutel** de naam van de sleutel in voor ex: Azureadtest.

    b. Voer in het tekstvak **waarde (optioneel)** de sleutel waarde in met de volgende notatie `accountname-aws-admin` . De account naam moet in alle kleine letters staan.

    c. Klik op **volgende: controleren**.

1. Voer in het dialoogvenster **Review** de volgende stappen uit:

    ![Configuratie van eenmalige aanmelding controleren][34]

    a. Voer in het tekstvak **rolnaam naam** de waarde in het volgende patroon in `accountname-aws-admin` .

    b. In het tekstvak **Beschrijving van rol** voert u dezelfde waarde in die u hebt gebruikt voor de naam van de rol.

    c. Klik op **Create Role**.

    d. Maak zoveel rollen als nodig is en wijs ze toe aan de id-provider.

    > [!NOTE]
    > U kunt ook nog andere rollen maken, zoals AccountName-Finance-Administrator, AccountName-alleen-lezen-gebruiker, AccountName-devops-User, AccountName-TPM-gebruiker met verschillende beleids regels die moeten worden bijgevoegd. Later kunnen deze functie beleid ook worden gewijzigd op basis van vereisten per AWS-account, maar is het altijd beter om hetzelfde beleid te blijven gebruiken voor elke rol in de AWS-accounts.

1. Noteer de account-ID voor het AWS-account in EC2-eigenschappen of in het IAM-dash board zoals hieronder gemarkeerde:

    ![Beheerders toegang selecteren](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Meld u nu aan bij [Azure Portal](https://portal.azure.com/) en navigeer naar **groepen**.

1. Maak nieuwe groepen met dezelfde naam als die van de IAM-rollen die u eerder hebt gemaakt en noteer de **object-id's** van deze nieuwe groepen.

    ![Beheerders toegang selecteren](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Meld u af bij het huidige AWS-account en meld u aan met een ander account waarvoor u eenmalige aanmelding wilt configureren met Azure AD.

1. Zodra alle rollen in de accounts zijn gemaakt, worden ze weer gegeven in de lijst **rollen** voor die accounts.

    ![Rollen instellen](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

1. We moeten alle ARN en vertrouwde entiteiten voor alle rollen in alle accounts vastleggen. deze moeten hand matig worden toegewezen met de Azure AD-toepassing.

1. Klik op de rollen om de waarden van de **rol Arn** en **vertrouwde entiteiten** te kopiëren. U hebt deze waarden nodig voor alle rollen die u moet maken in azure AD.

    ![Rollen instellen](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Voer de bovenstaande stap uit voor alle rollen in alle accounts en sla deze allemaal op in de indelings **functie Arn, vertrouwde entiteiten** in een Klad blok.

1. Open [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) in een ander venster.

    a. Meld u aan bij de Microsoft Graph Explorer-site met de referenties van de globale beheerder/mede beheerder voor uw Tenant.

    b. U moet voldoende machtigingen hebben om de rollen te maken. Klik op **machtigingen wijzigen** om de vereiste machtigingen op te halen.

    ![Dialoog venster Microsoft Graph Verkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Selecteer de volgende machtigingen in de lijst (als u deze nog niet hebt) en klik op machtigingen wijzigen 

    ![Dialoog venster Microsoft Graph Verkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Hiermee wordt u gevraagd om u opnieuw aan te melden en de toestemming te accepteren. Nadat u de toestemming hebt geaccepteerd, meldt u zich opnieuw aan bij de Microsoft Graph Explorer.

    e. Wijzig de versie vervolg keuzelijst in **bèta**. Gebruik de volgende query om alle service-principals van uw Tenant op te halen:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Als u meerdere mappen gebruikt, kunt u het volgende patroon gebruiken dat uw primaire domein bevat.`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialoog venster Microsoft Graph Verkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. In de lijst met Service-principals die zijn opgehaald, moet u het account ophalen dat u wilt wijzigen. U kunt ook de CTRL + F gebruiken om de toepassing te doorzoeken vanuit alle vermelde ServicePrincipals. U kunt de volgende query gebruiken met behulp van de **object-id** die u hebt gekopieerd uit de Azure AD-eigenschappen pagina om naar de betreffende service-principal te gaan.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialoog venster Microsoft Graph Verkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    bijvoorbeeld Extraheer de eigenschap appRoles uit het object Service-Principal.

    ![Dialoog venster Microsoft Graph Verkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. U moet nu nieuwe rollen genereren voor uw toepassing. 

    i. Onder JSON is een voor beeld van een appRoles-object. Maak een vergelijkbaar object om de functies toe te voegen die u wilt toevoegen aan uw toepassing.

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
    > U kunt alleen nieuwe rollen toevoegen na de **msiam_access** voor de patch-bewerking. U kunt ook zoveel functies toevoegen als u wilt die uw organisatie nodig heeft. Azure AD verzendt de **waarde** van deze rollen als de claim waarde in het SAML-antwoord.

    j. Ga terug naar uw Microsoft Graph Explorer en wijzig de methode van **down load** to **patch**. Patch het Service-Principal-object om de gewenste rollen te hebben door de eigenschap appRoles bij te werken zoals hierboven in het voor beeld wordt weer gegeven. Klik op **query uitvoeren** om de patch bewerking uit te voeren. Met een geslaagd bericht wordt bevestigd hoe de rol voor uw Amazon Web Services-toepassing wordt gemaakt.

    ![Dialoog venster Microsoft Graph Verkenner](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Wanneer de Service-Principal is gepatchd met meer rollen, kunt u gebruikers/groepen toewijzen aan de respectieve rollen. U kunt dit doen door naar de portal te gaan en te navigeren naar de Amazon Web Services toepassing. Klik op het tabblad **gebruikers en groepen** aan de bovenkant.

1. We raden u aan om nieuwe groepen te maken voor elke AWS-rol, zodat u die specifieke rol in die groep kunt toewijzen. Houd er rekening mee dat dit een toewijzing voor één groep aan één rol heeft. U kunt vervolgens de leden toevoegen die deel uitmaken van deze groep.

1. Zodra de groepen zijn gemaakt, selecteert u de groep en wijst u deze toe aan de toepassing.

    ![Toevoegen van eenmalige aanmelding configureren](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Geneste groepen worden niet ondersteund bij het toewijzen van groepen.

1. Als u de rol aan de groep wilt toewijzen, selecteert u de rol en klikt u op de knop **toewijzen** onder aan de pagina.

    ![Toevoegen van eenmalige aanmelding configureren](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Houd er rekening mee dat u uw sessie in Azure Portal moet vernieuwen om nieuwe rollen weer te geven.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Amazon Web Services (AWS) in het toegangs venster klikt, moet u Amazon Web Services (AWS)-toepassings pagina krijgen met de optie om de rol te selecteren.

![Toevoegen van eenmalige aanmelding configureren](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

U kunt ook de SAML-respons controleren om te zien welke rollen worden door gegeven als claims.

![Toevoegen van eenmalige aanmelding configureren](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Zie [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Inrichting configureren met MS Graph Api's](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [Amazon Web Services (AWS) beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/protect-aws)

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
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
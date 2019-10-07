---
title: Een bestaand toegangs pakket bewerken en beheren in azure AD rechten beheer (preview)-Azure Active Directory
description: Meer informatie over het bewerken en beheren van een bestaand toegangs pakket in Azure Active Directory rechten beheer (preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/26/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a79cf166025ced6cb08d2f9e24801ea498fdc1c
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326382"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Een bestaand toegangs pakket bewerken en beheren in azure AD-rechts beheer (preview-versie)

> [!IMPORTANT]
> Azure Active Directory (Azure AD)-rechts beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met een toegangs pakket kunt u een eenmalige configuratie uitvoeren van resources en beleids regels die automatisch de toegang beheren voor de levens duur van het toegangs pakket. Als toegangs pakket beheer kunt u de resources in een toegangs pakket op elk gewenst moment wijzigen zonder dat u zich zorgen hoeft te maken over het inrichten van de toegang van de gebruiker tot de nieuwe resources of het verwijderen van de toegang tot de vorige resources. Beleids regels kunnen ook op elk gewenst moment worden bijgewerkt, maar de wijzigingen van het beleid zijn alleen van invloed op nieuwe toegangs rechten.

In dit artikel wordt beschreven hoe u bestaande toegangs pakketten bewerkt en beheert.

## <a name="add-resource-roles"></a>Resourcerollen toevoegen

Een resource-rol is een verzameling machtigingen die zijn gekoppeld aan een resource. De manier waarop u resources beschikbaar maakt voor gebruikers, is door resource rollen toe te voegen aan uw toegangs pakket. U kunt resource rollen toevoegen voor groepen, teams, toepassingen en share point-sites.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik in het linkermenu op **resource rollen**.

1. Klik op **resource rollen toevoegen** om de pagina Resource rollen toevoegen voor toegang tot het pakket te openen.

    ![Toegangs pakket-resource rollen toevoegen](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Afhankelijk van of u een groep, team, toepassing of share point-site wilt toevoegen, voert u de stappen uit in een van de volgende secties van de resource-rol.

### <a name="add-a-group-or-team-resource-role"></a>Een resource functie voor een groep of team toevoegen

U kunt met het rechten beheer automatisch gebruikers toevoegen aan een groep of een micro soft-team wanneer ze een toegangs pakket toewijzen. 

- Wanneer een groep of team deel uitmaakt van een toegangs pakket en een gebruiker aan dat toegangs pakket is toegewezen, wordt de gebruiker toegevoegd aan die groep of dit team, als deze nog niet aanwezig is.
- Wanneer de toewijzing van een toegangs pakket voor een gebruiker verloopt, worden deze verwijderd uit de groep of het team, tenzij ze momenteel een toewijzing hebben aan een ander toegangs pakket dat dezelfde groep of hetzelfde team bevat.

U kunt elke [Azure AD-beveiligings groep of Office 365-groep](../fundamentals/active-directory-groups-create-azure-portal.md)selecteren.  Beheerders kunnen een groep toevoegen aan een catalogus. Catalog-eigen aren kunnen een wille keurige groep toevoegen aan de catalogus als ze eigenaar van de groep zijn. Houd bij het selecteren van een groep de volgende Azure AD-beperkingen in acht:

- Wanneer een gebruiker, inclusief een gast, wordt toegevoegd als een lid aan een groep of team, kunnen ze alle andere leden van die groep of dit team zien.
- Azure AD kan het lidmaatschap van een groep die is gesynchroniseerd vanuit Windows Server Active Directory met Azure AD Connect of die in Exchange Online is gemaakt als distributie groep niet wijzigen.  
- Het lidmaatschap van dynamische groepen kan niet worden bijgewerkt door een lid toe te voegen of te verwijderen, zodat dynamische groepslid maatschappen niet geschikt zijn voor gebruik met rechten beheer.

Zie groepen en [Office 365-groepen en micro soft teams](/microsoftteams/office-365-groups) [vergelijken](/office365/admin/create-groups/compare-groups) voor meer informatie.

1. Klik op de pagina **resource rollen toevoegen aan toegangs pakket** op **groepen en teams** om het deel venster groepen selecteren te openen.

1. Selecteer de groepen en teams die u wilt toevoegen in het toegangs pakket.

    ![Toegangs pakket-resource rollen toevoegen-groepen selecteren](./media/entitlement-management-access-package-edit/group-select.png)

1. Klik op **Selecteren**.

    Wanneer u de groep of het team selecteert, wordt in de kolom **subtype** een van de volgende subtypen weer geven:

    |  |  |
    | --- | --- |
    | Beveiliging | Wordt gebruikt voor het verlenen van toegang tot resources. |
    | Distributie | Wordt gebruikt voor het verzenden van meldingen naar een groep personen. |
    | O365 | Office 365-groep waarvoor geen teams zijn ingeschakeld. Wordt gebruikt voor samen werking tussen gebruikers, zowel binnen als buiten uw bedrijf. |
    | Team | Office 365-groep waarvoor teams zijn ingeschakeld. Wordt gebruikt voor samen werking tussen gebruikers, zowel binnen als buiten uw bedrijf. |

1. Selecteer in de lijst **rol** de optie **eigenaar** of **lid**.

    Doorgaans selecteert u de leden rol. Als u de rol eigenaar selecteert, kunnen gebruikers andere leden of eigen aren toevoegen of verwijderen.

    ![Toegangs pakket-resource functie toevoegen voor een groep of team](./media/entitlement-management-access-package-edit/group-role.png)

1. Klik op **Toevoegen**.

    Gebruikers met een bestaande toewijzing aan het toegangs pakket worden automatisch lid van deze groep of dit team wanneer ze worden toegevoegd.

### <a name="add-an-application-resource-role"></a>Een toepassings resource functie toevoegen

U kunt Azure AD automatisch gebruikers toegang geven tot een Azure AD-bedrijfs toepassing, met inbegrip van zowel SaaS-toepassingen als de toepassingen van uw organisatie die federatieve zijn voor Azure AD, wanneer aan een gebruiker een toegangs pakket is toegewezen. Voor toepassingen die worden geïntegreerd met Azure AD via federatieve eenmalige aanmelding, worden door Azure AD Federatie tokens uitgegeven voor gebruikers die zijn toegewezen aan de toepassing.

Toepassingen kunnen meerdere rollen hebben. Wanneer u een toepassing toevoegt aan een toegangs pakket en deze toepassing meer dan één rol heeft, moet u de juiste rol voor deze gebruikers opgeven.  Als u toepassingen ontwikkelt, kunt u meer lezen over hoe deze functies aan uw toepassingen worden verstrekt in het artikel over het configureren van [de rol claim die is uitgegeven in het SAML-token](../develop/active-directory-enterprise-app-role-management.md).

Zodra een toepassingsrol deel uitmaakt van een toegangs pakket:

- Wanneer een gebruiker dat toegangs pakket toewijst, wordt de gebruiker toegevoegd aan die toepassingsrol, indien deze nog niet aanwezig is.
- Wanneer de toewijzing van een toegangs pakket van een gebruiker verloopt, wordt hun toegang verwijderd uit de toepassing, tenzij ze een toewijzing hebben aan een ander toegangs pakket dat die toepassingsrol bevat.

Hier volgen enkele overwegingen bij het selecteren van een toepassing:

- Toepassingen kunnen ook groepen hebben die zijn toegewezen aan hun rollen.  U kunt ervoor kiezen om een groep toe te voegen in plaats van een toepassingsrol in een toegangs pakket, maar de toepassing is echter niet zichtbaar voor de gebruiker als onderdeel van het toegangs pakket in de portal mijn toegang.

1. Klik op de pagina **resource rollen toevoegen aan toegangs pakket** op **toepassingen** om het deel venster toepassingen selecteren te openen.

1. Selecteer de toepassingen die u wilt toevoegen in het toegangs pakket.

    ![Toegangs pakket-resource rollen toevoegen-toepassingen selecteren](./media/entitlement-management-access-package-edit/application-select.png)

1. Klik op **Selecteren**.

1. Selecteer een toepassingsrol in de lijst met **rollen** .

    ![Toegangs pakket-resource functie toevoegen voor een toepassing](./media/entitlement-management-access-package-edit/application-role.png)

1. Klik op **Toevoegen**.

    Gebruikers met bestaande toewijzingen aan het toegangs pakket krijgen automatisch toegang tot deze toepassing wanneer ze worden toegevoegd.

### <a name="add-a-sharepoint-site-resource-role"></a>Een resource functie voor een share point-site toevoegen

Azure AD kan gebruikers automatisch toegang geven tot een share point online-site of share point online-site verzameling wanneer ze een toegangs pakket toewijzen.

1. Klik op de pagina **resource rollen toevoegen aan toegangs pakket** op **share point-sites** om het deel venster share point online-sites selecteren te openen.

1. Selecteer de share point online-sites die u wilt toevoegen in het toegangs pakket.

    ![Toegangs pakket-resource rollen toevoegen-share point online-sites selecteren](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Klik op **Selecteren**.

1. Selecteer in de lijst **rol** een share point online-site functie.

    ![Toegangs pakket-resource functie toevoegen voor een share point online-site](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Klik op **Toevoegen**.

    Gebruikers met bestaande toewijzingen aan het toegangs pakket krijgen automatisch toegang tot deze share point online-site wanneer ze worden toegevoegd.

## <a name="remove-resource-roles"></a>Resource rollen verwijderen

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik in het linkermenu op **resource rollen**.

1. Zoek in de lijst met resource rollen de resource functie die u wilt verwijderen.

1. Klik op het weglatings teken ( **...** ) en klik vervolgens op **resource functie verwijderen**.

    Gebruikers met bestaande toewijzingen aan het toegangs pakket hebben automatisch hun toegang tot deze resource functie ingetrokken wanneer deze wordt verwijderd.

## <a name="add-a-new-policy"></a>Een nieuw beleid toevoegen

De manier waarop u opgeeft wie een toegangs pakket kan aanvragen, is door een beleid te maken. U kunt meerdere beleids regels maken voor één toegangs pakket als u wilt toestaan dat verschillende sets gebruikers toewijzingen krijgen met verschillende goed keuringen en verloop instellingen. Eén beleids regel kan niet worden gebruikt om interne en externe gebruikers toe te wijzen aan hetzelfde toegangs pakket. U kunt echter twee beleids regels maken in hetzelfde toegangs pakket: een voor interne gebruikers en één voor externe gebruikers. Als er meerdere beleids regels zijn die van toepassing zijn op een gebruiker, wordt deze gevraagd op het tijdstip van de aanvraag om het beleid te selecteren waaraan ze zijn toegewezen.

In het volgende diagram ziet u het proces op hoog niveau voor het maken van een beleid voor een bestaand toegangs pakket.

![Een beleids proces maken](./media/entitlement-management-access-package-edit/policy-process.png)

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **beleid** en vervolgens op **beleid toevoegen**.

1. Voer een naam en beschrijving voor het beleid in.

    ![Beleid maken met de naam en beschrijving](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Voer de stappen in een van de volgende beleids secties uit op basis van uw selectie voor **gebruikers die toegang kunnen vragen**.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Bestaande beleids regels bewerken

U kunt een beleid op elk gewenst moment bewerken. Als u de verval datum voor een beleid wijzigt, wordt de verval datum voor aanvragen waarvoor al een goed keuring in behandeling of goedgekeurd staat, niet gewijzigd.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **beleids regels** en klik vervolgens op het beleid dat u wilt bewerken.

    Het deel venster **beleids Details** wordt geopend onder aan de pagina.

    ![Toegangs venster pakket-Details van beleid](./media/entitlement-management-access-package-edit/policy-details.png)

1. Klik op **bewerken** om het beleid te bewerken.

    ![Toegangs beleid voor pakket bewerken](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Wanneer u klaar bent, klikt u op **bijwerken**.

## <a name="directly-assign-a-user"></a>Een gebruiker rechtstreeks toewijzen

In sommige gevallen wilt u mogelijk rechtstreeks specifieke gebruikers toewijzen aan een toegangs pakket, zodat gebruikers niet hoeven door te gaan met het proces van het aanvragen van het toegangs pakket. Om gebruikers rechtstreeks toe te wijzen, moet het toegangs pakket beschikken over een beleid waarmee beheerders direct toewijzingen kunnen toestaan.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik in het menu links op **toewijzingen**.

1. Klik op **nieuwe toewijzing** om het pakket gebruiker toevoegen voor toegang te openen.

    ![Toewijzingen-gebruiker toevoegen aan toegangs pakket](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Klik op **gebruikers toevoegen** om de gebruikers te selecteren aan wie u het toegangs pakket wilt toewijzen.

1. Selecteer in de lijst **beleid selecteren** een beleid met de instelling [geen (alleen beheerders directe toewijzingen)](#policy-none-administrator-direct-assignments-only) .

    Als dit toegangs pakket niet over dit type beleid beschikt, kunt u op **Nieuw beleid maken** klikken om er een toe te voegen.

1. Stel de datum en tijd in waarop u de toewijzing van de geselecteerde gebruikers wilt starten en beëindigen. Als er geen eind datum wordt gegeven, worden de verloop instellingen van het beleid gebruikt.

1. Geef eventueel een reden op voor uw directe toewijzing voor het bijhouden van de record.

1. Klik op **toevoegen** om de geselecteerde gebruikers rechtstreeks toe te wijzen aan het toegangs pakket.

    Na enkele ogen blikken klikt u op **vernieuwen** om de gebruikers in de lijst toewijzingen weer te geven.

## <a name="view-who-has-an-assignment"></a>Weer geven wie een toewijzing heeft

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **toewijzingen** voor een overzicht van actieve toewijzingen.

1. Klik op een specifieke toewijzing om meer details weer te geven.

1. Als u een lijst wilt weer geven met toewijzingen waarvoor niet alle resource rollen op de juiste wijze zijn ingericht, klikt u op de filter status en selecteert u **leveren**.

    U kunt aanvullende informatie over bezorgings fouten zien door de overeenkomende aanvraag van de gebruiker op de pagina **aanvragen** te vinden.

1. Als u verlopen toewijzingen wilt zien, klikt u op de filter status en selecteert u **verlopen**.

1. Als u een CSV-bestand van de gefilterde lijst wilt downloaden, klikt u op **downloaden**.

## <a name="view-requests"></a>Aanvragen weer geven

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Klik op een specifieke aanvraag om aanvullende informatie weer te geven.

## <a name="view-a-requests-delivery-errors"></a>De leverings fouten van een aanvraag weer geven

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Selecteer de aanvraag die u wilt weer geven.

    Als de aanvraag een leverings fout bevat, wordt de status van de aanvraag niet **bezorgd** en wordt de substatus **gedeeltelijk geleverd**.

    Als er bezorgings fouten zijn, wordt in het detail venster van de aanvraag een telling van de bezorgings fouten weer gegeven.

1. Klik op het aantal om alle bezorgings fouten van de aanvraag weer te geven.

## <a name="cancel-a-pending-request"></a>Een aanvraag in behandeling annuleren

U kunt een aanvraag die in behandeling is, alleen annuleren als deze nog niet is bezorgd.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op **aanvragen**.

1. Klik op de aanvraag die u wilt annuleren

1. Klik in het deel venster aanvraag Details op **Aanvraag annuleren**.

## <a name="copy-my-access-portal-link"></a>De koppeling naar mijn Access-Portal kopiëren

De meeste gebruikers in uw Directory kunnen zich aanmelden bij de portal van mijn toegang en er wordt automatisch een lijst met toegangs pakketten weer geven die ze kunnen aanvragen. Voor gebruikers van externe zakelijke partners die nog niet in uw Directory zijn, moet u deze een koppeling sturen die ze kunnen gebruiken om een toegangs pakket aan te vragen. 

Het is belang rijk dat u de volledige koppeling naar de portal van mijn Access kopieert wanneer u deze naar een interne zakelijke partner verzendt. Dit zorgt ervoor dat de partner toegang krijgt tot de portal van uw Directory om de aanvraag te doen. 

De koppeling wordt gestart met ' myaccess ', bevat een directory-hint en eindigt met een toegangs pakket-id. Zorg ervoor dat de koppeling alle volgende bevat:

 `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

Zolang het toegangs pakket is ingeschakeld voor externe gebruikers en u een beleid hebt voor de map van de externe gebruiker, kan de externe gebruiker de koppeling naar mijn Access-Portal gebruiken om het toegangs pakket aan te vragen.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Op de pagina overzicht kopieert u de **koppeling naar de portal van mijn toegang**.

    ![Overzicht van toegangs pakketten-koppeling naar mijn Access-Portal](./media/entitlement-management-shared/my-access-portal-link.png)

1. E-mail of verzend de koppeling naar uw externe zakelijke partner. Ze kunnen de koppeling delen met hun gebruikers om het toegangs pakket aan te vragen.

## <a name="change-the-hidden-setting"></a>De verborgen instelling wijzigen

Toegangs pakketten worden standaard gedetecteerd. Dit betekent dat als een beleid toestaat dat een gebruiker het toegangs pakket kan aanvragen, het toegangs pakket in hun mijn Access-Portal automatisch wordt weer gegeven.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik op de pagina overzicht op **bewerken**.

1. Stel de instelling **verborgen** in.

    Als deze waarde is ingesteld op **Nee**, wordt het toegangs pakket weer gegeven in de mijn Access-portal van de gebruiker.

    Als deze instelling is ingesteld op **Ja**, wordt het toegangs pakket niet vermeld in de mijn Access-portal van de gebruiker. De enige manier waarop een gebruiker het toegangs pakket kan weer geven, is als hij of zij de **koppeling directe mijn Access-Portal** naar het toegangs pakket heeft.

## <a name="delete"></a>Verwijderen

Een toegangs pakket kan alleen worden verwijderd als er geen actieve gebruikers toewijzingen zijn.

**Vereiste rol:** Globale beheerder, gebruikers beheerder, catalogus eigenaar of toegangs pakket beheer

1. Klik in de Azure Portal op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het menu links op **toegangs pakketten** en open vervolgens het toegangs pakket.

1. Klik in het menu links op **toewijzingen** en verwijder toegang voor alle gebruikers.

1. Klik in het menu links op **overzicht** en klik vervolgens op **verwijderen**.

1. Klik op **Ja**in het bericht verwijderen dat wordt weer gegeven.

## <a name="when-are-changes-applied"></a>Wanneer worden wijzigingen toegepast

In het recht op beheer worden bulksgewijs wijzigingen in de toewijzing en resources in uw toegangs pakketten enkele keren per dag verwerkt door Azure AD. Als u dus een toewijzing maakt of de resource rollen van uw toegangs pakket wijzigt, kan het tot 24 uur duren voordat deze wijziging wordt doorgevoerd in azure AD, plus de hoeveelheid tijd die nodig is om deze wijzigingen door te geven aan andere micro soft Online Services of verbonden SaaS-toepassing z. Als uw wijziging van invloed is op slechts enkele objecten, duurt het waarschijnlijk slechts enkele minuten om toe te passen in azure AD, waarna andere onderdelen van Azure AD vervolgens detecteren dat de SaaS-toepassingen worden gewijzigd en bijgewerkt. Als uw wijziging gevolgen heeft voor duizenden objecten, neemt de wijziging langer in beslag. Als u bijvoorbeeld een toegangs pakket hebt met 2 toepassingen en 100 gebruikers toewijzingen en u besluit een share point-siterol toe te voegen aan het toegangs pakket, kan er een vertraging optreden totdat alle gebruikers deel uitmaken van die share point-siterol. U kunt de voortgang bewaken via het Azure AD-controle logboek, het Azure AD-inrichtings logboek en de audit logboeken van de share point-site.

## <a name="next-steps"></a>Volgende stappen

- [Een catalogus eigenaar of een Access Package Manager toevoegen](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [Aanvraag proces en e-mail meldingen](entitlement-management-process.md)

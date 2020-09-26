---
title: 'Zelf studie: vak configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: 44f2195fb68b5a17eab3980f72cbc5374e1c033a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91312903"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Zelf studie: vak configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is het weer geven van de stappen die u moet uitvoeren in box en Azure AD om gebruikers accounts automatisch van Azure AD in te richten en uit te voeren.

> [!NOTE]
> In deze zelfstudie wordt een connector beschreven die is gebaseerd op de Azure AD-service voor het inrichten van gebruikers. Zie voor belangrijke details over wat deze service doet, hoe het werkt en veelgestelde vragen [Inrichting en ongedaan maken van inrichting van gebruikers automatiseren naar SaaS-toepassingen met Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Box hebt u de volgende items nodig:

- Een Azure AD-Tenant
- Een box-Business-abonnement of beter

> [!NOTE]
> Wanneer u de stappen in deze zelf studie test, wordt u aangeraden *geen* productie omgeving te gebruiken.

> [!NOTE]
> Apps moeten eerst worden ingeschakeld in de box-toepassing.

Volg deze aanbevelingen als u de stappen in deze zelfstudie wilt testen:

- Gebruik niet de productieomgeving, tenzij dit echt nodig is.
- Als u niet beschikt over een evaluatieomgeving in Azure AD, kunt u [een gratis proefversie van één maand krijgen](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Gebruikers toewijzen aan vak 

Azure Active Directory gebruikt een concept met de naam 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw Box-app. Eenmaal besloten, kunt u deze gebruikers toewijzen aan uw Box-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Gebruikers en groepen toewijzen
In het **vak > gebruikers en groepen** op het tabblad Azure Portal kunt u opgeven aan welke gebruikers en groepen toegang moet worden verleend. De toewijzing van een gebruiker of groep leidt ertoe dat de volgende zaken optreden:

* In azure AD is de toegewezen gebruiker (door middel van een directe toewijzing of groepslid maatschap) in staat om zich aan te melden. Als een gebruiker niet is toegewezen, staat Azure AD niet toe om zich aan te melden bij box en wordt er een fout bericht weer gegeven op de aanmeldings pagina van Azure AD.
* Een app-tegel voor box wordt toegevoegd aan het start programma van de [toepassing](../manage-apps/end-user-experiences.md)van de gebruiker.
* Als automatische inrichting is ingeschakeld, worden de toegewezen gebruikers en/of groepen aan de inrichtings wachtrij toegevoegd om automatisch te worden ingericht.
  
  * Als alleen gebruikers objecten zijn geconfigureerd om te worden ingericht, worden alle rechtstreeks toegewezen gebruikers in de inrichtings wachtrij geplaatst en worden alle gebruikers die lid zijn van een toegewezen groep in de inrichtings wachtrij geplaatst. 
  * Als groeps objecten zijn geconfigureerd om te worden ingericht, worden alle toegewezen groeps objecten ingericht voor box en alle gebruikers die lid zijn van deze groepen. De groeps-en gebruikers lidmaatschappen worden bewaard wanneer ze worden geschreven naar box.

U kunt de **kenmerken > tabblad eenmalige aanmelding** gebruiken om te configureren welke gebruikers kenmerken (of claims) worden weer gegeven aan box tijdens op SAML gebaseerde verificatie, en het tabblad **kenmerken > inrichten** om te configureren hoe gebruikers-en groeps kenmerken stromen van Azure AD naar box tijdens het inrichten.

### <a name="important-tips-for-assigning-users-to-box"></a>Belang rijke tips voor het toewijzen van gebruikers aan box 

*   Het is raadzaam om één Azure AD-gebruiker toe te wijzen aan box om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

*   Wanneer u een gebruiker aan het vak toewijzen, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="enable-automated-user-provisioning"></a>Automatische gebruikers inrichting inschakelen

In deze sectie vindt u instructies voor het verbinden van de API voor het inrichten van de gebruikers account van Azure AD in box en het configureren van de inrichtings service om toegewezen gebruikers accounts te maken, bij te werken en uit te scha kelen in vak op basis van de gebruikers-en groeps toewijzing in azure AD.

Als automatische inrichting is ingeschakeld, worden de toegewezen gebruikers en/of groepen aan de inrichtings wachtrij toegevoegd om automatisch te worden ingericht.
    
 * Als alleen gebruikers objecten zijn geconfigureerd om te worden ingericht, worden direct toegewezen gebruikers in de inrichtings wachtrij geplaatst en alle gebruikers die lid zijn van een toegewezen groep, worden in de inrichtings wachtrij geplaatst. 
    
 * Als groeps objecten zijn geconfigureerd om te worden ingericht, worden alle toegewezen groeps objecten ingericht voor box en alle gebruikers die lid zijn van deze groepen. De groeps-en gebruikers lidmaatschappen worden bewaard wanneer ze worden geschreven naar box.

> [!TIP] 
> U kunt er ook voor kiezen om op SAML gebaseerde eenmalige aanmelding voor in te scha kelen, gevolgd door de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="to-configure-automatic-user-account-provisioning"></a>Automatische toewijzing van gebruikers accounts configureren:

Het doel van deze sectie is het maken van een overzicht van het inrichten van Active Directory gebruikers accounts in box.

1. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

2. Als u het vak al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw exemplaar van box met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **vak** in de toepassings galerie. Selecteer een vak in de zoek resultaten en voeg dit toe aan uw lijst met toepassingen.

3. Selecteer uw instantie van Box en selecteer vervolgens het tabblad **inrichten** .

4. Stel de **Inrichtingsmodus** in op **Automatisch**. 

    ![Scherm afbeelding van het tabblad inrichten voor box in Azure Portal. De inrichtings modus is ingesteld op automatisch en autoriseren is gemarkeerd in de beheerders referenties.](./media/box-userprovisioning-tutorial/provisioning.png)

5. Klik onder de sectie **beheerders referenties** op **machtigen** om een dialoog venster Aanmelden bij een vak te openen in een nieuw browser venster.

6. Geef op de pagina **aanmelden om toegang te verlenen** tot het vak de vereiste referenties op en klik vervolgens op **autoriseren**. 
   
    ![Scherm afbeelding van het dialoog venster Aanmelden om toegang te verlenen aan het box-scherm, met vermelding van het e-mail adres en wacht woord en de knop autoriseren.](./media/box-userprovisioning-tutorial/IC769546.png "Automatische gebruikers inrichting inschakelen")

7. Klik op **toegang verlenen aan** om deze bewerking te autoriseren en terug te keren naar de Azure Portal. 
   
    ![Scherm afbeelding van het venster toegang autoriseren in box, met een verklarend bericht en de knop toegang tot vak verlenen.](./media/box-userprovisioning-tutorial/IC769549.png "Automatische gebruikers inrichting inschakelen")

8. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw Box-app. Als de verbinding mislukt, zorgt u ervoor dat uw Box-account beschikt over team beheerders machtigingen en voert u de stap **' autoriseren '** opnieuw uit.

9. Voer het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen in het veld **e-mail melding** en schakel het selectie vakje in.

10. Klik op **opslaan.**

11. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met.**

12. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD in box. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen, worden gebruikt voor de overeenkomst tussen de gebruikers accounts in box voor bijwerk bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

13. Als u de Azure AD-inrichtings service voor Box wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte instellingen

14. Klik op **opslaan.**

Hiermee start u de eerste synchronisatie van gebruikers en/of groepen die zijn toegewezen aan box in de sectie gebruikers en groepen. Het duurt langer voordat de initiële synchronisatie is uitgevoerd dan volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden, zolang de service wordt uitgevoerd. U kunt de sectie **synchronisatie Details** gebruiken om de voortgang te bewaken en koppelingen te volgen voor het inrichtings logboek, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtings service in uw Box-app.

Zie [Rapportage over automatische inrichting van gebruikersaccounts](../app-provisioning/check-status-user-account-provisioning.md) voor informatie over het lezen van de Azure AD-inrichtingslogboeken.

In uw Box-Tenant worden gesynchroniseerde gebruikers vermeld onder **beheerde gebruikers** in de **beheer console**.

![Integratie status](./media/box-userprovisioning-tutorial/IC769556.png "Integratie status")


## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountinrichting voor zakelijke apps beheren](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
* [Eenmalige aanmelding configureren](box-tutorial.md)

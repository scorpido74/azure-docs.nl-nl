---
title: 'Zelf studie: concur configureren voor het automatisch inrichten van gebruikers met Azure Active Directory | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Concur configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 441aa9805f2a453e22f207238315125d2a281838
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "60280403"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Zelf studie: concur configureren voor automatische gebruikers inrichting

Het doel van deze zelf studie is om u te laten zien welke stappen u moet uitvoeren in concur en Azure AD om gebruikers accounts van Azure AD automatisch in te richten en te deactiveren naar concur.

## <a name="prerequisites"></a>Vereisten

In het scenario dat in deze zelf studie wordt beschreven, wordt ervan uitgegaan dat u de volgende items al hebt:

*   Een Azure Active Directory-Tenant.
*   Een concur-abonnement met eenmalige aanmelding.
*   Een gebruikers account in concur met team beheerders machtigingen.

## <a name="assigning-users-to-concur"></a>Gebruikers toewijzen aan concur

Azure Active Directory gebruikt een concept met de naam ' toewijzingen ' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In de context van het automatisch inrichten van gebruikers accounts worden alleen de gebruikers en groepen die zijn toegewezen aan een toepassing in azure AD gesynchroniseerd.

Voordat u de inrichtings service configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot uw concur-app. Nadat u hebt besloten, kunt u deze gebruikers toewijzen aan uw concur-app door de volgende instructies te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfs-app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Belang rijke tips voor het toewijzen van gebruikers aan concur

*   U wordt aangeraden één Azure AD-gebruiker toe te wijzen aan concur om de inrichtings configuratie te testen. Extra gebruikers en/of groepen kunnen later worden toegewezen.

*   Wanneer u een gebruiker toewijst aan concur, moet u een geldige gebruikersrol selecteren. De rol ' standaard toegang ' werkt niet voor het inrichten.

## <a name="enable-user-provisioning"></a>Gebruikers inrichten inschakelen

In deze sectie vindt u instructies voor het verbinden van uw Azure AD-concur en het configureren van de inrichtings service om toegewezen gebruikers accounts in concur te maken, bij te werken en uit te scha kelen op basis van de gebruikers-en groeps toewijzing in azure AD.

> [!Tip] 
> U kunt er ook voor kiezen om eenmalige aanmelding op basis van SAML in te scha kelen voor concur, volgens de instructies in [Azure Portal](https://portal.azure.com). Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, maar deze twee functies gelden voor elkaar.

### <a name="to-configure-user-account-provisioning"></a>Het inrichten van een gebruikers account configureren:

Het doel van deze sectie is het instellen van het inrichten van Active Directory gebruikers accounts in te stellen op concur.

Als u apps in de service onkosten wilt inschakelen, moet de juiste installatie en het gebruik van een Web Service-beheer profiel zijn. Voeg de rol WS-beheerder niet toe aan uw bestaande beheerders profiel dat u gebruikt voor T&E-beheer functies.

Concur consultants of de client beheerder moeten een uniek beheerders profiel voor webservices maken en de client beheerder moet dit profiel gebruiken voor de functies van de webservices-beheerder (bijvoorbeeld het inschakelen van apps). Deze profielen moeten gescheiden worden gehouden van het dagelijks T&E-beheerders Profiel van de client beheerder (het T&E-beheerders profiel mag niet de rol WSAdmin toegewezen).

Wanneer u het profiel maakt dat moet worden gebruikt voor het inschakelen van de app, voert u de naam van de client beheerder in de velden van het gebruikers profiel in. Hiermee wijst u het eigendom toe aan het profiel. Wanneer een of meer profielen zijn gemaakt, moet de client zich aanmelden met dit profiel om te klikken op de knop '*inschakelen*' voor een partner-app in het menu webservices.

Om de volgende redenen mag deze actie niet worden uitgevoerd met het profiel dat ze gebruiken voor normaal T&E-beheer.

* De client moet worden ingesteld op*Ja*in het dialoog venster dat wordt weer gegeven nadat een app is ingeschakeld. Op deze manier kunt u bevestigen dat de client toegang heeft tot de gegevens van de partner, zodat u of de partner niet op de knop Ja kan klikken.

* Als een client beheerder die een app heeft ingeschakeld via het E-beheerders profiel T&, het bedrijf verlaat (wat resulteert in het profiel dat wordt gedeactiveerd), werken apps die gebruikmaken van dat profiel niet totdat de app is ingeschakeld met een ander actief WS-beheer profiel. Daarom moet u afzonderlijke WS-beheer profielen maken.

* Als een beheerder het bedrijf verlaat, kan de naam die aan het profiel voor de WS-beheerder is gekoppeld, indien gewenst zonder dat dit van invloed is op de ingeschakelde app, worden gewijzigd in de vervangende beheerder omdat dat profiel niet hoeft te worden geactiveerd.

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u aan bij uw **concur** -Tenant.

2. Selecteer **webservices**in het menu **beheer** .
   
    ![Concur-Tenant](./media/concur-provisioning-tutorial/IC721729.png "Concur-Tenant")

3. Selecteer aan de linkerkant in het deel venster **webservices** de optie **partner toepassing inschakelen**.
   
    ![Partner toepassing inschakelen](./media/concur-provisioning-tutorial/ic721730.png "Partner toepassing inschakelen")

4. Selecteer in de lijst **toepassing inschakelen** de optie **Azure Active Directory**en klik vervolgens op **inschakelen**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Klik op **Ja** om het dialoog venster **actie bevestigen** te sluiten.
   
    ![Actie bevestigen](./media/concur-provisioning-tutorial/ic721732.png "Actie bevestigen")

6. Blader in het [Azure Portal](https://portal.azure.com)naar het gedeelte **Azure Active Directory > Enter prise-apps > alle toepassingen** .

7. Als u concur al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van concur met behulp van het zoek veld. Als dat niet het geval is, selecteert u **toevoegen** en zoeken naar **concur** in de toepassings galerie. Selecteer concur in de zoek resultaten en voeg deze toe aan uw lijst met toepassingen.

8. Selecteer uw exemplaar van concur en selecteer vervolgens het tabblad **inrichten** .

9. Stel de **inrichtings modus** in op **automatisch**. 
 
    ![inrichtings](./media/concur-provisioning-tutorial/provisioning.png)

10. Voer de **gebruikers naam** en het **wacht woord** van uw concur-beheerder in het gedeelte **beheerders referenties** in.

11. Klik in het Azure Portal op **verbinding testen** om ervoor te zorgen dat Azure AD verbinding kan maken met uw concur-app. Als de verbinding mislukt, zorgt u ervoor dat uw concur-account team beheerders machtigingen heeft.

12. Voer het e-mail adres in van een persoon of groep die inrichtings fout meldingen moet ontvangen in het veld **e-mail melding** en schakel het selectie vakje in.

13. Klik op **opslaan.**

14. Selecteer in de sectie toewijzingen de optie **Azure Active Directory gebruikers synchroniseren met concur.**

15. Controleer in de sectie **kenmerk toewijzingen** de gebruikers kenmerken die zijn gesynchroniseerd vanuit Azure AD naar concur. De kenmerken die zijn geselecteerd als **overeenkomende** eigenschappen worden gebruikt om te voldoen aan de gebruikers accounts in concur voor bijwerk bewerkingen. Selecteer de knop Opslaan om eventuele wijzigingen door te voeren.

16. Als u de Azure AD-inrichtings service voor concur wilt inschakelen, wijzigt u de **inrichtings status** **in in het** gedeelte **instellingen**

17. Klik op **opslaan.**

U kunt nu een test account maken. Wacht Maxi maal 20 minuten om te controleren of het account is gesynchroniseerd met concur.

## <a name="additional-resources"></a>Extra resources

* [Inrichten van gebruikers accounts voor zakelijke apps beheren](tutorial-list.md)
* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Eenmalige aanmelding configureren](concur-tutorial.md)


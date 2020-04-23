---
title: 'Zelfstudie: Gebruikersinrichting - LinkedIn Sales Navigator, Azure AD'
description: Meer informatie over het configureren van Azure Active Directory om gebruikersaccounts automatisch in te richten en te de-provisionen voor LinkedIn Sales Navigator.
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
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869748"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Zelfstudie: LinkedIn Sales Navigator configureren voor automatische gebruikersinrichting

Het doel van deze zelfstudie is om u de stappen te laten zien die u moet uitvoeren in LinkedIn Sales Navigator en Azure AD om gebruikersaccounts automatisch in te richten en te de-provisionen van Azure AD naar LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Vereisten

Het scenario dat in deze zelfstudie wordt beschreven, gaat ervan uit dat u al de volgende items hebt:

* Een Azure Active Directory-tenant
* Een LinkedIn Sales Navigator-tenant 
* Een beheerdersaccount in LinkedIn Sales Navigator met toegang tot het LinkedIn-accountcentrum

> [!NOTE]
> Azure Active Directory integreert met LinkedIn Sales Navigator met behulp van het [SCIM-protocol.](http://www.simplecloud.info/)

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Gebruikers toewijzen aan LinkedIn Sales Navigator

Azure Active Directory gebruikt een concept genaamd 'toewijzingen' om te bepalen welke gebruikers toegang moeten krijgen tot geselecteerde apps. In het kader van automatische gebruikersaccountinrichting worden alleen de gebruikers en groepen die zijn 'toegewezen' aan een toepassing in Azure AD gesynchroniseerd.

Voordat u de inrichtingsservice configureert en inschakelt, moet u bepalen welke gebruikers en/of groepen in Azure AD de gebruikers vertegenwoordigen die toegang nodig hebben tot LinkedIn Sales Navigator. Zodra u hebt besloten, u deze gebruikers toewijzen aan LinkedIn Sales Navigator door de instructies hier te volgen:

[Een gebruiker of groep toewijzen aan een bedrijfsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Belangrijke tips voor het toewijzen van gebruikers aan LinkedIn Sales Navigator

* Het wordt aanbevolen om één Azure AD-gebruiker aan LinkedIn Sales Navigator toe te wijzen om de inrichtingsconfiguratie te testen. Mogelijk worden later extra gebruikers en/of groepen toegewezen.

* Wanneer u een gebruiker toewijst aan LinkedIn Sales Navigator, moet u de rol **Gebruiker** selecteren in het toewijzingsdialoogvenster. De rol 'Standaardtoegang' werkt niet voor inrichten.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Gebruikersvoorziening configureren voor LinkedIn Sales Navigator

In deze sectie u uw Azure AD verbinden met de SCIM-gebruikersaccountvoorzieningsAPI van LinkedIn Sales Navigator en de inrichtingsservice configureren om toegewezen gebruikersaccounts te maken, bij te werken en uit te schakelen in LinkedIn Sales Navigator op basis van gebruikers- en groepstoewijzing in Azure AD.

> [!TIP]
> U er ook voor kiezen om SAML-gebaseerde single sign-On in te schakelen voor LinkedIn Sales Navigator, volgens de instructies in [Azure portal.](https://portal.azure.com) Eenmalige aanmelding kan onafhankelijk van automatische inrichting worden geconfigureerd, hoewel deze twee functies elkaar aanvullen.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Ga als het gaat om het configureren van automatische gebruikersaccountvoorziening voor LinkedIn Sales Navigator in Azure AD:

De eerste stap is het ophalen van uw LinkedIn-toegangstoken. Als u een Enterprise-beheerder bent, u een toegangstoken zelf inrichten. Ga in uw accountcentrum naar **Algemene instellingen instellingen instellingen &gt; ** en open het deelvenster **SCIM-instellingen.**

> [!NOTE]
> Als u rechtstreeks toegang hebt tot het accountcentrum in plaats van via een koppeling, u deze bereiken via de volgende stappen.

1. Meld u aan bij Account Center.

2. Selecteer ** &gt; Beheerbeheerinstellingen** .

3. Klik op **Geavanceerde integraties** op de linkerzijbalk. U wordt doorverwezen naar het accountcenter.

4. Klik **+ Voeg nieuwe SCIM-configuratie toe** en volg de procedure door elk veld in te vullen.

    > [!NOTE]
    > Wanneer licenties voor automatisch toewijzen niet is ingeschakeld, betekent dit dat alleen gebruikersgegevens worden gesynchroniseerd.

    ![LinkedIn Sales Navigator Provisioning](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Wanneer autolicentietoewijzing is ingeschakeld, moet u de toepassingsinstantie en het licentietype noteren. Licenties worden toegewezen op basis van wie het eerst komt, het eerst maalt totdat alle licenties zijn ingenomen.

    ![LinkedIn Sales Navigator Provisioning](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Klik **op Token genereren**. U moet uw access token display onder het access **token** veld zien.

6. Sla uw toegangstoken op uw klembord of computer op voordat u de pagina verlaat.

7. Meld u vervolgens aan bij de [Azure-portal](https://portal.azure.com)en blader naar de sectie **Azure Active Directory > Enterprise Apps > Alle toepassingen.**

8. Als u LinkedIn Sales Navigator al hebt geconfigureerd voor eenmalige aanmelding, zoekt u naar uw instantie van LinkedIn Sales Navigator met behulp van het zoekveld. Selecteer anders **Toevoegen** en zoeken naar **LinkedIn Sales Navigator** in de toepassingsgalerie. Selecteer LinkedIn Sales Navigator in de zoekresultaten en voeg deze toe aan uw lijst met toepassingen.

9. Selecteer uw exemplaar van LinkedIn Sales Navigator en selecteer vervolgens het tabblad **Inrichten.**

10. Stel de **inrichtingsmodus** in op **Automatisch**.

    ![LinkedIn Sales Navigator Provisioning](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Vul de volgende velden in onder **Beheerdersreferenties:**

    * Voer in het veld https://developer.linkedin.comUrl van **tenant** .

    * Voer in het veld **Geheim token** het toegangstoken in dat u in stap 1 hebt gegenereerd en klik op **Verbinding testen** .

    * U ziet een melding voor succes aan de rechterbovenhoek van uw portal.

12. Voer het e-mailadres in van een persoon of groep die meldingen van provisioning-fouten moet ontvangen in het veld **E-mail melden** en schakel het selectievakje hieronder in.

13. Klik op **Opslaan**.

14. Controleer in de sectie **Kenmerktoewijzingen** de gebruikers- en groepskenmerken die worden gesynchroniseerd van Azure AD naar LinkedIn Sales Navigator. Houd er rekening mee dat de kenmerken die zijn geselecteerd als **eigenschappen matching,** worden gebruikt om de gebruikersaccounts en -groepen in LinkedIn Sales Navigator te matchen voor updatebewerkingen. Selecteer de knop Opslaan om wijzigingen door te voeren.

    ![LinkedIn Sales Navigator Provisioning](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Als u de Azure AD-inrichtingsservice voor LinkedIn Sales Navigator wilt inschakelen, wijzigt u de **instelstatus** in **Aan** in de sectie **Instellingen**

16. Klik op **Opslaan**.

Hiermee wordt gestart met de eerste synchronisatie van gebruikers en/of groepen die zijn toegewezen aan LinkedIn Sales Navigator in de sectie Gebruikers en groepen. Houd er rekening mee dat de eerste synchronisatie langer duurt dan de volgende synchronisaties, die ongeveer elke 40 minuten plaatsvinden zolang de service wordt uitgevoerd. U de sectie **Synchronisatiedetails** gebruiken om de voortgang te controleren en koppelingen naar het inrichten van activiteitslogboeken te volgen, waarin alle acties worden beschreven die worden uitgevoerd door de inrichtingsservice in uw LinkedIn Sales Navigator-app.

Zie [Rapportage over automatische gebruikersaccountinrichting voor](../app-provisioning/check-status-user-account-provisioning.md)meer informatie over het lezen van de azure AD-inrichtingslogboeken.

## <a name="additional-resources"></a>Aanvullende resources

* [Gebruikersaccountvoorziening voor Enterprise Apps beheren](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

---
title: Gebruikers toevoegen aan een dynamische groep-zelf studie-Azure AD | Microsoft Docs
description: In deze zelfstudie gebruikt u groepen met gebruikerslidmaatschapsregels voor het automatisch toevoegen of verwijderen van gebruikers
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ee5fa52f59ea2ef3332fe66c81c24ff44c64e81
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582886"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Zelfstudie: Automatisch groepsleden toevoegen of verwijderen

In Azure Active Directory (Azure AD) kunt u automatisch gebruikers aan beveiligingsgroepen of Office 365-groepen toevoegen of daaruit verwijderen, zodat u dit niet altijd handmatig hoeft te doen. Wanneer alle eigenschappen van een gebruiker of apparaat veranderen, evalueert Azure AD alle regels voor dynamische groepen in uw Azure AD-organisatie om te zien of de wijziging leden moet toevoegen of verwijderen.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Een automatisch gevulde groep gast gebruikers maken van een partner bedrijf
> * Licenties aan de groep toewijzen voor de partnerspecifieke functies die toegankelijk moeten zijn voor de gastgebruikers
> * Optioneel: de groep **Alle gebruikers** beveiligen door gastgebruikers te verwijderen zodat bijvoorbeeld alleen lidgebruikers toegang hebben tot interne sites

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor deze functie is een Azure AD Premium licentie voor u vereist als globale beheerder van de organisatie. Als u er nog geen hebt, selecteert u in azure AD **licenties** > voor**producten** > **try/buy**.

U bent niet verplicht licenties aan de gebruikers toe te wijzen om ze op te nemen als leden in dynamische groepen. U hebt alleen het minimale aantal beschik bare Azure AD Premium P1-licenties in de organisatie nodig om al deze gebruikers te kunnen behandelen. 

## <a name="create-a-group-of-guest-users"></a>Een groep gastgebruikers maken

Eerst maakt u een groep voor uw gastgebruikers, die alle afkomstig zijn van één partnerbedrijf. Omdat zij speciale licenties nodig hebben, is het vaak efficiënter om een groep te maken voor dit doel.

1. Meld u aan bij de Azure Portalhttps://portal.azure.com) (met een account dat de globale beheerder is voor uw organisatie.
2. Selecteer **Azure Active Directory** > **groepen** > **nieuwe groep**.
   ![Selecteer een opdracht voor het starten van een nieuwe groep](./media/groups-dynamic-tutorial/new-group.png)
3. Ga als volgt te werk op de blade **Groep**:
  
   * Selecteer **beveiliging** als het groeps type.
   * Voer `Guest users Contoso` in als de naam en beschrijving voor de groep.
   * Wijzig het **lidmaatschaps type** in een **dynamische gebruiker**.
   
4. Selecteer **eigen aren** en op de Blade **eigen aren toevoegen** zoeken naar de gewenste eigen aren. Klik op de gewenste eigen aars om toe te voegen aan de selectie.
5. Klik op **selecteren** om de Blade **eigen aars toevoegen** te sluiten.  
6. Selecteer **dynamische query bewerken** in het vak **dynamische gebruikers leden** .
7. Ga als volgt te werk op de blade **Dynamisch-lidmaatschapregels**:

   * Klik in het veld **eigenschap** op de bestaande waarde en selecteer **User type**. 
   * Controleer of het **operator** veld **gelijk is aan** geselecteerd.  
   * Selecteer het veld **waarde** en voer **gast**in. 
   * Klik op de Hyper link- **expressie toevoegen** om een andere regel toe te voegen.
   * **Selecteer in**het veld **en/of** .
   * Selecteer **Bedrijfs naam**in het veld **eigenschap** .
   * Controleer of het **operator** veld **gelijk is aan** geselecteerd.
   * Typ **Contoso**in het veld **waarde** .
   * Klik op **Opslaan** om de Blade **dynamische lidmaatschaps regels** te sluiten.
   
8. Selecteer, op de blade **Groep**, **Maken** om de groep te maken.

## <a name="assign-licenses"></a>Licenties toewijzen

Nu u de nieuwe groep hebt gemaakt, kunt u de benodigde licenties aan deze partnergebruikers toewijzen.

1. Selecteer **Licenties** in Microsoft Azure Active Directory, selecteer een of meer licenties, en selecteer vervolgens **Toewijzen**.
2. Selecteer **Gebruikers en groepen**, selecteer de groep **Gastgebruikers Contoso** en sla uw wijzigingen op.
3. Bij **Toewijzingsopties** kunt u de abonnementen bij de geselecteerde licenties in- en uitschakelen. Wanneer u een wijziging aanbrengt, moet u op **OK** klikken uw wijzigingen op te slaan.
4. Voltooi de toewijzing door onder aan het deelvenster **Licentie toewijzen** op **Toewijzen** te klikken.

## <a name="remove-guests-from-all-users-group"></a>Gastgebruikers verwijderen uit de groep Alle gebruikers

Mogelijk wilt u als beheerder uiteindelijk alle gastgebruikers per bedrijf toewijzen aan een afzonderlijke groep. U kunt nu ook de groep **alle gebruikers** wijzigen, zodat deze alleen is gereserveerd voor gebruikers in uw organisatie. Vervolgens kunt u aan deze groep apps en licenties toewijzen die specifiek zijn voor uw organisatie.

   ![De groep Alle gebruikers zo wijzigen dat er alleen leden overblijven](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Resources opschonen

**De groep Gastgebruikers verwijderen**

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een account dat de globale beheerder is voor uw organisatie.
2. Selecteer **Azure Active Directory** > **groepen**. Selecteer de groep **Gastgebruikers Contoso**, selecteer het weglatingsteken (...) en selecteer vervolgens **Verwijderen**. Wanneer u de groep verwijdert, worden alle toegewezen licenties verwijderd.

**De groep Alle gebruikers herstellen**
1. Selecteer **Azure Active Directory** > **groepen**. Selecteer de naam van de groep **Alle gebruikers** om de groep te openen.
1. Selecteer **Dynamisch-lidmaatschapregels**, verwijder alle tekst in de regel en selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:
> [!div class="checklist"]
> * Een groep gastgebruikers maken
> * Licenties toewijzen aan uw nieuwe groep
> * De groep Alle gebruikers zo wijzigen dat er alleen leden overblijven

Ga naar het volgende artikel voor meer informatie over de basisprincipes van groepslicenties
> [!div class="nextstepaction"]
> [Basisprincipes van groepslicenties](../fundamentals/active-directory-licensing-whatis-azure-portal.md)




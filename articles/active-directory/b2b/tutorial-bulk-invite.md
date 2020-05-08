---
title: Zelf studie voor het bulksgewijs uitnodigen van B2B-samenwerkings gebruikers-Azure AD
description: In deze zelfstudie leert u hoe u PowerShell en een CSV-bestand gebruikt voor het verzenden van bulk-uitnodigingen naar externe gebruikers van de Microsoft Azure AD B2B-samenwerking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f88b310bc00881e66ee8e8b5f2d40616d60315
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926908"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Zelfstudie: Bulksgewijs gebruikers uitnodigen voor Microsoft Azure AD B2B-samenwerking

Als u Azure Active Directory (Azure AD) B2B-samenwerking gebruikt om te werken met externe partners, kunt u tegelijkertijd meerdere gastgebruikers uitnodigen voor uw organisatie. In deze zelf studie leert u hoe u de Azure Portal kunt gebruiken om bulk uitnodigingen naar externe gebruikers te verzenden. Ga als volgt te werk:

> [!div class="checklist"]
> * Gebruik **bulksgewijze gebruikers** van de uitnodiging om een bestand met door komma's gescheiden waarden (. CSV) voor te bereiden met de gebruikers gegevens en de voor keuren voor uitnodigingen
> * Upload het CSV-bestand naar Azure AD
> * Controleer of de gebruikers zijn toegevoegd aan de map

Als u geen Azure Active Directory hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="understand-the-csv-template"></a>De CSV-sjabloon begrijpen

Down load en vul het CSV-sjabloon bulksgewijs uploaden in om Azure AD-gast gebruikers bulksgewijs te kunnen uitnodigen. De CSV-sjabloon die u downloadt, kan eruitzien als in dit voor beeld:

![Werk blad voor upload-en aanroep-outs waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>Structuur van CSV-sjabloon

De rijen in een gedownloade CSV-sjabloon zijn als volgt:

- **Versie nummer**: de eerste rij met het versie nummer moet worden opgenomen in de upload CSV.
- **Kolom koppen**: de indeling van de kolom koppen is &lt;de *item naam* &gt; [propertyname] &lt; *vereist of leeg*&gt;. Bijvoorbeeld `Email address to invite [inviteeEmail] Required`. Sommige oudere versies van de sjabloon hebben mogelijk een kleine variatie.
- **Voor beelden van rij**: we hebben in de sjabloon een rij met voor beelden van geldige waarden voor elke kolom opgenomen. U moet de voor beelden van de rij verwijderen en vervangen door uw eigen vermeldingen.

### <a name="additional-guidance"></a>Aanvullende richtlijnen

- De eerste twee rijen van de upload sjabloon mogen niet worden verwijderd of gewijzigd, of de upload kan niet worden verwerkt.
- De vereiste kolommen worden eerst weer gegeven.
- Het is niet raadzaam om nieuwe kolommen toe te voegen aan de sjabloon. Alle extra kolommen die u toevoegt, worden genegeerd en worden niet verwerkt.
- U wordt aangeraden de meest recente versie van de CSV-sjabloon zo vaak mogelijk te downloaden.

## <a name="prerequisites"></a>Vereisten

U moet twee of meer test e-mailaccounts hebben waarnaar u uitnodigingen kunt verzenden. De accounts moet van buiten uw organisatie zijn. U kunt elk type account gebruiken, met inbegrip van sociale accounts, bijvoorbeeld met een adres van gmail.com of outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Gast gebruikers bulksgewijs uitnodigen

1. Meld u aan bij de Azure Portal met een account dat een gebruikers beheerder in de organisatie is.
2. Selecteer **Azure Active Directory**in het navigatie deel venster.
3. Selecteer onder **beheren**de optie **gebruikers** > **bulksgewijs uitnodigen**.
4. Selecteer op de pagina **gebruikers bulksgewijs uitnodigen** de optie **downloaden** om een geldige. CSV-sjabloon met uitnodigings eigenschappen te verkrijgen.

    ![Knop downloaden in bulk-uitnodiging](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Open de CSV-sjabloon en voeg een regel toe voor elke gast gebruiker. De vereiste waarden zijn:

   * **E-mail adres dat moet worden uitgenodigd** : de gebruiker die een uitnodiging ontvangt

   * **Omleidings-URL** : de URL waarnaar de uitgenodigde gebruiker is doorgestuurd na acceptatie van de uitnodiging

    ![Voor beeld van een CSV-bestand met gast gebruikers ingevoerd](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Gebruik geen komma's in het **aangepaste uitnodigings bericht** omdat het bericht niet kan worden geparseerd.

6. Sla het bestand op.
7. Blader op de pagina **bulksgewijs uitnodigen gebruikers** onder **uw CSV-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart. 
8. Wanneer de bestands inhoud is gevalideerd, ziet u dat het **bestand is geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
9. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om te beginnen met de bulk bewerking van Azure waarmee de uitnodigingen worden toegevoegd. 
10. Als u de taak status wilt weer geven, selecteert u **hier Klik hier om de status van elke bewerking weer te geven**. U kunt ook **bulksgewijze bewerkings resultaten** selecteren in het gedeelte **activiteit** . Voor meer informatie over elk regel item in de bulk bewerking selecteert u de waarden onder de kolommen **# geslaagd**, **# fout**of **Totaal aantal aanvragen** . Als er fouten zijn opgetreden, worden de redenen voor de fout weer gegeven.

    ![Voor beeld van resultaten van een bulk bewerking](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Wanneer de taak is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

## <a name="verify-guest-users-in-the-directory"></a>Gast gebruikers in de adres lijst controleren

Controleer of de gast gebruikers die u hebt toegevoegd in de Directory aanwezig zijn in de Azure Portal of met behulp van Power shell.

### <a name="view-guest-users-in-the-azure-portal"></a>Gast gebruikers weer geven in de Azure Portal

1. Meld u aan bij de Azure Portal met een account dat een gebruikers beheerder in de organisatie is.
2. Selecteer **Azure Active Directory**in het navigatie deel venster.
3. Onder **Beheren**, selecteer **Gebruikers**.
4. Onder **weer geven**selecteert u **Alleen gast gebruikers** en controleert u of de gebruikers die u hebt toegevoegd, worden weer gegeven.

### <a name="view-guest-users-with-powershell"></a>Gast gebruikers weer geven met Power shell

Voer de volgende opdracht uit:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

De gebruikers die u hebt uitgenodigd, worden weer gegeven met een User Principal Name (UPN) in de notatie *EmailAddress*#EXT #\@*domein*. Bijvoorbeeld *lstokes_fabrikam. com # ext #\@contoso.onmicrosoft.com*, waarbij contoso.onmicrosoft.com de organisatie is van waaruit u de uitnodigingen hebt verzonden.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de test gebruikers accounts in de Directory verwijderen in de Azure Portal op de pagina gebruikers door het selectie vakje naast de gast gebruiker in te scha kelen en vervolgens **verwijderen**te selecteren. 

U kunt ook de volgende Power shell-opdracht uitvoeren om een gebruikers account te verwijderen:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Bijvoorbeeld: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u bulk-uitnodigingen naar gastgebruikers buiten uw organisatie verzonden. Vervolgens leert u hoe het proces voor uitnodiging inwisselen werkt.

> [!div class="nextstepaction"]
> [Meer informatie over het proces voor uitnodiging inwisselen in Microsoft Azure AD B2B-samenwerking](redemption-experience.md)

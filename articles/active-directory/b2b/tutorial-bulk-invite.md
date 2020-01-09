---
title: Zelf studie voor het bulksgewijs uitnodigen van B2B-samenwerkings gebruikers-Azure AD
description: In deze zelfstudie leert u hoe u PowerShell en een CSV-bestand gebruikt voor het verzenden van bulk-uitnodigingen naar externe gebruikers van de Microsoft Azure AD B2B-samenwerking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 9/19/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 493c60e8148b666ba1f514d303229b7a11c2e43e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425030"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Zelf studie: Azure AD B2B Collaboration-gebruikers (preview) bulksgewijs uitnodigen

|     |
| --- |
| In dit artikel wordt een open bare preview-functie van Azure Active Directory beschreven. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.|
|     |

> [!NOTE]
> Vanaf 12/22/2019 is de functie voor bulk-uitnodiging gebruikers (preview) tijdelijk uitgeschakeld.

Als u Azure Active Directory (Azure AD) B2B-samenwerking gebruikt om te werken met externe partners, kunt u tegelijkertijd meerdere gastgebruikers uitnodigen voor uw organisatie. In deze zelf studie leert u hoe u de Azure Portal kunt gebruiken om bulk uitnodigingen naar externe gebruikers te verzenden. Ga als volgt te werk:

> [!div class="checklist"]
> * Gebruik **gebruikers voor bulk-uitnodiging (preview)** om een bestand met door komma's gescheiden waarden (. CSV) voor te bereiden met de gebruikers gegevens en de voor keuren voor uitnodigingen
> * Upload het CSV-bestand naar Azure AD
> * Controleer of de gebruikers zijn toegevoegd aan de map

Als u geen Azure Active Directory hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 

## <a name="prerequisites"></a>Vereisten

U moet twee of meer test e-mailaccounts hebben waarnaar u uitnodigingen kunt verzenden. De accounts moet van buiten uw organisatie zijn. U kunt elk type account gebruiken, met inbegrip van sociale accounts, bijvoorbeeld met een adres van gmail.com of outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Gast gebruikers bulksgewijs uitnodigen

1. Meld u aan bij de Azure Portal met een account dat een gebruikers beheerder in de organisatie is.
2. Selecteer **Azure Active Directory**in het navigatie deel venster.
3. Selecteer onder **beheren**de optie **gebruikers** > **bulk-uitnodiging**.
4. Selecteer op de pagina **gebruikers bulksgewijs uitnodigen (preview)** de optie **downloaden** om een geldig CSV-bestand met de uitnodigings-eigenschappen te verkrijgen.

    ![Knop downloaden in bulk-uitnodiging](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Open het CSV-bestand en voeg een regel toe voor elke gast gebruiker. De vereiste waarden zijn:

   * **E-mail adres dat moet worden uitgenodigd** : de gebruiker die een uitnodiging ontvangt

   * **Omleidings-URL** : de URL waarnaar de uitgenodigde gebruiker is doorgestuurd na acceptatie van de uitnodiging

    ![Voor beeld van een CSV-bestand met gast gebruikers ingevoerd](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Gebruik geen komma's in het **aangepaste uitnodigings bericht** omdat het bericht niet kan worden geparseerd.

6. Sla het bestand op.
7. Blader op de pagina **bulksgewijs uitnodigen gebruikers (preview)** onder **uw CSV-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart. 
8. Wanneer de bestands inhoud is gevalideerd, ziet u dat het **bestand is geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
9. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om te beginnen met de bulk bewerking van Azure waarmee de uitnodigingen worden toegevoegd. 
10. Als u de taak status wilt weer geven, selecteert u **hier Klik hier om de status van elke bewerking weer te geven**. U kunt ook **bulk bewerkings resultaten selecteren (preview)** in het gedeelte **activiteit** . Voor meer informatie over elk regel item in de bulk bewerking selecteert u de waarden onder de kolommen **# geslaagd**, **# fout**of **Totaal aantal aanvragen** . Als er fouten zijn opgetreden, worden de redenen voor de fout weer gegeven.

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

De gebruikers die u hebt uitgenodigd, worden weer gegeven met een user principal name (UPN) in de notatie *emailaddress*#EXT #\@*domein*. Bijvoorbeeld *lstokes_fabrikam. com # ext #\@contoso.onmicrosoft.com*, waarbij contoso.onmicrosoft.com de organisatie is van waaruit u de uitnodigingen hebt verzonden.

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

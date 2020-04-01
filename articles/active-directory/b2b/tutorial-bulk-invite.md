---
title: Zelfstudie voor bulkuitnodigende B2B-samenwerkingsgebruikers - Azure AD
description: In deze zelfstudie leert u hoe u PowerShell en een CSV-bestand gebruikt voor het verzenden van bulk-uitnodigingen naar externe gebruikers van de Microsoft Azure AD B2B-samenwerking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 2/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: c429648adeb0c81799bff2dca1650de965395a60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77166442"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users-preview"></a>Zelfstudie: Gebruikers van Azure AD B2B-samenwerkingsgebruikers uitnodigen (voorbeeld)

|     |
| --- |
| In dit artikel wordt een openbare preview-functie van Azure Active Directory beschreven. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over voorvertoningen.|
|     |

> [!NOTE]
> Per 12/22/2019 is de functie Bulk invite users (Preview) tijdelijk uitgeschakeld.
> Er is momenteel geen datum bekend waarop deze functie opnieuw wordt ingeschakeld in de Azure-portal. Als u gastgebruikers in bulk wilt uitnodigen met PowerShell, raadpleegt u de [zelfstudie voor B2B-bulkuitnodigingen](bulk-invite-powershell.md) of de [B2B-code en PowerShell-voorbeelden.](code-samples.md)

Als u Azure Active Directory (Azure AD) B2B-samenwerking gebruikt om te werken met externe partners, kunt u tegelijkertijd meerdere gastgebruikers uitnodigen voor uw organisatie. In deze zelfstudie leert u hoe u de Azure-portal gebruiken om bulkuitnodigingen naar externe gebruikers te verzenden. Ga als volgt te werk:

> [!div class="checklist"]
> * **Bulkgebruikers uitnodigen (Preview)** gebruiken om een door komma's gescheiden waardebestand (.csv) voor te bereiden met de gebruikersinformatie en uitnodigingsvoorkeuren
> * Het CSV-bestand uploaden naar Azure AD
> * Controleer of de gebruikers zijn toegevoegd aan de map

Als u geen Azure Active Directory hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint. 

## <a name="prerequisites"></a>Vereisten

U moet twee of meer test e-mailaccounts hebben waarnaar u uitnodigingen kunt verzenden. De accounts moet van buiten uw organisatie zijn. U kunt elk type account gebruiken, met inbegrip van sociale accounts, bijvoorbeeld met een adres van gmail.com of outlook.com.

## <a name="invite-guest-users-in-bulk"></a>Gasten in bulk uitnodigen

1. Meld u aan bij de Azure-portal met een account dat een gebruikersbeheerder in de organisatie is.
2. Selecteer Azure Active **Directory**in het navigatiedeelvenster .
3. Selecteer **Onder Beheren**de optie**Gebruikersbulkuitnodiging** **Users** > .
4. Selecteer op de pagina **Bulk-invite-gebruikers (Preview)** de optie **Downloaden** om een geldig CSV-bestand met uitnodigingseigenschappen op te halen.

    ![Knop Downloaden van bulkuitnodigingen](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Open het CSV-bestand en voeg een regel toe voor elke gastgebruiker. Vereiste waarden zijn:

   * **E-mailadres om uit te nodigen** - de gebruiker die een uitnodiging ontvangt

   * **Omleidingsurl** - de URL waarnaar de uitgenodigde gebruiker wordt doorgestuurd nadat de uitnodiging is geaccepteerd

    ![Voorbeeld van een CSV-bestand met gastgebruikers ingevoerd](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Gebruik geen komma's in het **aangepaste uitnodigingsbericht** omdat ze voorkomen dat het bericht wordt ontleed.

6. Sla het bestand op.
7. Blader op de pagina **Bulk invite users (Preview)** onder **Uw csv-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert, wordt de validatie van het CSV-bestand gestart. 
8. Wanneer de inhoud van het bestand is gevalideerd, wordt **bestand geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak indienen.
9. Wanneer uw bestand de validatie doorstaat, selecteert u **Verzenden** om de Azure-bulkbewerking te starten die de uitnodigingen toevoegt. 
10. Als u de taakstatus wilt weergeven, selecteert **u Klik hier om de status van elke bewerking weer te geven.** U ook **resultaten van de bulkbewerking (Voorbeeld)** selecteren in de sectie **Activiteit.** Selecteer de waarden onder de kolommen **# Succes**, # **Mislukt**of **Totaal aantal aanvragen** voor meer informatie over elk regelitem in de bulkbewerking. Als er fouten zijn opgetreden, worden de redenen voor de fout weergegeven.

    ![Voorbeeld van resultaten van bulkbewerking](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Wanneer de taak is voltooid, ziet u een melding dat de bulkbewerking is geslaagd.

## <a name="verify-guest-users-in-the-directory"></a>Gastgebruikers in de map verifiëren

Controleer of de gastgebruikers die u hebt toegevoegd, in de map aanwezig zijn in de Azure-portal of met PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Gastgebruikers weergeven in de Azure-portal

1. Meld u aan bij de Azure-portal met een account dat een gebruikersbeheerder in de organisatie is.
2. Selecteer Azure Active **Directory**in het navigatiedeelvenster .
3. Onder **Beheren**, selecteer **Gebruikers**.
4. Selecteer **onder Weergeven**alleen **gastgebruikers** en controleer of de gebruikers die u hebt toegevoegd, worden weergegeven.

### <a name="view-guest-users-with-powershell"></a>Gastgebruikers weergeven met PowerShell

Voer de volgende opdracht uit:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

U ziet de gebruikers die u hebt uitgenodigd, met een gebruikersnaam (UPN)\@in de indeling *e-mailadres*#EXT#*domein.* Bijvoorbeeld *\@lstokes_fabrikam.com#EXT# contoso.onmicrosoft.com*, waar contoso.onmicrosoft.com de organisatie is van waaruit u de uitnodigingen hebt verzonden.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u de gebruikersaccounts van de test in de map in de Azure-portal op de pagina Gebruikers verwijderen door het selectievakje naast de gastgebruiker in te schakelen en vervolgens **Verwijderen**te selecteren. 

U ook de volgende PowerShell-opdracht uitvoeren om een gebruikersaccount te verwijderen:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Bijvoorbeeld: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u bulk-uitnodigingen naar gastgebruikers buiten uw organisatie verzonden. Vervolgens leert u hoe het proces voor uitnodiging inwisselen werkt.

> [!div class="nextstepaction"]
> [Meer informatie over het proces voor uitnodiging inwisselen in Microsoft Azure AD B2B-samenwerking](redemption-experience.md)

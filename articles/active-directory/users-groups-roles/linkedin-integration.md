---
title: Beheerderstoestemming voor LinkedIn-accountverbindingen - Azure AD | Microsoft Documenten
description: Hier wordt uitgelegd hoe u LinkedIn-integratieaccountverbindingen in Microsoft-apps in Azure Active Directory in- of uitschakelt
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54e3821d269d11397ec4f9f5833e33ac6b555abc
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755113"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>LinkedIn-accountverbindingen integreren in Azure Active Directory

U gebruikers in uw organisatie toegang geven tot hun LinkedIn-verbindingen in sommige Microsoft-apps. Er worden geen gegevens gedeeld totdat gebruikers toestemming geven om hun accounts met elkaar te verbinden. U uw organisatie integreren in het Azure Active [Directory-beheercentrum](https://aad.portal.azure.com)(Azure AD).

> [!IMPORTANT]
> De instelling Voor LinkedIn-accountverbindingen wordt momenteel uitgerold naar Azure AD-organisaties. Wanneer deze wordt uitgerold naar uw organisatie, is deze standaard ingeschakeld.
>
> Uitzonderingen:
>
> * De instelling is niet beschikbaar voor klanten die Microsoft Cloud gebruiken voor de Amerikaanse overheid, Microsoft Cloud Germany of Azure en Office 365 die worden beheerd door 21Vianet in China.
> * De instelling is standaard uitgeschakeld voor huurders die in Duitsland zijn ingericht. Houd er rekening mee dat de instelling niet beschikbaar is voor klanten die Microsoft Cloud Germany gebruiken.
> * De instelling is standaard uitgeschakeld voor huurders die in Frankrijk zijn ingericht.
>
> Zodra LinkedIn-accountverbindingen zijn ingeschakeld voor uw organisatie, werken de accountverbindingen nadat gebruikers toestemming hebben gegeven voor apps die namens hen toegang hebben tot bedrijfsgegevens. Zie [De toegang van een gebruiker tot een toepassing verwijderen](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)voor informatie over de instelling voor gebruikerstoestemming.

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>LinkedIn-accountverbindingen inschakelen in de Azure-portal

U LinkedIn-accountverbindingen inschakelen voor alleen de gebruikers die u toegang wilt hebben, van uw hele organisatie tot alleen geselecteerde gebruikers in uw organisatie.

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com/) met een account dat een globale beheerder is voor de Azure AD-organisatie.
1. Selecteer **Gebruikers**.
1. Selecteer **op** de pagina Gebruikers de optie **Gebruikersinstellingen**.
1. Laat gebruikers onder **LinkedIn-accountverbindingen**hun accounts verbinden om toegang te krijgen tot hun LinkedIn-verbindingen binnen sommige Microsoft-apps. Er worden geen gegevens gedeeld totdat gebruikers toestemming geven om hun accounts met elkaar te verbinden.

    * Selecteer **Ja** om de service in te schakelen voor alle gebruikers in uw organisatie
    * Geselecteerde **groep selecteren** om de service in te schakelen voor slechts een groep geselecteerde gebruikers in uw organisatie
    * Selecteer **Nee** om toestemming in te trekken van alle gebruikers in uw organisatie

    ![LinkedIn-accountverbindingen integreren in de organisatie](./media/linkedin-integration/linkedin-integration.png)

1. Wanneer u klaar bent, selecteert u **Opslaan** om uw instellingen op te slaan.

> [!Important]
> LinkedIn-integratie is niet volledig ingeschakeld voor uw gebruikers totdat ze toestemming geven om hun accounts te verbinden. Er worden geen gegevens gedeeld wanneer u accountverbindingen inschakelt voor uw gebruikers.

### <a name="assign-selected-users-with-a-group"></a>Geselecteerde gebruikers toewijzen met een groep

We hebben de optie 'Geselecteerd' vervangen die een lijst met gebruikers opgeeft met de optie om een groep gebruikers te selecteren, zodat u de mogelijkheid inschakelen om LinkedIn- en Microsoft-accounts voor één groep te verbinden in plaats van veel individuele gebruikers. Als u geen LinkedIn-accountverbindingen hebt ingeschakeld voor geselecteerde individuele gebruikers, hoeft u niets te doen. Als u eerder LinkedIn-accountverbindingen voor geselecteerde individuele gebruikers hebt ingeschakeld, moet u het als:

1. Download de huidige lijst met individuele gebruikers
1. De momenteel ingeschakelde individuele gebruikers naar een groep verplaatsen
1. Gebruik de groep van de vorige groep als de geselecteerde groep in de instelling LinkedIn-accountverbindingen in het Azure AD-beheercentrum.

> [!NOTE]
> Zelfs als u uw momenteel geselecteerde individuele gebruikers niet naar een groep verplaatst, kunnen ze nog steeds LinkedIn-gegevens in Microsoft-apps zien.

### <a name="move-currently-selected-users-to-a-group"></a>Momenteel geselecteerde gebruikers naar een groep verplaatsen

1. Maak een CSV-bestand van de gebruikers die zijn geselecteerd voor LinkedIn-accountverbindingen.
1. Meld u aan bij Microsoft 365 met uw beheerdersaccount.
1. Start PowerShell.
1. De Azure AD-module installeren door`Install-Module AzureAD`
1. Voer het volgende script uit:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Zie [LinkedIn-accountverbindingen inschakelen in de Azure-portal](#enable-linkedin-account-connections-in-the-azure-portal)als de geselecteerde groep in de instelling LinkedIn-accountverbindingen in het Azure AD-beheercentrum.

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Groepsbeleid gebruiken om LinkedIn-accountverbindingen in te schakelen

1. De [beheersjabloonbestanden van Office 2016 downloaden (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030)
1. Haal de **ADMX-bestanden** eruit en kopieer ze naar uw centrale winkel.
1. Open Groepsbeleidsbeheer.
1. Maak een groepsbeleidsobject met de volgende instelling:**Beheersjablonen** >  **voor gebruikersconfiguratie** > **Microsoft Office 2016** > **Diverse** > **LinkedIn-functies weergeven in Office-toepassingen**.
1. Selecteer **Ingeschakeld** of **uitgeschakeld**.
  
   Status | Effect
   ------ | ------
   **Ingeschakeld** | De instelling **LinkedIn-functies weergeven in Office-toepassingen** in Office 2016-opties is ingeschakeld. Gebruikers in uw organisatie kunnen LinkedIn-functies gebruiken in hun Office 2016-toepassingen.
   **Handicap** | De **instelling LinkedIn-functies weergeven in Office-toepassingen** in Office 2016-opties is uitgeschakeld en eindgebruikers kunnen deze instelling niet wijzigen. Gebruikers in uw organisatie kunnen LinkedIn-functies niet gebruiken in hun Office 2016-toepassingen.

Dit groepsbeleid is alleen van invloed op Office 2016-apps voor een lokale computer. Als gebruikers LinkedIn uitschakelen in hun Office 2016-apps, kunnen ze nog steeds LinkedIn-functies zien in Office 365.

## <a name="next-steps"></a>Volgende stappen

* [Toestemming van gebruikers en het delen van gegevens voor LinkedIn](linkedin-user-consent.md)

* [LinkedIn-informatie en -functies in uw Microsoft-apps](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn helpcentrum](https://www.linkedin.com/help/linkedin)

* [Uw huidige LinkedIn-integratieinstelling weergeven in de Azure-portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)

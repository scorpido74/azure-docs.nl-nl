---
title: Zelf studie`:` een beheerde identiteit gebruiken om toegang te krijgen tot Azure Key Vault-Windows-Azure AD
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het gebruiken van een door het Windows-VM-systeem toegewezen beheerde identiteit om toegang te krijgen tot Azure Key Vault.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a5c2c101b934f07ab85082e726cb8e1341e5d0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888723"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Zelfstudie: een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot Azure Key Vault met een door het systeem toegewezen beheerde identiteit voor een virtuele Windows-machine (VM). Key Vault fungeert als een bootstrap en maakt het mogelijk voor uw clienttoepassing om met het geheim toegang te krijgen tot resources die niet zijn beveiligd met Azure Active Directory (AD). Managed Service Identity's worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen. 

Procedures voor:


> [!div class="checklist"]
> * Uw virtuele machine toegang verlenen tot een geheim dat is opgeslagen in een sleutelkluis 
> * Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee het geheim uit Key Vault ophalen 

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-access"></a>Toegang verlenen  
 
In deze sectie wordt beschreven hoe u uw VM toegang verleent tot een geheim dat is opgeslagen in een Key Vault. Met behulp van beheerde identiteiten voor Azure-resources kan uw code toegangstokens ophalen voor verificatie bij resources die ondersteuning bieden voor Microsoft Azure AD-verificatie.  Niet alle Azure-Services bieden echter ondersteuning voor Azure AD-verificatie. Als u beheerde identiteiten wilt gebruiken voor Azure-resources met die services, slaat u de service referenties op in Azure Key Vault en gebruikt u de beheerde identiteit van de virtuele machine om toegang te krijgen tot Key Vault om de referenties op te halen. 

Eerst moeten we een sleutelkluis maken en de door het systeem toegewezen beheerde identiteit van onze VM toegang tot de sleutelkluis verlenen.   

1. Selecteer boven aan de linkernavigatiebalk **Een resource maken** > **Beveiliging en identiteit** > **Key Vault**.  
2. Geef een **naam** op voor de nieuwe sleutelkluis. 
3. Zoek de sleutelkluis in hetzelfde abonnement en dezelfde resourcegroep als de virtuele machine die u eerder hebt gemaakt. 
4. Selecteer **Toegangsbeleid** en klik op de knop **Nieuwe toevoegen**. 
5. Selecteer **Geheimenbeheer** in Configureren met sjabloon. 
6. Kies **Principal selecteren**, en voer in het zoekveld de naam in van de virtuele machine die u eerder hebt gemaakt.  Selecteer de virtuele machine in de lijst met resultaten en klik op **selecteren**. 
7. Klik op **OK** om het nieuwe toegangsbeleid toe te voegen en klik op **OK** om de selectie van het toegangsbeleid te voltooien. 
8. Klik op **Maken** om het maken van de sleutelkluis te voltooien. 

    ![Alt-tekst voor afbeelding](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Voeg vervolgens een geheim toe aan de sleutelkluis, zodat u het geheim later kunt ophalen met behulp van code die wordt uitgevoerd op uw virtuele machine: 

1. Selecteer **Alle resources** en zoek en selecteer de sleutelkluis die u hebt gemaakt. 
2. Selecteer **Geheimen** en klik op **Toevoegen**. 
3. Selecteer **Handmatig** in **Opties uploaden**. 
4. Voer een naam en een waarde in voor de geheime sleutel.  De waarde kan alles wat u wilt. 
5. Laat de activeringsdatum en vervaldatum leeg en laat **Ingeschakeld** ingesteld staan op **Ja**. 
6. Klik op **Maken** om het geheim te maken. 
 
## <a name="get-an-access-token"></a>Een toegangstoken opvragen  

In deze sectie wordt beschreven hoe u een toegangs token kunt ophalen met behulp van de VM-identiteit en hoe u het kunt ophalen uit de Key Vault. Als PowerShell 4.3.1 of hoger niet is geïnstalleerd, moet u [de meest recente versie downloaden en installeren](https://docs.microsoft.com/powershell/azure/overview).

Eerst gebruiken we de door het systeem toegewezen beheerde identiteit van de VM om een toegangstoken op te halen voor verificatie bij Key Vault:
 
1. Navigeer in Azure Portal naar **Virtuele machines**, ga naar uw virtuele Windows-machine en klik op de pagina **Overzicht** op **Verbinden**.
2. Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de **virtuele Windows-machine**.  
3. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u PowerShell in de externe sessie.  
4. Voer in PowerShell een Invoke-WebRequest-opdracht uit op de tenant om het token voor de lokale host in de specifieke poort voor de virtuele machine op te halen.  

    De PowerShell-aanvraag:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Extraheer vervolgens de volledige reactie, die is opgeslagen als een tekenreeks in JSON-indeling (JavaScript Object Notation) in het object $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Extraheer vervolgens het toegangstoken uit het antwoord.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Gebruik ten slotte de Invoke-WebRequest-opdracht van Powershell om het geheim op te halen dat u eerder in de sleutelkluis hebt gemaakt, waarbij u het toegangstoken doorgeeft in de autorisatie-header.  U hebt de URL van uw Key Vault nodig. Deze bevindt zich in de sectie **Essentials** van de pagina **overzicht** van de Key Vault.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    De reactie ziet er ongeveer als volgt uit: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Zodra u het geheim hebt opgehaald uit de sleutelkluis, kunt u deze gebruiken om te verifiëren bij een service die een gebruikersnaam en wachtwoord vereist. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een door het Windows-VM-systeem toegewezen beheerde identiteit kunt gebruiken voor toegang tot Azure Key Vault.  Zie voor meer informatie over Azure Key Vault:

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-overview)

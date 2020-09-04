---
title: Zelfstudie`:` Een beheerde identiteit gebruiken voor toegang tot Azure Key Vault - Linux - Azure AD
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het gebruiken van een door het Linux-VM-systeem toegewezen beheerde identiteit om toegang te krijgen tot Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6756d66f176314ad5abd0c94f7bdf96c42a460a4
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255306"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Zelfstudie: een door het Linux-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe u toegang krijgt tot Azure Key Vault met een door het systeem toegewezen beheerde identiteit voor een virtuele Linux-machine (VM). Key Vault fungeert als een bootstrap en maakt het mogelijk voor uw clienttoepassing om met het geheim toegang te krijgen tot resources die niet zijn beveiligd met Azure Active Directory (AD). Beheerde entiteiten voor Azure-resources worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Microsoft Azure AD-verificatie ondersteunen, zonder referenties in code te hoeven invoegen. 

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Uw virtuele machine toegang verlenen tot een geheim dat is opgeslagen in een sleutelkluis 
> * Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee het geheim uit de sleutelkluis ophalen 
 
## <a name="prerequisites"></a>Vereisten

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Uw virtuele machine toegang verlenen tot een geheim dat is opgeslagen in een sleutelkluis  

Met behulp van beheerde service-identiteiten voor Azure-resources kan uw code toegangstokens ophalen voor verificatie bij resources die ondersteuning bieden voor Azure Active Directory-verificatie.  Niet alle Azure-services bieden echter ondersteuning voor Azure AD-verificatie.Als u bij deze services beheerde identiteiten voor Azure-resources wilt gebruiken, slaat u de servicereferenties op in Azure Key Vault en gebruikt u beheerde identiteiten voor Azure-resources om toegang te krijgen tot Key Vault en de referenties op te halen. 

Eerst moeten we een sleutelkluis maken en de door het systeem toegewezen beheerde identiteit van onze VM toegang tot de sleutelkluis verlenen.   

1. Selecteer boven aan de linkernavigatiebalk **Een resource maken** > **Beveiliging en identiteit** > **Key Vault**.  
2. Geef een **naam** op voor de nieuwe sleutelkluis. 
3. Zoek de sleutelkluis in hetzelfde abonnement en dezelfde resourcegroep als de virtuele machine die u eerder hebt gemaakt. 
4. Selecteer **Toegangsbeleid** en klik op de knop **Nieuwe toevoegen**. 
5. Selecteer **Geheimenbeheer** in Configureren met sjabloon. 
6. Kies **Principal selecteren**, en voer in het zoekveld de naam in van de virtuele machine die u eerder hebt gemaakt.  Selecteer de virtuele machine in de lijst met resultaten en klik op **Selecteren**. 
7. Klik op **OK** om het nieuwe toegangsbeleid toe te voegen en klik op **OK** om de selectie van het toegangsbeleid te voltooien. 
8. Klik op **Maken** om het maken van de sleutelkluis te voltooien. 

    ![Alt-tekst voor afbeelding](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

Voeg vervolgens een geheim toe aan de sleutelkluis, zodat u het geheim later kunt ophalen met behulp van code die wordt uitgevoerd op uw virtuele machine: 

1. Selecteer **Alle resources** en zoek en selecteer de sleutelkluis die u hebt gemaakt. 
2. Selecteer **Geheimen** en klik op **Toevoegen**. 
3. Selecteer **Handmatig** in **Opties uploaden**. 
4. Voer een naam en een waarde in voor de geheime sleutel.  De waarde mag elke gewenste waarde zijn. 
5. Laat de activeringsdatum en vervaldatum leeg en laat **Ingeschakeld** ingesteld staan op **Ja**. 
6. Klik op **Maken** om het geheim te maken. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee het geheim uit de sleutelkluis ophalen  

U hebt een SSH-client nodig om deze stappen uit te voeren.Als u Windows gebruikt, kunt u de SSH-client in het [Windows-subsysteem voor Linux](/windows/wsl/about) gebruiken. Zie [De sleutels van uw SSH-client gebruiken onder Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md) of [Een sleutelpaar met een openbare SSH-sleutel en een privé-sleutel maken en gebruiken voor virtuele Linux-machines in Azure](../../virtual-machines/linux/mac-create-ssh-keys.md) als u hulp nodig hebt bij het configureren van de sleutels van uw SSH-client.
 
1. Navigeer in de portal naar de virtuele Linux-machine en klik in het **overzicht** op **Verbinden**. 
2. **Maak verbinding** met de virtuele machine met de SSH-client van uw keuze. 
3. Dien in het terminalvenster met behulp van CURL een aanvraag in bij het eindpunt van de lokaal beheerde identiteiten voor Azure-resources om een toegangstoken voor Azure Key Vault op te halen.  
 
    Hieronder ziet u de CURL-aanvraag voor het toegangstoken.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    De reactie bevat het toegangstoken dat u nodig hebt voor toegang tot Resource Manager. 
    
    Reactie:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    U kunt dit toegangstoken gebruiken voor verificatie bij Azure Key Vault.Met de volgende CURL-aanvraag wordt een geheim uit Key Vault gelezen met behulp van CURL en de REST-API van Key Vault.  U hebt de URL van uw sleutelkluis nodig. Deze vindt u in de sectie **Essentials** van de pagina **Overzicht** van de sleutelkluis.U hebt ook het toegangstoken nodig dat u hebt verkregen in de vorige aanroep. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    De reactie ziet er ongeveer als volgt uit: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Zodra u het geheim hebt opgehaald uit de sleutelkluis, kunt u deze gebruiken om te verifiëren bij een service die een gebruikersnaam en wachtwoord vereist.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een door het Linux-VM-systeem toegewezen beheerde identiteit kunt gebruiken voor toegang tot Azure Key Vault.  Zie voor meer informatie over Azure Key Vault:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)
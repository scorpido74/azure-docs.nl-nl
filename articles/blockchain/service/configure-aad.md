---
title: Azure Active Directory-toegang configureren - Azure Blockchain-service
description: Azure Blockchain-service configureren met Azure Active Directory-toegang
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455867"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Azure Active Directory-toegang configureren voor Azure Blockchain-service

In dit artikel leert u hoe u toegang verleent en verbinding maakt met Azure Blockchain Service-knooppunten met azure active directory(Azure AD) gebruikers-, groeps- of toepassings-id's.

Azure AD biedt cloudgebaseerd identiteitsbeheer en stelt u in staat om één identiteit te gebruiken voor een hele onderneming en toegang te krijgen tot toepassingen in Azure. Azure Blockchain Service is geïntegreerd met Azure AD en biedt voordelen zoals ID-federatie, single sign-on en multi-factor authenticatie.

## <a name="prerequisites"></a>Vereisten

* [Een blockchain-lid maken met behulp van de Azure-portal](create-member.md)

## <a name="grant-access"></a>Toegang verlenen

U toegang verlenen op zowel lidniveau als knooppuntniveau. Het verlenen van toegangsrechten op lidniveau verleent op zijn beurt toegang tot alle knooppunten onder het lid.

### <a name="grant-member-level-access"></a>Toegang tot lidniveau verlenen

Om toegangstoestemming te verlenen op lidniveau.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Navigeer naar **Toegangsbesturingselement (IAM) > Voeg > roltoewijzing toevoegen toe.**
1. Selecteer de rol **Van Toegang voor het Knooppunt voor Blockchain-leden (Preview)** en voeg het Azure AD ID-object toe waartoe u toegang wilt verlenen. Azure AD ID-object kan zijn:

    | Azure AD-object | Voorbeeld |
    |-----------------|---------|
    | Azure AD-gebruiker   | `kim@contoso.onmicrosoft.com` |
    | Azure AD-groep  | `sales@contoso.onmicrosoft.com` |
    | Toepassings-id  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Roltoewijzing toevoegen](./media/configure-aad/add-role-assignment.png)

1. Selecteer **Opslaan**.

### <a name="grant-node-level-access"></a>Toegang tot knooppuntniveau verlenen

U toegang verlenen op knooppuntniveau door te navigeren naar knooppuntbeveiliging en op de knooppuntnaam te klikken die u toegang wilt verlenen.

Selecteer de rol Van Toegang voor het Knooppunt voor Blockchain-leden (Preview) en voeg het Azure AD ID-object toe waartoe u toegang wilt verlenen.

Zie [Transactieknooppunten voor Azure Blockchain Service configureren](configure-transaction-nodes.md#azure-active-directory-access-control)voor meer informatie .

## <a name="connect-using-azure-blockchain-connector"></a>Verbinding maken met Azure Blockchain Connector

Download of kloon de [Azure Blockchain Connector van GitHub.](https://github.com/Microsoft/azure-blockchain-connector/)

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

De sectie Snelstart in het **leesthema** volgen om de connector van de broncode te bouwen.

### <a name="connect-using-an-azure-ad-user-account"></a>Verbinding maken met een Azure AD-gebruikersaccount

1. Voer de volgende opdracht uit om te verifiëren met een Azure AD-gebruikersaccount. MyAADDirectory \<\> vervangen door een Azure AD-domein. Bijvoorbeeld `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD vraagt om referenties.
1. Meld u aan met uw gebruikersnaam en wachtwoord.
1. Bij succesvolle verificatie maakt uw lokale proxy verbinding met uw blockchain-knooppunt. U uw Geth-client nu koppelen aan het lokale eindpunt.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Verbinding maken met een toepassings-id

Veel toepassingen verifiëren met Azure AD met behulp van een toepassings-id in plaats van een Azure AD-gebruikersaccount.

Als u verbinding wilt maken met uw knooppunt met een toepassings-id, vervangt u **aadauthcode** door **aadclient.**

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parameter | Beschrijving |
|-----------|-------------|
| tenant-id | Azure AD-domein, bijvoorbeeld,`yourdomain.onmicrosoft.com`
| client-id | Client-id van de geregistreerde toepassing in Azure AD
| klantgeheim | Clientgeheim van de geregistreerde toepassing in Azure AD

Zie De portal gebruiken om een Azure [AD-toepassing en serviceprincipal te maken waarmee toegang tot resources kan worden gemaakt](../../active-directory/develop/howto-create-service-principal-portal.md) voor meer informatie over het registreren van een toepassing in Azure AD.

### <a name="connect-a-mobile-device-or-text-browser"></a>Een mobiel apparaat of tekstbrowser verbinden

Voor een mobiel apparaat of browser op basis van tekst waar het pop-upscherm azure AD-verificatie niet mogelijk is, genereert Azure AD een eenmalige toegangscode. U de toegangscode kopiëren en doorgaan met Azure AD-verificatie in een andere omgeving.

Als u de toegangscode wilt genereren, vervangt u **aadauthcode** door **aaddevice.** MyAADDirectory \<\> vervangen door een Azure AD-domein. Bijvoorbeeld `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Blockchain Service-beveiliging](data-security.md)voor meer informatie over gegevensbeveiliging in Azure Blockchain Service.
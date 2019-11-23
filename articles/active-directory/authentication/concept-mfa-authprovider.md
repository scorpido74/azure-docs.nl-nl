---
title: Azure Multi-Factor Auth Providers - Azure Active Directory
description: When should you use an Auth Provider with Azure MFA?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d4b89f7416847e01cad8cb4f9bc52248d09170d
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382007"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>When to use an Azure Multi-Factor Authentication Provider

Verificatie in twee stappen is standaard beschikbaar voor globale beheerders van Azure Active Directory- en Office 365-gebruikers. Als u echter wilt profiteren van [geavanceerde functies](howto-mfa-mfasettings.md), moet u de volledige versie van Azure Multi-Factor Authentication (MFA) aanschaffen.

An Azure Multi-Factor Auth Provider is used to take advantage of features provided by Azure Multi-Factor Authentication for users who **do not have licenses**.

> [!NOTE]
> Effective September 1st, 2018 new auth providers may no longer be created. Existing auth providers may continue to be used and updated, but migration is no longer possible. Multi-factor authentication will continue to be available as a feature in Azure AD Premium licenses.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Caveats related to the Azure MFA SDK

Note the SDK has been deprecated and will only continue to work until November 14, 2018. Na deze datum kan de SDK niet meer worden aangeroepen.

## <a name="what-is-an-mfa-provider"></a>Wat is een MFA-provider?

There are two types of Auth providers, and the distinction is around how your Azure subscription is charged. De optie Per verificatie berekent het aantal verificaties die in een maand op uw tenant worden uitgevoerd. Deze optie wordt aanbevolen als u een aantal gebruikers hebt die slechts sporadisch verifiëren. De optie Per gebruiker berekent het aantal personen in uw tenant die in een maand verificatie in twee stappen uitvoeren. Deze optie wordt aanbevolen als u een aantal gebruikers met licenties hebt, maar MFA wilt uitbreiden naar meer gebruikers buiten de restricties van uw licentie.

## <a name="manage-your-mfa-provider"></a>Uw MFA-provider beheren

U kunt het gebruiksmodel (per ingeschakelde gebruiker of per verificatie) niet wijzigen nadat een MFA-provider is gemaakt.

If you purchased enough licenses to cover all users that are enabled for MFA, you can delete the MFA provider altogether.

Als de MFA-provider niet is gekoppeld aan een Azure AD-tenant, of als u de nieuwe MFA-provider koppelt aan een andere Azure AD-tenant, worden de gebruikersinstellingen en configuratieopties niet overgedragen. Also, existing Azure MFA Servers need to be reactivated using activation credentials generated through the MFA Provider. Reactivating the MFA Servers to link them to the MFA Provider doesn't impact phone call and text message authentication, but mobile app notifications stop working for all users until they reactivate the mobile app.

### <a name="removing-an-authentication-provider"></a>Removing an authentication provider

> [!CAUTION]
> There is no confirmation when deleting an authentication provider. Selecting **Delete** is a permanent process.

Authentication providers can be found in the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers**. Click on listed providers to see details and configurations associated with that provider.

Before removing an authentication provider, take note of any customized settings configured in your provider. Decide what settings need to be migrated to general MFA settings from your provider and complete the migration of those settings. 

Azure MFA Servers linked to providers will need to be reactivated using credentials generated under **Azure portal** > **Azure Active Directory** > **MFA** > **Server settings**. Before reactivating, the following files must be deleted from the `\Program Files\Multi-Factor Authentication Server\Data\` directory on Azure MFA Servers in your environment:

- caCert
- cert
- groupCACert
- groupKey
- groupName
- licenseKey
- pkey

![Delete an auth provider from the Azure portal](./media/concept-mfa-authprovider/authentication-provider-removal.png)

When you have confirmed that all settings have been migrated, you can browse to the **Azure portal** > **Azure Active Directory** > **MFA** > **Providers** and select the ellipses **...** and select **Delete**.

> [!WARNING]
> Deleting an authentication provider will delete any reporting information associated with that provider. You may want to save activity reports before deleting your provider.

> [!NOTE]
> Users with older versions of the Microsoft Authenticator app and Azure MFA Server may need to re-register their app.

## <a name="next-steps"></a>Volgende stappen

[Multi-Factor Authentication-instellingen configureren](howto-mfa-mfasettings.md)

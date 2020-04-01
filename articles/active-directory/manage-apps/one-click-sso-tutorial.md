---
title: SSO-configuratie (met één klik) van uw Azure Marketplace-toepassing | Microsoft Documenten
description: Stappen voor configuratie met één klik voor Uw toepassing vanuit azure marketplace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e0416991-4b5d-4b18-89bb-91b6070ed3ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a83d27af4fd783b95c53ef3a9169cb72bfc29d34
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67872431"
---
# <a name="one-click-app-configuration-of-single-sign-on"></a>Configuratie van een app met één klik van één aanmelding

 In deze zelfstudie leert u hoe u sso-toepassingen (met één klik, enkele aanmeldingsconfiguratie) uitvoert voor SAML-ondersteunende Azure Active Directory-toepassingen (Azure AD) vanuit de Azure Marketplace.

## <a name="introduction-to-one-click-sso"></a>Inleiding tot SSO met één klik

De SSO-functie met één klik is ontworpen om eenmalige aanmelding te configureren voor Azure Marketplace-apps die het SAML-protocol ondersteunen. Met deze optie u op de configuratiepagina van Azure AD-sSO de Azure AD-metagegevens aan de toepassingszijde automatisch configureren. Op deze manier u SSO snel instellen met minimale handmatige inspanning.

## <a name="advantages-of-one-click-sso"></a>Voordelen van SSO met één klik

- Snelle SSO-configuratie van Azure Marketplace-toepassingen waarvoor handmatige installatie aan toepassingszijde vereist is.
- Efficiëntere en nauwkeurigere SSO-configuratie.
- Geen partnercommunicatie of ondersteuning die nodig is voor het instellen. De toepassing biedt de gebruikersinterface voor SAML-configuratie.

## <a name="prerequisites"></a>Vereisten

- Een actief abonnement van de toepassing om te configureren met SSO. U hebt ook beheerdersreferenties nodig.
- De **aangemeldsextensie Mijn apps secure** van Microsoft is geïnstalleerd in de browser. Zie [Apps openen en gebruiken op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)voor meer informatie.

## <a name="one-click-sso-configuration-steps"></a>SSO-configuratiestappen met één klik

1. Voeg de toepassing toe vanuit de Azure Marketplace.

2. Selecteer **Eenmalig aanmelden**.

3. Selecteer **Eén aanmelding inschakelen**.

4. Vul de verplichte configuratiewaarden in de sectie **BasisSAML-configuratie** in.

    > [!NOTE]
    > Als de toepassing aangepaste claims heeft die u moet configureren, moet u deze afhandelen voordat u Met één klik SSO uitvoert.

5. Als de SSO-functie met één klik beschikbaar is voor uw Azure Marketplace-toepassing, ziet u het volgende scherm. Mogelijk moet u de **browserextensie Mijn apps Secure Sign-in** installeren door **de extensie Installeren**te selecteren.

   ![De veilige aanmeldingsbrowserextensie Mijn apps installeren](./media/one-click-sso-tutorial/install-myappssecure-extension.png)

6. Nadat u de extensie aan de browser hebt toegevoegd, selecteert u **De naam \<\>van de toepassing instellen**. Nadat u bent doorgestuurd naar de portal voor toepassingsbeheer, meldt u zich aan als beheerder.

   ![Naam van de toepassing instellen](./media/one-click-sso-tutorial/setup-sso.png)

7. De browserextensie configureert Automatisch SSO op de toepassing. Bevestig door **Ja**te selecteren .

   ![De automatisch ingevulde gegevens opslaan](./media/one-click-sso-tutorial/save-autopopulate.png)

   > [!NOTE]
   > Als de SSO-configuratie voor uw toepassing extra stappen vereist, volgt u de aanwijzingen om de stappen uit te voeren.

8. Nadat de configuratie is voltooid, selecteert u **OK** om de wijzigingen op te slaan.

   ![De automatisch ingevulde gegevens opslaan](./media/one-click-sso-tutorial/save-data.png)

9. Er wordt een bevestigingsvenster weergegeven om u te laten weten dat de SSO-instellingen zijn geconfigureerd.

   ![SSO geconfigureerd](./media/one-click-sso-tutorial/sso-configured.png)

10. Nadat de configuratie is geslaagd, bent u afgemeld bij de toepassing en teruggestuurd naar de Azure-portal.

11. U **Testen** selecteren om eenmalige aanmelding te testen.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
* [Wat is de browserextensie Mijn apps Secure Sign-in?](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)
 

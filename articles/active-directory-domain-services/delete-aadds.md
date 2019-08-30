---
title: Azure Active Directory Domain Services uitschakelen | Microsoft Docs
description: Azure Active Directory Domain Services uitschakelen met behulp van de Azure Portal
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: d5917ad94212c8b18d4362528bdfbafb02aec808
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171971"
---
# <a name="disable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Active Directory Domain Services uitschakelen met behulp van de Azure Portal
In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om Azure Active Directory (AD) Domain Services uit te scha kelen voor uw Azure AD-adres lijst.

> [!WARNING]
> **Verwijdering is permanent en kan niet worden omgekeerd.**
> Ga voorzichtig te werk! Wanneer u het beheerde domein verwijdert:
>   * Domein controllers voor het beheerde domein worden ongedaan gemaakt en uit het virtuele netwerk verwijderd.
>   * Gegevens op het beheerde domein worden permanent verwijderd. Dit omvat aangepaste organisatie-eenheden, Gpo's, aangepaste DNS-records, service-principals, Gmsa's enzovoort die u hebt gemaakt in het beheerde domein.
>   * Computers die zijn gekoppeld aan het beheerde domein, verliezen hun vertrouwens relatie met het domein en moeten uit het domein worden verwijderd.
>   * U kunt zich niet aanmelden bij deze computers met behulp van zakelijke AD-referenties. Gebruik in plaats daarvan de lokale beheerders referenties voor de computer.
> Als u het beheerde domein verwijdert, wordt de Azure AD-map niet verwijderd of wordt de map anders beïnvloed.

Voer de volgende stappen uit om uw Azure AD Domain Services beheerde domein te verwijderen:
1. Ga naar de [Azure AD Domain Services extensie](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) in de Azure Portal.
2. Klik op de naam van uw beheerde domein.

    ![Te verwijderen domein selecteren](./media/getting-started/domain-services-delete-select-domain.png)

3. Klik op de pagina **overzicht** op de knop **verwijderen** .

    ![Domein verwijderen](./media/getting-started/domain-services-delete-domain.png)

4. Om het verwijderen te bevestigen, typt u de DNS-domein naam van het beheerde domein. Klik op de knop **verwijderen** wanneer u klaar bent.

    ![Bevestiging van domein verwijderen](./media/getting-started/domain-services-delete-domain-confirm.png)

Het kan 15-20 minuten of langer duren voordat het beheerde domein is verwijderd.

Overweeg [feedback](contact-us.md) te geven om ons te helpen begrijpen welke functies u in de toekomst helpt bij het kiezen van Azure AD Domain Services. Deze feedback helpt ons de service te laten werken om beter te voldoen aan de vereisten en gebruiks voorbeelden van uw implementatie.

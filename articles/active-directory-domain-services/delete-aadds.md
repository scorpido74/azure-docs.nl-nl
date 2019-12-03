---
title: Azure Active Directory Domain Services verwijderen | Microsoft Docs
description: Meer informatie over het uitschakelen of verwijderen van een Azure Active Directory Domain Services beheerd domein met behulp van de Azure Portal
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: ee4c41ca3179eead0e4fd470a02a5cdfa4a6d43a
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705241"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Een door Azure Active Directory Domain Services beheerd domein verwijderen met de Azure Portal

Als u een beheerd domein niet meer nodig hebt, kunt u een Azure Active Directory Domain Services-exemplaar (Azure AD DS) verwijderen. Er is geen optie voor het uitschakelen of tijdelijk uitschakelen van een door Azure AD DS beheerd domein. Als u het door Azure AD DS beheerde domein verwijdert, wordt de Azure AD-Tenant niet verwijderd. Dit artikel laat u zien hoe u de Azure Portal kunt gebruiken om een door Azure AD DS beheerd domein te verwijderen.

> [!WARNING]
> **Verwijdering is permanent en kan niet worden omgekeerd.**
> Wanneer u een door Azure AD DS beheerd domein verwijdert, gebeurt het volgende:
>   * Domein controllers voor het beheerde domein worden ongedaan gemaakt en uit het virtuele netwerk verwijderd.
>   * Gegevens op het beheerde domein worden permanent verwijderd. Deze gegevens omvatten aangepaste organisatie-eenheden, Gpo's, aangepaste DNS-records, service-principals, Gmsa's, enzovoort die u hebt gemaakt.
>   * Computers die zijn gekoppeld aan het beheerde domein, verliezen hun vertrouwens relatie met het domein en moeten uit het domein worden verwijderd.
>       * U kunt zich niet aanmelden bij deze computers met behulp van de referenties voor bedrijfs AD. In plaats daarvan moet u de lokale beheerders referenties voor de computer gebruiken.

## <a name="delete-the-managed-domain"></a>Het beheerde domein verwijderen

Voer de volgende stappen uit om een door Azure AD DS beheerd domein te verwijderen:

1. Zoek in het Azure Portal naar en selecteer **Azure AD Domain Services**.
1. Selecteer de naam van uw met Azure AD DS beheerd domein, zoals *aadds.contoso.com*.
1. Selecteer **Verwijderen** op de pagina **Overzicht**. Om het verwijderen te bevestigen, typt u de domein naam van het beheerde domein opnieuw en selecteert u vervolgens **verwijderen**.

Het kan 15-20 minuten of langer duren voordat het beheerde domein van Azure AD DS verwijderd.

## <a name="next-steps"></a>Volgende stappen

Overweeg [feedback te delen][feedback] voor de functies die u wilt zien in azure AD DS.

Zie [een Azure Active Directory Domain Services exemplaar maken en configureren][create-instance]als u opnieuw aan de slag wilt gaan met Azure AD DS.

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md

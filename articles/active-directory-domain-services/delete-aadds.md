---
title: Azure Active Directory Domain Services verwijderen | Microsoft Documenten
description: Meer informatie over het uitschakelen of verwijderen van een beheerd Azure Directory Domain Services-domein met behulp van de Azure-portal
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e6fcb53995fd255386e5b291937d6d50c850c724
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476292"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Een beheerd Azure Directory Domain Services-domein verwijderen met behulp van de Azure-portal

Als u geen beheerd domein meer nodig hebt, u een Azure Active Directory Domain Services-exemplaar (Azure AD DS) verwijderen. Er is geen optie om een door Azure AD DS beheerd domein uit te schakelen of tijdelijk uit te schakelen. Als u het beheerde Azure AD DS-beheerde domein verwijdert of anderszins een negatieve invloed heeft op de AZURE AD-tenant. In dit artikel ziet u hoe u de Azure-portal gebruiken om een door Azure AD DS beheerd domein te verwijderen.

> [!WARNING]
> **Verwijdering is permanent en kan niet worden teruggedraaid.**
> Wanneer u een door Azure AD DS beheerd domein verwijdert, worden de volgende stappen uitgevoerd:
>   * Domeincontrollers voor het beheerde domein worden gedeprovisioneerd en uit het virtuele netwerk verwijderd.
>   * Gegevens over het beheerde domein worden permanent verwijderd. Deze gegevens omvatten aangepaste O's, GPO's, aangepaste DNS-records, serviceprincipals, GMSA's, enz.
>   * Machines die zijn gekoppeld aan het beheerde domein verliezen hun vertrouwensrelatie met het domein en moeten worden losgekoppeld van het domein.
>       * U zich niet aanmelden bij deze machines met behulp van bedrijfsAD-referenties. In plaats daarvan moet u de lokale beheerdersreferenties voor de machine gebruiken.

## <a name="delete-the-managed-domain"></a>Het beheerde domein verwijderen

Voer de volgende stappen uit om een door Azure AD DS beheerd domein te verwijderen:

1. Zoek en selecteer Azure AD Domain Services in de **Azure-portal.**
1. Selecteer de naam van uw door Azure AD DS beheerde domein, zoals *aaddscontoso.com*.
1. Selecteer **Verwijderen** op de pagina **Overzicht**. Als u de verwijdering wilt bevestigen, typt u de domeinnaam van het beheerde domein opnieuw en selecteert u **Verwijderen**.

Het kan 15-20 minuten of langer duren om het beheerde Azure AD DS-beheerde domein te verwijderen.

## <a name="next-steps"></a>Volgende stappen

Overweeg [feedback te delen][feedback] voor de functies die u wilt zien in Azure AD DS.

Zie [Een Azure Active Directory Domain Services-exemplaar maken en configureren][create-instance]als u opnieuw aan de slag wilt met Azure AD DS.

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md

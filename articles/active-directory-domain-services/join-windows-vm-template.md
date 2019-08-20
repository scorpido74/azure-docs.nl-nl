---
title: Een Windows Server-VM toevoegen aan Azure Active Directory Domain Services | Microsoft Docs
description: Een virtuele machine met Windows Server toevoegen aan een beheerd domein met Azure Resource Manager sjablonen.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 599d474b7c45274c87878c622149a86bc93af318
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612270"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Een virtuele machine met Windows Server toevoegen aan een beheerd domein met behulp van een resource manager-sjabloon
In dit artikel wordt beschreven hoe u een virtuele Windows Server-machine kunt koppelen aan een Azure AD Domain Services beheerd domein met behulp van Resource Manager-sjablonen.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voordat u begint
Voor het uitvoeren van de taken die in dit artikel worden vermeld, hebt u het volgende nodig:
1. Een geldig **Azure-abonnement**.
2. Een **Azure AD-Directory** : gesynchroniseerd met een on-premises Directory of een alleen-Cloud Directory.
3. **Azure AD Domain Services** moet zijn ingeschakeld voor de Azure AD-adres lijst. Als u dit nog niet hebt gedaan, volgt u alle taken die in de aan de slag- [hand leiding](tutorial-create-instance.md)worden beschreven.
4. Zorg ervoor dat u de IP-adressen van het beheerde domein hebt geconfigureerd als de DNS-servers voor het virtuele netwerk. Zie [DNS-instellingen bijwerken voor het virtuele Azure-netwerk](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network) voor meer informatie.
5. Voer de stappen uit die nodig zijn om [wacht woorden te synchroniseren met uw Azure AD Domain Services beheerde domein](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="install-and-configure-required-tools"></a>Vereiste hulpprogram ma's installeren en configureren
U kunt een van de volgende opties gebruiken om de stappen uit te voeren die in dit document worden beschreven:
* **Azure PowerShell**: [Installeren en configureren](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **Azure CLI**: [Installeren en configureren](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Optie 1: Een nieuwe virtuele Windows Server-machine inrichten en toevoegen aan een beheerd domein
**naam Quick**start-sjabloon: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Als u een virtuele Windows Server-machine wilt implementeren en wilt toevoegen aan een beheerd domein, voert u de volgende stappen uit:
1. Navigeer naar de [sjabloon Quick](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)start.
2. Klik op **Implementeren in Azure**.
3. Geef op de pagina **aangepaste implementatie** de vereiste informatie op voor het inrichten van de virtuele machine.
4. Selecteer het **Azure-abonnement** waarin u de virtuele machine wilt inrichten. Kies hetzelfde Azure-abonnement waarin u Azure AD Domain Services hebt ingeschakeld.
5. Kies een bestaande **resource groep** of maak een nieuwe.
6. Kies een **locatie** waar u de nieuwe virtuele machine wilt implementeren.
7. Geef in **bestaande VNET-naam**het virtuele netwerk op waarin u uw Azure AD Domain Services beheerde domein hebt geïmplementeerd.
8. Geef in **bestaande subnetnaam**het subnet op in het virtuele netwerk waarin u deze virtuele machine wilt implementeren. Selecteer niet het gateway-subnet in het virtuele netwerk. Selecteer ook niet het toegewezen subnet waarin uw beheerde domein is geïmplementeerd.
9. Geef in het **voor voegsel van de DNS-label**de hostnaam op voor de virtuele machine die wordt ingericht. Bijvoorbeeld: contoso-Win.
10. Selecteer de juiste **VM-grootte** voor de virtuele machine.
11. Geef in **domein om lid te worden**van de DNS-domein naam van uw beheerde domein.
12. Geef in **domein gebruikers naam**de gebruikers accountnaam op die moet worden gebruikt om de virtuele machine toe te voegen aan het beheerde domein.
13. Geef bij **domein wachtwoord**het wacht woord op van het domein gebruikers account waarnaar wordt verwezen door de para meter ' domainUsername '.
14. Optioneel: U kunt een **OE-pad** naar een aangepaste OE opgeven, waarin u de virtuele machine wilt toevoegen. Als u geen waarde voor deze para meter opgeeft, wordt de virtuele machine toegevoegd aan de standaard organisatie-eenheid voor **Aad DC-computers** op het beheerde domein.
15. Geef in het veld naam van de **VM-beheerder** de naam op van een lokale Administrator-account voor de virtuele machine.
16. Geef in het veld **wacht woord** voor de VM-beheerder een lokaal beheerders wachtwoord voor de virtuele machine op. Geef een sterk wacht woord voor de lokale beheerder op om de virtuele machine te beveiligen tegen aanvallen met een wacht woord.
17. Klik op **Ik ga akkoord met de bovenstaande voor waarden**.
18. Klik op **aanschaffen** om de virtuele machine in te richten.

> [!WARNING]
> **Wacht woorden met een waarschuwing.**
> Het sjabloon parameter bestand bevat wacht woorden voor domein accounts en lokale beheerders wachtwoorden voor de virtuele machine. Zorg ervoor dat u dit bestand niet op dezelfde plaats laat staan op bestands shares of andere gedeelde locaties. U kunt dit bestand het beste verwijderen wanneer u klaar bent met het implementeren van uw virtuele machines.
>

Nadat de implementatie is voltooid, wordt uw nieuw ingerichte Windows-virtuele machine toegevoegd aan het beheerde domein.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Optie 2: Een bestaande virtuele machine met Windows Server toevoegen aan een beheerd domein
**Quick**start-sjabloon: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Als u een bestaande virtuele machine met Windows Server wilt toevoegen aan een beheerd domein, voert u de volgende stappen uit:
1. Navigeer naar de [sjabloon Quick](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)start.
2. Klik op **Implementeren in Azure**.
3. Geef op de pagina **aangepaste implementatie** de vereiste informatie op voor het inrichten van de virtuele machine.
4. Selecteer het **Azure-abonnement** waarin u de virtuele machine wilt inrichten. Kies hetzelfde Azure-abonnement waarin u Azure AD Domain Services hebt ingeschakeld.
5. Kies een bestaande **resource groep** of maak een nieuwe.
6. Kies een **locatie** waar u de nieuwe virtuele machine wilt implementeren.
7. Geef in het veld **VM-lijst** de namen op van de bestaande virtuele machines die moeten worden gekoppeld aan het beheerde domein. Gebruik een komma om afzonderlijke VM-namen van elkaar te scheiden. Bijvoorbeeld **Contoso-Web, contoso-API**.
8. Geef in de **gebruikers naam voor domein deelname**de gebruikers accountnaam op die moet worden gebruikt om de virtuele machine toe te voegen aan het beheerde domein.
9. Geef het wacht woord op van het domein gebruikers account waarnaar wordt verwezen door de para meter ' domainUsername ' in het **wacht woord van domein toevoegen**.
10. Geef in het **domein FQDN**de DNS-domein naam van uw beheerde domein op.
11. Optioneel: U kunt een **OE-pad** naar een aangepaste OE opgeven, waarin u de virtuele machine wilt toevoegen. Als u geen waarde voor deze para meter opgeeft, wordt de virtuele machine toegevoegd aan de standaard organisatie-eenheid voor **Aad DC-computers** op het beheerde domein.
12. Klik op **Ik ga akkoord met de bovenstaande voor waarden**.
13. Klik op **aanschaffen** om de virtuele machine in te richten.

> [!WARNING]
> **Wacht woorden met een waarschuwing.**
> Het sjabloon parameter bestand bevat wacht woorden voor domein accounts en lokale beheerders wachtwoorden voor de virtuele machine. Zorg ervoor dat u dit bestand niet op dezelfde plaats laat staan op bestands shares of andere gedeelde locaties. U kunt dit bestand het beste verwijderen wanneer u klaar bent met het implementeren van uw virtuele machines.
>

Nadat de implementatie is voltooid, worden de opgegeven virtuele Windows-machines gekoppeld aan het beheerde domein.


## <a name="related-content"></a>Gerelateerde inhoud
* [Overzicht van Azure PowerShell](/powershell/azure/overview)
* [Sjabloon voor Azure Quick Start: domein lid worden van een nieuwe VM](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Sjabloon voor Azure Quick Start: domein toevoegen aan bestaande Vm's](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)

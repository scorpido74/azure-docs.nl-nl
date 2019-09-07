---
title: 'Azure Active Directory Domain Services: Problemen met netwerk beveiligings groepen oplossen | Microsoft Docs'
description: Problemen met de configuratie van netwerk beveiligings groepen voor Azure AD Domain Services oplossen
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 450ee5635b378ed7c4d4e4bedc1c4245f6b52d70
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "70743451"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>Problemen met ongeldige netwerk configuratie voor uw beheerde domein oplossen
Dit artikel helpt u bij het oplossen van problemen met betrekking tot het netwerk en het oplossen van fouten die het volgende waarschuwings bericht veroorzaken:

## <a name="alert-aadds104-network-error"></a>AADDS104 waarschuwing: Netwerkfout
**Waarschuwings bericht:** *Micro soft kan de domein controllers voor dit beheerde domein niet bereiken. Dit kan gebeuren als een netwerk beveiligings groep (NSG) die in uw virtuele netwerk is geconfigureerd, de toegang tot het beheerde domein blokkeert. Een andere mogelijke reden is als er een door de gebruiker gedefinieerde route is die inkomend verkeer van het Internet blokkeert.*

Ongeldige NSG-configuraties zijn de meest voorkomende oorzaak van netwerk fouten voor Azure AD Domain Services. De netwerk beveiligings groep (NSG) die voor het virtuele netwerk is geconfigureerd, moet toegang tot [specifieke poorten](network-considerations.md#network-security-groups-and-required-ports)toestaan. Als deze poorten zijn geblokkeerd, kan micro soft uw beheerde domein niet bewaken of bijwerken. Daarnaast wordt de synchronisatie tussen uw Azure AD-Directory en uw beheerde domein beïnvloed. Zorg dat deze poorten zijn geopend tijdens het maken van uw NSG om onderbreking van de service te voor komen.

### <a name="checking-your-nsg-for-compliance"></a>Uw NSG controleren op naleving

1. Ga naar de pagina [netwerk beveiligings groepen](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) in de Azure Portal
2. Kies in de tabel het NSG dat is gekoppeld aan het subnet waarin uw beheerde domein is ingeschakeld.
3. Klik onder **instellingen** in het linkerdeel venster op regels voor **binnenkomende beveiliging**
4. Controleer de regels en Identificeer welke regels de toegang tot [deze poorten](network-considerations.md#network-security-groups-and-required-ports) blok keren
5. Bewerk de NSG om te voldoen aan de naleving door de regel te verwijderen, een regel toe te voegen of een nieuwe NSG volledig te maken. Stappen voor het [toevoegen van een regel](#add-a-rule-to-a-network-security-group-using-the-azure-portal) of het maken van een nieuwe, compatibele NSG

## <a name="sample-nsg"></a>Voor beeld NSG
In de volgende tabel ziet u een voor beeld van een NSG die ervoor zorgt dat uw beheerde domein veilig blijft terwijl micro soft gegevens kan bewaken, beheren en bijwerken.

![voor beeld NSG](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Azure AD Domain Services heeft onbeperkte uitgaande toegang vanuit het virtuele netwerk nodig. We raden u aan geen aanvullende NSG-regel te maken waarmee de uitgaande toegang voor het virtuele netwerk wordt beperkt.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Een regel toevoegen aan een netwerk beveiligings groep met behulp van de Azure Portal
Als u Power shell niet wilt gebruiken, kunt u hand matig afzonderlijke regels aan Nsg's toevoegen met behulp van de Azure Portal. Als u regels wilt maken in uw netwerk beveiligings groep, voert u de volgende stappen uit:

1. Ga naar de pagina [netwerk beveiligings groepen](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) in de Azure Portal.
2. Kies in de tabel het NSG dat is gekoppeld aan het subnet waarin uw beheerde domein is ingeschakeld.
3. Onder **instellingen** in het linkerdeel venster klikt u op **binnenkomende beveiligings regels** of **uitgaande beveiligings regels**.
4. Maak de regel door te klikken op **toevoegen** en de gegevens in te vullen. Klik op **OK**.
5. Controleer of uw regel is gemaakt door deze in de regel tabel te zoeken.


## <a name="need-help"></a>Hulp nodig?
Neem contact op met het product team van Azure Active Directory Domain Services om [feedback te delen of voor ondersteuning](contact-us.md).

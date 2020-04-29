---
title: Account beheer-Azure VMware-oplossing op CloudSimple Portal
description: Hierin wordt beschreven hoe u accounts in de Azure VMware-oplossing beheert door de CloudSimple-Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025364"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Accounts in de Azure VMware-oplossing beheren via de CloudSimple-Portal

Wanneer u uw CloudSimple-service maakt, maakt deze een account op CloudSimple. Het account is gekoppeld aan uw Azure-abonnement waar de service zich bevindt. Alle gebruikers met de rollen eigenaar en Inzender in het abonnement hebben toegang tot de CloudSimple-Portal. De Azure-abonnements-ID en Tenant-ID die zijn gekoppeld aan de CloudSimple-service zijn te vinden op de pagina accounts.

Als u accounts in de CloudSimple-Portal wilt beheren, [opent u de portal](access-cloudsimple-portal.md) en selecteert u **account** in het menu aan de zijkant.

Selecteer **samen vatting** om informatie weer te geven over de CloudSimple-configuratie van uw bedrijf. De huidige capaciteit van uw Cloud configuratie wordt weer gegeven, inclusief het aantal Privécloud, de totale opslag, de vSphere-cluster configuratie, het aantal knoop punten en het aantal reken kernen. Er is een koppeling opgenomen om extra knoop punten aan te schaffen als de huidige configuratie niet aan al uw behoeften voldoet.

## <a name="email-alerts"></a>E-mailwaarschuwingen

U kunt e-mail adressen toevoegen van alle personen die u op de hoogte wilt stellen over wijzigingen in de configuratie van de Privécloud.

1. Klik in het gebied **extra e-mail waarschuwingen** op **nieuwe toevoegen**.
2. Voer het e-mail adres in.
3. Druk op Return.  

Als u een vermelding wilt verwijderen, klikt u op **X**.

## <a name="cloudsimple-operator-access"></a>Toegang tot CloudSimple-operator

Met de instelling voor operator toegang kan CloudSimple u helpen bij het oplossen van problemen, waardoor een ondersteunings technicus zich kan aanmelden bij uw CloudSimple-Portal.  De instelling is standaard ingeschakeld. Alle acties die worden uitgevoerd door de ondersteunings technicus wanneer u zich aanmeldt bij uw klant account, worden geregistreerd en beschikbaar gesteld voor uw beoordeling op de pagina **activiteiten** > **controleren** .

Klik op de **CloudSimple-operator toegang ingeschakeld** om de toegang in of uit te scha kelen.

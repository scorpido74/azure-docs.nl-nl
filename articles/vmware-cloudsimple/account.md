---
title: Account beheer-portal voor Azure VMware-oplossingen (AVS)
description: Hierin wordt beschreven hoe u accounts beheert op de Azure VMware Solutions (AVS)-Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1fd7cb1c55fb664828448cef0b67ea9b16323bdf
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025364"
---
# <a name="manage-accounts-on-the-azure-vmware-solutions-avs-portal"></a>Accounts beheren op de Azure VMware Solutions (AVS)-Portal

Wanneer u uw AVS-service maakt, wordt er een account voor AVS gemaakt. Het account is gekoppeld aan uw Azure-abonnement waar de service zich bevindt. Alle gebruikers met de rollen eigenaar en Inzender in het abonnement hebben toegang tot de AVS-Portal. De Azure-abonnements-ID en Tenant-ID die zijn gekoppeld aan de AVS-service zijn te vinden op de pagina accounts.

Als u accounts in de AVS-Portal wilt beheren, [opent u de portal](access-cloudsimple-portal.md) en selecteert u **account** in het menu aan de zijkant.

Selecteer **samen vatting** om informatie weer te geven over de AVS-configuratie van uw bedrijf. De huidige capaciteit van uw Cloud configuratie wordt weer gegeven, inclusief het aantal persoonlijke Clouds van de AVS, de totale opslag, de vSphere-cluster configuratie, het aantal knoop punten en het aantal reken kernen. Er is een koppeling opgenomen om extra knoop punten aan te schaffen als de huidige configuratie niet aan al uw behoeften voldoet.

## <a name="email-alerts"></a>E-mail waarschuwingen

U kunt e-mail adressen toevoegen van alle personen die u op de hoogte wilt stellen over wijzigingen in de configuratie van de Privécloud in de Cloud.

1. Klik in het gebied **extra e-mail waarschuwingen** op **nieuwe toevoegen**.
2. Voer het e-mail adres in.
3. Druk op Return.  

Als u een vermelding wilt verwijderen, klikt u op **X**.

## <a name="avs-operator-access"></a>AVS-operator toegang

Met de instelling voor operator toegang kan AVS u helpen problemen op te lossen door een ondersteunings technicus in staat te stellen zich aan te melden bij uw AVS-Portal. De instelling is standaard ingeschakeld. Alle acties die worden uitgevoerd door de ondersteunings technicus wanneer u zich aanmeldt bij uw klant account, worden geregistreerd en beschikbaar gesteld voor uw beoordeling op de pagina **activiteiten** > **controle** .

Klik op de knop **toegang AVS-operator ingeschakeld** om de toegang in of uit te scha kelen.

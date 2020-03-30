---
title: Accountbeheer - Azure VMware-oplossing per CloudSimple-portal
description: Beschrijft hoe u accounts beheert op de Azure VMware-oplossing per CloudSimple-portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00348d36c38fdbfdf69a2e4e80a4c720268b40c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025364"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>Accounts beheren op de Azure VMware-oplossing per CloudSimple-portal

Wanneer u uw CloudSimple-service maakt, wordt een account gemaakt op CloudSimple. Het account is gekoppeld aan uw Azure-abonnement waar de service zich bevindt. Alle gebruikers met een rol als eigenaar en bijdrager in het abonnement hebben toegang tot de CloudSimple-portal. De Azure-abonnements-id en -tenant-id die zijn gekoppeld aan de CloudSimple-service zijn te vinden op de pagina Accounts.

Als u accounts in de CloudSimple-portal wilt beheren, [opent u de portal](access-cloudsimple-portal.md) en selecteert u **Account** in het zijmenu.

Selecteer **Overzicht** om informatie over de CloudSimple-configuratie van uw bedrijf weer te geven. De huidige capaciteit van uw cloudconfiguratie wordt weergegeven, inclusief het aantal private clouds, totale opslag, vSphere-clusterconfiguratie, aantal knooppunten en het aantal rekenkernen. Er is een koppeling opgenomen om extra knooppunten aan te schaffen als de huidige configuratie niet aan al uw behoeften voldoet.

## <a name="email-alerts"></a>E-mailwaarschuwingen

U e-mailadressen toevoegen van personen die u op de hoogte wilt stellen van wijzigingen in de Private Cloud-configuratie.

1. Klik in het gebied **Extra e-mailwaarschuwingen** op **Nieuw toevoegen**.
2. Voer het e-mailadres in.
3. Druk op Return.  

Als u een item wilt verwijderen, klikt u op **X**.

## <a name="cloudsimple-operator-access"></a>CloudSimple operator toegang

Met de instelling voor toegang tot de operator kan CloudSimple u helpen met het oplossen van problemen door een ondersteuningstechnicus toe te staan zich aan te melden bij uw CloudSimple-portal.  De instelling is standaard ingeschakeld. Alle acties die door de ondersteuningstechnicus worden uitgevoerd wanneer ze zijn aangemeld bij uw klantenaccount, worden geregistreerd en beschikbaar voor uw beoordeling op de pagina > **Activiteitscontrole.** **Activity**

Klik op de **cloudsimple-operatortoegang ingeschakeld** om de toegang in of uit te schakelen.

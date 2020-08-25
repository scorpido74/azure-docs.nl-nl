---
title: Back-up automatisch inschakelen bij het maken van VM's met Azure Policy
description: Een artikel met informatie over het gebruik van Azure Policy om back-up automatisch in te scha kelen voor alle Vm's die in een bepaald bereik zijn gemaakt
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: de4923000bc842203535e03727fd532c67a8f517
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826069"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Back-up automatisch inschakelen bij het maken van VM's met Azure Policy

Een van de belangrijkste verantwoordelijkheden van een back-up-of nalevings beheerder in een organisatie is ervoor te zorgen dat alle bedrijfs kritieke computers een back-up maken met de juiste Bewaar periode.

Momenteel biedt Azure Backup een ingebouwd beleid (met behulp van Azure Policy) dat kan worden toegewezen aan **alle virtuele Azure-machines in een opgegeven locatie binnen een abonnement of resource groep**. Wanneer dit beleid is toegewezen aan een bepaald bereik, worden alle nieuwe Vm's die in dat bereik zijn gemaakt, automatisch geconfigureerd voor back-up naar een **bestaande kluis op dezelfde locatie en hetzelfde abonnement**. De gebruiker kan de kluis en het Bewaar beleid opgeven waarnaar de back-ups van virtuele machines moeten worden gekoppeld.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Het ingebouwde beleid wordt momenteel alleen ondersteund voor virtuele Azure-machines. Gebruikers moeten er zeker van zijn dat het Bewaar beleid dat tijdens de toewijzing is opgegeven, een VM-Bewaar beleid is. Raadpleeg [Dit](./backup-azure-policy-supported-skus.md) document voor een overzicht van alle VM-sku's die door dit beleid worden ondersteund.

* Het beleid kan per keer worden toegewezen aan één locatie en abonnement. Als u back-ups wilt maken voor Vm's op verschillende locaties en abonnementen, moet u meerdere exemplaren van de beleids toewijzing maken, één voor elke combi natie van locatie en abonnement.

* De opgegeven kluis en de virtuele machines die voor back-up zijn geconfigureerd, kunnen zich onder verschillende resource groepen bevindt.

* Het bereik van de beheer groep wordt momenteel niet ondersteund.

* Het ingebouwde beleid is momenteel niet beschikbaar in nationale Clouds.

## <a name="using-the-built-in-policy"></a>Het ingebouwde beleid gebruiken

Volg de onderstaande stappen om het beleid toe te wijzen aan het vereiste bereik:

1. Meld u aan bij de Azure Portal en navigeer naar het **beleids** dashboard.
1. Selecteer **definities** in het linkermenu om een lijst op te halen met alle ingebouwde beleids regels voor Azure-resources.
1. Filter de lijst voor **Category = backup**. U ziet dat de lijst wordt gefilterd op één beleid met de naam ' back-up configureren op Vm's van een locatie naar een bestaande centrale kluis op dezelfde locatie '.
![Beleids dashboard](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. Selecteer de naam van het beleid. U wordt omgeleid naar de gedetailleerde definitie van dit beleid.
![Deel venster beleids definitie](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. Selecteer de knop **toewijzen** boven aan het deel venster. Hiermee wordt u omgeleid naar het deel venster **beleid toewijzen** .
1. Selecteer onder **basis beginselen**de drie punten naast het veld **bereik** . Hiermee opent u een context paneel met de rechter muisknop waarin u het abonnement kunt selecteren waarop het beleid moet worden toegepast. U kunt desgewenst ook een resource groep selecteren, zodat het beleid alleen wordt toegepast op virtuele machines in een bepaalde resource groep.
![Basis principes van beleids toewijzing](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. Kies op het tabblad **para meters** een locatie in de vervolg keuzelijst en selecteer de kluis en het back-upbeleid waaraan de vm's in het bereik moeten worden gekoppeld.
![Beleids toewijzings parameters](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. Zorg ervoor dat het **effect** is ingesteld op deployIfNotExists.
1. Ga naar **controleren en maken** en selecteer **maken**.

> [!NOTE]
>
> Azure Policy kunnen ook worden gebruikt op bestaande Vm's, met behulp van [herstellen](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Het is raadzaam dat dit beleid per keer niet wordt toegewezen aan meer dan 200 Vm's. Als het beleid is toegewezen aan meer dan 200 Vm's, kan dit ertoe leiden dat de back-up een paar uur later wordt geactiveerd dan het tijdstip dat is opgegeven in de planning.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Azure Policy](../governance/policy/overview.md)

---
title: Back-upcentrum-Veelgestelde vragen
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Back-upcentrum
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: b10a9e73e65cf12c43ce28b429a8f12e0b960a76
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995778"
---
# <a name="backup-center---frequently-asked-questions"></a>Back-upcentrum: veelgestelde vragen

## <a name="management"></a>Beheer

### <a name="can-backup-center-be-used-across-tenants"></a>Kan backup Center worden gebruikt in meerdere tenants?

Ja, als u [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) gebruikt en toegang hebt gedelegeerd voor abonnementen over verschillende tenants, kunt u Back-upcentrum gebruiken als een enkel glas venster voor het beheren van back-ups tussen tenants.

### <a name="can-backup-center-be-used-to-manage-both-recovery-services-vaults-and-backup-vaults"></a>Kan backup Center worden gebruikt voor het beheren van zowel Recovery Services kluizen als back-upkluizen?

Ja, het Back-upcentrum kan zowel [Recovery Services kluizen](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) als [back-upkluizen](backup-vault-overview.md)beheren.

### <a name="is-there-a-delay-before-data-surfaces-in-backup-center"></a>Is er een vertraging voor gegevens Opper vlakken in Back-upcentrum?

Back-upcentrum is gericht op het bieden van real-time informatie. Er kan een paar seconden vertraging optreden tussen het moment dat een entiteit wordt weer gegeven in een afzonderlijk kluis scherm en de tijd die dezelfde entiteit bevat in het Back-upcentrum.

## <a name="configuration"></a>Configuratie

### <a name="do-i-need-to-configure-anything-to-see-data-in-backup-center"></a>Moet ik iets configureren om gegevens in Back-upcentrum te bekijken?

Nee. Het Back-upcentrum is klaar voor de doos. Als u echter [back-uprapporten](https://docs.microsoft.com/azure/backup/configure-reports) wilt weer geven onder Back-upcentrum, moet u de rapportage voor uw kluizen configureren.

### <a name="do-i-need-to-have-any-special-permissions-to-use-backup-center"></a>Heb ik speciale machtigingen nodig om het Back-upcentrum te gebruiken?

Het Back-upcentrum als zodanig heeft geen nieuwe machtigingen nodig. Als u het juiste niveau van RBAC-toegang hebt voor de resources die u beheert, kunt u Back-upcentrum voor deze bronnen gebruiken. Als u bijvoorbeeld informatie over uw back-ups wilt weer geven, hebt u **Lees** toegang tot uw kluizen nodig. Als u een back-up wilt configureren en andere back-upbewerkingen wilt uitvoeren, moet u de rollen **back-upinzender** of **back-upoperator** hebben. Meer informatie over [RBAC-rollen voor Azure backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

## <a name="pricing"></a>Prijzen

### <a name="do-i-need-to-pay-anything-extra-to-use-backup-explorer"></a>Moet ik extra betalen om back-upverkenner te gebruiken?

Op dit moment zijn er geen extra kosten (afgezien van de back-upkosten) voor het gebruik van Back-upcentrum. Als u echter [back-uprapporten](https://docs.microsoft.com/azure/backup/configure-reports) gebruikt in Back-upcentrum, [zijn er kosten verbonden](https://azure.microsoft.com/pricing/details/monitor/) aan het gebruik van Azure monitor logboeken voor back-uprapporten.

## <a name="next-steps"></a>Volgende stappen

Lees de andere veelgestelde vragen:

* [Veelgestelde vragen over Recovery Services kluizen](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq)
* [Veelgestelde vragen over back-ups van Azure-VM'S](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq)

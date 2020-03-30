---
title: StorSimple-opslagaccounts, abonnementen migreren
description: Meer informatie over het migreren van abonnementen, opslagaccounts voor uw StorSimple Device Manager-service.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 428c336d98e278910b229e9c0d877a9ae6268c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169711"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Abonnementen en opslagaccounts migreren die zijn gekoppeld aan de StorSimple-apparaatbeheerservice

Mogelijk moet u uw StorSimple-service verplaatsen naar een nieuwe inschrijving of naar een nieuw abonnement. Deze migratiescenario's zijn accountwijzigingen of datacenterwijzigingen. Gebruik de volgende tabel om te begrijpen welke van deze scenario's worden ondersteund, inclusief de gedetailleerde stappen om te verplaatsen.

## <a name="account-changes"></a>Accountwijzigingen

| u bewegen...| Ondersteund| Downtime| Azure Support-proces| Methode|
|-----|-----|-----|-----|-----|
| Een volledig abonnement (inclusief StorSimple-service- en opslagaccounts) naar een andere inschrijving? | Ja       | Nee       | **Inschrijvingsoverdracht**<br>Gebruiken:<li>Wanneer u een nieuwe Azure-toezegging koopt onder een nieuwe overeenkomst.</li><li>U wilt alle accounts en abonnementen migreren van de oude inschrijving naar de nieuwe. Dit omvat alle Azure-services onder het oude abonnement.</li> | **Stap 1: Open een Azure Enterprise Operation Support-ticket.**<li>Ga [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)naar.</li><li> Selecteer **Inschrijvingsbeheer** en selecteer **Vervolgens Overzetten van één inschrijving naar een nieuwe inschrijving**.<br>**Stap 2: De gevraagde informatie verstrekken**<br>Neem het op:<li>broninschrijvingsnummer</li><li> bestemmingsinschrijvingsnummer</li><li>overdrachtsdatum|
| StorSimple service van een bestaand account naar een nieuwe inschrijving?    | Ja       | Nee       | **Accountoverboeking**<br>Gebruiken:<li>Wanneer u geen volledige inschrijvingsoverdracht wilt.</li><li>U wilt alleen specifieke accounts verplaatsen naar een nieuwe inschrijving.</li>| **Stap 1: Open een Azure Enterprise Operation Support-ticket.**<li>Ga [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)naar.</li><li>Selecteer **Inschrijvingsbeheer** en selecteer **Vervolgens Een EA-account overzetten naar een nieuwe inschrijving**.<br>**Stap 2: De gevraagde informatie verstrekken**<br>Neem het op:<li>broninschrijvingsnummer</li><li> bestemmingsinschrijvingsnummer</li><li>overdrachtsdatum|
| StorSimple service van het ene abonnement naar een ander abonnement?      | Nee        |    Ja         | None|<li>Migreer gegevens van het StorSimple-apparaat.</li><li>Voer een fabrieksreset van het apparaat uit, hiermee worden alle lokale gegevens op het apparaat verwijderd.</li><li>Registreer het apparaat met het nieuwe abonnement op een StorSimple Device Manager-service.</li><li>Migreer de gegevens terug naar het apparaat.|
|Kan ik het eigendom van een Azure-abonnement overdragen aan een andere directory? | Ja       | Nee       | Een bestaand abonnement koppelen aan uw Azure AD-map | Verwijzen [om een bestaand abonnement te koppelen aan uw Azure AD-map.](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) Het kan maximaal 10 minuten duren voordat alles goed wordt weergegeven.|
| StorSimple-apparaat van de ene StorSimple Device Manager-service naar een andere service in een andere regio?      | Nee        | Ja            | None |Hetzelfde als hierboven.|
| Opslagaccount voor een nieuw abonnement of resourcegroep?     | Ja        | Nee             |Opslagaccount verplaatsen naar verschillende abonnements- of resourcegroep |Als de toegangssleutels voor het opslagaccount na de verhuizing zijn bijgewerkt, moet de gebruiker de toegangssleutels handmatig configureren voor het gemigreerde opslagaccount via de StorSimple Device Manager-service.|
| Klassiek opslagaccount naar een Azure Resource Manager-opslagaccount      | Ja        | Nee             |Migreren van klassiek naar Azure Resource Manager |<li>Ga voor gedetailleerde instructies over het migreren van een opslagaccount van klassiek naar Azure Resource Manager naar [Een klassiek opslagaccount migreren.](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account)</li><li> Als de toegangssleutels voor opslagaccounts na migratie worden bijgewerkt, moet de gebruiker de toegangssleutels voor het gemigreerde opslagaccount synchroniseren via de StorSimple Device Manager-service. Dit is om ervoor te zorgen dat de StorSimple-apparaten normaal blijven functioneren en primaire/back-upgegevens naar Azure kunnen worden geklasseerd. Ga voor gedetailleerde instructies over het synchroniseren van toegangssleutels naar [De werkstroom van rotatie](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> In het geval van een StorSimple Cloud Appliance moet het toestel naar behoren functioneren als het klassieke opslagaccount wordt gemigreerd, maar de onderliggende virtuele machine nog steeds in het klassiek blijft. Als de onderliggende virtuele machine voor het cloudtoestel wordt gemigreerd, werkt de functionaliteit deactiveren en verwijderen niet.</li><li> U moet een nieuwe StorSimple Cloud Appliances maken in de Azure-portal en vervolgens mislukken van de oudere cloudapparaten. U geen StorSimple Cloud Appliance maken in de nieuwe Azure-portal met een klassiek opslagaccount, ze moeten een Azure Resource Manager-opslagaccount hebben. Ga voor meer informatie naar [Implementeren en beheren van een StorSimple Cloud Appliance.](storsimple-8000-cloud-appliance-u2.md)</li>|

## <a name="datacenter-changes"></a>Datacenterwijzigingen

| u bewegen...| Ondersteund|Downtime| Azure Support-proces| Methode|
|-----|-----|-----|-----|-----|
| Een StorSimple-service van het ene Azure-datacenter naar het andere? | Nee | Ja |None  |<li>Migreer gegevens van het StorSimple-apparaat.</li><li>Voer een fabrieksreset van het apparaat uit, hiermee worden alle lokale gegevens op het apparaat verwijderd.</li><li>Registreer het apparaat met het nieuwe abonnement op een nieuwe StorSimple Device Manager-service.</li><li>Migreer de gegevens terug naar het apparaat.|
| Een opslagaccount van het ene Azure-datacenter naar het andere? | Nee |Ja  |None  | Hetzelfde als hierboven.|

## <a name="next-steps"></a>Volgende stappen

* [StorSimple Device Manager-service implementeren](storsimple-8000-manage-service.md)
* [StorSimple 8000-serie apparaat implementeren in Azure-portal](storsimple-8000-deployment-walkthrough-u2.md)

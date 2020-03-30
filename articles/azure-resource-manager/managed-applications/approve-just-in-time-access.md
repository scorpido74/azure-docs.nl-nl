---
title: Just-in-time-toegang goedkeuren
description: Beschrijft hoe consumenten van Azure Managed Applications aanvragen voor just-in-time toegang tot een beheerde toepassing goedkeuren.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651018"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Just-in-time toegang configureren en goedkeuren voor Azure Managed Applications

Als consument van een beheerde toepassing u zich misschien niet op uw gemak voelen om de uitgever permanent toegang te geven tot de beheerde brongroep. Azure Managed Applications biedt een functie die just-in-time (JIT)-toegang wordt genoemd, die momenteel in preview is, om u meer controle te geven over het verlenen van toegang tot beheerde resources. Hiermee u goedkeuren wanneer en hoe lang de uitgever toegang heeft tot de brongroep. De uitgever kan gedurende die tijd de vereiste updates uitvoeren, maar wanneer die tijd voorbij is, vervalt de toegang van de uitgever.

De werkstroom voor het verlenen van toegang is:

1. De uitgever voegt een beheerde toepassing toe aan de marktplaats en geeft aan dat JIT-toegang beschikbaar is.

1. Tijdens de implementatie schakelt u JIT-toegang in voor uw exemplaar van de beheerde toepassing.

1. Na de implementatie u de instellingen voor JIT-toegang wijzigen.

1. De uitgever stuurt een verzoek om toegang.

1. Je keurt het verzoek goed.

Dit artikel richt zich op de acties die consumenten ondernemen om JIT-toegang en goedkeuringvan aanvragen mogelijk te maken. Zie [Just-in-time toegang aanvragen in Azure Managed Applications](request-just-in-time-access.md)voor meer informatie over het publiceren van een beheerde toepassing met JIT-toegang.

> [!NOTE]
> Als u just-in-time-toegang wilt gebruiken, moet u over een [Azure Active Directory P2-licentie beschikken.](../../active-directory/privileged-identity-management/subscription-requirements.md)

## <a name="enable-during-deployment"></a>Inschakelen tijdens implementatie

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek een marktplaatsvermelding voor een beheerde toepassing met JIT ingeschakeld. Selecteer **Maken**.

1. Terwijl u waarden voor de nieuwe beheerde toepassing verstrekt, u met de **jit-configuratiestap** JIT-toegang voor de beheerde toepassing in- of uitschakelen. Selecteer **Ja** voor **JIT Access inschakelen**. Deze optie is standaard geselecteerd voor beheerde toepassingen die zijn gedefinieerd met JIT ingeschakeld in de marketplace.

   ![Toegang configureren](./media/approve-just-in-time-access/configure-jit-access.png)

   U JIT-toegang alleen inschakelen tijdens de implementatie. Als u **Nee**selecteert, krijgt de uitgever permanente toegang tot de beheerde brongroep. U JIT-toegang later niet inschakelen.

1. Als u de standaardgoedkeuringsinstellingen wilt wijzigen, selecteert u **JIT-configuratie aanpassen**.

   ![Toegang aanpassen](./media/approve-just-in-time-access/customize-jit-access.png)

   Een beheerde toepassing met JIT ingeschakeld heeft standaard de volgende instellingen:

   * Goedkeuringsmodus – automatisch
   * Maximale toegangsduur – 8 uur
   * Fiatteurs – geen

   Wanneer de goedkeuringsmodus is ingesteld op **automatisch,** ontvangen de fiatteurs een melding voor elk verzoek, maar wordt het verzoek automatisch goedgekeurd. Wanneer de fiatteurs op **handmatige ingesteld,** ontvangen ze een melding voor elk verzoek en een van hen moet deze goedkeuren.

   De maximale duur van de activering geeft de maximale tijd aan die een uitgever kan aanvragen voor toegang tot de beheerde brongroep.

   De lijst met fiatteurs is de Azure Active Directory-gebruikers die JIT-toegangsverzoeken kunnen goedkeuren. Als u een goedkeurder wilt toevoegen, selecteert u **Goedgekeurder toevoegen** en zoeken naar de gebruiker.

   Nadat u de instelling hebt bijgewerkt, selecteert u **Opslaan**.

## <a name="update-after-deployment"></a>Bijwerken na implementatie

U de waarden voor de manier waarop aanvragen worden goedgekeurd wijzigen. Als u jit-toegang tijdens de implementatie echter niet hebt ingeschakeld, u deze later niet inschakelen.

Ga als lid van het werk om de instellingen voor een geïmplementeerde beheerde toepassing te wijzigen:

1. Selecteer in de portal de beheertoepassing.

1. Selecteer **JIT-configuratie** en wijzig de instellingen indien nodig.

   ![Toegangsinstellingen wijzigen](./media/approve-just-in-time-access/change-settings.png)

1. Selecteer **Opslaan** wanneer u klaar bent.

## <a name="approve-requests"></a>Aanvragen goedkeuren

Wanneer de uitgever om toegang vraagt, wordt u op de hoogte gesteld van het verzoek. U JIT-toegangsaanvragen rechtstreeks goedkeuren via de beheerde toepassing of voor alle beheerde toepassingen via de Azure AD Privileged Identity Management-service. Als u just-in-time-toegang wilt gebruiken, moet u over een [Azure Active Directory P2-licentie beschikken.](../../active-directory/privileged-identity-management/subscription-requirements.md)

Ga als u aanvragen goedkeuren via de beheerde toepassing:

1. Selecteer **JIT Access** voor de beheerde toepassing en selecteer **Aanvragen goedkeuren**.

   ![Aanvragen goedkeuren](./media/approve-just-in-time-access/approve-requests.png)
 
1. Selecteer het verzoek om goed te keuren.

   ![Aanvraag selecteren](./media/approve-just-in-time-access/select-request.png)

1. Geef in het formulier de reden voor de goedkeuring op en selecteer **Goedkeuren**.

Aanvragen goedkeuren via Azure AD Privileged Identity Management:

1. Selecteer **Alle services** en ga op zoek naar Azure AD Privileged Identity **Management**. Selecteer deze in de beschikbare opties.

   ![Service zoeken](./media/approve-just-in-time-access/search.png)

1. Selecteer **Aanvragen goedkeuren**.

   ![Aanvragen goedkeuren selecteren](./media/approve-just-in-time-access/select-approve-requests.png)

1. Selecteer **azure beheerde toepassingen**en selecteer het verzoek om goed te keuren.

   ![Aanvragen selecteren](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Just-in-time toegang aanvragen in Azure Managed Applications](request-just-in-time-access.md)voor meer informatie over het publiceren van een beheerde toepassing met JIT-toegang.

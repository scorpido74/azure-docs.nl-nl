---
title: Regels configureren en waarschuwingen beheren
description: Beschrijft hoe u regels configureert en waarschuwingen beheert in FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482983"
---
# <a name="configure-rules-and-manage-alerts"></a>Regels configureren en waarschuwingen beheren

Met Azure FarmBeats u regels maken op basis van de bedrijfslogica, naast de sensorgegevens die afkomstig zijn van de sensoren en apparaten die in uw farm worden geïmplementeerd. De regels activeren waarschuwingen in het systeem wanneer sensorwaarden een drempelwaarde overschrijden. Door de waarschuwingen die na de drempelwaarden zijn gemaakt, te bekijken en te analyseren, u snel reageren op eventuele problemen en de vereiste oplossingen krijgen.

## <a name="create-rule"></a>Regel maken

1. Ga op de startpagina naar **Regels.**
2. Selecteer **Nieuwe regel**. Het venster Nieuwe regel wordt weergegeven.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Voer de **regelnaam** en **regelbeschrijving in** en selecteer vervolgens een farm in het vervolgkeuzemenu **Farm selecteren.**
4. Typ de naam van uw bedrijf om de sectie Farm en **Voorwaarden** te selecteren in hetzelfde venster.  

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. Voer **in Voorwaarden**de waarden voor **Meten**, **Operator** en **Waarde**in .
6. Typ de lijstnaam in de vervolgkeuzelijst **Meten.**
7. Selecteer **+Voorwaarde toevoegen** om meer voorwaarden aan de regel toe te voegen.
8. Selecteer het **niveau Ernst**.
9. Schakel in **Actie**de knop **E-mail ingeschakeld** om e-mailwaarschuwingen in te schakelen.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Voer de **e-mailadressen** in waarnaar u de e-mailwaarschuwing wilt verzenden, samen met het **e-mailonderwerp** en **aanvullende notities.**  
11. Schakel in de **regelstatus**de knop **Ingeschakeld** schakelen in om de regel in of uit te schakelen.
    U het aantal apparaten bekijken dat door de regel wordt beïnvloed.
12. Selecteer **Toepassen** om de regel te maken.

## <a name="view-rule"></a>Regel weergeven

Op de pagina **Farm** worden de lijst met beschikbare regels weergegeven. Selecteer een **regelnaam**. In een venster worden de volgende details weergegeven die van toepassing zijn op de geselecteerde regel:
 - Regelnaam
 - Koppeling naar het bedrijf waaraan de regel is gekoppeld
 - Gemaakte datum
 - Laatst bijgewerkte datum
 - Ernstniveau
 - Regelstatus
 - Lijst van voorwaarden  
 - Aantal apparaten waarop de regel van invloed is

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Regel bewerken

Voer de volgende stappen uit om een regel te bewerken:

1. Selecteer **Regels op** de startpagina in het linkernavigatiemenu.
   Het venster regels wordt weergegeven.
2. Selecteer de regel waarvoor u wilt bewerken.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Selecteer **Bewerken** op de actiebalk, het venster **Regel bewerken** wordt weergegeven.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Wijzig de **regelnaam**en **regelbeschrijving** en selecteer vervolgens een farm in het vervolgkeuzemenu **Farm selecteren.**
5. Typ uw bedrijfsnaam om de boerderij te selecteren en **voorwaarden** worden in hetzelfde venster weergegeven.  
6. In **Voorwaarden**, bewerken **Maatregel**, **Operator** en **Waarde**.
7. Typ de lijstnaam in de vervolgkeuzelijst **Meten.**
8. Selecteer **+Voorwaarde toevoegen** om voorwaarden aan de regels toe te voegen/bewerken.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Selecteer het **ernstniveau**.  
10. Schakel in **Actie**de knop **E-mail ingeschakeld** om e-mailwaarschuwingen in te schakelen.
11. Bewerk de **e-mailadressen** die u de e-mailwaarschuwing wilt verzenden, samen met het **e-mailonderwerp** en **aanvullende notities.**  
12. Schakel in de **regelstatus**de knop **Ingeschakeld** schakelen in om de regel in of uit te schakelen.
U het aantal apparaten bekijken dat door deze regel wordt beïnvloed.
13. Selecteer **Toepassen** om de regel te bewerken.

## <a name="change-rule-status"></a>Regelstatus wijzigen

Voer de volgende stappen uit om de status van een regel te wijzigen:

1. Selecteer **Regels op** de startpagina in het linkernavigatiemenu. Het venster regels wordt weergegeven.
2. Selecteer de regel waarvoor u de status wilt wijzigen.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Selecteer **Status wijzigen** op de actiebalk. Het venster **Status wijzigen** wordt weergegeven.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Wijzig de regelstatus met de knop **Status wijzigen.**
   U het aantal apparaten weergeven dat wordt beïnvloed door de regel.
4. Selecteer **Toepassen** om de regelstatus te wijzigen.

## <a name="delete-rule"></a>Regel verwijderen

Voer de volgende stappen uit om een regel te verwijderen:

1. Selecteer **Regels op** de startpagina in het linkernavigatiemenu. Het venster regels wordt weergegeven.
2. Selecteer de regel waarvoor u wilt verwijderen.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Selecteer **Verwijderen op** de actiebalk.

    ![Project Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Het dialoogvenster **Regel verwijderen** wordt weergegeven. Selecteer **Verwijderen**.

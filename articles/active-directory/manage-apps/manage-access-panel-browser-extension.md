---
title: Problemen met de Azure Access-paneel uitbreiding voor IE oplossen | Microsoft Docs
description: Groeps beleid gebruiken voor het implementeren van de invoeg toepassing Internet Explorer voor de portal mijn apps.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16abfbeacd972ee8b0ab55f09945e687c95f0093
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763258"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Problemen met de uitbrei ding van het toegangs paneel voor Internet Explorer oplossen

Dit artikel helpt u bij het oplossen van de volgende problemen:

* U hebt geen toegang tot uw apps via de portal mijn apps terwijl u Internet Explorer gebruikt.
* U ziet het bericht ' software installeren ', zelfs als u de software al hebt geïnstalleerd.

Als u een beheerder bent, raadpleegt u [de uitbrei ding van het toegangs paneel voor Internet Explorer implementeren met behulp van Groepsbeleid](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Het diagnostische hulp programma uitvoeren

U kunt installatie problemen met de uitbrei ding van het toegangs paneel diagnosticeren door het diagnostische hulp programma voor het toegangs paneel te downloaden en uit te voeren. 

Het diagnostische hulp programma downloaden en installeren:

1. [Selecteer deze koppeling om het diagnostische hulp programma te downloaden.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Open het bestand en pak de inhoud naar uw computer uit.
1. Als u het hulp programma wilt uitvoeren, klikt u met de rechter muisknop op het bestand met de naam *AccessPanelExtensionDiagnosticTool.js* en selecteert u **openen met**  >  **micro soft Windows-scripthost**.

    ![Openen met > micro soft Windows-scripthost](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Bekijk de diagnostische resultaten die worden weer gegeven en selecteer **Ja** om de problemen op te lossen. Het dialoog venster **resultaten controleren** wordt weer gegeven met informatie over wat u moet doen als de extensie niet werkt.  
1. Lees het bericht en selecteer **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Controleer of de uitbrei ding voor het toegangs paneel is ingeschakeld

Controleren of u de extensie voor het toegangs paneel hebt ingeschakeld in Internet Explorer:

1. Selecteer in Internet Explorer het **tandwiel pictogram** in de rechter bovenhoek van het venster en selecteer **Internet opties**.
1. Ga naar het tabblad **Program ma's** en selecteer **invoeg toepassingen beheren**.
1. Selecteer **uitbrei ding toegangs paneel** in de sectie **micro soft Corporation** en selecteer **inschakelen**.
1. Als u de wijzigingen wilt opslaan, sluit u alle vensters van de Internet Explorer-browser die u hebt geopend. De wijziging wordt van kracht wanneer u Internet Explorer de volgende keer opent.

## <a name="enable-extensions-for-inprivate-browsing"></a>Extensies inschakelen voor InPrivate-navigatie

Uitbrei dingen voor InPrivate-navigatie inschakelen:

1. Selecteer in Internet Explorer het **tandwiel pictogram** in de rechter bovenhoek van het venster en selecteer **Internet opties**.
1. Ga naar het tabblad **Privacy** en controleer of het selectie vakje **werk balken en uitbrei dingen uitschakelen bij het starten van InPrivate-Browsing** is uitgeschakeld.
1. Als u de wijzigingen wilt opslaan, sluit u alle vensters van de Internet Explorer-browser die u hebt geopend. De wijziging wordt van kracht wanneer u Internet Explorer de volgende keer opent.

## <a name="uninstall-the-access-panel-extension"></a>De uitbrei ding voor het toegangs paneel verwijderen

De uitbrei ding van het toegangs paneel verwijderen van uw computer:

1. Zoek in het configuratie scherm op *verwijderen*.
1. Selecteer in de zoek resultaten de optie **een programma verwijderen**.

    ![Selecteer de optie een programma verwijderen in het configuratie scherm](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Selecteer in de lijst de **toegangs paneel uitbreiding** en selecteer **verwijderen**.

    ![De uitbrei ding voor het toegangs paneel verwijderen](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. U kunt vervolgens de uitbrei ding opnieuw installeren om te zien of het probleem is opgelost.

Als u problemen ondervindt bij het verwijderen van de uitbrei ding, kunt u deze ook uit het [micro soft](https://go.microsoft.com/?linkid=9779673) -hulp programma herstellen.

## <a name="related-articles"></a>Verwante artikelen:

* [Toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](what-is-single-sign-on.md)
* [De uitbrei ding van het toegangs paneel voor Internet Explorer implementeren met behulp van groepsbeleid](deploy-access-panel-browser-extension.md)

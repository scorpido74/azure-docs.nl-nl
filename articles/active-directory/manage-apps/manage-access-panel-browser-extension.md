---
title: Problemen met de Azure Access Panel Extension for IE oplossen | Microsoft Documenten
description: Groepsbeleid gebruiken om de invoegtoepassing Internet Explorer te implementeren voor de mijn apps-portal.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723920"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Problemen met de access panelextensie voor Internet Explorer oplossen

In dit artikel u de volgende problemen oplossen:

* U hebt geen toegang tot uw apps via de mijn apps-portal tijdens het gebruik van Internet Explorer.
* U ziet het bericht 'Software installeren', ook al hebt u de software al geïnstalleerd.

Zie De extensie van [het toegangspaneel voor Internet Explorer implementeren met groepsbeleid](deploy-access-panel-browser-extension.md)als u een beheerder bent.

## <a name="run-the-diagnostic-tool"></a>Het diagnosegereedschap uitvoeren

U installatieproblemen met de Access Panel Extension diagnosticeren door het diagnostisch hulpprogramma voor het toegangspaneel te downloaden en uit te voeren. 

Ga als volgende voor het downloaden en installeren van het diagnoseprogramma:

1. [Selecteer deze koppeling om het diagnoseprogramma te downloaden.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Open het bestand en haal de inhoud naar uw computer.
1. Als u het gereedschap wilt uitvoeren, klikt u met de rechtermuisknop op het bestand *accesspanelextensiondiagnostictool.js* en selecteert u **Openen met** > **Microsoft Windows Based Script Host**.

    ![Openen met > Microsoft Windows Based Script Host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Controleer de diagnostische resultaten die worden weergegeven en selecteer **Ja** om de problemen op te lossen. Het dialoogvenster **Resultaten controleren** wordt weergegeven met informatie over wat u moet doen als de extensie niet werkt.  
1. Lees het bericht en selecteer **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Controleren of de extensie Access Panel is ingeschakeld

Ga als volgt te werk om te controleren of u de extensie Access Panel in Internet Explorer hebt ingeschakeld:

1. Selecteer in Internet Explorer het **tandwielpictogram** in de rechterbovenhoek van het venster en selecteer **internetopties**.
1. Ga naar het tabblad **Programma's** en selecteer **Invoegtoepassingen beheren**.
1. Selecteer **Access Panel Extension** in de sectie Microsoft **Corporation** en selecteer **Inschakelen**.
1. Als u de wijzigingen wilt opslaan, sluit u alle windows van de Internet Explorer-browser die u hebt geopend. De wijziging wordt van kracht wanneer u internet explorer de volgende keer opent.

## <a name="enable-extensions-for-inprivate-browsing"></a>Extensies inschakelen voor InPrivate Browsing

Extensies inschakelen voor InPrivate Browsing:

1. Selecteer in Internet Explorer het **tandwielpictogram** in de rechterbovenhoek van het venster en selecteer **internetopties**.
1. Ga naar het tabblad **Privacy** en controleer of het selectievakje **Werkbalken en extensies uitschakelen wanneer InPrivate Browsing wordt gestart,** is uitgeschakeld.
1. Als u de wijzigingen wilt opslaan, sluit u alle windows van de Internet Explorer-browser die u hebt geopend. De wijziging wordt van kracht wanneer u internet explorer de volgende keer opent.

## <a name="uninstall-the-access-panel-extension"></a>De extensie van het access-paneel verwijderen

Ga als lid van het e-selectiescherm van uw computer:

1. Zoek in het Configuratiescherm naar *verwijderen*.
1. Selecteer in de zoekresultaten De optie **Een programma verwijderen**.

    ![De optie Een programma verwijderen selecteren in het Configuratiescherm](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. Selecteer **access panelextensie** in de lijst en selecteer **Verwijderen**.

    ![De extensie van het access-paneel verwijderen](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. U vervolgens proberen om de extensie opnieuw te installeren om te zien of het probleem is opgelost.

Als u problemen ondervindt bij het verwijderen van de extensie, u deze ook verwijderen met het hulpprogramma [Microsoft Fix It.](https://go.microsoft.com/?linkid=9779673)

## <a name="related-articles"></a>Verwante artikelen:

* [Toepassingstoegang en één aanmelding met Azure Active Directory](what-is-single-sign-on.md)
* [De extensie van het toegangspaneel voor Internet Explorer implementeren met groepsbeleid](deploy-access-panel-browser-extension.md)

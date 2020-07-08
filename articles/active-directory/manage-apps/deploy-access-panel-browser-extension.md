---
title: Azure Access Panel-extensie voor Internet Explorer implementeren met behulp van een GPO | Microsoft Docs
description: Groeps beleid gebruiken voor het implementeren van de invoeg toepassing Internet Explorer voor de portal mijn apps.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94c434a2892060acfdd56c496a31e41597c21357
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763428"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Procedure: de uitbrei ding van het toegangs paneel voor Internet Explorer implementeren met behulp van groeps beleid

In deze zelf studie ziet u hoe u groeps beleid gebruikt om de uitbrei ding van het toegangs paneel voor Internet Explorer op afstand te installeren op de computers van uw gebruikers. Deze extensie is vereist voor Internet Explorer-gebruikers die zich moeten aanmelden bij apps die zijn geconfigureerd met [eenmalige aanmelding op basis van wacht woorden](what-is-single-sign-on.md#password-based-sso).

Het is raadzaam dat beheerders de implementatie van deze uitbrei ding automatiseren. Anders moeten gebruikers de uitbrei ding zelf downloaden en installeren, wat gevoelig is voor gebruikers fout en beheerders machtigingen vereist. In deze zelf studie wordt één methode beschreven voor het automatiseren van software-implementaties met behulp van groeps beleid. [Meer informatie over groeps beleid.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

De uitbrei ding van het toegangs paneel is ook beschikbaar voor [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) en [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), en u hebt geen beheerders machtigingen nodig om te installeren.

## <a name="prerequisites"></a>Vereisten

* U hebt [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)ingesteld en u hebt de computers van uw gebruikers toegevoegd aan uw domein.
* U moet beschikken over de machtiging instellingen bewerken om het groepsbeleid object (GPO) te bewerken. Standaard hebben leden van de volgende beveiligings groepen deze machtiging: domein beheerders, ondernemings Administrators en groepsbeleid Maker eigen aren. [Meer informatie.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Stap 1: het distributie punt maken

Eerst moet u het installatie pakket op een netwerk locatie plaatsen die toegankelijk is voor de computers waarop u de uitbrei ding op afstand wilt installeren. Voer de volgende stappen uit om dit te doen:

1. Meld u aan bij de server als beheerder.
1. Ga in het venster **Serverbeheer** naar **bestanden en opslag Services**.

    ![Bestanden en opslag Services openen](./media/deploy-access-panel-browser-extension/files-services.png)

1. Ga naar het tabblad **shares** . Klik vervolgens op **taken**  >  **nieuwe share...**

    ![Scherm afbeelding laat zien waar een nieuwe share kan worden gevonden vanuit het scherm taken](./media/deploy-access-panel-browser-extension/shares.png)

1. Voltooi de **wizard Nieuwe share** en stel machtigingen in om ervoor te zorgen dat deze toegankelijk zijn vanaf de computers van uw gebruikers. [Meer informatie over shares.](https://technet.microsoft.com/library/cc753175.aspx)
1. Down load het volgende Microsoft Windows Installer-pakket (MSI-bestand): [toegangs venster Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Kopieer het installatie pakket naar een gewenste locatie op de share.

    ![Het MSI-bestand kopiëren naar de share](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Controleer of de client computers toegang hebben tot het installatie pakket vanaf de share.

## <a name="step-2-create-the-group-policy-object"></a>Stap 2: het groeps beleidsobject maken

1. Meld u aan bij de server die als host fungeert voor uw Active Directory Domain Services (AD DS)-installatie.
1. Ga in Serverbeheer naar **hulpprogram ma's**  >  **Groepsbeleid beheer**.

    ![Ga naar Hulpprogram Ma's > groepsbeleid beheer](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. Bekijk in het linkerdeel venster van het venster **Groepsbeleid beheer** de hiërarchie van uw organisatie-eenheid (OE) en bepaal op welk bereik u het groeps beleid wilt Toep assen. U kunt bijvoorbeeld kiezen voor een kleine organisatie-eenheid om te implementeren voor een aantal gebruikers om te testen, of u kunt een OE op het hoogste niveau kiezen om te implementeren in uw hele organisatie.

   > [!NOTE]
   > Als u uw organisatie-eenheden (ou's) wilt maken of bewerken, schakelt u terug naar de Serverbeheer en gaat u naar **hulpprogram ma's**  >  **Active Directory gebruikers en computers**.

1. Wanneer u een OE hebt geselecteerd, klikt u er met de rechter muisknop op en selecteert u **een groeps beleidsobject in dit domein maken en koppelt u deze hier...**

    ![Scherm afbeelding toont de optie een nieuwe groeps beleidsobject maken](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. Typ in de opdracht **Nieuw groeps beleidsobject** een naam voor het nieuwe Groepsbeleid-object.
1. Klik met de rechter muisknop op het groepsbeleid object dat u hebt gemaakt en selecteer **bewerken**.

## <a name="step-3-assign-the-installation-package"></a>Stap 3: het installatie pakket toewijzen

1. Bepaal of u de extensie wilt implementeren op basis van **computer configuratie** of **gebruikers configuratie**. Wanneer u [computer configuratie](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)gebruikt, wordt de uitbrei ding geïnstalleerd op de computer, ongeacht de gebruikers die zich aanmelden. Met [gebruikers configuratie](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)hebben gebruikers de uitbrei ding voor hen geïnstalleerd, ongeacht de computers waarop ze zich aanmelden.
1. Ga in het linkerdeel venster van het venster **Groepsbeleidsbeheer-editor** naar een van de volgende mappaden, afhankelijk van het type configuratie dat u hebt gekozen:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Klik met de rechter muisknop op **Software-installatie**en selecteer **Nieuw**  >  **pakket...**
1. Ga naar de gedeelde map met het installatie pakket uit [stap 1: Maak het distributie punt](#step-1-create-the-distribution-point), selecteer het. msi-bestand en klik op **openen**.

   > [!IMPORTANT]
   > Als de share zich op deze server bevindt, controleert u of u toegang hebt tot het. msi-bestand via het netwerkpad in plaats van het lokale bestandspad.

    ![Het installatie pakket selecteren in de gedeelde map](./media/deploy-access-panel-browser-extension/select-package.png)

1. Selecteer in de prompt **software implementeren** de optie **toegewezen** voor uw implementatie methode. Klik vervolgens op **OK**.

De uitbrei ding wordt nu geïmplementeerd in de organisatie-eenheid die u hebt geselecteerd. [Meer informatie over Installatie van software van groepsbeleid.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Stap 4: de uitbrei ding voor Internet Explorer automatisch inschakelen

Naast het uitvoeren van het installatie programma moet elke extensie voor Internet Explorer expliciet worden ingeschakeld voordat deze kan worden gebruikt. Volg de onderstaande stappen om de uitbrei ding voor het toegangs paneel in te scha kelen met groeps beleid:

1. Ga in het venster **Groepsbeleidsbeheer-editor** naar een van de volgende paden, afhankelijk van het type configuratie dat u hebt gekozen in [stap 3: het installatie pakket toewijzen](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Klik met de rechter muisknop op de **lijst met invoeg toepassingen**en selecteer **bewerken**.

    ![Klik met de rechter muisknop op de lijst add-on en selecteer Bewerken](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. Selecteer **ingeschakeld**in het venster Overzicht van de **invoeg toepassing** . Klik vervolgens onder het gedeelte **Opties** op **weer geven...**.

    ![Klik op inschakelen en klik vervolgens op weer geven...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. In het venster **inhoud weer geven** voert u de volgende stappen uit:

   1. Kopieer en plak de volgende klasse-ID voor de eerste kolom (het veld **waardenaam** ):`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Typ voor de tweede kolom (het veld **waarde** ) de volgende waarde:`1`
   1. Klik op **OK** om het venster **inhoud weer geven** te sluiten.

      ![Vul de waarden in zoals opgegeven in de vorige stap](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Klik op **OK** om uw wijzigingen toe te passen en sluit het venster **overzicht van de invoeg toepassing** .

De uitbrei ding moet nu zijn ingeschakeld voor de computers in de geselecteerde organisatie-eenheid. [Meer informatie over het gebruik van groeps beleid om invoeg toepassingen van Internet Explorer in of uit te scha kelen.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Stap 5 (optioneel): de vraag ' wacht woord onthouden ' uitschakelen

Wanneer gebruikers zich aanmelden bij websites met de uitbrei ding van het toegangs paneel, wordt in Internet Explorer de volgende prompt weer gegeven waarin u wordt gevraagd om uw wacht woord op te geven?

![Toont het ' wilt u uw wacht woord opslaan... ' verschijnt](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Als u wilt voor komen dat uw gebruikers deze prompt kunnen zien, volgt u de onderstaande stappen om te voor komen dat automatisch aanvullen wacht woorden onthouden:

1. Ga in het **Groepsbeleidsbeheer-editor** -venster naar het pad dat hieronder wordt weer gegeven. Deze configuratie-instelling is alleen beschikbaar onder **gebruikers configuratie**.

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Zoek de instelling **met de naam inschakelen de functie automatisch aanvullen voor gebruikers namen en wacht woorden op formulieren**.

   > [!NOTE]
   > In vorige versies van Active Directory kan deze instelling worden vermeld met de naam **niet automatisch aanvullen toestaan om wacht woorden op te slaan**. De configuratie voor die instelling wijkt af van de instelling die in deze zelf studie wordt beschreven.

    ![Vergeet niet om dit te zoeken onder gebruikers instellingen](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Klik met de rechter muisknop op de bovenstaande instelling en selecteer **bewerken**.
1. In het venster met **de titel de functie automatisch aanvullen voor gebruikers namen en wacht woorden op formulieren inschakelen**, selecteert u **uitgeschakeld**.

    ![Selecteer de optie uitgeschakeld voor de functie automatisch aanvullen inschakelen](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Klik op **OK** om deze wijzigingen toe te passen en het venster te sluiten.

Gebruikers kunnen hun referenties niet meer opslaan of automatisch aanvullen gebruiken om toegang te krijgen tot eerder opgeslagen referenties. Dit beleid staat gebruikers echter toe om automatisch aanvullen te blijven gebruiken voor andere typen formulier velden, zoals zoek velden.

> [!WARNING]
> Als dit beleid wordt ingeschakeld nadat gebruikers bepaalde referenties hebben opgeslagen, worden de referenties die al zijn opgeslagen *niet* gewist met dit beleid.

## <a name="step-6-testing-the-deployment"></a>Stap 6: de implementatie testen

Volg de onderstaande stappen om te controleren of de implementatie van de extensie is geslaagd:

1. Als u hebt geïmplementeerd met **computer configuratie**, meldt u zich aan bij een client computer die hoort bij de organisatie-eenheid die u hebt geselecteerd in [stap 2: Maak het Groepsbeleid-object](#step-2-create-the-group-policy-object). Als u hebt geïmplementeerd met behulp van **gebruikers configuratie**, moet u zich aanmelden als een gebruiker die lid is van de organisatie-eenheid.
1. Het kan zijn dat er een aantal aanmeldingen voor de groeps beleids wijzigingen worden uitgevoerd voor een volledige update met deze computer. Als u de update wilt afdwingen, opent u een **opdracht prompt** venster en voert u de volgende opdracht uit:`gpupdate /force`
1. U moet de computer opnieuw opstarten om de installatie te kunnen uitvoeren. Opstart chassis kan aanzienlijk meer tijd in beslag nemen dan gebruikelijk tijdens de installatie van de extensie.
1. Nadat de computer opnieuw is opgestart, opent u **Internet Explorer**. Klik in de rechter bovenhoek van het venster op **extra** (het tandwiel pictogram) en selecteer vervolgens **invoeg toepassingen beheren**.
1. Controleer in het venster **add-ins beheren** of de **uitbrei ding voor het toegangs paneel** is geïnstalleerd en of de **status** is ingesteld op **ingeschakeld**.

   ![Controleer of de uitbrei ding voor het toegangs paneel is geïnstalleerd en ingeschakeld](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Lees meer

* [Toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](what-is-single-sign-on.md)
* [Problemen met de uitbrei ding van het toegangs paneel voor Internet Explorer oplossen](manage-access-panel-browser-extension.md)

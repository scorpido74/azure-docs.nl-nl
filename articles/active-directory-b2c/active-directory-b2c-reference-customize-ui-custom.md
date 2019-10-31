---
title: De gebruikers interface van een gebruiker met aangepaste beleids regels aanpassen | Microsoft Docs
description: Meer informatie over Azure Active Directory B2C aangepast beleid.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7e4714de9868dbd540e2e662b22a22da6df6514b
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147531"
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>De gebruikers interface van een gebruiker met aangepast beleid aanpassen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> Dit artikel is een geavanceerde beschrijving van de werking van UI-aanpassing en het inschakelen van Azure AD B2C aangepast beleid, met behulp van het Framework voor identiteits ervaring.


Een naadloze gebruikers ervaring is de sleutel voor een oplossing voor Business-to-consumer. Een naadloze gebruikers ervaring is een ervaring, hetzij op het apparaat of in de browser, waar de door de service geplaatste gebruikers niet kunnen worden onderscheiden van de klanten service die ze gebruiken.

## <a name="understand-the-cors-way-for-ui-customization"></a>Meer informatie over de CORS-methode voor UI-aanpassing

Met Azure AD B2C kunt u het uiterlijk van de gebruikers ervaring (UX) aanpassen op de verschillende pagina's die worden aangeboden en weer gegeven door Azure AD B2C met uw aangepaste beleids regels.

Daarom voert Azure AD B2C code uit in de browser van uw klant en maakt gebruik van de moderne en standaard aanpak voor [Cross-Origin Resource Sharing (CORS)](https://www.w3.org/TR/cors/) voor het laden van aangepaste inhoud van een specifieke URL die u opgeeft in een aangepast beleid om naar uw HTML5/CSS te verwijzen. sjablonen. CORS is een mechanisme voor het toestaan van beperkte bronnen, zoals letter typen, op een webpagina die moet worden aangevraagd vanuit een ander domein dan het domein waarvan de bron afkomstig is.

Vergeleken met de oude traditionele manier, waarbij sjabloon pagina's het eigendom zijn van de oplossing waarbij u beperkte tekst en afbeeldingen hebt geleverd, waarbij beperkte controle van de indeling en het gevoel tot meer dan problemen leidt om een naadloze ervaring te krijgen, kan de CORS-methode ondersteunt HTML5 en CSS en biedt u de volgende opties:

- Als host voor de inhoud en de oplossing worden de besturings elementen van het script aan client zijde geïnjecteerd.
- Volledige controle over elke pixel van de lay-out.

U kunt zoveel inhouds pagina's opgeven als u wilt, door HTML5/CSS-bestanden als toepasselijk te maken.

> [!NOTE]
> Uit veiligheids overwegingen is het gebruik van Java script momenteel geblokkeerd voor aanpassing. 

In elk van uw HTML5/CSS-sjablonen geeft u een *anker* element op dat overeenkomt met het vereiste `<div id="api">` element in de HTML-of de inhouds pagina zoals hieronder. Azure AD B2C vereist dat alle inhouds pagina's deze specifieke div-elementen bevatten.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

Azure AD B2C-gerelateerde inhoud voor de pagina wordt ingevoegd in deze DIV-element, terwijl de rest van de pagina u kunt beheren. De Azure AD B2C java script-code wordt opgehaald in uw inhoud en HTML wordt in dit specifieke div-element ingevoegd. Azure AD B2C injecteert de volgende besturings elementen, indien van toepassing: besturings element voor het kiezen van accounts, logboek besturings elementen, multi-factor (momenteel op telefoon gebaseerde) besturings elementen en kenmerk verzameling besturings elementen. Azure AD B2C zorgt ervoor dat alle besturings elementen compatibel zijn met HTML5 en toegankelijk zijn. alle besturings elementen kunnen volledig worden opgemaakt en de versie van het besturings element wordt niet Regress.

De samengevoegde inhoud wordt uiteindelijk weer gegeven als het dynamische document voor uw Consumer.

Om ervoor te zorgen dat alles werkt zoals verwacht, moet u het volgende doen:

- Zorg ervoor dat uw inhoud compatibel is met HTML5 en toegankelijk is
- Zorg ervoor dat de inhouds server is ingeschakeld voor CORS.
- Inhoud via HTTPS verwerken.
- Gebruik absolute URL'S zoals `https://yourdomain/content` voor alle koppelingen en CSS-inhoud.

> [!TIP]
> U kunt de site https://test-cors.org/ gebruiken om te controleren of de site waarop u uw inhoud beheert, CORS heeft ingeschakeld en CORS-aanvragen test. Dankzij deze site kunt u de CORS-aanvraag verzenden naar een externe server (om te testen of CORS wordt ondersteund), of de CORS-aanvraag verzenden naar een test server (om bepaalde functies van CORS te verkennen).

> [!TIP]
> De site https://enable-cors.org/ ook een meer bruikbare bronnen voor CORS.

Dankzij deze methode op basis van CORS hebben eind gebruikers een consistente ervaring tussen uw toepassing en de pagina's die worden geleverd door Azure AD B2C.

## <a name="create-a-storage-account"></a>Maak een opslagaccount

Als vereiste moet u een opslag account maken. U hebt een Azure-abonnement nodig om een Azure Blob Storage-account te maken. U kunt een gratis proef versie registreren op de [Azure-website](https://azure.microsoft.com/pricing/free-trial/).

1. Open een browser sessie en navigeer naar het [Azure Portal](https://portal.azure.com).
2. Meld u aan met uw beheerders referenties.
3. Klik op **een resource maken** > **opslag** > **opslag account**.  Het deel venster **opslag account maken** wordt geopend.
4. Geef bij **naam**een naam op voor het opslag account, bijvoorbeeld *contoso369b2c*. Deze waarde wordt later aangeduid als *storageAccountName*.
5. Kies de juiste selecties voor de prijs categorie, de resource groep en het abonnement. Zorg ervoor dat u de optie **vastmaken aan start Board** hebt ingeschakeld. Klik op **Maken**.
6. Ga terug naar de start Board en klik op het opslag account dat u hebt gemaakt.
7. Klik in de sectie **Services** op **blobs**. Er wordt een **BLOB service deel venster** geopend.
8. Klik op **+ container**.
9. Geef bij **naam**een naam voor de container op, bijvoorbeeld *B2C*. Deze waarde wordt later aangeduid als *containernaam*.
9. Selecteer **BLOB** als het **toegangs type**. Klik op **Maken**.
10. De container die u hebt gemaakt, wordt weer gegeven in de lijst in het **deel venster BLOB service**.
11. Sluit het deel venster **blobs** .
12. Klik in het **deel venster opslag account**op het **sleutel** pictogram. Een **toegangs toetsen venster** wordt geopend.  
13. Noteer de waarde van **key1**. Deze waarde wordt later aangeduid als *key1*.

## <a name="downloading-the-helper-tool"></a>Het hulp programma voor hulp downloaden

1.  Down load het hulp programma voor hulp van [github](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip).
2.  Sla het bestand *B2C-AzureBlobStorage-client-Master. zip* op de lokale computer op.
3.  Pak de inhoud van het bestand B2C-AzureBlobStorage-Client-master. zip uit op uw lokale schijf, bijvoorbeeld in de map **UI-Customization-Pack** , waarmee een *B2C-AzureBlobStorage-client-Master* -map wordt gemaakt.
4.  Open die map en pak de inhoud van het archief bestand *B2CAzureStorageClient. zip* in.

## <a name="upload-the-ui-customization-pack-sample-files"></a>De voorbeeld bestanden van de gebruikers interface-aanpassing-Pack uploaden

1.  Ga met behulp van Windows Verkenner naar de map *B2C-AzureBlobStorage-client-Master* onder de door de *gebruikers interface-aanpassing-Pack-* map gemaakt in de vorige sectie.
2.  Voer het bestand *B2CAzureStorageClient. exe* uit. Dit programma uploadt alle bestanden in de map die u opgeeft voor uw opslag account en schakelt CORS-toegang in voor die bestanden.
3.  Wanneer u hierom wordt gevraagd, geeft u het volgende op: a.  De naam van uw opslag account, *storageAccountName*, bijvoorbeeld *contoso369b2c*.
    b.  De primaire toegangs sleutel van uw Azure Blob-opslag, *key1*, bijvoorbeeld *contoso369b2c*.
    c.  De naam van uw opslag-Blob-opslag container, *containerName*, bijvoorbeeld *B2C*.
    d.  Het pad van de voorbeeld bestanden van het *Starter Pack* , bijvoorbeeld *. \B2CTemplates\wingtiptoys*.

Als u de voor gaande stappen hebt gevolgd, verwijst de HTML5-en CSS-bestanden van het *UI-aanpassings pakket* voor de fictieve bedrijfs **wingtiptoys** naar uw opslag account.  U kunt controleren of de inhoud correct is geüpload door het deel venster gerelateerde container in de Azure Portal te openen. U kunt er ook voor zorgen dat de inhoud correct is geüpload door de pagina vanuit een browser te openen. Zie voor meer informatie [Azure Active Directory B2C: een hulp programma dat wordt gebruikt om de functie voor het aanpassen van de gebruikers interface (UI) van de pagina te demonstreren](active-directory-b2c-reference-ui-customization-helper-tool.md).

## <a name="ensure-the-storage-account-has-cors-enabled"></a>Zorg ervoor dat CORS is ingeschakeld voor het opslag account

CORS (cross-Origin Resource Sharing) moet zijn ingeschakeld op uw eind punt om de inhoud van Azure AD B2C te laden. Dit komt doordat de inhoud wordt gehost op een ander domein dan het domein Azure AD B2C de pagina van kan worden bewaard.

Ga door met de volgende stappen om te controleren of de opslag waarop u uw inhoud host, CORS is ingeschakeld:

1. Open een browser sessie en ga naar de pagina *Unified. html* met de volledige URL van de locatie in uw opslag account `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html`. Bijvoorbeeld https://contoso369b2c.blob.core.windows.net/b2c/unified.html.
2. Navigeer naar https://test-cors.org. Op deze site kunt u controleren of CORS is ingeschakeld voor de pagina die u gebruikt.  
   <!--
   ![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
   -->

3. In **externe URL**voert u de volledige URL voor uw Unified. HTML-inhoud in en klikt u op **aanvraag verzenden**.
4. Controleer of de uitvoer in de sectie **resultaten** de *status XHR bevat: 200*, wat aangeeft dat CORS is ingeschakeld.
   <!--
   ![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
   -->
   Het opslag account moet nu een BLOB-container met de naam *B2C* in de afbeelding met de volgende wingtiptoys-sjablonen van het *Starter Pack*bevatten.

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

In de volgende tabel wordt het doel van de voor gaande HTML5-pagina's beschreven.

| HTML5-sjabloon | Beschrijving |
|----------------|-------------|
| *Phone factor. html* | Deze pagina kan worden gebruikt als een sjabloon voor een multi-factor Authentication-pagina. |
| *resetpassword. html* | Deze pagina kan worden gebruikt als een sjabloon voor een verg eten wacht woord pagina. |
| *selfasserted. html* | Deze pagina kan worden gebruikt als een sjabloon voor een registratie pagina voor een sociaal account, een registratie pagina voor een lokaal account of een aanmeldings pagina van een lokaal account. |
| *Unified. html* | Deze pagina kan worden gebruikt als een sjabloon voor een Unified Signing-of aanmeldings pagina. |
| *updateprofile. html* | Deze pagina kan worden gebruikt als een sjabloon voor een profiel update-pagina. |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>Een koppeling naar uw HTML5/CSS-sjablonen toevoegen aan uw gebruikers traject

U kunt een koppeling naar uw HTML5/CSS-sjablonen toevoegen aan uw gebruikers traject door rechtstreeks een aangepast beleid te bewerken.

De aangepaste HTML5/CSS-sjablonen die moeten worden gebruikt in uw gebruikers traject, moeten worden opgegeven in een lijst met inhouds definities die kunnen worden gebruikt in deze gebruikers ritten. Hiervoor moet een optioneel *\<ContentDefinitions >* XML-element worden gedeclareerd onder het gedeelte *\<BuildingBlocks >* van het XML-bestand van uw aangepaste beleid.

In de volgende tabel wordt de set met inhouds definitie-Id's beschreven die worden herkend door de Azure AD B2C engine voor identiteits ervaring en het type pagina's dat eraan is gekoppeld.

| ID van de inhouds definitie | Beschrijving |
|-----------------------|-------------|
| *API. error* | **Fout pagina**. Deze pagina wordt weer gegeven wanneer er een uitzonde ring of een fout wordt aangetroffen. |
| *API. idpselections* | Pagina voor het selecteren van de **identiteits provider**. Deze pagina bevat een lijst met id-providers waaruit de gebruiker kan kiezen tijdens het aanmelden. Deze providers zijn ondernemings-id-providers, sociale id-providers, zoals Facebook, Google + of lokale accounts (op basis van e-mail adres of gebruikers naam). |
| *API. idpselections. signup* | **Selectie van ID-provider voor registratie**. Deze pagina bevat een lijst met id-providers waaruit de gebruiker kan kiezen tijdens het aanmelden. Deze providers zijn ondernemings-id-providers, sociale id-providers, zoals Facebook, Google + of lokale accounts (op basis van e-mail adres of gebruikers naam). |
| *API. localaccountpasswordreset* | **Wachtwoord pagina verg eten**. Deze pagina bevat een formulier dat de gebruiker moet invullen om hun wacht woord opnieuw in te stellen.  |
| *API. localaccountsignin* | **Aanmeldings pagina voor het lokale account**. Deze pagina bevat een aanmeld formulier dat de gebruiker moet invullen bij het aanmelden met een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan een tekstvak voor tekst invoer en wacht woord bevatten. |
| *API. localaccountsignup* | De **registratie pagina van het lokale account**. Deze pagina bevat een aanmeld formulier dat de gebruiker moet invullen bij het aanmelden voor een lokaal account dat is gebaseerd op een e-mail adres of een gebruikers naam. Het formulier kan verschillende invoer besturings elementen bevatten, zoals een tekstinvoervak, een vak voor het invoeren van een wacht woord, keuze rondje, vervolg keuze vakjes en selectie vakjes met meerdere selecties. |
| *API. Phone factor* | **Multi-factor Authentication-pagina**. Op deze pagina kunnen gebruikers hun telefoon nummers (met behulp van tekst of spraak) verifiëren tijdens het registreren of aanmelden. |
| *API. selfasserted* | De **registratie pagina voor het sociaal account**. Deze pagina bevat een aanmeld formulier dat de gebruiker moet invullen bij het aanmelden met een bestaand account van een id-provider voor sociale netwerken, zoals Facebook of Google +. Deze pagina is vergelijkbaar met de voor gaande aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| *API. selfasserted. profileupdate* | **Pagina Profiel bijwerken**. Deze pagina bevat een formulier dat de gebruiker kan gebruiken om het profiel bij te werken. Deze pagina is vergelijkbaar met de voor gaande aanmeldings pagina voor het sociaal account, met uitzonde ring van de velden voor het invoeren van wacht woorden. |
| *API. signuporsignin* | **Uniforme registratie-of aanmeldings pagina**.  Op deze pagina kunt u aanmelden & aanmelden van gebruikers, die ondernemings-id-providers, sociale id-providers, zoals Facebook of Google + of lokale accounts kunnen gebruiken.

## <a name="next-steps"></a>Volgende stappen
[Naslag informatie: begrijpen hoe aangepaste beleids regels werken met het Framework voor identiteits ervaring in B2C](active-directory-b2c-reference-custom-policies-understanding-contents.md)

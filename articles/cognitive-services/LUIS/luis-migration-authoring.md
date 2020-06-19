---
title: Migreren naar Azure-resource voor het ontwerpen van 2
titleSuffix: Azure Cognitive Services
description: Migreer naar een Azure authoring-bron sleutel 2.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 06/17/2020
ms.author: diberry
ms.openlocfilehash: 5f0778436db7bd8c3a09e3ba346d8a9a6c4af454
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84983678"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Migreren naar een Azure-resource bewerkings sleutel

Language Understanding-ontwerp verificatie (LUIS) is gewijzigd van een e-mail account in een Azure-resource. Als dat nog niet is vereist, wordt het overschakelen naar een Azure-resource in de toekomst afgedwongen.


## <a name="what-is-migration"></a>Wat is migratie?

Migratie is het proces van het wijzigen van de ontwerp verificatie van een e-mail account in een Azure-resource. Uw account wordt gekoppeld aan een Azure-abonnement en een Azure-ontwerp bron nadat u de migratie hebt gemigreerd. **Alle LUIS-gebruikers (eigen aren of mede werkers) zullen uiteindelijk moeten worden gemigreerd.** Migratie moet worden uitgevoerd vanuit de LUIS-Portal. Als u de ontwerp sleutels maakt, zoals met de LUIS CLI, moet u het migratie proces nog steeds in de LUIS-Portal volt ooien. U kunt nog steeds co-auteurs op uw toepassingen hebben na de migratie, maar deze worden toegevoegd op het Azure-resource niveau in plaats van op toepassings niveau.

> [!Note]
> Vóór de migratie worden mede-auteurs deel _nemers_ genoemd op het niveau van de Luis-app. Na de migratie wordt de Azure-rol van _Inzender_ gebruikt voor dezelfde functionaliteit, maar op het Azure-resource niveau.

## <a name="note-before-you-migrate"></a>Opmerking Voordat u migreert

* Migratie **is een eenrichtings proces.** U kunt niet teruggaan nadat u hebt gemigreerd.
* Toepassingen worden **automatisch met u gemigreerd** als u de **eigenaar** bent van de toepassing.
* De eigenaar kan geen subset van te migreren apps kiezen en het proces kan niet onomkeerbaar zijn.
* Toepassingen worden **verwijderd van de zijden** van de mede werker nadat de **eigenaar is gemigreerd**.
* Eigen aren worden gevraagd e-mail berichten te verzenden naar mede werkers om hen te informeren over de migratie.
* Toepassingen worden **niet met u gemigreerd** als u een **samen werker** van de toepassing bent.
* Het is niet mogelijk dat een eigenaar weet dat zijn deel nemers zijn gemigreerd.
* Bij de **migratie worden niet** automatisch samen werkers verzameld en verplaatst of toegevoegd aan de Azure-ontwerp bron. De eigenaar van de app is degene die deze stap na de migratie moet uitvoeren. Voor deze stap zijn [machtigingen vereist voor de Azure-ontwerp bron](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate).
* Nadat deel nemers zijn toegewezen aan de Azure-resource, **moeten ze migreren naar Access-toepassingen**. Als dat niet het geval is, hebben ze geen toegang tot de toepassingen.
* Een gemigreerde gebruiker kan niet worden toegevoegd als een samen werker van de toepassing.
* Als **u de Voorspellings sleutels hebt die zijn toegewezen aan toepassingen die eigendom zijn van een andere gebruiker**, wordt de migratie voor zowel de eigenaar als de mede werkers **geblokkeerd** . Zie hieronder voor aanbevelingen.

> [!Note]
> Als u een Voorspellings runtime-resource moet maken, is er [een afzonderlijk proces](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) om het te maken.

## <a name="migration-prerequisites"></a>Migratie vereisten

* U moet zijn gekoppeld aan een geldig Azure-abonnement. Vraag uw Tenant beheerder om u toe te voegen aan het abonnement, of u kunt zich [hier](https://azure.microsoft.com/free/)aanmelden voor een gratis account.
* U moet een LUIS Azure-ontwerp bron maken via de LUIS-portal of van de Azure Portal. Het maken van een ontwerp bron vanuit de LUIS-Portal maakt deel uit van de migratie stroom die in de volgende sectie wordt besproken.
* Als u een **samen werker van toepassingen**bent, worden de toepassingen niet automatisch gemigreerd. Het is **raadzaam om een back-up te maken van deze toepassingen** door ze te exporteren of de [export-API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40)te gebruiken. U kunt de app na de migratie weer in LUIS importeren. Het import proces maakt een nieuwe app met een nieuwe app-ID waarvoor u de eigenaar bent.
* Als u de **eigenaar van de toepassing**bent, hoeft u uw apps niet te exporteren omdat deze automatisch worden gemigreerd. Het is **raadzaam om de collaborator's-lijst van elke app op te slaan.** Een e-mail sjabloon met deze lijst wordt optioneel als onderdeel van het migratie proces aangeboden.


|Portal|Functie|
|--|--|
|[Azure](https://azure.microsoft.com/free/)|* Voor spelling-en ontwerp resources maken.<br>* Mede werkers aan resources toewijzen.|
|[LUIS](https://www.luis.ai)|* Migreren naar nieuwe ontwerp resources.<br>* Maak een nieuwe ontwerp bron in de migratie stroom.<br>* Het toewijzen of opheffen van voor spelling-en ontwerp resources voor apps op de pagina **beheer van > Azure-resources** . <br>* Verplaats toepassingen van de ene ontwerp bron naar een andere.  |

> [!Note]
> Het **ontwerpen van uw Luis-app is gratis**, wat wordt aangegeven door de `F0` laag. Meer informatie [over prijs categorieën](luis-limits.md#key-limits).


## <a name="migration-steps"></a>Migratiestappen

1. In de LUIS-Portal waaraan u werkt, kunt u het migratie proces starten vanuit het pictogram van **Azure** op de bovenste werk balk.

   > [!div class="mx-imgBorder"]
   > ![Pictogram migratie](./media/migrate-authoring-key/migration-button.png)

2. In het pop-upvenster migratie kunt u de migratie voortzetten of later migreren. Selecteer **nu migreren**.

   > [!div class="mx-imgBorder"]
   > ![Eerste pop-upvenster in migratie proces selecteert u nu migreren.](./media/migrate-authoring-key/prompt-when-migrating-2.png)

3. Als een van uw apps deel nemers heeft, wordt u gevraagd **hen een e-mail te sturen** om hen te laten weten over de migratie. Dit is een optionele stap.

   Voor elke samen werker en app wordt de standaard e-mail toepassing geopend met een e-mail met een lichte opmaak. U kunt de e-mail bewerken voordat u deze verzendt. De e-mail sjabloon bevat de exacte App-ID en app-naam.
   
   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources
      
   Thank you
   ```
   
   > [!Note]
   > Nadat u uw account naar Azure hebt gemigreerd, zijn uw apps niet langer beschikbaar voor deel nemers.

4. Als u voor elke toepassing een samen werker bent, wordt u gevraagd **een kopie van de apps te exporteren** door deze optie te selecteren tijdens de migratie stroom. Zodra u de optie hebt geselecteerd, ziet u de pagina hieronder waar u klikt op de knop downloaden aan de linkerkant om de gewenste apps te exporteren. U kunt deze apps na de migratie weer importeren omdat ze niet automatisch met u worden gemigreerd. Dit is een optionele stap.

   > [!div class="mx-imgBorder"]
   > ![Vragen om de toepassing te exporteren.](./media/migrate-authoring-key/export-app-for-collabs-2.png)

5. U kunt ervoor kiezen om een nieuwe LUIS te maken of te migreren naar een bestaande ontwerp bron als u er al een hebt gemaakt in Azure. Kies de gewenste optie door de juiste knop hieronder te selecteren.

   > [!div class="mx-imgBorder"]
   > ![Ontwerp bron maken](./media/migrate-authoring-key/choose-existing-authoring-resource.png)

### <a name="create-new-authoring-resource-from-luis-to-migrate"></a>Nieuwe ontwerp bron maken van LUIS om te migreren

Als u een nieuwe ontwerp bron wilt maken, selecteert u **een nieuwe ontwerp bron maken** en geeft u de volgende informatie op in het volgende venster.

> [!div class="mx-imgBorder"]
> ![Ontwerp bron maken](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

* **Resource naam** : een door u gekozen aangepaste naam, die wordt gebruikt als onderdeel van de URL voor uw ontwerp-en Voorspellings eindpunt query's.
* **Abonnements naam** : het abonnement dat wordt gekoppeld aan de resource. Als u meer dan één abonnement bij uw Tenant hebt, selecteert u de gewenste versie in de vervolg keuzelijst.
* **Resource groep** : een aangepaste naam voor de resource groep die u in de vervolg keuzelijst hebt gekozen. Met resource groepen kunt u Azure-resources groeperen voor toegang en beheer.
* **Tenant** : de Tenant waaraan uw Azure-abonnement is gekoppeld. Dit wordt standaard ingesteld voor de Tenant die u momenteel kiest. U kunt tenants overschakelen door de meest rechtse avatar te selecteren die uw initialen bevat.

Nadat u de bovenstaande gegevens hebt ingevoerd, selecteert u **gereed**.

Houd er rekening mee dat u per abonnement 10 gratis ontwerp resources per regio kunt maken. Als uw abonnement meer dan 10 ontwerp bronnen heeft in dezelfde regio, kunt u geen nieuwe maken.

* Wanneer de ontwerp bron is gemaakt, wordt het bericht weer gegeven. Selecteer **sluiten** om het pop-upvenster te sluiten.

  > [!div class="mx-imgBorder"]
  > ![De ontwerp resource is gemaakt.](./media/migrate-authoring-key/migration-success-2.png)

### <a name="use-existing-authoring-resource-to-migrate"></a>Bestaande ontwerp Resource gebruiken om te migreren

Als uw abonnement al is gekoppeld aan een Azure-resource met LUIS-ontwerp of als u hebt gemaakt op basis van de Azure Portal en u wilt migreren in plaats van een nieuwe resource te maken, selecteert u **bestaande ontwerp Resource gebruiken** en geeft u de volgende informatie op in het volgende venster.

> [!div class="mx-imgBorder"]
>![Ontwerp bron maken](./media/migrate-authoring-key/choose-existing-authoring-resource-2.png)

* **Tenant** : de Tenant waaraan uw Azure-abonnement is gekoppeld. Dit wordt standaard ingesteld voor de Tenant die u momenteel kiest.
* **Abonnements naam** : het abonnement dat wordt gekoppeld aan de resource. Als u meer dan één abonnement bij uw Tenant hebt, selecteert u de gewenste versie in de vervolg keuzelijst.
* **Resource naam** : Selecteer de bron van waaruit u wilt migreren.

> [!Note]
> Als u uw bewerkings resource niet in de vervolg keuzelijst kunt zien, moet u ervoor zorgen dat u deze hebt gemaakt op de **juiste locatie** volgens de Luis-portal waarin u bent aangemeld. Zorg er ook voor dat de inhoud die u hebt gemaakt inderdaad een bewerkings **resource** is en geen **Voorspellings resource**is.


* Valideer de naam van uw ontwerp bron en klik op de knop **nu migreren** .

 > [!div class="mx-imgBorder"]
 > ![Ontwerp bron maken](./media/migrate-authoring-key/choose-authoring-resource-and-migrate-2.png)

* Het bericht geslaagd wordt weer gegeven. Selecteer **sluiten** om het pop-upvenster te sluiten.

 > [!div class="mx-imgBorder"]
 > ![De ontwerp resource is gemaakt.](./media/migrate-authoring-key/migration-success-2.png)

## <a name="using-apps-after-migration"></a>Apps na migratie gebruiken

* Nadat het migratie proces is uitgevoerd, worden alle LUIS-apps waarvan u de eigenaar bent, nu toegewezen aan één LUIS-ontwerp bron.
* In de lijst **mijn apps** worden de apps weer gegeven die zijn gemigreerd naar de nieuwe ontwerp bron.
* Voordat u toegang krijgt tot uw apps, selecteert u het abonnement en de LUIS-ontwerp bron om de apps te zien die u kunt ontwerpen.

 > [!div class="mx-imgBorder"]
 > ![Selecteer abonnement en LUIS authoring resource om de apps te zien die u kunt ontwerpen.](./media/create-app-in-portal-select-subscription-luis-resource.png)

* U hoeft niet de sleutel van de ontwerp bron te weten om uw apps in de LUIS-portal te kunnen bewerken.
* Als u van plan bent om uw apps programmatisch te bewerken, hebt u de sleutel waarden van de ontwerp functie nodig. Deze waarden worden weer gegeven op de pagina **Manage-> Azure-resources** in de Luis-Portal en zijn ook beschikbaar in de Azure Portal op de pagina **sleutels** van de resource. U kunt ook meer ontwerp resources maken en deze toewijzen vanaf dezelfde pagina.

 > [!div class="mx-imgBorder"]
 > ![De ontwerp bron beheren.](./media/migrate-authoring-key/manage-authoring-resource-2.png)

## <a name="add-contributors-to-authoring-resources"></a>Mede werkers toevoegen aan het ontwerpen van resources

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Meer informatie [over het toevoegen van inzenders](luis-how-to-collaborate.md) aan uw ontwerp bron. Inzenders hebben toegang tot alle toepassingen onder die resource.

U kunt mede werkers toevoegen aan de ontwerp bron vanuit het _Azure Portal_op de pagina **Access Control (IAM)** voor die resource. Zie [toegang tot Inzender toevoegen](luis-migration-authoring-steps.md#after-the-migration-process-add-contributors-to-your-authoring-resource)voor meer informatie.

> [!Note]
> Als de eigenaar van de LUIS-app is gemigreerd en de samen werker als bijdrager is toegevoegd aan de Azure-resource, heeft de mede werker nog steeds geen toegang tot de app, tenzij deze ook worden gemigreerd.

## <a name="luis-portal-migration-reminders"></a>Migratie herinneringen voor LUIS Portal

De [Luis-Portal](https://www.luis.ai) biedt het migratie proces.

U wordt gevraagd om te migreren als:
* U hebt apps in het e-mail verificatie systeem voor het ontwerpen.
* En u bent de eigenaar van de app.

U wordt wekelijks gevraagd uw apps te migreren. U kunt dit venster annuleren zonder te migreren. Als u vóór de volgende geplande periode wilt migreren, kunt u beginnen met het migratie proces vanuit het **Azure** -pictogram op de bovenste werk balk van de Luis-Portal.

U kunt het migratie proces vertragen door het venster af te sluiten. U wordt regel matig gemigreerd voordat u migreert of de deadline voor migratie wordt door gegeven. U kunt het migratie proces starten vanuit het vergrendelings pictogram van de bovenste navigatie balk.

## <a name="prediction-resources-blocking-migration"></a>Migratie van voor Spellings bronnen blok keren
Uw migratie heeft een negatieve invloed op elke runtime van de toepassing. Bij de migratie worden alle deel nemers uit uw apps verwijderd en wordt u verwijderd als samen werker van andere apps. Dit proces houdt in dat de sleutels die een mede werker toewijst ook worden verwijderd, waardoor uw toepassing kan worden verbroken als deze in productie is. Dit is de reden dat we de migratie blok keren totdat u samen werkers of sleutels die eraan zijn toegewezen, hand matig verwijdert.

### <a name="when-does-prediction-resources-block-migration"></a>Wanneer worden de migratie voor Voorspellings bronnen geblokkeerd?
* De migratie wordt geblokkeerd als u voor spelling/runtime-resources hebt toegewezen in apps waarvan u geen eigenaar bent.
* De migratie wordt geblokkeerd als u andere gebruikers voorspellings-en runtime-resources toewijst aan apps waarvan u eigenaar bent.

### <a name="recommended-steps-to-do-if-you-are-the-owner-of-the-app"></a>Aanbevolen stappen om uit te voeren als u de eigenaar van de app bent
Als u eigenaar bent van sommige toepassingen en deel nemers een voor spelling/runtime-sleutel aan deze toepassing hebt toegewezen, wordt er een fout bericht weer gegeven wanneer u een lijst migreert met de toepassings-Id's die de Voorspellings sleutels hebben toegewezen aan hun eigendom van andere gebruikers.

Het is raadzaam om het volgende te doen:
* Deel mede werkers over de migratie.
* Verwijder alle deel nemers uit de toepassingen die in de fout worden weer gegeven.
* Het migratie proces moet slagen als u samen werkers hand matig verwijdert.
* Deel nemers als mede werkers aan uw nieuwe ontwerp bron toewijzen.
* Samen werkers zijn de Voorspellings bronnen te migreren en opnieuw toe te wijzen aan de toepassingen.
Houd er rekening mee dat dit tot gevolg heeft dat de toepassing tijdelijk wordt onderbroken totdat de Voorspellings bronnen opnieuw zijn toegewezen.

Een andere oplossing is hier, vóór de migratie van de eigenaar, kunnen mede werkers app-eigen aren toevoegen als bijdrager aan hun Azure-abonnementen vanuit het Azure Portal. Hiermee wordt de eigenaar toegang tot de runtime-Voorspellings bron verleend. Als de eigenaar wordt gemigreerd met behulp van het nieuwe abonnement waaraan ze zijn toegevoegd (wat wordt gevonden onder een nieuwe Tenant), wordt het migratie proces niet alleen gedeblokkeerd voor zowel de samen werker als de eigenaar van de app, maar is een soepele migratie van apps met de Voorspellings sleutel nog steeds toegewezen, waardoor de apps niet worden verbroken.


### <a name="recommended-steps-to-do-if-you-are-a-collaborator-on-an-app"></a>Aanbevolen stappen voor het uitvoeren van een samen werker in een app
Als u samenwerkt met toepassingen en de voor spelling/runtime-sleutel aan deze toepassing hebt toegewezen, wordt een fout weer gegeven wanneer u een lijst migreert met de toepassings-Id's en sleutel paden die de migratie blok keren.

Het is raadzaam om het volgende te doen:
* Exporteer toepassingen als back-up. Dit wordt als een optionele stap in het migratie proces gegeven.
* De toewijzing van de Voorspellings resources op de pagina **beheer van > Azure-resources** ongedaan maken.
* Het migratie proces wordt uitgevoerd.
* Importeer toepassingen na de migratie.
* Wijs de Voorspellings sleutels opnieuw toe aan de pagina met **> Azure-resources** voor het beheren van toepassingen.

> [!Note]
> Wanneer u uw toepassingen importeert nadat u bent gemigreerd, hebben ze verschillende app-Id's en ze zijn anders dan de apps die in de productie omgeving worden weer gegeven. U bent nu eigenaar van deze toepassingen.

## <a name="troubleshooting-migration-process"></a>Problemen met het migratie proces oplossen

Wanneer u probeert te migreren, maar u kunt uw Azure-abonnement niet vinden in de vervolg keuzelijst:
* Zorg ervoor dat u beschikt over een geldig Azure-abonnement dat is gemachtigd om Cognitive Services-resources te maken. Ga naar [Azure Portal](https://ms.portal.azure.com) en controleer de status van het abonnement. Als u er nog geen hebt, [maakt u een gratis proef versie](https://azure.microsoft.com/free/).
* Zorg ervoor dat u zich in de juiste Tenant bevindt die is gekoppeld aan uw geldige abonnement. U kunt de tenants van de andere avatar op de volgende werk balk verplaatsen: ![ scha kelen tussen tenants.](./media/migrate-authoring-key/switch-user-tenant-2.png)

 Als u al een bestaande ontwerp resource hebt, maar deze niet kunt vinden wanneer u de optie bestaande authoring Resource gebruiken selecteert:
* De resource is waarschijnlijk gemaakt op een andere locatie dan de portal waarvoor u zich hebt aangemeld. Raadpleeg [de Luis-ontwerp regio's en de portals](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-authoring-regions)
* In plaats daarvan een nieuwe resource maken vanuit de LUIS Portal

Als u de optie nieuwe ontwerp bron maken selecteert en de migratie is mislukt met het fout bericht ' kan de Azure-gegevens van de gebruiker niet ophalen, opnieuw proberen
* Uw abonnement heeft mogelijk tien of meer ontwerp resources per regio per abonnement. Als dat het geval is, kunt u geen nieuwe ontwerp bron maken.
* Migreer door de optie bestaande ontwerp bron gebruiken te selecteren en selecteer een van de bestaande resources die u onder uw abonnement hebt.

Als de volgende fout wordt weer gegeven, schakelt u de [aanbevolen stappen uit als u de eigenaar van de app-sectie bent] de ![ migratie mislukt voor eigen aren](./media/migrate-authoring-key/migration-failed-for-owner-2.png)

Als de volgende fout wordt weer gegeven, controleert u de [aanbevolen stappen die u moet doen als u een samen werker op een app-gedeelte bent] de ![ migratie mislukt voor samen werkers](./media/migrate-authoring-key/migration-failed-for-collab-2.png)


## <a name="next-steps"></a>Volgende stappen

* [Begrippen](luis-concept-keys.md) over het ontwerpen en runtime-sleutels bekijken
* Controleren [hoe sleutels worden toegewezen](luis-how-to-azure-subscription.md) en [mede](luis-how-to-collaborate.md) werkers toevoegen

---
title: Aangepaste spraakvoorbeelden opnemen - Spraakservice
titleSuffix: Azure Cognitive Services
description: Maak een aangepaste stem van productiekwaliteit door een robuust script voor te bereiden, goed stemtalent aan te nemen en professioneel op te nemen.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 2897fe2e0cc8aeb929b0a33f5cdaba9c60f2a244
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261581"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Spraakvoorbeelden opnemen om een aangepaste stem te maken

Het creëren van een hoge kwaliteit productie aangepaste stem vanaf nul is niet een casual onderneming. De centrale component van een aangepaste stem is een grote verzameling van audio samples van menselijke spraak. Het is van vitaal belang dat deze audio-opnames van hoge kwaliteit zijn. Kies een stem talent die ervaring heeft met het maken van dit soort opnames, en hebben ze opgenomen door een bevoegde opname-ingenieur met behulp van professionele apparatuur.

Voordat u deze opnames maken, hebt u echter een script nodig: de woorden die door uw stemtalent worden gesproken om de audiosamples te maken. Voor de beste resultaten moet uw script een goede fonetische dekking en voldoende variatie hebben om het aangepaste spraakmodel te trainen.

Veel kleine maar belangrijke details gaan in het creëren van een professionele spraakopname. Deze gids is een roadmap voor een proces dat u zal helpen goede, consistente resultaten te behalen.

> [!TIP]
> Voor de hoogste kwaliteit resultaten, overweeg dan microsoft in te roepen om te helpen bij het ontwikkelen van uw aangepaste stem. Microsoft heeft uitgebreide ervaring met het produceren van stemmen van hoge kwaliteit voor zijn eigen producten, waaronder Cortana en Office.

## <a name="voice-recording-roles"></a>Spraakopnamerollen

Er zijn vier basisrollen in een aangepast spraakopnameproject:

Rol|Doel
-|-
Stemtalent        |De stem van deze persoon zal de basis vormen van de aangepaste stem.
Opnametechnicus  |Houdt toezicht op de technische aspecten van de opname en bedient de opnameapparatuur.
Directeur            |Bereidt het script voor en coacht de prestaties van het stemtalent.
Editor              |Rondt de audiobestanden af en bereidt ze voor op uploaden naar de Aangepaste Voice-portal.

Een individu kan meer dan één rol vervullen. Deze gids gaat ervan uit dat je in de eerste plaats de regierol invult en zowel een stemtalent als een opnametechnicus inhuurt. Als u de opnames zelf wilt maken, bevat dit artikel informatie over de rol van opnametechnicus. De editorrol is pas na de sessie nodig, dus kan worden uitgevoerd door de regisseur of de opnametechnicus.

## <a name="choose-your-voice-talent"></a>Kies je stemtalent

Acteurs met ervaring in voice-over of voice character werk maken goed op maat stemtalent. U ook vaak geschikt talent vinden onder omroepers en nieuwslezers.

Kies stemtalent waarvan je de natuurlijke stem leuk vindt. Het is mogelijk om unieke "karakter" stemmen te creëren, maar het is veel moeilijker voor de meeste talent om ze consequent uit te voeren, en de inspanning kan leiden tot stem spanning.

> [!TIP]
> Over het algemeen, vermijd het gebruik van herkenbare stemmen om een aangepaste stem te maken, tenzij, natuurlijk, je doel is om een stem van beroemdheden te produceren. Minder bekende stemmen zijn meestal minder storend voor gebruikers.

De belangrijkste factor voor het kiezen van stemtalent is consistentie. Uw opnames moeten allemaal klinken alsof ze op dezelfde dag in dezelfde kamer zijn gemaakt. U dit ideaal benaderen door middel van goede opnamepraktijken en engineering.

Je stemtalent is de andere helft van de vergelijking. Ze moeten kunnen spreken met consistente snelheid, volumeniveau, toonhoogte en toon. Duidelijke dictie is een must. Het talent moet ook in staat zijn om strikt controle over hun toonhoogte variatie, emotionele invloed, en spraak maniertjes.

Het opnemen van aangepaste stemsamples kan meer vermoeiend zijn dan andere soorten stemwerk. Het meeste stemtalent kan twee of drie uur per dag opnemen. Beperk sessies tot drie of vier per week, met een vrije dag ertussen indien mogelijk.

Opnames gemaakt voor een stemmodel moeten emotioneel neutraal zijn. Dat wil zeggen, een trieste uitspraak mag niet worden gelezen op een trieste manier. Stemming kan later worden toegevoegd aan de gesynthetiseerde spraak door middel van prosodie controles. Werk samen met je stemtalent om een "persona" te ontwikkelen die het algehele geluid en de emotionele toon van de aangepaste stem definieert. In het proces, zult u lokaliseren wat "neutraal" klinkt als voor die persona.

Een persona kan bijvoorbeeld een van nature vrolijke persoonlijkheid hebben. Dus "hun" stem zou kunnen dragen een nota van optimisme, zelfs wanneer ze spreken neutraal. Echter, een dergelijke persoonlijkheidseigenschap moet subtiel en consistent zijn. Luister naar lezingen van bestaande stemmen om een idee te krijgen van wat je nastreeft.

> [!TIP]
> Meestal wil je de spraakopnames die je maakt bezitten. Uw stemtalent moet vatbaar zijn voor een werk-voor-huurcontract voor het project.

## <a name="create-a-script"></a>Een script maken

Het uitgangspunt van een aangepaste spraakopnamesessie is het script, dat de uitingen bevat die door uw stemtalent moeten worden gesproken. (De term "uitingen" omvat zowel volledige zinnen als kortere zinnen.)

De uitingen in uw script kunnen overal vandaan komen: fictie, non-fictie, transcripties van toespraken, nieuwsberichten en alles wat beschikbaar is in gedrukte vorm. Als u ervoor wilt zorgen dat uw stem het goed doet op specifieke soorten woorden (zoals medische terminologie of programmeerjargon), wilt u misschien zinnen uit wetenschappelijke documenten of technische documenten opnemen. Voor een korte bespreking van mogelijke juridische kwesties, zie de ["Legaliteiten"](#legalities) sectie. U ook uw eigen tekst schrijven.

Uw uitingen hoeven niet uit dezelfde bron of dezelfde bron te komen. Ze hoeven niet eens iets met elkaar te maken te hebben. Als u echter bepaalde zinnen (bijvoorbeeld 'U bent ingelogd' in uw spraaktoepassing gebruikt, moet u deze in uw script opnemen. Dit geeft uw aangepaste stem een betere kans om deze zinnen goed uit te spreken. En als u besluit om een opname te gebruiken in plaats van gesynthetiseerde spraak, hebt u deze al in dezelfde stem.

Hoewel consistentie de sleutel is bij het kiezen van stemtalent, is variatie het kenmerk van een goed script. Uw script moet veel verschillende woorden en zinnen bevatten met een verscheidenheid aan zinslengtes, structuren en stemmingen. Elk geluid in de taal moet meerdere keren en in tal van contexten worden vertegenwoordigd *(fonetische dekking*genoemd).

Bovendien moet de tekst alle manieren bevatten waarop een bepaald geluid schriftelijk kan worden weergegeven en elk geluid op verschillende plaatsen in de zinnen plaatsen. Zowel declaratieve zinnen en vragen moeten worden opgenomen en gelezen met de juiste intonatie.

Het is moeilijk om een script te schrijven dat *net genoeg* gegevens biedt om de Custom Speech-portal in staat te stellen een goede stem te bouwen. In de praktijk, de eenvoudigste manier om een script dat robuuste fonetische dekking bereikt te maken is om een groot aantal monsters op te nemen. De standaardstemmen die Microsoft levert, zijn opgebouwd uit tienduizenden uitingen. U moet bereid zijn om een paar tot enkele duizenden uitingen op te nemen op zijn minst om een productie-kwaliteit aangepaste stem te bouwen.

Controleer het script zorgvuldig op fouten. Laat indien mogelijk iemand anders het ook controleren. Wanneer u door het script met je talent, zult u waarschijnlijk vangen een paar fouten.

### <a name="script-format"></a>Script-indeling

U uw script schrijven in Microsoft Word. Het script is voor gebruik tijdens de opnamesessie, zodat u het instellen op elke manier waarop u gemakkelijk werken. Maak het tekstbestand dat vereist is door de aangepaste spraakportal afzonderlijk.

Een basisscriptindeling bevat drie kolommen:

* Het nummer van de uiting, te beginnen bij 1. Nummering maakt het gemakkelijk voor iedereen in de studio om te verwijzen naar een bepaalde uiting ("laten we proberen nummer 356 opnieuw"). U de functie Word-alineanummering gebruiken om de rijen van de tabel automatisch te nummeren.
* Een lege kolom waarin u het take-nummer of de tijdcode van elke utterance schrijft om u te helpen deze in de voltooide opname te vinden.
* De tekst van de uiting zelf.

![Voorbeeldscript](media/custom-voice/script.png)

> [!NOTE]
> De meeste studio's opnemen in korte segmenten bekend als *takes*. Elke take bevat meestal 10 tot 24 uitingen. Alleen het noteren van het take-nummer is voldoende om later een utterance te vinden. Als u opnamen maakt in een studio die liever langere opnamen maakt, wilt u in plaats daarvan de tijdcode noteren. De studio krijgt een prominente tijdsaanduiding.

Laat na elke rij voldoende ruimte om notities te schrijven. Zorg ervoor dat er geen uiting wordt verdeeld over pagina's. Nummer de pagina's en druk uw script af aan één kant van het papier.

Print drie exemplaren van het script: een voor het talent, een voor de ingenieur, en een voor de directeur (u). Gebruik een paperclip in plaats van nietjes: een ervaren stemkunstenaar scheidt de pagina's om te voorkomen dat er ruis wordt gemaakt terwijl de pagina's worden gedraaid.

### <a name="legalities"></a>Wettigheid

Volgens het auteursrecht kan het lezen van auteursrechtelijk beschermde tekst door een acteur een voorstelling zijn waarvoor de auteur van het werk moet worden gecompenseerd. Deze voorstelling zal niet herkenbaar zijn in het eindproduct, de aangepaste stem. Toch is de wettigheid van het gebruik van een auteursrechtelijk beschermd werk voor dit doel niet goed ingeburgerd. Microsoft kan hierover geen juridisch advies geven; raadpleeg uw eigen raadsman.

Gelukkig is het mogelijk om deze problemen volledig te vermijden. Er zijn veel bronnen van tekst die u gebruiken zonder toestemming of licentie.

|Tekstbron|Beschrijving|
|-|-|
|[CMU Arctisch corpus](http://festvox.org/cmu_arctic/)|Ongeveer 1100 zinnen geselecteerd uit out-of-copyright werken specifiek voor gebruik in spraaksynthese projecten. Een uitstekend uitgangspunt.|
|Werkt niet meer<br>onder het auteursrecht|Typisch werken gepubliceerd vóór 1923. Voor Het Engels biedt [Project Gutenberg](https://www.gutenberg.org/) tienduizenden van dergelijke werken aan. Misschien wilt u zich richten op nieuwere werken, omdat de taal dichter bij het moderne Engels zal zijn.|
|Overheidswerkzaamheden&nbsp;|Werken gemaakt door de Amerikaanse overheid zijn niet auteursrechtelijk beschermd in de Verenigde Staten, hoewel de overheid kan aanspraak maken op het auteursrecht in andere landen / regio's.|
|Publiek domein|Werken waarvoor het auteursrecht expliciet is afgewezen of die zijn gewijd aan het publieke domein. Het is mogelijk dat het auteursrecht in sommige rechtsgebieden niet volledig kan worden opgeheven.|
|Permissively-licentie werken|Werken verdeeld onder een licentie zoals Creative Commons of de GNU Free Documentation License (GFDL). Wikipedia maakt gebruik van de GFDL. Sommige licenties kunnen echter beperkingen opleggen aan de prestaties van de gelicentieerde inhoud die van invloed kunnen zijn op het maken van een aangepast spraakmodel, dus lees de licentie zorgvuldig.|

## <a name="recording-your-script"></a>Uw script opnemen

Neem uw script op in een professionele opnamestudio die gespecialiseerd is in spraakwerk. Ze hebben een opnamecabine, de juiste apparatuur en de juiste mensen om het te bedienen. Het loont om niet te beknibbelen op de opname.

Bespreek uw project met de opnametechnicus van de studio en luister naar hun advies. De opname moet weinig of geen dynamisch bereik compressie (maximum van 4:1). Het is van cruciaal belang dat de audio een consistent volume en een hoge signaal-ruisverhouding heeft, terwijl het vrij is van ongewenste geluiden.

### <a name="do-it-yourself"></a>Doe het zelf

Als u wilt maken van de opname zelf, in plaats van te gaan in een opnamestudio, hier is een korte inleiding. Dankzij de opkomst van thuis opname en podcasting, is het makkelijker dan ooit om goede opname advies en middelen online te vinden.

Uw "opnamecabine" moet een kleine kamer zijn zonder merkbare echo of "kamertoon.". Het moet zo stil en geluidsdicht mogelijk zijn. Gordijnen op de muren kunnen worden gebruikt om echo te verminderen en te neutraliseren of "deaden" het geluid van de kamer.

Gebruik een hoogwaardige studiocondensatormicrofoon (kortweg "microfoon" die is bedoeld voor het opnemen van spraak. Sennheiser, AKG en nog nieuwere Zoom microfoons kunnen goede resultaten opleveren. U een microfoon kopen, of huur een van een lokale audio-visuele verhuur bedrijf. Zoek er een met een USB-interface. Dit type microfoon combineert gemakkelijk het microfoonelement, de voorversterker en de analoog-naar-digitale converter in één pakket, waardoor de aansluiting wordt vereenvoudigd.

U ook een analoge microfoon gebruiken. Veel huurhuizen bieden "vintage" microfoons bekend om hun stem karakter. Merk op dat professionele analoge versnelling maakt gebruik van evenwichtige XLR-connectoren, in plaats van de 1/4-inch stekker die wordt gebruikt in consumentenapparatuur. Als je analoog gaat, heb je ook een voorversterker en een computeraudio-interface met deze connectors nodig.

Installeer de microfoon op een standaard of giek, en installeer een pop filter voor de microfoon om ruis te elimineren van "plosive" medeklinkers zoals "p" en "b." Sommige microfoons worden geleverd met een hangvat die ze isoleert van trillingen in de stand, wat handig is.

Het stemtalent moet op een consistente afstand van de microfoon blijven. Gebruik tape op de vloer om te markeren waar ze moeten staan. Als het talent liever zit, let dan extra op om de microfoonafstand te bewaken en stoelgeluid te voorkomen.

Gebruik een standaard om het script vast te houden. Vermijd hengelen de stand, zodat het geluid kan reflecteren in de richting van de microfoon.

De persoon die de opnameapparatuur -de ingenieur- moet in een aparte kamer van het talent, met een manier om te praten met het talent in de opnamestand (een *talkback circuit).*

De opname moet zo min mogelijk ruis bevatten, met als doel een signaal-ruisverhouding van 80 db of beter.

Luister goed naar een opname van stilte in uw "stand", zoek uit waar enig geluid vandaan komt, en elimineer de oorzaak. Veel voorkomende bronnen van lawaai zijn ventilatieopeningen, tl-lichtballasten, verkeer op nabijgelegen wegen en ventilatoren voor apparatuur (zelfs notebook-pc's kunnen ventilatoren hebben). Microfoons en kabels kunnen elektrische ruis oppikken van nabijgelegen wisselstroombedrading, meestal een gezoem of gezoem. Een buzz kan ook worden veroorzaakt door een *grond lus*, die wordt veroorzaakt door het hebben van apparatuur aangesloten op meer dan een elektrisch circuit.

> [!TIP]
> In sommige gevallen u mogelijk een equalizer of een plug-in voor ruisonderdrukkingssoftware gebruiken om ruis uit uw opnamen te verwijderen, hoewel het altijd het beste is om deze bij de bron te stoppen.

Stel niveaus zo in dat het grootste deel van het beschikbare dynamische bereik van digitale opname wordt gebruikt zonder overdrijven. Dat betekent dat de audio luid, maar niet zo luid dat het wordt vervormd. Een voorbeeld van de golfvorm van een goede opname wordt weergegeven in de volgende afbeelding:

![Een goede opname golfvorm](media/custom-voice/good-recording.png)

Hier wordt het grootste deel van het bereik (hoogte) gebruikt, maar de hoogste toppen van het signaal bereiken de boven- of onderkant van het venster niet. U ook zien dat de stilte in de opname een dunne horizontale lijn benadert, wat wijst op een lage ruisvloer. Deze opname heeft een acceptabel dynamisch bereik en een signaal-ruisverhouding.

Neem rechtstreeks op in de computer via een hoogwaardige audio-interface of een USB-poort, afhankelijk van de microfoon die u gebruikt. Voor analoog, houd de audio-keten eenvoudig: microfoon, voorversterker, audio-interface, computer. Je zowel [Avid Pro Tools](https://www.avid.com/en/pro-tools) als Adobe [Audition](https://www.adobe.com/products/audition.html) maandelijks in licentie geven tegen een redelijke prijs. Als uw budget is zeer krap, probeer dan de gratis [Audacity](https://www.audacityteam.org/).

Opnemen op 44,1 kHz 16 bit monofone (cd-kwaliteit) of beter. De huidige state-of-the-art is 48 kHz 24-bits, als uw apparatuur ondersteunt. U zult uw audio down-samplen tot 16 kHz 16-bits voordat u deze indient bij de Custom Voice-portal. Toch loont het om een hoogwaardige originele opname in het geval bewerkingen nodig zijn.

Idealiter hebben verschillende mensen dienen in de rollen van directeur, ingenieur, en talent. Probeer het niet allemaal zelf te doen. In een snuifje, kan een persoon zowel de directeur en de ingenieur.

### <a name="before-the-session"></a>Voor de sessie

Om te voorkomen dat het verspillen van studio tijd, lopen door het script met je stem talent voor de opnamesessie. Terwijl het stemtalent vertrouwd raakt met de tekst, kunnen ze de uitspraak van onbekende woorden verduidelijken.

> [!NOTE]
> De meeste opnamestudio's bieden elektronische weergave van scripts in de opnamecabine. Typ in dit geval uw doorloopnotities rechtstreeks in het document van het script. U wilt nog steeds een papieren kopie om aantekeningen te maken op tijdens de sessie, dat wel. De meeste ingenieurs zullen willen een papieren exemplaar, ook. En je wilt nog steeds een derde gedrukte kopie als een back-up voor het talent in het geval de computer is uitgeschakeld.

Uw stemtalent kan zich afvragen welk woord u wilt benadrukken in een utterance (het 'operatieve woord'). Vertel hen dat u een natuurlijke lezing wilt zonder bijzondere nadruk. De nadruk kan worden toegevoegd wanneer de toespraak wordt gesynthetiseerd; het mag geen deel uitmaken van de oorspronkelijke opname.

Richt het talent om woorden duidelijk uit te spreken. Elk woord van het script moet worden uitgesproken als geschreven. Geluiden mogen niet worden weggelaten of onduidelijke samen, zoals gebruikelijk is in casual spraak, *tenzij ze zijn geschreven op die manier in het script*.

|Geschreven tekst|Ongewenste toevallige uitspraak|
|-|-|
|nooit zal geven u op|nooit zal geven u op|
|er zijn vier lichten|Er zijn vier lichten.|
|hoe is het weer vandaag|hoe is het weer vandaag|
|zeg hallo tegen mijn kleine vriend|zeg hallo tegen mijn lil' vriend|

Het talent mag *geen* duidelijke pauzes tussen woorden toevoegen. De zin moet nog steeds natuurlijk stromen, zelfs terwijl het klinkt een beetje formeel. Dit mooie onderscheid kan de praktijk te nemen om goed te krijgen.

### <a name="the-recording-session"></a>De opnamesessie

Maak een referentieopname of *overeenkomend bestand* van een typische utterance aan het begin van de sessie. Vraag het talent om deze regel te herhalen elke pagina of zo. Vergelijk de nieuwe opname telkens met de referentie. Deze praktijk helpt het talent consistent te blijven in volume, tempo, toonhoogte en intonatie. Ondertussen kan de technicus het matchbestand gebruiken als referentie voor niveaus en algehele consistentie van geluid.

Het wedstrijdbestand is vooral belangrijk wanneer u de opname hervat na een pauze of op een andere dag. Je wilt het een paar keer spelen voor het talent en ze het elke keer laten herhalen totdat ze goed bij elkaar passen.

Coach je talent om diep adem te halen en even te pauzeren voor elke uiting. Neem een paar seconden stilte op tussen uitingen. Woorden moeten worden uitgesproken op dezelfde manier elke keer dat ze verschijnen, rekening houdend met de context. Bijvoorbeeld, "record" als een werkwoord wordt anders uitgesproken dan "record" als een zelfstandig naamwoord.

Neem een goede vijf seconden stilte op voor de eerste opname om de 'kamertoon' vast te leggen. Deze praktijk helpt de Custom Voice portal te compenseren voor de resterende ruis in de opnames.

> [!TIP]
> Alles wat je echt nodig hebt om vast te leggen is de stem talent, zodat u een monofone (single-channel) opname van alleen hun lijnen te maken. Als u echter opneemt in stereo, u het tweede kanaal gebruiken om het gebabbel in de controlekamer op te nemen om de discussie over bepaalde regels of takes vast te leggen. Verwijder dit nummer uit de versie die is geüpload naar de aangepaste voice-portal.

Luister goed, met behulp van een koptelefoon, naar de prestaties van het stemtalent. U bent op zoek naar goede, maar natuurlijke dictie, juiste uitspraak, en een gebrek aan ongewenste geluiden. Aarzel niet om uw talent te vragen om opnieuw een uiting die niet voldoet aan deze normen op te nemen.

> [!TIP]
> Als u een groot aantal uitingen gebruikt, heeft één utterance mogelijk geen merkbaar effect op de resulterende aangepaste stem. Het is misschien beter om uitingen met problemen op te merken, ze uit te sluiten van uw gegevensset en te zien hoe uw aangepaste stem uitpakt. U altijd terug naar de studio en de gemiste monsters later opnemen.

Let op het take-nummer of de tijdcode op uw script voor elke utterance. Vraag de technicus om elke uiting ook in de metagegevens of het actieblad van de opname te markeren.

Neem regelmatig pauzes en bieden een drankje om uw stem talent te helpen houden hun stem in goede vorm.

### <a name="after-the-session"></a>Na de sessie

Moderne opnamestudio's draaien op computers. Aan het einde van de sessie ontvangt u een of meer audiobestanden, geen tape. Deze bestanden zullen waarschijnlijk WAV- of AIFF-formaat in cd-kwaliteit (44,1 kHz 16-bits) of beter zijn. 48 kHz 24-bits is gebruikelijk en wenselijk. Hogere samplingrates, zoals 96 kHz, zijn over het algemeen niet nodig.

De Aangepaste Voice-portal vereist dat elke verstrekte utterance in zijn eigen bestand staat. Elk audiobestand dat door de studio wordt geleverd, bevat meerdere uitingen. Dus de primaire post-productie taak is om de opnames op te splitsen en voor te bereiden op indiening. De opnametechnicus heeft mogelijk markeringen in het bestand geplaatst (of een apart actieblad opgegeven) om aan te geven waar elke utterance begint.

Gebruik je notities om de exacte takes te vinden die je wilt, en gebruik vervolgens een hulpprogramma voor geluidsbewerking, zoals [Avid Pro Tools,](https://www.avid.com/en/pro-tools) [Adobe Audition](https://www.adobe.com/products/audition.html)of de gratis [Audacity,](https://www.audacityteam.org/)om elke utterance naar een nieuw bestand te kopiëren.

Laat slechts ongeveer 0,2 seconden stilte achter aan het begin en einde van elke clip, behalve de eerste. Dat bestand moet beginnen met een volledige vijf seconden stilte. Gebruik geen audio-editor om stille delen van het bestand te "nul uit". Inclusief de "kamertoon" zal helpen de Custom Voice algoritmen compenseren voor eventuele resterende achtergrondgeluid.

Luister naar elk bestand zorgvuldig. In dit stadium u kleine ongewenste geluiden bewerken die u tijdens de opname hebt gemist, zoals een lichte lip voor een lijn, maar wees voorzichtig om geen werkelijke spraak te verwijderen. Als u een bestand niet herstellen, verwijdert u het uit uw gegevensset en houdt u er rekening mee dat u dit hebt gedaan.

Converteer elk bestand naar 16 bits en een samplerate van 16 kHz voordat je het studio-gebabbel opslaat en verwijder, als je het studiochatter hebt opgenomen, het tweede kanaal. Sla elk bestand op in WAV-indeling en noem de bestanden met het utterancenummer uit uw script.

Maak ten slotte het *transcript* dat elk WAV-bestand koppelt aan een tekstversie van de bijbehorende utterance. [Het maken van aangepaste spraaklettertypen](how-to-customize-voice-font.md) bevat details van de vereiste indeling. U de tekst rechtstreeks uit uw script kopiëren. Maak vervolgens een Zip-bestand van de WAV-bestanden en het teksttranscript.

Archiveer de originele opnames op een veilige plaats voor het geval u ze later nodig hebt. Bewaar ook je script en notities.

## <a name="next-steps"></a>Volgende stappen

Je bent klaar om je opnames te uploaden en je aangepaste stem te maken.

> [!div class="nextstepaction"]
> [Aangepaste spraaklettertypen maken](how-to-customize-voice-font.md)

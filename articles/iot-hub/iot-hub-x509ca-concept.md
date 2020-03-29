---
title: Concepten van Azure IoT Hub X.509-beveiliging | Microsoft Documenten
description: Concept - inzicht in de waarde X.509 certificaatautoriteitencertificaten in de productie en verificatie van IoT-apparaten .
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3c7e1167b3326620863d35cb2d4b07235cbd5517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61320239"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Conceptueel begrip van X.509 CA-certificaten in de IoT-industrie

In dit artikel wordt de waarde beschreven van het gebruik van Ca-certificaten (X.509-certificaatautoriteiten) in de productie en verificatie van IoT-apparaten naar IoT Hub. Het bevat informatie over supply chain setup en hoogtepunten voordelen.

In dit artikel wordt beschreven:

* Wat X.509 CA-certificaten zijn en hoe u ze krijgen

* Uw X.509 CA-certificaat registreren bij IoT Hub

* Een productieketen instellen voor X.509 CA-gebaseerde verificatie

* Hoe apparaten die zijn ondertekend met X.509 CA verbinding maken met IoT Hub

## <a name="overview"></a>Overzicht

X.509 Certificate Authority (CA) authenticatie is een benadering voor het authenticeren van apparaten naar IoT Hub met behulp van een methode die het creëren van apparaatidentiteiten en levenscyclusbeheer in de supply chain drastisch vereenvoudigt.

Een onderscheidend kenmerk van de X.509 CA-verificatie is een één-op-één relatie die een CA-certificaat heeft met zijn downstream-apparaten. Deze relatie maakt registratie van een willekeurig aantal apparaten in IoT Hub mogelijk door één keer een X.509 CA-certificaat te registreren, anders moeten unieke certificaten van het apparaat vooraf worden geregistreerd voor elk apparaat voordat een apparaat verbinding kan maken. Deze één-op-vele relatie vereenvoudigt ook het beheer van apparaatcertificaten tijdens het levenscyclusbeheer.

Een ander belangrijk kenmerk van de X.509 CA authenticatie is vereenvoudiging van supply chain logistiek. Veilige verificatie van apparaten vereist dat elk apparaat een uniek geheim heeft als een sleutel als basis voor vertrouwen. Bij verificatie op basis van certificaten is dit geheim een privésleutel. Een typische productiestroom van apparaten omvat meerdere stappen en bewaarders. Het veilig beheren van persoonlijke sleutels van apparaten tussen meerdere bewaarders en het onderhouden van vertrouwen is moeilijk en duur. Met behulp van certificaatautoriteiten lost dit probleem op door elke beheerder in een cryptografische vertrouwensketen te ondertekenen in plaats van ze te voorzien van persoonlijke sleutels van het apparaat. Elke bewaarder op zijn beurt tekent apparaten op hun respectieve processtap van de productiestroom. Het algemene resultaat is een optimale supply chain met ingebouwde verantwoording door gebruik te maken van de cryptografische vertrouwensketen. Het is vermeldenswaard dat dit proces de meeste veiligheid oplevert wanneer apparaten hun unieke privésleutels beschermen. Hiertoe dringen we aan op het gebruik van Hardware Secure Modules (HSM) die in staat zijn om intern privésleutels te genereren die nooit het daglicht zullen zien.

Dit artikel biedt een end-to-end weergave van het gebruik van de X.509 CA-authenticatie, van supply chain-setup tot apparaatverbinding, terwijl gebruik wordt gemaakt van een voorbeeld in de echte wereld om begrip te verharden.

## <a name="introduction"></a>Inleiding

Het X.509 CA-certificaat is een digitaal certificaat waarvan de houder andere certificaten kan ondertekenen. Dit digitale certificaat is X.509 omdat het voldoet aan een certificaatopmaakstandaard die is voorgeschreven door de RFC 5280-standaard van IETF en een certificaatautoriteit (CA) is omdat de houder andere certificaten kan ondertekenen.

Het gebruik van X.509 CA is het best te begrijpen in relatie tot een concreet voorbeeld. Denk aan Company-X, een maker van Smart-X-Widgets die zijn ontworpen voor professionele installatie. Company-X besteedt zowel de productie als de installatie uit. Het contracteert fabrikant Factory-Y om de Smart-X-Widgets te produceren, en dienstverlener Technician-Z te installeren. Company-X wil dat Smart-X-Widget rechtstreeks wordt verzonden van Factory-Y naar Technician-Z voor installatie en dat het direct verbinding maakt met Het geval van IoT Hub van Company-X na installatie zonder verdere tussenkomst van Company-X. Om dit mogelijk te maken, moet Company-X een paar eenmalige installatiebewerkingen voltooien om Smart-X-Widget te primeeren voor automatische verbinding. Met het end-to-end scenario in het achterhoofd, is de rest van dit artikel als volgt gestructureerd:

* Het X.509 CA-certificaat verkrijgen

* X.509 CA-certificaat registreren bij IoT Hub

* Apparaten aanmelden bij een vertrouwensketen

* Apparaatverbinding

## <a name="acquire-the-x509-ca-certificate"></a>Het X.509 CA-certificaat verkrijgen

Company-X heeft de mogelijkheid om een X.509 CA-certificaat aan te schaffen bij een openbare basiscertificaatautoriteit of er een te maken via een zelfondertekend proces. De ene optie zou optimaal zijn ten opzichte van de andere, afhankelijk van het toepassingsscenario. Ongeacht de optie, het proces omvat twee fundamentele stappen, het genereren van een publiek / private sleutelpaar en het ondertekenen van de openbare sleutel in een certificaat.

![Stroom voor het genereren van een X509CA-certificaten](./media/iot-hub-x509ca-concept/csr-flow.png)

Details over het uitvoeren van deze stappen verschillen met verschillende dienstverleners.

### <a name="purchasing-an-x509-ca-certificate"></a>Een X.509 CA-certificaat kopen

De aankoop van een CA-certificaat heeft het voordeel dat een bekende root CA optreedt als een vertrouwde derde partij om in te staan voor de legitimiteit van IoT-apparaten wanneer de apparaten verbinding maken. Bedrijf-X zou deze optie kiezen als ze van plan smart-x-widget om te communiceren met producten of diensten van derden na de eerste verbinding met IoT Hub.

Als u een X.509 CA-certificaat wilt kopen, kiest Company-X een leverancier van rootcertificatenservices. Een internet zoektocht naar de zinsnede 'Root CA' zal opleveren goede leads. De hoofd-CA zal Company-X begeleiden over het maken van het publiek/private sleutelpaar en hoe u een Certificaatondertekeningsaanvraag (CSR) voor hun services genereren. Een MVO is het formele proces van het aanvragen van een certificaat bij een certificeringsinstantie. Het resultaat van deze aankoop is een certificaat voor gebruik als een autoriteit certificaat. Gezien de alomtegenwoordigheid van X.509-certificaten, is het certificaat waarschijnlijk goed geformatteerd volgens de RFC 5280-standaard van IETF.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Een zelfondertekend X.509 CA-certificaat maken

Het proces voor het maken van een zelfondertekend X.509 CA-certificaat is vergelijkbaar met de aankoop, met uitzondering van het betrekken van een derde ondertekenaar zoals de rootcertificaatautoriteit. In ons voorbeeld zal Company-X het autoriteitscertificaat ondertekenen in plaats van een basiscertificaatautoriteit. Bedrijf-X kan deze optie kiezen voor het testen totdat ze klaar zijn om een certificaat van de autoriteit te kopen. Company-X kan ook een zelfondertekend X.509 CA-certificaat in productie gebruiken, als Smart-X-Widget niet bedoeld is om verbinding te maken met services van derden buiten de IoT Hub.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Het X.509-certificaat registreren op IoT Hub

Company-X moet de X.509 CA registreren bij IoT Hub, waar het zal dienen om Smart-X-Widgets te authenticeren terwijl ze verbinding maken. Dit is een eenmalig proces waarmee u een willekeurig aantal Smart-X-Widget-apparaten verifiëren en beheren. Dit proces is eenmalig vanwege een eenmalige relatie tussen het certificaat van de autoriteit en apparaten en vormt ook een van de belangrijkste voordelen van het gebruik van de X.509 CA-verificatiemethode. Het alternatief is om individuele certificaat duimafdrukken uploaden voor elk Smart-X-Widget apparaat waardoor de operationele kosten.

Het registreren van het X.509 CA-certificaat is een proces in twee stappen, het uploaden van certificaten en het bewijs van het bezit van het certificaat.

![Een X509CA-certificaat registreren](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X.509 CA-certificaat uploaden

Het X.509 CA-certificaat uploadproces is precies dat, upload het CA-certificaat naar IoT Hub. IoT Hub verwacht het certificaat in een bestand. Company-X uploadt eenvoudig het certificaatbestand. Het certificaatbestand MAG IN GEEN GEVAL privésleutels bevatten. Best practices van normen voor Public Key Infrastructure (PKI) mandaten dat kennis van Company-X's prive in dit geval uitsluitend binnen Company-X.

### <a name="proof-of-possession-of-the-certificate"></a>Bewijs van bezit van het certificaat

Het X.509 CA-certificaat is, net als elk digitaal certificaat, openbare informatie die vatbaar is voor afluisteren. Als zodanig kan een afluisteraar een certificaat onderscheppen en proberen het als zijn eigen certificaat te uploaden. In ons voorbeeld wil IoT Hub ervoor zorgen dat het CA-certificaat Company-X echt van Company-X is. Zij doet dit door Company-X uit te dagen om aan te tonen dat zij in feite het certificaat bezitten door middel van een [proof-of-possession (PoP) flow](https://tools.ietf.org/html/rfc5280#section-3.1). De proof-of-possession flow houdt in dat IoT Hub een willekeurig nummer genereert dat door Company-X moet worden ondertekend met behulp van zijn private key. Als Company-X pki best practices volgde en hun privésleutel beschermde, zouden alleen zij in staat zijn om correct te reageren op de proof-of-possession-uitdaging. IoT Hub gaat over tot het registreren van het X.509 CA-certificaat na een succesvolle reactie van de proof-of-possession-uitdaging.

Een succesvol antwoord op de proof-of-possession challenge van IoT Hub maakt de X.509 CA registratie compleet.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Apparaten aanmelden bij een vertrouwensketen van certificaten

IoT vereist dat elk apparaat een unieke identiteit bezit. Deze identiteiten staan in de formuliercertificaten voor verificatieschema's op basis van certificaten. In ons voorbeeld betekent dit dat elke Smart-X-Widget in het bezit moet zijn van een uniek apparaatcertificaat. Hoe is Company-X hiervoor in de supply chain aan de opte erin gaan?

Een manier om dit te doen is het vooraf genereren van certificaten voor Smart-X-Widgets en het toevertrouwen van kennis van overeenkomstige unieke apparaat private keys met supply chain partners. Voor Company-X betekent dit dat factory-Y en Technicus-Z moeten worden toevertrouwen. Hoewel dit een geldige methode is, komt het met uitdagingen die moeten worden overwonnen om het vertrouwen als volgt te waarborgen:

1. Het hebben van apparaat private sleutels te delen met supply chain partners, naast het negeren van PKI best practices van nooit delen van prive-sleutels, maakt het opbouwen van vertrouwen in de supply chain duur. Het betekent dat kapitaalsystemen zoals beveiligde kamers om persoonlijke sleutels van apparaten te huisvesten, en processen zoals periodieke beveiligingsaudits moeten worden geïnstalleerd. Beide voegen kosten toe aan de supply chain.

2. Veilig goed voor apparaten in de supply chain en later beheren ze in de implementatie wordt een een-op-een taak voor elke key-to-device paar vanaf het punt van apparaat unieke certificaat (vandaar prive-sleutel) generatie tot apparaat pensionering. Dit sluit groepsbeheer van apparaten uit, tenzij het concept van groepen op de een of andere manier expliciet in het proces is ingebouwd. Veilig beheer van de boekhouding en de levenscyclus van apparaten wordt daarom een zware operationele last. In ons voorbeeld zou Company-X deze last dragen.

X.509 CA certificaat authenticatie biedt elegante oplossingen voor afore genoemde uitdagingen door het gebruik van certificaatketens. Een certificaatketen is het resultaat van een CA die een tussenliggende CA ondertekent die op zijn beurt een andere tussenliggende CA tekent en zo verder gaat totdat een laatste tussenliggende CA een apparaat ondertekent. In ons voorbeeld, Company-X tekent Factory-Y, die op zijn beurt tekent Technician-Z dat eindelijk tekent Smart-X-Widget.

![Hiërarchie van certificaatketen](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

Bovenstaande cascade van certificaten in de keten presenteert de logische overdracht van autoriteit. Veel supply chains volgen deze logische overdracht waarbij elke tussentijdse CA wordt aangemeld in de keten terwijl het ontvangen van alle upstream CA-certificaten, en de laatste tussentijdse CA eindelijk elk apparaat ondertekent en alle autoriteitscertificaten uit de keten injecteert in het apparaat. Dit is gebruikelijk wanneer het contract productiebedrijf met een hiërarchie van fabrieken opdracht geeft aan een bepaalde fabriek om de productie te doen. Hoewel de hiërarchie kan worden verschillende niveaus diep (bijvoorbeeld door geografie / producttype / productielijn), alleen de fabriek aan het einde krijgt om te communiceren met het apparaat, maar de keten wordt gehandhaafd vanaf de top van de hiërarchie.

Alternatieve ketens kunnen verschillende tussenliggende CA-interactie met het apparaat hebben, in welk geval de CA die met het apparaat communiceert, op dat moment de inhoud van de certificaatketen injecteert. Hybride modellen zijn ook mogelijk wanneer slechts een deel van de CA fysieke interactie met het apparaat heeft.

In ons voorbeeld werken zowel Factory-Y als Technician-Z samen met de Smart-X-Widget. Terwijl Company-X eigenaar is van Smart-X-Widget, het eigenlijk niet fysiek interactie met het in de gehele supply chain. De certificaatketen van vertrouwen voor Smart-X-Widget omvat daarom Company-X ondertekening Factory-Y die op zijn beurt tekent Technician-Z die vervolgens zal de definitieve handtekening van Smart-X-Widget. De productie en installatie van Smart-X-Widget omvatten Factory-Y en Technician-Z met behulp van hun respectieve tussentijdse CA-certificaten om elke Smart-X-Widgets te ondertekenen. Het eindresultaat van dit hele proces is Smart-X-Widgets met unieke apparaatcertificaten en certificaatketen van vertrouwen die naar het Company-X CA-certificaat gaan.

![Vertrouwensketen van de certs van het ene bedrijf tot de certs van een ander bedrijf](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Dit is een goed punt om de waarde van de X.509 CA-methode te bekijken. In plaats van certificaten voor elke Smart-X-Widget vooraf te genereren en af te geven in de supply chain, hoefde Company-X factory-Y maar één keer te ondertekenen. In plaats van elk apparaat gedurende de gehele levenscyclus van het apparaat te moeten volgen, kan Company-X nu apparaten volgen en beheren via groepen die natuurlijk uit het supply chain-proces komen, bijvoorbeeld apparaten die door Technician-Z na juli van een jaar zijn geïnstalleerd.

Last but not least, de CA-methode van authenticatie betrekt veilige verantwoording in de apparaat productie supply chain. Vanwege het proces van de certificaatketen worden de acties van elk lid in de keten cryptografisch geregistreerd en verifieerbaar.

Dit proces is gebaseerd op bepaalde veronderstellingen die moeten worden opgedoken voor volledigheid. Het vereist onafhankelijke creatie van apparaat unieke openbare / private key pair en dat de private key worden beschermd in het apparaat. Gelukkig, veilige silicium chips in de vorm van Hardware Secure Modules (HSM) in staat om intern het genereren van sleutels en de bescherming van prive-sleutels bestaan. Company-X hoeft slechts een van dergelijke chips toe te voegen aan de component bill of materials van Smart-X-Widget.

## <a name="device-connection"></a>Apparaatverbinding

Eerdere secties hierboven zijn opgebouwd tot apparaatverbinding. Door simpelweg een X.509 CA-certificaat één keer te registreren bij IoT Hub, hoe maken potentieel miljoenen apparaten verbinding en worden ze vanaf de eerste keer geverifieerd?  Simpel; via hetzelfde certificaat uploaden en proof-of-possession flow die we eerder tegenkwamen bij het registreren van het X.509 CA certificaat.

Apparaten die zijn vervaardigd voor X.509 CA-verificatie zijn uitgerust met unieke certificaten van het apparaat en een certificaatketen uit hun respectieve productieketen. Apparaatverbinding, zelfs voor de allereerste keer, gebeurt in een proces in twee stappen: certificaatketen uploaden en proof-of-possession.

Tijdens het uploaden van de certificaatketen uploadt het apparaat het unieke certificaat van het apparaat samen met de certificaatketen die erin is geïnstalleerd naar IoT Hub. Met behulp van het vooraf geregistreerde X.509 CA-certificaat kan IoT Hub een aantal dingen cryptografisch valideren, dat de geüploade certificaatketen intern consistent is en dat de keten is ontstaan door de geldige eigenaar van het X.509 CA-certificaat. Net was met de X.509 CA registratieproces, IoT Hub zou een proof-of-possession challenge-response proces om na te gaan dat de keten en dus apparaat certificaat eigenlijk behoort tot het apparaat uploaden. Het doet dit door het genereren van een willekeurige uitdaging te worden ondertekend door het apparaat met behulp van de private sleutel voor validatie door IoT Hub. Een succesvolle reactie activeert IoT Hub om het apparaat als authentiek te accepteren en het verbinding te verlenen.

In ons voorbeeld zou elke Smart-X-Widget zijn unieke certificaat voor apparaten uploaden samen met Factory-Y- en Technician-Z X.509 CA-certificaten en vervolgens reageren op de proof-of-possession-uitdaging van IoT Hub.

![Stroom van het ene cert naar het andere, pop uitdaging van hub](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Merk op dat de basis van vertrouwen ligt in de bescherming van privésleutels, waaronder persoonlijke sleutels van het apparaat. We kunnen daarom niet genoeg benadrukken het belang van veilige silicium chips in de vorm van Hardware Secure Modules (HSM) voor de bescherming van het apparaat prive-sleutels, en de algehele beste praktijk van nooit delen van prive-sleutels, zoals een fabriek toevertrouwen een andere met haar privésleutel.
---
title: Azure Kinect DK-hardwarespecificaties
description: Inzicht in de onderdelen, specificaties en mogelijkheden van Azure Kinect DK.
author: tesych
ms.author: tesych
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/14/2020
ms.topic: article
keywords: azure, kinect, specificaties, hardware, DK, mogelijkheden, diepte, kleur, RGB, IMU, microfoon, matrix, diepte
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: dbe4cac64e324c1188ec5a01b4da3935a5d5b479
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371289"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Azure Kinect DK-hardwarespecificaties

In dit artikel wordt in detail beschreven hoe Azure Kinect-hardware de nieuwste sensortechnologieën van Microsoft integreert in één USB-verbonden apparaat.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Voorwaarden

De volgende afkortingen worden in dit artikel gebruikt.

- NFOV (dieptemodus met smalle field-of-view)
- WFOV (dieptemodus met brede field-of-view)
- FOV (field-of-view)
- FPS (frames per seconde)
- IMU (Inertial Measurement Unit)
- FoI (Field of Interest)

## <a name="product-dimensions-and-weight"></a>Productdimensies en -gewicht

Het Azure Kinect-apparaat heeft de volgende dimensies voor grootte en gewicht.

- **Dimensies**: 103 x 39 x 126 mm
- **Gewicht**: 440 g

![Azure Kinect DK-dimensies](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>Besturingsomgeving

Azure Kinect DK is bedoeld voor ontwikkelaars en commerciële bedrijven die werken onder de volgende omgevingsomstandigheden:

- **Temperatuur**: 10-25 ⁰C
- **Vochtigheid**: 8-90% (niet-condenserend) relatieve vochtigheid

> [!NOTE]
> Gebruik buiten deze omgevingsvoorwaarden kan ervoor zorgen dat het apparaat een fout retourneert en/of onjuist functioneert. Deze omgevingsvoorwaarden zijn van toepassing op de omgeving direct rond het apparaat, onder alle bedieningsomstandigheden. Bij gebruik met een externe behuizing, worden actief temparatuurbeheer en/of andere koelingsoplossingen aanbevolen om ervoor te zorgen dat het apparaat binnen deze voorwaarden blijft werken. Het ontwerp van het apparaat bevat een koelkanaal tussen het voorste gedeelte en de bevestigingsclip aan de achterkant. Wanneer u het apparaat implementeert, moet u ervoor zorgen dat dit koelkanaal niet wordt geblokkeerd.

Raadpleeg de aanvullende [productinformatie over de veiligheid](https://support.microsoft.com/help/4023454/safety-information).

## <a name="depth-camera-supported-operating-modes"></a>Besturingsmodi die worden ondersteund op de dieptecamera

Azure Kinect DK integreert een door Microsoft ontworpen dieptecamera met 1 megapixel ToF (Time-of-Flight), met behulp van de [afbeeldingssensor die werd gepresenteerd op de ISSCC 2018-conferentie](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018). De dieptecamera biedt ondersteuning voor de onderstaande modi:

 | Modus            | Oplossing | FoI       | FPS                | Besturingsbereik* | Belichtingstijd |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV, zonder binning   | 640x576    | 75°x65°   | 0, 5, 15, 30       | 0,5 - 3,86 m       | 12,8 ms        |
| NFOV 2x2, met binning (SW) | 320x288    | 75°x65°   | 0, 5, 15, 30       | 0,5 - 5,46 m       | 12,8 ms        |
| WFOV 2x2, met binning | 512x512    | 120°x120° | 0, 5, 15, 30       | 0,25 - 2,88 m      | 12,8 ms        |
| WFOV, zonder binning   | 1024x1024  | 120°x120° | 0, 5, 15           | 0,25 - 2,21 m      | 20,3 ms        |
| Passieve IR      | 1024x1024  | N.v.t.       | 0, 5, 15, 30       | N.v.t.              | 1,6 ms         |

\*15% tot 95% reflectievermogen bij 850 nm, 2,2 μW/cm<sup>2</sup>/nm, willekeurige fout standaarddeviatie ≤ 17 mm, typische systeemfout < 11 mm + 0,1 % afstand zonder interferentie met meerdere paden. De diepte die is opgegeven buiten het aangegeven bereik, is afhankelijk van het reflectievermogen van objecten.

## <a name="color-camera-supported-operating-modes"></a>Besturingsmodi die worden ondersteund op de dieptecamera.

Azure Kinect DK omvat een OV12A10 12MP CMOS-sensor met een rollende sluiter. De systeemeigen besturingsmodi worden hieronder weergegeven:

|             RGB-cameraresolutie (HxV)  |          Hoogte-breedteverhouding  |          Indelingsopties   |          Framesnelheden (FPS)  |          Nominale FOV (HxV)(na verwerking)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840x2160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       2560x1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       1920x1080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90°x59°                              |
|       1280x720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90°x59°                              |
|       4096x3072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90°x74.3°                            |
|       2048x1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90°x74.3°                            |

De RGB-camera is compatibel met USB-videoklasse en kan worden gebruikt zonder de sensor-SDK. De kleurruimte van de RGB-camera: BT.601 volledig bereik [0..255]. 

> [!NOTE]
> De sensor-SDK kan kleurenafbeeldingen leveren in de BGRA-pixelindeling. Dit is geen native modus die op het apparaat wordt ondersteund, en zorgt bij gebruik voor extra CPU-belasting. De host-CPU wordt gebruikt om MJPEG-afbeeldingen te converteren die zijn ontvangen van het apparaat.

## <a name="rgb-camera-exposure-time-values"></a>Waarden voor belichtingstijd van RGB-camera

Hieronder ziet u de toewijzing voor de geschikte handmatige belichtingswaarden voor de RGB-camera:

| exp| 2^exp | 50Hz   |60Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10.000|   8330 |
|  -7|    7813|  20.000|  16670 |
|  -6|   15.625|  30.000|  33.330 |
|  -5|   31.250|  40.000|  41.670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125.000|  60000|  66.670 |
|  -2|  250.000|  80.000|  83.330 |
|  -1|  500.000| 100.000| 100.000 |
|   0| 1\.000.000| 120.000| 116.670 |
|   1| 2\.000.000| 130.000| 133.330 |

## <a name="depth-sensor-raw-timing"></a>Onbewerkte tijdsinstellingen van dieptesensor

Dieptemodus | IR <br>Pulsen | Puls <br>Breedte  | Niet-actief <br>Perioden| Niet-actieve tijd | Belichting <br> Time
-|-|-|-|-|-
NFOV, zonder binning <br>  NFOV 2xx, met binning <br> WFOV 2x2, met binning | 9 | 125 us | 8 | 1450 us | 12,8 ms 
WFOV, zonder binning                                            | 9 | 125 us | 8 | 2390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Gezichtsveld (field-of-view) van camera

In de volgende afbeelding ziet u de diepte en field-of-view van de RGB camera, of de hoeken die worden ‘gezien’ met de sensoren. In dit diagram wordt de RGB-camera weergegeven in de 4:3-modus.

![Camera FOV](./media/resources/hardware-specs-media/camera-fov.png)

Deze afbeelding demonstreert de field-of-view van de camera, gezien vanaf de voorkant op een afstand van 2000 mm.

![Camera FOV - voorkant](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Wanneer de diepte in de NFOV-modus is, heeft de RGB-camera een betere overlapping van pixels in de resolutie 4:3 dan in 16:9.

## <a name="motion-sensor-imu"></a>Bewegingssensor (IMU)

De ingebouwde IMU (Inertial Measurement Unit ) is een LSM6DSMUS en omvat zowel een versnellingsmeter als een gyroscoop. De versnellingsmeter en gyroscoop worden tegelijkertijd getest op 1,6 kHz. De voorbeelden worden op 208 Hz gemeld bij de host.

## <a name="microphone-array"></a>Microfoonmatrix

Azure Kinect DK omvat een circulaire matrix met zeven microfoons van hoge kwaliteit, die wordt geïdentificeerd als een standaardapparaat met USB-audioklasse 2.0. Alle 7 kanalen zijn toegankelijk. De prestatiespecificaties zijn:

- Gevoeligheid: -22 dBFS (94 dB SPL, 1 kHz)
- Verhouding signaal-ruis > 65 dB
- Punt van akoestische overbelasting: 116 dB

![Microfoon](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

Azure Kinect DK is een samengesteld USB 3-apparaat dat de volgende hardware-eindpunten beschikbaar maakt voor het besturingssysteem:

Leveranciers-id is 0x045E (Microsoft), tabel voor product-id hieronder:

|    USB-interface        |    PNP IP    |     Opmerkingen            |
|-------------------------|--------------|----------------------|
|    USB 3.1 Gen1-hub    |    0x097A    |    Hoofd-hub    |
|    USB 2.0-hub         |    0x097B    |    HS USB          |
|    Dieptecamera       |    0x097C    |    USB 3.0            |
|    Kleurencamera       |    0x097D    |    USB 3.0            |
|    Microfoons        |    0x097E    |    HS USB          |

## <a name="indicators"></a>Indicatoren

Het apparaat heeft aan de voorkant een streamingindicator voor de camera. Deze kan via een programma worden uitgeschakeld met behulp van de sensor-SDK

De status-LED achter het apparaat geeft de apparaatstatus aan:

| Wanneer het lampje is     | Betekent dit                                                   |
|-----------------------|------------------------------------------------------------|
| Effen wit           | Apparaat is actief en werkt goed.                         |
| Wit knipperend        | Apparaat is aan, maar heeft geen USB 3.0-gegevensverbinding.   |
| Geelbruin knipperend        | Apparaat is onvoldoende opgeladen om te kunnen worden gebruikt.               |
| Geelbruin-wit knipperend  | Firmware-update of -herstel wordt uitgevoerd                    |

## <a name="power-device"></a>Apparaat opladen

Het apparaat kan op twee manieren worden opgeladen:

1. Met behulp van de meegeleverde voeding. Gegevens worden verbonden via een afzonderlijke USB-kabel Type-C naar Type-A.
2. Met behulp van de kabel Type-C naar Type-C, voor zowel energie als gegevens.

Er wordt geen kabel Type-C naar Type-C meegeleverd met Azure Kinect DK.

> [!NOTE]
> - De meegeleverde voedingskabel is een USB-kabel Type-A naar een coaxkabel. Gebruik de geleverde wandcontactdoos met deze kabel. Het apparaat gebruikt mogelijk meer stroom dat de twee USB Type-A-standaardpoorten kunnen leveren.
> - USB-kabels zijn belangrijk, en wij raden u aan kabels van hoge kwaliteit te gebruiken en de functionaliteit van deze kabels te controleren, vóórdat u de eenheid op afstand implementeert.

> [!TIP]
> Een goede Type-C-kabel selecteren:
> - De [USB-gecertificeerde kabel](https://www.usb.org/products) moet zowel energie als gegevens ondersteunen.
> - Een passieve kabel moet minder dan 1,5 m lang zijn. Indien langer nodig is, gebruik dan een actieve kabel. 
> - De kabel moet minimaal 1.5 A ondersteunen. Anders moet u verbinding maken met een externe voeding.

Kabel controleren:

- Koppel het apparaat via de kabel aan de host-pc.
- Controleer of alle apparaten juist worden weergegeven in Windows-apparaatbeheer. De diepte- en RGB-camera moeten worden weergegeven zoals in het onderstaande voorbeeld.

  ![Azure Kinect DK in Apparaatbeheer](./media/resources/hardware-specs-media/device-manager.png)

- Controleer in de Azure Kinect-viewer of de kabel betrouwbaar kan streamen vanaf alle sensoren, met de volgende instellingen:

  - Dieptecamera: NFOV, zonder binning
  - RGB-camera: 2160p
  - Microfoons en IMU ingeschakeld

## <a name="what-does-the-light-mean"></a>Wat wil het lichtje zeggen?

De aan/uit-indicator is een LED-lampje aan de achterkant van Azure Kinect DK. De kleur van het LED-lampje verandert, afhankelijk van de status van uw apparaat.

![Op de afbeelding ziet u de achterkant van de Azure Kinect DK Er zijn drie genummerde bijschriften: één voor een LED-indicator, en daaronder twee voor kabels.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

In deze illustratie zijn de volgende onderdelen gelabeld:

1. Aan/uit-indicator
1. Stroomkabel (aangesloten op de voedingsbron)
1. USB-C-gegevenskabel (aangesloten op de pc)

Controleer of de kabels zijn aangesloten zoals weergegeven. Kijk daarna in de volgende tabel wat de betekenis is van de verschillende statussen van het aan/uit-lampje.

|Wanneer het lampje is: |Betekent dit dat: |En moet u: |
| ---| --- | --- |
|Effen wit |Het apparaat is aan en werkt juist. |Gebruik het apparaat. |
|Niet verlicht |Het apparaat is niet aangesloten op de pc. |Zorg ervoor dat de ronde stroomkabel is aangesloten op het apparaat en op de USB-stroomadapter.<br /><br />Zorg ervoor dat de USB-C-kabel is aangesloten op het apparaat en op de pc. |
|Wit knipperend |Apparaat krijgt stroom, maar heeft geen USB 3.0-gegevensverbinding. |Zorg ervoor dat de ronde stroomkabel is aangesloten op het apparaat en op de USB-stroomadapter.<br /><br />Zorg ervoor dat de USB-C-kabel is aangesloten op het apparaat en op een USB 3.0-poort op de pc.<br /><br />Sluit het apparaat aan op een andere USB 3.0-poort op de pc.<br /><br />Open Apparaatbeheer op de pc (**Start** > **Configuratiescherm** > **Apparaatbeheer**), en controleer of de pc een ondersteunde USB 3.0-hostcontroller heeft. |
|Geelbruin knipperend |Apparaat is onvoldoende opgeladen om te kunnen worden gebruikt. |Zorg ervoor dat de ronde stroomkabel is aangesloten op het apparaat en op de USB-stroomadapter.<br /><br />Zorg ervoor dat de USB-C-kabel is aangesloten op het apparaat en op de pc. |
|Geelbruin, vervolgens wit knipperend |Het apparaat krijgt stroom en ontvangt een firmware-update, of de fabrieksinstellingen worden hersteld op het apparaat. |Wacht totdat het lampje van de aan/uit-indicator effen wit is. Zie [Azure Kinect DK opnieuw instellen](reset-azure-kinect-dk.md) voor meer informatie. |

## <a name="power-consumption"></a>Energieverbruik

Azure Kinect DK verbruikt maximaal 5,9 W. Het precieze energieverbruik is afhankelijk van het gebruik.

## <a name="calibration"></a>Kalibratie

Azure Kinect DK is in de fabriek gekalibreerd. De kalibratieparameters voor visuele en inerte sensoren kunnen met behulp van een programma worden opgevraagd via de sensor-SDK.

## <a name="device-recovery"></a>Apparaatherstel

U kunt de apparaatfirmware opnieuw instellen op de oorspronkelijke firmware met behulp van de knop onder de vergrendeling.

![Herstelknop voor Azure Kinect DK](./media/resources/hardware-specs-media/recovery.png)

Lees [deze instructies](reset-azure-kinect-dk.md) om het apparaat te herstellen.

## <a name="next-steps"></a>Volgende stappen

- [De sensor-SDK van Azure Kinect gebruiken](about-sensor-sdk.md)
- [Hardware instellen](set-up-azure-kinect-dk.md)

---
title: TexConv - Gereedschap Textuurconversie
description: Handleiding voor het texconv-opdrachtregelgereedschap
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680022"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Gereedschap Textuurconversie

TexConv is een commandline-tool voor het verwerken van texturen van typische invoerindelingen zoals PNG, TGA, JPEG en DDS tot geoptimaliseerde indelingen voor runtime-verbruik.
Hoewel het meest voorkomende scenario is `A.xxx` om één `B.yyy`invoerbestand om te zetten in een geoptimaliseerde indeling, heeft het hulpprogramma veel extra opties voor geavanceerd gebruik.

## <a name="command-line-help"></a>Opdrachtregelhelp

Als u TexConv.exe met de `--help` parameter uitvoert, worden alle beschikbare opties weergegeven. Daarnaast print TexConv de gebruikte opties wanneer deze wordt uitgevoerd, om te begrijpen wat het doet. Raadpleeg deze uitvoer voor meer informatie.

## <a name="general-usage"></a>Algemeen gebruik

TexConv produceert altijd **precies één uitvoerbestand.** Het kan **meerdere invoerbestanden** gebruiken om de uitvoer van samen te stellen. Voor de assemblage heeft het ook een **kanaaltoewijzing**nodig, die het vertelt welk kanaal *(Rood, Groen, Blauw* of *Alpha)* moet nemen uit welk invoerbestand en het naar welk kanaal van de uitvoerafbeelding moet verplaatsen.

De meest rechttoe rechtaan opdrachtregel is dit:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`geeft het uitvoerbestand en de uitvoerindeling op
- `-in0`geeft de eerste invoerafbeelding op
- `-rgba`vertelt dat de uitvoerafbeelding alle vier de kanalen moet gebruiken en dat ze 1:1 uit de invoerafbeelding moeten worden genomen

## <a name="multiple-input-files"></a>Meerdere invoerbestanden

Als u de uitvoer uit meerdere invoerbestanden `-in` wilt samenstellen, geeft u elk invoerbestand op met de optie met een toenemend aantal:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Bij het monteren van een kubuskaart van `-right`2D-texturen `-front` `-back` kan `-px` `-nx`men `-py` `-ny`ook `-pz` `-nz`gebruik maken van `-left`, , `-top`, `-bottom`, of , , , , .

Om deze ingangen aan het uitvoerbestand toe te zetten, is een juiste kanaaltoewijzing nodig.

## <a name="channel-mappings"></a>Kanaaltoewijzingen

De opties voor kanaaltoewijzing geven aan van welke invoer de opgegeven uitvoerkanalen moeten worden gevuld. U de invoer voor elk kanaal afzonderlijk als volgt opgeven:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Hier zouden de RGB-kanalen van de uitvoer worden gevuld met behulp van de eerste invoerafbeelding, maar rood en blauw worden verwisseld. Het alfakanaal van de uitvoer wordt gevuld met de waarden van het rode kanaal van de tweede invoerafbeelding.

Het afzonderlijk opgeven van de toewijzing voor elk kanaal geeft de grootste flexibiliteit. Voor het gemak kan hetzelfde worden geschreven met behulp van "swizzling" operators:

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Uitvoerkanalen

De volgende opties voor het toewijzen van kanalen zijn beschikbaar:

- `-r`, `-g` `-b`, `-a` : Deze specificeren toewijzingen voor één kanaal
- `-rg`: Geef de rode en groene kanaaltoewijzingen op.
- `-rgb`: Geef de toewijzingen van het rode, groene en blauwe kanaal op.
- `-rgba`: Hiermee geeft u alle vier kanaaltoewijzingen op.

Als u alleen het R-, RG- of RGB-kanaal vermeldt, geeft het TexConv de opdracht om een uitvoerbestand te maken met respectievelijk slechts 1, 2 of 3 kanalen.

### <a name="input-swizzling"></a>Invoerswizzling

Wanneer wordt vermeld welke invoerstructuur welk uitvoerkanaal moet vullen, kan men de input swizzle:

- `-rgba in0`is gelijk aan`-rgba in0.rgba`
- `-rgba in0.bgra`zal swizzle de input kanalen
- `-rgb in0.rrr`dupliceert het rode kanaal in alle kanalen

Men kan ook kanalen vullen met zwart of wit:

- `-rgb in0 -a white`maakt een 4-kanaals uitvoerstructuur, maar stelt alfa in op volledig ondoorzichtig
- `-rg black -b white`creëert een volledig blauwe textuur

## <a name="common-options"></a>Algemene opties

De meest interessante opties staan hieronder vermeld. Meer opties worden `TexConv --help`vermeld door .

### <a name="output-type"></a>Uitvoertype

- `-type 2D`: De uitvoer zal een gewone 2D-afbeelding zijn.
- `-type Cubemap`: De uitvoer is een kubusafbeelding. Alleen ondersteund voor DDS-uitvoerbestanden. Wanneer dit is opgegeven, kan men de kubuskaart van 6 regelmatige 2D-invoerafbeeldingen assembleren.

### <a name="image-compression"></a>Afbeeldingscompressie

- `-compression none`: De uitvoerafbeelding wordt niet gecomprimeerd.
- `-compression medium`: Als deze wordt ondersteund, gebruikt de uitvoerafbeelding compressie zonder al te veel kwaliteit op te offeren.
- `-compression high`: Als deze wordt ondersteund, gebruikt de uitvoerafbeelding compressie- en opofferingkwaliteit ten gunste van een kleiner bestand.

### <a name="mipmaps"></a>Mipmaps Mipmaps

TexConv genereert standaard mipmaps wanneer de uitvoerindeling dit ondersteunt.

- `-mipmaps none`: Mipmaps worden niet gegenereerd.
- `-mipmaps Linear`: Als mipmaps worden ondersteund, worden ze gegenereerd met behulp van een doosfilter.

### <a name="usage-srgb--gamma-correction"></a>Gebruik (sRGB / gammacorrectie)

De `-usage` optie geeft het doel van de uitvoer aan en vertelt TexConv dus of gammacorrectie moet worden toegepast op de invoer- en uitvoerbestanden. Het gebruik heeft alleen invloed op de RGB-kanalen. Het alfakanaal wordt altijd beschouwd als 'lineaire' waarden. Als het gebruik niet is opgegeven, probeert de modus 'automatisch' het gebruik te detecteren vanuit de indeling en bestandsnaam van de eerste invoerafbeelding. De single- en dual channel output formaten zijn bijvoorbeeld altijd lineair. Controleer de output om te zien welke beslissing TexConv heeft genomen.

- `-usage Linear`: De uitvoerafbeelding bevat waarden die geen kleuren vertegenwoordigen. Dit is meestal het geval voor metalen en ruwheid texturen, evenals allerlei maskers.

- `-usage Color`: De uitvoerafbeelding vertegenwoordigt kleur, zoals diffuse/albedo-kaarten. De sRGB-vlag wordt ingesteld in de uitvoer-DDS-header.

- `-usage HDR`: Het uitvoerbestand moet meer dan 8 bits per pixel gebruiken voor codering. Daarom worden alle waarden opgeslagen in de lineaire ruimte. Voor HDR-texturen maakt het niet uit of de gegevens kleur of andere gegevens vertegenwoordigen.

- `-usage NormalMap`: De uitvoerafbeelding vertegenwoordigt een normale kaart tussen raaklijnen en spatie. Waarden worden genormaliseerd en mipmapberekening wordt enigszins geoptimaliseerd.

- `-usage NormalMap_Inverted`: De uitgang is een raaklijn-ruimte normale kaart met Y wijst in de tegenovergestelde richting dan de ingang.

### <a name="image-rescaling"></a>Afbeelding opnieuw schalen

- `-minRes 64`: Hiermee geeft u de minimale resolutie van de uitvoer op. Als de invoerafbeelding kleiner is, wordt deze opgeschaald.
- `-maxRes 1024`: Hiermee geeft u de maximale resolutie van de uitvoer op. Als de invoerafbeelding groter is, wordt deze gedownscaled.
- `-downscale 1`: Als dit groter is dan 0, worden de invoerafbeeldingen gehalveerd in resolutie N-tijden. Gebruik dit om een algehele kwaliteitsvermindering toe te passen.

## <a name="examples"></a>Voorbeelden

### <a name="convert-a-color-texture"></a>Een kleurstructuur converteren

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Een normale kaart converteren

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Een HDR-kubuskaart maken

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Een geweldige bron voor HDR-kubuskaarten is [hdrihaven.com.](https://hdrihaven.com/hdris/)

### <a name="bake-multiple-images-into-one"></a>Meerdere afbeeldingen in één afbeeldingen bakken

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Eén kanaal extraheren

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```

---
title: TexConv-patroon conversie programma
description: Gebruikers handleiding voor het TexConv-opdracht regel programma
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80680022"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv-patroon conversie programma

TexConv is een opdracht regel programma voor het verwerken van structuren van typische invoer indelingen, zoals PNG, TGA, JPEG en DDS in geoptimaliseerde indelingen voor runtime-verbruik.
Het meest voorkomende scenario is om één invoer bestand te converteren `A.xxx` naar een geoptimaliseerde indeling `B.yyy` . het hulp programma heeft veel extra opties voor Geavanceerd gebruik.

## <a name="command-line-help"></a>Help voor de opdracht regel

Als u TexConv.exe met de `--help` para meter uitvoert, worden alle beschik bare opties weer geven. Daarnaast drukt TexConv de gebruikte opties af wanneer deze wordt uitgevoerd, zodat u beter kunt begrijpen wat er gebeurt. Raadpleeg deze uitvoer voor meer informatie.

## <a name="general-usage"></a>Algemeen gebruik

TexConv maakt altijd **precies één uitvoer** bestand. Het kan **meerdere invoer** bestanden gebruiken voor het samen stellen van de uitvoer van. Voor de assembly moet er ook een **kanaal toewijzing**worden opgegeven, waarmee wordt aangegeven welk kanaal (*rood, groen, blauw* of *alpha*) moet worden meegenomen van het invoer bestand en dit naar het kanaal van de uitvoer installatie kopie te verplaatsen.

De meest rechtse opdracht regel is als volgt:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out` Hiermee geeft u het uitvoer bestand en de indeling op
- `-in0` Hiermee wordt de eerste invoer afbeelding opgegeven
- `-rgba` Hiermee wordt aangegeven dat de uitvoer installatie kopie alle vier de kanalen moet gebruiken en dat deze 1:1 van de invoer installatie kopie moeten worden genomen

## <a name="multiple-input-files"></a>Meerdere invoer bestanden

Als u de uitvoer van meerdere invoer bestanden wilt samen stellen, geeft u elk invoer bestand op met behulp van de `-in` optie met een verhoogd aantal:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

Bij het samen stellen van een cubemap vanuit 2D-bitmappatronen, kan er ook een `-right` ,, `-left` `-top` , `-bottom` ,, of,,,, `-front` `-back` `-px` `-nx` `-py` `-ny` `-pz` , worden gebruikt `-nz` .

Als u deze invoer wilt toewijzen aan het uitvoer bestand, is een juiste kanaal toewijzing nodig.

## <a name="channel-mappings"></a>Kanaal toewijzingen

De opties voor kanaal toewijzing geven op waaruit de invoer moet worden gevuld in de opgegeven uitvoer kanalen. U kunt de invoer voor elk kanaal afzonderlijk als volgt opgeven:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Hier worden de RGB-kanalen van de uitvoer gevuld met de eerste invoer afbeelding, maar rood en blauw worden omgewisseld. Het Alfa kanaal van de uitvoer wordt opgevuld met de waarden uit het rode kanaal van de tweede invoer afbeelding.

Het opgeven van de toewijzing voor elk kanaal biedt de grootste flexibiliteit. Voor het gemak kan hetzelfde worden geschreven met behulp van de swizzling-Opera tors:

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Uitvoer kanalen

De volgende opties voor kanaal toewijzing zijn beschikbaar:

- `-r`, `-g` , `-b` , `-a` : Deze geven toewijzingen voor één kanaal op
- `-rg` : Geef de toewijzingen van het rode en groene kanaal op.
- `-rgb` : Geef de toewijzingen voor het rood, groen en blauw kanaal op.
- `-rgba` : Hiermee geeft u alle vier kanaal toewijzingen op.

Als alleen het R-, RG-of RGB-kanaal wordt vermeld, krijgt TexConv een uitvoer bestand te maken met respectievelijk slechts 1, 2 of 3 kanalen.

### <a name="input-swizzling"></a>Invoer swizzling

Wanneer wordt aangegeven welk invoer patroon moet worden gevuld met het uitvoer kanaal, kan een van de invoer swizzle:

- `-rgba in0` is gelijk aan `-rgba in0.rgba`
- `-rgba in0.bgra` swizzle worden de invoer kanalen
- `-rgb in0.rrr` dupliceert het rood kanaal in alle kanalen

U kunt ook kanalen vullen met zwart of wit:

- `-rgb in0 -a white` maakt een uitvoer patroon van 4 kanalen, maar stelt Alfa in op volledig ondoorzichtig
- `-rg black -b white` Er wordt een volledig blauw patroon gemaakt

## <a name="common-options"></a>Algemene opties

De meest interessante opties worden hieronder weer gegeven. Meer opties worden weer gegeven door `TexConv --help` .

### <a name="output-type"></a>Uitvoertype

- `-type 2D` : De uitvoer is een gewone 2D-afbeelding.
- `-type Cubemap` : De uitvoer is een cubemap-installatie kopie. Alleen ondersteund voor DDS-uitvoer bestanden. Als deze is opgegeven, kan de cubemap worden samengesteld op basis van 6 reguliere 2D-invoer installatie kopieën.

### <a name="image-compression"></a>Afbeeldingscompressie

- `-compression none` : De uitvoer afbeelding wordt gedecomprimeerd.
- `-compression medium` : Indien ondersteund, gebruikt de uitvoer installatie kopie compressie zonder dat er te veel kwaliteit wordt geoffert.
- `-compression high` : Indien ondersteund, gebruikt de uitvoer installatie kopie compressie en gaat de kwaliteit af van een kleiner bestand.

### <a name="mipmaps"></a>Mipmaps

Standaard genereert TexConv mipmaps wanneer de uitvoer indeling dit ondersteunt.

- `-mipmaps none` : Mipmaps wordt niet gegenereerd.
- `-mipmaps Linear` : Indien ondersteund, wordt mipmaps gegenereerd met een box-filter.

### <a name="usage-srgb--gamma-correction"></a>Gebruik (sRGB/gamma correctie)

`-usage`Met de optie geeft u het doel van de uitvoer op en vertelt u TexConv of gamma correctie moet worden toegepast op de invoer-en uitvoer bestanden. Het gebruik is alleen van invloed op de RGB-kanalen. Het Alfa kanaal wordt altijd geacht ' lineaire ' waarden te bevatten. Als er geen gebruik is opgegeven, probeert de modus ' auto ' het gebruik te detecteren vanuit de indeling en de bestands naam van de eerste invoer installatie kopie. Zo zijn bijvoorbeeld enkelvoudige en dubbele kanaal uitvoer indelingen altijd lineair. Controleer de uitvoer om te zien welke beslissings TexConv zijn gemaakt.

- `-usage Linear` : De uitvoer afbeelding bevat waarden die geen kleuren vertegenwoordigen. Dit is meestal het geval voor metalen en ruwe bitmappatronen, evenals alle soorten maskers.

- `-usage Color` : De uitvoer afbeelding vertegenwoordigt een kleur, zoals een diffuse/albedo kaarten. De sRGB-vlag wordt ingesteld in de header uitvoer DDS.

- `-usage HDR` : In het uitvoer bestand moeten meer dan 8 bits per pixel worden gebruikt voor de code ring. Daarom worden alle waarden opgeslagen in lineaire ruimte. Voor HDR-bitmappatronen is het niet belang rijk of de gegevens kleur of andere gegevens vertegenwoordigen.

- `-usage NormalMap` : De uitvoer afbeelding vertegenwoordigt een normale kaart voor de raaklijn ruimte. Waarden worden genormaliseerd en mipmap berekening wordt enigszins geoptimaliseerd.

- `-usage NormalMap_Inverted` : De uitvoer is een normale toewijzing van een raaklijn ruimte met Y die in tegenovergestelde richting ligt dan de invoer.

### <a name="image-rescaling"></a>Afbeelding opnieuw schalen

- `-minRes 64` : Hiermee geeft u de minimale resolutie van de uitvoer op. Als de invoer afbeelding kleiner is, wordt deze geschaald.
- `-maxRes 1024` : Hiermee geeft u de maximale resolutie van de uitvoer op. Als de invoer installatie kopie groter is, wordt de downscaled opgehaald.
- `-downscale 1` : Als dit groter is dan 0, worden de installatie kopieën in de resolutie N keer gehalveerd. Gebruik deze om een algemene kwaliteits vermindering toe te passen.

## <a name="examples"></a>Voorbeelden

### <a name="convert-a-color-texture"></a>Een kleuren patroon converteren

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Een normale kaart converteren

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Een HDR-cubemap maken

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

Een goede bron voor HDR-cubemaps is [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Meerdere afbeeldingen in één maken

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Een enkel kanaal extra heren

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```

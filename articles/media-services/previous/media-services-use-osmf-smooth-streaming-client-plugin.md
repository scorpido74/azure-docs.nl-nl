---
title: Soepele streaming-plug-in voor het Open Source Media Framework
description: Meer informatie over het gebruik van de plug-in Voor vloeiend streamen van Azure Media Services voor het Adobe Open Source Media Framework.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 45a2829411ea4713df898c90be73792718160cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255116"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>De Microsoft Smooth Streaming Plugin gebruiken voor het Adobe Open Source Media Framework  
## <a name="overview"></a>Overzicht
De Microsoft Smooth Streaming-plug-in voor Open Source Media Framework 2.0 (SS voor OSMF) breidt de standaardmogelijkheden van OSMF uit en voegt het afspelen van Microsoft Smooth Streaming-inhoud toe aan nieuwe en bestaande OSMF-spelers. De plugin voegt ook Smooth Streaming playback mogelijkheden toe aan Strobe Media Playback (SMP).

SS voor OSMF bevat twee versies van plugin:

* Statische smooth streaming plugin voor OSMF (.swc)
* Dynamic Smooth Streaming-plug-in voor OSMF (.swf)

Dit document gaat ervan uit dat de lezer een algemene werkkennis heeft van OSMF en OSMF plug-ins. Voor meer informatie over OSMF, zie de documentatie op de [officiële OSMF site.](http://osmf.org/)

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Streaming plugin voor OSMF 2.0
De plug-in ondersteunt het laden en afspelen van on-demand Smooth Streaming-inhoud met de volgende functies:

* On-demand smooth streaming afspelen (afspelen, pauzeren, zoeken, stoppen)
* Live Smooth Streaming afspelen (Afspelen)
* Live DVR-functies (Pauzeren, Zoeken, DVR afspelen, go-to-live)
* Ondersteuning voor videocodecs - H.264
* Ondersteuning voor audiocodecs - OC
* Meerdere audiotaalschakelen met OSMF ingebouwde API's
* Maximale selectie van afspeelkwaliteit met OSMF ingebouwde API's
* Sidecar ondertiteling met OSMF captions plugin
* Adobe&reg; &reg; Flash Player 11.4 of hoger.
* Deze versie ondersteunt alleen OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Ondersteunde functies en bekende problemen
Raadpleeg [dit document](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/)voor een volledige lijst met ondersteunde functies, niet-ondersteunde functies en bekende problemen.

## <a name="loading-the-plugin"></a>De plug-in laden
OSMF-plug-ins kunnen statisch (bij het compileren) of dynamisch (bij run-time) worden geladen. De Smooth Streaming plugin voor OSMF download bevat zowel dynamische als statische versies.

* Statische belasting: Om statisch te laden, is een statisch bestand (Library) vereist. Statische plug-ins worden toegevoegd als een verwijzing naar de projecten en samenvoegen in het uiteindelijke uitvoerbestand op het compileren tijd.
* Dynamisch laden: Om dynamisch te laden, is een vooraf gecompileerd (SWF)-bestand vereist. Dynamische plug-ins worden geladen in de runtime en niet opgenomen in de projectuitvoer. (Gecompileerde uitvoer) Dynamische plug-ins kunnen worden geladen met behulp van HTTP- en BESTANDSprotocollen.

Zie de officiële [OSMF-plug-inpagina](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)voor meer informatie over statisch en dynamisch laden.

### <a name="ss-for-osmf-static-loading"></a>SS voor OSMF Statisch laden
Het onderstaande codefragment laat zien hoe u de SS-plug-in voor OSMF statisch laden en een basisvideo afspelen met de OSMF MediaFactory-klasse. Voordat u de SS voor OSMF-code opneemt, moet u ervoor zorgen dat de projectreferentie de statische plug-in "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" bevat.

```
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS voor OSMF Dynamic Loading
Het onderstaande codefragment laat zien hoe u de SS-plug-in voor OSMF dynamisch laden en een basisvideo afspelen met de OSMF MediaFactory-klasse. Kopieer de dynamische plug-in 'MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf' naar de projectmap als u wilt laden met behulp van het FILE-protocol of kopieert onder een webserver voor HTTP-belasting. Het is niet nodig om "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" op te nemen in de projectreferenties.

pakket {

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Strobe Media afspelen met de SS ODMF Dynamic Plugin
De Smooth Streaming voor OSMF dynamische plug-in is compatibel met [Strobe Media Playback (SMP).](http://osmf.org/strobe_mediaplayback.html) U de SS voor OSMF-plug-in gebruiken om vloeiend afspelen van inhoud op vloeiend afspelen toe te voegen aan SMP. Kopieer hiervoor "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" onder een webserver voor HTTP-belasting met behulp van de volgende stappen:

1. Blader door de [installatiepagina voor het afspelen van Strobe-media](http://osmf.org/dev/2.0gm/setup.html). 
2. Stel de src in op een Smooth Streaming-bron (bijv.http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3. Breng de gewenste configuratiewijzigingen aan en klik op Voorbeeld en Bijwerken.
   
   **Opmerking** Uw inhoudswebserver heeft een geldige crossdomain.xml nodig. 
4. Kopieer en plak de code naar een eenvoudige HTML-pagina met uw favoriete teksteditor, zoals in het volgende voorbeeld:

        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



1. Voeg de Smooth Streaming OSMF-plug-in toe aan de insluitcode en sla op.
   
        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>
2. Sla uw HTML-pagina op en publiceer op een webserver. Blader naar de gepubliceerde webpagina&reg; met uw favoriete Flash Player-internetbrowser (Internet Explorer, Chrome, Firefox, enzovoort).
3. Geniet van vloeiende&reg; streaming-inhoud in Adobe Flash&reg; Player.

Voor meer informatie over de algemene OSMF ontwikkeling, zie de officiële [OSMF development pagina.](http://osmf.org/resources.html)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Microsoft Adaptive Streaming Plugin voor OSMF-update](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 


# desktopCapturer

> [`navigator.mediaDevices.getUserMedia`] API' sini kullanarak masaüstünden ses ve video yakalamak için kullanılabilecek medya kaynaklarıyla ilgili bilgilere erişin.

Süreç:[ İşleyici](../glossary.md#renderer-process)

Aşağıdaki örnek, ` Electron` isimli masaüstü penceresinden nasıl ekran kaydedilebileceğini göstermektedir:

```javascript
// İşleme sürecinde.
const {desktopCapturer} = require('electron')

desktopCapturer.getSources({types: ['window', 'screen']}, (error, sources) => {
  if (error) throw error
  for (let i = 0; i < sources.length; ++i) {
    if (sources[i].name === 'Electron') {
      navigator.mediaDevices.getUserMedia({
        audio: false,
        video: {
          mandatory: {
            chromeMediaSource: 'desktop',
            chromeMediaSourceId: sources[i].id,
            minWidth: 1280,
            maxWidth: 1280,
            minHeight: 720,
            maxHeight: 720
          }
        }
      }, handleStream, handleError)
      return
    }
  }
})

function handleStream (stream) {
  document.querySelector('video').src = URL.createObjectURL(stream)
}

function handleError (e) {
  console.log(e)
}
```

`desktopCapturer` tarafından sağlanan bir kaynaktan video yakalamak için [`navigator.mediaDevices.getUserMedia`]' a iletilen kısaltmalar `chromeMediaSource: 'desktop'` ve `audio: false` içermelidir.

Ses ve videoyu masaüstünün tamamından yakalamak için [`navigator.mediaDevices.getUserMedia`] içermelidir. `chromeMediaSource: 'desktop'`, her ikisi içinde `audio` and `video`, ancak yalnızca bir `chromeMediaSourceId`kısıtlama getirilmelidir.

```javascript
const constraints = {
  audio: {
    mandatory: {
      chromeMediaSource: 'desktop'
    }
  },
  video: {
    mandatory: {
      chromeMediaSource: 'desktop'
    }
  }
}
```

## Metodlar

`desktopCapturer` modülü aşağıdaki yöntemleri içerir:

### `desktopCapturer.getSources(options, callback)`

* `ayarlar` Nesne 
  * `types` Dizi[] - Yakalanacak masaüstü kaynaklarının türlerini listeleyen dizelerin bir dizisi, kullanılabilir türleri `screen` ve `window`' dir.
  * `thumbnailSize` [Size](structures/size.md) (İsteğe Bağlı) - Ortam kaynağı küçük resminin boyutlandırılacağı boyut. Varsayılan `150` x `150`.
* `geri arama` Fonksiyon 
  * `error` Hata 
  * `kaynaklar`[DesktopCapturerSource[]](structures/desktop-capturer-source.md)

Bittiğinde tüm mevcut masaüstü medya kaynakları hakkıda bilgi toplamaya başlar ve `callback(error, sources)`ı arar.

`sources` [`DesktopCapturerSource`](structures/desktop-capturer-source.md) nesnelerinin bir dizilişidir, her `DesktopCapturerSource` yakalanabilen bir ekran veya tek bir pencereyi temsil eder.
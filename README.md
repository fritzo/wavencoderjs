# WavEncoder #

WavEncoder is a fast cross-browser riff wave encoder for real-time audio synthesis in HTML5.
Works in Web Workers and in the main window
(in contrast to encoders using window.btoa).

Dual licensed under the [MIT](http://www.opensource.org/licenses/MIT)
or [GPL Version 2](http://www.opensource.org/licenses/GPL-2.0) licenses.

## Examples ##

To play 1 sec of noise in the main window, we can

    <script type='text/javascript' src='wavencoder.js'></script>
    <script type='text/javascript'>

      var samples = [];
      for (var t = 0; t < 22050; ++t) {
        samples[t] = 2 * Math.random() - 1;  // in the interval [-1,1]
      }

      var datauri = WavEncoder.encode(samples);
      var audio = new Audio(datauri);

      document.onload(function(){ audio.play(); });

    </script>

WavEncoder objects are optimized to create many samples of the same length.
A typical use case is to generate a set of tones, say in a web worker

    includeScripts('wavencoder.js');
    
    var sampleRateHz = 44100;
    var numSamples = 1 * sampleRateHz;                 // 1 sec
    var baseFreq = 2 * Math.PI * 27.5 / sampleRateHz;  // A0

    var wavEncoder = new WavEncoder(numSamples, {sampleRateHz: sampleRateHz});

    var tones = [];
    var samples = [];
    for (var n = 0; n < 88; ++n) {

      var freq = baseFreq * Math.pow(2, n/12);
      for (var t = 0; t < numSamples; ++t) {
        samples[t] = Math.sin(freq * t);
      }

      tones[n] = wavEncoder.encode(samples);
    }

    postMessage(tones);


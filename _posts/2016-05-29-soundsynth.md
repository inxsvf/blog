---
layout: blog
category: blog
published: true
title: Síntese de Som
---

A [Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API) é utilizada para o processamento e síntese de audio diretamente no browser.

Tal como na utilização do Canvas é necessaria a definição de um _contexto_, para a manipulação de som tambem será necessário um: [AudioContext](https://developer.mozilla.org/en/docs/Web/API/AudioContext). No entanto, recomenda-se a utilização só de um contexto audio por aplicação, pois é capaz de manipular diversas entradas de audio.

```javascript
var AudioContext = window.AudioContext || window.webkitAudioContext;
var audioCtx = new AudioContext();
```

Um conceito presente nesta API (e também comum a outras ferramentas de manipulação de audio) é o conceito de _nós_ e _audio routes_. Estes conceitos são um modo simples de representar as ligações entre uma fonte de som e um _destino_ (pex colunas), que, entre elas, pode existir outros _nós_ que podem aplicar efeitos sonoros ao som original ou outro tipo de manipulação.

```javascript
//Exemplo: criou-se um oscilador como fonte, que se ligou a um nó para 
//controlo do volume, e por fim ligou-se este nó ao nó final que 
//usualmente são as colunas do computador.
var oscillator = audioCtx.createOscillator();
var gainNode = audioCtx.createGain();
oscillator.connect(gainNode);
gainNode.connect(audioCtx.destination);
```

Para além do contexto, é necessário uma fonte de audio, pode ser:

* Gerada diretamente a partir de JavaScript (pex atraves de um nó audio como um oscilador);
* Criada a partir de dados [PCM](https://en.wikipedia.org/wiki/Pulse-code_modulation);
* Retirada de elementos HTML como `&lt;video>` ou `&lt;audio>`;
* A partir da webcam ou microfone do computador do utilizador ([WebRTC MediaStream](https://developer.mozilla.org/en-US/docs/Web/API/Media_Streams_API#LocalMediaStream)).

Para este exemplo, optou-se por utilizar um (oscilador)[https://en.wikibooks.org/wiki/Sound_Synthesis_Theory/Oscillators_and_Wavetables] para gerar um tom simples, que será ligado às colunas do computador, `context.destination`. Para poder ouvir o som, tem-se que se dar início ao oscilador. No exemplo seguinte, tambem se incluiu (na ultima linha) o pedido para o oscilador terminar ao fim de 3 segundos. 

```javascript
analyser = audioCtx.createAnalyser();
oscillator.connect(analyser);
analyser.connect(audioCtx.destination);
```

Neste exemplo tambem se pretende visualizar o sinal de audio, para isso vamos acrescentar um _nó_ ao nosso grafo: `AnalyserNode`. Este nó não modifica o sinal, mas permite utilizar esses dados. O _analyser_ extrai a frequência, onda, entre outros dados. 

```javascript
var oscillator = audioCtx.createOscillator();
var gainNode = audioCtx.createGain();
oscillator.connect(gainNode);
gainNode.connect(audioCtx.destination);
```
O nó _analyser_ captura os dados audio atraves da [Transformada de Fourier](https://en.wikipedia.org/wiki/Fast_Fourier_transform) a uma determinada frequência definida pela propriedade `AnalyserNode.fft` (por omissão 2048Hz). Existem diversos métodos para capturar diferentes tipos de dados, neste caso utilzou-se: `AnalyserNode.getByteTimeDomainData()`, devolve um array de inteiros (8bit), (Uint8Array)[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Typed_arrays].

```javascript
bufferLength = analyser.frequencyBinCount;
dataArray = new Uint8Array(bufferLength);
analyser.getByteTimeDomainData(dataArray);
```
Criou-se um _array_ Uint8Array, de tamanho determinado pelo `AnalyserNode.frequencyBinCount` e preencheu-se esse array com os dados lidos pelo _analyser_ atraves de `AnalyserNode.getByteTimeDomainData(dataArray)`.

Para o desenho da onda, sempre que se atualiza o desenho (chama-se a função `draw()`), tem de se atualizar os dados do `dataArray`, ou seja, tem que que se ler os dados que passam do oscilador para o _analyser_ atraves da função `AnalyserNode.getByteTimeDomainData(dataArray)`. Após essa atualização, tem de se percorrer todos esses valores, para atualizar o valor de `y` (o valor de `x` corresponde a uma divisão da largura do canvas em partes iguais).

Nota: na demonstração seguinte o oscilador termina ao fim de 20s.

<iframe id="frame_A_skeleton_template" src="/blog/snippets/02soundSynth.html" width="400" height="400" frameborder="0"></iframe>


```javascript
var canvas, ctx,
    oscillator, audioCtx, analyser,
    freq, waveType, bufferLength, dataArray,
    width = 300, height = 300, x, y;

function setup() {
    var canvas = document.getElementById('canvas');

    freq = 180;
    waveType = 'sine';

    ctx = canvas.getContext("2d");
    ctx.strokeStyle = 'rgb(0, 0, 255)';

    audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    analyser = audioCtx.createAnalyser();

    oscillator = audioCtx.createOscillator();
    oscillator.connect(analyser);
    analyser.connect(audioCtx.destination);
    
    bufferLength = analyser.frequencyBinCount;
    dataArray = new Uint8Array(bufferLength);
    analyser.getByteTimeDomainData(dataArray);

    oscillator.type = waveType;
    oscillator.frequency.value = freq;

    oscillator.start();
    draw();
}

function draw() {
    requestAnimationFrame(draw);

    analyser.getByteTimeDomainData(dataArray);

    ctx.clearRect(0, 0, width, height);
    ctx.beginPath();

    var sliceWidth = width / bufferLength;
    
    x = 0;
    y = (dataArray[0] / 128.0) * height/2;
    ctx.moveTo(x, y);
    
    for(var i = 1; i != bufferLength; i++){
        x += sliceWidth;
        y = (dataArray[i] / 128.0) * height/2;
        ctx.lineTo(x, y );
    }
    ctx.stroke();

}
```

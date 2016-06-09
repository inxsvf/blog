---
layout: blog
category: blog
published: true
title: Aceder à Câmara do Utilizador
---
Para poder aceder à câmara (microfone, e/ou ecrã) do utilizador usa-se a [navigator.getUserMedia API](https://developer.mozilla.org/en-US/docs/Web/API/Navigator/getUserMedia).
Note-se que por questões de segurança, esta API só funciona através do protocolo [HTTPS](https://en.wikipedia.org/wiki/HTTPS), e se o utilizador der autorização para captar imagem (para poder testar localmente ler [este artigo](/blog/simpleServer)).

#### Aceder à Câmara do Utilizador


<iframe id="frame_A_skeleton_template" src="{{site.baseurl}}/snippets/03camera.html" width="400" height="500" frameborder="0" style="margin: 20px;"></iframe>


```markup
<script>
  var ctx, canvas, video;

  function setup() {
    canvas = document.getElementById("canvas");
    ctx = canvas.getContext("2d");
    video = document.getElementById("video");

    var errBack = function(error) {
      console.log("Video capture error: ", error.code); 
    };

    navigator.getUserMedia = navigator.getUserMedia ||
                             navigator.webkitGetUserMedia ||
                             navigator.mozGetUserMedia ||
                             navigator.msGetUserMedia;


    if (navigator.getUserMedia) {
      navigator.getUserMedia({ "video": true }, function(stream) {
      	video.src = window.URL.createObjectURL(stream);
      }, errBack);
    } 
  }

  function snap(){
  	ctx.drawImage(video, 0, 0, 640, 480);
  }
</script>
...
<body onload="setup()">
	<video id="video" width="640" height="480" autoplay></video>
	<button id="snapBtn" onclick="snap()">Snap Photo</button>
	<canvas id="canvas" width="640" height="480"></canvas>
</body>
```

No início, colocaram-se as definições do costume, em que se acrescentou a do _video_, que corresponde ao elemento [_video_](https://developer.mozilla.org/en/docs/Web/HTML/Element/video) em que aparecerá o que se vê através da câmara do utilizador. Também definiu-se a função `errBack` para lidar com erros que apareçam ao aceder à câmara do utilizador, ou caso o utilizador rejeite a ligação.

Conforme o browser, a função `getUserMedia` pode não estar disponível (nos _browsers_ mais antigos) ou ter nome diferente, para resolver esse problema:


```JavaScript
navigator.getUserMedia = navigator.getUserMedia ||
	          navigator.webkitGetUserMedia ||
	          navigator.mozGetUserMedia ||
	          navigator.msGetUserMedia;

if (navigator.getUserMedia) {
	// Pode-se avançar
} else {
	alert('getUserMedia() is not supported in your browser');
}
```


Se se poder avançar, indica-se a componente a aceder, neste caso só ao à câmara, i.e. ao video: `{ "video": true }`, e ligamos a _stream_ da câmara do utilizador como fonte (_source_) do elemento `video` anterior:

```javascript
navigator.getUserMedia({ "video": true }, function(stream) {
				video.src = window.URL.createObjectURL(stream);
			}, errBack);
```

Note-se que na definição do elemento `video`, colocou-se como pré-definição "autoplay", para começar assim que tenha imagem.


#### Tirar uma Fotografia


Para tirar uma fotografia, criou-se um botão para _disparar_ cujo _handler_ utiliza a funcção `drawImage() para exibir um _frame_ do `video` para o _canvas_:


```javascript
function snap(){
	ctx.drawImage(video, 0, 0, 640, 480);
}
```

```markup
<button id="snapBtn" onclick="snap()">Snap Photo</button>
<canvas id="canvas" width="640" height="480"></canvas>
```

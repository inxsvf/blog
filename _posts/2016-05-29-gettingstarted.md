Primeiros Passos

O elemento `<canvas>` define uma região onde se pode “desenhar”. Esta zona pode ser acedida através, por exemplo, de _JavaScript_ que a partir de várias funções é possível a criação de gráficos, animações e outras composições.

Segue-se um _template_ que funcionará como ponto de partida. Para ficar consistente com a tecnologia de eleição da UC - _Processing_ - as funções principais, aqui, têm o mesmo nome.

```markup
<!DOCTYPE html>
<html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=utf-8"/>
		<title>Canvas Tutorial</title>
		<script type="application/javascript">
			function setup() {
				var canvas = document.getElementById('canvas');
				if(canvas.getContext){
					var ctx = canvas.getContext('2d');
					//drawing code here
				} else {
					//canvas-unsupported code here
				}
			}
		</script>
	</head>
	<body onload="setup()">
		<canvas id="canvas" width="600" height="600"></canvas>
	</body>
</html>
```

Em cima, colocou-se o elemento canvas com um tamanho predefinido (`width="300" height="300"` em _pixels_) no documento HTML, e registou-se o _handler_ `setup()` para que quando a pagina estiver carregada (`onload`) criar-se o (_canvas_) _context_ (`ctx`), a partir do qual criam-se os gráficos, quer em 2d ou 3D (WebGl), dentro do elemento _canvas_ associado.

#### Exemplo 1: Desenhar um quadrado


<iframe id="frame_A_skeleton_template" src="/snippets/00square.html" width="200" height="200" frameborder="0" style="margin: 20px;"></iframe>

```javascript
function setup() {
	var canvas = document.getElementById('canvas');
	var ctx = canvas.getContext("2d");

	//quadrado azul
	ctx.fillStyle = "rgb(0, 0, 255)";
	ctx.fillRect(10, 10, 60, 60);
}
```

O [_fillStyle_](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/fillStyle), é usado para definir a cor e/ou estilo das formas criadas: cor, gradiente (linear ou radial) ou padrão (uma imagem repetida). Aqui optou-se pela cor azul (`"rgb(0, 0, 255)"`), que também poderia ser definida como `"blue"` (ver [como definir cores](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value)).

De seguida, para desenhar rectângulos existem três opções, tendo-se optado pelo _fillRect_:

*   [`fillRect(x, y, width, height)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/fillRect) Um rectângulo preenchido, isto é, com _fill_ definido pelo _fillStyle_;
*   [`strokeRect(x, y, width, height)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/strokeRect) Para desenhar só o contorno de um rectângulo;
*   [`clearRect(x, y, width, height)`](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/clearRect)   Limpa a area definida pelo rectângulo.
(em que, x e y definem a posição do canto superior esquerdo do rectângulo e _width_ e _height_ definem respectivamente o comprimento e largura do rectângulo)

Tendo em conta que, normalmente, a origem (0,0) do sistema de coordenadas do canvas é no canto superior esquerdo desse elemento, e cada unidade corresponde a 1 pixel, desenhou-se um quadrado com largura e comprimento, _width_ = _height_ = 60_pixels_, com origem no ponto (x, y) = (10, 10).

<img src="https://mdn.mozillademos.org/files/224/Canvas_default_grid.png" alt="grid system" style="width: 220px;">

#### Exemplo 2: Quadrado em movimento

<iframe id="frame_A_skeleton_template" src="/snippets/01movingSquare.html" width="300" height="200" frameborder="0"></iframe>

```javascript
var interval,
	width = 240, height = 150,
	x = 0, y = 10;

function setup() {
	canvas = document.getElementById("canvas");
	canvas.width = width;
	canvas.height = height;
	ctx = canvas.getContext("2d");
	
	interval = setInterval(draw, 50);
}

function draw() {
	ctx.fillStyle = "rgba(255, 255, 255, 0.2)";
	ctx.fillRect (0, 0, width, height);

	ctx.fillStyle = "rgb(0, 0, 255)";
	ctx.fillRect (x, y, 60, 60);
	x += 5;
	if (x > width) x = 0;
}
```

No _canvas_, tal como no _Processing_, para criar a ilusão de movimento de um objecto, é necessário redesenha-lo na posição seguinte.

No _Processing_, a função `draw()` é uma função especial que é chamada de acordo com a _framerate_ definida. Aqui, para controlar a animação tem-se de se pedir ao _browser_ para chamar a função `draw()`. Dependendo da situação, pode-se usar:

*   [`setInterval(function, delay)`](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setInterval) Chama (varias vezes) a função function a cada delay ms.
*   [`setTimeout(function, delay)`](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setTimeout) Chama (uma vez) a função function ao fim de delay ms.
*   [`requestAnimationFrame(callback)`](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame) Informa o browser que se pretende fazer uma animação, e qual a função a chamar, callback, para atualizar essa animação.

Neste caso, utilizou-se `setInterval(draw, 50)`, que chama a função draw ao fim de 50ms (equivale a 20 fps).

A função `draw()` começa por limpar a area do canvas, mas como se pretende que o movimento deixe um rasto, definiu-se um valor para transparência. Caso não se desejasse este efeito, em vez da definição de cor e de desenho do rectângulo, bastava definir a propriedade `clearRect` com as dimensões do _canvas_.

De seguida, define-se a cor (azul) e desenha-se o rectângulo.

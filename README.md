# Juego
Antes de que podamos programar la parte funcional del juego, necesitamos crear la estructura básica de la página que lo va a contener. Podemos hacerlo utilizando HTML y el elemento  <canvas>. En el codigo se mostrara la estructura del documento HTML, que servira para visualizar todo el juego.
  <!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Gamedev Canvas Workshop</title>
    <style>
    	* { padding: 0; margin: 0; }
    	canvas { background: #eee; display: block; margin: 0 auto; }
    </style>
</head>
<body>

<canvas id="myCanvas" width="480" height="320"></canvas>

<script>
	// JavaScript code goes here
</script>

</body>
</html>

Para poder visualizar los graficos en el elemento <canvas> debemos hacer una referencia a él en javascript:
  var canvas = document.getElementById("myCanvas");
  var ctx = canvas.getContext("2d");
 En los siguientes fragmentos de codigo podremos visualizar una serie de figuras (Rectangulo y un Circulo)
  ctx.beginPath();
  ctx.rect(20, 40, 50, 50);
  ctx.fillStyle = "#FF0000";
  ctx.fill();
  ctx.closePath();
  
  ctx.beginPath();
  ctx.arc(240, 160, 20, 0, Math.PI*2, false);
  ctx.fillStyle = "green";
  ctx.fill();
  ctx.closePath();
  ASi concluiria nuestra primera leccion y nuestro codigo deberia verse asi
  <!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title>Gamedev Canvas Workshop - lesson 1: create the Canvas and draw on it</title>
    <style>* { padding: 0; margin: 0; } canvas { background: #eee; display: block; margin: 0 auto; }</style>
</head>
<body>

<canvas id="myCanvas" width="480" height="320"></canvas>

<script>
    var canvas = document.getElementById("myCanvas");
    var ctx = canvas.getContext("2d");
    ctx.beginPath();
    ctx.rect(20, 40, 50, 50);
    ctx.fillStyle = "#FF0000";
    ctx.fill();
    ctx.closePath();
    ctx.beginPath();
    ctx.arc(240, 160, 20, 0, Math.PI*2, false);
    ctx.fillStyle = "green";
    ctx.fill();
    ctx.closePath();
    ctx.beginPath();
    ctx.rect(160, 10, 100, 40);
    ctx.strokeStyle = "rgba(0, 0, 255, 0.5)";
    ctx.stroke();
    ctx.closePath();
</script>

</body>
</html>

 # LECCION 2
Esta leccion consiste en hacer mover el circulo ( se dibujo en la leccion anterior ), aunque mas que mover, es borrar y pintar de nuevo  en una posicion ligeramente diferente. Para hacer esto necesitamos definir una función de dibujo que se ejecutará una y otra vez,        cambiando una serie de variables para modificar la posición de cada personaje. Gracias a la naturaleza infinita de setInterval, la función draw () se llamará cada 10 milisegundos por siempre, o hasta que lo detengamos. Ahora, vamos a dibujar la bola.
 
function draw() {
    // código para dibujar
}
setInterval(draw, 10);

ctx.beginPath();
ctx.arc(50, 50, 10, 0, Math.PI*2);
ctx.fillStyle = "#0095DD";
ctx.fill();
ctx.closePath();

Pero esto solo nos muestra una bola que se esta dibujando una y otra vez en el mismo lugar, pero, no se nota el movimiento, Ahora en lugar de hacer que se dibuje siempre en la posicion (50, 50) definiremos un punto de inicio en las variables x e y, Aqui definiremos las variables x e y y seguido de esto actualizamos la funcion para usar dichas variables. Una parte importante es darle valores pequeños a x e y para que parezca que se esta moviendo, para esto definimos 2 valores dx y dy, Ahora hay que actualizar x e y con nuestras variables dx dy.
var x = canvas.width/2;
var y = canvas.height-30;

function draw() {
    ctx.beginPath();
    ctx.arc(x, y, 10, 0, Math.PI*2);
    ctx.fillStyle = "#0095DD";
    ctx.fill();
    ctx.closePath();
}

var dx = 2;
var dy = -2;

function draw() {
    ctx.beginPath();
    ctx.arc(x, y, 10, 0, Math.PI*2);
    ctx.fillStyle = "#0095DD";
    ctx.fill();
    ctx.closePath();
    x += dx;
    y += dy;
}

La bola está dejando un rastro porque estamos pintando un nuevo círculo en cada fotograma sin borrar el anterior: clearRect (). Este método tiene cuatro parámetros: las coordenadas x e y de la esquina superior izquierda de un rectángulo y las coordenadas x e y de la esquina inferior derecha de un rectángulo. En todo el área definida por ese rectángulo se borrará cualquier cosa que se haya pintado antes.
function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.beginPath();
    ctx.arc(x, y, 10, 0, Math.PI*2);
    ctx.fillStyle = "#0095DD";
    ctx.fill();
    ctx.closePath();
    x += dx;
    y += dy;
}

Asi concluye esta segunda leccion.

## LECCION 3
Es agradable ver nuestra bola moviéndose, pero desaparece rápidamente de la pantalla, limitando la diversión que podemos tener con ella. Ahora implementaremos una detección de colisión muy simple para hacer que la pelota rebote en los cuatro bordes del Canvas. Para detectar la colisión verificamos si la bola está tocando la pared y si es así, cambiaremos la dirección de su movimiento en consecuencia. Para facilitar los cálculos, definamos una variable llamada ballRadius que mantendrá el radio del círculo dibujado y se utilizará para los cálculos. Añade esto a tu código, en algún lugar por debajo de las declaraciones de variables existentes :var ballRadius = 10;
Ahora actualice la línea que dibuja la bola dentro de la funcion drawBall() a esto:
ctx.arc(x, y, ballRadius, 0, Math.PI*2);

Hay cuatro paredes para rebotar la pelota, vamos a centrarnos en la de arriba en primer lugar. Tendremos que comprobar, en cada fotograma, si la pelota está tocando el borde superior del Canvas, si es así, invertiremos el movimiento de la bola para que empiece a moverse en la dirección opuesta y se mantenga dentro de los límites visibles. Recordando que el sistema de coordenadas comienza desde la parte superior izquierda, podemos llegar a algo como esto:

if(y + dy < 0) {
    dy = -dy;
}
Si el valor de y de la posición de la bola es menor que cero, cambie la dirección del movimiento en el eje y, estableciéndolo igual a sí mismo, invertido. Si la pelota se movía hacia arriba con una velocidad de 2 píxeles por fotograma, ahora se moverá "arriba" con una velocidad de -2 píxeles, lo que en realidad equivale a bajar a una velocidad de 2 píxeles por fotograma.

if(y + dy > canvas.height) {
    dy = -dy;
}
Si la posición y de la pelota es mayor que la altura del canvas (recuerde que contamos los valores y desde la parte superior izquierda, de modo que el borde superior empieza en 0 y el borde inferior está en 480 píxeles, la altura del canvas), entonces rebota del borde inferior invirtiendo el movimiento del eje y como antes.
if(y + dy > canvas.height || y + dy < 0) {
    dy = -dy;
}
Si cualquiera de las dos afirmaciones es verdadera, invierte el movimiento de la pelota. Tenemos el borde superior e inferior cubiertos, así que pensemos en los de izquierda y derecha. Es muy similar en realidad, todo lo que tienes que hacer es repetir las declaraciones de x en lugar de y:

if(x + dx > canvas.width || x + dx < 0) {
    dx = -dx;
}

if(y + dy > canvas.height || y + dy < 0) {
    dy = -dy;
}
Prueba tu código en este punto, y te quedarás impresionado, ¡ahora tenemos una pelota que rebotó en los cuatro bordes del canvas! Pero tenemos otro problema sin embargo, cuando la bola golpea cada pared se hunde en ella levemente antes de cambiar la dirección. Esto es porque estamos calculando el punto de colisión de la pared y el centro de la bola, mientras que deberíamos hacerlo por su circunferencia. La bola debe rebotar justo después de que toca la pared.

if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
    dx = -dx;
}
if(y + dy > canvas.height-ballRadius || y + dy < ballRadius) {
    dy = -dy;
}
Cuando la distancia entre el centro de la bola y el borde de la pared es exactamente igual que el radio de la pelota, cambiará la dirección del movimiento. Restando el radio de un ancho del eje y añadiéndolo al otro nos da la impresión de una adecuada detección de colisiones, la pelota rebota de las paredes como debería hacerlo.

## LECCION 4
La bola está rebotando en las paredes libremente y puedes estar mirándola toda la vida, pero, ahora mismo, no hay interactividad. ¡No es un juego si no puedes controlarlo! Vamos a añadirle la interacción del usuario: una paleta. Necesitamos una paleta para golpear la bola. Empezamos por definir variables para conseguirlo. Añade las variables siguientes en la parte de arriba de tu código, junto a las que ya tenías:

var paddleHeight = 10;
var paddleWidth = 75;
var paddleX = (canvas.width-paddleWidth)/2;
paddleHeight servirá para definir la altura de la paleta, paddleWidth la anchura y paddleX la posición en el eje X en la que empieza a dibujarse. Definimos una función que dibujará la paleta en la pantalla. Añade este código justo después de la función drawBall():

function drawPaddle() {
    ctx.beginPath();
    ctx.rect(paddleX, canvas.height-paddleHeight, paddleWidth, paddleHeight);
    ctx.fillStyle = "#0095DD";
    ctx.fill();
    ctx.closePath();
}
Podemos dibujar la paleta donde queramos, pero debería responder a las acciones del usuario. Ha llegado la hora de implementar algunos controles de teclado. Vamos a necesitar: Dos variables para guardar la información sobre si se ha pulsado el botón izquierdo o el derecho. Dos funciones (event listeners) que respondan a los eventos keydown y keyup (pulsar tecla, liberar tecla). Queremos que se ejecute algún código para manejar la paleta cuando se pulsen los botones. Dos funciones que manejen los eventos keydown y keyup que se ejecutarán cuando se pulsen los botones. La habilidad de mover la paleta a la izquierda y a la derecha Empezaremos por definir las variables que nos dirán si se ha pulsado un botón. 

var rightPressed = false;
var leftPressed = false;

Las dos las inicializamos con el valor false porque al principio no están pulsados los botones. Para "escuchar" las pulsaciones de las teclas necesitamos definir dos "escuchadores de eventos" (event listeners). Cuando ocurra el evento keydown al pulsar cualquier tecla del teclado, la función keyDownHandler() se ejecutará. Cuando se liberará la tecla pulsada, se ejecutará la función keyUpHandler().
function keyDownHandler(e) {
    if(e.keyCode == 39) {
        rightPressed = true;
    }
    else if(e.keyCode == 37) {
        leftPressed = true;
    }
}

function keyUpHandler(e) {
    if(e.keyCode == 39) {
        rightPressed = false;
    }
    else if(e.keyCode == 37) {
        leftPressed = false;
    }
}
Ya tenemos las variables que contienen la información sobre las teclas pulsadas, los escuchadores de eventos y las funciones relevantes. Ahora vamos a ocuparnos del código que utilizará todo eso y moverá la paleta en la pantalla. Dentro de la función draw() comprobaremos si está pulsada la flecha izquierda o la derecha cada vez que se dibuje un fotograma. Nuestro código podría tener este aspecto:

if(rightPressed) {
    paddleX += 7;
}
else if(leftPressed) {
    paddleX -= 7;
}

if(rightPressed && paddleX < canvas.width-paddleWidth) {
    paddleX += 7;
}
else if(leftPressed && paddleX > 0) {
    paddleX -= 7;
}
drawPaddle();
## LECCION 5
Es divertido ver la bola rebotando en las paredes y ser capaz de mover la pala pero, aparte de eso, el juego no hace nada y no tiene ningún progreso ni un objetivo final. Sería bueno desde el punto de vista del juego poder perder. La lógica asociada a perder en este juego es fácil de entender: si se te escapa la bola y alcanza el borde inferior de la pantalla, pierdes y se acabó el juego. Intentemos implementar el final del juego en nuestro juego. Aquí está el trozo de código de la tercera lección en donde hicimos que la pelota rebotara en las paredes:

if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
    dx = -dx;
}

if(y + dy > canvas.height-ballRadius || y + dy < ballRadius) {
    dy = -dy;
}
En lugar de dejar que la pelota rebote en las cuatro paredes, vamos a permitir que lo haga sólo en tres: izquierda, arriba y derecha. Alcanzar la pared inferior supondrá el fin del juego. Editaremos el segundo bloque if y lo convertiremos en un "if else" que activará el estado de "final de juego" cuando la pelota colisione con el borde inferior del terreno de juego. Por ahora nos conformaremos con mostrar un mensaje con la función alert() y con reiniciar el juego volviendo a cargar la página. Modifica el segundo if para que quede así:

if(y + dy < ballRadius) {
    dy = -dy;
} else if(y + dy > canvas.height-ballRadius) {
    alert("GAME OVER");
    document.location.reload();
}

Para terminar esta lección sólo nos falta detectar la colisión de la bola y la paleta para que pueda rebotar, volviendo hacia la zona de juego. La manera más sencilla de hacerlo es comprobar si el centro de la bola está entre los límites izquierdo y derecho de la paleta. Actualiza el último fragmento del código, el "if else" de antes, para que te quede así:

if(y + dy < ballRadius) {
    dy = -dy;
} else if(y + dy > canvas.height-ballRadius) {
    if(x > paddleX && x < paddleX + paddleWidth) {
        dy = -dy;
    }
    else {
        alert("GAME OVER");
        document.location.reload();
    }
}
Si la bola toca el borde inferior del lienzo (Canvas) debemos comprobar si golpea la pala. Si es así, entonces rebota como el jugador se imagina que va a ocurrir; si no, el juego ha terminado.

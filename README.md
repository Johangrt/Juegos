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
## LECCION 6
Hemos cambiado la mecánica del juego y ahora ya podemos perder. Esto es genial porque significa que el juego finalmente se comporta como un juego. Sin embargo, pronto resultará aburrido si todo lo que puedes conseguir es hacer rebotar la pelota en las paredes y en la pala. Lo que el juego necesitamos es romper ladrillos con la bola. Ahora vamos a dibujar los ladrillos. El propósito principal de esta lección consiste en escribir unas pocas líneas de código para los ladrillos, utilizando un bucle anidado que recorra una matriz bidimensional. Pero, antes, necesitamos preparar unas variables que definan la información sobre los ladrillos, como su ancho y alto, filas y columnas, etc. Añade estas líneas a tu programa, debajo de las otras variables que has definido antes:

var brickRowCount = 3;
var brickColumnCount = 5;
var brickWidth = 75;
var brickHeight = 20;
var brickPadding = 10;
var brickOffsetTop = 30;
var brickOffsetLeft = 30;
Aquí hemos definido el número de filas (Row) y columnas (Column) de ladrillos, su ancho (Width) y alto (Height), el hueco entre los ladrillos para que no se toquen (Padding), y un margen superior (Top) e izquierdo (Left) para que no se dibujen tocando los bordes. Guardaremos nuestros ladrillos en una matriz bidimensional que contendrá las columnas (c) de los ladrillos. Cada columna contendrá, a su vez, toda la fila (r) de ladrillos. Cada ladrillo se va a representar con un objeto con las posiciones "x" e "y" en las que se dibujará.
var bricks = [];
for(c=0; c<brickColumnCount; c++) {
    bricks[c] = [];
    for(r=0; r<brickRowCount; r++) {
        bricks[c][r] = { x: 0, y: 0 };
    }
}
El código anterior pasará por las filas y las columnas y creará los ladrillos. TEN EN CUENTA que esos objetos que representan a los ladrillos también se utilizarán para detectar colisiones más adelante. Por si no lo terminas de entender... bricks[0][0] es el primer ladrillo (columna 0, fila 0) y se dibujará en "x" 0 e "y" 0. El siguiente ladrillo será el brick[0][1] (columna 0, fila 1) y se dibujará también en (0,0). Así, continuaremos hasta el final de la primera columna, que será el ladrillo bricks[0][2] porque hay 3 filas, de la 0 a la 2. Terminará así el bucle de dentro y seguirá el de fuera, valiendo ahora la "c" 1. Seguiremos recorriendo bricks[] hasta llegar a bricks[2][4], que es el último ladrillo.

function drawBricks() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            bricks[c][r].x = 0;
            bricks[c][r].y = 0;
            ctx.beginPath();
            ctx.rect(0, 0, brickWidth, brickHeight);
            ctx.fillStyle = "#0095DD";
            ctx.fill();
            ctx.closePath();
        }
    }
}
Viene a ser lo mismo de antes, sólo que hemos añadido ctx.rect() para dibujar un rectángulo por cada ladrillo, además de otras llamadas a funciones para que, efectivamente, se dibuje el rectángulo. Cada ladrillo se dibujará en la posición (0, 0), tendrá un ancho brickWidth y un alto de brickHeight. Estupendo pero... ¡estamos dibujando todos los ladrillos en el mismo sitio! ¡Eso no puede ser! Vamos a calcular en qué posición "x" e "y" se tiene que dibujar cada ladrillo así:

var brickX = (c*(brickWidth+brickPadding))+brickOffsetLeft;
var brickY = (r*(brickHeight+brickPadding))+brickOffsetTop;
El primer ladrillo se dibujará arriba a la izquierda, concretamente en (brickoffsetLeft, brickOffsetTop), porque c y r valen 0. El siguiente ladrillo (columna 0, fila 1) se dibujará más abajo. Intenta hacer tú mismo los cálculos y verás cómo cada ladrillo de la misma columna se dibujará más abajo o más arriba según en qué fila se encuentre. También verás cómo cada ladrillo de la misma fila se dibujará más a la izquierda o a la derecha según en qué columna se encuentre.

function drawBricks() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            var brickX = (c*(brickWidth+brickPadding))+brickOffsetLeft;
            var brickY = (r*(brickHeight+brickPadding))+brickOffsetTop;
            bricks[c][r].x = brickX;
            bricks[c][r].y = brickY;
            ctx.beginPath();
            ctx.rect(brickX, brickY, brickWidth, brickHeight);
            ctx.fillStyle = "#0095DD";
            ctx.fill();
            ctx.closePath();
        }
    }
}
## LECCION 7
Ya tenemos los ladrillos en la pantalla pero el juego todavía no es muy interesante, porque la bola los atraviesa. Tenemos que pensar una manera de detectar colisiones para que la bola pueda rebotar en los ladrillos y romperlos. Nosotros decidimos cómo implementar esto, por supuesto, pero puede ser complicado calcular si la bola está tocando el rectángulo o no, porque no hay funciones del <canvas> que sirvan para saberlo. En este tutorial vamos a hacerlo de la manera más fácil que existe: comprobaremos si el centro de la bola está tocando (colisionando) con cualquiera de los ladrillos. No siempre funcionará a la perfección y hay formas de detectar colisiones más sofisticadas que funcionan mejor, pero no nos interesa verlas porque estamos aprendiendo y tenemos que hacer las cosas fáciles. Una función para detectar colisionesSection Para lograr nuestro objetivo vamos a definir una función que, con un bucle, recorrerá todos los ladrillos y comparará la posición de cada uno con la posición de la bola, cada vez que se dibuje un fotograma. Para que el código sea más legible definiremos la variable "b" que almacenará el objeto ladrillo en cada vuelta de bucle:

function collisionDetection() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            var b = bricks[c][r];
            // calculations
        }
    }
}
Si el centro de la bola está dentro de las coordenadas de uno de nuestros ladrillos, cambiaremos la dirección de la bola. Se cumplirá que el centro de la bola está dentro de ladrillo si se cumplen al mismo tiempo estas cuatro condiciones:
La posición "x" de la bola es mayor que la posición "x" del ladrillo
La posición "x" de la bola es menor que la posición del ladrillo más el ancho del ladrillo
La posición "y" de la bola es mayor que la posición "y" del ladrillo.
La posición "y" de la bola es menor que la posición del ladrillo más su altura.
Traducimos esto a JavaScript:

function collisionDetection() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            var b = bricks[c][r];
            if(x > b.x && x < b.x+brickWidth && y > b.y && y < b.y+brickHeight) {
                dy = -dy;
            }
        }
    }
}
Hacer que los ladrillos desaparezcan cuando reciben un golpeSection
El código anterior funcionará correctamente y la bola cambiará de dirección. El problema es que los ladrillos siguen donde están. Tenemos que imaginar una forma de ocuparnos de los que ya hemos golpeado con la bola. Podemos hacerlo añadiendo un parámetro extra para indicar si queremos pintar cada ladrillo en la pantalla o no. En la parte del código donde inicializamos los ladrillos, añadiremos una propiedad status a cada ladrillo. Cambia tu código fijándote en la línea que está resaltada:

var bricks = [];
for(c=0; c<brickColumnCount; c++) {
    bricks[c] = [];
    for(r=0; r<brickRowCount; r++) {
        bricks[c][r] = { x: 0, y: 0, status: 1 };
    }
} 
A continuación consultaremos el "status" de cada ladrillo para saber si lo tenemos que dibujar o no. Si "status" vale 1, lo dibujaremos. Si vale 0, no lo dibujaremos porque habrá sido golpeado por la bola. Actualiza tu función drawBricks() para que quede así:

function drawBricks() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            if(bricks[c][r].status == 1) {
                var brickX = (c*(brickWidth+brickPadding))+brickOffsetLeft;
                var brickY = (r*(brickHeight+brickPadding))+brickOffsetTop;
                bricks[c][r].x = brickX;
                bricks[c][r].y = brickY;
                ctx.beginPath();
                ctx.rect(brickX, brickY, brickWidth, brickHeight);
                ctx.fillStyle = "#0095DD";
                ctx.fill();
                ctx.closePath();
            }
        }
    }
} 
 
Ahora tenemos que ocuparnos del valor de "status" en la función collisionDetection(): si el ladrillo está activo (status 1) comrpobaremos si hay colisión. Si hay colisión, pondremos el "status" de ese ladrillo a 0 para no volver a pintarlo.
function collisionDetection() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            var b = bricks[c][r];
            if(b.status == 1) {
                if(x > b.x && x < b.x+brickWidth && y > b.y && y < b.y+brickHeight) {
                    dy = -dy;
                    b.status = 0;
                }
            }
        }
    }
}
## LECCION 8
Si puedes ver el contador mientras juegas, puede que consigas impresionar a tus amigos. Necesitas una variable para guardar el contador. Añade esto a tu JavaScript, después de las otras definiciones de variables:
var score = 0;
También necesitas una función drawScore() para enseñar el contador por pantalla. Añade esto después de la función collisionDetection():

function drawScore() {
    ctx.font = "16px Arial";
    ctx.fillStyle = "#0095DD";
    ctx.fillText("Score: "+score, 8, 20);
}
Dibujar texto en el <canvas> es similar a dibujar un círculo o cualquier otra figura. La definición del tipo de letra (fuente) se hace igual que en CSS, puedes fijar el tamaño y fuente con el método font() method. Despúes utilizas fillStyle() para fijar el color y fillText() para escribir el texto y el lugar en el que se va a dibujar. El primer parámetro es el texto en si y los otros dos son las coordenadas.
function collisionDetection() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            var b = bricks[c][r];
            if(b.status == 1) {
                if(x > b.x && x < b.x+brickWidth && y > b.y && y < b.y+brickHeight) {
                    dy = -dy;
                    b.status = 0;
                    score++;
                }
            }
        }
    }
} 
drawScore();
Mostrar un mensaje de victoria cuando se hayan destruido todos los ladrillosSection
Lo de sumar puntos funciona, pero tiene un final. ¿Qué ocurrirá cuando no queden ladrillos? Precisamente ese es el principal objetivo del juego, tendrás que dibujar un mensaje de victoria. 
function collisionDetection() {
    for(c=0; c<brickColumnCount; c++) {
        for(r=0; r<brickRowCount; r++) {
            var b = bricks[c][r];
            if(b.status == 1) {
                if(x > b.x && x < b.x+brickWidth && y > b.y && y < b.y+brickHeight) {
                    dy = -dy;
                    b.status = 0;
                    score++;
                    if(score == brickRowCount*brickColumnCount) {
                        alert("YOU WIN, CONGRATULATIONS!");
                        document.location.reload();
                    }
                }
            }
        }
    }
}  
Gracias a esto, los jugadores pueden ganar cuando rompen todos los ladrillos, que es muy importante. La función document.location.reload() vuelve a cargar la página y el juego empieza de nuevo, una vez se hace clic sobre el botón del alert().
## LECCION 9
document.addEventListener("mousemove", mouseMoveHandler, false);
 Podemos cambiar la posición de la pala basándonos en las coordenadas del puntero del ratón. Eso es lo que hace la función siguiente
function mouseMoveHandler(e) {
    var relativeX = e.clientX - canvas.offsetLeft;
    if(relativeX > 0 && relativeX < canvas.width) {
        paddleX = relativeX - paddleWidth/2;
    }
}
En esta función calculamos un valor relativeX, que es la posición horizontal del ratón en el "viewport" (e.clientX), menos la distancia entre el borde izquierdo del terreno de juego y el borde izquierdo del "viewport" (canvas.offsetLeft). Si el valor resultante es mayor que cero y menor que el ancho del terreno de juego, es que el ratón está dentro de los límites, y calculamos la posición de la paleta poniéndole el valor relativeX menos la mitad del ancho de la paleta, para que el movimiento sea de verdad relativo a la mitad de la paleta. Ahora, la paleta seguirá la posición del cursor del ratón pero, como restringimos el movimiento al <canvas>, no desaparecerá completamente por los lados.
## LECCION 10
	
Siempre es posible mejorar cualquier juego que hagamos. Por ejemplo, podemos dar vidas al jugador. Así, aunque pierda la bola una o dos veces, todavía puede intentar derribar todo el muro. También podemos mejorar los aspectos gráficos. Dar vidas es bastante sencillo. Primero, añade una variable para guardar el número de vidas que tiene en cada momento.
var lives = 3;
Mostrar por pantalla el número de vidas es prácticamente lo mismo que mostrar el contador de puntos. Añade la función siguiente detrás de la función drawScore():

function drawLives() {
    ctx.font = "16px Arial";
    ctx.fillStyle = "#0095DD";
    ctx.fillText("Lives: "+lives, canvas.width-65, 20);
}
En lugar de terminar el juego inmediatamente, restaremos una vida hasta que ya no quede ninguna. También podemos colocar la bola y la paleta en la posición inicial cuando el jugador empiece con la vida siguiente. En la función draw().
alert("GAME OVER");
document.location.reload();
... por estas otras:
lives--;
if(!lives) {
    alert("GAME OVER");
    document.location.reload();
}
else {
    x = canvas.width/2;
    y = canvas.height-30;
    dx = 2;
    dy = -2;
    paddleX = (canvas.width-paddleWidth)/2;
}
Ahora, cuando la bola toca el fondo, restamos una vida. Si no queda ninguna, el jugador pierde y termina la partida. Si queda alguna, entonces colocamos la bola y la paleta en el centro, y hacemos que la bola vaya en la nueva dirección correcta y a la velocidad inicial. drawLives(); Ahora vamos a ocuparnos de algo que no es particular de este juego, sino de la forma en la que se muestran las imágenes en pantalla. requestAnimationFrame ayuda al navegador a refrescar la imagen mejor que con el método setInterval() que estamos utilizando.
setInterval(draw, 10);
draw(); 
Y, ahora, al final de la función draw(), justo antes de la llave que la cierra, añade la línea siguiente, que hará que la función draw() se llame a sí misma una y otra vez: Ahora draw() se ejecuta una y otra vez con un bucle requestAnimationFrame() pero, en lugar de hacerlo cada 10 milisegundos, dejamos que sea el navegadro quien decida cada cuánto tiempo. El navegador sincronizará el refresco, es decir, el número de fotogramas por segundo, a lo que sea capaz la máquina que está ejecutando el juego. De este modo la animación será más eficiente y más suave que el viejo método setInterval().

---
title: Robocode Tank Royale.
language: ES
author: www.martinezpenya.es
subject: Modelos de Inteligencia Artificial
keywords: [MIA, 2023, Modelos de Inteligencia Artificial, Curso Especialización IA-BD]
IES: www.ieseduardoprimo.es
header: ${title} - ${subject} (ver. ${today}) 
footer:${currentFileName}.pdf - ${author} - ${IES} - ${pageNo}/${pageCount}
typora-root-url:${filename}/../
typora-copy-images-to:${filename}/../assets
---
[toc]

# Preparación del entorno

* Al menos java 11 (Yo estoy usando Java 19 y la versión 0.19.3 de Tank Royale)

  ```sh
  java -version
  ```

* Descargar la última versión desde releases: https://github.com/robocode-dev/tank-royale/releases

* Ejecutar el `jar` descargado:

  ```sh
  java -jar robocode-tankroyale-gui-x.y.z.jar
  ```

* Descarga y descomprime los Bots de ejemplo: https://github.com/robocode-dev/tank-royale/releases

* Configura la gui para acceder a la carpeta de robots: `Config` -> `Bot Root Directories` (del menú)

* [opcional] Añadir sonidos al gui. Descarga y descomprime la carpeta `sounds.zip` de: https://github.com/robocode-dev/sounds/releases

  ```sh
  [directorio padre]
  ├── robocode-tankroyale-gui-x.y.z.jar
  └── sounds/  <-- carpeta de sonidos
      ├── bots_collision.wav
      ...
      └── wall_collision.wav   
  ```

* Necesitaras IntelliJ para poner todo en funcionamiento y para programar tu Bot.

> ## Juega un poco por tu cuenta con el entorno GUI, explora las opciones, tipos de batallas, crea alguna ronda de las mismas, inicializa Bots, añádelos, juega con la velocidad de juego, etc.

# Mi primer Bot

## Proyecto IntelliJ

Para acelerar el proceso, he preparado un proyecto en IntelliJ con toda la arquitectura básica que necesitará tu Bot. Descarga y descomprime esta [carpeta](https://raw.githubusercontent.com/martinezpenya/MIA-IABD-2324/master/UD01/assets/IABDBot.zip) y abre el proyecto con el IDE IntelliJ.

También necesitaras la libreria (API) de Robocode Tank Royale que puedes descargar desde aquí: https://github.com/robocode-dev/tank-royale/releases. Descarga el archivo: `robocode-tankroyale-bot-api-x.y.z.jar`

La estructura debería quedar de la siguiente manera:

```sh
[directorio padre]
├── lib
|	└──robocode-tankroyale-gui-x.y.z.jar
└── IABDBot  <-- Proyecto de IntelliJ
    ├── src
    ...
    └── IABDBot.iml
```

> ### Asegurate de entender el funcionamiento del Bot IABDBot, tiene comentarios donde se explican partes importante del Bot y que daremos por conocidas en los siguientes puntos.
>
> Es muy importante que entiendas la diferencia entre movimientos bloqueantes y simultáneos, así como las condiciones y los eventos disponibles.

## Configuración del servidor (GUI) y el proyecto IntelliJ para ejecutar en local o remoto

El servidor permite conexiones en remoto/local a través de un password que se genera automáticamente la primera vez que lanzas la GUI. Este password lo puedes encontrar/modificar en el archivo `server.properties`, en el ejemplo siguiente la clave de acceso es **CEIABDEPM2023**

```sh
bots-secrets=CEIABDEPM2023
[...]
```

Otra información que necesitaras será la IP del servidor al que quieres conectar, normalmente será `localhost` (tu host local), y cuando sea necesario hacer pruebas el profesor te facilitará su IP.

Ahora solo queda configurar nuestro proyecto de IntelliJ para configurar estas variables en el momento de ejecutar el Bot. Accede a la configuración de las ejecuciones en la parte superior derecha (una vez abierta la clase `IABDBot.java)`:

<img src="/assets/EditConfigurationsIntelliJ.png" alt="EditConfigurationsIntelliJ" style="zoom:50%;" />

Puedes ver que en el apartado `Environment Variables` tienes el siguiente valor:

```sh
SERVER_SECRET=CEIABDEPM2023;SERVER_URL=ws://localhost:7654
```

Como puedes imaginar **SERVER_SECRET** hace referencia a la clave del servidor, y **SERVER_URL** a la dirección del servidor y es donde deberás reemplazar (según el caso) localhost por la IP que te indique el profesor. Así podrás ejecutar tu Bot directamente desde IntelliJ y aparecerá en el Servidor para poder añadirlo a las batallas. 

> ### Ojo, el servidor debe estar inicializado antes de lanzar el bot de lo contrario obendrás un error similar a este:
>
> ```sh
> Exception in thread "main" dev.robocode.tankroyale.botapi.BotException: Could not create web socket for URL: ws://localhost:7654
> 	at dev.robocode.tankroyale.botapi.internal.BaseBotInternals.connect(BaseBotInternals.java:268)
> 	at dev.robocode.tankroyale.botapi.internal.BaseBotInternals.start(BaseBotInternals.java:254)
> 	at dev.robocode.tankroyale.botapi.BaseBot.start(BaseBot.java:114)
> 	at IABDBot.main(IABDBot.java:11)
> 
> Process finished with exit code 1
> ```

Si el Bot encuentra el servidor y la contraseña es correcta deberia aparecer esto al inicializarlo en IntelliJ:

```sh
Connected to: ws://localhost:7654
```

> ## Prueba por tu cuenta mezclando en las batallas Bots de ejemplo, con tu Bot o incluso el de algún compañero/a.

# ¿Cómo mejoro mi Bot?

Dividiremos todo lo que debemos conocer en 4 apartados:

## Conociendo el campo de batalla

Sigue atentamente la documentación de RCTR sobre la [**anatomía** de los Bots](https://robocode-dev.github.io/tank-royale/articles/anatomy.html)

Puntos importantes:

* Si el radar no se mueve, no detecta
* Inicialmente el robot, el cañón y el radar se mueven conjuntamente (pero se puede cambiar)
* El Bot se simplifica a un cuadrado de 36x36 unidades.
* Para las colisiones (con Bots o paredes) se simula como un círculo de 18 unidades de radio.

A continuación revisa las **[coordenadas y ángulos](https://robocode-dev.github.io/tank-royale/articles/coordinates-and-angles.html)**

Puntos importantes:

- Este apartado es totalmente diferente al de Robocode Original.

Estudia también las [**físicas**](https://robocode-dev.github.io/tank-royale/articles/physics.html)

Puntos importantes:

- Se frena más rápido que se acelera.
- Cuanto más rápido vas, más lento giras.
- El cañón gira máximo 20º por turno.
- El radar gira máximo 45º por turno.
- La potencia de tiro influye en el daño provocado y los puntos conseguidos, pero también en el calentamiento del cañón.
- Inicialmente el cañón está caliente (3 unidades), al principio has de esperar a que se enfrie para disparar.
- El atropello da más puntos que los disparos (pero te resta energía)
- Si chocas con una pared, pierdes puntos.
- La energía que te sobra en una ronda no da puntos (modo kamikaze con el último robot?)

Por último te en cuenta las [**puntuaciones**](https://robocode-dev.github.io/tank-royale/articles/scoring.html)

Puntos importantes:

- Consigues puntos si:
  - tu disparo golpea a otro Bot (sino, te resta)
  - eres el que mata al Bot
  - cada vez que otro Bot muere, pero tu sigues vivo
  - eres el último robot vivo
  - atropellas a otro Bot
  - si matas por atropello a otro Bot
- El último Bot que quede vivo no tiene porqué ser el ganador.

### Eventos própios

Definimos una condición, y cuando esta se cumple se dispara un evento:

```java
    // Esta condición se dispara cuando el bot completa su giro
    public static class TurnCompleteCondition extends Condition {

        private final IBot bot;

        public TurnCompleteCondition(IBot bot) {
            this.bot = bot;
        }

        @Override
        public boolean test() {
            // El método test() es el que debemos reescribir para definir el resultado de nuestra condición.
            return bot.getTurnRemaining() == 0;
        }
    }
```

Podriamos usar esta condición en un fragmento similar a este:

```java
waitFor(new TurnCompleteCondition());
```

Podriamos usar funciones Lambda de la siguiente manera:

```java
waitFor(new Condition(() -> getTurnRemaining() == 0);
```

El resultado de los dos fragmentos seria el mismo.

## Personalizar mi Bot

Podemos establecer colores para el cuerpo, torreta de cañón, el radar, el arco de escaneo y de la bala:

```java
Color kaki = Color.fromString("#febe56");
setBodyColor(kaki);
setTurretColor(kaki);
setRadarColor(kaki);
setScanColor(kaki);
setBulletColor(kaki);
```

## Técnicas de escaneo (Radar)

Sentidos de nuestro Bot:

Sentido del **tacto**, tu Bot sabe cuando:

- golpea una pared (`onHitWall`),
- es alcanzado por un disparo (`onHitByBullet`),
- es alcanzado por otro Bot (`onHitBot`). 

Sentido de la **vista**, tu Bot sabe cuándo ha visto otro robot, pero sólo si lo escanea (`onScannedBot`)

**Otros** sentidos, tu robot también sabe cuándo ha muerto (`onDeath`), cuándo ha muerto otro robot (`onRobotDeath`).

Además también es consciente de sus balas y sabe cuando una bala ha sido disparada (`onBulletFired`) ha alcanzado a un oponente (`onBulletHit`), cuando una bala golpea una pared (`onBulletWall`) o cuando una bala golpea a otra bala (`onBulletHitBullet`).

Configurar correctamente tu Bot con:

```java
setAdjustRadarForBodyTurn(true);//true if the radar must adjust/compensate for the body's turn;
								//false if the radar must turn with the body turning (default).
setAdjustGunForBodyTurn(true);  //true if the gun must adjust/compensate for the bot's turn;
							    //false if the gun must turn with the bot's turn.
setAdjustRadarForGunTurn(true); //true if the radar must adjust/compensate for the gun's turn;
								//false if the radar must turn with the gun's turn.
```

Que el radar siempre de vueltas:

```java
setTurnRadarLeft(Double.POSITIVE_INFINITY); //degrees - is the amount of degrees to turn left. If negative, the radar will turn right.
```

Revisa el API (`rescan()` i `setRescan()`)

Fijar el radar en un enemigo (one on one radar):

- Escaneo con multiplicador

  ```java
  public void onScannedBot(ScannedBotEvent e) {
      double factor=1.9;
      setTurnRadarLeft(factor * radarBearingTo(e.getX(), e.getY()));
  }
  ```

  Segun el factor:

  - `1.0` - Bloqueo de radar delgado. Debe llamar a scan() para evitar perder el bloqueo. Que Dios te ayude si alguna vez te saltas un turno.
  - `1.9` - El arco del radar comienza amplio y se estrecha lentamente tanto como sea posible mientras se mantiene en el objetivo.
  - `2.0`: el arco del radar recorre un ángulo fijo. El ángulo exacto elegido depende de las posiciones del enemigo y del radar cuando se detecta al enemigo por primera vez. El ángulo se incrementará si es necesario para mantener el bloqueo.

- Arco de radar Ancho

  ```java
  public void onScannedBot(ScannedBotEvent e) {
      //set the wide to add to the scan.
      double wide=36.0;
      // Absolute angle towards target
      double angleToEnemy = radarBearingTo(e.getX(), e.getY());
      // Distance we want to scan from middle of enemy to either side
      // The 36.0 is how many units from the center of the enemy robot it scans.
      double extraTurn = Math.min(Math.toDegrees(Math.atan(36.0 / distanceTo(e.getX(), e.getY()))), getMaxRadarTurnRate());
  
      // Adjust the radar turn so it goes that much further in the direction it is going to turn
      // Basically if we were going to turn it left, turn it even more left, if right, turn more right.
      // This allows us to overshoot our enemy so that we get a good sweep that will not slip.
      if (angleToEnemy < 0)
          angleToEnemy -= extraTurn;
      else
          angleToEnemy += extraTurn;
  
      //Turn the radar
      setTurnRadarLeft(angleToEnemy);
  }
  ```

- Radar Oscilante (variable global que sepa la dirección y nos ayude a cambiarla de vez en cuando)

- Escaneo más inteligente (seguir al cercano? según la situación?) ...

- Nos interesa mantener una lista de enemigos? `e.getScannedBotId()`? y por ejemplo fijar el radar en el más débil?...

## Técnicas de desplazamiento (Movimiento)

### Pensamiento lateral

Si has jugado baloncesto antes, sabes que si quieres defender a alguien que sostiene el balón, debes maximizar tu movimiento lateral enfrentándote siempre a él (de frente). Lo mismo ocurre con tu robot.

Para conseguir esta posición debemos hacer algo similar a esto:

```java
turnLeft(bearingTo(e.getX(), e.getY()) + 90);
```

que siempre colocará tu robot perpendicular (90 grados) a tu enemigo.

### ¿Hacia adelante o hacia atrás?

Cuando te enfrentas a un oponente, la idea de "adelante" y "atrás" (del primer Bot) se vuelven algo obsoletas. Probablemente estés pensando más en términos de "atacar a la izquierda" o "atacar a la derecha". Para realizar un seguimiento de la dirección del movimiento, simplemente declare una variable como hicimos para oscilar el radar.

```java
class MyRobot extends Bot {
	private byte moveDirection = 1;
```

luego, cuando quieras mover tu robot, simplemente puedes decir:

```java
setForward(100 * moveDirection);
```

Puedes cambiar de dirección cambiando el valor de moveDirection de 1 a -1 así:

```java
moveDirection *= -1;
```

### Cambiar de dirección

El enfoque más intuitivo para cambiar de dirección es simplemente cambiar la dirección del movimiento cada vez que golpeas una pared o golpeas a otro robot de esta manera:

```java
public void onHitWall(HitWallEvent e) { moveDirection *= -1; }
public void onHitBot(HitBotEvent e) { moveDirection *= -1; }
```

Sin embargo, descubrirás que si haces eso, terminarás presionando obstinadamente contra un robot que te embiste desde un costado (como un perro en celo). Esto se debe a que se llama a `onHitRobot()` tantas veces que moveDirection sigue cambiando y nunca te alejas.

Un mejor enfoque es simplemente probar para ver si su robot se ha detenido. Si es así, probablemente significa que has golpeado algo y querrás cambiar de dirección. Puedes hacerlo con el código:

```java
if (getSpeed() == 0)
	moveDirection *= -1;
```

Ponlo en tu método `doMove()` (o en cualquier otro lugar donde estés manejando el movimiento) y podrás manejar todos los eventos de impacto con las paredes u otros Bots.

### ¿Bailamos?

**Dando vueltas (Círculos)**

Puedes rodear a tu enemigo simplemente usando las técnicas anteriores:

```java
public class IABDBot extends Bot {
	double moveDirection=1;
	private double ultimoEnemigoVisto;
    ...
@Override
    public void run() {
        while (isRunning()) {
            doMove();
            go();
        }
        
    public void onScannedBot(ScannedBotEvent e) {
        ...
        ultimoEnemigoVisto=bearingTo(e.getX(), e.getY());
		...
    }
        
    public void doMove() {
        // switch directions if we've stopped
        if (getSpeed() == 0)
            moveDirection *= -1;
        // circle our enemy
        setTurnLeft(ultimoEnemigoVisto+90);
        setForward(1000 * moveDirection);
    }
```

Objetivo: rodea a tu enemigo usando el código de movimiento anterior, como un tiburón rodeando a su presa en el agua.

**Evita Ametrallamiento**

Un problema que puedes notar con el anterior tipo de movimiento es que es presa fácil para los objetivos predictivos porque sus movimientos son tan... predecibles.

Para evadir las balas de manera más efectiva, debes moverte de lado a lado o "atacar". Una buena forma de hacerlo es cambiar de dirección después de un cierto número de "tics", así:

```java
public void doMove() {
    // switch directions if we've stopped
    if (getSpeed() == 0)
        moveDirection *= -1;
    // circle our enemy
    setTurnLeft(ultimoEnemigoVisto+90);
    if (getTurnNumber() % 20 == 0) {
        moveDirection *= -1;
        setForward(1000 * moveDirection);
    }
}
```

Mira como se balancea hacia adelante y hacia atrás usando el código de movimiento anterior. Observa lo bien que esquiva las balas. Pero ojo, porque puede afectar a tu precisión de disparo.

**Cada vez más cerca**

Notarás que tanto el primero como este último tipo de movimientos tienen otro problema: se atascan fácilmente en las esquinas y terminan golpeándose contra las paredes. Un problema adicional es que si su enemigo está lejos, disparan mucho pero no aciertan mucho.

Para hacer que tu robot se acerque a tu enemigo, simplemente modifica el código para que se gire ligeramente hacia su enemigo, así:

```java
setTurnLeft((lastScannedBearing + 90 - (15 * moveDirection)));
```

15 es un factor en grados que puedes modificar y ajustar. Prueba!

Modificando el primero conseguimos una variación que utiliza el código anterior para lanzarse en espiral hacia su enemigo.

Mientras que con el segundo que utiliza el código anterior para acercarse cada vez más. También es bastante bueno para evadir balas.

Fíjate que ninguno de los Bots anteriores queda atrapado en una esquina por mucho tiempo.

### Evitando las paredes

Un problema con todos los Bots anteriores es que golpean mucho las paredes y golpearlas agota tu energía. Una mejor estrategia sería detenerse antes de chocar contra las paredes. ¿Pero cómo?

**Agregar un evento personalizado**

Lo primero que debemos hacer es decidir qué tan cerca permitiremos que nuestro robot llegue a las paredes:

```java
public class WallAvoider extends Bot {
	...
	private int wallMargin = 60; 
```

A continuación, agregamos un evento personalizado que se activará cuando se cumpla una determinada condición:

```java
// Don't get too close to the walls
addCustomEvent(new Condition("TooCloseToWalls") {
    public boolean test() {
        // El método test() es el que debemos reescribir para definir el resultado de nuestra condición.
        return (
            // we're too close to the left wall
            (getX() <= wallMargin ||
             // or we're too close to the right wall
             getX() >= getArenaWidth() - wallMargin ||
             // or we're too close to the bottom wall
             getY() <= wallMargin ||
             // or we're too close to the top wall
             getY() >= getArenaHeight() - wallMargin)
        );
    }
});
```

Ten en cuenta que estamos creando una clase interna anónima con esta llamada. Necesitamos hacer override del método `test()` para devolver un valor booleano cuando ocurra nuestro evento personalizado.

**Gestionar el evento personalizado**

Lo siguiente que debemos hacer es manejar el evento, que se puede hacer así:

```java
    public void onCustomEvent(CustomEvent e) {
        if (e.getCondition().getName().equals("TooCloseToWalls")) {
            // switch directions and move away
            moveDirection *= -1;
            setForward(100 * moveDirection);
        }
    }
```

Sin embargo, el problema con ese enfoque es que este evento podría dispararse una y otra vez, provocando que cambiemos rápidamente de un lado a otro, sin alejarnos nunca. O si ya aparecemos cerca de la pared quedamos atrapados.

Para evitar este "sacudida de la muerte" deberíamos tener una variable que indique que estamos manejando el evento. Podemos declarar otro así:

```java
public class WallAvoider extends Bot {
	...
	private int tooCloseToWall = 0;
```

Luego maneje el evento de manera un poco más inteligente:

```java
public void onCustomEvent(CustomEvent e) {
    if (e.getCondition().getName().equals("TooCloseToWalls")) {
        if (tooCloseToWall <= 0) {
            // Si no estábamos ya cerca de las paredes, ahora lo estamos
            tooCloseToWall += wallMargin;
            setMaxSpeed(0); // Para!!!            
        }
    }
}
```

**Manejo de los dos modos**

Hay dos últimos problemas que debemos resolver. En primer lugar, tenemos un método `doMove()` donde colocamos todo nuestro código de movimiento normal. Si estamos tratando de alejarnos de una pared, no queremos que se llame a nuestro código de movimiento normal, creando (una vez más) el "sacudida de la muerte". En segundo lugar, queremos volver eventualmente al movimiento "normal", por lo que eventualmente deberíamos tener el "tiempo de espera" de la variable `tooCloseToWall`.

Podemos resolver ambos problemas con la siguiente implementación de `doMove()`:

```java
public void doMove() {
	...

    // if we're close to the wall, eventually, we'll move away
    if (tooCloseToWall > 0) tooCloseToWall--;

    // switch directions if we've stopped
    if (getSpeed() == 0) {
        setMaxSpeed(8);
        moveDirection *= -1;
        setForward(1000 * moveDirection);
    }
}
```

Con todo el código anterior evitamos chocar contra las paredes. Observa cómo se desliza suavemente hacia los lados pero nunca (bueno, rara vez) los golpea.

### Bot multimodo

Además de los colores que elijas, la mayor parte de la personalidad de tu robot está en su código de movimiento. Por otra parte, situaciones diferentes requieren tácticas diferentes. Usando el ejemplo de evitar paredes, es posible que desees codificar tu bot para que cambie los "modos" según ciertos criterios. Podrias pensar en algo como esto:

```java
public class MultiModeBot extends Bot {
    private final static int MODO_RONDAR=0;
    private final static int MODO_ESQUIVAR=1;
    private final static int MODO_ASESINO=2;
    private int modoRobot=MODO_RONDAR;
    ...
}

public void onRobotDeath(RobotDeathEvent e) {
	...
	if (getEnemyCount() > 10) {
		// Un gran número de enemigos sugiere movimientos fluidos
		modoRobot=MODO_RONDAR;
	} else if (getEnemyCount() > 1) {
		// esquivar es la mejor táctica para grupos pequeños
		modoRobot=MODO_ESQUIVAR;
	} else if (getEnemyCount() == 1) {
		// Si solo queda un robot, persíguelo
		modoRobot=MODO_ASESINO;
	}
	...
}   

public void doMove() {
    switch (modoRobot){
        case MODO_RONDAR:
            //ronda
            break;
        case MODO_ESQUIVAR:
            //esquiva
            break;
        case MODO_ASESINO:
            //asesina
            break;
    }
    ...
}
```

Los detalles se dejan (como siempre) como ejercicio para el alumnado.

## Técnicas de ataque (Disparo)

### Técnicas básicas

- Separa el movimiento del cañón del movimiento del Bot (`setAdjustGunForBodyTurn`)
- Técnica de apuntado básica: `setTurnGunLeft` o `setTurnGunRight` junto con `gunBearingTo`

### Fórmula de cálculo de potencia de fuego

Otro aspecto importante al disparar es calcular la potencia de fuego de tu bala. La documentación del método fire() explica que puedes disparar una bala en el rango de `0.1` a `3.0`. Como probablemente ya habrás concluido, es una buena idea disparar balas de baja potencia cuando tu enemigo está lejos y balas de alta resistencia cuando está cerca.

```java
public void smartFire(double distance){
    if (distance >200) || getEnergy() <15){
        fire(1);
    } else if (distance > 50){
        fire(2);
    } else {
        fire(3);
    }
}
```

Podrías usar una serie de declaraciones if-else-if-else para determinar la potencia de fuego, en función de si el enemigo está a 50 unidades, a 200, etc (como en el fragmento anterior). Pero tales construcciones son demasiado rígidas. Después de todo, el rango de posibles valores de potencia de fuego cae a lo largo de un continuo, no de bloques discretos. Un mejor enfoque es utilizar una fórmula. He aquí un ejemplo:

```java
setFire(400/distanceTo(e.getX(), e.getY()));
```

Con esta fórmula, a medida que aumenta la distancia del enemigo, la potencia de fuego disminuye. Asimismo, a medida que el enemigo se acerca, la potencia de fuego aumenta. Los valores superiores a 3 se reducen a 3, por lo que nunca dispararemos una bala mayor que 3, pero probablemente deberíamos reducir el valor de todos modos (solo para estar seguros) de esta manera:

```java
setFire(Math.max(400/distanceTo(e.getX(), e.getY()), 3));
```

### Evitar disparos prematuros

Una situación que encontraras es que tu Bot dispare antes de haber girado el arma hacia el objetivo. Para evitar disparos prematuros, usa el método `getGunTurnRemaining()` para ver qué tan lejos está tu cañón del objetivo y no dispare hasta que esté cerca.

Además, no puedes disparar si el arma está "caliente" desde el último disparo y llamar a `fire()` o `setFire()` solo desperdiciará un turno. Podemos probar si el arma está fría llamando a `getGunHeat()`.

### Apuntando de manera predictiva

O.. "Usar la trigonometría para impresionar a tus amigos y destruir a tus enemigos".

Si quisiéramos poder golpear a un robot que recorre las paredes siempre fallaríamos, necesitamos poder predecir dónde estará en el futuro, pero ¿cómo podemos hacerlo?

$$
Distancia = Velocidad * Tiempo
$$
Usando la anterior formula podemos calcular cuánto tiempo tardará una bala en llegar allí.

- **Distancia**: se puede encontrar llamando a `distanceTo(e.getX(), e.getY())`

- **Velocidad**: según la documentación de RCTR, una bala viaja a una velocidad de: 
  $$
  20 - potenciaDeFuego * 3
  $$

- **Tiempo**: podemos calcular el tiempo resolviendo:
  $$
  Tiempo = {Distancia \over Velocidad}
  $$

El siguiente código lo hace:

```java
// calcular la potencia basado en la distancia
double enemyDistance = distanceTo(e.getX(), e.getY());
double firePower = Math.min(500 / enemyDistance, 3);
// calcular la velocidad de la bala
double bulletSpeed = 20 - firePower * 3;
// calculamos el tiempo que necesita la bala para impactar al enemigo
long time = (long) (enemyDistance / bulletSpeed);
```

### Obteniendo futuras coordenadas X,Y

A continuación, debemos calcular la posición futura de nuestro enemigo:

```java
// Calcular la velocidad actual de tu enemigo
double enemyVelocity = e.getSpeed();

// Calcular el desplazamiento en las coordenadas X e Y
double deltaX = enemyVelocity * Math.sin(e.getDirection()); // Componente X del desplazamiento
double deltaY = enemyVelocity * Math.cos(e.getDirection()); // Componente Y del desplazamiento

// Calcular las coordenadas futuras
double futureX = e.getX() + (deltaX * time);
double futureY = e.getY() + (deltaY * time);
```

### Girando el arma al punto previsto

Por último, debemos apuntar nuestro cañon al lugar previsto de impacto:

```java
setTurnGunLeft(gunBearingTo(futureX, futureY));
```

# Investigación y desarrollo propio

A partir de aquí el trabajo será individual de cada alumno (puede solaparse con el paso 3). Deberéis investigar/prever a vuestros adversarios (los conocidos, y los de vuestros compañeros). Aplicar las técnicas que consideréis más útiles para intentar quedar lo más arriba posible en la tabla de clasificación.

# Fuentes de información

- [Wikipedia](https://es.wikipedia.org)
- [GhatGPT](https://chat.openai.com)
- [Modelos de Inteligencia Artificial (Ed. Marcombo)](https://www.marcombo.com/modelos-de-inteligencia-artificial-9788426734419/)
- https://iep.utm.edu/artificial-intelligence/
- Materiales MIA curso MEC-20230524
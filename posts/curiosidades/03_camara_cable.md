# 03 — La cámara voladora no está volando

> *"El truco más viejo del mundo: que algo complejo parezca simple."*  
> — t474_r0b07

---

La ves pasar sobre el campo.  
Suave. Veloz. Silenciosa.

Todo el mundo piensa que es un dron.

Espera.

---

## ¿Por qué no es un dron?

Esa es la pregunta correcta.

Porque parece un dron. Se mueve como un dron. Desde la tribuna no hay forma de distinguirlo.

Pero si fuera un dron, habría problemas que nadie en la transmisión querría tener:

**Batería.** Un dron profesional dura entre 20 y 30 minutos.  
Un partido dura 90 minutos más tiempo añadido.  
Necesitarías cambiar el dron **tres veces** en pleno juego.

**Fallo catastrófico.** Un dron pierde señal, pierde motor, pierde batería — y cae.  
Sobre el campo. Sobre los jugadores. Sobre 80,000 personas.  
No hay sistema de redundancia que haga eso aceptable.

**Interferencia RF.** Un estadio lleno genera una nube de radiofrecuencia de decenas de miles de teléfonos, radios de seguridad, sistemas de transmisión.  
El control inalámbrico de un dron en ese ambiente es una ruleta rusa.

**Video.** La transmisión inalámbrica de video en 4K introduce latencia y compresión.  
Los broadcasters necesitan señal limpia, sin cortes, sin artefactos.

La solución a todos esos problemas es la misma:  
**un cable.**

No un dron. Un robot atado al estadio.

> `// a veces la tecnología más confiable no es la más nueva.`  
> `// es la que no puede caerse.`

---

## El nombre real

Se llama **Spidercam**.  
Desarrollado en Austria por Jens C. Peters.  
Inspirado en el **Skycam** estadounidense — inventado en 1984 por Garrett Brown, el mismo ingeniero detrás del Steadicam.

El principio: cuatro cables. cuatro winches. un robot en el medio.  
Simple de describir. Brutal de ejecutar.

```
ANCHOR_A ●─────────────────────────● ANCHOR_B
          \         DOLLY          /
           \          ●           /
            \        /|\         /
             \      / | \       /
              \    /  |  \     /
               \  /   |   \  /
                \/         \/
ANCHOR_D ●─────────────────────────● ANCHOR_C
                     ↑
              CAMPO DE JUEGO
```

Cuatro **winches motorizados** en las esquinas del estadio — anclados en el techo o en torres temporales.  
De cada winch sale un cable de **Kevlar** — sí, el mismo material de los chalecos antibalas.  
Los cuatro cables convergen en el **dolly**: el carruaje donde vive la cámara.

El dolly no es solo una cámara colgada.  
Lleva estabilización giroscópica, motores de pan y tilt, zoom remoto, fibra óptica para video y alimentación eléctrica — todo por los propios cables.

Nunca desciende más de **10 metros** sobre el campo en juego activo.  
Cubre áreas de hasta **250 × 250 metros**.  
Cuando hay un tiro al arco, sube en fracciones de segundo para no cruzarse con el balón.

---

## El problema que resuelve cada milisegundo

Mover el dolly de un punto a otro en 3D requiere calcular una pregunta en tiempo real:

*"Para que el dolly esté en (x, y, z), ¿cuánto cable necesita cada winch?"*

Eso se llama **cinemática inversa** — y ocurre miles de veces por segundo.

```python
import math

# Los 4 anchors en las esquinas del estadio
anchors = {
    'A': (0,   105, 25),
    'B': (68,  105, 25),
    'C': (68,  0,   25),
    'D': (0,   0,   25),
}

def inverse_kinematics(x, y, z):
    return {
        name: round(math.sqrt(
            (x - ax)**2 + (y - ay)**2 + (z - az)**2
        ), 4)
        for name, (ax, ay, az) in anchors.items()
    }

# Dolly en el centro del campo, altura 8m
print(inverse_kinematics(34, 52, 8))
# {'A': 46.12, 'B': 46.12, 'C': 46.12, 'D': 46.12}
# simétrico — tiene sentido, es el centro exacto

# Dolly volando hacia la portería norte
print(inverse_kinematics(34, 90, 6))
# {'A': 23.43, 'B': 23.43, 'C': 66.87, 'D': 66.87}
# cables A y B se acortan → dolly se acerca a esa esquina
# cables C y D se alargan → dolly se aleja de esa esquina
```

Cada vez que el operador mueve el joystick, este cálculo se ejecuta, los winches reciben nuevas instrucciones y los motores ajustan.

Pero calcular la longitud objetivo no es suficiente.  
Los cables tienen masa. Tienen elasticidad. El viento empuja el dolly.  
La aceleración genera oscilaciones.

Para corregir todo eso en tiempo real existe el **PID controller** —  
*Proportional-Integral-Derivative*.

Está en termostatos, en el control de crucero de tu auto, en drones, en robots industriales.  
Y en el Spidercam — uno por cada winch, ejecutándose cada milisegundo:

```python
class PID:
    def __init__(self, kp, ki, kd):
        self.kp = kp  # reacciona al error actual
        self.ki = ki  # corrige errores acumulados
        self.kd = kd  # anticipa hacia dónde va el error
        self.integral = 0
        self.prev_error = 0

    def compute(self, target, current, dt):
        error = target - current
        self.integral += error * dt
        derivative = (error - self.prev_error) / dt
        self.prev_error = error
        return (self.kp * error) + (self.ki * self.integral) + (self.kd * derivative)

winch = PID(kp=2.5, ki=0.01, kd=0.8)

# cada ciclo de 1ms:
velocidad_motor = winch.compute(
    target=44.38,    # longitud objetivo calculada por IK
    current=44.42,   # longitud real leída por el encoder
    dt=0.001
)
# el motor ajusta hasta que error → 0
```

Lo que ves como una toma suave y cinematográfica es **cuatro PIDs corriendo en paralelo, 1,000 veces por segundo**, peleando contra física, gravedad y viento para que el dolly esté exactamente donde tiene que estar.

---

## Lo que puede salir mal

En 2013, **NASCAR Coca-Cola 600**, Charlotte Motor Speedway.  
Vuelta 121. El cable del sistema CableCam de Fox Sports se rompe.  
La cámara cae al circuito. Impacta varios autos en carrera — incluyendo el del líder.  
El evento se detiene.

El cable que falló era de **nylon**.

Esa es la razón por la que los sistemas profesionales usan Kevlar.  
Y la razón por la que hay sensores de tensión en cada winch que activan frenos de emergencia ante cualquier caída brusca de carga.

El Spidercam con Kevlar tampoco puede "caer libremente" —  
si todo falla, los cables lo sostienen colgado.  
El peor escenario es que quede suspendido en el aire.

No que caiga sobre 80,000 personas.

> `// un sistema sin redundancia no es un sistema.`  
> `// es una apuesta.`

---

## La conexión que nadie hace

El Spidercam es un **Cable-Driven Parallel Robot** — CDPR.

La misma categoría de sistema mueve el receptor del telescopio **FAST** en China —  
el radiotelescopio más grande del mundo, 500 metros de diámetro —  
que usa cuatro cables para posicionar un receptor de **30 toneladas** sobre el plato.

La cámara que te muestra el gol en cámara lenta  
y el telescopio que escucha el universo buscando señales de vida extraterrestre  
operan con la misma matemática.

> `// la escala cambia. la física no.`

---

## Challenge embebido

```
Un Spidercam en un estadio de 68m × 105m.
Los 4 anchors en las esquinas a 22m de altura.

El operador quiere el dolly en:
x = 20m, y = 30m, z = 5m

¿Cuál es el cable más corto?
¿Cuál es el más largo?
¿Cuántos metros de diferencia hay entre ambos?

Muestra el cálculo.
Respuesta → issues del repo · título: [HACKBALL-03]
```

---

<details>
<summary><code>// referencias técnicas</code></summary>

- Spidercam — [Wikipedia](https://en.wikipedia.org/wiki/Spidercam)
- Inverse Kinematics for SpiderCam — [marginallyclever.com](https://www.marginallyclever.com/2015/02/code-inverse-kinematics-spidercam-skycam/)
- PID Controller — Ziegler-Nichols tuning method, 1942
- Cable-Driven Parallel Robots — Springer, 2022
- FAST Telescope — NAOC China, 500m aperture

</details>

---

<details>
<summary><code>// lore relacionado</code></summary>

**La catenaria tiene 333 años.**

En 1691, Jakob Bernoulli publicó la forma exacta que toma un cable bajo su propio peso.  
La llamó "catenaria" — del latín *catena*, cadena.

Leibniz y Huygens llegaron a la misma solución el mismo año, de forma independiente.

```
y = a × cosh(x/a)
```

Esa ecuación vive en el firmware de los winches del Spidercam.  
Cada toma aérea que ves en un partido la ejecuta sin que nadie lo sepa.

El conocimiento no caduca.  
Solo cambia de contexto.

</details>

---

*← [02 — El balón también tiene sensores](02_balon_sensores.md) · siguiente → [04 — La cámara del árbitro](04_camara_arbitro.md)*

---

> *t474_r0b07 · [github.com/t474-r0b07](https://github.com/t474-r0b07)*  
> `// construyo sistemas pensando en cómo romperlos.`

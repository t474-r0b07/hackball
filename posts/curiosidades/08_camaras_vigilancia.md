# 08 — ¿Cuántas cámaras te observan durante un partido?

> *"La invisibilidad no es un derecho.*  
> *Es un privilegio que ya perdiste."*  
> — t474_r0b07
---
![banner](../../assets/gh_hackball_8.png)
---

Compraste tu entrada.  
Pasaste los torniquetes.  
Buscaste tu asiento.

En ese trayecto de 10 minutos,  
apareciste en más sistemas de los que puedes contar.

---

## El inventario de quien te ve

```
FUENTE                        QUIÉN LA OPERA          TÚ CONSIENTES
──────────────────────────────────────────────────────────────────────
Torniquete biométrico         FIFA / club             ✓ (ticket digital)
CCTV interior del estadio     club / seguridad        implícito
CCTV exterior / accesos       ciudad / policía        implícito
Cámaras de transmisión        broadcaster             implícito
Reconocimiento facial         seguridad privada       implícito / dudoso
Cámara del árbitro            FIFA                    implícito
Teléfonos de otros fans       desconocidos            sin consentimiento
Prensa acreditada             medios                  implícito
Drones de seguridad           autoridades             implícito
Sistema de análisis de masas  FIFA / ciudad            enterrado en T&C
```

Cada fila es un sistema distinto.  
Cada sistema tiene su propio operador, su propia base de datos, su propia política de retención.

No hay un centro de control unificado.  
Hay una docena de sistemas que no se hablan entre sí  
pero que todos, simultáneamente, saben que estás ahí.

---

## El Mundial 2026 — el experimento más grande de la historia

Vancouver instaló **200 cámaras temporales** como requisito de FIFA  
en zonas de actividad relacionada con el Mundial —  
estadio, fan zones, sitios de entrenamiento.

Durante los Juegos Olímpicos de Invierno de 2010 en la misma ciudad  
se activaron **casi 1,000 cámaras de seguridad**.  
El RCMP instaló 900 alrededor de los venues.  
La ciudad instaló 90 en sitios públicos del centro.

Pregunta oficial de CBC News al RCMP:  
*¿esas 900 cámaras siguen operando?*

Respuesta del RCMP: sin comentarios.

> `// los sistemas de vigilancia temporal`  
> `// rara vez son temporales.`

En Seattle, a días del inicio del Mundial,  
el concejal Bob Kettle acusó a la alcaldesa de violar la ley municipal  
por no activar las cámaras CCTV ya instaladas en el Stadium District.

Las cámaras están. Están instaladas. Están conectadas.  
El debate no es si existen — es si alguien tiene autoridad para apagarlas.

---

## Reconocimiento facial — el salto cualitativo

Las cámaras clásicas graban.  
Las cámaras con reconocimiento facial **identifican**.

La diferencia no es menor.

```
CÁMARA CLÁSICA:
[imagen] → almacenamiento → revisión humana posterior

CÁMARA CON FACIAL RECOGNITION:
[imagen] → modelo de detección → vector facial → base de datos
         → match en tiempo real → alerta automática
         → acción: acceso denegado / seguimiento / registro
```

Qatar 2022 fue el campo de prueba masivo —  
el sistema **Hayya** de Fan ID biométrico procesó la identidad de cada asistente  
como condición de entrada al país.

Para el Mundial 2026, FIFA y los tres países anfitriones están implementando  
un **Digital Fan ID unificado** con coordinación biométrica transfronteriza:  
una vez que tus datos están en el sistema,  
funcionan en los 16 estadios de los 3 países.

El reconocimiento facial ya no solo verifica que eres quien dices ser.  
Analiza comportamiento de multitudes en tiempo real:  
densidad de crowd, flujos de movimiento irregulares, patrones anómalos.

Traducción: el sistema no solo sabe quién eres.  
Sabe cómo te mueves.  
Y sabe cuándo tu movimiento no es normal.

---

## La capa que nadie menciona

Encima de toda la infraestructura de cámaras existe una capa adicional  
que opera en silencio:

**AI video analytics en edge devices.**

No es procesamiento centralizado.  
Es inteligencia corriendo directamente en el hardware de cada cámara.

```python
# Lo que corre en cada cámara inteligente en tiempo real:

detecciones = {
    'armas':                  detectar(frame, modelo='weapon_detection'),
    'objetos_abandonados':    detectar(frame, modelo='abandoned_object'),
    'densidad_crowd':         calcular(frame, modelo='crowd_density'),
    'movimiento_anomalo':     analizar(frame, modelo='anomaly_detection'),
    'perimetro':              verificar(frame, modelo='perimeter_breach'),
    'rostro':                 identificar(frame, modelo='facial_recognition'),
}

for alerta, resultado in detecciones.items():
    if resultado.confidence > THRESHOLD:
        enviar_alerta(centro_comando, alerta, resultado, timestamp)
```

Cada cámara es un nodo de procesamiento independiente.  
Los 16 estadios generan un flujo unificado de datos  
que el comité organizador puede ver en tiempo real.

Para el Mundial 2026 eso son **6 millones de fans**  
procesados por ese sistema a lo largo del torneo.

---

## Tu teléfono también es una cámara

Pero en dirección contraria.

El WiFi del estadio sabe cuándo llegaste, dónde te ubicaste y cuándo te fuiste —  
con precisión de metros, a través de triangulación por señal.

La app oficial del torneo, si la instalaste,  
tiene acceso a tu ubicación, tu cámara y tu historial de búsqueda.

Y los **48,000 teléfonos** de otros fans en el estadio  
están fotografiando y filmando constantemente —  
y subiendo ese contenido a plataformas que tienen  
sus propios sistemas de reconocimiento facial.

Apareces en el fondo de la selfie de alguien.  
Esa foto se sube a Instagram.  
Instagram tiene facial recognition.  
Instagram sabe que estuviste ahí.

Tú nunca subiste nada.

---

## OPSEC en un estadio — ¿tiene sentido?

En ciberseguridad, **OPSEC** — *Operations Security* —  
es el proceso de identificar qué información tuya es visible para un adversario  
y tomar medidas para reducir esa exposición.

Aplicado a un estadio:

```
LO QUE EMITES                    CÓMO REDUCIRLO
──────────────────────────────────────────────────────────
Rostro identificable             No hay manera práctica
Señal WiFi del teléfono          Modo avión / WiFi desactivado
Señal Bluetooth                  Bluetooth desactivado
Ubicación GPS                    GPS desactivado
App oficial instalada            No instalarla
Pago con tarjeta                 Efectivo (donde acepten)
Ticket digital vinculado a ti    No hay manera si es nominativo
```

La conclusión honesta:  
en un estadio moderno con infraestructura de seguridad completa,  
la reducción de exposición es **marginal**.

No porque las medidas no funcionen.  
Sino porque la mayoría de los vectores son requisitos de entrada  
o consecuencias de estar en un espacio público con miles de personas.

> `// la pregunta no es si puedes ser invisible.`  
> `// la pregunta es: ¿quién tiene acceso a los datos que generas`  
> `// y qué pueden hacer con ellos?`

---

## La pregunta que nadie responde

Vancouver, Seattle, París, Qatar.  
Cada evento instala infraestructura de vigilancia "temporal".

¿Qué pasa con esa infraestructura cuando el evento termina?

La respuesta varía por ciudad y por país.  
Pero la tendencia documentada es consistente:

los sistemas se quedan.  
Las políticas de retención de datos se extienden.  
El acceso de terceros no siempre se revoca.

No porque haya una conspiración.  
Sino porque desmantelar infraestructura de seguridad activa  
requiere decisión política activa —  
y nadie tiene incentivo para tomar esa decisión.

> `// es más fácil dejar encendido`  
> `// que justificar por qué apagas.`

---

## Challenge embebido

```
Estás en un estadio con la siguiente infraestructura activa:
- 400 cámaras CCTV internas
- 80 cámaras con facial recognition en accesos
- WiFi público con 45,000 dispositivos conectados
- App oficial instalada en el 60% de los asistentes
- Sistema de tickets nominativos digitales

Tu objetivo: asistir al partido dejando el mínimo rastro posible.

Preguntas:
1. ¿Qué vectores puedes controlar y cuáles no?
2. ¿Cuál es el vector de mayor exposición que SÍ puedes mitigar?
3. ¿Existe alguna combinación de medidas que te haga
   prácticamente no rastreable? ¿Por qué sí o por qué no?

No hay respuesta correcta única.
Hay análisis de superficie de ataque.

Respuesta → issues del repo · título: [HACKBALL-08]
```

---

<details>
<summary><code>// referencias técnicas</code></summary>

- Vancouver CCTV Mundial 2026 — CBC News, diciembre 2025
- Seattle CCTV debate — KOMO News, mayo-junio 2026
- Biometrics en estadios Mundial 2026 — The Costa Rica News, junio 2026
- FIFA Digital Fan ID — inside.fifa.com
- AI video analytics en estadios — Intellisee, junio 2026
- OPSEC framework — NSA Operations Security guidelines
- Hayya Fan ID Qatar 2022 — FIFA official documentation

</details>

---

<details>
<summary><code>// lore relacionado</code></summary>

**El problema de la cámara de Londres.**

Para los Juegos Olímpicos de 2012, Londres agregó miles de cámaras CCTV.  
Ya era la ciudad más vigilada de Europa — con más cámaras per cápita que cualquier otra capital occidental.

Un estudio del año siguiente determinó que la mayoría de las cámaras instaladas para los Juegos  
seguían operando — pero sin protocolo claro de supervisión.

El problema no era que las cámaras existieran.  
El problema era que nadie tenía claro quién era responsable de qué hacía con el footage.

En seguridad informática lo llamaríamos un problema de **ownership de activos** —  
cuando un activo existe en producción pero nadie tiene asignada la responsabilidad de su seguridad.

Un activo sin owner no es un activo seguro.  
Es un activo esperando ser comprometido.

</details>

---

*← [07 — Las apuestas son datos](07_apuestas_datos.md) · siguiente → [09 — El mejor sistema defensivo](09_defensa_capas.md)*

---

> *t474_r0b07 · [github.com/t474-r0b07](https://github.com/t474-r0b07)*  
> `// construyo sistemas pensando en cómo romperlos.`

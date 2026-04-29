# S-80 Isaac Peral · Gemelo Digital 3D Interactivo

Recorrido virtual en tres dimensiones del submarino convencional español **S-80 Isaac Peral** (clase Isaac Peral, P-75I), reconstruido a partir de imágenes de referencia y especificaciones públicas. Permite explorar el exterior y el interior compartimento a compartimento, ver fotografías reales del interior aplicadas como texturas y operar todos los subsistemas desde un cockpit simulado al estilo SUBICS / CONAN-SUB.

> Aplicación 100% cliente. Three.js + JavaScript vanilla. Sin dependencias de servidor. Funciona abriendo el HTML directamente en cualquier navegador moderno.

---

## Índice

1. [Resumen](#resumen)
2. [Características destacadas](#características-destacadas)
3. [Estructura del proyecto](#estructura-del-proyecto)
4. [Requisitos](#requisitos)
5. [Puesta en marcha](#puesta-en-marcha)
6. [Manual de uso · Movimiento 3D](#manual-de-uso--movimiento-3d)
7. [Manual del cockpit HUD](#manual-del-cockpit-hud)
8. [Compartimentos modelados](#compartimentos-modelados)
9. [Subsistemas simulados](#subsistemas-simulados)
10. [Imágenes de referencia](#imágenes-de-referencia)
11. [Arquitectura técnica](#arquitectura-técnica)
12. [Personalización y extensión](#personalización-y-extensión)
13. [Solución de problemas](#solución-de-problemas)
14. [Datos técnicos del S-80](#datos-técnicos-del-s-80)
15. [Glosario](#glosario)
16. [Fuentes y referencias OSINT](#fuentes-y-referencias-osint)
17. [Créditos y licencia](#créditos-y-licencia)

---

## Resumen

El proyecto reproduce un **gemelo digital** caminable del S-80 con dos modos integrados:

- **Modo recorrido**: el usuario se mueve libremente como una persona dentro y fuera del submarino, con escala humanizada (2,15 m de altura libre interior, ojos a 1,65 m del suelo).
- **Modo cockpit**: una capa HUD superpone los controles operativos reales de un submarino moderno (timón, planos, EOT, lastre, AIP, sonar, armas, atmósfera) con simulador físico simplificado.

El submarino visible mide **81,05 m de eslora × 7,3 m de manga**, replica los **timones en X de popa**, hélice de **7 palas**, vela con cuatro mástiles diferenciados (periscopio óptico, ESM, snorkel, antena), domo sonar de proa, **6 tubos lanzatorpedos** y revestimiento anecoico procedural.

---

## Características destacadas

### Exterior
- Casco con textura procedural de **losetas anecoicas** (variación de tonalidad por baldosa).
- Vela con **bordes redondeados**, parabrisas translúcido en la cofa, placa identificativa **S-81** en ambas caras y bandera de España.
- **4 mástiles** sobre la vela: periscopio óptico (con cabezal), mástil ESM con bola, snorkel con cabezal rectangular, antena de comunicaciones.
- **Aletas de buceo de proa** sobre la vela y **timones en X** a 45°, 135°, 225° y 315° en popa.
- **Hélice de 7 palas** con paso, cubo cónico y eje. Velocidad de giro **atada al RPM del motor simulado**.
- **Domo sonar de proa** translúcido y carenado del array remolcado en popa.
- Aberturas free-flood, escotillas de salvamento (anillos), cleats sobre cubierta.

### Interior
- **6 compartimentos** separados por mamparos con escotilla circular abierta y puerta abatida lateral.
- Suelo de rejilla metálica (textura procedural), techo con haces de cables y conductos de colores codificados (rojo eléctrico, amarillo aire, azul agua, marrón hidráulico).
- Lámparas tubulares cada 4 m + luces puntuales por compartimento (luz roja en CIC simulando «battle stations»).
- Etiquetas amarillas iluminadas en cada mamparo con el nombre del compartimento.
- **9 fotografías reales** del interior del S-81 aplicadas como cuadros murales (con marco, lámpara y cartel) en sus compartimentos correspondientes.

### Cockpit HUD
- 6 paneles tipo consola militar (verde/ámbar sobre fondo oscuro): **Sonar pasivo, Estado del buque, Puesto de maniobra, Propulsión/Energía, Armamento, Lastre y trimado**.
- 6 instrumentos circulares con aguja, escala y zona roja: Timón · Planos proa · Planos popa · Profundidad · Velocidad · Compás giratorio.
- **EOT** (Engine Order Telegraph) con 8 posiciones canónicas.
- Cascada de **sonar pasivo** 0-360° con ruido ambiente y trazas de contactos en tiempo real.
- 6 tubos lanzatorpedos interactivos, **botón de fuego** con cooldown.
- 9 tanques de lastre/trimado animados.
- Simulador físico que integra rumbo, profundidad, presión, atmósfera (O₂/CO₂), batería, AIP, posición geográfica.

---

## Estructura del proyecto

```
38_NAVANTIA_S80/
├── README.md                      ← este fichero
├── S80v001.html                   ← aplicación principal (HTML + CSS + JS)
├── S80v001_assets.js              ← imágenes embebidas en base64 (1,51 MB)
├── gemini-code-1777481694478.html ← prototipo inicial de referencia
└── S-80/                          ← carpeta con las imágenes originales
    ├── 9fb562a5_*.png             ← render 3D de la sala de control
    ├── album-interior-*.webp      ← 8 fotos reales del interior del S-81
    ├── 1437365*.webp              ← fotos del simulador y CIC
    ├── *678x381.*                 ← fotos exteriores e ilustraciones
    ├── s-80p-*-cutaway-*.jpg      ← corte transversal del submarino
    └── ...                        ← otras imágenes de referencia
```

| Fichero | Descripción | Tamaño aprox. |
|---|---|---|
| `S80v001.html` | Aplicación completa: estructura HTML, estilos, motor 3D, simulador, HUD | ~85 KB |
| `S80v001_assets.js` | Las 9 imágenes principales codificadas como data-URI base64 dentro de `window.S80_PHOTOS` | ~1,51 MB |
| `S-80/` | Originales de las imágenes (no necesarias en runtime, solo para regenerar `S80v001_assets.js`) | ~6 MB |

---

## Requisitos

| Componente | Mínimo | Recomendado |
|---|---|---|
| Navegador | Chrome 90+, Firefox 88+, Edge 90+ | Última versión |
| WebGL | WebGL 1.0 | WebGL 2.0 |
| Resolución | 1024×768 | 1920×1080 o superior |
| RAM disponible | 1 GB | 2 GB |
| GPU | Cualquier GPU integrada con WebGL | GPU dedicada para 60 fps |
| Internet | **Solo en la primera ejecución** (Three.js desde CDN). Después se cachea | Conexión rápida durante carga inicial |

> Three.js r128 se descarga desde `cdnjs.cloudflare.com`. Si necesitas funcionamiento totalmente offline, descarga `three.min.js` y cambia el `<script src=…>` para apuntar al fichero local.

---

## Puesta en marcha

### Opción A — Doble click (recomendada)

1. Coloca `S80v001.html` y `S80v001_assets.js` en la misma carpeta.
2. Doble click en `S80v001.html`.
3. El navegador abre la escena, las imágenes embebidas se decodifican localmente y todo funciona sin servidor.

### Opción B — Servidor local

Si prefieres servirlo con un servidor de desarrollo (útil al trastear con código):

```bash
# Python 3
cd C:\_CONFIANZA23\PRODUCTOS\38_NAVANTIA_S80
python -m http.server 8000

# o Node
npx serve .
```

Después abre `http://localhost:8000/S80v001.html`.

### Opción C — Regenerar `S80v001_assets.js`

Si añades o cambias imágenes en la carpeta `S-80/`:

```powershell
# Windows PowerShell (UTF-8 sin BOM)
$base = '.\S-80\'
$prefix = 'album-interior-of-the-spanish-navy-s-80-plus-class-diesel-v0-'
$files  = @('888o2f2o9oje1','ahlm4jrl9oje1','amad02oq9oje1','cztlnhym9oje1',
            'dkn9ka9k9oje1','mz7lr6gd9oje1','onom4rqp9oje1','s1t988zo9oje1')
$sb = [System.Text.StringBuilder]::new()
$null = $sb.AppendLine('window.S80_PHOTOS = {')
foreach ($f in $files) {
    $b = [Convert]::ToBase64String([IO.File]::ReadAllBytes($base+$prefix+$f+'.webp'))
    $null = $sb.AppendLine("  '$f': 'data:image/webp;base64,$b',")
}
$b = [Convert]::ToBase64String([IO.File]::ReadAllBytes(
    $base + '9fb562a5_1163_47c0_b249_53721b196ba3_51e84f8fda.png'))
$null = $sb.AppendLine("  '9fb562a5': 'data:image/png;base64,$b'")
$null = $sb.AppendLine('};')
[IO.File]::WriteAllText('.\S80v001_assets.js', $sb.ToString(),
    [Text.UTF8Encoding]::new($false))
```

---

## Manual de uso · Movimiento 3D

El jugador es un cuerpo virtual con cámara colocada a la altura de los ojos. Puede caminar libremente por dentro y por fuera del submarino sin colisiones (modo «no-clip» para facilitar la exploración).

### Controles del ratón

| Acción | Comando |
|---|---|
| **Activar mira con ratón** | Click izquierdo en la escena → bloquea el cursor (pointer-lock) |
| Mirar alrededor | Mover el ratón (yaw + pitch). El crosshair central se ilumina cuando está activo |
| **Liberar el ratón** | Tecla `ESC` |

> Los clicks sobre los paneles del HUD **no** activan el pointer-lock — solo se activa al hacer click directamente sobre la escena 3D.

### Controles de teclado

| Tecla | Acción |
|---|---|
| `W` / `↑` | Avanzar |
| `S` / `↓` | Retroceder |
| `A` | Lateral izquierda (strafe) |
| `D` | Lateral derecha (strafe) |
| `Q` / `←` | Girar a babor |
| `E` / `→` | Girar a estribor |
| `Espacio` | Subir verticalmente |
| `Shift` o `Ctrl` | Bajar verticalmente |

### Controles táctiles (móvil/tableta)

Se mantienen los pads inferiores cuando el HUD está apagado. Los botones cuadrados con flechas funcionan con `touchstart` / `touchend`.

### Botones de la barra izquierda

| Botón | Función |
|---|---|
| **Casco: Semitransparente / Opaco / Oculto** | Cicla el modo del casco exterior. En *Semitransparente* se ve el interior; *Oculto* permite ver solo el equipamiento |
| **Velocidad: Normal / Rápida (×3) / Lenta (×0,4)** | Multiplica la velocidad del jugador, útil para cubrir 81 m rápidamente |
| **Cockpit HUD: ON / OFF** | Muestra u oculta los 6 paneles del cockpit |
| **Teletransportarse a…** | Selector con 8 destinos preestablecidos |

### Destinos del teletransporte

| Opción | Coordenadas | Descripción |
|---|---|---|
| Exterior (vista general) | (0, 14, 35) | Vista exterior elevada, mirando al submarino |
| Cofa de la vela | (10,5, 8,8, 0) | En lo alto de la vela, junto al periscopio |
| Sala de torpedos (proa) | (34, 0,7, 0) | Frente a los 6 tubos lanzatorpedos |
| Camarotes oficiales | (23, 0,7, 0) | Camarote del comandante |
| Sala de control / CIC | (11,5, 0,7, 0) | Centro de mando, bajo la vela |
| Camarotes / Cocina | (-2, 0,7, 0) | Literas triples y cocina |
| Planta AIP | (-17, 0,7, 0) | Reformador y tanque LOX |
| Sala de motores | (-32, 0,7, 0) | Motor MTU y eje |

### Panel de información (HUD apagado)

Cuando el cockpit HUD está OFF, en la esquina superior derecha aparece un panel con:
- Datos técnicos del S-80
- **Ubicación detectada automáticamente** (cambia al cruzar mamparos)
- Coordenadas x/y/z del jugador
- Recordatorio de controles

---

## Manual del cockpit HUD

El HUD está dividido en **6 paneles** dispuestos por las cuatro esquinas y el centro inferior. Cada panel tiene un título amarillo con un LED parpadeante verde que indica que el panel está activo.

### Panel 1 · Sonar Pasivo · CFTT (arriba-izquierda)

**Cascada del sonar**: gráfica 300×90 px que se actualiza a 10 Hz. El eje horizontal cubre 360° de demora (0° = norte) y el eje vertical es el tiempo (lo más reciente arriba). Puntos verdes brillantes = contactos confirmados (HI), verdes intermedios = MID, oscuros = LOW.

**Lista de contactos**: cada fila muestra:
- **ID** del contacto (M01 = mercante, S01 = submarino, B01 = biológico…)
- **B**xxx° = demora actual
- **R**xx,x nm = distancia
- xx,x kn = velocidad
- Clasificación textual (MERC. / SUBM(?) / BIOLG. / PESQ.)

Los contactos **derivan ligeramente** en demora y rango cada frame para simular movimiento real.

### Panel 2 · Estado del Buque (arriba-derecha)

Resumen operativo del buque:

| Campo | Unidad | Notas |
|---|---|---|
| Rumbo | grados | 000-359°, integrado a partir del timón |
| Profundidad | metros | Calculada por planos + flotabilidad MBT |
| Velocidad | nudos | Derivada de RPM motor |
| Posición | lat/lon | Avanza por integración de rumbo y velocidad |
| Casco | % | Fijo 100% (no hay simulación de daños) |
| Presión amb. | bar | 1,013 + profundidad × 0,0985 |
| O₂ aire | % | 20,9% por defecto. **Se vuelve naranja** si baja de 19% |
| CO₂ | % | 0,07% por defecto. **Se vuelve naranja** si supera 0,3% |
| Temperatura/Humedad | °C / % | Drift estocástico dentro de rangos seguros |

**Mosaico de compartimentos** (MOT/AIP/TRP/CIC/OFI/TOR): tooltip con el nombre completo al pasar el ratón. Verde = OK, naranja = WARN, rojo = DANGER.

### Panel 3 · Puesto de Maniobra · Timonel · CONAN-SUB (abajo-izquierda)

El panel principal del cockpit, modelado a partir de la foto del simulador del CESADAR (CIC bajo iluminación red light).

**Fila de instrumentos circulares** (6 gauges):

| Instrumento | Rango | Zona roja |
|---|---|---|
| Timón | -35° a +35° | — |
| Planos proa | -25° a +25° | — |
| Planos popa | -25° a +25° | — |
| Profundidad | 0 a 300 m | Sí, a partir de 250 m |
| Velocidad | 0 a 20 kn | — |
| Compás | 360° giratorio | Aguja N en rojo |

Cada gauge tiene:
- Aguja amarilla con sombra luminosa
- Tick marks principales (cada 10%) y secundarios
- Etiqueta de mín/máx en los extremos
- Valor digital ámbar bajo el dial

**EOT (Engine Order Telegraph)** — 8 posiciones canónicas con el código RPM:

| Posición | Etiqueta | RPM ordenado |
|---|---|---|
| Atrás avante | AT.AV | -150 |
| Atrás 1/3 | AT.1/3 | -50 |
| Paro | PARO | 0 |
| Avante 1/3 | AV.1/3 | +50 |
| Avante 2/3 | AV.2/3 | +100 |
| Estándar | EST. | +130 |
| Toda avante | TODA | +160 |
| Flanco | FLANCO | +190 |

El botón activo se resalta en verde con borde amarillo y glow. Cada cambio se loguea en el panel de mensajes superior izquierdo.

**Botones de control de superficie**:

| Botón | Acción |
|---|---|
| ◀ Babor 5° / Estribor 5° ▶ | Incrementa el timón ordenado en ±5°, hasta ±35° |
| Centro Timón | Pone el timón a 0° |
| ▼ Pl. proa abajo / ▲ Pl. proa arriba | ±5° a planos de proa hasta ±25° |
| Pl. proa cero | Cero planos proa |
| ▼ Pl. popa abajo / ▲ Pl. popa arriba | ±5° a planos de popa hasta ±25° |
| Pl. popa cero | Cero planos popa |
| 🔭 Izar Periscopio | Teletransporta al jugador a la cofa de la vela |
| ↑ A SUPERFICIE | Sopla MBT, planos +15°, ascenso |
| ⚠ INMERSIÓN (rojo) | Inunda MBT, planos -25°, EOT a flanco |

### Panel 4 · Propulsión · AIP · Energía (abajo-derecha)

Tabla de subsistemas con LEDs animados (verde = ON, ámbar = WARN, rojo = ERR, gris = OFF):

| Subsistema | Lectura |
|---|---|
| AIP Bioetanol | Estado EN LÍNEA / PARADO |
| Diésel MTU 16V 396 SE84 | RPM o PARADO |
| Batería principal | CARGANDO / DESCARGA |
| Snorkel | IZADO / BAJO |
| Carga batería | % (0-100) |
| Motor principal | RPM real |
| Eje | kW = RPM × 4,5 |
| O₂ líquido (LOX) | % |
| Bioetanol | % |
| H₂ reformador | activo |

**Botones**: `Arrancar Diésel` · `Izar Snorkel` · `AIP On/Off`

> Para **cargar batería** se necesita: diésel arrancado + snorkel izado + profundidad < 8 m. En caso contrario la batería siempre descarga.

### Panel 5 · Sistema de Armas · DM2A4 / SUT (centro-derecha)

**Grid 3×2 de tubos lanzatorpedos T1-T6**. Cada tubo muestra el número, el estado y el arma:

| Estado | Color borde | Significado |
|---|---|---|
| EMPTY | gris | Tubo vacío |
| LOADED | verde | Cargado con torpedo |
| FLOODED | azul | Inundado, listo para abrir compuerta |
| OPEN | naranja con glow | Compuerta exterior abierta, listo para disparar |
| FIRED | rojo + opacidad reducida | Recién disparado (recarga en 6 s) |

**Click sobre un tubo** cicla los estados: EMPTY → LOADED → FLOODED → OPEN → EMPTY (excepto FIRED que se autorecarga).

**Información de Fire Control**:
- Solución FC: demora y rango calculados al blanco principal
- Blanco: ID + clasificación
- Tubo activo: T1-T6 si hay alguno OPEN, «ninguno» si no

**Botón rojo `⚠ FUEGO ⚠`**: dispara el primer tubo que esté en estado OPEN. Si ninguno está OPEN, error en log.

### Panel 6 · Lastre y Trimado (centro-inferior)

**Cinco tanques principales** MBT 1-5 con barra animada azul que sube/baja con la cantidad de agua (0% = vacío = boyante, 100% = lleno = neutro/negativo).

**Cuatro tanques auxiliares**:
- TRIM PROA — para asentar la proa
- TRIM POPA — para asentar la popa
- NEGATIVO — sumersión rápida
- AUX — auxiliar/uso general

**Cinco botones de acción**:

| Botón | Efecto |
|---|---|
| SOPLAR MBT | Cada MBT pierde 25% de agua (sube el sub) |
| INUNDAR MBT | Cada MBT gana 25% de agua (baja el sub) |
| Bombear→PROA | Pasa 10% del trim popa al de proa |
| Bombear→POPA | Pasa 10% del trim proa al de popa |
| ⚠ SOPLO EMERG. (rojo) | Vacía todos los MBT al instante + planos +25° |

---

## Compartimentos modelados

El submarino está dividido en **6 compartimentos** longitudinalmente, separados por mamparos con escotilla circular y puerta abierta lateral. La proa apunta hacia el eje +X.

### 1. Sala de motores y propulsión (popa) · `x ∈ [-40,5 ; -24,5]`
- **Motor diésel MTU 16V 396 SE84** con 6 cilindros en línea, tapa de culata, bancada y placa identificativa
- **Generador eléctrico** cilíndrico
- **Motor eléctrico de propulsión** principal con eje de transmisión hacia la hélice
- 3 cuadros eléctricos con LEDs verdes/naranjas
- Tuberías hidráulicas en el techo
- 3 válvulas con volante rojo
- **Foto mural babor**: motor MTU real

### 2. Planta AIP / Bioetanol · `x ∈ [-24,5 ; -9,5]`
- **Reformador de bioetanol** vertical etiquetado
- **Tanque de oxígeno líquido (LOX)** con marca O₂ y aislamiento
- **Pila de combustible** rectangular con 6 indicadores azules
- Tanque de bioetanol cilíndrico
- Tuberías criogénicas azul/blanco en el techo
- **Foto mural babor**: panel eléctrico de la planta
- **Foto mural estribor**: consola «Propulsión y Sistemas» con 4 pantallas

### 3. Camarotes tripulación / Cocina · `x ∈ [-9,5 ; 4,5]`
- **4 columnas de literas triples** a babor (12 plazas en total)
  - Cada litera con marco metálico, colchón azul, almohada blanca, manta y cortina de privacidad
  - Lámpara individual por litera
  - Lockers verticales personales (2,5 m de alto) al fondo
- **Cocina a estribor (zona popa)**:
  - Encimera de acero inoxidable de 4 m
  - Horno con puerta translúcida
  - Frigorífico vertical
  - Fregadero con grifo
  - Armarios altos
- **Comedor a estribor (zona proa)**:
  - Mesa con dos bancos enfrentados y respaldos
  - Pantalla TV en pared
- **Foto mural babor**: pasillo de camarotes con cortinas azules y retrato de la Princesa Leonor

### 4. Sala de control / CIC · `x ∈ [4,5 ; 18,5]`
- **Suelo azul tipo alfombra** (replicando la foto de referencia)
- **Dos columnas de periscopio centrales** con visor inclinado y manetas
- **Fila estribor de 5 sillones de operador** (azul oscuro) con consolas inclinadas
- **Fila babor de 4 sillones de operador** cara a cara con la fila estribor
- Cada consola tiene pantalla con texto procedural ("S-80 SUBSYS RDY · SONAR · AIR · HVAC · NAV: 36°N 06°W · DEPTH: 110m") y teclado
- **Consola del timonel** con tres pantallas (verde/cyan/naranja) y dos volantes de buceo
- **Mesa de derrota** central iluminada al fondo
- **Pantalla mural grande** (2,7 × 1,5 m) en la pared de proa con la **foto del CIC real** del simulador del CESADAR
- **Foto mural babor**: oficial trabajando en consola
- **Foto mural estribor**: vista del puesto de control bajo «battle stations» (luz roja)

### 5. Camarotes oficiales / Aseos · `x ∈ [18,5 ; 28,5]`
- **3 camarotes individuales** con tabiques separadores a babor (litera + escritorio plegable + lámpara)
- **Camarote del Comandante** a estribor (más amplio, con cama doble, mesa de trabajo, silla)
- **Cartel «COMANDANTE»** amarillo en la entrada
- **Aseo individual** con cabina translúcida, WC y lavabo con grifo
- **Mesa de wardroom** central
- **Foto mural babor**: cartel «Todo por la Patria» con gorra «Isaac Peral S-81»
- **Foto mural estribor**: camarote individual con escritorio y silla azul

### 6. Sala de torpedos (proa) · `x ∈ [28,5 ; 40,5]`
- **6 torpedos en racks**: 3 a babor, 3 a estribor, en tres alturas
  - Cuerpo cilíndrico verde, **cabeza ojival ámbar**, 4 aletas en cruz, hélice trasera
- **6 tubos lanzatorpedos** numerados 1-6:
  - Tubo cilíndrico, compuerta culata circular, **volante rojo de cierre**, número en placa amarilla
- **Cabina de control de armas** con pantalla y botoneras roja/verde
- **Polipasto** (sistema de izado) anclado al techo
- **Rieles de carga** en el suelo

---

## Subsistemas simulados

El simulador es físicamente plausible (no es realismo de alta fidelidad). Cada `simTick(dt)` integra:

### Dinámica del buque
- **Suavizado ordered → actual**: rate 0,8 (timón), 0,7 (planos), 0,4 (motor)
- **Velocidad** = RPM × 0,06 (con 190 RPM ≈ 11 kn flank)
- **Heading** += rudder × |speed| × 0,04 × dt
- **Profundidad** -= (sternPlanes × |speed| × 0,012 + bowPlanes × |speed| × 0,008) × dt
- **Flotabilidad neta** = (50 - mediaMBT) × 0,06 m/s vertical
- **Presión** = 1,013 + profundidad × 0,0985 bar
- **Posición lat/lon** integrada por rumbo y velocidad (1 nm = 1,852 km)

### Atmósfera interna
- CO₂ sube +0,00006%/s, llega máximo a 0,5%
- O₂ baja -0,00004%/s, mínimo 18%
- Si AIP ON: O₂ +0,00010%/s, CO₂ -0,00012%/s, LOX -0,001%/s, etanol -0,0008%/s

### Energía
- **Carga batería**: solo si diésel ON + snorkel ON + profundidad < 8 m → +0,6%/s
- **Descarga**: ritmo base 0,02%/s + 0,0006 × |RPM motor|
- **Potencia eje** = |RPM motor| × 4,5 kW

### Sonar
- Cada 100 ms se desplaza la cascada 1 px hacia abajo
- Nueva fila: ruido aleatorio (0-35 verde) + tres pixeles brillantes en la demora de cada contacto, con color según confianza

### Armamento
- Cada tubo es una máquina de estados independiente
- Disparo: solo si hay tubo en OPEN. Marca FIRED durante 6 s, luego vuelve a EMPTY

---

## Imágenes de referencia

### Aplicadas como texturas en la app

| Imagen | Compartimento | Posición |
|---|---|---|
| `9fb562a5_*.png` | Sala de Control | Pared proa (pantalla mural 2,7×1,5 m) |
| `888o2f2o9oje1.webp` | Sala de Motores | Babor |
| `cztlnhym9oje1.webp` | Planta AIP | Babor (panel eléctrico) |
| `dkn9ka9k9oje1.webp` | Planta AIP | Estribor (consola Propulsión y Sistemas) |
| `onom4rqp9oje1.webp` | Camarotes tripulación | Babor (pasillo cortinas) |
| `ahlm4jrl9oje1.webp` | Sala de Control / CIC | Babor (consola oficial) |
| `mz7lr6gd9oje1.webp` | Sala de Control / CIC | Estribor (puesto de control) |
| `amad02oq9oje1.webp` | Camarotes oficiales | Babor («Todo por la Patria») |
| `s1t988zo9oje1.webp` | Camarotes oficiales | Estribor (camarote individual) |

### Disponibles en `S-80/` para consulta

- `s-80p-isaac-peral-class-submarine-cutaway-scaled-1-1.jpg` — corte transversal didáctico
- `S-80-con-las-nuevas-zonas-de-alargamiento.png` — esquema de las zonas de extensión
- `Portada-AIP-678x381.jpg` — render 3D de la planta AIP
- `Captura_de_pantalla_2025_05_07_181752_2c43cb8b1b.jpg` — render técnico de un compartimento
- `1437365079.webp`, `1437365096.webp` — fotos del simulador con timonel y CIC en luz roja
- `Submarino-S-81-Isaac-Peral-678x381.jpg`, `S81-27052022-678x381.jpeg` — fotos exteriores
- Otras: ilustraciones, comparativas con clase Rubis, etc.

---

## Arquitectura técnica

### Pila tecnológica

- **Three.js r128** (CDN cdnjs) — motor 3D WebGL
- **Vanilla JavaScript** — sin frameworks ni build system
- **HTML5** + **CSS3** (gradientes, animaciones, grid, flexbox)
- **Canvas 2D** — para los gauges, compás, sonar y texturas procedurales
- **WebGL Texture Loading** desde data-URIs base64 (sin XHR/CORS)

### Organización del código

```
S80v001.html
├── <head>
│   ├── <meta> viewport responsive
│   └── <style>
│       ├── Estilos generales (body, canvas, panels)
│       ├── Estilos del HUD legacy (log, info, controls táctiles)
│       └── ====== COCKPIT HUD ======
│           ├── #cockpit-hud (overlay raíz)
│           ├── .hud-panel (estilo común)
│           ├── #sonar-panel, #status-panel, #helm-panel...
│           ├── .gauge, .eot-btn, .helm-controls
│           ├── .led, .tube, .tank
│           └── @media responsive < 1380 px
├── <body>
│   ├── #log-panel (logs de sistema)
│   ├── #info-panel (datos buque, ubicación, coords)
│   ├── #toggleHull, #toggleSpeed, #hudToggleBtn, #teleport
│   ├── #crosshair, #hint
│   ├── #controls, #vcontrols (pads táctiles)
│   ├── #cockpit-hud (overlay del cockpit con 6 paneles)
│   └── <script> (~1300 líneas de JS)
│       ├── Constantes (HULL_LENGTH, FLOOR_Y, COMPARTMENTS...)
│       ├── INTERIOR_PHOTOS (8 fotos del álbum)
│       ├── TELEPORT_POINTS
│       ├── Helpers (mat, box, cyl, sphere, makeCanvasTexture)
│       ├── ====== SIMULADOR / COCKPIT ======
│       │   ├── simSub (state object)
│       │   ├── THROTTLE_RPM
│       │   ├── initHud()
│       │   ├── cycleTube(), fireWeapon()
│       │   ├── simTick(dt)
│       │   ├── updateHud()
│       │   ├── drawDialGauge()
│       │   ├── drawCompass()
│       │   ├── drawSonar()
│       │   └── setHudVisible()
│       ├── init()
│       ├── setupLighting(), buildEnvironment()
│       ├── buildHullExterior(), buildSail(), buildSternFeatures(), buildBowFeatures()
│       ├── buildInteriorBase()
│       ├── buildEngineRoom(), buildAIPRoom(), buildCrewQuarters()
│       ├── buildControlRoom(), buildOfficerQuarters(), buildTorpedoRoom()
│       ├── createPhotoPanel(), loadInteriorPhotos(), loadControlRoomTexture()
│       ├── spawnPlayer()
│       ├── setupControls() (mouse, keyboard, touch, hud, hull, speed, teleport)
│       └── animate() (loop principal: simTick + updateHud + render)

S80v001_assets.js
└── window.S80_PHOTOS = { '888o2f2o9oje1': 'data:image/webp;base64,...', ... }
```

### Bucle principal (`animate()`)

```text
requestAnimationFrame(animate)
├── dt = clock.getDelta()
├── Movimiento jugador (translateZ, translateX, position.y, rotation.y)
├── propeller.rotation.x += dt × (motorRpm × 0,025)
├── simTick(dt)              ← simulación física
├── updateHud()              ← redibujado de gauges, sonar, etc.
├── updateUI()               ← localización en panel info viejo
└── renderer.render(scene, camera)
```

### Sistema de coordenadas

- Eje **+X = proa**, **-X = popa**
- Eje **+Y = arriba**, **-Y = abajo**
- Eje **+Z / -Z = costados** (estribor / babor según convención adoptada en el código)
- Origen en el **centro geométrico del submarino**
- Mar a **y = 18 m**, lecho marino a **y = -25 m**

### Carga de imágenes sin servidor

El problema clásico: navegadores bloquean `XHR`/`fetch` desde `file://` por CORS. Solución implementada:

1. Las imágenes se preconvierten a base64 con PowerShell y se guardan en `S80v001_assets.js`
2. El HTML carga ese JS via `<script src="S80v001_assets.js">` (los scripts locales sí están permitidos)
3. `window.S80_PHOTOS` contiene un diccionario `{key: 'data:image/webp;base64,...'}`
4. El `THREE.TextureLoader` recibe los data-URIs directamente — no se hace ninguna petición de red, solo decodificación interna

---

## Personalización y extensión

### Añadir un nuevo compartimento

1. Modifica el array `COMPARTMENTS` en `S80v001.html`:
   ```js
   { x0: 4.5, x1: 18.5, name: 'Nuevo nombre' }
   ```
2. Crea una función `buildMiNuevoCompartimento(parent)` siguiendo el patrón de las existentes
3. Llámala desde `init()` después de las demás
4. Añade un destino de teletransporte en `TELEPORT_POINTS`

### Añadir una nueva foto mural

1. Coloca el `.webp` o `.png` en `S-80/`
2. Regenera `S80v001_assets.js` con el script PowerShell
3. Añade una entrada en `INTERIOR_PHOTOS`:
   ```js
   { key: 'mi_foto', compartment: 3, side: 'starboard', title: 'MI TÍTULO' }
   ```

### Cambiar el límite del timón o de los planos

```js
const simSub = {
    rudder:    { ordered: 0, actual: 0, max: 35 },  // ← cambiar max
    bowPlanes: { ordered: 0, actual: 0, max: 25 },  // ← cambiar max
    ...
};
```

### Añadir un nuevo gauge al puesto de maniobra

1. Añade un `<canvas>` en la sección `.gauges-row` del helm-panel
2. Crea su elemento `gauge-val` con un `id`
3. En `updateHud()` llama a `drawDialGauge('mi-gauge-id', valor, min, max, 'LBL')`
4. Y `setT('mi-val-id', formato(valor))`

### Añadir un nuevo subsistema de propulsión

1. Añade campo en `simSub` (ej. `solarPanels: { on: false, pct: 0 }`)
2. Añade fila en la `<table class="prop-table">` con LED + valor
3. Añade botón en `.prop-actions` con `data-sys="solar"`
4. En `initHud()` extiende el handler del botón
5. En `simTick()` integra el comportamiento físico
6. En `updateHud()` actualiza LED y valor mostrado

### Cambiar el modelo de submarino

Las constantes principales son:
```js
const HULL_LENGTH = 81.05;
const HULL_RADIUS = 3.65;
const FLOOR_Y = -0.95;
const CEIL_Y  =  1.20;
```

Modificarlas reescala automáticamente la geometría del casco y el interior. Habrá que ajustar las posiciones absolutas de equipamiento manualmente.

---

## Solución de problemas

### Las fotos del interior no se ven (cuadros vacíos azules)

- Verifica que `S80v001_assets.js` esté en la **misma carpeta** que `S80v001.html`
- Abre la consola del navegador (`F12`) y comprueba que se carga sin errores 404
- En el panel de log superior izquierdo debería aparecer «Imágenes embebidas: 9»

### El navegador no entra en pantalla completa con el ratón

- Pointer-lock necesita un gesto explícito. Haz **click sobre la zona 3D** (no sobre el HUD)
- Verifica que el navegador no esté bloqueando pointer-lock (algunos modos privados pueden hacerlo)
- Pulsa `ESC` antes de probar

### Rendimiento bajo (frames perdidos)

- Cierra otras pestañas pesadas
- Reduce la resolución del navegador o usa una ventana más pequeña
- Cambia a **Velocidad: Lenta** para reducir el ritmo de movimiento (no afecta a fps directamente)
- Apaga el cockpit HUD si es necesario (el redibujado de canvas tiene un coste)

### El simulador no responde a los botones del HUD

- Asegúrate de que `Cockpit HUD: ON`
- Algunos bloqueadores de pointer-events pueden interferir; comprueba la consola

### Three.js no carga (escena negra)

- Necesitas **conexión a Internet** la primera vez para descargar Three.js desde CDN
- Para uso 100% offline, descarga `three.min.js` localmente y cambia `<script src=…>` para apuntar al fichero local

### El submarino se hunde en el suelo / atraviesa paredes

Es **normal**: no hay detección de colisiones por diseño, para facilitar la exploración libre. Usa el teletransporte para volver a una zona conocida.

---

## Datos técnicos del S-80

| Parámetro | Valor |
|---|---|
| **Eslora total** | 81,05 m (S-81 original 71 m, alargado 10 m por corrección de estabilidad) |
| **Manga** | 7,3 m |
| **Calado** | 6,2 m |
| **Desplazamiento sumergido** | ~3.000 t |
| **Velocidad superficie** | ~12 kn |
| **Velocidad sumergido** | 19 kn (snorkeling), >20 kn (motor eléctrico) |
| **Profundidad máxima** | >300 m operativa |
| **Autonomía AIP** | ~3 semanas sumergido sin snorkel |
| **Autonomía total** | 50 días en patrulla |
| **Tripulación** | 32 + 8 operadores especiales |
| **Diésel** | 3 × MTU 16V 396 SE84 |
| **AIP** | Reformador de bioetanol + pila de combustible H₂/O₂ (LOX) |
| **Propulsión** | 1 motor eléctrico de imanes permanentes, 1 hélice de 7 palas |
| **Sistema combate** | SUBICS (Lockheed Martin) + SCOMBA (Navantia) — 7 consolas MFCC + LTD |
| **Consolas operador** | CONAN-SUB (SAINSEL) |
| **Sonar** | S-Cube (Thales) — array cilíndrico, flanco, hidrófono remolcado, intercept |
| **Armamento** | 6 tubos de 533 mm, 18 armas: torpedos DM2A4 SeaHake (Atlas Elektronik) y SUT, misiles Sub-Harpoon, minas, capacidad de operaciones especiales (DDS) |
| **Astillero** | Navantia, Cartagena |
| **Unidades** | S-81 Isaac Peral · S-82 Narciso Monturiol · S-83 Cosme García · S-84 Mateo García de los Reyes |

---

## Glosario

| Sigla | Significado |
|---|---|
| **AIP** | Air-Independent Propulsion (propulsión anaerobia) |
| **CFTT** | Cascade Frequency-Time-Tracking (en sonar) |
| **CIC** | Combat Information Center (Sala de control) |
| **CONAN-SUB** | Consola del fabricante SAINSEL para submarinos |
| **DM2A4** | Torpedo SeaHake mod 4 de Atlas Elektronik |
| **EOT** | Engine Order Telegraph (telégrafo de máquinas) |
| **ESM** | Electronic Support Measures (medidas electrónicas de apoyo) |
| **FC** | Fire Control (control de tiro) |
| **HUD** | Heads-Up Display (capa superpuesta con información) |
| **LOX** | Liquid Oxygen (oxígeno líquido) |
| **LTD** | Large Tactical Display (gran pantalla táctica del comandante) |
| **MBT** | Main Ballast Tank (tanque principal de lastre) |
| **MFCC** | Multi-Function Console (consola operativa multifunción del SUBICS) |
| **OOD** | Officer of the Deck / Oficial de cuarto |
| **PCP** | Permanent Magnet Motor / motor de imanes permanentes |
| **RPM** | Revoluciones por minuto |
| **SCOMBA** | Sistema de Combate (Navantia) |
| **SUBICS** | Submarine Integrated Combat System (Lockheed Martin) |
| **SUT** | Surface and Underwater Target (torpedo alemán) |

---

## Fuentes y referencias OSINT

### Documentación del S-80 / programa P-75I

- [Navantia commissions S-81 Isaac Peral submarine](https://www.navantia.es/en/news/press-releases/navantia-commissions-s-81-isaac-peral-submarine-to-the-spanish-navy/)
- [Navantia conducts S-81 combat system trials – Naval News](https://www.navalnews.com/naval-news/2021/08/navantia-conducts-spanish-submarine-s-81-isaac-peral-combat-system-trials/)
- [Navantia's S-80 Programme – Vanguard Canada](https://vanguardcanada.com/navantias-s-80-programme/)
- [The combat system of the S-80 submarines – Armies Magazine](https://www.armiesmagazine.com/2023/01/04/the-combat-system-of-the-s-80-submarines/)
- [S-80 Program – the choice of combat system – Revista Ejércitos](https://www.revistaejercitos.com/en/articulos/programa-s-80-la-eleccion-del-sistema-de-combate/)
- [Spain christens S-82 Narciso Monturiol – marineforum](https://marineforum.online/en/spain-baptises-s-82-narciso-monturiol/)
- [Spanish submarine S-81 begins first NATO patrol – Army Recognition](https://www.armyrecognition.com/news/navy-news/2025/spanish-submarine-s-81-isaac-peral-begins-first-nato-sea-guardian-patrol-in-mediterranean)
- [COVER STORY – Navantia's S-80 (s80.ca)](https://s80.ca/news/cover-story-navantias-s-80/)

### Sistemas de control de submarinos

- [Diving plane – Wikipedia](https://en.wikipedia.org/wiki/Diving_plane)
- [Submarine – Wikipedia](https://en.wikipedia.org/wiki/Submarine)
- [Submarine Hydraulic Systems – Chapter 5 (maritime.org)](https://maritime.org/doc/fleetsub/hydr/chap5.php)
- [The Fleet Type Submarine – Chapter 14 (maritime.org)](https://maritime.org/doc/fleetsub/chap14.php)
- [Diving Procedures – Fleet Submarine](https://fleetsubmarine.com/diving.html)
- [Ship Control Station – Smithsonian](https://americanhistory.si.edu/subs/operating/attackcenter/controlstation/index.html)
- [Ballast Control Panel – Smithsonian](https://americanhistory.si.edu/subs/operating/attackcenter/controlstation/ballastcontrol.html)
- [Submarine 101 – Rick Campbell](http://rickcampbellauthor.com/styled/index.html)
- [The "X-Factor" in Columbia-Class Submarine Design](https://www.globecomposite.com/blog/x-factor-columbia-class-submarine-design)

### Tecnologías 3D

- [Three.js documentation](https://threejs.org/docs/)
- [WebGL fundamentals](https://webglfundamentals.org/)

---

## Créditos y licencia

### Código

Aplicación desarrollada como prototipo de gemelo digital interactivo. Código JavaScript/HTML/CSS sin librerías externas más allá de Three.js.

### Imágenes

Las fotografías del directorio `S-80/` proceden de fuentes públicas de Internet (sitios de noticias militares, Reddit, prensa naval) y se incluyen con fines **demostrativos y educativos**. Los derechos pertenecen a sus autores originales (Armada Española, Navantia, fotógrafos de prensa). Si eres titular de derechos y necesitas que se retire alguna imagen, contacta con el responsable del proyecto.

### Marca y derechos del S-80

«S-80», «Isaac Peral», «Narciso Monturiol», «Cosme García» y «Mateo García de los Reyes» son nombres de buques de la Armada Española. SUBICS es marca registrada de Lockheed Martin. CONAN-SUB es marca registrada de SAINSEL. SCOMBA es marca registrada de Navantia. MTU 16V 396 SE84 es marca registrada de Rolls-Royce Power Systems.

Este proyecto es una **representación didáctica no oficial** y no está afiliado a Navantia, la Armada Española ni ningún otro organismo oficial.

### Versión

Build `S80v001` — abril 2026

---

> *«Todo por la Patria»* — Lema histórico de la Armada Española, presente en los camarotes del S-81 Isaac Peral.

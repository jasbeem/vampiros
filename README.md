# Cacería de Vampiros (PeerJS)

App web de deducción social para aula. Un **profesor (admin)** crea una aldea y comparte un **código de 5 dígitos** o un **enlace directo**. Los **alumnos (aldeanos)** se conectan y juegan en tiempo real. La comunicación es **P2P con PeerJS**.

## Requisitos

- Node.js 18+ (recomendado)
- npm 9+

## Instalación

```bash
npm install
```

## Desarrollo local

```bash
npm run dev
```

Abre la URL que te muestre Vite.

## Build de producción (detallado)

### 1) Generar el build

```bash
npm run build
```

### 2) Qué genera exactamente

El build crea la carpeta `dist/` lista para servir como hosting estático. Contenido típico:

- `dist/index.html`
- `dist/assets/*.js` (bundles con hash)
- `dist/assets/*.css` (estilos con hash)
- `dist/audio/*.mp3` (copiados desde `public/audio/`)

Notas:

- Los nombres de `assets` cambian por el hash en cada build.
- Los audios se copian sin transformación.

### 3) Verificar el build localmente

```bash
npm run preview
```

Esto sirve `dist/` en local para comprobar que funciona igual que en producción.

### 4) Despliegue

Cualquier hosting estático sirve (Netlify, Vercel, GitHub Pages, etc.).

Pasos mínimos:

1. Ejecuta el build: `npm run build`
2. Sube el contenido de `dist/`

## Flujo de juego

### 1) Profesor (admin)

- Pulsa **“Fundar nueva aldea”**.
- Se genera un **código numérico aleatorio de 5 dígitos**.
- El código se muestra siempre en el panel del admin.
- Al pulsar el código, se abre a pantalla completa y se **copia el enlace** (`/?room=XXXXX`).
- Importa el banco de palabras (CSV/TXT) y selecciona:
  - **Asignatura**
  - **Situación de aprendizaje**
  - **Nivel de dificultad**
- El sistema elige **aleatoriamente** una pareja de palabras (aldeano/vampiro) del filtro.
- Pulsa **“Cae la noche (Asignar Roles)”** para iniciar.

### 2) Alumnos (aldeanos)

- Entran con el **código** o el **enlace directo**.
- Completan nombre y avatar.
- El admin aprueba entradas si la partida ya está en curso.

### 3) Revelación de roles (30s)

- Tras iniciar, se muestra **solo un mensaje y un contador** durante 30s.
- No aparecen botones ni modales extra.
- Después de 30s comienza la ronda automáticamente.

### 4) Ronda de palabras y votación

- Los jugadores escriben su palabra.
- Al votar, se elimina a un jugador y se evalúan condiciones de victoria.

## Reglas de victoria

- Si queda **1 aldeano y 1 vampiro**, ganan los vampiros.
- Si no quedan aldeanos, ganan los vampiros.
- Si no quedan vampiros, se entra en **VAMPIRE_MEET** para el último intento.

## VAMPIRE_MEET (último intento)

- Entra cuando **no quedan vampiros vivos**.
- Si hay **más de un vampiro**, todos escriben una propuesta y luego votan.
- La votación solo se habilita cuando todos los vampiros han propuesto.
- Si hay **un solo vampiro**, escribe una palabra y se verifica directamente.
- Si la palabra coincide con la de los aldeanos → **ganan los vampiros**.
- Si falla o hay empate en la votación → **ganan los aldeanos**.
- Vampiros muertos pueden participar en la votación.

## Audio (panel admin)

- El reproductor solo se muestra al admin **después de iniciar la partida**.
- Controles visibles: anterior, play/pausa, siguiente, nombre de pista y volumen.
- Nombres visibles: `pista1`, `pista2`, `pista3`, etc.

Archivos esperados en `public/audio/`:

- `background.mp3`
- `cyber.mp3`
- `ganador01.mp3`
- `ganador02.mp3`
- `music.mp3`
- `track1.mp3`
- `track2.mp3`
- `track3.mp3`
- `track4.mp3`
- `track5.mp3`

## Formato del archivo de importación

El archivo debe ser `.csv` o `.txt` con separador **punto y coma**. Cada línea tiene 5 campos:

```
Asignatura;Situación;Dificultad;PalabraAldeano;PalabraVampiro
```

Ejemplo:

```
Digitalización;Creación digital;fácil;JPG;Imagen
Digitalización;Creación digital;medio;H.264;Códec
Digitalización;Creación digital;difícil;Lossless;Compresión sin pérdida
```

Selección automática:

- Si solo existe **una asignatura**, se selecciona automáticamente.
- Si solo existe **una situación**, se selecciona automáticamente.
- Si solo existe **un nivel**, se selecciona automáticamente.

## Notas

- La app **no funciona con `file://`**. Usa `npm run dev`, `npm run preview` o un hosting.
- El enlace directo usa `/?room=XXXXX` para precargar el código.

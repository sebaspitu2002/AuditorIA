# Cómo construir tu Auditor Inteligente de Expedientes Inmobiliarios

*Guía paso a paso para principiantes*

De cero conocimientos de programación a un MVP que cobra por uso, con objetivos claros, commits sugeridos y el porqué de cada paso clave.

## 1. Qué vas a construir

Una aplicación web donde un agente inmobiliario **sube los documentos de una vivienda** (Nota Simple, certificado catastral, recibo del IBI), una inteligencia artificial **lee** esos documentos, un programa **detecta lo que no cuadra** (superficies distintas, titulares que no coinciden, cargas vigentes, documentos que faltan) y el agente **paga unos 9 €** para ver el informe completo en formato semáforo.

Está pensada para agentes independientes y pequeñas agencias de España que cierran pocas operaciones al mes: sin suscripción, sin llamada de demostración, valor en los primeros minutos.

> **Cómo usar esta guía**
>
> **Sigue las fases en orden.** Cada una termina con algo que puedes comprobar con tus propios ojos, así sabes que avanzas.
>
> **No intentes entenderlo todo antes de empezar.** En programación se aprende haciendo: copia, ejecuta, rompe, arregla.
>
> **Cuando te atasques,** copia el mensaje de error completo y pide ayuda explicando qué querías conseguir y qué pasó.
>
> **Haz un commit cada vez que termines un paso pequeño.** Es tu red de seguridad.

## Mapa general del proyecto

Los tiempos son orientativos, para alguien que empieza de cero y dedica 10-15 horas a la semana.

| **Fase** | **Qué construyes**                                                     | **Tiempo orientativo** |
|----------|------------------------------------------------------------------------|------------------------|
| 0        | Tu entorno de trabajo: herramientas instaladas y repositorio en GitHub | 2-3 días               |
| 1        | Motor de extracción: un PDF entra, un JSON limpio sale                 | 2-3 semanas            |
| 2        | Motor de cruce y checklist: detecta discrepancias y faltantes          | 1-2 semanas            |
| 3        | API y base de datos: el “cerebro” accesible por internet               | 2-3 semanas            |
| 4        | Login y pantallas: lo que ve el agente inmobiliario                    | 2-3 semanas            |
| 5        | Pagos con Stripe: el informe completo se desbloquea al pagar           | 1 semana               |
| 6        | Seguridad, RGPD y publicación en internet                              | 1-2 semanas            |
| 7        | Prueba con agentes reales y mejora                                     | Continuo               |

## 2. Vocabulario básico

Estas palabras aparecerán todo el rato. No hace falta memorizarlas: vuelve aquí cuando algo no te suene.

| **Término**                | **Qué significa (en cristiano)**                                                                                                                   |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Terminal                   | Ventana donde escribes órdenes al ordenador con texto en lugar de hacer clic. Al principio impone, pero es más rápido y es donde ocurre casi todo. |
| Repositorio                | La carpeta de tu proyecto, con todo su historial de cambios guardado. Se aloja en GitHub para tener copia en la nube.                              |
| Git                        | Programa que guarda “fotos” de tu proyecto a lo largo del tiempo. Si algo se rompe, puedes volver a una foto anterior.                             |
| Commit                     | Una de esas fotos, con un mensaje que explica qué cambió. Es la unidad básica de tu progreso.                                                      |
| Push                       | Enviar tus commits desde tu ordenador a GitHub.                                                                                                    |
| Rama (branch)              | Una línea de trabajo paralela. Trabajas en una rama por fase y la unes a la principal (main) cuando funciona.                                      |
| Frontend                   | Lo que el usuario ve y toca: botones, pantallas, formularios. Aquí se hace con Next.js.                                                            |
| Backend                    | La “trastienda” que hace el trabajo pesado: leer PDFs, llamar a la IA, guardar datos. Aquí se hace con Python y FastAPI.                           |
| API / endpoint             | La puerta por la que el frontend pide cosas al backend. Es como un camarero que lleva pedidos a la cocina y trae los platos.                       |
| Base de datos              | Donde se guarda la información de forma ordenada (usuarios, expedientes, pagos). Aquí usarás PostgreSQL.                                           |
| LLM                        | El modelo de lenguaje de IA (por ejemplo, GPT-4o-mini o DeepSeek) que “lee” el texto de los documentos y responde.                                 |
| JSON                       | Formato de texto para ordenar datos en pares “campo: valor”. Es el idioma común entre programas.                                                   |
| Pydantic                   | Librería de Python que actúa como plantilla: define qué campos debe tener una respuesta y rechaza lo que no encaje.                                |
| Variable de entorno (.env) | Archivo local donde guardas secretos (claves de API, contraseñas) para que nunca aparezcan en el código.                                           |
| Entorno virtual            | Una “caja” de Python aislada para cada proyecto, de modo que las librerías de uno no se mezclan con las de otro.                                   |
| Test                       | Un pequeño programa que comprueba automáticamente que tu código hace lo que debe.                                                                  |
| Webhook                    | Un aviso automático: cuando alguien paga, Stripe “llama por teléfono” a tu backend para decírtelo.                                                 |
| Despliegue (deploy)        | Publicar tu aplicación en internet para que otras personas puedan usarla.                                                                          |
| RGPD                       | Reglamento europeo de protección de datos. Importa mucho aquí porque manejarás nombres, DNI y datos de propiedad.                                  |

## 3. Git y commits en 5 minutos

> **Por qué es importante**
>
```text
Los commits son tu red de seguridad y tu diario de progreso. Si haces un cambio y todo deja de funcionar, puedes volver al último commit bueno. Además, un historial claro te permite (a ti dentro de tres meses, o a otra persona) entender qué se hizo y por qué.
```

### El ciclo básico

Cada vez que termines un paso pequeño que funcione, repite estas cuatro órdenes en la terminal:

```bash
git status # ver qué archivos has cambiado
git add . # preparar todos los cambios
git commit -m "feat: mensaje" # guardar la foto con un mensaje
git push # subirla a GitHub
```

### Cómo escribir los mensajes

Usamos un estilo llamado **Conventional Commits**: un prefijo que indica el tipo de cambio, seguido de una frase corta en presente que diga qué hace el commit.

| **Prefijo** | **Cuándo usarlo**                           | **Ejemplo**                           |
|-------------|---------------------------------------------|---------------------------------------|
| feat        | Añades algo nuevo que funciona              | feat(cruce): compara superficies      |
| fix         | Arreglas un error                           | fix(cruce): corrige división por cero |
| chore       | Tareas de mantenimiento o configuración     | chore: añade .gitignore               |
| docs        | Cambias documentación o README              | docs: explica cómo arrancar           |
| test        | Añades o cambias pruebas                    | test(cruce): casos de nombres         |
| refactor    | Reordenas código sin cambiar lo que hace    | refactor: separa funciones            |
| ci          | Automatizaciones (comprobaciones en GitHub) | ci: lanza tests en cada push          |

### Reglas de oro

- **Un commit = una idea.** Si tu mensaje necesita la palabra “y”, probablemente son dos commits.

- **Commits pequeños y frecuentes** mejor que uno gigante al final del día.

- **Nunca subas secretos.** El archivo .env, las claves de API y los PDFs reales de clientes deben estar en .gitignore.

- **Una rama por fase:** git switch -c fase-1-extraccion. Cuando la fase funciona, la unes a main desde GitHub (Pull Request) y le pones una etiqueta de versión, por ejemplo v0.1.

> **Si subes una clave por error**
>
```text
Bórrala del código no basta: el historial de Git la conserva. Entra en el panel del proveedor y **revoca esa clave y genera una nueva** de inmediato.
```

## Fase 0: Preparar tu entorno

> **Qué vas a lograr**
>
```text
Tener instaladas todas las herramientas y un repositorio en GitHub con la estructura básica del proyecto y tu primer commit subido.
```

> **Por qué es un paso clave**
>
```text
Es como montar el taller antes de fabricar. La mayoría de los bloqueos de un principiante no son de programación, sino de instalación y configuración.
Además, empezar con Git desde el minuto uno te da la costumbre de guardar el progreso y te protege de perder trabajo.
```

### Paso a paso

1.  Crea una cuenta gratuita en **GitHub** (github.com).

2.  Instala **Visual Studio Code**, el editor donde escribirás el código.

3.  Instala **Git** y configura tu identidad: git config --global user.name "Tu Nombre" y git config --global user.email "tu@correo.com".

4.  Instala **Python** (versión 3.12 o superior) y comprueba con python --version que responde.

5.  Instala **Node.js** (versión LTS) y comprueba con node --version.

6.  Instala **Docker Desktop**: te permitirá tener una base de datos PostgreSQL local sin instalarla a mano.

7.  En GitHub crea un repositorio llamado auditor-expedientes y clónalo en tu ordenador con git clone.

8.  Dentro crea las carpetas backend, frontend y docs, y un archivo README.md con dos líneas explicando el proyecto.

9.  Crea un archivo .gitignore que excluya .env, .venv, node_modules y la carpeta de PDFs de prueba.

10. Haz tu primer commit y git push. Comprueba en GitHub que los archivos aparecen.

### Commits sugeridos

Haz un commit al terminar cada bloque de trabajo. Estos mensajes son una guía; adáptalos a lo que hayas hecho de verdad.

```text
chore: inicializa monorepo con carpetas backend, frontend y docs
docs: añade README con descripción del proyecto
chore: añade .gitignore y .env.example
```

> **Cómo sabes que has terminado**
>
```text
Los comandos python --version, node --version, git --version y docker --version responden sin errores.
Tu repositorio en GitHub muestra las carpetas y tus commits.
```

### Errores frecuentes

- Instalar varias versiones de Python y no saber cuál se está usando.

- Olvidar el .gitignore y subir accidentalmente archivos con secretos.

- Saltarse la comprobación de cada instalación: si algo falla ahora, fallará todo después.

## Fase 1: Motor de extracción (el corazón del negocio)

> **Qué vas a lograr**
>
```text
Un programa aislado que recibe el PDF de una Nota Simple y devuelve un JSON limpio con titulares, DNI, superficie y cargas vigentes. Todavía sin pantallas ni base de datos.
```

> **Por qué es un paso clave**
>
```text
Este es el mayor riesgo técnico del proyecto. Si la IA lee mal una Nota Simple, el informe será incorrecto y ningún agente pagará por él (ni volverá a confiar).
Por eso se hace primero y de forma aislada: es mucho más barato descubrir ahora que un prompt falla que después de haber construido pantallas y pagos encima.
Aquí además creas tu “examen” de calidad: un conjunto de documentos de prueba con las respuestas correctas, para medir con números si mejoras o empeoras.
```

### Conceptos que necesitas

- **Nota Simple:** documento informativo del Registro de la Propiedad con titulares, descripción de la finca y cargas (hipotecas, embargos…).

- **PDF nativo vs. escaneado:** en uno el texto se puede copiar; en el otro es una foto. Para la primera versión, trabaja solo con PDFs nativos.

- **Pydantic:** define la “plantilla” de respuesta y obliga a la IA a ceñirse a ella, lo que reduce que se invente datos.

- **Prompt:** las instrucciones que le das a la IA. Pedirle que devuelva solo cargas vigentes y solo JSON es parte de su trabajo.

### Paso a paso

1.  Entra en backend y crea un entorno virtual: python -m venv .venv. Actívalo (en Windows .venv\Scripts\activate, en Mac/Linux source .venv/bin/activate).

2.  Instala las librerías: pip install pymupdf pydantic openai python-dotenv. (La librería openai también sirve para DeepSeek, que usa el mismo formato.)

3.  Reúne **10-15 documentos de prueba, anonimizados** (tapa nombres y DNI reales o usa ejemplos). Guárdalos en una carpeta ignorada por Git.

4.  Crea los modelos Pydantic de la Nota Simple y del certificado catastral (ver ejemplo más abajo).

5.  Escribe la función que extrae el texto del PDF con PyMuPDF. Si sale vacío, el PDF es un escaneo: devuelve un error claro.

6.  Consigue una clave de API del proveedor de IA que elijas y guárdala en un archivo .env (nunca en el código).

7.  Escribe el primer prompt: “Eres un oficial de notaría español. Extrae la información del texto y devuélvela en el JSON indicado. Ignora las cargas canceladas.”

8.  Llama a la IA, recibe la respuesta y **valídala con Pydantic**. Si no cumple la plantilla, reintenta una vez más.

9.  Crea el “examen”: para cada PDF de prueba escribe a mano el JSON correcto y programa un script que compare y calcule el porcentaje de acierto por campo.

10. Mejora el prompt por iteraciones hasta superar el 95 % en titulares, superficie y cargas.

### Ejemplo: la plantilla (Pydantic)

Cada campo que declares es un campo que la IA debe rellenar. Los que pueden faltar llevan \| None.

```python
from pydantic import BaseModel

class Titular(BaseModel):
    nombre: str
    dni: str | None = None
    porcentaje: float | None = None

class Carga(BaseModel):
    tipo: str  # hipoteca, embargo, servidumbre...
    descripcion: str

class NotaSimpleSchema(BaseModel):
    referencia_catastral: str | None = None
    superficie_m2: float | None = None
    titulares: list[Titular]
    cargas_vigentes: list[Carga]
```

### Ejemplo: extraer el texto de un PDF

```python
import fitz  # así se importa PyMuPDF

def extraer_texto(ruta_pdf):
    with fitz.open(ruta_pdf) as doc:
        return "".join(pagina.get_text() for pagina in doc)
```

### Commits sugeridos

Haz un commit al terminar cada bloque de trabajo. Estos mensajes son una guía; adáptalos a lo que hayas hecho de verdad.

```text
chore(backend): crea entorno virtual y dependencias de extracción
feat(extraccion): modelos Pydantic NotaSimpleSchema y CatastroSchema
feat(extraccion): extractor de texto con PyMuPDF
feat(extraccion): detecta PDF escaneado y devuelve error controlado
feat(extraccion): cliente de IA con salida JSON y reintento
feat(extraccion): prompt v1 para Nota Simple (solo cargas vigentes)
feat(extraccion): prompt v1 para certificado catastral
test(extraccion): dataset anonimizado con respuestas esperadas
test(extraccion): script de evaluación con precisión por campo
refactor(extraccion): mejora prompt tras evaluación (>95 %)
```

> **Cómo sabes que has terminado**
>
```text
Ejecutas un comando con un PDF y en pantalla aparece un JSON con titulares, DNI, superficie y cargas vigentes.
Tu script de evaluación muestra al menos un 95 % de acierto en los campos importantes.
```

### Errores frecuentes

- Subir PDFs reales de clientes al repositorio: son datos personales. Anonimiza y excluye la carpeta con .gitignore.

- Pegar la clave de API directamente en el código en lugar de usar .env.

- Dar por bueno el prompt tras probarlo con solo dos documentos. Por eso existe el “examen” con 10-15 casos.

- Querer resolver los escaneos (OCR) ya en esta fase: déjalo para más adelante.

## Fase 2: Motor de cruce y checklist normativo (lo que se cobra)

> **Qué vas a lograr**
>
```text
Un módulo que compara los datos extraídos de los distintos documentos y produce un informe con incidencias (rojo, amarillo, verde) y una lista de documentos que faltan según la Comunidad Autónoma.
```

> **Por qué es un paso clave**
>
```text
Aquí está el valor por el que el cliente paga. Leer un PDF ya lo hace cualquiera con una IA; lo que ahorra tiempo y disgustos es que alguien le diga “la Nota Simple dice 80 m² y el Catastro 110 m²: esto hay que arreglarlo antes de vender”.
Esta parte se programa con reglas normales (si… entonces…), **sin IA**. Eso la hace predecible, barata y fácil de comprobar con tests: dada la misma entrada, siempre sale el mismo resultado.
```

### Conceptos que necesitas

- **Incidencia:** un hallazgo con nivel (rojo, amarillo o verde), un código y un mensaje comprensible.

- **Umbral:** el límite a partir del cual algo se considera problema (por ejemplo, más de un 10 % de diferencia en superficie).

- **Normalizar:** limpiar textos antes de compararlos (quitar acentos, pasar a minúsculas, convertir “Mª” en “maria”) para que “Mª del Carmen” y “Maria Carmen” se reconozcan como parecidos.

- **Test automático:** programa que comprueba tu función con casos conocidos. Con pytest lo ejecutas en segundos.

### Paso a paso

1.  Crea la carpeta cruce en el backend y define el modelo Incidencia (nivel, código, mensaje).

2.  Programa comparar_superficies con un umbral configurable (ver ejemplo).

3.  Programa la normalización de nombres y compáralos con la librería rapidfuzz, que mide qué tan parecidos son dos textos.

4.  Añade la validación del formato de DNI/NIE (número y letra de control).

5.  Resume las cargas vigentes agrupadas por tipo: hipotecas, embargos, afecciones fiscales, servidumbres, usufructos.

6.  Crea un archivo JSON con el checklist por Comunidad Autónoma (certificado de eficiencia energética, cédula de habitabilidad donde proceda, último recibo del IBI, certificado de deudas de la comunidad de propietarios…). **Verifica los requisitos de cada comunidad en fuentes oficiales** antes de darlos por buenos.

7.  Programa el generador del Informe: incidencias, documentos faltantes y porcentaje de completitud del expediente.

8.  Instala pytest y escribe tests para los casos límite: nombres casi iguales, superficies justo en el umbral, expediente sin cargas.

### Ejemplo: comparar superficies

```python
def comparar_superficies(registro, catastro, umbral=0.10):
    diferencia = abs(registro - catastro) / max(registro, catastro)
    if diferencia > umbral:
        return ("rojo", f"Registro: {registro} m2, Catastro: {catastro} m2")
    return ("verde", "Las superficies coinciden")
```

### Commits sugeridos

Haz un commit al terminar cada bloque de trabajo. Estos mensajes son una guía; adáptalos a lo que hayas hecho de verdad.

```text
feat(cruce): modelo Incidencia con nivel, código y mensaje
feat(cruce): comparador de superficies con umbral configurable
feat(cruce): normalizador de nombres y comparación por similitud
feat(cruce): validación de formato de DNI/NIE
feat(cruce): resumen de cargas vigentes por tipo
feat(checklist): reglas por Comunidad Autónoma en JSON
feat(cruce): generador de Informe con incidencias y faltantes
test(cruce): casos límite de nombres, superficies y cargas
```

> **Cómo sabes que has terminado**
>
```text
Le das dos JSON (Nota Simple y Catastro) y devuelve un Informe con incidencias y documentos faltantes coherentes.
Al ejecutar pytest todos los tests salen en verde.
```

### Errores frecuentes

- Usar la IA para decidir si “hay discrepancia”. Es más fiable, barato y comprobable hacerlo con código normal.

- Comparar nombres letra a letra sin normalizar: dará falsas alarmas constantes.

- Dar por válidos los requisitos por comunidad sin contrastarlos con fuentes oficiales.

## Fase 3: API y base de datos

> **Qué vas a lograr**
>
```text
Un servidor FastAPI con endpoints para crear expedientes, subir documentos, lanzar el análisis y consultar el informe y su progreso, guardando todo en PostgreSQL y los PDFs en almacenamiento en la nube.
```

> **Por qué es un paso clave**
>
```text
Hasta ahora todo eran scripts que solo tú ejecutabas en tu terminal. La API convierte tu trabajo en un servicio al que se puede llamar desde una web, desde el móvil o desde cualquier otro programa.
La base de datos da memoria al sistema: qué usuario tiene qué expediente, en qué estado está y si ya pagó. Sin ella no puedes cobrar ni desbloquear informes de forma fiable.
```

### Conceptos que necesitas

- **Endpoint:** una dirección concreta de la API, como /api/v1/expedientes, que hace una tarea.

- **Migración:** un archivo que describe un cambio en la estructura de la base de datos. Herramientas como Alembic las aplican en orden, como un historial de commits para tus tablas.

- **Almacenamiento de objetos (S3 / R2):** un “disco duro en la nube” pensado para guardar archivos. Los PDFs no se guardan en la base de datos.

- **Proceso asíncrono:** el análisis tarda 10-20 segundos; el servidor debe poder informar del progreso sin bloquearse.

### Paso a paso

1.  Crea un docker-compose.yml que arranque PostgreSQL en local y comprueba que puedes conectarte.

2.  Instala fastapi, uvicorn, sqlalchemy, alembic y psycopg. Crea un primer endpoint de prueba (ver ejemplo) y ábrelo en el navegador.

3.  Define las tablas: usuario, expediente, documento, informe y pago. Genera la primera migración con Alembic y aplícala.

4.  Crea el endpoint para **crear un expediente**.

5.  Crea el endpoint para **subir varios PDFs** a la vez, con validaciones de tipo, tamaño y número máximo de archivos.

6.  Guarda los PDFs en un bucket de Cloudflare R2 o S3 (en desarrollo, puedes empezar en una carpeta local).

7.  Añade un clasificador automático: la IA indica si cada archivo es Nota Simple, Catastro, IBI u otro, para que el usuario no tenga que etiquetarlos.

8.  Une las piezas: extraer → cruzar → guardar el informe.

9.  Añade el endpoint de **progreso** (mensajes como “Leyendo Nota Simple…”, “Cruzando con Catastro…”).

10. Escribe tests de integración del flujo completo y pruébalo con la documentación automática de FastAPI en /docs.

### Ejemplo: tu primer endpoint

Guárdalo como main.py y ejecútalo con uvicorn main:app --reload. Luego abre http://127.0.0.1:8000/docs.

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/salud")
def salud():
    return {"estado": "ok"}
```

### Commits sugeridos

Haz un commit al terminar cada bloque de trabajo. Estos mensajes son una guía; adáptalos a lo que hayas hecho de verdad.

```text
chore: añade docker-compose con PostgreSQL
feat(api): app FastAPI con endpoint de salud
feat(db): modelos de usuario, expediente, documento, informe y pago
feat(db): migración inicial con Alembic
feat(api): endpoint para crear expedientes
feat(api): subida múltiple de PDFs con validaciones
feat(storage): guarda PDFs en almacenamiento de objetos
feat(api): clasificador automático del tipo de documento
feat(api): pipeline extracción, cruce e informe
feat(api): endpoint de progreso del análisis
test(api): pruebas de integración del flujo completo
```

> **Cómo sabes que has terminado**
>
```text
Con la documentación /docs (o con curl) creas un expediente, subes 3 PDFs y recibes un informe guardado en la base de datos.
El endpoint de progreso muestra en qué paso va el análisis.
```

### Errores frecuentes

- Guardar los PDFs dentro de la base de datos en vez de en almacenamiento de archivos.

- No poner límites de tamaño y de cantidad de archivos: alguien podría subir ficheros enormes y disparar tu coste.

- Modificar las tablas “a mano” sin migraciones: te costará reproducir el entorno en producción.

## Fase 4: Login y pantallas (la experiencia de usuario)

> **Qué vas a lograr**
>
```text
Una web Next.js donde el usuario entra con Google, pulsa “Crear nuevo expediente”, arrastra sus PDFs, ve el progreso en directo y recibe el informe con tarjetas de semáforo.
```

> **Por qué es un paso clave**
>
```text
El agente inmobiliario nunca verá tu código: verá esta interfaz. Si no entiende qué hacer en 5 minutos, abandonará aunque el motor sea perfecto.
La “fricción cero” del modelo de negocio se gana aquí: registro con un clic, un botón gigante, arrastrar y soltar sin clasificar, y resultados visuales en lugar de texto denso.
```

### Conceptos que necesitas

- **Componente:** una pieza reutilizable de pantalla (un botón, una tarjeta, la zona de arrastrar y soltar).

- **Tailwind CSS:** forma de dar estilo escribiendo clases cortas directamente en los elementos.

- **Autenticación:** comprobar quién es el usuario. Con Google OAuth o enlace mágico no gestionas contraseñas, lo que es más simple y seguro.

- **Progreso en tiempo real:** el backend envía mensajes al navegador mientras trabaja (Server-Sent Events) para que el usuario no se quede mirando una pantalla en blanco.

### Paso a paso

1.  En la carpeta frontend crea la app con npx create-next-app@latest (activa TypeScript y Tailwind) y arráncala con npm run dev.

2.  Implementa el login con Google y, opcionalmente, con enlace por correo. Haz que el backend valide la sesión en cada petición.

3.  Crea el panel vacío con un único botón grande: “Crear nuevo expediente”.

4.  Construye el componente de arrastrar y soltar para varios PDFs y conéctalo al endpoint de subida.

5.  Crea la pantalla de progreso conectada al endpoint de progreso (“Leyendo Nota Simple…”).

6.  Diseña la vista del expediente con tarjetas de semáforo: rojo (discrepancia grave), amarillo (falta un documento), verde (todo correcto) y una barra “Expediente al 60 %”.

7.  Gestiona los errores con mensajes claros (PDF escaneado, archivo demasiado grande, fallo de la IA).

8.  Revisa cómo se ve en móvil: muchos agentes lo usarán desde el teléfono.

### Commits sugeridos

Haz un commit al terminar cada bloque de trabajo. Estos mensajes son una guía; adáptalos a lo que hayas hecho de verdad.

```text
chore(frontend): crea app Next.js con Tailwind
feat(auth): login con Google
feat(auth): validación de sesión en el backend
feat(ui): panel con botón Crear nuevo expediente
feat(ui): zona de arrastrar y soltar para varios PDFs
feat(ui): pantalla de progreso en tiempo real
feat(ui): tarjetas de semáforo y barra de completitud
feat(ui): mensajes de error comprensibles
style(ui): adapta las pantallas a móvil
```

> **Cómo sabes que has terminado**
>
```text
Una persona ajena al proyecto entra, sube documentos y entiende el informe sin que le expliques nada.
Todo el flujo funciona en tu ordenador de extremo a extremo, también en la pantalla del móvil.
```

### Errores frecuentes

- Diseñar demasiadas pantallas: para el MVP basta con login, panel, subida y expediente.

- Dejar al usuario sin feedback durante los 10-20 segundos de análisis.

- Probarlo solo tú mismo: quien conoce el producto no ve los obstáculos que verá un usuario nuevo.

## Fase 5: Pagos con Stripe (modelo freemium)

> **Qué vas a lograr**
>
```text
El usuario sube documentos gratis, ve un resumen (“2 discrepancias graves y 1 carga vigente”) y paga unos 9 € para desbloquear el detalle completo. El backend registra el pago y libera el informe.
```

> **Por qué es un paso clave**
>
```text
Sin este paso tienes una herramienta, no un negocio. El modelo freemium permite que el usuario compruebe el valor antes de pagar, pero el detalle solo se entrega tras el cobro.
El punto más delicado es la seguridad: **el detalle debe bloquearse en el backend**. Si envías todo el informe al navegador y solo lo difuminas con CSS, cualquiera puede leerlo con las herramientas del navegador sin pagar.
```

### Conceptos que necesitas

- **Stripe Checkout:** página de pago alojada por Stripe. Tú no tocas datos de tarjetas, lo que simplifica la seguridad y el cumplimiento.

- **Webhook:** aviso automático de Stripe a tu backend cuando el pago se completa (evento checkout.session.completed). Es la fuente de verdad de que el pago ocurrió.

- **Idempotencia:** que procesar dos veces el mismo aviso no cause dos activaciones ni dos créditos.

- **Modo de pruebas:** Stripe permite simular pagos sin dinero real con tarjetas de prueba.

### Paso a paso

1.  Crea una cuenta en Stripe y trabaja en **modo de pruebas** durante todo el desarrollo.

2.  Crea el producto “Auditoría completa de inmueble” con su precio.

3.  Crea un endpoint en el backend que genere una sesión de Checkout ligada al expediente.

4.  Crea el endpoint del webhook. **Verifica siempre la firma** del aviso para asegurarte de que viene realmente de Stripe.

5.  Al recibir el pago, marca el expediente como pagado en la base de datos. Protege este paso contra avisos duplicados.

6.  Modifica la API para que devuelva el detalle completo **solo si** el expediente está pagado; si no, solo el resumen.

7.  En la web, muestra el resumen gratuito, el detalle bloqueado y el botón “Desbloquear informe completo”, más una pantalla de éxito.

8.  Prueba todo el flujo con la Stripe CLI y una tarjeta de prueba (por ejemplo, 4242 4242 4242 4242).

9.  Cuando el flujo básico funcione, añade los bonos prepago (por ejemplo, 5 expedientes por 39 €) con un saldo de créditos.

### Commits sugeridos

Haz un commit al terminar cada bloque de trabajo. Estos mensajes son una guía; adáptalos a lo que hayas hecho de verdad.

```text
feat(pagos): producto y precio de auditoría en Stripe
feat(pagos): endpoint que crea la sesión de Checkout
feat(pagos): webhook con verificación de firma
feat(pagos): control de idempotencia del webhook
feat(api): informe completo solo para expedientes pagados
feat(ui): resumen gratuito y detalle bloqueado
feat(ui): botón de desbloqueo y pantalla de éxito
feat(pagos): bonos prepago con saldo de créditos
test(pagos): flujo completo en modo de pruebas
```

> **Cómo sabes que has terminado**
>
```text
En modo de pruebas puedes pagar con la tarjeta de prueba y el informe se revela automáticamente.
Sin pagar, el detalle **no** aparece ni en la respuesta de la API ni inspeccionando la página.
```

### Errores frecuentes

- Ocultar el detalle solo con CSS o en el frontend.

- Fiarse de la redirección tras el pago en lugar del webhook para marcar como pagado.

- Cambiar a modo real sin haber probado los casos de fallo (pago rechazado, aviso repetido).

## Fase 6: Seguridad, RGPD y publicación

> **Qué vas a lograr**
>
```text
Poner la aplicación en internet de forma segura, con protecciones básicas, información legal y copias de seguridad, lista para que la usen las primeras agencias.
```

> **Por qué es un paso clave**
>
```text
Tu aplicación maneja datos personales sensibles: nombres, DNI, titularidad y cargas de viviendas. Un fallo aquí puede acarrear sanciones y, sobre todo, destruir la confianza de tus clientes.
Publicar no es solo “subir el código”: implica proteger el servicio, controlar costes y poder recuperarte si algo falla.
```

### Conceptos que necesitas

- **RGPD:** te obliga, entre otras cosas, a informar del uso de los datos, limitar su conservación y proteger su acceso.

- **Rate limiting:** limitar cuántas peticiones puede hacer un usuario para evitar abusos y gastos inesperados de IA.

- **Producción:** el entorno real que usan los clientes, distinto de tu ordenador.

- **Backup:** copia de seguridad de la base de datos que puedes restaurar.

### Paso a paso

1.  Añade límites por usuario (peticiones y expedientes) para controlar el coste de IA.

2.  Programa el **borrado automático de los PDFs** tras un plazo definido; no los guardes más de lo necesario.

3.  Redacta la política de privacidad, los términos de uso y la información sobre el tratamiento de datos. **Consulta con un profesional legal**: entre otras cosas, si envías datos personales a un proveedor de IA fuera de la UE, debes evaluarlo.

4.  Aclara en el informe que es una herramienta de apoyo y que la Nota Simple es informativa, no un certificado con valor legal.

5.  Añade registro de errores (por ejemplo, con Sentry) y guarda el coste real de IA de cada expediente.

6.  Despliega el frontend en Vercel y el backend en un servidor económico (VPS) con HTTPS, y usa una base de datos gestionada (Neon o Supabase) o propia.

7.  Configura copias de seguridad automáticas y **prueba a restaurar una**.

8.  Cambia las claves a las de producción (Stripe en modo real, claves nuevas) y verifica que ningún secreto esté en el repositorio.

9.  Documenta cómo desplegar y qué variables de entorno necesita cada parte.

### Commits sugeridos

Haz un commit al terminar cada bloque de trabajo. Estos mensajes son una guía; adáptalos a lo que hayas hecho de verdad.

```text
feat(seguridad): límites de uso por usuario
feat(privacidad): borrado automático de PDFs tras el plazo
docs: política de privacidad y términos de uso
feat(observabilidad): registro de errores y coste por expediente
chore(deploy): Dockerfile del backend y despliegue en el servidor
chore(deploy): despliegue del frontend y conexión con la API
chore(deploy): copias de seguridad automáticas de la base de datos
docs: guía de despliegue y variables de entorno
```

> **Cómo sabes que has terminado**
>
```text
Puedes entrar desde cualquier dispositivo a tu dominio con HTTPS y completar el flujo entero, incluido un pago real de prueba.
Has restaurado una copia de seguridad con éxito y sabes cuánto te cuesta cada expediente.
```

### Errores frecuentes

- Guardar los PDFs indefinidamente “por si acaso”.

- Lanzar sin límites de uso: una sola persona abusando podría generarte una factura de IA enorme.

- Hacer copias de seguridad pero no comprobar nunca que se pueden restaurar.

## Fase 7: Probar con usuarios reales

> **Por qué es clave**
>
```text
Todo lo anterior son suposiciones hasta que un agente real lo usa con un expediente real. Esta fase decide qué merece la pena construir después, y evita que inviertas meses en funciones que nadie pide.
```

### Qué hacer

1.  Consigue **5-10 agentes** dispuestos a probarlo con expedientes reales (conocidos, grupos de agentes, redes profesionales).

2.  Observa cómo lo usan sin ayudarles: anota dónde dudan y dónde abandonan.

3.  Mide tres números: cuántos suben documentos, cuántos llegan a ver el informe y cuántos pagan.

4.  Comprueba el coste real de IA por expediente y compáralo con tu estimación de márgenes.

5.  Recoge las peticiones repetidas y ordénalas por frecuencia.

### Ideas para después del MVP

- **Enlace por WhatsApp** para que el propietario suba los documentos que faltan desde su móvil.

- **OCR** para leer PDFs escaneados.

- **Suscripciones** para agencias que consumen más de 4 expedientes al mes.

- **Analítica del embudo** (subida → informe → pago) para mejorar la conversión.

> **Regla para no perderte**
>
```text
Deja estas ideas fuera hasta que el MVP esté en manos de usuarios reales. Añadir funciones antes de validar es la forma más habitual de retrasar el lanzamiento indefinidamente.
```

## 4. Cuando te atasques

- **Lee el error completo, de abajo arriba.** La última línea suele decir qué falló y la anterior, dónde.

- **Reduce el problema:** prueba solo la parte que falla, con un ejemplo mínimo.

- **Pide ayuda con contexto:** qué querías hacer, qué comando ejecutaste, el error entero y tu sistema operativo.

- **Vuelve al último commit que funcionaba** con git stash o git checkout si has roto algo y no sabes cómo.

- **Descansa.** Muchos errores obvios se ven al volver a mirarlos con la cabeza fresca.

## 5. Lista final de comprobación

| **✔** | **Antes de enseñar el producto a alguien**                                                                  |
|-------|-------------------------------------------------------------------------------------------------------------|
| ☐     | La extracción supera el 95 % en tu conjunto de pruebas anonimizado.                                         |
| ☐     | El motor de cruce tiene tests en verde y el checklist por Comunidad está contrastado con fuentes oficiales. |
| ☐     | El detalle del informe se bloquea en el backend y solo se libera tras un pago confirmado por webhook.       |
| ☐     | No hay claves ni PDFs reales en el repositorio.                                                             |
| ☐     | Existen límites de uso, borrado automático de PDFs y copias de seguridad probadas.                          |
| ☐     | La política de privacidad y los avisos legales están revisados por un profesional.                          |
| ☐     | Sabes cuánto te cuesta la IA por cada expediente.                                                           |
| ☐     | Una persona ajena ha completado el flujo sin ayuda.                                                         |

Empieza por la Fase 0 y no te saltes la Fase 1: si el motor de extracción funciona, todo lo demás es construir alrededor de algo que ya tiene valor.

# Análisis del material de la cátedra (TdSE - Software Design Patterns)

Fecha del relevamiento: 2026-10-08. Commit analizado: `82ff24d` (rama `main`).

Este documento describe qué hay en el repositorio que entregó la cátedra. No propone arquitectura ni
código: es un inventario con observaciones y preguntas abiertas.

## Resumen

- **No es un dataset de entregas de alumnos.** El repo es un fork de
  [`JuanManuelCruz-FIUBA/tdse-software_design_patterns`](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns)
  (se sincroniza a diario con `.github/workflows/sync-upstream.yml`). Contiene **7 proyectos de
  referencia** hechos por el docente y **7 guías en Markdown** que los explican.
- Los **trabajos de alumnos no están en el repo**. El `README.md` solo tiene una tabla con **21
  enlaces a repositorios externos** de trabajos finales. De esos, 19 son públicos y **17 tienen
  código**. Todos usan la misma placa (Nucleo-F103RB) y el mismo IDE, y **14 parten de la plantilla de
  la cátedra** (sección 6).
- **No hay correcciones ni devoluciones docentes**, ni rúbricas, ni consignas de TP, ni reglamento.
  Las memorias de los alumnos muestran que **las devoluciones existen** ("Correcciones según devolución
  de primer entrega"), pero no quedaron en los archivos. Probablemente estén en los PRs de GitHub.
- Lo que sí hay es una **arquitectura de referencia muy consistente**: Cyclic Executive con tick de
  1 ms, tareas `*_init`/`*_update`, statecharts con `switch`, tablas de configuración/datos, colas de
  eventos entre tareas e interrupciones resueltas con callbacks de HAL. Ese es, en la práctica, el
  "patrón" contra el que se mediría a los alumnos.

## 1. Qué hay: estructura, tipos de archivo y volumen

```
tdse-sdp/
├── README.md                 # Índice: contexto, proyectos de referencia, tabla de TF de alumnos
├── STM32_Project.md          # Guía 1: proyecto base generado con STM32CubeIDE
├── Semihosting.md            # Guía 2: printf por el debugger
├── Cyclic_Executive.md       # Guía 3: super-loop + tareas con período de 1 ms
├── Model_Integration.md      # Guía 4: Sensor -> System -> Actuator con statecharts
├── GPIO_Interrupt.md         # Guía 5: interrupciones EXTI
├── Timer_Interrupt.md        # Guía 6: interrupciones de TIM2/TIM3
├── ADC_Interrupt.md          # Guía 7: interrupciones de ADC1/ADC2
├── TdSE_workspace/           # Workspace de STM32CubeIDE con los 7 proyectos
├── .github/workflows/        # CI propio del fork (sync con upstream, land de ramas)
├── .claude/, CLAUDE.md       # Configuración del fork, no es material de la cátedra
└── .gitignore                # Plantilla estándar de C
```

Volumen (sin `.git`): **778 archivos, ~31 MB**.

| Extensión | Cantidad | Qué son |
|:--|--:|:--|
| `.h` | 416 | Mayoría CMSIS + HAL de ST (vendor). Solo ~10 por proyecto son propios |
| `.c` | 163 | HAL (vendor), `Core/` (generado por CubeMX) y `app/` (código del docente) |
| `.cyclo` | 50 | Salida de complejidad ciclomática de GCC, en las carpetas `Debug/` versionadas |
| `.txt` | 26 | Mayormente licencias de ST; además un `app/readme.txt` por proyecto |
| `.prefs`, `.xml`, `.project`, `.cproject`, `.mxproject` | ~50 | Metadatos de Eclipse/STM32CubeIDE |
| `.ioc` | 7 | Configuración de STM32CubeMX (periféricos, pines, reloj, NVIC) |
| `.ld`, `.s` | 7 + 7 | Linker script y startup (idénticos en los 7 proyectos) |
| `.mk`, `makefile`, `.list` | ~16 | Build generado por el IDE (solo en 2 proyectos) |
| `.me` | 8 | `*_commented_read.me`: versión didáctica, muy comentada, de `app.h` y `app_it.h` |
| `.cfg`, `.launch` | 6 + 2 | Configuración de OpenOCD y de depuración |
| `.md` | 9 | 7 guías + README + CLAUDE.md |

### Proporción de código propio y código generado o de terceros (líneas, `.c` + `.h`)

| Proyecto | `app/` (docente) | `Core/` (CubeMX + USER CODE) | `Drivers/` (ST HAL/CMSIS) |
|:--|--:|--:|--:|
| stm32_project | 0 | 1.824 | 78.178 |
| semihosting | 0 | 1.832 | 78.178 |
| cyclic_executive | 1.348 | 1.849 | 78.178 |
| gpio_interrupt | 1.365 | 1.897 | 78.178 |
| timer_interrupt | 1.370 | 2.055 | 94.481 |
| adc_interrupt | 1.374 | 2.093 | 87.566 |
| model_integration | 2.296 | 1.849 | 78.178 |

Más del 95 % de cada proyecto es código de ST. Lo que escribe el usuario está en `app/` y en los
bloques `/* USER CODE BEGIN ... */ ... /* USER CODE END ... */` de `Core/Src/main.c` y
`Core/Src/stm32f1xx_it.c`. Cerca de la mitad de las líneas de `app/` son la licencia BSD y los
separadores de sección de la plantilla.

### Historial de git

- 65 commits, casi todos del docente (`JuanManuelCruz-FIUBA`), entre el 2026-09-19 y el 2026-09-21,
  más algunos de `alutenberg` y los merges del fork.
- En el historial hay **proyectos que después se borraron** (commit `b7d5c1f`):
  `tdse-tp3_01-porting_c_code_solved`, `tdse-tp3_02-porting_c_code_solved` y
  `tdse-tp3_03-system_setup_menu`. Los dos primeros tienen "solved" en el nombre, así que parecen
  **soluciones de TP3**. Incluyen tareas `task_display`, `task_test` y una librería `display.c`, y el
  tercero suma sensor, system y actuator. Las primeras versiones de los proyectos actuales tenían
  nombres del tipo `tdse-tp0_01-stm32_project` y `tdse-tp2_00-model_integration`, lo que sugiere que
  los proyectos se corresponden con TP numerados (TP0, TP2, TP3...).

## 2. El código C

### Cuántos proyectos y qué representa cada uno

Hay 7 proyectos en `TdSE_workspace/`. Están pensados como una **secuencia incremental**, donde cada
uno agrega una pieza sobre el anterior:

| # | Proyecto | Qué agrega |
|:-:|:--|:--|
| 1 | `stm32_project` | Proyecto vacío generado por CubeMX para la placa Nucleo-F103RB |
| 2 | `semihosting` | `printf` por semihosting (`rdimon`, `initialise_monitor_handles()`), excluyendo `syscalls.c` |
| 3 | `cyclic_executive` | Carpeta `app/`, `app_init()`/`app_update()`, tick de 1 ms, `task_a` (bloqueante) y `task_b` (no bloqueante), medición de WCET/BCET con DWT |
| 4 | `model_integration` | Patrón Sensor → System → Actuator con statecharts y colas de eventos |
| 5 | `gpio_interrupt` | EXTI en PB4, PB5 y PC13, con callback `HAL_GPIO_EXTI_Callback` en `app_it.c` |
| 6 | `timer_interrupt` | TIM2/TIM3 con `HAL_TIM_PeriodElapsedCallback` |
| 7 | `adc_interrupt` | ADC1/ADC2 con `HAL_ADC_ConvCpltCallback` |

Los proyectos 5 a 7 parten de `cyclic_executive`, no de `model_integration`. Su `app/` es idéntico
salvo `app_it.c`, `board.h` y el `readme.txt`. Los callbacks de interrupción son **esqueletos vacíos**
(`/* Work to be done. */`). Son plantillas para que el alumno las complete, no soluciones.

### Toolchain e IDE

- **STM32CubeIDE 1.19.0** (Eclipse CDT, build administrado, sin Makefile propio) y **STM32CubeMX
  6.15.0** (`.ioc`).
- **Firmware STM32Cube FW_F1**: los `.ioc` dicen V1.8.7, pero los `readme.txt` dicen V1.8.6.
- Compilador **GNU Arm (arm-none-eabi-gcc)** con optimización `-Os` en todos los proyectos. Los que
  usan semihosting linkean con `-specs=rdimon.specs` y `rdimon`, y excluyen `Core/Src/syscalls.c`.
- Depuración con **OpenOCD** (`*.cfg`, `*.launch`).
- Los archivos `.cyclo` muestran que el IDE compila con medición de complejidad ciclomática por función
  (por ejemplo, `app_update 7`). Es una métrica que el propio toolchain ya produce.
- `adc_interrupt/Debug/` y `timer_interrupt/Debug/` están versionados (~540 KB cada uno, con el
  desensamblado `.list`) porque el `.gitignore` solo ignora `/Debug/` en la raíz. Parece un descuido.

### Estructura común

Todos los proyectos tienen la misma estructura, que es la de CubeMX más `app/`:

```
<proyecto>/
├── <proyecto>.ioc, STM32F103RBTX_FLASH.ld, .cproject, .project, .settings/
├── Core/{Inc,Src,Startup}/     # Generado por CubeMX; el usuario solo escribe en USER CODE
├── Drivers/{CMSIS,STM32F1xx_HAL_Driver}/
└── app/
    ├── readme.txt              # Descripción del ejemplo (formato fijo)
    ├── inc/  app.h, app_it.h, board.h, dwt.h, logger.h, systick.h, task_*.h
    └── src/  app.c, app_it.c, logger.c, systick.c, task_*.c
```

Convenciones que se repiten en todos los archivos de `app/` (son candidatas a criterios verificables):

- **Plantilla de archivo** con licencia BSD-3 y autor, y secciones fijas marcadas con comentarios:
  `inclusions`, `macros and definitions`, `internal data declaration`, `internal functions
  declaration`, `internal data definition`, `external data declaration`, `external functions
  definition`, `end of file`. Los `.h` tienen include guard y CPP guard.
- **Nombres**: `snake_case`; tipos con sufijo `_t`; globales con prefijo `g_`; punteros con `p_`;
  booleanos con `b_`; estados `ST_*`, eventos `EV_*` e identificadores `ID_*` como `enum`.
- **Comparaciones "Yoda"** (`if (ZERO < g_app_tick_cnt)`, `if (true == flag)`).
- **Constantes con sufijo `ul`** y macros `*_INI`, `*_MIN`, `*_MED`, `*_MAX`.
- **Tareas** con firma `void task_x_init(void *parameters)` / `void task_x_update(void *parameters)`,
  registradas en `task_cfg_list[]`. La cantidad se calcula con `sizeof(lista)/sizeof(elemento)`.
- **Separación por archivo** en cada tarea del modelo: `task_x.c` (lógica y statechart),
  `task_x_attribute.h` (tipos `cfg`/`dta`, estados, eventos) y `task_x_interface.{c,h}` (API para
  que otras tareas le envíen eventos).
- **Abstracción de placa** en `board.h` (`BTN_A_PIN`, `LED_A_PORT`, `LED_ON`...), seleccionada por la
  macro `BOARD`.
- **Logging** con `LOGGER_INFO(...)`, que deshabilita interrupciones mientras imprime.

Los comentarios del código están en inglés y las guías `.md` en castellano.

## 3. Correcciones o devoluciones docentes

**No hay ninguna en el repo.** No hay planillas, rúbricas, issues, comentarios de revisión en el
código ni anotaciones del tipo "corregido".

Hay algunos indicios indirectos de que existen en otro lado:

- Dos trabajos de la tabla del README son **reentregas** (`tdse-tpf03_Reentrega`,
  `TDSE_TF_2c2025_3_06_REENTREGA`). Si hubo reentrega, hubo antes una devolución.
- Los proyectos `*_solved` del historial parecen soluciones de referencia de TP3.

## 4. Documentación de criterios, consignas o reglamentos

**No hay consignas de TP, rúbricas ni reglamento.** La documentación que existe es descriptiva y
tutorial ("cómo se hace"), no evaluativa ("qué se exige y cómo se califica"):

- `README.md` lista los atributos de calidad que busca la materia: *Portabilidad, Escalabilidad,
  Flexibilidad, Fiabilidad, Reutilización, Rendimiento, Costo, Disponibilidad, Mantenibilidad,
  Sensibilidad, Simplicidad, Razonabilidad, Colaboración, Capacidad de Prueba, Eficiencia, Robustez,
  Previsibilidad*. Es solo la lista: no define ninguno ni dice cómo se mide.
- La sección **"Patrones" del README está vacía**: la tabla tiene el encabezado y ninguna fila.
- Las guías describen **pasos de configuración** y el **flujo vector → handler de HAL → callback
  débil (`__weak`) → reimplementación en `app_it.c`**.

Reglas que están **explícitas** en las guías (y que una herramienta podría verificar directamente):

1. El código del usuario en archivos generados va **solo dentro de bloques `USER CODE`**, porque
   CubeMX borra lo que esté afuera al regenerar (`STM32_Project.md`).
2. El código de aplicación va en **`app/inc` y `app/src`**, y esas carpetas se agregan a la
   compilación (`Cyclic_Executive.md`).
3. `main.c` se vincula con la aplicación **solo mediante `app_init()` y `app_update()`**, y `app.c`
   con cada tarea mediante `task_x_init()` y `task_x_update()`.
4. Los datos de cada tarea se separan en una estructura **de configuración constante (`*_cfg_t`)** y
   otra **de datos variables (`*_dta_t`)**, agrupadas en arrays.
5. El período de 1 ms lo da una variable que actualiza el **callback de SysTick**
   (`HAL_SYSTICK_IRQHandler()` en `stm32f1xx_it.c` → `HAL_SYSTICK_Callback()` en `app_it.c`).
6. Los callbacks de HAL se reimplementan en **`app/src/app_it.c`**, no en `Core/`.
7. La modularización es **Escrutar → Procesar → Actuar** (Sensor → System → Actuator), y las tareas
   se comunican **mediante interfaces de eventos**, no accediendo directamente a datos ajenos
   (aunque ver la sección 6).

Lo que queda **implícito**, deducible del código pero no escrito como criterio: las convenciones de
nombres, la plantilla de archivo, las comparaciones Yoda, que las tareas no bloqueen, el uso de
`board.h`, la protección de recursos compartidos con `CPSID`/`CPSIE`, el `default:` en los `switch`
de los statecharts y la medición de WCET.

## 5. Patrones de diseño y restricciones de hardware

### Patrones de diseño (mencionados y aplicados)

| Patrón / concepto | Dónde se menciona | Dónde se aplica |
|:--|:--|:--|
| **Bare metal, Super-Loop / Cyclic Executive** | README, `Cyclic_Executive.md` | `app.c`: `app_update()` recorre `task_cfg_list[]` una vez por tick |
| **Update by Time (tick 1 ms)** | README, `Cyclic_Executive.md` | `g_app_tick_cnt` (`volatile`) incrementado en `HAL_SYSTICK_Callback`; `app_update` lo consume con interrupciones deshabilitadas |
| **Event-Triggered System** | README, `readme.txt` | Colas de eventos entre tareas (`task_system_interface.c`) |
| **Polling & Interrupts** | README | Polling de GPIO en `task_sensor.c`; EXTI/TIM/ADC en `app_it.c` |
| **Código bloqueante y no bloqueante** | `readme.txt` | `task_a` (bucle activo / `HAL_Delay`) frente a `task_b` (comparación con `HAL_GetTick()`), elegidos con `TEST_X` |
| **Statecharts / FSM** | README, `Model_Integration.md` | `switch (state)` con `case ST_*` y transiciones disparadas por `EV_*` en sensor, system y actuator |
| **Escrutar - Procesar - Actuar** | README, `Model_Integration.md` | `task_sensor` → `task_system` → `task_actuator` |
| **Tabla de configuración y tabla de datos** (data-driven) | `Cyclic_Executive.md` | `task_cfg_list[]` con punteros a función; `task_sensor_cfg_list[]` / `task_sensor_dta_list[]` |
| **Cola circular de eventos** | implícito | `event_task_system_queue_t` (head/tail/count, largo 16) |
| **Interfaz por eventos** (`put_event_*`, `get_event_*`, `any_event_*`) | `Model_Integration.md` | `task_system_interface.c`, `task_actuator_interface.c` |
| **Modos de sistema** | — | `task_system_mode_t {NORMAL, MODE_QTY}` y despacho por modo |
| **Callbacks débiles de HAL** (inversión de dependencia) | guías de interrupciones | `HAL_*_Callback` reimplementados en `app_it.c` |
| **Abstracción de placa** | — | `board.h` con soporte para 10 placas por `#if` |

### Restricciones de hardware (explícitas o deducibles de la configuración)

- **MCU**: STM32F103RB (Cortex-M3) en una placa **Nucleo-F103RB**. Linker script: **128 KB de flash y
  20 KB de RAM**, con heap mínimo de 0x200 (512 B) y stack mínimo de 0x400 (1 KB). Es idéntico en los
  7 proyectos.
- **Reloj**: HSI con PLL ×16 → **SystemCoreClock = 64 MHz** (15,625 ns por ciclo).
- **SysTick a 1 kHz** → período de 1 ms. Todas las tareas deberían ejecutarse dentro de ese tick, y por
  eso se mide `g_app_runtime_us` y el WCET/BCET de cada tarea con el **contador de ciclos DWT**.
- **Pines**: LD2 (LED verde) en PA5, B1 (botón) en PC13 con EXTI15_10, USART2 en PA2/PA3 (VCP),
  SWD en PA13/PA14 y SWO en PB3. `gpio_interrupt` suma D5/D4 en PB4/PB5 (EXTI4 y EXTI9_5).
  `adc_interrupt` usa ADC1 en el canal 0 y ADC2 en el canal 1.
- **Prioridades NVIC**: todas en 0/0.
- **Timers**: TIM2 y TIM3 con prescaler 0 y período 65535 (desborde cada ~1,02 ms a 64 MHz).
- **Secciones críticas**: `__asm("CPSID i")` / `__asm("CPSIE i")` alrededor de variables compartidas
  con una ISR y dentro de cada `LOGGER_INFO`.
- **Semihosting**: frena mucho la ejecución y requiere un debugger conectado. Se habilita y deshabilita
  con `LOGGER_CONFIG_USE_SEMIHOSTING`.
- **Restricciones implícitas** que la materia parece valorar: no usar memoria dinámica (todo es
  estático), no bloquear dentro de las tareas, no hacer trabajo pesado en las ISR (solo incrementar un
  contador o marcar un flag) y usar `volatile` en las variables compartidas con interrupciones.

## 6. Entregas de alumnos (repos externos del README)

Relevamiento hecho el 2026-10-08 clonando (solo lectura, `--depth 1`) cada repo de la tabla del README
y revisando la rama a la que apuntan los links. No se compiló ni se ejecutó nada.

### Disponibilidad

- De los **21** repos, **19 son públicos y accesibles**. `lucianafalcon/tdse-tf_3` (Interfaz EMG) y
  `pauleDFT/TDSE_TF_2c2025_3_06_REENTREGA` (Jarra Eléctrica) piden autenticación: son privados o ya no
  existen.
- En los 19 accesibles, **todas las rutas de los links existen** en la rama indicada (salvo por la
  inversión de columnas "Código" e "Informe" ya mencionada).
- **2 de los 19 no tienen código**: `camilamon123/tdse-tf_1-4` (Luz-Morse) y `mpdcfiuba/tdse-tf_3-4`
  (Órganos de tubos) solo tienen la memoria y documentos de requisitos. Quedan **17 entregas con
  código**.
- Los commits finales van de **febrero a septiembre de 2026**, así que hay al menos dos cohortes
  (2.º cuatrimestre de 2025 con entrega en febrero o marzo de 2026, y 1.er cuatrimestre de 2026 con
  entrega en agosto).

### El proceso de entrega deja rastro en las ramas

Los nombres de las ramas reflejan las etapas del trabajo final, que se repiten en casi todos los repos:

1. **Propuesta** (`propuesta`, `Propuesta.md`, `Borrador-propuesta`, `Entrega-de-README-propuesta`).
2. **Informe de avance** (`informe_de_avance`, `Informe-de-Avance`, `PR_informe_de_avance`).
3. **Entrega final: memoria + video + código** (`memoria-video-codigo`, `Memoria_Video_Codigo`,
   `Entrega_Final`).

Varios repos tienen ramas como `README.md-corregido`, `consulta_codigo`, `Estado_Requisitos` o
`revert-1-...`, y ramas `*-patch-N` creadas desde la web de GitHub. Eso sugiere que **las entregas se
hacen como pull requests** y que la revisión ocurre ahí.

### Toolchain y estructura del código

Las 17 entregas con código son **muy homogéneas en plataforma**:

- **Todas** usan **STM32F103RB en una Nucleo-F103RB** (según el `.ioc`), generadas con
  **STM32CubeIDE**. CubeMX es 6.13.0 o 6.15.0, y FW_F1 es V1.8.6 o V1.8.7.
- 16 tienen `.cproject`. `Pedro-ub` (Whack-A-Mole) tiene `.ioc` y carpetas `Inc/` y `Src/` en la raíz,
  pero no el proyecto de Eclipse, así que no se puede importar directamente.
- Ninguna usa CMake, PlatformIO ni Makefile propio. Ninguna usa memoria dinámica (`malloc`/`free`).
- **6 versionan la carpeta `Debug/`** con artefactos de compilación (113 a 402 archivos). Hay que
  filtrarlos antes de analizar.

En cuanto a la estructura, **14 de las 17 siguen la plantilla de la cátedra** (`app/inc` y `app/src`,
`app_init`/`app_update`, `task_cfg_list`, tareas `task_*` con archivos `_attribute.h` e
`_interface.c`, statecharts `switch (state)` con `case ST_*`). Hay variantes de forma:

- `App/Inc` y `App/Src` con mayúscula (`mjkloeckner`, `valenguirin`).
- `app/` en la raíz del repo, no dentro de la carpeta del proyecto (`ecamueira`, `tomas-condo`).
- Sufijos en otro orden (`task_actuator_attribute_LED.h` frente a `task_actuator_LED_attribute.h`).
- Proyectos anidados en rutas largas (`SRAGV/Trabajo-FINAL_tdse`, `Software STM32/main`,
  `ENTREGA_FINAL_MEMORIA/CODIGO_..._V3.0`).

**3 entregas se apartan de la plantilla**, y son las más interesantes para probar la herramienta:

| Repo | Estructura propia |
|:--|:--|
| `CavalittoDiazTubinezFerrero/tdse-tf_1-01` (BeepBuddy) | Capas `app/`, `config/`, `hardware/{audio,bluetooth,buttons,buzzer,leds}`, con `mode_manager`. No usa `task_cfg_list` ni statecharts `ST_*` |
| `Pedro-ub/tdse-tf_2026-1erC_3-02` (Whack-A-Mole) | Archivos planos en `Src/` e `Inc/`, con `task_scheduler.c`, `fsm.c`, `queue.c` y `game.c` propios |
| `valenguirin/tdse-tpf03_Reentrega` (Alarma vecinal) | Capa BSP propia (`bsp_gpio`, `bsp_uart_*`, `bsp_eeprom`), drivers `ble` y `gsm` con `*_interface`, y `wcet.h` propio. Sin `task_cfg_list` ni logger |

### Volumen

Contando solo el código propio (`app/` más los bloques `USER CODE` de `Core/`), sin `Drivers/`,
`Debug/` ni copias:

- **Entre ~1.000 y ~5.400 líneas no vacías** por entrega (mediana ~3.700), en **12 a 62 archivos**.
- **3 a 16 tareas** por entrega entre las que siguen la plantilla. Las más comunes son `task_sensor`, `task_system`, `task_actuator`,
  `task_display` y `task_menu`, además de tareas específicas como `task_bluetooth`, `task_storage`,
  `task_pwm` o `task_dht22`.
- Los bloques `USER CODE` de `Core/` suman pocas líneas (16 a 73): **casi todo el código propio está
  en `app/`**, como pide la guía.
- **Ruido a filtrar**: `franavin` tiene las versiones V1.0 y V3.0 del código en la misma rama.
  `Embebidos-Fran-Marcos-Nacho` incluye una copia de `tdse-tp2_01-model_integration` ("código de
  ejemplo de debouncing") y tres memorias de otros alumnos, de una materia anterior, usadas como
  ejemplo.

### Cuánto del código viene de la cátedra

- **13 entregas conservan el encabezado de licencia de Juan Manuel Cruz** en 6 a 38 archivos. Partieron
  de los proyectos de referencia y los extendieron.
- `logger.c` y `systick.c` son **idénticos o casi idénticos** al de referencia en casi todas.
  `app.c` está modificado en todas (más tareas, otros modos).
- `display.c` (driver de LCD) es **casi idéntico en 3 entregas** (11 a 21 líneas distintas sobre 282)
  a la librería de los proyectos `tp3_*` que se borraron del repo de la cátedra. Esa librería usa
  **19 llamadas a `HAL_Delay`**. Así que los `HAL_Delay` de los drivers de display de los alumnos son,
  en buena parte, **heredados del material de la cátedra**.

Para la herramienta, esto significa que hay que **separar el código heredado del código propio del
alumno** (por ejemplo, comparándolo contra las plantillas). Si no, se le atribuirían al alumno
decisiones de la cátedra.

### Patrones de diseño y restricciones observados en el código

- **Cyclic Executive con tick de 1 ms**: presente en las 14 que siguen la plantilla. `Leangc13` no usa
  `HAL_SYSTICK_IRQHandler()`: incrementa `g_app_tick_cnt` directamente en `SysTick_Handler`, dentro de
  `USER CODE`, y deja `HAL_SYSTICK_Callback` como código muerto. Es una **variante válida que una regla
  rígida ("tiene que llamar a `HAL_SYSTICK_IRQHandler`") marcaría como error**.
- **Statecharts**: entre 6 y 45 `case ST_*` por entrega. En general hay modos de sistema (por ejemplo,
  `task_system_normal`, `task_system_setup`, `task_system_falla`), lo que extiende el
  `task_system_mode_t` de la referencia.
- **Secciones críticas** con `CPSID`/`__disable_irq` y **`volatile`** en todas las entregas con código.
- **Bloqueos dentro del ciclo de tareas**: la mayoría de los `HAL_Delay` están en drivers de display,
  EEPROM o sensores, o en funciones `*_init`, donde los propios alumnos los justifican en comentarios
  ("el `HAL_Delay` aquí es aceptable"). Pero hay casos **dentro de statecharts o de la navegación**:
  - `franavin`: `task_system_setup_statechart()` con `HAL_Delay(1000)` y `HAL_Delay(10)`.
  - `Leangc13`: `handle_setup_navigation()` con `HAL_Delay(1500)` y `HAL_Delay(800)`.

  Esto es exactamente lo que el Cyclic Executive busca evitar. A la vez, hay alumnos que documentan
  la regla explícitamente (`Matias-J-Sanchez-Q`: *"Los 5 ms de ciclo de escritura los cuenta el tick,
  NO HAL_Delay"*), lo que sugiere que **es un criterio que los docentes marcan en las devoluciones**.
- **Uso de `float`** en 8 entregas (cálculos de sensores). Puede ser relevante porque el Cortex-M3 no
  tiene FPU, pero no hay evidencia de que la cátedra lo penalice.

### Las memorias siguen una plantilla común

Las memorias (de 43 a 94 KB de Markdown) tienen casi la misma estructura de capítulos. Eso indica que
**hay una plantilla de memoria de la cátedra** que no está en el repo:

- **Encabezado**: título, autores, resumen (a veces también un *abstract*), **registro de versiones**
  e índice.
- **Cap. 1, Introducción general**: necesidad y objetivo, productos comparables, justificación del
  enfoque técnico, alcance y limitaciones.
- **Cap. 2, Introducción específica**: requisitos (en tabla), casos de uso y descripción de módulos.
- **Cap. 3, Diseño e implementación**: arquitectura general, hardware y firmware (con statecharts).
- **Cap. 4, Ensayos y resultados**: pruebas funcionales de hardware y firmware, prueba de integración
  (video), **Console & Build Analyzer** (ocupación de memoria), **WCET por tarea**, **factor de uso de
  CPU (U)**, **medición de consumo** y **tabla de cumplimiento de requisitos**.
- **Cap. 5, Conclusiones** y, en muchas, un capítulo o tabla de **uso de herramientas de IA**
  (integrante, herramienta, uso y forma de verificación).

Los números del capítulo 4 (WCET, U, memoria, consumo) aparecen en casi todas las memorias. Son las
**restricciones de hardware que la cátedra realmente pide medir y justificar**, y ofrecen un punto de
contraste: lo que dice la memoria frente a lo que se puede verificar en el código.

Una memoria (`Pedro-ub`) menciona una *"corrección de formato de figuras y tablas según **pautas de la
cátedra** (epígrafes de tabla arriba, referencia previa en el texto...)"*, así que también hay
**pautas de formato** escritas en algún lado.

### Evidencia de devoluciones docentes (que no están en los repos)

**Ningún repo incluye devoluciones en archivos.** Pero las memorias muestran que existen:

- En su registro de versiones, al menos 3 memorias tienen entradas literales **"Correcciones según
  devolución de primer entrega"** y **"... de segunda entrega"** (`Matias-J-Sanchez-Q`,
  `Embebidos-Fran-Marcos-Nacho`, `Taller-de-sistemas-embebidos-tps`). Esa redacción idéntica sugiere
  que viene de la plantilla.
- `lautaaguirre` registra una *"versión final (sujeta a revisión y correcciones del docente)"*, y
  `CavalittoDiazTubinezFerrero` menciona *"recibir devoluciones parciales"* por etapa.
- Hay dos reentregas en la tabla del README.

Lo más probable es que las devoluciones estén en los **comentarios de los pull requests o issues de
GitHub** (consistente con las ramas `*-patch-N` y `PR_informe_de_avance`), en el campus o por correo.
En este relevamiento no se consultaron PRs ni issues: solo se clonó el contenido git.

## 7. Qué falta, qué no se entiende y preguntas para la cátedra

### Lo que falta para el objetivo del trabajo

1. **No hay código de alumnos en este repo.** Las 21 entregas de la tabla del README son repos
   externos (19 accesibles, 17 con código; ver la sección 6). En 16 de las 21 filas las **columnas
   "Código" e "Informe" están invertidas**. Todas son **trabajos finales integradores** (TF), no los
   TP intermedios que se corresponden con los proyectos de referencia.
2. **No hay devoluciones docentes en ningún archivo**, ni acá ni en los repos de alumnos, aunque las
   memorias muestran que existieron (sección 6). Sin ellas no hay ejemplos de "buena corrección" para
   calibrar ni para alimentar el RAG.
3. **No hay rúbrica ni consignas.** No se sabe qué pide cada TP, qué pesa más en la nota ni qué cuenta
   como error grave o como sugerencia.
4. La **sección "Patrones" del README está vacía**, y los atributos de calidad están solo enumerados.

### Inconsistencias y errores en el material de referencia

Son relevantes porque, si el material de referencia es la "verdad", la herramienta los heredaría:

- **`adc_interrupt` no llama a `HAL_SYSTICK_IRQHandler()` en `SysTick_Handler`**
  (`TdSE_workspace/adc_interrupt/Core/Src/stm32f1xx_it.c:190`). Por lo tanto,
  `HAL_SYSTICK_Callback()` nunca se ejecuta, `g_app_tick_cnt` no se incrementa y **`app_update()`
  nunca corre las tareas**. Los otros tres proyectos con `app/` sí tienen esa llamada. Es justo el tipo
  de error que el sistema debería detectar.
- `ADC_Interrupt.md` declara `TIM_HandleTypeDef hadc1;` (debería ser `ADC_HandleTypeDef`), muestra
  `HAL_TIM_IRQHandler` donde va `HAL_ADC_IRQHandler` y usa rutas `timer_interrupt/...`. Es un
  copy-paste de la guía de timers. El código del proyecto está bien.
- `adc_interrupt/app/readme.txt` dice `Example: timer_interrupt`.
- Las guías alternan `Core/Src` con `Code/Src`, y `Cyclic_Executive.md` escribe `app_int()` y
  `task_a_update()` donde debería decir `app_init()` y `task_name_update()`.
- `board.h` fija `BOARD = NUCLEO_F103RC`, pero la placa configurada en `.ioc` es **NUCLEO-F103RB**.
- En la cola de eventos (`task_system_interface.c`), `put_event_task_system()` **no controla el
  desborde**. Con 16 eventos sin consumir, `head == tail` y `any_event_task_system()` informa que la
  cola está vacía. Además, `count` se actualiza pero nunca se usa.
- En `task_sensor.c` se definen `DEL_BTN_MAX` y el campo `tick`, pero **no se implementa antirrebote**:
  el statechart cambia de estado con una sola lectura. No queda claro si es intencional (una
  simplificación didáctica) o si falta.
- `task_a.c` y `task_b.c` tienen un tercer caso `TEST_2` con el comentario *"Here Chatbot Artificial
  Intelligence generated code"*: la cátedra contempla que los alumnos usen código generado por IA.
- Hay carpetas `Debug/` versionadas en dos proyectos y algunos `.gitignore` por proyecto.

### Preguntas para la cátedra

**Sobre el dataset**

1. ¿Pueden compartir **entregas reales de alumnos de los TP** (no solo los TF), idealmente varias por
   consigna y de distintos cuatrimestres? ¿En qué formato llegan: repo de GitHub, zip, rama?
2. ¿Las entregas tienen la misma estructura que las referencias (`app/`, `task_*`, `board.h`)? ¿Es
   obligatorio partir de estas plantillas?
3. ¿Qué rama o commit de cada repo de la tabla del README es la entrega evaluada? (Varias filas tienen
   los links invertidos.)
4. ¿Hay restricciones de privacidad o de licencia para usar el código de los alumnos en el trabajo
   final de la especialización? ¿Hace falta anonimizar o pedir consentimiento?

**Sobre los criterios**

5. ¿Existe una **rúbrica o planilla de corrección**, aunque sea informal? ¿Qué pesa más: que funcione,
   la arquitectura (patrones), el estilo o la documentación (memoria y video)?
6. ¿Las convenciones de la sección 2 (plantilla de archivo, nombres, comparaciones Yoda, separación
   `attribute`/`interface`) son **obligatorias** o solo recomendadas?
7. ¿Qué cuenta como **error grave** (por ejemplo, un bloqueo dentro de una tarea, un `HAL_Delay`
   dentro de una ISR, código fuera de `USER CODE`, que el WCET supere 1 ms, acceder directamente a
   datos de otra tarea) y qué cuenta como observación menor?
8. ¿Qué significa en concreto cada atributo de calidad del README (por ejemplo, "Razonabilidad" o
   "Sensibilidad") a la hora de corregir?
9. ¿Qué se espera cuando el alumno usa código generado por IA (el caso `TEST_2`)? ¿Hay que detectarlo,
   declararlo o evaluarlo igual que el resto?
10. ¿El antirrebote, el control de desborde de colas y la protección de secciones críticas forman
    parte de lo que se evalúa?

**Sobre las devoluciones**

11. ¿Pueden compartir **devoluciones ya hechas** (correos, comentarios en GitHub, planillas,
    observaciones de reentregas) para tener ejemplos del tono, la profundidad y el formato esperados?
12. ¿Qué formato de devolución les resultaría útil: comentarios por línea, informe por criterio o una
    nota sugerida?

**Sobre el alcance técnico**

13. ¿La placa es siempre **NUCLEO-F103RB**, o hay alumnos con otras placas (por ejemplo, las que
    lista `board.h`)? Eso cambia las restricciones de memoria, de reloj y de pines.
14. ¿Se evalúan también el `.ioc` y la configuración de periféricos, o solo el código C?
15. ¿Qué pasó con los proyectos `tp3_*_solved` y `system_setup_menu` que se borraron del repo? ¿Son
    soluciones que se pueden usar como referencia interna (para el RAG) aunque no se publiquen?
16. ¿Se van a completar las secciones "Patrones" del README y las guías que faltan (por ejemplo,
    `display`, menú, UART)?

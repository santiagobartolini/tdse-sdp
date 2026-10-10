<b>Cyclic Executive ...</b>

  * Modelo de *programación* y *planificación* de **tareas**
    * Ejecuta una secuencia fija de **tareas** en un bucle infinito (*Super-Loop*).
    * Permite implementar **Event-Triggered Systems**.
    * Permite gestionar *eventos* por **Polling & Interrupts**.
    * Permite ejecutar **tareas** del tipo *Non-Blocking Code* & *Update by Time Code* con **period = 1mS**.
    * Permite *Modularizar* el código en en *tareas* del tipo: **Escrutar - Procesar - Actuar**.
  * Requiere de:
      * Configurar el linker para agregar una biblioteca (y sus opciones de linkeo).
      * Excluir un archivo de la compilación para evitar conflictos con las llamadas al sistema del semihosting.
      * Configurar el depurador (*OpenOCD*).
      * Modificar el código fuente  código de dos archivo (```stm32f1xx_it.c``` & ```main.c```), como se detalla a continuación.
      * El **period** de ejecución de tareas (**1mS**) lo aporta una variable actualizada por un *Callback* del *Handler* de la *Interrupción* del **Systick**.
      * La vinculación entre ```main.c``` y ```app.c``` (**Cyclic Executive**) es mediante las funciones ```app_int()``` & ```app_update()```.
      * La vinculación entre ```app.c``` (**Cyclic Executive**) y ```task_name.c``` (**Task**) es mediante las funciones ```task_name_int()``` & ```task_a_update()```.
	  * las funciones ```app_int()``` & ```app_update()```, gentionan datos de cada tarea, encapsulados en dos tipos de estructuras:
		* Una estructura para datos fijos del tipo ```task_cfg_t```.
		* Una estructura para datos variables del tipo ```task_dta_t```.
		* Dichas estructuras se agrupan en sendos arrays, las del tipo ```task_cfg_t``` en ```task_cfg_list[TASK_QTY]``` y las del tipo ```task_dta_t``` en ```task_dta_list[TASK_QTY]```.
	  * Agregar al *árbol de directorios* del proyecto, la carpeta **app**, destinada a almacenar código fuente y archivos de configuración creados por el usuario.
      * Incluir en la compilación, las carpetas **app/inc** & **app**, que contienen  archivos de encabezamiento (```.h```), de código fuente (```.c```) y de comentario (```.txt```).

```
cyclic_executive/Code/Src/stm32f1xx_it.c

. . .

  /* USER CODE BEGIN SysTick_IRQn 1 */

  HAL_SYSTICK_IRQHandler();

  /* USER CODE END SysTick_IRQn 1 */

/* USER CODE BEGIN Includes */

/* Application includes */
#include "logger.h"
#include "app.h"

/* USER CODE END Includes */


cyclic_executive/Code/Src/main.c

/* USER CODE BEGIN 0 */

#if (1 == LOGGER_CONFIG_USE_SEMIHOSTING)

extern void initialise_monitor_handles(void);

#endif

/* USER CODE END 0 */
  . . .

  /* USER CODE BEGIN 1 */

  #if (1 == LOGGER_CONFIG_USE_SEMIHOSTING)

  initialise_monitor_handles();

  #endif

  /* USER CODE END 1 */
  . . .

  /* USER CODE BEGIN 2 */

  /* Application Init */
  app_init();

  /* USER CODE END 2 */
  . . .

    /* USER CODE BEGIN 3 */

    /* Application Update */
    app_update();

  }
  /* USER CODE END 3 */

. . .
```

```
cyclic_executive
├───.settings
├───Core
├───Drivers
└───app
    ├───inc
    └───src
```

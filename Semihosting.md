<b>Semihosting ...</b>

  * Semihosting es un mecanismo que permite al microcontrolador usar los recursos de tu computadora (como la pantalla, el teclado o archivos), a través de la placa de depuración.
    * Sirve para ver mensajes de funciones como ```printf()``` directamente en la consola del IDE sin configurar un puerto físico, requiere de:
      * Configurar el linker para agregar una biblioteca (y sus opciones de linkeo).
      * Excluir un archivo de la compilación para evitar conflictos con las llamadas al sistema del semihosting.
      * Configurar el depurador (*OpenOCD*).
      * Modificar el código fuente  código en el archivo (```main.c```), como se detalla a continuación.
  
```
semihosting/Code/Src/main.c

. . .

/* USER CODE BEGIN Includes */

#include "stdio.h"

/* USER CODE END Includes */

. . .

/* USER CODE BEGIN 0 */

extern void initialise_monitor_handles(void);

/* USER CODE END 0 */
  . . .

  /* USER CODE BEGIN 1 */

  initialise_monitor_handles();

  /* USER CODE END 1 */
  . . .

  /* USER CODE BEGIN 2 */

  printf("Hello World!\n");

  /* USER CODE END 2 */

. . .
```

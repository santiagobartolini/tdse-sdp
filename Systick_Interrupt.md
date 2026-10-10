<b>Systick Interrupt ...</b>

  * Con **STM32CubeIde** (*STM32CubeMX*), es posible gestionar *excepciones e interrupciones* de un **STM32 Project**. El código así generado, vinculado con *excepciones e interrupciones* se encuentra en los *archivos o carpetas*:
    * ```systick_interrupt/Core/Src/main.c```, contiene el prototipo y el código de la función de inicialización del generador de *excepción o interrupción* (**en nuestro caso Systick**), ejecutado por la función ```int main(void)``` y potencialmente su **handle**.
    *  ```systick_interrupt/Core/Startup/startup_stm32f103rbtx.s```, contienen **vectores** de *excepciones e interrupciones* bajo el *rótulo* ```g_pfnVectors:```
      * Dichos **vectores** tienen en común el sufijo ```_IRQHandler()```, su código se encuentra en el  *archivo* ```systick_interrupt/Code/Src/stm32f1xx_it.c```, ejecutan **funciones de HAL** con prefijo```HAL_``` y sufijo ```_IRQHandler()```.
      * Dichas **funciones de HAL** se encuentran en en *archivos* ```.c``` en la *carpeta* ```systick_interrupt/Drivers/STM32F1xx_HAL_Driver/Src```, que tienen en común el prefijo ```stm32f1xx_hal_``` (**en nuestro caso Systick**, en el archivo ```stm32f1xx_hal_cortex.c```)
      * Dichas **funciones de HAL**, ejecutan **callbacks**, con prefijo```HAL_``` y sufijo ```_Callback()```.
      * Dichos **callbacks** están definidos en forma *débil* ```__weak```, para completar o reemplazar por el usuario. En nuestros proyectos el usuario reemplazará en el *archivo* ```systick_interrupt/app/src/app_it.c```.

| GPIO (X: A to E) | Handle (```main.c```) | Vector (```startup_stm32f103rbtx.s``` & ```stm32f1xx_it.c```) | HAL Function (```stm32f1xx_it.c``` & ```stm32f1xx_hal_cortex.c```) | Callback (```stm32f1xx_hal_cortex.c``` => ```app_it.c```) |
|:---- | :----- |  :----- | :------------- | :------- |
| Systick | - | SysTick_Handler | HAL_SYSTICK_IRQHandler() | HAL_SYSTICK_Callback(); |

```
systick_interrupt/Core/Src/main.c

. . .

/* Private function prototypes -----------------------------------------------*/
void SystemClock_Config(void);

. . .

/**
  * @brief  The application entry point.
  * @retval int
  */
int main(void)
{
  . . .

  /* Reset of all peripherals, Initializes the Flash interface and the Systick. */
  HAL_Init();

  . . .

  /* Configure the system clock */
  SystemClock_Config();

  . . . 
}

void SystemClock_Config(void)
{
  RCC_OscInitTypeDef RCC_OscInitStruct = {0};
  RCC_ClkInitTypeDef RCC_ClkInitStruct = {0};

  /** Initializes the RCC Oscillators according to the specified parameters
  * in the RCC_OscInitTypeDef structure.
  */
  RCC_OscInitStruct.OscillatorType = RCC_OSCILLATORTYPE_HSI;
  RCC_OscInitStruct.HSIState = RCC_HSI_ON;
  RCC_OscInitStruct.HSICalibrationValue = RCC_HSICALIBRATION_DEFAULT;
  RCC_OscInitStruct.PLL.PLLState = RCC_PLL_ON;
  RCC_OscInitStruct.PLL.PLLSource = RCC_PLLSOURCE_HSI_DIV2;
  RCC_OscInitStruct.PLL.PLLMUL = RCC_PLL_MUL16;
  if (HAL_RCC_OscConfig(&RCC_OscInitStruct) != HAL_OK)
  {
    Error_Handler();
  }

  /** Initializes the CPU, AHB and APB buses clocks
  */
  RCC_ClkInitStruct.ClockType = RCC_CLOCKTYPE_HCLK|RCC_CLOCKTYPE_SYSCLK
                              |RCC_CLOCKTYPE_PCLK1|RCC_CLOCKTYPE_PCLK2;
  RCC_ClkInitStruct.SYSCLKSource = RCC_SYSCLKSOURCE_PLLCLK;
  RCC_ClkInitStruct.AHBCLKDivider = RCC_SYSCLK_DIV1;
  RCC_ClkInitStruct.APB1CLKDivider = RCC_HCLK_DIV2;
  RCC_ClkInitStruct.APB2CLKDivider = RCC_HCLK_DIV1;

  if (HAL_RCC_ClockConfig(&RCC_ClkInitStruct, FLASH_LATENCY_2) != HAL_OK)
  {
    Error_Handler();
  }
}

. . .

```

```
systick_interrupt/Core/Startup/startup_stm32f103rbtx.s

. . .

/******************************************************************************
*
* The minimal vector table for a Cortex M3.  Note that the proper constructs
* must be placed on this to ensure that it ends up at physical address
* 0x0000.0000.
*
******************************************************************************/
  .section .isr_vector,"a",%progbits
  .type g_pfnVectors, %object
  .size g_pfnVectors, .-g_pfnVectors


g_pfnVectors:

  . . .

  .word SysTick_Handler

  . . .

  .word BootRAM          /* @0x108. This is for boot in RAM mode for
                            STM32F10x Medium Density devices. */

. . .
```

```
systick_interrupt/Core/Src/stm32f1xx_it.c

. . .

/**
  * @brief This function handles System tick timer.
  */
void SysTick_Handler(void)
{
  /* USER CODE BEGIN SysTick_IRQn 0 */

  /* USER CODE END SysTick_IRQn 0 */
  HAL_IncTick();
  /* USER CODE BEGIN SysTick_IRQn 1 */

  HAL_SYSTICK_IRQHandler();

  /* USER CODE END SysTick_IRQn 1 */
}

. . .
```

```
systick_interrupt/Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_cortex.c

. . .

/**
  * @brief  This function handles SYSTICK interrupt request.
  * @retval None
  */
void HAL_SYSTICK_IRQHandler(void)
{
  HAL_SYSTICK_Callback();
}

/**
  * @brief  SYSTICK callback.
  * @retval None
  */
__weak void HAL_SYSTICK_Callback(void)
{
  /* NOTE : This function Should not be modified, when the callback is needed,
            the HAL_SYSTICK_Callback could be implemented in the user file
   */
}

. . .
```

```
systick_interrupt/app/src/app_it.c

. . .

/********************** external data declaration ****************************/
volatile uint32_t g_app_tick_cnt;

. . .

/********************** external functions definition ************************/

. . .

/**
  * @brief  SYSTICK callback.
  * @retval None
  */
void HAL_SYSTICK_Callback(void)
{
	/* Update Tick Counter */
	g_app_tick_cnt++;
}

. . .
```

```
adc_interrupt
├───Core
|   └───Startup
├───Drivers
|   └───STM32F1xx_HAL_Driver
|       └───Src
└───app
    └───src
```

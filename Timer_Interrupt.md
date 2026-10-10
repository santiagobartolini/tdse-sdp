<b>Timer Interrupt ...</b>

  * Con **STM32CubeIde** (*STM32CubeMX*), es posible gestionar *excepciones e interrupciones* de un **STM32 Project**. El código así generado, vinculado con *excepciones e interrupciones* se encuentra en los *archivos o carpetas*:
    * ```timer_interrupt/Core/Src/main.c```, contiene el prototipo y el código de la función de inicialización del generador de *excepción o interrupción* (**en nuestro caso Timer**), ejecutado por la función ```int main(void)``` y potencialmente su **handle**.
    *  ```timer_interrupt/Core/Startup/startup_stm32f103rbtx.s```, contienen **vectores** de *excepciones e interrupciones* bajo el *rótulo* ```g_pfnVectors:```
      * Dichos **vectores** tienen en común el sufijo ```_IRQHandler()```, su código se encuentra en el  *archivo* ```timer_interrupt/Code/Src/stm32f1xx_it.c```, ejecutan **funciones de HAL** con prefijo```HAL_``` y sufijo ```_IRQHandler()```.
      * Dichas **funciones de HAL** se encuentran en en *archivos* ```.c``` en la *carpeta* ```timer_interrupt/Drivers/STM32F1xx_HAL_Driver/Src```, que tienen en común el prefijo ```stm32f1xx_hal_``` (**en nuestro caso Timer**, en el archivo ```stm32f1xx_hal_tim.c```)
      * Dichas **funciones de HAL**, ejecutan **callbacks**, con prefijo```HAL_``` y sufijo ```_Callback()```.
      * Dichos **callbacks** están definidos en forma *débil* ```__weak```, para completar o reemplazar por el usuario. En nuestros proyectos el usuario reemplazará en el *archivo* ```timer_interrupt/app/src/app_it.c```.

| GPIO (X: A to E) | Handle (```main.c```) | Vector (```startup_stm32f103rbtx.s``` & ```stm32f1xx_it.c```) | HAL Function (```stm32f1xx_it.c``` & ```stm32f1xx_hal_tim.c```) | Callback (```stm32f1xx_hal_tim.c``` => ```app_it.c```) |
|:---- | :----- |  :----- | :------------- | :------- |
| T1 | htim1 | TIM1_BRK_IRQHandler | HAL_TIM_IRQHandler() | HAL_TIM_IC_CaptureCallback(); HAL_TIM_OC_DelayElapsedCallback(); HAL_TIM_PWM_PulseFinishedCallback(); HAL_TIM_PeriodElapsedCallback(); HAL_TIMEx_BreakCallback(); HAL_TIM_TriggerCallback(); HAL_TIMEx_CommutCallback(); |
| T1 | htim1 | TIM1_UP_IRQHandler | HAL_TIM_IRQHandler() | HAL_TIM_IC_CaptureCallback(); HAL_TIM_OC_DelayElapsedCallback(); HAL_TIM_PWM_PulseFinishedCallback(); HAL_TIM_PeriodElapsedCallback(); HAL_TIMEx_BreakCallback(); HAL_TIM_TriggerCallback(); HAL_TIMEx_CommutCallback(); |
| T1 | htim1 | TIM1_TRG_COM_IRQHandler | HAL_TIM_IRQHandler() | HAL_TIM_IC_CaptureCallback(); HAL_TIM_OC_DelayElapsedCallback(); HAL_TIM_PWM_PulseFinishedCallback(); HAL_TIM_PeriodElapsedCallback(); HAL_TIMEx_BreakCallback(); HAL_TIM_TriggerCallback(); HAL_TIMEx_CommutCallback(); |
| T1 | htim1 | TIM1_CC_IRQHandler| HAL_TIM_IRQHandler() | HAL_TIM_IC_CaptureCallback(); HAL_TIM_OC_DelayElapsedCallback(); HAL_TIM_PWM_PulseFinishedCallback(); HAL_TIM_PeriodElapsedCallback(); HAL_TIMEx_BreakCallback(); HAL_TIM_TriggerCallback(); HAL_TIMEx_CommutCallback(); |
| T2 | htim2 | TIM2_IRQHandler() | HAL_TIM_IRQHandler() | HAL_TIM_IC_CaptureCallback(); HAL_TIM_OC_DelayElapsedCallback(); HAL_TIM_PWM_PulseFinishedCallback(); HAL_TIM_PeriodElapsedCallback(); HAL_TIMEx_BreakCallback(); HAL_TIM_TriggerCallback(); HAL_TIMEx_CommutCallback(); |
| T3 | htim3 | TIM3_IRQHandler() | HAL_TIM_IRQHandler() | HAL_TIM_IC_CaptureCallback(); HAL_TIM_OC_DelayElapsedCallback(); HAL_TIM_PWM_PulseFinishedCallback(); HAL_TIM_PeriodElapsedCallback(); HAL_TIMEx_BreakCallback(); HAL_TIM_TriggerCallback(); HAL_TIMEx_CommutCallback(); |
| T4 | htim4 | TIM4_IRQHandler() | HAL_TIM_IRQHandler() | HAL_TIM_IC_CaptureCallback(); HAL_TIM_OC_DelayElapsedCallback(); HAL_TIM_PWM_PulseFinishedCallback(); HAL_TIM_PeriodElapsedCallback(); HAL_TIMEx_BreakCallback(); HAL_TIM_TriggerCallback(); HAL_TIMEx_CommutCallback(); |

```
timer_interrupt/Core/Src/main.c

. . .

/* Private variables ---------------------------------------------------------*/
TIM_HandleTypeDef htim2;
TIM_HandleTypeDef htim3;

. . .

/* Private function prototypes -----------------------------------------------*/
. . . 

static void MX_TIM2_Init(void);
static void MX_TIM3_Init(void);

. . .

/**
  * @brief  The application entry point.
  * @retval int
  */
int main(void)
{
  . . .

    /* Initialize all configured peripherals */
  
  . . .
  
  MX_TIM2_Init();
  MX_TIM3_Init();

  . . . 
}

static void MX_TIM2_Init(void)
{

  /* USER CODE BEGIN TIM2_Init 0 */

  /* USER CODE END TIM2_Init 0 */

  TIM_ClockConfigTypeDef sClockSourceConfig = {0};
  TIM_MasterConfigTypeDef sMasterConfig = {0};

  /* USER CODE BEGIN TIM2_Init 1 */

  /* USER CODE END TIM2_Init 1 */
  htim2.Instance = TIM2;
  htim2.Init.Prescaler = 0;
  htim2.Init.CounterMode = TIM_COUNTERMODE_UP;
  htim2.Init.Period = 65535;
  htim2.Init.ClockDivision = TIM_CLOCKDIVISION_DIV1;
  htim2.Init.AutoReloadPreload = TIM_AUTORELOAD_PRELOAD_DISABLE;
  if (HAL_TIM_Base_Init(&htim2) != HAL_OK)
  {
    Error_Handler();
  }
  sClockSourceConfig.ClockSource = TIM_CLOCKSOURCE_INTERNAL;
  if (HAL_TIM_ConfigClockSource(&htim2, &sClockSourceConfig) != HAL_OK)
  {
    Error_Handler();
  }
  sMasterConfig.MasterOutputTrigger = TIM_TRGO_RESET;
  sMasterConfig.MasterSlaveMode = TIM_MASTERSLAVEMODE_DISABLE;
  if (HAL_TIMEx_MasterConfigSynchronization(&htim2, &sMasterConfig) != HAL_OK)
  {
    Error_Handler();
  }
  /* USER CODE BEGIN TIM2_Init 2 */

  /* USER CODE END TIM2_Init 2 */

}

. . .

/**
  * @brief TIM3 Initialization Function
  * @param None
  * @retval None
  */
static void MX_TIM3_Init(void)
{

  /* USER CODE BEGIN TIM3_Init 0 */

  /* USER CODE END TIM3_Init 0 */

  TIM_ClockConfigTypeDef sClockSourceConfig = {0};
  TIM_MasterConfigTypeDef sMasterConfig = {0};

  /* USER CODE BEGIN TIM3_Init 1 */

  /* USER CODE END TIM3_Init 1 */
  htim3.Instance = TIM3;
  htim3.Init.Prescaler = 0;
  htim3.Init.CounterMode = TIM_COUNTERMODE_UP;
  htim3.Init.Period = 65535;
  htim3.Init.ClockDivision = TIM_CLOCKDIVISION_DIV1;
  htim3.Init.AutoReloadPreload = TIM_AUTORELOAD_PRELOAD_DISABLE;
  if (HAL_TIM_Base_Init(&htim3) != HAL_OK)
  {
    Error_Handler();
  }
  sClockSourceConfig.ClockSource = TIM_CLOCKSOURCE_INTERNAL;
  if (HAL_TIM_ConfigClockSource(&htim3, &sClockSourceConfig) != HAL_OK)
  {
    Error_Handler();
  }
  sMasterConfig.MasterOutputTrigger = TIM_TRGO_RESET;
  sMasterConfig.MasterSlaveMode = TIM_MASTERSLAVEMODE_DISABLE;
  if (HAL_TIMEx_MasterConfigSynchronization(&htim3, &sMasterConfig) != HAL_OK)
  {
    Error_Handler();
  }
  /* USER CODE BEGIN TIM3_Init 2 */

  /* USER CODE END TIM3_Init 2 */

}

. . .
```

```
timer_interrupt/Core/Startup/startup_stm32f103rbtx.s

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

  .word TIM1_BRK_IRQHandler
  .word TIM1_UP_IRQHandler
  .word TIM1_TRG_COM_IRQHandler
  .word TIM1_CC_IRQHandler
  .word TIM2_IRQHandler
  .word TIM3_IRQHandler
  .word TIM4_IRQHandler

  . . .

  .word BootRAM          /* @0x108. This is for boot in RAM mode for
                            STM32F10x Medium Density devices. */

. . .
```

```
timer_interrupt/Core/Src/stm32f1xx_it.c

. . .

/**
  * @brief This function handles TIM2 global interrupt.
  */
void TIM2_IRQHandler(void)
{
  /* USER CODE BEGIN TIM2_IRQn 0 */

  /* USER CODE END TIM2_IRQn 0 */
  HAL_TIM_IRQHandler(&htim2);
  /* USER CODE BEGIN TIM2_IRQn 1 */

  /* USER CODE END TIM2_IRQn 1 */
}

/**
  * @brief This function handles TIM3 global interrupt.
  */
void TIM3_IRQHandler(void)
{
  /* USER CODE BEGIN TIM3_IRQn 0 */

  /* USER CODE END TIM3_IRQn 0 */
  HAL_TIM_IRQHandler(&htim3);
  /* USER CODE BEGIN TIM3_IRQn 1 */

  /* USER CODE END TIM3_IRQn 1 */
}

. . .
```

```
timer_interrupt/Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_tim.c

. . .

/**
  * @brief  This function handles TIM interrupts requests.
  * @param  htim TIM  handle
  * @retval None
  */
void HAL_TIM_IRQHandler(TIM_HandleTypeDef *htim)
{
  . . .

  HAL_TIM_IC_CaptureCallback(htim);
  . . .
  HAL_TIM_OC_DelayElapsedCallback(htim);
  . . .
  HAL_TIM_PWM_PulseFinishedCallback(htim);
  . . .
  HAL_TIM_PeriodElapsedCallback(htim);
  . . . 
  HAL_TIMEx_BreakCallback(htim);
  . . .
  HAL_TIM_TriggerCallback();
  . . .
  HAL_TIMEx_CommutCallback();

  . . .
}

/**
  * @brief  Period elapsed callback in non-blocking mode
  * @param  htim TIM handle
  * @retval None
  */
__weak void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
  /* Prevent unused argument(s) compilation warning */
  UNUSED(htim);

  /* NOTE : This function should not be modified, when the callback is needed,
            the HAL_TIM_PeriodElapsedCallback could be implemented in the user file
   */
}

. . .
```

```
timer_interrupt/app/src/app_it.c

. . .

/********************** external data declaration ****************************/
. . .

extern TIM_HandleTypeDef htim2;
extern TIM_HandleTypeDef htim3;

. . .

/********************** external functions definition ************************/
void app_it_init(void)
{
  . . .

	/* Start timer */
	HAL_TIM_Base_Start_IT(&htim2);
	HAL_TIM_Base_Start_IT(&htim3);
}

/* Callback in non blocking modes (Interrupt and DMA) */
void HAL_TIM_PeriodElapsedCallback(TIM_HandleTypeDef *htim)
{
	// Check which version of the timer triggered this callback
	if (htim == &htim2)
	{
		/* Work to be done. */
	}

	if (htim == &htim3)
	{
		/* Work to be done. */
	}
}

. . .
```

```
timer_interrupt
├───Core
|   └───Startup
├───Drivers
|   └───STM32F1xx_HAL_Driver
|       └───Src
└───app
    └───src
```

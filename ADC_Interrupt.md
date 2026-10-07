<b>ADC Interrupt ...</b>

  * Con **STM32CubeIde** (*STM32CubeMX*), es posible gestionar *excepciones e interrupciones* de un **STM32 Project**. El código así generado, vinculado con *excepciones e interrupciones* se encuentra en los *archivos o carpetas*:
    * ```timer_interrupt/Core/Src/main.c```, contiene el prototipo y el código de la función de inicialización del generador de *excepción o interrupción* (**en nuestro caso ADC**), ejecutado por la función ```int main(void)``` y potencialmente su **handle**.
    *  ```timer_interrupt/Core/Startup/startup_stm32f103rbtx.s```, contienen **vectores** de *excepciones e interrupciones* bajo el *rótulo* ```g_pfnVectors:```
      * Dichos **vectores** tienen en común el sufijo ```_IRQHandler()```, su código se encuentra en el  *archivo* ```timer_interrupt/Code/Src/stm32f1xx_it.c```, ejecutan **funciones de HAL** con prefijo```HAL_``` y sufijo ```_IRQHandler()```.
      * Dichas **funciones de HAL** se encuentran en en *archivos* ```.c``` en la *carpeta* ```timer_interrupt/Drivers/STM32F1xx_HAL_Driver/Src```, que tienen en común el prefijo ```stm32f1xx_hal_``` (**en nuestro caso ADC**, en el archivo ```stm32f1xx_hal_adc.c```)
      * Dichas **funciones de HAL**, ejecutan **callbacks**, con prefijo```HAL_``` y sufijo ```_Callback()```.
      * Dichos **callbacks** están definidos en forma *débil* ```__weak```, para completar o reemplazar por el usuario. En nuestros proyectos el usuario reemplazará en el *archivo* ```timer_interrupt/app/src/app_it.c```.

| GPIO (X: A to E) | Handle (```main.c```) | Vector (```startup_stm32f103rbtx.s``` & ```stm32f1xx_it.c```) | HAL Function (```stm32f1xx_it.c``` & ```stm32f1xx_hal_adc.c```) | Callback (```stm32f1xx_hal_adc.c``` => ```app_it.c```) |
|:---- | :----- |  :----- | :------------- | :------- |
| ADC1 | hadc1 | ADC1_2_IRQHandler | HAL_ADC_IRQHandler() | HAL_ADC_ConvCpltCallback(); HAL_ADCEx_InjectedConvCpltCallback(); HAL_ADC_LevelOutOfWindowCallback(); |
| ADC2 | hadc2 | ADC1_2_IRQHandler | HAL_ADC_IRQHandler() |  HAL_ADC_ConvCpltCallback(); HAL_ADCEx_InjectedConvCpltCallback(); HAL_ADC_LevelOutOfWindowCallback(); |

```
adc_interrupt/Core/Src/main.c

/* Private variables ---------------------------------------------------------*/
TIM_HandleTypeDef hadc1;
TIM_HandleTypeDef hadc2;

. . .

/* Private function prototypes -----------------------------------------------*/
. . . 

static void MX_ADC1_Init(void);
static void MX_ADC2_Init(void);

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
  
  MX_ADC1_Init();
  MX_ADC2_Init();

  . . . 
}

/**
  * @brief ADC1 Initialization Function
  * @param None
  * @retval None
  */
static void MX_ADC1_Init(void)
{

  /* USER CODE BEGIN ADC1_Init 0 */

  /* USER CODE END ADC1_Init 0 */

  ADC_ChannelConfTypeDef sConfig = {0};

  /* USER CODE BEGIN ADC1_Init 1 */

  /* USER CODE END ADC1_Init 1 */

  /** Common config
  */
  hadc1.Instance = ADC1;
  hadc1.Init.ScanConvMode = ADC_SCAN_DISABLE;
  hadc1.Init.ContinuousConvMode = DISABLE;
  hadc1.Init.DiscontinuousConvMode = DISABLE;
  hadc1.Init.ExternalTrigConv = ADC_SOFTWARE_START;
  hadc1.Init.DataAlign = ADC_DATAALIGN_RIGHT;
  hadc1.Init.NbrOfConversion = 1;
  if (HAL_ADC_Init(&hadc1) != HAL_OK)
  {
    Error_Handler();
  }

  /** Configure Regular Channel
  */
  sConfig.Channel = ADC_CHANNEL_0;
  sConfig.Rank = ADC_REGULAR_RANK_1;
  sConfig.SamplingTime = ADC_SAMPLETIME_1CYCLE_5;
  if (HAL_ADC_ConfigChannel(&hadc1, &sConfig) != HAL_OK)
  {
    Error_Handler();
  }
  /* USER CODE BEGIN ADC1_Init 2 */

  /* USER CODE END ADC1_Init 2 */

}

/**
  * @brief ADC2 Initialization Function
  * @param None
  * @retval None
  */
static void MX_ADC2_Init(void)
{

  /* USER CODE BEGIN ADC2_Init 0 */

  /* USER CODE END ADC2_Init 0 */

  ADC_ChannelConfTypeDef sConfig = {0};

  /* USER CODE BEGIN ADC2_Init 1 */

  /* USER CODE END ADC2_Init 1 */

  /** Common config
  */
  hadc2.Instance = ADC2;
  hadc2.Init.ScanConvMode = ADC_SCAN_DISABLE;
  hadc2.Init.ContinuousConvMode = DISABLE;
  hadc2.Init.DiscontinuousConvMode = DISABLE;
  hadc2.Init.ExternalTrigConv = ADC_SOFTWARE_START;
  hadc2.Init.DataAlign = ADC_DATAALIGN_RIGHT;
  hadc2.Init.NbrOfConversion = 1;
  if (HAL_ADC_Init(&hadc2) != HAL_OK)
  {
    Error_Handler();
  }

  /** Configure Regular Channel
  */
  sConfig.Channel = ADC_CHANNEL_1;
  sConfig.Rank = ADC_REGULAR_RANK_1;
  sConfig.SamplingTime = ADC_SAMPLETIME_1CYCLE_5;
  if (HAL_ADC_ConfigChannel(&hadc2, &sConfig) != HAL_OK)
  {
    Error_Handler();
  }
  /* USER CODE BEGIN ADC2_Init 2 */

  /* USER CODE END ADC2_Init 2 */

}

. . .

```

```
adc_interrupt/Core/Startup/startup_stm32f103rbtx.s

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

  .word ADC1_2_IRQHandler

  . . .

  .word BootRAM          /* @0x108. This is for boot in RAM mode for
                            STM32F10x Medium Density devices. */
```

```
adc_interrupt/Core/Src/stm32f1xx_it.c

/**
  * @brief This function handles ADC1 and ADC2 global interrupts.
  */
void ADC1_2_IRQHandler(void)
{
  /* USER CODE BEGIN ADC1_2_IRQn 0 */

  /* USER CODE END ADC1_2_IRQn 0 */
  HAL_ADC_IRQHandler(&hadc1);
  HAL_ADC_IRQHandler(&hadc2);
  /* USER CODE BEGIN ADC1_2_IRQn 1 */

  /* USER CODE END ADC1_2_IRQn 1 */
}
```

```
adc_interrupt/Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_adc.c

/**
  * @brief  This function handles TIM interrupts requests.
  * @param  htim TIM  handle
  * @retval None
  */
void HAL_TIM_IRQHandler(TIM_HandleTypeDef *htim)
{
  . . .

  HAL_ADC_ConvCpltCallback(hadc);
  
  HAL_ADCEx_InjectedConvCpltCallback(hadc);
  
  HAL_ADC_LevelOutOfWindowCallback(hadc);

  . . .
}

/**
  * @brief  Conversion complete callback in non blocking mode 
  * @param  hadc: ADC handle
  * @retval None
  */
__weak void HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef* hadc)
{
  /* Prevent unused argument(s) compilation warning */
  UNUSED(hadc);
  /* NOTE : This function should not be modified. When the callback is needed,
            function HAL_ADC_ConvCpltCallback must be implemented in the user file.
   */
}
```

```
adc_interrupt/app/src/app_it.c

/********************** external data declaration ****************************/
. . .

extern ADC_HandleTypeDef hadc1;
extern ADC_HandleTypeDef hadc2;

. . .

/********************** external functions definition ************************/
void app_it_init(void)
{
  . . .

	/* Start ADC */
	HAL_ADC_Start_IT(&hadc1);
	HAL_ADC_Start_IT(&hadc2);
}

/**
  * @brief  Conversion complete callback in non blocking mode
  * @param  hadc: ADC handle
  * @retval None
  */
void HAL_ADC_ConvCpltCallback(ADC_HandleTypeDef* hadc)
{
	// Check which version of the adc triggered this callback
	if (hadc == &hadc1)
	{
		/* Work to be done. */
	}

	if (hadc == &hadc2)
	{
		/* Work to be done. */
	}
}
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

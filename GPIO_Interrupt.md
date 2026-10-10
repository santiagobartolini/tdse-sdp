<b>GPIO Interrupt ...</b>

  * Con **STM32CubeIde** (*STM32CubeMX*), es posible gestionar *excepciones e interrupciones* de un **STM32 Project**. El código así generado, vinculado con *excepciones e interrupciones* se encuentra en los *archivos o carpetas*:
    * ```gpio_interrupt/Core/Src/main.c```, contiene el prototipo y el código de la función de inicialización del generador de *excepción o interrupción* (**en nuestro caso GPIO**), ejecutado por la función ```int main(void)``` y potencialmente su **handle**.
    *  ```gpio_interrupt/Core/Startup/startup_stm32f103rbtx.s```, contienen **vectores** de *excepciones e interrupciones* bajo el *rótulo* ```g_pfnVectors:```
      * Dichos **vectores** tienen en común el sufijo ```_IRQHandler()```, su código se encuentra en el  *archivo* ```gpio_interrupt/Code/Src/stm32f1xx_it.c```, ejecutan **funciones de HAL** con prefijo```HAL_``` y sufijo ```_IRQHandler()```.
      * Dichas **funciones de HAL** se encuentran en en *archivos* ```.c``` en la *carpeta* ```gpio_interrupt/Drivers/STM32F1xx_HAL_Driver/Src```, que tienen en común el prefijo ```stm32f1xx_hal_``` (**en nuestro caso GPIO**, en el archivo ```stm32f1xx_hal_gpio.c```)
      * Dichas **funciones de HAL**, ejecutan **callbacks**, con prefijo```HAL_``` y sufijo ```_Callback()```.
      * Dichos **callbacks** están definidos en forma *débil* ```__weak```, para completar o reemplazar por el usuario. En nuestros proyectos el usuario reemplazará en el *archivo* ```gpio_interrupt/app/src/app_it.c```.

| GPIO (X: A to E) | Handle (```main.c```) | Vector (```startup_stm32f103rbtx.s``` & ```stm32f1xx_it.c```) | HAL Function (```stm32f1xx_it.c``` & ```stm32f1xx_hal_gpio.c```) | Callback (```stm32f1xx_hal_gpio.c``` => ```app_it.c```) |
|:---- | :----- |  :----- | :------------- | :------- |
| PX0  | - | EXTI0_IRQHandler() | HAL_GPIO_EXTI_IRQHandler() | HAL_GPIO_EXTI_Callback() |
| PX1  | - | EXTI1_IRQHandler() | HAL_GPIO_EXTI_IRQHandler() | HAL_GPIO_EXTI_Callback() |
| PX2  | - | EXTI2_IRQHandler() | HAL_GPIO_EXTI_IRQHandler() | HAL_GPIO_EXTI_Callback() |
| PX3  | - | EXTI3_IRQHandler() | HAL_GPIO_EXTI_IRQHandler() | HAL_GPIO_EXTI_Callback() |
| PX4  | - | EXTI4_IRQHandler() | HAL_GPIO_EXTI_IRQHandler() | HAL_GPIO_EXTI_Callback() |
| PX9 to 5 | - | EXTI9_5_IRQHandler() | HAL_GPIO_EXTI_IRQHandler() | HAL_GPIO_EXTI_Callback() |
| PX15 to 10 | - | EXTI15_10_IRQHandler() | HAL_GPIO_EXTI_IRQHandler() | HAL_GPIO_EXTI_Callback() |

```
gpio_interrupt/Core/Src/main.c

. . .

/* Private function prototypes -----------------------------------------------*/
. . . 

static void MX_GPIO_Init(void);

. . .

/**
  * @brief  The application entry point.
  * @retval int
  */
int main(void)
{
  . . .

    /* Initialize all configured peripherals */
  MX_GPIO_Init();

  . . . 
}

/**
  * @brief GPIO Initialization Function
  * @param None
  * @retval None
  */
static void MX_GPIO_Init(void)
{
  GPIO_InitTypeDef GPIO_InitStruct = {0};
  /* USER CODE BEGIN MX_GPIO_Init_1 */

  /* USER CODE END MX_GPIO_Init_1 */

  /* GPIO Ports Clock Enable */
  __HAL_RCC_GPIOC_CLK_ENABLE();
  __HAL_RCC_GPIOD_CLK_ENABLE();
  __HAL_RCC_GPIOA_CLK_ENABLE();
  __HAL_RCC_GPIOB_CLK_ENABLE();

  /*Configure GPIO pin Output Level */
  HAL_GPIO_WritePin(LD2_GPIO_Port, LD2_Pin, GPIO_PIN_RESET);

  /*Configure GPIO pin : B1_Pin */
  GPIO_InitStruct.Pin = B1_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_IT_RISING;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  HAL_GPIO_Init(B1_GPIO_Port, &GPIO_InitStruct);

  /*Configure GPIO pin : LD2_Pin */
  GPIO_InitStruct.Pin = LD2_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_OUTPUT_PP;
  GPIO_InitStruct.Pull = GPIO_NOPULL;
  GPIO_InitStruct.Speed = GPIO_SPEED_FREQ_LOW;
  HAL_GPIO_Init(LD2_GPIO_Port, &GPIO_InitStruct);

  /*Configure GPIO pins : D5_Pin D4_Pin */
  GPIO_InitStruct.Pin = D5_Pin|D4_Pin;
  GPIO_InitStruct.Mode = GPIO_MODE_IT_RISING;
  GPIO_InitStruct.Pull = GPIO_PULLUP;
  HAL_GPIO_Init(GPIOB, &GPIO_InitStruct);

  /* EXTI interrupt init*/
  HAL_NVIC_SetPriority(EXTI4_IRQn, 0, 0);
  HAL_NVIC_EnableIRQ(EXTI4_IRQn);

  HAL_NVIC_SetPriority(EXTI9_5_IRQn, 0, 0);
  HAL_NVIC_EnableIRQ(EXTI9_5_IRQn);

  HAL_NVIC_SetPriority(EXTI15_10_IRQn, 0, 0);
  HAL_NVIC_EnableIRQ(EXTI15_10_IRQn);

  /* USER CODE BEGIN MX_GPIO_Init_2 */

  /* USER CODE END MX_GPIO_Init_2 */
}

. . .
```

```
gpio_interrupt/Core/Startup/startup_stm32f103rbtx.s

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

  .word EXTI0_IRQHandler
  .word EXTI1_IRQHandler
  .word EXTI2_IRQHandler
  .word EXTI3_IRQHandler
  .word EXTI4_IRQHandler

  . . .

  .word EXTI9_5_IRQHandler

  . . .

  .word EXTI15_10_IRQHandler

  . . .

  .word BootRAM          /* @0x108. This is for boot in RAM mode for
                            STM32F10x Medium Density devices. */

. . .
```

```
gpio_interrupt/Core/Src/stm32f1xx_it.c

. . .

/**
  * @brief This function handles EXTI line4 interrupt.
  */
void EXTI4_IRQHandler(void)
{
  /* USER CODE BEGIN EXTI4_IRQn 0 */

  /* USER CODE END EXTI4_IRQn 0 */
  HAL_GPIO_EXTI_IRQHandler(D5_Pin);
  /* USER CODE BEGIN EXTI4_IRQn 1 */

  /* USER CODE END EXTI4_IRQn 1 */
}

/**
  * @brief This function handles EXTI line[9:5] interrupts.
  */
void EXTI9_5_IRQHandler(void)
{
  /* USER CODE BEGIN EXTI9_5_IRQn 0 */

  /* USER CODE END EXTI9_5_IRQn 0 */
  HAL_GPIO_EXTI_IRQHandler(D4_Pin);
  /* USER CODE BEGIN EXTI9_5_IRQn 1 */

  /* USER CODE END EXTI9_5_IRQn 1 */
}

/**
  * @brief This function handles EXTI line[15:10] interrupts.
  */
void EXTI15_10_IRQHandler(void)
{
  /* USER CODE BEGIN EXTI15_10_IRQn 0 */

  /* USER CODE END EXTI15_10_IRQn 0 */
  HAL_GPIO_EXTI_IRQHandler(B1_Pin);
  /* USER CODE BEGIN EXTI15_10_IRQn 1 */

  /* USER CODE END EXTI15_10_IRQn 1 */
}

. . .
```

```
gpio_interrupt/Drivers/STM32F1xx_HAL_Driver/Src/stm32f1xx_hal_gpio.c

. . .

/**
  * @brief  This function handles EXTI interrupt request.
  * @param  GPIO_Pin: Specifies the pins connected EXTI line
  * @retval None
  */
void HAL_GPIO_EXTI_IRQHandler(uint16_t GPIO_Pin)
{
  /* EXTI line interrupt detected */
  if (__HAL_GPIO_EXTI_GET_IT(GPIO_Pin) != 0x00u)
  {
    __HAL_GPIO_EXTI_CLEAR_IT(GPIO_Pin);
    HAL_GPIO_EXTI_Callback(GPIO_Pin);
  }
}

/**
  * @brief  EXTI line detection callbacks.
  * @param  GPIO_Pin: Specifies the pins connected EXTI line
  * @retval None
  */
__weak void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
  /* Prevent unused argument(s) compilation warning */
  UNUSED(GPIO_Pin);
  /* NOTE: This function Should not be modified, when the callback is needed,
           the HAL_GPIO_EXTI_Callback could be implemented in the user file
   */
}

. . .
```

```
gpio_interrupt/app/src/app_it.c

. . .

/**
  * @brief  EXTI line detection callbacks.
  * @param  GPIO_Pin Specifies the pins connected EXTI line
  * @retval None
  */
void HAL_GPIO_EXTI_Callback(uint16_t GPIO_Pin)
{
	// Check which version of the gpio triggered this callback
	if (GPIO_Pin == BTN_A_PIN)
	{
		/* Work to be done. */
	}

	if (GPIO_Pin == BTN_B_PIN)
	{
		/* Work to be done. */
	}

	if (GPIO_Pin == BTN_C_PIN)
	{
		/* Work to be done. */
	}

}

. . .
```

```
gpio_interrupt
├───Core
|   └───Startup
├───Drivers
|   └───STM32F1xx_HAL_Driver
|       └───Src
└───app
    └───src
```

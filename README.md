# FIUBA - Electrónica - Taller de Sistemas Embebidos
## Software Design Patterns
### Contexto

* Los patrones de diseño (**design patterns**) son soluciones habituales a problemas comunes en el diseño de software. Cada patrón es como un plano que se puede personalizar para resolver un problema de diseño particular de tu código.

<details>
<summary><b>Codificamos en C, soluciones del tipo ...</b></summary>

* **Bare Metal** (sin Sistema Operativo)
  * **Cyclic Executive**
    * *Super-Loop* (**Polling & Interrupts**)
    * *Update by Time Code* (**period = 1mS**)
    * **Event-Triggered Systems**
    * **Estructurada, Modular**
      * *Escrutar - Procesar - Actuar*
    * **Software Design Patterns** (*Statecharts*)
      * **Portabilidad, Escalabilidad, Flexibilidad, Fiabilidad, Reutilización,**
      * **Rendimiento, Costo, Disponibilidad, Mantenibilidad, Sensibilidad,**
      * **Simplicidad, Razonabilidad, Colaboración, Capacidad de Prueba,**
      * **Eficiencia, Robustez, Previsibilidad, etc.**
  * **STM32CubeIDE**, entorno de desarrollo integrado multi-OS en C/C++ para el desarrollo de código STM32.
  * **STM32CubeMX**, herramienta gráfica que simplifica la configuración de los productos STM32 y genera el código de inicialización correspondiente.
  * **HAL**, capa de abstracción de hardware de STM32, un software embebido que garantiza la máxima portabilidad en toda la gama STM32.

</details>

---

### Patrones
| Referencias |   |
|:----------- | - |
| | |

---
### Proyectos de referencia
| Referencias | Fuentes |   |
| :------- | :----| - |
| [<b>STM32 Project</b>](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/blob/main/STM32_Project.md) | [stm32_project](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/tree/main/TdSE_workspace/stm32_project) | <b>X</b> |
| [<b>Semihosting</b>](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/blob/main/Semihosting.md) | [semihosting](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/tree/main/TdSE_workspace/semihosting) | <b>X</b> |
| [<b>Cyclic Executive</b>](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/blob/main/Cyclic_Executive.md) | [cyclic_executive](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/tree/main/TdSE_workspace/cyclic_executive) | <b>X</b> |
| [<b>Model Integration</b>](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/blob/main/Model_Integration.md) | [model_integration](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/tree/main/TdSE_workspace/model_integration) | <b>X</b> |
| [<b>GPIO Interrupt</b>](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/blob/main/GPIO_Interrupt.md) | [gpio_interrupt](https://github.com/JuanManuelCruz-FIUBA/tdse-software_design_patterns/tree/main/TdSE_workspace/gpio_interrupt) | |

---

### Ejemplo de proyectos de alumnos
| **Título** | **Código** | **Informe** |
|:---------- | :--------- | :---------- |
| Dimmer + Switch (Ventilador & Luces) | [link](https://github.com/Embebidos-Fran-Marcos-Nacho/tdse-tf_1-2/tree/Memoria-final-y-video/Software%20STM32/main) | [link](https://github.com/Embebidos-Fran-Marcos-Nacho/tdse-tf_1-2/blob/Memoria-final-y-video/Memoria%20t%C3%A9cnica/Memoria%20t%C3%A9cnica.md) |
| Cerradura Electrónica de Alta Seguridad | [link](https://github.com/Matias-J-Sanchez-Q/tdse-tf_2026-1erC_2-02/tree/Matias-J-Sanchez-Q-patch-1/tpfinal) | [link](https://github.com/Matias-J-Sanchez-Q/tdse-tf_2026-1erC_2-02/blob/Matias-J-Sanchez-Q-patch-1/Memoria_final.md) |
| Smartceta | [link](https://github.com/igonzalezb/tdse-tf_3-03/tree/Entrega_Final/tdse-tf_3-03) | [Link](https://github.com/igonzalezb/tdse-tf_3-03/blob/Entrega_Final/Memoria%20Tecnica/Memoria%20Tecnica.md)  |
| Aspiradora Inteligente | [link](https://github.com/lautaaguirre/tdse-tf_2026-1erC_1-01/blob/MEMORIA-VIDEO-CODIGO/Memoria_Video_Codigo.md) | [Link](https://github.com/lautaaguirre/tdse-tf_2026-1erC_1-01/tree/MEMORIA-VIDEO-CODIGO/tdse-tf-aspiradora)  |
| Muestreo Multiparamétrico de Signos Vitales (MMSV) | [link](https://github.com/LautaroBonacorsi/tdse-tf_2026-1erC_1-02/blob/memoria_video_c%C3%B3digo.md/Memoria_Video_C%C3%B3digo.md) | [Link](https://github.com/LautaroBonacorsi/tdse-tf_2026-1erC_1-02/tree/memoria_video_c%C3%B3digo.md/tdse-tf_2026-1erC_1-02)  |
| Sistema de Riego Automático con Gestión de Viento (SRAGV) | [link](https://github.com/Leangc13/tdse-tf_2026-1erC_1-04/blob/memoria-video-codigo/Memoria_Video_C%C3%B3digo.md) | [Link](https://github.com/Leangc13/tdse-tf_2026-1erC_1-04/tree/memoria-video-codigo/SRAGV/Trabajo-FINAL_tdse)  |
| Incubadora de huevos automática | [link](https://github.com/pmartinez-madero/tdse-tf_2026-1erC_2-01/blob/Memoria_Video_Codigo/Memoria_Video_Codigo.md) | [Link](https://github.com/pmartinez-madero/tdse-tf_2026-1erC_2-01/tree/Memoria_Video_Codigo/proof_menu)  |
| Pecera Inteligente | [link](https://github.com/lautaro-fritz/tdse-tf_2026-1erC_2-03/blob/Memoria_Video_C%C3%B3digo.md/Memoria_Video_Codigo.md) | [Link](https://github.com/lautaro-fritz/tdse-tf_2026-1erC_2-03/tree/Memoria_Video_C%C3%B3digo.md/tdse-tf)  |
| Estación Hidropónica | [link](https://github.com/franavin/tdse-tf_2026-1erC_3-1/blob/FINAL-memoria-video-codigo/ENTREGA_FINAL_MEMORIA/Memoria_Video_C%C3%B3digo.md) | [Link](https://github.com/franavin/tdse-tf_2026-1erC_3-1/tree/FINAL-memoria-video-codigo/Memoria%20T%C3%A9cnica/CODIGO_tdse_tf_estacion-hidroponica_V2.0)  |
| Whack-A-Mole | [link](https://github.com/Pedro-ub/tdse-tf_2026-1erC_3-02/blob/Memora_video_codigo/Memoria_Video_Codigo.md) | [Link](https://github.com/Pedro-ub/tdse-tf_2026-1erC_3-02/tree/Memora_video_codigo)  |

---

### Proyectos de prueba
| Referencias | Fuentes |   |
| :------- | :----| - |
| | |




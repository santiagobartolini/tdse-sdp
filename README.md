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
| Cerradura Electrónica de Alta Seguridad | [link](https://github.com/Matias-J-Sanchez-Q/tdse-tf_2026-1erC_2-02/tree/Matias-J-Sanchez-Q-patch-1/tpfinal) | [link](https://github.com/Matias-J-Sanchez-Q/tdse-tf_2026-1erC_2-02/blob/Matias-J-Sanchez-Q-patch-1/Memoria_final.md) |
| Aspiradora Inteligente | [link](https://github.com/lautaaguirre/tdse-tf_2026-1erC_1-01/blob/MEMORIA-VIDEO-CODIGO/Memoria_Video_Codigo.md) | [Link](https://github.com/lautaaguirre/tdse-tf_2026-1erC_1-01/tree/MEMORIA-VIDEO-CODIGO/tdse-tf-aspiradora)  |
| Muestreo Multiparamétrico de Signos Vitales (MMSV) | [link](https://github.com/LautaroBonacorsi/tdse-tf_2026-1erC_1-02/blob/memoria_video_c%C3%B3digo.md/Memoria_Video_C%C3%B3digo.md) | [Link](https://github.com/LautaroBonacorsi/tdse-tf_2026-1erC_1-02/tree/memoria_video_c%C3%B3digo.md/tdse-tf_2026-1erC_1-02)  |
| Sistema de Riego Automático con Gestión de Viento (SRAGV) | [link](https://github.com/Leangc13/tdse-tf_2026-1erC_1-04/blob/memoria-video-codigo/Memoria_Video_C%C3%B3digo.md) | [Link](https://github.com/Leangc13/tdse-tf_2026-1erC_1-04/tree/memoria-video-codigo/SRAGV/Trabajo-FINAL_tdse)  |
| Incubadora de huevos automática | [link](https://github.com/pmartinez-madero/tdse-tf_2026-1erC_2-01/blob/Memoria_Video_Codigo/Memoria_Video_Codigo.md) | [Link](https://github.com/pmartinez-madero/tdse-tf_2026-1erC_2-01/tree/Memoria_Video_Codigo/proof_menu)  |
| Pecera Inteligente | [link](https://github.com/lautaro-fritz/tdse-tf_2026-1erC_2-03/blob/Memoria_Video_C%C3%B3digo.md/Memoria_Video_Codigo.md) | [Link](https://github.com/lautaro-fritz/tdse-tf_2026-1erC_2-03/tree/Memoria_Video_C%C3%B3digo.md/tdse-tf)  |
| Estación Hidropónica | [link](https://github.com/franavin/tdse-tf_2026-1erC_3-1/blob/FINAL-memoria-video-codigo/ENTREGA_FINAL_MEMORIA/Memoria_Video_C%C3%B3digo.md) | [Link](https://github.com/franavin/tdse-tf_2026-1erC_3-1/tree/FINAL-memoria-video-codigo/Memoria%20T%C3%A9cnica/CODIGO_tdse_tf_estacion-hidroponica_V2.0)  |
| Whack-A-Mole | [link](https://github.com/Pedro-ub/tdse-tf_2026-1erC_3-02/blob/Memora_video_codigo/Memoria_Video_Codigo.md) | [Link](https://github.com/Pedro-ub/tdse-tf_2026-1erC_3-02/tree/Memora_video_codigo)  |
| BeepBuddy | [link](https://github.com/CavalittoDiazTubinezFerrero/tdse-tf_1-01/blob/Entrega-de-memoria-del-trabajo-final/MemoriaDelTrabajoFinal.md) | [Link](https://github.com/CavalittoDiazTubinezFerrero/tdse-tf_1-01/tree/Entrega-de-memoria-del-trabajo-final/stm32-project)  |
| Dimmer + Switch (Ventilador & Luces) | [link](https://github.com/Embebidos-Fran-Marcos-Nacho/tdse-tf_1-2/tree/Memoria-final-y-video/Software%20STM32/main) | [link](https://github.com/Embebidos-Fran-Marcos-Nacho/tdse-tf_1-2/blob/Memoria-final-y-video/Memoria%20t%C3%A9cnica/Memoria%20t%C3%A9cnica.md) |
| Alarma vecinal | [link](https://github.com/valenguirin/tdse-tpf03_Reentrega/blob/Informe_y_video/Informe_y_video/Readme.md) | [Link](https://github.com/valenguirin/tdse-tpf03_Reentrega/tree/Code/Code/alarma_vecinal_v8)  |
| Luz-Morse: Decodificador de Código Morse por Señales de Luz | [link](https://github.com/camilamon123/tdse-tf_1-4/blob/main/README-entrega-final.md) | -  |
| Sistema de detección de apneas y variaciones de oxigenación durante el sueño | [link](https://github.com/Taller-de-sistemas-embebidos-tps/tdse-tf_2-01/blob/informe/Memoria_del_Trabajo_Final_Sleep_Centinel/Memoria_del_Trabajo%20_Final_Sleep_Centinel.md) | [Link](https://github.com/Taller-de-sistemas-embebidos-tps/tdse-tf_2-01/tree/informe/code_final)  |
| Juego Interactivo "Simón dice" | [link](https://github.com/tomas-condo/tdse-tf_2-02_/blob/tomas-condo-patch-1/Informe_Final.md) | [Link](https://github.com/tomas-condo/tdse-tf_2-02_/tree/tomas-condo-patch-1)  |
| Sistema Automático de Gestión de Cultivos (SAGC) | [link](https://github.com/ecamueira/tdse-tf_2-3/blob/entrega-final/TP_final.md) | [Link](https://github.com/ecamueira/tdse-tf_2-3/tree/entrega-final)  |
| Control para Salas de Aislados | [link](https://github.com/nicopotenza/tdse-tf_2-04/blob/Entrega_Final/Memoria%20del%20Trabajo%20Final.md) | [Link](https://github.com/nicopotenza/tdse-tf_2-04/tree/Entrega_Final/Software/tdse-tf_2-04_01-esqueleto_principal)  |
| Controlador de derretidor de miel | [link](https://github.com/mjkloeckner/tdse-tf_3-02/blob/main/MEMORIA.md) | [Link](https://github.com/mjkloeckner/tdse-tf_3-02/tree/main/firmware-nucleo)  |
| Smartceta | [link](https://github.com/igonzalezb/tdse-tf_3-03/tree/Entrega_Final/tdse-tf_3-03) | [Link](https://github.com/igonzalezb/tdse-tf_3-03/blob/Entrega_Final/Memoria%20Tecnica/Memoria%20Tecnica.md)  |
| Sistema de control de organos de tubos con microcontroladores | [link](https://github.com/mpdcfiuba/tdse-tf_3-4/blob/main/Readme.md) | - |
| Interfaz EMG para Monitoreo de Actividad Muscular | [link](https://github.com/lucianafalcon/tdse-tf_3/blob/memoria-final-y-video/OneDrive/Desktop/TPF_embebidos/final/README.md) | [Link](https://github.com/lucianafalcon/tdse-tf_3/tree/memoria-final-y-video/OneDrive/Desktop/TPF_embebidos/final/tdse-tp3_04-interactive_menu-main)  |
| Jarra Eléctrica | [link](https://github.com/pauleDFT/TDSE_TF_2c2025_3_06_REENTREGA/blob/Reentrega/Memoria_t%C3%A9cnica_e_im%C3%A1genes/Memoria_del_trabajo_final.md) | [Link](https://github.com/pauleDFT/TDSE_TF_2c2025_3_06_REENTREGA/tree/Reentrega/Codigo_trabajo_final/tdse_tf_06)  |


---

### Proyectos de prueba
| Referencias | Fuentes |   |
| :------- | :----| - |
| | |




Guía Detallada: Cómo crear una Calculadora en Android Studio
A continuación, construiremos paso a paso una aplicación de Calculadora utilizando Android Studio, Kotlin y XML. Analizaremos cada línea de código para entender no solo cómo se hace, sino por qué se hace.

Paso 1: Crear un nuevo proyecto
Abre Android Studio y selecciona New Project (Nuevo Proyecto).

Elige Empty Views Activity y haz clic en Next (Siguiente).

Nota: "Empty Views Activity" crea un proyecto base con un diseño clásico basado en vistas (Views y XML). Es la forma fundamental y más utilizada históricamente para aprender la estructura de Android.

Completa los datos de tu aplicación:

Name (Nombre): Calculadora

Package name (Nombre del paquete): (Déjalo como viene por defecto, ej. com.tuusuario.calculadora)

Language (Idioma): Kotlin

Minimum SDK (SDK mínimo): API 24 (Esto determina qué dispositivos antiguos podrán instalar tu aplicación; API 24 cubre casi la totalidad de los teléfonos actuales).

Haz clic en Finish (Finalizar) y espera unos segundos a que Android Studio cargue y construya la estructura inicial de tu proyecto.

Paso 2: Diseñar la Interfaz Visual (XML)
Vamos a crear la "cara" de nuestra calculadora: una pantalla superior para mostrar los números y una cuadrícula inferior para los botones.

En el menú lateral izquierdo, ve a la ruta res/layout/activity_main.xml.

Cambia a la vista de Code (Código) o Split (Dividida) en la esquina superior derecha.

Reemplaza todo el contenido existente por el siguiente código:
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    android:background="#F3F3F3">

    <TextView
        android:id="@+id/tvDisplay"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:gravity="end|bottom"
        android:text="0"
        android:textSize="48sp"
        android:textColor="#000000"
        android:padding="16dp" />

    <GridLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:columnCount="4"
        android:rowCount="4">

        <Button android:id="@+id/btn7" android:text="7" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn8" android:text="8" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn9" android:text="9" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnDiv" android:text="/" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />

        <Button android:id="@+id/btn4" android:text="4" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn5" android:text="5" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn6" android:text="6" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnMul" android:text="*" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />

        <Button android:id="@+id/btn1" android:text="1" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn2" android:text="2" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn3" android:text="3" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnSub" android:text="-" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />

        <Button android:id="@+id/btnClear" android:text="C" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn0" android:text="0" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnEqual" android:text="=" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnAdd" android:text="+" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />

    </GridLayout>
</LinearLayout>


Análisis del Diseño (XML)
LinearLayout (Contenedor Principal): Organiza sus elementos internos (la pantalla y los botones) en una sola dirección. Al usar android:orientation="vertical", los elementos se apilan ordenadamente uno debajo del otro.

TextView (La pantalla de la calculadora):

android:id="@+id/tvDisplay": Le otorga un "nombre" único para que podamos modificar su texto desde el código Kotlin.

android:layout_weight="1" y android:layout_height="0dp": Esta combinación hace que la pantalla se expanda para ocupar todo el espacio vertical sobrante, empujando los botones hacia el fondo.

android:gravity="end|bottom": Alinea los números en la parte inferior derecha, imitando una calculadora física.

GridLayout (La cuadrícula de botones):

android:columnCount="4" y android:rowCount="4": Crea una matriz perfecta de 4x4 para organizar las teclas.

Button (Los botones):

Cada botón tiene un id único (ej. @+id/btn7).

android:layout_columnWeight="1" y android:layout_width="0dp": Garantiza que cada botón ocupe exactamente el mismo ancho dentro de su columna, manteniendo la simetría visual.

Paso 3: Programar la Lógica (Kotlin)
Ahora le daremos inteligencia a los botones para que realicen cálculos matemáticos reales.

Ve al archivo MainActivity.kt (ubicado en java/com.tuusuario.calculadora/).

Reemplaza el código con lo siguiente (Asegúrate de que la primera línea, el package, coincida con el nombre de tu proyecto si es diferente):

package com.tuusuario.calculadora // Asegúrate de que esto coincida con tu paquete

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    // Variables globales para manejar el estado de la calculadora
    private lateinit var tvDisplay: TextView
    private var primerNumero: Double = 0.0
    private var operacionActual: String = ""
    private var esNuevaOperacion: Boolean = true

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main) // Conecta la lógica con el diseño XML

        tvDisplay = findViewById(R.id.tvDisplay)

        // Agrupamos los IDs de los botones numéricos en una lista
        val botonesNumeros = listOf(
            R.id.btn0, R.id.btn1, R.id.btn2, R.id.btn3, R.id.btn4,
            R.id.btn5, R.id.btn6, R.id.btn7, R.id.btn8, R.id.btn9
        )

        // Usamos un ciclo for para asignarle la misma acción a todos los números
        for (id in botonesNumeros) {
            findViewById<Button>(id).setOnClickListener { boton ->
                numeroPresionado((boton as Button).text.toString())
            }
        }

        // Configuración de los botones de operaciones
        findViewById<Button>(R.id.btnAdd).setOnClickListener { operacionPresionada("+") }
        findViewById<Button>(R.id.btnSub).setOnClickListener { operacionPresionada("-") }
        findViewById<Button>(R.id.btnMul).setOnClickListener { operacionPresionada("*") }
        findViewById<Button>(R.id.btnDiv).setOnClickListener { operacionPresionada("/") }

        // Configuración del botón Igual y Limpiar
        findViewById<Button>(R.id.btnEqual).setOnClickListener { calcularResultado() }
        findViewById<Button>(R.id.btnClear).setOnClickListener { limpiarCalculadora() }
    }

    private fun numeroPresionado(digito: String) {
        // Si acabamos de elegir una operación o acabamos de encenderla, limpiamos la pantalla
        if (esNuevaOperacion) {
            tvDisplay.text = ""
            esNuevaOperacion = false
        }
        // Añadimos el nuevo dígito al texto que ya está en pantalla
        tvDisplay.text = "${tvDisplay.text}$digito"
    }

    private fun operacionPresionada(operador: String) {
        val textoActual = tvDisplay.text.toString()
        // Validamos que haya un número en pantalla y que no sea el mensaje de "Error"
        if (textoActual.isNotEmpty() && textoActual != "Error") {
            primerNumero = textoActual.toDouble()
            operacionActual = operador
            esNuevaOperacion = true
        }
    }

    private fun calcularResultado() {
        val textoActual = tvDisplay.text.toString()
        
        // Solo calculamos si tenemos un número en pantalla, una operación pendiente, y no estamos en estado de "Error"
        if (textoActual.isNotEmpty() && operacionActual.isNotEmpty() && textoActual != "Error") {
            val segundoNumero = textoActual.toDouble()
            var resultado = 0.0

            // Evaluamos qué operación realizar
            when (operacionActual) {
                "+" -> resultado = primerNumero + segundoNumero
                "-" -> resultado = primerNumero - segundoNumero
                "*" -> resultado = primerNumero * segundoNumero
                "/" -> {
                    // Prevenir la división por cero (que causaría un colapso en la app)
                    if (segundoNumero != 0.0) {
                        resultado = primerNumero / segundoNumero
                    } else {
                        tvDisplay.text = "Error"
                        esNuevaOperacion = true
                        return // Salimos de la función inmediatamente
                    }
                }
            }

            // Mostrar resultado: quitamos los decimales (.0) si el número es un entero exacto
            if (resultado % 1.0 == 0.0) {
                tvDisplay.text = resultado.toInt().toString()
            } else {
                tvDisplay.text = resultado.toString()
            }
            
            operacionActual = ""
            esNuevaOperacion = true
        }
    }

    private fun limpiarCalculadora() {
        tvDisplay.text = "0"
        primerNumero = 0.0
        operacionActual = ""
        esNuevaOperacion = true
    }
}

Análisis del Código (Kotlin)
Variables de la clase:

tvDisplay: Guarda la referencia de la pantalla (TextView). Usamos lateinit porque la inicializaremos un poco más adelante en el código, no al instante.

primerNumero: Almacena en la memoria el primer número ingresado antes de que el usuario elija una operación matemática.

operacionActual: Guarda en texto qué símbolo (+, -, etc.) se presionó.

esNuevaOperacion: Un interruptor lógico (Boolean). Si es true, le dice a la app que el próximo número que se presione debe borrar la pantalla y empezar uno nuevo.

Método onCreate(): Es el punto de partida de la aplicación.

setContentView: Enlaza nuestra lógica en Kotlin con el diseño visual en XML.

findViewById: Busca un elemento visual en específico utilizando el id que le asignamos en el paso 2.

setOnClickListener: Le dice a la aplicación "quédate escuchando, y cuando hagan clic en este botón, ejecuta este código". Para los números, usamos un ciclo for para no repetir la misma instrucción 10 veces.

Método numeroPresionado(): Recibe el dígito pulsado y lo concatena (lo une) al texto que ya existe en la pantalla.

Método operacionPresionada(): Guarda el número actual de la pantalla en la variable primerNumero y prepara la calculadora para recibir el siguiente número.

Método calcularResultado(): * Usa una estructura when (más limpia y eficiente que usar múltiples if/else) para realizar la operación correspondiente.

Seguridad: Incluye una validación para evitar que el programa se cierre inesperadamente si un usuario intenta dividir entre 0.0, arrojando un amigable mensaje de "Error".

Método limpiarCalculadora(): Reinicia todas las variables a su estado original, igual que el botón C en el mundo real.

Paso 4: Ejecutar la aplicación
En la barra superior de Android Studio, selecciona un emulador virtual (ej. Pixel) o conecta tu celular físico (recuerda tener la Depuración por USB activada en tu teléfono).

Haz clic sobre el botón verde de Play (Run 'app').

Android Studio compilará los archivos, construirá el APK y abrirá automáticamente tu nueva calculadora en el dispositivo. ¡Es momento de probar que toda la lógica matemática funciona tal y como la programaste!

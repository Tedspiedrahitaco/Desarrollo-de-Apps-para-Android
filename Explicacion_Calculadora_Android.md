# Guía Detallada: Cómo Crear una Calculadora en Android Studio

A continuación, se presenta una explicación paso a paso y el análisis detallado del código para crear una aplicación de Calculadora utilizando Android Studio, Kotlin y XML.

---

## Paso 1: Crear un nuevo proyecto

1. Abre **Android Studio** y selecciona **New Project** (Nuevo Proyecto).
2. Elige **Empty Views Activity** y haz clic en **Next**.
   > *Nota: "Empty Views Activity" crea un proyecto base con un diseño clásico basado en vistas (Views y XML), que es ideal para aprender la estructura tradicional de Android.*
3. Completa los datos:
   - **Name**: Calculadora
   - **Package name**: (Déjalo como viene por defecto)
   - **Language**: Kotlin
   - **Minimum SDK**: API 24 (o la que te recomiende por defecto, determina qué dispositivos podrán instalar tu app).
4. Haz clic en **Finish** y espera a que Android Studio cargue y construya el proyecto inicial.

---

## Paso 2: Diseñar la Interfaz (XML)

Vamos a crear una pantalla con un texto arriba para mostrar los números y una cuadrícula de botones abajo.

Ve al archivo `res/layout/activity_main.xml`. Cambia a la vista de "Code" (Código) o "Split" y reemplaza todo el contenido por esto:

```xml
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

        <Button android:id="@+id/btn7" text="7" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn8" text="8" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn9" text="9" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnDiv" text="/" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />

        <Button android:id="@+id/btn4" text="4" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn5" text="5" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn6" text="6" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnMul" text="*" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />

        <Button android:id="@+id/btn1" text="1" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn2" text="2" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn3" text="3" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnSub" text="-" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />

        <Button android:id="@+id/btnClear" text="C" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btn0" text="0" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnEqual" text="=" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />
        <Button android:id="@+id/btnAdd" text="+" android:layout_width="0dp" android:layout_height="wrap_content" android:layout_columnWeight="1" />

    </GridLayout>
</LinearLayout>
```

### 🧠 Explicación del Diseño (XML)
* **`LinearLayout` (Contenedor Principal):** Organiza sus elementos hijos (el display y la cuadrícula de botones) en una sola dirección. Al usar `android:orientation="vertical"`, los elementos se apilan uno debajo del otro.
* **`TextView` (Pantalla de la calculadora):**
  * `android:id="@+id/tvDisplay"`: Le da un identificador único para poder referenciar y modificar su texto desde el código Kotlin.
  * `android:layout_weight="1"` y `android:layout_height="0dp"`: Hace que el texto ocupe todo el espacio vertical sobrante (empujando los botones hacia abajo).
  * `android:gravity="end|bottom"`: Alinea el texto en la parte inferior derecha, como en las calculadoras reales.
* **`GridLayout` (Cuadrícula de botones):**
  * `android:columnCount="4"` y `android:rowCount="4"`: Crea una cuadrícula perfecta de 4 columnas y 4 filas para distribuir los botones numéricos y de operaciones.
* **`Button` (Botones individuales):**
  * Cada botón tiene un `id` único (ej. `@+id/btn7`, `@+id/btnAdd`).
  * `android:layout_columnWeight="1"` y `android:layout_width="0dp"`: Asegura que cada botón dentro del `GridLayout` ocupe exactamente la misma proporción de ancho en su columna, haciendo que la cuadrícula se vea simétrica.

---

## Paso 3: Programar la Lógica (Kotlin)

Ahora le daremos vida a los botones. Ve a tu archivo `MainActivity.kt` (está en `java/com.tuusuario.calculadora`).

Reemplaza el código con lo siguiente (asegúrate de mantener tu declaración `package` en la primera línea):

```kotlin
import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    // Variables para manejar la lógica de la calculadora
    private lateinit var tvDisplay: TextView
    private var primerNumero: Double = 0.0
    private var operacionActual: String = ""
    private var esNuevaOperacion: Boolean = true

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        tvDisplay = findViewById(R.id.tvDisplay)

        // Configurar los botones de números usando una lista y un ciclo
        val botonesNumeros = listOf(
            R.id.btn0, R.id.btn1, R.id.btn2, R.id.btn3, R.id.btn4,
            R.id.btn5, R.id.btn6, R.id.btn7, R.id.btn8, R.id.btn9
        )

        for (id in botonesNumeros) {
            findViewById<Button>(id).setOnClickListener { boton ->
                numeroPresionado((boton as Button).text.toString())
            }
        }

        // Configurar botones de operaciones
        findViewById<Button>(R.id.btnAdd).setOnClickListener { operacionPresionada("+") }
        findViewById<Button>(R.id.btnSub).setOnClickListener { operacionPresionada("-") }
        findViewById<Button>(R.id.btnMul).setOnClickListener { operacionPresionada("*") }
        findViewById<Button>(R.id.btnDiv).setOnClickListener { operacionPresionada("/") }

        // Botón Igual
        findViewById<Button>(R.id.btnEqual).setOnClickListener { calcularResultado() }

        // Botón Limpiar (C)
        findViewById<Button>(R.id.btnClear).setOnClickListener { limpiarCalculadora() }
    }

    private fun numeroPresionado(digito: String) {
        if (esNuevaOperacion) {
            tvDisplay.text = ""
            esNuevaOperacion = false
        }
        val textoActual = tvDisplay.text.toString()
        tvDisplay.text = textoActual + digito
    }

    private fun operacionPresionada(operador: String) {
        val textoActual = tvDisplay.text.toString()
        if (textoActual.isNotEmpty()) {
            primerNumero = textoActual.toDouble()
            operacionActual = operador
            esNuevaOperacion = true
        }
    }

    private fun calcularResultado() {
        val textoActual = tvDisplay.text.toString()
        if (textoActual.isNotEmpty() && operacionActual.isNotEmpty()) {
            val segundoNumero = textoActual.toDouble()
            var resultado = 0.0

            when (operacionActual) {
                "+" -> resultado = primerNumero + segundoNumero
                "-" -> resultado = primerNumero - segundoNumero
                "*" -> resultado = primerNumero * segundoNumero
                "/" -> {
                    if (segundoNumero != 0.0) {
                        resultado = primerNumero / segundoNumero
                    } else {
                        tvDisplay.text = "Error"
                        esNuevaOperacion = true
                        return
                    }
                }
            }

            // Mostrar resultado sin decimales si es un número entero
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
```

### 🧠 Explicación del Código (Kotlin)

* **Variables de la Clase:**
  * `tvDisplay`: Guarda la referencia a la pantalla (`TextView`) de la calculadora para poder modificar los números visibles. Se inicializa más tarde gracias a `lateinit`.
  * `primerNumero`: Almacena el número inicial insertado *antes* de elegir una operación (+, -, *, /).
  * `operacionActual`: Guarda en texto qué operación (`+`, `-`, etc.) fue seleccionada.
  * `esNuevaOperacion`: Un interruptor lógico (`Boolean`). Si cambia a `true`, indica que el próximo número que presiones debe sobreescribir la pantalla (pasa cuando cambias de dígito tras pulsar una operación).

* **Método `onCreate()`:**
  * Es el equivalente al punto de partida principal que se ejecuta al abrir la aplicación.
  * *`setContentView`*: Conecta tu lógica Kotlin con el diseño visual XML (`R.layout.activity_main`).
  * *`findViewById`*: Encuentra cada elemento visual a partir del Identificador (`id`) que le diste en el XML.
  * **Eventos de Clic (`setOnClickListener`)**: En lugar de repetir el código de clic 10 veces (uno por cada número), se iteran los IDs en un ciclo `for` que manda a llamar la función `numeroPresionado()`.

* **Método `numeroPresionado(digito: String)`:**
  * Almacena o añade dígitos a la pantalla. Si `esNuevaOperacion` es `true`, limpia primero la pantalla de dígitos residuales para empezar a escribir un número completamente nuevo.

* **Método `operacionPresionada(operador: String)`:**
  * Se activa al pulsar `+`, `-`, `*` o `/`. 
  * Toma el número del `tvDisplay` y lo guarda en `primerNumero` de cara a usarlo después cuando sea hora de resolver el cálculo completo.

* **Método `calcularResultado()`:**
  * Se dispara al pulsar `=`.
  * Extrae el `segundoNumero` tecleado de la pantalla.
  * Recurre a una estructura `when` (una función alternativa elegante a muchos "if/else") para comprobar la `operacionActual`. Efectúa la matemática deseada entre el primero y segundo números.
  * En una división por `0.0`, reacciona arrojando un texto de `"Error"`, previniendo que la aplicación colapse.
  * Su último paso extrae los decimales `.0` si el número resultante es un entero exacto (p.ej.: Muestra `5` en lugar de `5.0`).

* **Método `limpiarCalculadora()`:**
  * Devuelve todo de vuelta a los valores predeterminados (pantalla en `0` y variables en `0.0`), tal como la tecla `C` en una calculadora física.

---

## Paso 4: Ejecutar la aplicación

1. En la parte superior de Android Studio, selecciona tu dispositivo, como un **emulador virtual** (Pixel, Nexus, etc.) o conecta un celular físico si tienes la *Depuración por USB* activada.
2. Haz clic sobre el botón circular verde de **Play** ("Run 'app'").
3. Android Studio compilará los archivos transformando el Kotlin y el XML en una APK y abrirá automáticamente tu nueva **Calculadora**. ¡Es momento de probar que toda la lógica funciona tal y como la programaste!

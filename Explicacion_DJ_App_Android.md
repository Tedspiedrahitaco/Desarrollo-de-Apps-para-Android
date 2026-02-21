# Guía Detallada: Cómo Crear una DJ App en Android Studio

A continuación, se presenta una explicación paso a paso y el análisis detallado del código para resolver el "Reto de Clase: El DJ App" utilizando Android Studio, Kotlin y XML.

---

## Paso 1: Crear un nuevo proyecto

1. Abre **Android Studio** y selecciona **New Project** (Nuevo Proyecto).
2. Elige **Empty Views Activity** y haz clic en **Next**.
3. Completa los datos:
   - **Name**: DJApp
   - **Package name**: (Déjalo como viene por defecto)
   - **Language**: Kotlin
   - **Minimum SDK**: API 24.
4. Haz clic en **Finish** y espera a que el proyecto cargue.

---

## Paso 2: Preparar Recursos (Audios)

Para reproducir sonidos, necesitas guardarlos en una carpeta específica dentro de los recursos del proyecto.

1. En el panel de la izquierda (Project), ve a `app -> res`.
2. Haz clic derecho sobre la carpeta `res`, selecciona **New -> Android Resource Directory**.
3. En la ventana que aparece, cambia el **Resource type** a `raw` y haz clic en **OK**. Se creará una carpeta llamada `raw`.
4. Arrastra y suelta de 4 a 8 archivos de audio (`.mp3` o `.wav`) dentro de la nueva carpeta `res/raw`. 
   *Nota: Los nombres de los archivos deben ir en minúsculas y sin espacios (ej. `beat_1.mp3`, `sample_vocal.wav`, `fx_scratch.mp3`).*

---

## Paso 3: Diseñar la Interfaz (XML)

Vamos a crear una cuadrícula de botones para nuestros sonidos, controles globales (Stop, Pause), y un deslizador (SeekBar) para el volumen.

Ve al archivo `res/layout/activity_main.xml`. Cambia a la vista de "Code" o "Split" y reemplaza el contenido por esto:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp"
    android:background="#121212">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="DJ App - Control de Volumen"
        android:textColor="#FFFFFF"
        android:textSize="18sp"
        android:gravity="center"
        android:layout_marginBottom="8dp"/>

    <SeekBar
        android:id="@+id/seekBarVolume"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:max="100"
        android:progress="50"
        android:layout_marginBottom="24dp"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:gravity="center"
        android:layout_marginBottom="24dp">

        <Button
            android:id="@+id/btnPauseAll"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Pausar Todo"
            android:layout_marginEnd="16dp"
            android:backgroundTint="#FF9800"/>

        <Button
            android:id="@+id/btnStopAll"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Detener Todo"
            android:backgroundTint="#F44336"/>
    </LinearLayout>

    <GridLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:columnCount="2"
        android:rowCount="3"
        android:alignmentMode="alignMargins"
        android:columnOrderPreserved="false">

        <!-- Agrega de 4 a 6 botones como estos, asegurando layout_columnWeight y layout_rowWeight -->
        <Button
            android:id="@+id/btnSound1"
            android:text="Sonido 1"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_columnWeight="1"
            android:layout_rowWeight="1"
            android:layout_margin="8dp"
            android:backgroundTint="#3F51B5"/>

        <Button
            android:id="@+id/btnSound2"
            android:text="Sonido 2"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_columnWeight="1"
            android:layout_rowWeight="1"
            android:layout_margin="8dp"
            android:backgroundTint="#009688"/>

        <Button
            android:id="@+id/btnSound3"
            android:text="Sonido 3"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_columnWeight="1"
            android:layout_rowWeight="1"
            android:layout_margin="8dp"
            android:backgroundTint="#9C27B0"/>

        <Button
            android:id="@+id/btnSound4"
            android:text="Sonido 4"
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_columnWeight="1"
            android:layout_rowWeight="1"
            android:layout_margin="8dp"
            android:backgroundTint="#673AB7"/>

    </GridLayout>
</LinearLayout>
```

### 🧠 Explicación del Diseño (XML)
* **`LinearLayout` y Fondo Oscuro:** Toda la ventana está de arriba a abajo. Se usó un color `#121212` para darle un aspecto de app profesional / nocturna (modo DJ).
* **`SeekBar`:** Es una barra deslizante. Se configura de 0 a 100 (`android:max="100"`) para controlar el volumen global y poder guardarlo.
* **`GridLayout`:** Usamos 2 columnas (`columnCount="2"`) y 3 filas (`rowCount="3"`). El atributo `layout_rowWeight="1"` y `layout_columnWeight="1"` logran que los botones ocupen el espacio equitativamente, viéndose como pads gigantes de música.

---

## Paso 4: Programar la Lógica (Kotlin)

Abre el archivo `MainActivity.kt`. Reemplaza su contenido manejando el reproductor dinámico, almacenamiento de preferencias y efectos de color.

```kotlin
package com.tuusuario.djapp // Asegúrate de cambiar esto según el paquete de tu app

import android.content.Context
import android.content.SharedPreferences
import android.graphics.Color
import android.media.MediaPlayer
import android.os.Bundle
import android.widget.Button
import android.widget.SeekBar
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    // Mapa para gestionar los MediaPlayers dinámicamente y optimizar memoria
    private val reproductores = mutableMapOf<Int, MediaPlayer>()
    private var globalVolume: Float = 0.5f
    
    // Variables para SharedPreferences (Bonus)
    private lateinit var sharedPreferences: SharedPreferences

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. Inicializar SharedPreferences para persistir el volumen
        sharedPreferences = getSharedPreferences("DJAppPrefs", Context.MODE_PRIVATE)
        val savedVolume = sharedPreferences.getInt("volumen_global", 50)
        globalVolume = savedVolume / 100f

        // 2. Configurar el SeekBar de volumen
        val seekBarVolume = findViewById<SeekBar>(R.id.seekBarVolume)
        seekBarVolume.progress = savedVolume
        seekBarVolume.setOnSeekBarChangeListener(object : SeekBar.OnSeekBarChangeListener {
            override fun onProgressChanged(seekBar: SeekBar?, progress: Int, fromUser: Boolean) {
                globalVolume = progress / 100f
                // Aplicar volumen a todos los reproductores activos
                reproductores.values.forEach { it.setVolume(globalVolume, globalVolume) }
                
                // Guardar el nuevo estado del volumen (Persistencia)
                sharedPreferences.edit().putInt("volumen_global", progress).apply()
            }
            override fun onStartTrackingTouch(seekBar: SeekBar?) {}
            override fun onStopTrackingTouch(seekBar: SeekBar?) {}
        })

        // 3. Vincular y configurar los botones de sonido
        // Asegúrate de tener archivos llamados sound1, sound2, sound3, y sound4 en res/raw
        configurarBoton(R.id.btnSound1, R.raw.sound1, "#3F51B5")
        configurarBoton(R.id.btnSound2, R.raw.sound2, "#009688")
        configurarBoton(R.id.btnSound3, R.raw.sound3, "#9C27B0")
        configurarBoton(R.id.btnSound4, R.raw.sound4, "#673AB7")

        // 4. Configurar controles Globales (Pause y Stop)
        findViewById<Button>(R.id.btnPauseAll).setOnClickListener {
            reproductores.values.forEach { 
                if (it.isPlaying) it.pause() 
            }
        }

        findViewById<Button>(R.id.btnStopAll).setOnClickListener {
            reproductores.values.forEach { 
                it.stop() 
                it.prepareAsync() // Prepararlo de nuevo para el siguiente play
            }
            // Restaurar color a todos los botones
            findViewById<Button>(R.id.btnSound1).setBackgroundColor(Color.parseColor("#3F51B5"))
            findViewById<Button>(R.id.btnSound2).setBackgroundColor(Color.parseColor("#009688"))
            findViewById<Button>(R.id.btnSound3).setBackgroundColor(Color.parseColor("#9C27B0"))
            findViewById<Button>(R.id.btnSound4).setBackgroundColor(Color.parseColor("#673AB7"))
        }
    }

    private fun configurarBoton(botonId: Int, audioResId: Int, mainColorHex: String) {
        val boton = findViewById<Button>(botonId)
        
        // Crear la instancia de MediaPlayer para este botón y asignarle el volumen actual
        val mediaPlayer = MediaPlayer.create(this, audioResId)
        mediaPlayer.setVolume(globalVolume, globalVolume)
        reproductores[botonId] = mediaPlayer

        // Evento cuando termine de sonar: Regresar al color original
        mediaPlayer.setOnCompletionListener {
            boton.setBackgroundColor(Color.parseColor(mainColorHex))
            boton.setTextColor(Color.WHITE)
        }

        boton.setOnClickListener {
            // Si ya está sonando, reiniciarlo desde el principio (superposición tipo DJ)
            if (mediaPlayer.isPlaying) {
                mediaPlayer.seekTo(0)
            } else {
                mediaPlayer.start()
            }
            
            // Efecto de color: Cambiar a un color brillante/blanco temporalmente mientras suena
            boton.setBackgroundColor(Color.WHITE)
            boton.setTextColor(Color.BLACK)
        }
    }

    // 5. Mejorar Rendimiento: Liberar memoria cuando la app se cierra
    override fun onDestroy() {
        super.onDestroy()
        reproductores.values.forEach { 
            it.release() 
        }
        reproductores.clear()
    }
}
```

### 🧠 Explicación del Código (Kotlin)

* **`reproductores = mutableMapOf<Int, MediaPlayer>()`:** 
  Usa un *Mapa* (Diccionario) para almacenar los recursos de audio, vinculando el ID del botón con su objeto `MediaPlayer`. Esto facilita operaciones masivas como pausar todos los audios simultáneamente sin repetir código y logrando gestionar todo de manera dinámica.
* **`SharedPreferences`:**
  Guarda información permanentemente en el celular para que no se borre al cerrar la app. En este caso, lee y escribe el nivel del `SeekBar` (volumen global). Al volver a abrir la app, el SeekBar recordará exactamente qué nivel de volumen dejó el usuario.
* **`SeekBar.OnSeekBarChangeListener`:**
  Este evento detecta cuándo el usuario arrastra la barra, traduce ese dato numérico a un porcentaje de `0.0f` a `1.0f` (es el formato que usa Android para volumen de sistema en `MediaPlayer`), se lo aplica dinámicamente a todos los sonidos e invoca a las `SharedPreferences` para guardarlo.
* **Función `configurarBoton()`:**
  Encapsula toda la lógica específica para cada botón de la DJ App:
  * **Efectos de Color:** Al presionar y hacer el `<MediaPlayer>.start()`, cambia a color blanco temporalmente, dándole esa apariencia de "Pad Presionado". Se usa el método `setOnCompletionListener` que escucha cuándo el audio acaba naturalmente para regresar el botón al color original asignado.
  * **Inicio Rápido (Superposición):** Si tocas el botón mientras ya está sonando, con `seekTo(0)` ordenas que se repita desde el principio para hacer el famoso efecto DJ de repetición.
* **`onDestroy()`:**
  ¡Cumpliendo una regla de oro del Android Rendering en pro del rendimiento! El framework llama obligatoriamente a esta función antes de destruir por completo la ventana de la actividad. El código itera toda la base de `reproductores` haciendo un llamado directo a `.release()`, soltando recursos valiosos y liberando memoria RAM que de otro modo quedaría flotante y alentarían el dispositivo.

---

## Paso 5: Ejecutar y Probar

1. Compila la app presionando el botón Play (Run) verde.
2. Sube el nivel auditivo del slider, y empieza a presionar cada pad para armar ritmos y beats a tu gusto.
3. Prueba pausarlos todos, pararlos, o tocar repetidas veces un botón para crear efectos rítmicos. Puedes modificar tus propios audios metiendo lo que quieras en tu carpeta `/raw`. ¡Tu primera DJ App está lista y lograste todas las metas del Bonus!

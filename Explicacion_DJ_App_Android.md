Guía Detallada: Cómo crear una aplicación de DJ en Android Studio
En este proyecto daremos un salto emocionante: construiremos nuestra propia "DJ App". Aprenderemos a reproducir pistas de audio, controlar el volumen global con una barra deslizante, y lo más importante: descubriremos cómo hacer que la aplicación "recuerde" nuestras configuraciones incluso después de cerrarla.

A continuación, se presenta la explicación paso a paso y el análisis detallado del código.

Paso 1: Crear un nuevo proyecto
Abre Android Studio y selecciona New Project (Nuevo Proyecto).

Elige Empty Views Activity y haz clic en Next (Siguiente).

Completa los datos:

Name: DJApp

Package name: (Déjalo por defecto)

Language: Kotlin

Minimum SDK: API 24

Haz clic en Finish (Finalizar) y espera a que el proyecto cargue por completo.

Paso 2: Preparar Recursos (Audios)
Para reproducir sonidos propios, necesitamos guardarlos en una carpeta especial dentro del proyecto.

En el panel lateral izquierdo (Project), navega a la ruta app -> res.

Haz clic derecho sobre la carpeta res, selecciona New -> Android Resource Directory (Nuevo Directorio de Recursos de Android).

En la ventana que aparece, cambia el Resource type (Tipo de recurso) a raw y haz clic en OK. Verás que se ha creado una nueva carpeta llamada raw.

Arrastra y suelta 4 archivos de audio (.mp3 o .wav) dentro de la nueva carpeta res/raw.

⚠️ Regla de Oro de Android: Los nombres de los archivos deben ir estrictamente en minúsculas, sin espacios ni caracteres especiales (ej. sonido1.mp3, beat_base.wav). Renómbralos a sound1, sound2, sound3 y sound4 para seguir este tutorial.

Paso 3: Diseñar la Interfaz Visual (XML)
Vamos a crear nuestra consola de DJ: una barra de volumen, controles globales (Pausar/Detener) y unos "pads" (botones gigantes) para lanzar los sonidos.

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
        android:rowCount="2"
        android:alignmentMode="alignMargins">

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

Análisis del Diseño (XML)
LinearLayout y Fondo Oscuro: Organiza toda la pantalla de arriba hacia abajo. El color #121212 (gris muy oscuro) le da un aspecto de aplicación profesional de música (modo nocturno).

SeekBar: Es la barra deslizante. La configuramos de 0 a 100 (android:max="100") para controlar nuestro volumen.

GridLayout: Usamos 2 columnas (columnCount="2") y 2 filas (rowCount="2"). Los atributos layout_rowWeight="1" y layout_columnWeight="1" hacen que los botones se expandan y ocupen el espacio equitativamente, viéndose como verdaderos "pads" de DJ.

Paso 4: Programar la Lógica (Kotlin)
Abre el archivo MainActivity.kt. Aquí es donde ocurre la magia: manejaremos múltiples audios a la vez, guardaremos las preferencias del usuario y añadiremos efectos visuales. Reemplaza el código con lo siguiente:

package com.tuusuario.djapp // Revisa que este sea tu paquete correcto

import android.content.Context
import android.content.SharedPreferences
import android.graphics.Color
import android.media.MediaPlayer
import android.os.Bundle
import android.widget.Button
import android.widget.SeekBar
import androidx.appcompat.app.AppCompatActivity

class MainActivity : AppCompatActivity() {

    // Mapa (Diccionario) para gestionar los MediaPlayers dinámicamente
    private val reproductores = mutableMapOf<Int, MediaPlayer>()
    private var volumenGlobal: Float = 0.5f
    
    // Herramienta para guardar datos en la memoria del teléfono
    private lateinit var sharedPreferences: SharedPreferences

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // 1. Inicializar SharedPreferences (Nuestra memoria de guardado)
        sharedPreferences = getSharedPreferences("DJAppPrefs", Context.MODE_PRIVATE)
        
        // Recuperar el volumen guardado (si es la primera vez, el valor por defecto será 50)
        val volumenGuardado = sharedPreferences.getInt("volumen_global", 50)
        volumenGlobal = volumenGuardado / 100f // Convertir de 0-100 a 0.0-1.0 para Android

        // 2. Configurar el SeekBar (Deslizador de volumen)
        val seekBarVolume = findViewById<SeekBar>(R.id.seekBarVolume)
        seekBarVolume.progress = volumenGuardado
        
        seekBarVolume.setOnSeekBarChangeListener(object : SeekBar.OnSeekBarChangeListener {
            override fun onProgressChanged(seekBar: SeekBar?, progress: Int, fromUser: Boolean) {
                volumenGlobal = progress / 100f
                // Aplicar el nuevo volumen a todos los sonidos cargados
                reproductores.values.forEach { it.setVolume(volumenGlobal, volumenGlobal) }
                
                // Guardar permanentemente este nuevo nivel de volumen
                sharedPreferences.edit().putInt("volumen_global", progress).apply()
            }
            override fun onStartTrackingTouch(seekBar: SeekBar?) {}
            override fun onStopTrackingTouch(seekBar: SeekBar?) {}
        })

        // 3. Vincular los botones con sus sonidos (asegúrate de que los nombres coincidan en res/raw)
        configurarBoton(R.id.btnSound1, R.raw.sound1, "#3F51B5")
        configurarBoton(R.id.btnSound2, R.raw.sound2, "#009688")
        configurarBoton(R.id.btnSound3, R.raw.sound3, "#9C27B0")
        configurarBoton(R.id.btnSound4, R.raw.sound4, "#673AB7")

        // 4. Configurar controles Globales
        findViewById<Button>(R.id.btnPauseAll).setOnClickListener {
            reproductores.values.forEach { mediaPlayer ->
                if (mediaPlayer.isPlaying) mediaPlayer.pause() 
            }
        }

        findViewById<Button>(R.id.btnStopAll).setOnClickListener {
            reproductores.values.forEach { mediaPlayer ->
                // Pausar y regresar el audio al segundo 0 (equivalente a Stop seguro)
                if (mediaPlayer.isPlaying) mediaPlayer.pause()
                mediaPlayer.seekTo(0) 
            }
            // Restaurar el color original a todos los botones
            findViewById<Button>(R.id.btnSound1).setBackgroundColor(Color.parseColor("#3F51B5"))
            findViewById<Button>(R.id.btnSound1).setTextColor(Color.WHITE)
            findViewById<Button>(R.id.btnSound2).setBackgroundColor(Color.parseColor("#009688"))
            findViewById<Button>(R.id.btnSound2).setTextColor(Color.WHITE)
            findViewById<Button>(R.id.btnSound3).setBackgroundColor(Color.parseColor("#9C27B0"))
            findViewById<Button>(R.id.btnSound3).setTextColor(Color.WHITE)
            findViewById<Button>(R.id.btnSound4).setBackgroundColor(Color.parseColor("#673AB7"))
            findViewById<Button>(R.id.btnSound4).setTextColor(Color.WHITE)
        }
    }

    private fun configurarBoton(botonId: Int, audioResId: Int, colorOriginalHex: String) {
        val boton = findViewById<Button>(botonId)
        
        // Crear el reproductor para este sonido y ajustarle el volumen
        val mediaPlayer = MediaPlayer.create(this, audioResId)
        mediaPlayer.setVolume(volumenGlobal, volumenGlobal)
        reproductores[botonId] = mediaPlayer // Lo guardamos en nuestro Mapa

        // Evento: ¿Qué pasa cuando el audio termina de sonar naturalmente?
        mediaPlayer.setOnCompletionListener {
            boton.setBackgroundColor(Color.parseColor(colorOriginalHex))
            boton.setTextColor(Color.WHITE)
        }

        // Evento: Al presionar el pad
        boton.setOnClickListener {
            // Efecto DJ: Si tocas el botón mientras ya suena, se reinicia desde cero (Stutter effect)
            if (mediaPlayer.isPlaying) {
                mediaPlayer.seekTo(0)
            } else {
                mediaPlayer.start()
            }
            
            // Efecto Visual: Cambiar a blanco brillante mientras el pad está "activo"
            boton.setBackgroundColor(Color.WHITE)
            boton.setTextColor(Color.BLACK)
        }
    }

    // 5. RENDIMIENTO: Liberar memoria cuando la app se cierra
    override fun onDestroy() {
        super.onDestroy()
        reproductores.values.forEach { mediaPlayer ->
            mediaPlayer.release() // Destruye el reproductor para liberar RAM
        }
        reproductores.clear()
    }
}

Análisis del Código (Kotlin)
mutableMapOf<Int, MediaPlayer>(): Usamos un Mapa (una especie de diccionario) para almacenar todos nuestros audios, vinculando el ID de cada botón con su archivo de sonido. Esto nos permite usar ciclos (forEach) para pausar todos los audios a la vez, ahorrándonos escribir decenas de líneas de código repetitivo.

SharedPreferences: Piensa en esto como la "Memory Card" de tu aplicación. Guarda información permanentemente en tu teléfono. En este código, la usamos para guardar un número ("volumen_global") cada vez que mueves la barra. Al volver a abrir la app, lee ese archivo y ajusta el volumen justo donde lo dejaste.

SeekBar.OnSeekBarChangeListener: Este "escuchador" detecta en tiempo real cuándo arrastras la barra de volumen. Traduce la posición de la barra (0 a 100) a decimales (0.0f a 1.0f), que es el formato matemático que Android exige para ajustar el volumen.

Función configurarBoton(): Centraliza la lógica para no repetir código.

Inicio Rápido (Superposición): Gracias a seekTo(0), si tocas el pad repetidamente, el sonido se reinicia desde el milisegundo cero sin detenerse, creando el famoso efecto "Stutter" o tartamudeo de los DJs.

Efectos Visuales: Al presionar, el botón se ilumina de blanco. Usamos setOnCompletionListener para que, cuando la pista termine sola, el botón recupere su color original.

onDestroy(): ¡Esta es una regla de oro profesional! Los archivos de audio consumen mucha memoria RAM. Cuando Android destruye la aplicación al cerrarla, llamamos a .release() en cada sonido para devolverle esa memoria al celular.

Paso 5: Ejecutar y Probar
Compila la aplicación presionando el botón verde de Play (Run) en la barra superior.

Mueve la barra de volumen hacia arriba y hacia abajo (¡y cierra la app para comprobar que guardó tu preferencia!).

Presiona los pads para lanzar tus sonidos, experimenta pulsándolos rápido y prueba los botones de control global.

¡Felicidades, tu primera DJ App profesional está lista!

Ve al archivo res/layout/activity_main.xml.

Cambia a la vista de Code (Código) o Split (Dividida) y reemplaza todo el contenido por esto:

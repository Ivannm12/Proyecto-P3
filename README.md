from PySide6.QtWidgets import (
    QApplication, QWidget, QLabel, QVBoxLayout, QLineEdit, QPushButton, QMessageBox
)
from PySide6.QtGui import QPixmap
import sys
import os
import random

class OrtografiaConImagen(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Test de Ortografía con Imágenes")
        self.setGeometry(200, 200, 400, 400)

        # Carpeta de imágenes
        self.imagenes_dir = "imagenes"
        self.lista_imagenes = [f for f in os.listdir(self.imagenes_dir) if f.endswith(('.png', '.jpg', '.jpeg'))]

        # Variables para las imágenes y el puntajeq
        self.imagenes_mostradas = []  # Lista para las imágenes ya mostradas
        self.imagen_actual = ""
        self.puntaje = 0
        self.intento = 0
        self.acertadas = 0
        self.incorrectas = 0

        self.layout = QVBoxLayout()

        self.instruccion = QLabel("¿Cómo se llama esto? Escribe con buena ortografía:")
        self.layout.addWidget(self.instruccion)

        self.imagen_label = QLabel()
        self.imagen_label.setFixedHeight(200)
        self.imagen_label.setScaledContents(True)
        self.layout.addWidget(self.imagen_label)

        self.input = QLineEdit()
        self.layout.addWidget(self.input)

        self.boton_verificar = QPushButton("Verificar")
        self.boton_verificar.clicked.connect(self.verificar)
        self.layout.addWidget(self.boton_verificar)

        self.resultado = QLabel("Puntaje: 0")
        self.layout.addWidget(self.resultado)

        self.setLayout(self.layout)
        self.mostrar_nueva_imagen()

    def mostrar_nueva_imagen(self):
        # Si ya se mostraron todas las imágenes, termina el test
        if len(self.imagenes_mostradas) == len(self.lista_imagenes):
            self.mostrar_puntaje_final()
            return
        
        self.input.clear()

        # Seleccionamos una imagen que no se haya mostrado
        imagen_no_mostrada = random.choice([img for img in self.lista_imagenes if img not in self.imagenes_mostradas])
        self.imagen_actual = imagen_no_mostrada
        self.imagenes_mostradas.append(imagen_no_mostrada)  # Marcamos esta imagen como mostrada

        ruta = os.path.join(self.imagenes_dir, self.imagen_actual)
        pixmap = QPixmap(ruta)
        self.imagen_label.setPixmap(pixmap)
        self.intento += 1

    def verificar(self):
        respuesta_usuario = self.input.text().strip().lower()
        palabra_correcta = os.path.splitext(self.imagen_actual)[0].lower()

        if respuesta_usuario == palabra_correcta:
            self.puntaje += 1
            self.acertadas += 1
            QMessageBox.information(self, "¡Correcto!", "¡Muy bien!")
        else:
            self.incorrectas += 1
            QMessageBox.warning(self, "Incorrecto", f"La forma correcta era: {palabra_correcta}")

        self.resultado.setText(f"Puntaje: {self.puntaje} de {self.intento}")
        self.mostrar_nueva_imagen()

    def mostrar_puntaje_final(self):
        resumen = f"Test terminado\n\nPuntaje final: {self.puntaje} de {self.intento}\nAcertaste: {self.acertadas}\nFallaste: {self.incorrectas}"
        QMessageBox.information(self, "Resumen del Test", resumen)
        self.close()  # Cerrar la aplicación

if __name__ == "__main__":
    app = QApplication(sys.argv)
    ventana = OrtografiaConImagen()
    ventana.show()
    sys.exit(app.exec())

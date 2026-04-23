¡Hola! Como desarrollador de software, he diseñado este plan de trabajo estructurado para que tus estudiantes no solo sigan instrucciones, sino que comprendan la arquitectura detrás de una app profesional. 

Vamos a construir **"Juguetería Ivette"**, utilizando un flujo de trabajo moderno y una estética vibrante en tonos morados.

---

## 🛠️ Fase 1: Entorno y Configuración (Pasos 1-3)

### 1. Estructura de Directorios
Desde tu terminal o gestor de archivos, crea la jerarquía solicitada:
* **Carpeta raíz:** `xflutterivette0684`
* **Proyecto Flutter:** `crudjugueteria`

### 2. Configuración en Firebase Console
1.  Ve a [Firebase Console](https://console.firebase.google.com/).
2.  Crea un nuevo proyecto llamado **"Jugueteria-CRUD"**.
3.  En el menú lateral, ve a **Firestore Database** y haz clic en **Crear base de datos**.
4.  Selecciona **"Iniciar en modo de prueba"** (para facilitar el desarrollo inicial) y elige una ubicación de servidor cercana.
5.  Crea una colección llamada `juguetes`.

---

## 📦 Fase 2: Librerías y Dependencias (Pasos 4-5)

Para integrar Firebase y manejar la UI de forma fluida, modificaremos el archivo `pubspec.yaml`.

### ¿Cómo agregar las librerías?
Ejecuta estos comandos en tu terminal dentro de la carpeta `crudjugueteria`:
```bash
flutter pub add firebase_core
flutter pub add cloud_firestore
```

### Modificación del `pubspec.yaml`
Tu archivo debería verse así (presta atención a la indentación):

```yaml
dependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.x.x  # Versión más reciente
  cloud_firestore: ^4.x.x
```

---

## 🏗️ Fase 3: Metodología de Trabajo (Agentes y Roles) (Paso 8)

Para este proyecto, simularemos un entorno de **Desarrollo Ágil** basado en agentes:

| Agente | Rol | Skill / Responsabilidad |
| :--- | :--- | :--- |
| **Arquitecto Cloud** | Configuración Firebase | Conexión del `google-services.json` y reglas de Firestore. |
| **Backend Dev** | Lógica de Datos | Creación del servicio CRUD (`firestore_service.dart`). |
| **Frontend Dev** | UI/UX Designer | Creación de interfaces en **Morado Intenso**. |
| **QA Tester** | Depuración | Validación de que los campos (Nombre, Precio, Stock) se guarden correctamente. |

**Flujo de Trabajo:**
1. **Configuración inicial** $\rightarrow$ 2. **Servicios de Datos** $\rightarrow$ 3. **Vistas de UI** $\rightarrow$ 4. **Integración Final**.

---

## 📂 Estructura de Carpetas Sugerida
```text
crudjugueteria/
├── lib/
│   ├── services/
│   │   └── firebase_service.dart  <-- (Lógica CRUD)
│   ├── pages/
│   │   ├── home_page.dart         <-- (Lista de juguetes)
│   │   ├── add_juguete_page.dart  <-- (Crear/Editar)
│   └── main.dart                  <-- (Punto de entrada)
```

---

## 💻 Código Funcional (Paso 6)

### 1. `services/firebase_service.dart` (El motor CRUD)
Este archivo gestiona la comunicación con Firestore.

```dart
import 'package:cloud_firestore/cloud_firestore.dart';

FirebaseFirestore db = FirebaseFirestore.instance;

// LEER (Read)
Future<List> getJuguetes() async {
  List juguetes = [];
  QuerySnapshot querySnapshot = await db.collection('juguetes').get();
  for (var doc in querySnapshot.docs) {
    final Map<String, dynamic> data = doc.data() as Map<String, dynamic>;
    final juguete = {
      "nombre": data['nombre'],
      "precio": data['precio'],
      "stock": data['stock'],
      "uid": doc.id,
    };
    juguetes.add(juguete);
  }
  return juguetes;
}

// GUARDAR (Create)
Future<void> addJuguete(String nombre, double precio, int stock) async {
  await db.collection('juguetes').add({
    "nombre": nombre,
    "precio": precio,
    "stock": stock,
  });
}

// ACTUALIZAR (Update)
Future<void> updateJuguete(String uid, String newNombre, double newPrecio, int newStock) async {
  await db.collection('juguetes').doc(uid).set({
    "nombre": newNombre,
    "precio": newPrecio,
    "stock": newStock,
  });
}

// BORRAR (Delete)
Future<void> deleteJuguete(String uid) async {
  await db.collection('juguetes').doc(uid).delete();
}
```

### 2. `pages/home_page.dart` (Interfaz en Morado)

```dart
import 'package:flutter/material.dart';
import '../services/firebase_service.dart';

class Home extends StatefulWidget {
  @override
  _HomeState createState() => _HomeState();
}

class _HomeState extends State<Home> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Juguetería Ivette'),
        backgroundColor: Colors.deepPurple,
      ),
      body: FutureBuilder(
        future: getJuguetes(),
        builder: ((context, snapshot) {
          if (snapshot.hasData) {
            return ListView.builder(
              itemCount: snapshot.data?.length,
              itemBuilder: (context, index) {
                return ListTile(
                  title: Text(snapshot.data?[index]['nombre'], 
                       style: TextStyle(color: Colors.deepPurple, fontWeight: FontWeight.bold)),
                  subtitle: Text("Precio: \$${snapshot.data?[index]['precio']} - Stock: ${snapshot.data?[index]['stock']}"),
                  trailing: IconButton(
                    icon: Icon(Icons.delete, color: Colors.purpleAccent),
                    onPressed: () async {
                      await deleteJuguete(snapshot.data?[index]['uid']);
                      setState(() {}); // Refrescar
                    },
                  ),
                );
              },
            );
          } else {
            return const Center(child: CircularProgressIndicator(color: Colors.deepPurple));
          }
        }),
      ),
      floatingActionButton: FloatingActionButton(
        backgroundColor: Colors.deepPurple,
        onPressed: () async {
          // Aquí llamarías a la página de agregar, por simplicidad:
          await addJuguete("Carrito", 25.0, 10);
          setState(() {});
        },
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

---

## 🚀 Práctica Guiada con Antigravity (Paso 7)

**Antigravity** se refiere a simplificar el desarrollo eliminando la "fricción" o el "peso" de procesos complejos. Para una práctica con estudiantes:

1.  **Fase de Despegue:** Los estudiantes no deben preocuparse por el backend complejo. Usamos Firestore porque es *NoSQL* y nos permite "lanzar" datos rápidamente.
2.  **Zero Gravity UI:** Utilicen el widget `ThemeData` en `main.dart` para que toda la app herede el color morado automáticamente, evitando repetir código.
3.  **Desafío Práctico:** * Pide a los estudiantes que añadan un cuarto campo llamado `categoria` (ej. Peluches, Acción). 
    * Deben modificar el `firebase_service.dart` y la UI para reflejar este cambio.

> **Nota para el docente:** Asegúrate de que los estudiantes descarguen el archivo `google-services.json` desde la consola de Firebase y lo coloquen en `android/app/`, de lo contrario, la app no despegará.

¿Te gustaría que profundicemos en cómo validar que los campos de precio y stock solo acepten números?

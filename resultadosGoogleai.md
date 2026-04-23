¡Hola! Como creador de software, he diseñado este plan de trabajo integral.
Vamos a construir la aplicación "Juguetería CRUD" utilizando una arquitectura
limpia, siguiendo la metodología de Antigravity (orientada a agentes y flujos de
trabajo eficientes) y con una estética moderna en tonos morados.

1. Preparación del Entorno y Estructura

Primero, creamos la estructura de carpetas en tu sistema:

mkdir xflutterivette0684
cd xflutterivette0684
flutter create crudjugueteria
cd crudjugueteria

2. Configuración en Firebase Console

1.  Ve a Firebase Console.
2.  Crea un proyecto llamado crudjugueteria.
3.  En el menú lateral, ve a Firestore Database y haz clic en Crear base de
    datos.
4.  Selecciona "Modo prueba" (para desarrollo) y elige una ubicación de
    servidor.
5.  Crea una colección llamada juguetes.
6.  Registra tu app (Android/iOS) y descarga el archivo google-services.json
    (para Android) y colócalo en android/app/.

3. Librerías e Integración (pubspec.yaml)

Para implementar Firebase y el diseño, editamos el archivo pubspec.yaml:

dependencies:
  flutter:
    sdk: flutter
  # Librerías Core
  firebase_core: ^2.24.2
  cloud_firestore: ^4.14.0
  cupertino_icons: ^1.0.2

Instalación: Ejecuta flutter pub get en la terminal. Inicialización: En
lib/main.dart, asegúrate de inicializar Firebase:

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  runApp(const MyApp());
}

4. Metodología Antigravity: Agentes y Flujo de Trabajo

Para esta práctica guiada, dividiremos el desarrollo en Agentes Cognitivos. Cada
estudiante debe asumir o entender estos roles:

| Agente              | Rol           | Skill (Habilidad)                | Tarea                      |
| :------------------ | :------------ | :------------------------------- | :------------------------- |
| **Architect**       | Estructurador | Definición de Modelos            | Crear la clase `Juguete`.  |
| **Firebase Master** | Backend       | Gestión de Firestore             | Crear el servicio de CRUD. |
| **UI Designer**     | Estética      | Diseño de Widgets (Purple Theme) | Crear la interfaz visual.  |
| **Flow Controller** | Lógica        | Manejo de estados y navegación   | Unir la lógica con la UI.  |

5. Estructura de Archivos (Arquitectura Sugerida)

lib/
├── models/
│   └── juguete_model.dart
├── services/
│   └── firebase_service.dart
├── screens/
│   ├── home_screen.dart
│   └── add_edit_screen.dart
└── main.dart

6. Implementación del Código Funcional

A. El Modelo (Agente Architect)

lib/models/juguete_model.dart

class Juguete {
  String id;
  String nombre;
  double precio;
  int stock;

  Juguete({required this.id, required this.nombre, required this.precio, required this.stock});

  // Convertir de Firestore a Objeto
  factory Juguete.fromFirestore(Map<String, dynamic> data, String id) {
    return Juguete(
      id: id,
      nombre: data['nombre'] ?? '',
      precio: (data['precio'] ?? 0.0).toDouble(),
      stock: data['stock'] ?? 0,
    );
  }

  // Convertir de Objeto a JSON para Firestore
  Map<String, dynamic> toFirestore() {
    return {
      'nombre': nombre,
      'precio': precio,
      'stock': stock,
    };
  }
}

B. El Servicio CRUD (Agente Firebase Master)

lib/services/firebase_service.dart

import 'cloud_firestore/cloud_firestore.dart';
import '../models/juguete_model.dart';

class FirebaseService {
  final CollectionReference collection = FirebaseFirestore.instance.collection('juguetes');

  // Leer (Stream para tiempo real)
  Stream<List<Juguete>> getJuguetes() {
    return collection.snapshots().map((snapshot) =>
        snapshot.docs.map((doc) => Juguete.fromFirestore(doc.data() as Map<String, dynamic>, doc.id)).toList());
  }

  // Crear
  Future<void> addJuguete(Juguete juguete) => collection.add(juguete.toFirestore());

  // Actualizar
  Future<void> updateJuguete(Juguete juguete) => collection.doc(juguete.id).update(juguete.toFirestore());

  // Borrar
  Future<void> deleteJuguete(String id) => collection.doc(id).delete();
}

C. La Interfaz de Usuario (Agente UI Designer & Flow Controller)

lib/screens/home_screen.dart (Color Morado Atractivo)

import 'package:flutter/material.dart';
import '../services/firebase_service.dart';
import '../models/juguete_model.dart';
import 'add_edit_screen.dart';

class HomeScreen extends StatelessWidget {
  final FirebaseService _service = FirebaseService();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Juguetería Ivette'),
        backgroundColor: Colors.deepPurple,
      ),
      body: StreamBuilder<List<Juguete>>(
        stream: _service.getJuguetes(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return const Center(child: CircularProgressIndicator(color: Colors.deepPurple));
          
          return ListView.builder(
            itemCount: snapshot.data!.length,
            itemBuilder: (context, index) {
              final juguete = snapshot.data![index];
              return Card(
                color: Colors.deepPurple.shade50,
                margin: const EdgeInsets.symmetric(horizontal: 10, vertical: 5),
                child: ListTile(
                  title: Text(juguete.nombre, style: const TextStyle(color: Colors.deepPurple, fontWeight: FontWeight.bold)),
                  subtitle: Text('Precio: \$${juguete.precio} - Stock: ${juguete.stock}'),
                  trailing: Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      IconButton(
                        icon: const Icon(Icons.edit, color: Colors.purple),
                        onPressed: () => Navigator.push(context, MaterialPageRoute(builder: (_) => AddEditScreen(juguete: juguete))),
                      ),
                      IconButton(
                        icon: const Icon(Icons.delete, color: Colors.redAccent),
                        onPressed: () => _service.deleteJuguete(juguete.id),
                      ),
                    ],
                  ),
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        backgroundColor: Colors.deepPurple,
        child: const Icon(Icons.add),
        onPressed: () => Navigator.push(context, MaterialPageRoute(builder: (_) => const AddEditScreen())),
      ),
    );
  }
}

D. Formulario Crear/Editar

lib/screens/add_edit_screen.dart

import 'package:flutter/material.dart';
import '../models/juguete_model.dart';
import '../services/firebase_service.dart';

class AddEditScreen extends StatefulWidget {
  final Juguete? juguete;
  const AddEditScreen({this.juguete});

  @override
  _AddEditScreenState createState() => _AddEditScreenState();
}

class _AddEditScreenState extends State<AddEditScreen> {
  final _formKey = GlobalKey<FormState>();
  final _nombreCtrl = TextEditingController();
  final _precioCtrl = TextEditingController();
  final _stockCtrl = TextEditingController();

  @override
  void initState() {
    if (widget.juguete != null) {
      _nombreCtrl.text = widget.juguete!.nombre;
      _precioCtrl.text = widget.juguete!.precio.toString();
      _stockCtrl.text = widget.juguete!.stock.toString();
    }
    super.initState();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.juguete == null ? 'Nuevo Juguete' : 'Editar Juguete'),
        backgroundColor: Colors.deepPurple,
      ),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              TextFormField(controller: _nombreCtrl, decoration: const InputDecoration(labelText: 'Nombre', labelStyle: TextStyle(color: Colors.deepPurple))),
              TextFormField(controller: _precioCtrl, decoration: const InputDecoration(labelText: 'Precio'), keyboardType: TextInputType.number),
              TextFormField(controller: _stockCtrl, decoration: const InputDecoration(labelText: 'Stock'), keyboardType: TextInputType.number),
              const SizedBox(height: 20),
              ElevatedButton(
                style: ElevatedButton.styleFrom(backgroundColor: Colors.deepPurple),
                onPressed: () {
                  final j = Juguete(
                    id: widget.juguete?.id ?? '',
                    nombre: _nombreCtrl.text,
                    precio: double.parse(_precioCtrl.text),
                    stock: int.parse(_stockCtrl.text),
                  );
                  if (widget.juguete == null) {
                    FirebaseService().addJuguete(j);
                  } else {
                    FirebaseService().updateJuguete(j);
                  }
                  Navigator.pop(context);
                },
                child: const Text('Guardar Juguete', style: TextStyle(color: Colors.white)),
              )
            ],
          ),
        ),
      ),
    );
  }
}

7. Resumen de Flujo de Trabajo (Para Estudiantes)

1.  Agent Architect: Define los datos (clase Juguete).
2.  Agent Firebase: Conecta la nube (Firebase Console) y crea los métodos CRUD.
3.  Agent Designer: Aplica el color deepPurple y crea las tarjetas.
4.  Testing: Ejecuta flutter run y verifica que los datos se reflejen en la
    consola de Firebase al instante.

Este proyecto es una base sólida. El uso de Antigravity fomenta la separación de
responsabilidades, lo que facilita el mantenimiento del software. ¡Éxito con tu
práctica!

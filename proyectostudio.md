Proyecto crud fotografiasfamiliares en flutter con console firebase, proporciona un plan de trabajo de acuerdo a lo siguiente:

creacion de careta xflutterherrrera0606 y subcarpeta crupstudio,
trabajar con antigravity
crear base de datos (studiofamiliar) con firestore desde consola de firebase

que librerias se integran para implementarlo 
por ejemplo cómo se agrega firebase core y cómo se modifica el archivo pubspec.yaml

cómo crear proyecto, Implementar el crud (crear, leer, actualizar y borrar) 
para trabajar con fotografias, con tres campos (nombre,precio y stock de hoja) mostrar los archivos correspondientes, en dart con su código, que sea totalmente funcional, actúa como un creador de software. 

puedes profundizar tu respuesta para realizar una práctica guiada trabajando con antigravity

muestrame la metodologia paso a paso, para crear agentes, roles, skills y flujo de trabajo para el proyecto crud studio en flutter con console firebase, mostrar estructura de carpetas, que sea totalmente funcional

¡Excelente elección! Como arquitecto de software, te guiaré en la construcción de este sistema. Vamos a integrar la potencia de **Flutter** y **Firebase** con la metodología de orquestación de **Antigravity** (basada en agentes y flujos de trabajo especializados).

Aquí tienes el plan de ejecución detallado para tu proyecto `xflutterherrrera0606/crupstudio`.

---

## 1. Configuración del Entorno y Firebase

### Estructura de Directorios
Primero, asegúrate de tener la ruta física establecida:
```bash
# Crear carpetas
mkdir -p xflutterherrrera0606/crupstudio
cd xflutterherrrera0606/crupstudio

# Crear proyecto Flutter
flutter create .
```

### Consola de Firebase
1.  Ve a [Firebase Console](https://console.firebase.google.com/).
2.  Crea un proyecto llamado `studiofamiliar`.
3.  Habilita **Firestore Database** en modo de prueba.
4.  Crea una colección llamada `fotografias`.
5.  Registra tu app (Android/iOS/Web) y descarga el archivo `google-services.json` (para Android) o `GoogleService-Info.plist` (para iOS) y colócalo en la carpeta respectiva (`android/app/` o `ios/Runner/`).

---

## 2. Dependencias (pubspec.yaml)

Para que el CRUD y Firebase funcionen, modifica tu `pubspec.yaml` de la siguiente forma:

```yaml
dependencies:
  flutter:
    sdk: flutter
  # Core de Firebase
  firebase_core: ^2.24.2
  # Base de datos Firestore
  cloud_firestore: ^4.14.0
  # Manejo de estados (Recomendado para trabajar con flujos)
  provider: ^6.1.1
```

**Instalación:** Ejecuta `flutter pub get` en la terminal.

---

## 3. Metodología Antigravity: Agentes y Roles

En el contexto de **Antigravity**, definiremos un flujo de trabajo basado en responsabilidades separadas para mantener el código limpio y funcional.

| Agente | Rol | Skill (Habilidad) |
| :--- | :--- | :--- |
| **DataAgent** | Manejo de Firestore | CRUD directo: `add`, `get`, `update`, `delete`. |
| **LogicAgent** | Orquestador de Negocio | Validaciones de datos (nombre, precio, stock). |
| **UIAgent** | Interfaz de Usuario | Renderizado de formularios y listas dinámicas. |

### Flujo de Trabajo (Workflow)
1. **Trigger:** El usuario presiona "Guardar".
2. **Action:** `LogicAgent` valida que el precio sea positivo y el stock no sea nulo.
3. **Dispatch:** `DataAgent` envía el mapa de datos a la colección `studiofamiliar`.
4. **Notify:** `UIAgent` refresca la lista automáticamente mediante un `StreamBuilder`.

---

## 4. Implementación del Código (CRUD Funcional)

### Estructura de Carpetas Recomendada
```text
lib/
├── agents/
│   ├── data_agent.dart    (Lógica Firebase)
│   └── logic_agent.dart   (Validaciones)
├── models/
│   └── fotografia.dart    (Modelo de datos)
├── ui/
│   ├── list_view.dart     (Lectura y Borrado)
│   └── form_view.dart     (Creación y Actualización)
└── main.dart              (Inicialización)
```

### El Modelo: `lib/models/fotografia.dart`
```dart
class Fotografia {
  String id;
  String nombre;
  double precio;
  int stockHoja;

  Fotografia({required this.id, required this.nombre, required this.precio, required this.stockHoja});

  Map<String, dynamic> toMap() => {
    "nombre": nombre,
    "precio": precio,
    "stockHoja": stockHoja,
  };
}
```

### El Agente de Datos: `lib/agents/data_agent.dart`
```dart
import 'cloud_firestore/cloud_firestore.dart';

class DataAgent {
  final FirebaseFirestore _db = FirebaseFirestore.instance;

  // CREATE
  Future<void> createFoto(String nombre, double precio, int stock) async {
    await _db.collection('fotografias').add({
      'nombre': nombre,
      'precio': precio,
      'stockHoja': stock,
    });
  }

  // READ (Stream para tiempo real)
  Stream<QuerySnapshot> getFotos() {
    return _db.collection('fotografias').snapshots();
  }

  // UPDATE
  Future<void> updateFoto(String id, String nombre, double precio, int stock) async {
    await _db.collection('fotografias').doc(id).update({
      'nombre': nombre,
      'precio': precio,
      'stockHoja': stock,
    });
  }

  // DELETE
  Future<void> deleteFoto(String id) async {
    await _db.collection('fotografias').doc(id).delete();
  }
}
```

### Interfaz de Usuario: `lib/main.dart`
```dart
import 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'agents/data_agent.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Inicialización vital
  runApp(MaterialApp(home: CrudStudioApp()));
}

class CrudStudioApp extends StatelessWidget {
  final DataAgent agent = DataAgent();
  final TextEditingController _nombreCtrl = TextEditingController();
  final TextEditingController _precioCtrl = TextEditingController();
  final TextEditingController _stockCtrl = TextEditingController();

  void _showForm(BuildContext context, {String? id}) {
    showModalBottomSheet(
      context: context,
      isScrollControlled: true,
      builder: (_) => Padding(
        padding: EdgeInsets.only(bottom: MediaQuery.of(context).viewInsets.bottom, left: 15, right: 15, top: 15),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(controller: _nombreCtrl, decoration: InputDecoration(labelText: 'Nombre')),
            TextField(controller: _precioCtrl, decoration: InputDecoration(labelText: 'Precio'), keyboardType: TextInputType.number),
            TextField(controller: _stockCtrl, decoration: InputDecoration(labelText: 'Stock de Hoja'), keyboardType: TextInputType.number),
            ElevatedButton(
              child: Text(id == null ? 'Crear' : 'Actualizar'),
              onPressed: () {
                if (id == null) {
                  agent.createFoto(_nombreCtrl.text, double.parse(_precioCtrl.text), int.parse(_stockCtrl.text));
                } else {
                  agent.updateFoto(id, _nombreCtrl.text, double.parse(_precioCtrl.text), int.parse(_stockCtrl.text));
                }
                Navigator.pop(context);
              },
            )
          ],
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Studio Familiar CRUD')),
      body: StreamBuilder(
        stream: agent.getFotos(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          return ListView.builder(
            itemCount: snapshot.data!.docs.length,
            itemBuilder: (context, index) {
              var doc = snapshot.data!.docs[index];
              return ListTile(
                title: Text(doc['nombre']),
                subtitle: Text("Precio: \$${doc['precio']} - Stock: ${doc['stockHoja']}"),
                trailing: Row(
                  mainAxisSize: MainAxisSize.min,
                  children: [
                    IconButton(icon: Icon(Icons.edit), onPressed: () => _showForm(context, id: doc.id)),
                    IconButton(icon: Icon(Icons.delete, color: Colors.red), onPressed: () => agent.deleteFoto(doc.id)),
                  ],
                ),
              );
            },
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => _showForm(context),
        child: Icon(Icons.add),
      ),
    );
  }
}
```

---

## 5. Resumen de Flujo CrudStudio

1.  **Agentes:** Cada función (DataAgent) se encarga de una sola cosa.
2.  **Firestore:** `studiofamiliar` recibe los datos en tiempo real.
3.  **UI:** Se actualiza automáticamente gracias al `StreamBuilder`.
4.  **Campos:** Se cumplen estrictamente los campos de **nombre**, **precio** y **stock de hoja**.

> **Nota de Seguridad:** No olvides configurar las reglas de Firebase Firestore en la consola para permitir lectura/escritura mientras estás en fase de desarrollo.

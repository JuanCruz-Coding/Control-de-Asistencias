# **Guía Rápida: Configurar Firebase para tu App de Fichadas**

Juanchi, esta es la guía "a prueba de balas" para tener tu base de datos lista en 10 minutos.

### **Paso 1: Crear el Proyecto en Firebase**

1. Andá a [https://firebase.google.com/](https://firebase.google.com/)  
2. Iniciá sesión con tu cuenta de Google (o creá una).  
3. Hacé clic en **"Ir a la consola"** (arriba a la derecha).  
4. Hacé clic en **"+ Agregar un proyecto"**.  
5. Ponele un nombre (ej: "ControlDeAsistencia").  
6. Desactivá la opción de "Google Analytics para este proyecto" (no lo necesitamos y hace la creación más rápida).  
7. Hacé clic en "Crear proyecto". Esperá a que termine.

### **Paso 2: Activar los Servicios que Usaremos**

Una vez dentro de tu proyecto, en el menú de la izquierda, vamos a activar dos cosas:

**A. Autenticación (Authentication)**

1. Hacé clic en **Authentication**.  
2. Hacé clic en **"Comenzar"**.  
3. Te mostrará muchos "Proveedores". Hacé clic en **"Anónimo"**.  
4. Habilitálo y dale "Guardar".  
   * *¿Por qué?* Esto nos da un userId único por cada celular, sin que el empleado tenga que crear un usuario y contraseña. Es perfecto para esto.

**B. Base de Datos (Firestore)**

1. En el menú, hacé clic en **Firestore Database**.  
2. Hacé clic en **"Crear base de datos"**.  
3. **¡IMPORTANTE\!** Te va a preguntar por las Reglas de Seguridad. Elegí **"Comenzar en modo de prueba"**.  
   * *¿Por qué?* Esto nos deja leer y escribir en la base de datos mientras desarrollamos. *Antes de salir a producción, hay que cambiar esto por reglas seguras*.  
4. Te va a pedir una ubicación. Elegí la más cercana (usualmente algo como southamerica-east1 o us-central).  
5. Hacé clic en "Habilitar".

### **Paso 3: Obtener las "Credenciales" (El firebaseConfig)**

Tu app web (el HTML) necesita "la llave y la contraseña" para saber a qué base de datos conectarse.

1. En el menú principal de tu proyecto, hacé clic en el **ícono de engranaje** (al lado de "Descripción general del proyecto").  
2. Andá a **"Configuración del proyecto"**.  
3. En la pestaña "General", bajá hasta que veas "Tus apps".  
4. Hacé clic en el ícono que dice **\</\>** (que significa "App Web").  
5. Ponele un "apodo" a tu app (ej: "Fichada Movil Web").  
6. Hacé clic en **"Registrar app"**.  
7. **¡AHÍ ESTÁ\!** Firebase te va a mostrar un código. Buscá el objeto que empieza con const firebaseConfig \= { ... }.

Ese objeto firebaseConfig es tu tesoro. Cópialo.

### **Paso 4: ¿Qué hago con esas credenciales?**

En el código que yo te di (fichada\_movil.html), vas a ver líneas como:

const firebaseConfig \= JSON.parse(typeof \_\_firebase\_config \!== 'undefined' ? \_\_firebase\_config : '{}');  
const authToken \= typeof \_\_initial\_auth\_token \!== 'undefined' ? \_\_initial\_auth\_token : null;

Esto es "magia" de este entorno de chat para conectarse solo.

Si vos quisieras probar este archivo fichada\_movil.html en **tu propia computadora** (fuera de este chat), deberías:

1. Crear el archivo fichada\_movil.html en tu PC.  
2. Borrar esas líneas de \_\_firebase\_config y \_\_initial\_auth\_token.  
3. **Pegar** el objeto firebaseConfig que copiaste en el Paso 3\.

El código de initApp() quedaría así:

// ...  
import { getAuth, signInAnonymously } from "...";  
import { getFirestore } from "...";

// PEGARÍAS TU CONFIG ACÁ  
const firebaseConfig \= {  
  apiKey: "TU\_API\_KEY\_SECRETA",  
  authDomain: "tu-proyecto.firebaseapp.com",  
  projectId: "tu-proyecto",  
  storageBucket: "tu-proyecto.appspot.com",  
  messagingSenderId: "123456789",  
  appId: "1\_123456789\_web\_abcdef"  
};

// ...  
async function initApp() {  
    try {  
        // 2\. Inicializar Firebase  
        const app \= initializeApp(firebaseConfig); // Usa tu config  
        db \= getFirestore(app);  
        auth \= getAuth(app);  
          
        // 3\. Autenticar al usuario  
        onAuthStateChanged(auth, async (user) \=\> {  
            if (user) {  
                currentUserId \= user.uid;  
                habilitarApp();  
            } else {  
                await signInAnonymously(auth); // Logueo anónimo  
            }  
        });  
// ...

¡Listo\! Con eso, tu app HTML se conecta a *tu* base de datos Firebase.
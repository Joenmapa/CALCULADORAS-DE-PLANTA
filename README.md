# RecipeManager — Servidor de datos

## Instalación (una sola vez en la PC servidor)

```bash
pip install flask openpyxl flask-cors
```

## Configuración

1. Abre `excel_config.json`
2. Cambia `excel_path` a la ruta real de tu Excel, por ejemplo:
   ```
   \\\\servidor\\RecipesDB\\RecipeDatabase.xlsx
   ```
3. Ajusta los nombres de hojas y columnas para que coincidan exactamente con tu Excel

## Arrancar el servidor

```bash
python server.py
```

Verifica que funciona abriendo en el navegador:
```
http://localhost:5000/api/health
```

Desde otras PCs de la red:
```
http://192.168.X.X:5000/api/health   ← usa la IP de la PC donde corre el servidor
```

## Conectar el HTML

En el archivo `RecipeManager_MASTER_v4.html`, reemplaza la función `loadData()` con:

```js
const API_URL = 'http://192.168.X.X:5000/api';  // ← IP del servidor

async function loadData() {
  try {
    const res = await fetch(API_URL + '/data');
    if (!res.ok) throw new Error('HTTP ' + res.status);
    DB = await res.json();
    rmMap = {};
    DB.rawMaterials.forEach(r => rmMap[r.code] = r);
    renderList();
    document.getElementById('loading').style.display = 'none';
    document.getElementById('app').style.display = 'flex';
  } catch (e) {
    document.getElementById('loading').innerHTML =
      '<p style="color:red;padding:20px">Error conectando al servidor:<br>' + e.message +
      '<br><br>Verifica que el servidor esté corriendo en ' + API_URL + '</p>';
  }
}
```

## Estructura del Excel esperada

### Hoja: Productos
| Código | Descripción | Brand | Sabor | Disolución | Batch (LT) | Línea |
|--------|-------------|-------|-------|------------|------------|-------|
| P001   | Jugo naranja | MarcaX | Naranja | 1 | 10000 | A3FLEX |

### Hoja: MateriasPrimas
| Código | Descripción | Tipo | Unidad | Brix |
|--------|-------------|------|--------|------|
| RM001  | Conc. naranja | CONCENTRATE | LB | 65 |

### Hoja: Recetas
| CódigoProducto | CódigoIngrediente | Cantidad |
|----------------|-------------------|----------|
| P001           | RM001             | 245.5    |
| P001           | RM002             | 1200     |

> Una fila por ingrediente por producto.

## Para que el servidor arranque automático con Windows

Crea un acceso directo a `server.py` en:
```
C:\Users\TuUsuario\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup
```
O configura una tarea programada en el Administrador de tareas de Windows.

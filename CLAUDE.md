# CLAUDE.md — MTB Forms

Formulario web para conectar credenciales de cuentas **XM** (plataforma de trading) con el robot de trading automatizado **MTB**. El usuario ingresa sus datos y credenciales; el formulario los registra en NocoDB.

---

## Stack tecnológico

| Capa | Tecnología |
|------|-----------|
| Frontend | HTML5 + Vanilla JavaScript (ES6+) |
| Estilos | Tailwind CSS (CDN) + configuración inline |
| Iconos | Material Symbols Outlined (Google Fonts) |
| Tipografía | Lexend (Google Fonts) |
| Backend | NocoDB (API REST) |
| Control de versiones | Git + GitHub |

No hay framework, bundler, ni dependencias npm. Todo en un único archivo HTML.

---

## Estructura del proyecto

```
mtb-forms/
├── index.html        # Aplicación completa (HTML + CSS inline + JS inline)
└── old_index.html    # Versión anterior — usaba n8n webhook como backend
```

---

## Flujo de la aplicación

```
Usuario llena formulario (nombre, email, cuenta XM, password, servidor)
    ↓
Submit → validación HTML5 nativa
    ↓
POST /api/v2/tables/{TABLE_ID}/records → NocoDB
    ↓
Éxito: oculta #formPage, muestra #successPage
Error: muestra caja de error inline
```

### Vistas (en el mismo HTML)

| ID | Descripción | Visible inicial |
|----|-------------|----------------|
| `#formPage` | Formulario principal | Sí |
| `#successPage` | Confirmación de envío | No (se muestra al éxito) |

---

## Campos del formulario

| Campo HTML id | Tipo | Validación |
|---------------|------|-----------|
| `nombre` | text | required |
| `email` | email | required, type=email |
| `xm_account` | text | required |
| `xm_password` | password | required, minlength="6" |
| `xm_server` | select | required |
| `autorizacion` | checkbox | required |

**Servidores XM disponibles:** XMGlobal-Real 1 al XMGlobal-Real 10.

---

## Integración con NocoDB

```javascript
const NOCODB_TOKEN = "2hoJ8xCl69bYvz21JGFa1aKJ3svVVLDnFtvmPLJz";
const NOCODB_TABLE_ID = "mpdoejihjtpcb3v";
// Endpoint: https://app.nocodb.com/api/v2/tables/{TABLE_ID}/records
```

**Payload enviado:**
```json
{
  "nombre": "...",
  "email": "...",
  "xm_account": "...",
  "xm_password": "...",
  "xm_server": "XMGlobal-Real N",
  "autorizacion": true,
  "autorizacion_at": "ISO timestamp"
}
```

> ⚠️ **Seguridad:** El token de NocoDB está expuesto en el cliente (visible en el HTML fuente). Si el token se rota, actualizar directamente en `index.html`. Considerar a futuro usar un proxy backend o serverless function para ocultar el token.

---

## Funciones JavaScript

| Función | Descripción |
|---------|-------------|
| `togglePassword()` | Alterna visibilidad del campo password; cambia ícono entre `visibility` / `visibility_off` |
| `safeReadJson(response)` | Helper — lee JSON de la respuesta HTTP manejando content-types inesperados |
| Event listener `submit` | Valida, muestra loading, envía a NocoDB, maneja éxito/error |

---

## Colores personalizados (Tailwind config inline)

```javascript
colors: {
  primary: '#1a73e8',        // Azul principal
  'mtb-blue': '#0d47a1',    // Azul oscuro MTB
  'mtb-light': '#e3f2fd',   // Azul claro
}
```

---

## Historial relevante

- **Versión actual (`index.html`):** backend NocoDB, campos con nombres en inglés
- **Versión anterior (`old_index.html`):** backend n8n webhook (`/webhook/connect-robotvr`), campos con guiones (`cuenta-xm`, `password-xm`)

Si se necesita volver al flujo de n8n, ver `old_index.html` como referencia.

---

## Pendientes / notas

| Ítem | Prioridad |
|------|-----------|
| Mover token NocoDB a un backend/proxy para no exponerlo en cliente | Alta |
| Agregar validación de formato de cuenta XM | Media |
| Página de error con instrucciones de soporte | Baja |
| Considerar versión con n8n como paso intermedio (igual que old_index) | Baja |

---

## Contacto soporte

WhatsApp: +52 442 186 0803

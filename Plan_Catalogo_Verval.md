# 🗂️ Plan Estratégico — Catálogo Digital Verval

> **Objetivo:** Transformar el catálogo Word en un sistema profesional, actualizable con IA, que maximice ventas a través de web, QR, PDF imprimible y WhatsApp.

---

## 📊 Estado Actual

- **Catálogo:** ~30 productos en `.docx` sin estructura visual ni categorías
- **Web existente:** [empresasverval.cl](https://empresasverval.cl/) — funcional pero anticuada
- **Landing page:** Prototipo avanzado en Claude Code (pendiente de integrar)
- **Problema:** Precios como *"depende de la medida"* sin detalle → fricción de ventas
- **Oportunidad:** Unificar todo en un sistema con fuente de verdad única

---

## 🏗️ Arquitectura en 3 Capas

### Capa 1 — Fuente de Verdad: `productos.json`

Un solo archivo JSON del que se genera **todo lo demás** automáticamente.

```json
{
  "productos": [
    {
      "id": "001",
      "nombre": "Rollo Paletizar",
      "categoria": "Embalaje",
      "descripcion": "Película estirable de alta transparencia, ideal para paletizar de forma manual.",
      "precio": 3690,
      "unidad": "unidad",
      "unidades_por_caja": 6,
      "foto": "rollo-paletizar.jpg",
      "tags": ["paletizar", "polietileno", "manual"]
    }
  ]
}
```

**Herramienta:** VS Code  
**Flujo:** Editar JSON → Claude Code regenera PDF + web automáticamente  
**Ventaja:** Agregar un producto nuevo toma ~30 segundos con IA

---

### Capa 2 — Salidas para el Cliente

#### A) Página Web del Catálogo ⭐ prioritaria
- Integrada en la landing page existente como ruta `/catalogo`
- Lee el JSON y renderiza tarjetas de productos
- El cliente escanea el QR → ve el catálogo en su teléfono
- Botón "Consultar por WhatsApp" con mensaje pre-escrito

#### B) PDF Imprimible de Alta Calidad
- Generado automáticamente desde el mismo JSON
- Herramienta: Puppeteer (Node.js) — genera PDF desde HTML
- Flujo: Actualizas JSON → corres script → PDF listo. Sin tocar Word nunca más
- La web tiene botón "Descargar PDF"

> 💡 **Ambas se complementan.** El QR apunta a la web, la web ofrece el PDF.

---

### Capa 3 — Flujo de Trabajo con IA

```
1. Agregas/editas producto en productos.json
           ↓
2. Claude Code regenera PDF + actualiza la web
           ↓
3. Subes a GitHub → Vercel despliega automáticamente
           ↓
4. El QR apunta siempre a la misma URL (nunca cambia)
```

---

## 🛠️ Stack Recomendado

| Necesidad | Herramienta | Por qué |
|---|---|---|
| Gestionar productos | JSON en VS Code | Simple, control total, funciona con IA |
| Catálogo web | Landing page existente (Claude Code) | No duplicar trabajo |
| PDF imprimible | Puppeteer (Node.js) | PDF desde HTML, calidad profesional |
| QR | qrcode.js (gratis) | Generado desde la propia web |
| WhatsApp | `wa.me/56...?text=...` | Sin integración técnica, funciona de inmediato |
| Fotos estandarizadas | Adobe Firefly / ChatGPT imagen | Prompt único para todos los productos |
| Hosting | Vercel (gratis) | Conecta con GitHub, deploy automático |

---

## 📦 Categorías de Productos (del catálogo actual)

- **Embalaje:** Rollo Paletizar, Cinta de Embalaje, Rollo Pullpack, Rollo Prepicado, Lámina Prepicada
- **Papel Kraft:** Bolsas Papel (100/200/500/700/800), Rollo Papel Kraft 40cm, Rollo Papel Kraft 57cm
- **Protección / Cocina:** Guantes Nitrilo, Film Alusa 30x300
- **Bandejas:** Bandeja Aluminio (C5–C40), Bandeja Plástica Sushi 2 Rolls, Bandeja Plástica Sushi 3 Rolls
- **Bolsas Especiales:** Bolsa para Pollo, Bolsa Vacío, Bolsa Camiseta 40x50
- **Aseo / Residuos:** Bolsa Aseo 50x70, 70x90, 80x110, 90x110, 110x120 T2

---

## 📸 Estandarización de Fotos con IA

Cuando tengas imágenes de productos, usar este prompt base en cualquier IA de imágenes (Firefly, DALL-E, Midjourney):

```
Fondo blanco neutro, iluminación uniforme de estudio,
producto centrado y bien encuadrado, estilo catálogo
comercial profesional, sin sombras duras, alta resolución.
```

**Herramientas:** Adobe Firefly (integrado en Photoshop) · ChatGPT imagen · Midjourney

---

## ✅ Pasos en Orden

- [ ] **Paso 1** — Crear `productos.json` con todos los productos actuales, bien estructurado y categorizado
- [ ] **Paso 2** — Crear prototipo visual HTML del catálogo (diseño de tarjetas, colores, tipografía)
- [ ] **Paso 3** — Validar diseño visualmente → aprobar paleta y layout
- [ ] **Paso 4** — Integrar en landing page de Claude Code como ruta `/catalogo`
- [ ] **Paso 5** — Agregar fotos reales o generadas con IA al JSON
- [ ] **Paso 6** — Generar PDF imprimible y QR
- [ ] **Paso 7** — Configurar botón de WhatsApp con mensaje pre-escrito
- [ ] **Paso 8** — Deploy en Vercel con dominio `empresasverval.cl`

---

## 🔗 Referencias

- Web actual: https://empresasverval.cl/
- Hosting recomendado: https://vercel.com
- WhatsApp link: `https://wa.me/56XXXXXXXXX?text=Hola,%20me%20interesa%20el%20catálogo%20Verval`
- Documentación Puppeteer (PDF): https://pptr.dev

---

## 💬 Notas y Decisiones Pendientes

- [ ] ¿El catálogo va en `empresasverval.cl/catalogo` o en un subdominio `catalogo.empresasverval.cl`?
- [ ] ¿Se muestran los precios públicamente o solo tras contacto por WhatsApp?
- [ ] ¿Productos con precio variable (bandejas aluminio, bolsas vacío) muestran rango o van a consultar?
- [ ] ¿Quiénes tienen acceso al repositorio GitHub para editar el JSON?

---

*Creado con Claude · Empresas Verval · [[Proyectos]]*

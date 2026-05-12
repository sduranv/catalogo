# 🏭 Plan Catálogo Digital — Empresas Verval

> [!info] Principio rector
> **Una sola fuente de verdad** que se renderiza en varios formatos. No mantener copias paralelas (un Word, un PDF en Canva, la web…). Editas en un lugar → todo se actualiza.

---

## Arquitectura general

```
                    ┌─── PDF imprimible / descargable
                    │
  AIRTABLE ─────────┼─── Web /catalogo (mobile-first, en landing actual)
  (fuente única)    │
                    └─── QR estable → apunta a /catalogo
```

- **Agregar producto**: 1 fila en Airtable + 1 foto → todo se actualiza solo.
- **Cambiar precio**: editas el campo → web refleja al instante, PDF se regenera con un comando.
- **QR impreso**: nunca caduca porque la URL no cambia.

---

## Fase 1 — Base de datos de productos

> [!tip] Tiempo estimado: 1–2 días

### Crear base en Airtable

Campos por producto:

| Campo | Tipo | Notas |
|---|---|---|
| `nombre` | Texto | Nombre comercial limpio |
| `categoria` | Select | Papel Kraft · Bolsas · Bandejas · Aseo · Embalaje Industrial |
| `descripcion_corta` | Texto | 1 línea para la card del catálogo web |
| `descripcion_comercial` | Texto largo | Orientada a venta, no genérica |
| `uso_recomendado` | Texto | Ejemplo: "Panaderías, restaurantes, catering" |
| `precio_unitario` | Moneda (CLP) | Precio base por unidad |
| `unidad_venta` | Select | unidad · caja · paquete · kg · saco |
| `unidades_por_bulto` | Número | Ej: 72 cintas por caja |
| `medidas_variantes` | Texto | Si tiene múltiples medidas |
| `foto_principal` | Attachment | Foto estandarizada (ver Fase 2) |
| `sku` | Texto | Código interno |
| `estado` | Select | Activo · Agotado · Destacado |
| `orden` | Número | Para controlar orden en el catálogo |

### Migrar productos del Word

- [ ] Usar Claude para convertir el .docx actual a CSV importable
- [ ] Prompt sugerido: *"Convierte este catálogo a CSV con columnas: nombre, categoria, descripcion_corta, uso_recomendado, precio_unitario, unidad_venta, unidades_por_bulto, medidas_variantes. Categoriza en: Papel Kraft, Bolsas Plásticas, Bandejas, Aseo Industrial, Embalaje Industrial. Mejora las descripciones para que sean comerciales y no repetitivas."*
- [ ] Importar CSV a Airtable
- [ ] Revisar y ajustar categorías manualmente

### Checklist Fase 1

- [ ] Base Airtable creada con todos los campos
- [ ] 25 productos migrados y categorizados
- [ ] Descripciones reescritas (no más "ideal para panaderías" x5)
- [ ] API key de Airtable generada para la web

---

## Fase 2 — Estandarización de fotos

> [!tip] Tiempo estimado: 2–3 días (puede ir en paralelo con Fase 1)

### Principio

Para productos industriales como los de Verval, **claridad > glamour**. Lo que vende es que el cliente identifique el producto en 1 segundo. La consistencia visual entre las 25+ fotos importa más que cuán linda sea cada una.

### Flujo recomendado

```
Foto con celular (fondo neutro)
        │
        ▼
Photoroom / Cleanup.pictures
(fondo blanco + sombra sutil)
        │
        ▼
Subir a Airtable
```

### Prompt fijo para estandarización AI

Si se usa una herramienta generativa para cleanup o ambientación:

```
Product photography, pure white seamless background,
soft diffused top lighting, subtle ground contact shadow,
product centered in frame, no props, no text overlays,
clean commercial style, high detail
```

> [!warning] Guardar este prompt
> Reutilizar exactamente el mismo para todos los productos. La consistencia viene de aplicar el mismo tratamiento a todo.

### Herramientas evaluadas

| Herramienta | Uso | Costo |
|---|---|---|
| **Photoroom** | Remover fondo + sombra + batch | Gratis limitado / Pro ~$10/mes |
| **Cleanup.pictures** | Remover fondo rápido | Gratis para uso básico |
| **Adobe Firefly** | Generación/extensión de fondo | Incluido en CC |
| **Nano Banana / Imagen** | Edición generativa | Variable |

### Checklist Fase 2

- [ ] Definir setup de foto (superficie, luz, ángulo estándar)
- [ ] Fotografiar todos los productos
- [ ] Procesar con Photoroom (batch si es posible)
- [ ] Subir fotos a Airtable
- [ ] Verificar que todas se ven consistentes en grid

---

## Fase 3 — Salidas del catálogo

> [!tip] Tiempo estimado: 3–5 días

### 3A — Catálogo web (`/catalogo`)

Se integra en el landing actual (Claude Code).

**Layout propuesto:**
1. Hero con barra de búsqueda y texto "Catálogo de productos"
2. Chips/filtros de categoría (Papel Kraft · Bolsas · Bandejas · etc.)
3. Grid de cards (foto, nombre, "desde $X", badge si es destacado)
4. Click en card → modal/drawer con:
   - Foto grande
   - Descripción comercial completa
   - Medidas / variantes
   - Unidad de venta y unidades por bulto
   - **CTA 1**: "Consultar por WhatsApp" (link wa.me con mensaje prellenado)
   - **CTA 2**: "Agregar a cotización" (mini carrito)
5. Botón flotante "Enviar cotización por WhatsApp" (junta todos los productos agregados en un solo mensaje)

**Conexión con Airtable:**
- Fetch desde API de Airtable en build time (SSG) o en runtime
- Alternativa: exportar JSON desde Airtable y commitear al repo (más simple, menos dinámico)

**Tracking:**
- Evento en cada click de WhatsApp con nombre del producto
- Evento en cada apertura de modal de producto
- UTM en links de QR para diferenciar origen

### 3B — PDF imprimible

Dos caminos:

| Opción | Pros | Contras |
|---|---|---|
| **Canva template** | Rápido, visual, fácil | Manual, se desalinea de la web |
| **Página `/catalogo/print`** + Puppeteer | Automático, siempre alineado | Requiere desarrollo |

**Recomendación**: ruta code-based con CSS `@media print` o página dedicada que renderiza los mismos datos de Airtable en layout de impresión.

```bash
# Generar PDF desde la línea de comandos
npm run catalog:pdf
# → genera catalogo-verval-2026-05.pdf
```

**Estructura del PDF:**
- Portada con logo, QR y datos de contacto
- Índice por categoría
- 1 página por categoría con grid de productos
- Contraportada con WhatsApp, email, dirección, QR grande

### 3C — Código QR

- URL estable: `https://empresasverval.cl/catalogo`
- Usar `qr-code-styling` (npm) para QR con logo Verval embebido
- Variantes con UTM para trackear origen:
  - `?ref=qr-tarjeta` → tarjetas de presentación
  - `?ref=qr-pdf` → portada del catálogo impreso
  - `?ref=qr-caja` → sticker en cajas despachadas
  - `?ref=qr-feria` → material de ferias/eventos

### Checklist Fase 3

- [ ] Página `/catalogo` funcionando en el landing
- [ ] Conexión con Airtable (API o JSON estático)
- [ ] Cards de producto con modal y CTAs
- [ ] Filtros por categoría
- [ ] Búsqueda por nombre
- [ ] Botón WhatsApp con mensaje prellenado por producto
- [ ] Mini carrito de cotización
- [ ] Script de generación de PDF
- [ ] QR generado con logo y variantes UTM
- [ ] QR insertado en portada del PDF

---

## Fase 4 — Mecánica de venta y mantenimiento

> [!tip] Tiempo estimado: 1 día de setup + mantenimiento continuo

### WhatsApp Business

- [ ] Activar WhatsApp Business (gratis) con número fijo de la empresa
- [ ] Crear catálogo nativo dentro de WhatsApp Business (aparece en el chat)
- [ ] Configurar mensaje de bienvenida automático
- [ ] Configurar respuestas rápidas para cotizaciones frecuentes

**Link de WhatsApp para el catálogo web:**
```
https://wa.me/569XXXXXXXX?text=Hola%2C%20me%20interesa%20cotizar%20[PRODUCTO]
```

### Analytics

- [ ] Instalar Plausible o Google Analytics en `/catalogo`
- [ ] Configurar eventos:
  - `producto_visto` → qué productos abren
  - `whatsapp_click` → qué productos cotizan
  - `cotizacion_enviada` → cuántos usan el mini carrito
  - `qr_origen` → de dónde llegan (tarjeta, caja, PDF, feria)
- [ ] Revisar métricas mensualmente para decidir qué destacar

### Flujo de mantenimiento

```
Producto nuevo:
  1. Foto → Photoroom → fondo blanco
  2. Fila nueva en Airtable (llenar todos los campos)
  3. Si web es SSG: rebuild del sitio
  4. Si web es runtime: aparece solo
  5. Regenerar PDF: npm run catalog:pdf

Cambio de precio:
  1. Editar campo en Airtable
  2. Web se actualiza (rebuild o automático)
  3. Regenerar PDF si se va a reimprimir

Producto agotado:
  1. Cambiar estado a "Agotado" en Airtable
  2. Card en web muestra badge "Agotado" (no desaparece)
```

---

## Stack de herramientas

| Qué | Herramienta | Por qué esta y no otra |
|---|---|---|
| Base de productos | **Airtable** | Imágenes nativas, API, vista mobile, gratis para este volumen |
| Fotos | **Photoroom** | Batch processing, consistencia automática, rápido |
| Descripciones | **Claude** (web o API) | Reescribir descripciones genéricas a copy comercial |
| Landing + catálogo web | **Claude Code** + framework actual | Ya existe el prototipo, se extiende |
| PDF | **Puppeteer/Playwright** via script | Misma data, mismo diseño, automatizado |
| QR | **qr-code-styling** (npm) | Permite logo embebido, personalizable |
| WhatsApp | **WhatsApp Business** (app) | Gratis, catálogo nativo, respuestas rápidas |
| Analytics | **Plausible** o **GA4** | Saber qué productos miran y cotizan |
| Documentación | **Obsidian** (este archivo) | Control del proyecto, prompts guardados, checklists |

---

## Prompts útiles guardados

### Migrar Word a CSV

```
Tengo un catálogo de productos en texto. Conviértelo a CSV con estas columnas:
nombre, categoria, descripcion_corta (1 línea comercial), uso_recomendado,
precio_unitario (solo número), unidad_venta, unidades_por_bulto, medidas_variantes.

Categorías posibles: Papel Kraft, Bolsas Plásticas, Bandejas y Envases,
Aseo Industrial, Embalaje Industrial.

Reescribe cada descripcion_corta para que sea única, comercial y orientada
al beneficio del cliente. No repitas frases entre productos.

[PEGAR CONTENIDO DEL WORD AQUÍ]
```

### Mejorar descripción de producto

```
Eres un copywriter B2B de insumos industriales en Chile.
Reescribe esta descripción de producto para que sea:
- Orientada al beneficio del cliente (ahorro, practicidad, higiene)
- Máximo 2 líneas
- Sin tecnicismos innecesarios
- Tono profesional pero cercano

Producto: [NOMBRE]
Descripción actual: [DESCRIPCIÓN]
Público: restaurantes, panaderías, hoteles, catering, supermercados
```

### Estandarizar foto de producto

```
Product photography, pure white seamless background,
soft diffused top lighting, subtle ground contact shadow,
product centered in frame, no props, no text overlays,
clean commercial style, high detail, slight reflection on surface
```

---

## Decisiones pendientes

- [ ] **Framework del landing**: ¿Next.js / Astro / otro? (define cómo conectar Airtable)
- [ ] **WhatsApp Business**: ¿ya tienen número fijo o hay que activar?
- [ ] **Dominio del catálogo**: ¿`empresasverval.cl/catalogo` o subdominio `catalogo.empresasverval.cl`?
- [ ] **Precios visibles**: ¿mostrar precios en la web o solo "solicitar cotización"?
- [ ] **Idioma**: ¿solo español o eventualmente inglés?

---

> [!success] Siguiente paso concreto
> Responder las decisiones pendientes → generar el CSV con Claude → importar a Airtable → mientras tanto, empezar con las fotos. Todo lo demás se construye sobre eso.

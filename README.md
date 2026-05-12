# Catálogo Digital — Empresas Verval

Sistema de catálogo digital para Empresas Verval. Fuente de verdad única en JSON que alimenta web, PDF imprimible y WhatsApp.

## Estructura

```
catalogo.json          → Base de datos de productos (fuente de verdad)
catalogo.docx          → Catálogo original en Word (referencia)
Plan_Catalogo_Verval.md → Plan estratégico del proyecto
```

## Productos

25 productos organizados en 7 categorías:

| Categoría | Productos |
|---|---|
| Embalaje | Rollo Paletizar, Cinta Embalaje, Rollo Pullpack, Film Alusa, Rollo Prepicado, Lámina Prepicada |
| Bolsas de Papel | Bolsa Papel 100, 200, 500, 700, 800 |
| Papel Kraft | Rollo Papel Kraft 40 cm, 57 cm |
| Protección | Guantes Nitrilo |
| Bandejas | Bandeja Aluminio, Bandeja Sushi 2 Rolls, Bandeja Sushi 3 Rolls |
| Bolsas Plásticas | Bolsa Camiseta 40x50, Bolsa para Pollo, Bolsa Vacío |
| Bolsas de Aseo | Bolsa Aseo 50x70, 70x90, 80x110, 90x110, 110x120 T2 |

## Plan del proyecto

1. **`productos.json`** — Crear y estructurar todos los productos *(completado)*
2. **Prototipo HTML** — Diseño de tarjetas, colores y tipografía
3. **Validar diseño** — Aprobar paleta y layout
4. **Integrar en landing** — Ruta `/catalogo` en la web existente
5. **Fotos** — Agregar imágenes reales o generadas con IA
6. **PDF + QR** — Generación automática con Puppeteer
7. **WhatsApp** — Botón de consulta con mensaje pre-escrito
8. **Deploy** — Vercel con dominio `empresasverval.cl`

## Stack

- **Datos:** JSON en VS Code
- **Web:** Landing page + ruta `/catalogo`
- **PDF:** Puppeteer (Node.js)
- **Hosting:** Vercel (deploy automático desde GitHub)
- **WhatsApp:** Link directo `wa.me`

## Flujo de actualización

```
Editar productos.json → Claude Code regenera PDF + web → Push a GitHub → Vercel despliega
```

---

Empresas Verval — [empresasverval.cl](https://empresasverval.cl/)

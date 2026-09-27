# Tienda online

Tienda online con panel de administración (productos, categorías, cupones, ventas, equipo y estética), hecha con React + Vite + Tailwind y Supabase como backend.

## Puesta en marcha

### 1. Supabase

1. En el proyecto de Supabase, ir a **SQL Editor** y ejecutar, en este orden, los archivos de `supabase/migrations/`:
   1. `20260823020000_base_schema.sql` (tablas, RLS, buckets de imágenes)
   2. `20260823000000_checkout_order_rpc.sql`
   3. `20260823010000_expire_pending_sales.sql`
2. Desplegar la Edge Function que crea empleados (con la [CLI de Supabase](https://supabase.com/docs/guides/cli)):
   ```bash
   supabase link --project-ref <tu-project-ref>
   supabase functions deploy create-employee
   ```
3. Crear el primer administrador:
   - **Authentication → Users → Add user**, con email y contraseña (marcar "Auto confirm").
   - En **SQL Editor**:
     ```sql
     insert into public.employees (user_id, name, email, role)
     select id, 'Administrador', email, 'admin'
     from auth.users where email = 'tu-email@ejemplo.com';
     ```
   Los demás empleados se crean desde el panel, en **Equipo**.

### 2. Variables de entorno

Los datos de conexión están en Supabase, en **Project Settings → API** (Project URL y la clave `anon` / publishable):

```
VITE_SUPABASE_URL=https://<tu-project-ref>.supabase.co
VITE_SUPABASE_ANON_KEY=<tu-anon-key>
```

- **Local:** copiar `.env.example` a `.env` y completar. El `.env` nunca se sube al repositorio.
- **Netlify:** cargarlas en **Site configuration → Environment variables**.

No se debe usar nunca la clave `service_role` en estas variables.

### 3. Netlify

Importar este repositorio en Netlify. `netlify.toml` ya define el build (`npm run build`, carpeta `dist`) y la redirección para las rutas de la SPA.

### 4. Desarrollo local

```bash
npm install
npm run dev
```

## Personalización

El nombre de la tienda, logo, colores, tipografías, textos de portada, datos fiscales y formato del ticket se configuran desde el panel, en **Estética**. Se guardan en la base de datos, no en el código.

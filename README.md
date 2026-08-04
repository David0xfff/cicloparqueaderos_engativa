# Cicloparqueaderos — Localidad de Engativá, Bogotá D.C.

Dataset en formato JSON con los cicloparqueaderos ubicados en la localidad de Engativá, Bogotá, Colombia. Pensado para ser consumido por una app (mapa, listado, filtros, etc.).

**Archivo:** `cicloparqueaderos_engativa.json`
**Total de registros:** 31
**Última actualización:** agosto 2026

---

## Estructura del JSON

El archivo es un **array de objetos**. Cada objeto representa un cicloparqueadero:

```json
{
  "id": "CPE-001",
  "fid": 1,
  "nombre": "Carvajal",
  "tipo": "cicloparqueadero",
  "localidad": "Engativá",
  "barrio": "San Cayetano",
  "coordenadas": {
    "lat": 4.66161631,
    "lng": -74.10915202
  },
  "globalId": "1f1d6596-a062-49f8-a929-ad5253f477ec"
}
```

## Diccionario de campos

| Campo | Tipo | Descripción |
|---|---|---|
| `id` | string | Código único legible, formato `CPE-XXX` (Cicloparqueadero Engativá). |
| `fid` | number | Identificador numérico secuencial (1 a 31). |
| `nombre` | string | Nombre del sitio o punto de referencia asociado al cicloparqueadero. |
| `tipo` | string | Siempre `"cicloparqueadero"`. Útil si combinas este dataset con el de ciclotalleres en una misma app y necesitas diferenciarlos. |
| `localidad` | string | Siempre `"Engativá"`. |
| `barrio` | string | Barrio donde se ubica el sitio. Ver sección **Calidad del dato: Barrio** más abajo. |
| `coordenadas.lat` | number | Latitud en grados decimales (WGS84). |
| `coordenadas.lng` | number | Longitud en grados decimales (WGS84). |
| `globalId` | string | Identificador único global (UUID) heredado de la fuente original, en minúsculas y sin llaves `{}`. |

---

## Calidad del dato: campo `Barrio`

Este campo requiere atención antes de usarlo para decisiones críticas (por ejemplo, agrupar estadísticas por barrio):

- **Sin marca especial** → el barrio fue confirmado con una fuente directa (sitio del negocio, Alcaldía Local, Wikipedia de estaciones de TransMilenio, directorios de ciudad).
- **Con sufijo `(aprox.)`** → el barrio fue inferido por cercanía a otros puntos ya confirmados en el mismo corredor vial (ej. Av. 68, Calle 80). Es una buena aproximación pero **no está verificado con una fuente oficial**.
- Un registro (`City Parking Hotel Marriott`, barrio *Franco*) queda administrativamente en la localidad de **Fontibón**, no Engativá, aunque la fuente original de datos lo clasificaba como Engativá. Se conservó tal cual pero conviene decidir si se excluye según el alcance de tu proyecto.

**Recomendación:** si tu app necesita el barrio exacto de forma confiable, valida los puntos marcados `(aprox.)` contra Google Maps o la cartografía oficial de la Secretaría Distrital de Planeación antes de usarlos en reportes o toma de decisiones.

---

## Ejemplo de uso

### JavaScript / fetch
```javascript
fetch('cicloparqueaderos_engativa.json')
  .then(res => res.json())
  .then(data => {
    data.forEach(sitio => {
      console.log(sitio.nombre, sitio.coordenadas.lat, sitio.coordenadas.lng);
    });
  });
```

### Filtrar solo los barrios confirmados (sin "(aprox.)")
```javascript
const confirmados = data.filter(s => !s.barrio.includes('(aprox.)'));
```

### Agregar un marcador en un mapa (Leaflet)
```javascript
data.forEach(sitio => {
  L.marker([sitio.coordenadas.lat, sitio.coordenadas.lng])
    .bindPopup(`<b>${sitio.nombre}</b><br>${sitio.barrio}`)
    .addTo(map);
});
```

---

## Origen de los datos

- Ubicación y GlobalID: archivo fuente `Cicloparqueadero_Engativa.xlsx` (coordenadas corregidas: la fuente original traía X/Y sin punto decimal).
- Barrio: investigación complementaria (búsqueda web) realizada por no contar el dataset original con este campo.

## Dataset relacionado

Si tu app también muestra ciclotalleres, usa `ciclotalleres_engativa.json`, que sigue exactamente la misma estructura de campos (con `"tipo": "ciclotaller"`), o el archivo combinado `engativa_bicis_completo.json` que trae ambos datasets en un solo JSON.

# 🔎 VALIDACIONES Y KPIs DEL DATASET ORO — BIGQUERY SQL

- Tabla central: grupo6-scotiabank.oro.hecho_riesgo
- Dimensiones: banco, indicador, moneda, fecha

## 1️⃣ Validación de valores nulos críticos

```sql
SELECT *
FROM `grupo6-scotiabank.oro.hecho_riesgo`
WHERE id_banco IS NULL
   OR id_fecha IS NULL
   OR id_moneda IS NULL
   OR id_indicador IS NULL
   OR valor IS NULL;
```


## 2️⃣ Validación de duplicados en la tabla de hechos

La clave de negocio debe ser única:

```sql
SELECT id_banco, id_fecha, id_moneda, id_indicador,
       COUNT(*) AS repeticiones
FROM `grupo6-scotiabank.oro.hecho_riesgo`
GROUP BY 1, 2, 3, 4
HAVING COUNT(*) > 1;
```

## 3️⃣ Validación semántica contra límites definidos (zona verde/amarilla/roja)

```sql
SELECT
  hr.id_banco,
  b.nombre AS banco_nombre,
  i.nombre AS indicador,
  hr.valor,
  i.lim_amarillo_min,
  i.lim_amarillo_max,
  i.flag_limite,
  CASE 
    WHEN i.flag_limite = 1 AND hr.valor < i.lim_amarillo_min THEN 'RIESGO'
    WHEN i.flag_limite = 1 AND hr.valor BETWEEN i.lim_amarillo_min AND i.lim_amarillo_max THEN 'ZONA AMARILLA'
    WHEN i.flag_limite = 1 AND hr.valor > i.lim_amarillo_max THEN 'ÓPTIMO'
    
    WHEN i.flag_limite = 0 AND hr.valor < i.lim_amarillo_min THEN 'ÓPTIMO'
    WHEN i.flag_limite = 0 AND hr.valor BETWEEN i.lim_amarillo_min AND i.lim_amarillo_max THEN 'ZONA AMARILLA'
    WHEN i.flag_limite = 0 AND hr.valor > i.lim_amarillo_max THEN 'RIESGO'
  END AS clasificacion
FROM `grupo6-scotiabank.oro.hecho_riesgo` hr
JOIN `grupo6-scotiabank.oro.dim_indicador` i USING(id_indicador)
JOIN `grupo6-scotiabank.oro.dim_banco` b USING(id_banco);
```

## 4️⃣ Ranking de bancos por indicador (competitividad)
```sql
SELECT
  hr.id_indicador,
  i.nombre AS indicador,
  hr.id_banco,
  b.nombre AS banco,
  hr.valor,
  RANK() OVER(PARTITION BY hr.id_indicador ORDER BY hr.valor DESC) AS ranking
FROM `grupo6-scotiabank.oro.hecho_riesgo` hr
JOIN `grupo6-scotiabank.oro.dim_banco` b USING(id_banco)
JOIN `grupo6-scotiabank.oro.dim_indicador` i USING(id_indicador);

5️⃣ Validación de cobertura temporal (evolución del dataset)
SELECT id_indicador, COUNT(DISTINCT id_fecha) AS meses_reportados
FROM `grupo6-scotiabank.oro.hecho_riesgo`
GROUP BY id_indicador
ORDER BY 2 DESC;
```

# 🚀 KPIs Y MÉTRICAS — ANÁLISIS EVOLUTIVO

## 6️⃣ KPI — Promedio histórico del indicador por banco

```sql
SELECT
  hr.id_banco,
  b.nombre AS banco,
  hr.id_indicador,
  i.nombre AS indicador,
  AVG(hr.valor) AS promedio_valor
FROM `grupo6-scotiabank.oro.hecho_riesgo` hr
JOIN `grupo6-scotiabank.oro.dim_banco` b USING(id_banco)
JOIN `grupo6-scotiabank.oro.dim_indicador` i USING(id_indicador)
GROUP BY 1,2,3,4
ORDER BY 3, AVG(hr.valor) DESC;
```

## 7️⃣ KPI — Tendencia mensual (variación porcentual)
```sql
WITH serie AS (
  SELECT
    id_banco,
    id_indicador,
    id_fecha,
    valor,
    LAG(valor) OVER(PARTITION BY id_banco, id_indicador ORDER BY id_fecha) AS valor_prev
  FROM `grupo6-scotiabank.oro.hecho_riesgo`
)
SELECT *,
       (valor - valor_prev) / NULLIF(valor_prev, 0) * 100 AS variacion_pct
FROM serie;
```

## 8️⃣ KPI — Detección de alertas activas

```
SELECT *
FROM (
  SELECT
    hr.*,
    i.flag_limite,
    CASE
      WHEN i.flag_limite = 1 AND hr.valor < i.lim_amarillo_min THEN 'ALERTA'
      WHEN i.flag_limite = 0 AND hr.valor > i.lim_amarillo_max THEN 'ALERTA'
    END AS alerta
  FROM `grupo6-scotiabank.oro.hecho_riesgo` hr
  JOIN `grupo6-scotiabank.oro.dim_indicador` i USING(id_indicador)
)
WHERE alerta IS NOT NULL;
```

## 9️⃣ KPI — Consolidado de salud financiera por banco
```sql
SELECT
  b.nombre AS banco,
  COUNT(CASE WHEN valor > lim_amarillo_max AND flag_limite = 1 THEN 1 END) AS kpis_optimos,
  COUNT(CASE WHEN valor BETWEEN lim_amarillo_min AND lim_amarillo_max THEN 1 END) AS kpis_amarillo,
  COUNT(CASE WHEN valor < lim_amarillo_min AND flag_limite = 1 THEN 1 END) AS kpis_rojos
FROM `grupo6-scotiabank.oro.hecho_riesgo` hr
JOIN `grupo6-scotiabank.oro.dim_indicador` i USING(id_indicador)
JOIN `grupo6-scotiabank.oro.dim_banco` b USING(id_banco)
GROUP BY banco
ORDER BY kpis_rojos DESC;
```
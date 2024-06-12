-------------------------------------------------------------------------



--1--Total de ventas en el año 2009:

  --¿Cuál es el total de ventas realizadas en el año 2009?

DECLARE @fecha_inicio datetime = '2009-01-01';
DECLARE @fecha_fin datetime = '2009-12-31';
SELECT SUM(total) AS Total_Ventas
FROM [ve].[documento]
WHERE fechaMovimiento BETWEEN @fecha_inicio AND @fecha_fin;

-------------------------------------------------------------------------

--2--Personas sin entradas registradas en la tabla personaDestino:

  --¿Cuáles son las personas que no tienen una entrada registrada en la tabla    personaDestino?


SELECT p.*

FROM [ma].[persona] p
LEFT JOIN [ma].[personaDestino] pd ON p.persona = pd.persona
WHERE pd.persona IS NULL;

-------------------------------------------------------------------------


--3--Promedio del monto total de transacciones de ventas:

  --¿Cuál es el promedio del monto total de todas las transacciones de ventas registradas en la base de datos, expresadas en moneda local (soles peruanos)?

SELECT FORMAT(AVG(total), 'C', 'es-PE') AS [Promedio del Monto Total en Soles]
FROM [ve].[documento];

-------------------------------------------------------------------------

--4--Documentos de ventas con monto total superior al promedio:

  --Obtenga una lista de todos los documentos de ventas cuyo monto total supere el promedio del monto total de todos los documentos de ventas registrados en la base de datos.

SELECT *
FROM [ve].[documento]
WHERE total > (SELECT AVG(total) FROM [ve].[documento]);

-------------------------------------------------------------------------

--5--Documentos de ventas pagados con una forma de pago específica:

  --Listar los documentos de ventas que han sido pagados utilizando una forma de pago específica desde la tabla documentoPago.


SELECT d.*
FROM [ve].[documentoPago] dp
INNER JOIN [ve].[documento] d ON dp.documento = d.documento
INNER JOIN [pa].[pago] p ON dp.pago = p.pago
WHERE p.formaPago = 1; -- 1 es pago al Contado
------------------------------------------------------------------------

--6--Detalles de documentos de ventas canjeados:

  --¿Cómo se distribuye el saldo total entre los diferentes almacenes, considerando la información de los saldos iniciales de inventario en la base de datos?



SELECT d.*, dc.*
FROM [ve].[documento] d
INNER JOIN [ve].[documentosCanjeados] dc ON d.documento = dc.documento01 OR d.documento = dc.documento02;

-------------------------------------------------------------------------

--7--Saldo total distribuido por almacén:

  --Obtenga una lista de todos los documentos de ventas cuyo monto total supere el promedio del monto total de todos los documentos de ventas registrados en la base de datos.


SELECT almacen, SUM(costoSoles) AS Saldo_Total
FROM [ma].[saldosIniciales]
GROUP BY almacen;
-------------------------------------------------------------------------

--8--Detalles de documentos de ventas por vendedor:

  --¿Cuáles son los detalles de los documentos de ventas asociados al vendedor con identificación número 3 en la base de datos, considerando la información detallada de cada documento en relación con sus elementos de venta?

SELECT d.*
FROM [ve].[documento] d
INNER JOIN [ve].[documentoDetalle] dd ON d.documento = dd.documento
WHERE d.vendedor = 3;

------------------------------------------------------------------------
--9--Total de ventas por año y vendedor:

  --¿Cuál es el total de ventas por año y vendedor en la base de datos de ventas, considerando solo aquellos vendedores cuya suma total de ventas en un año específico sea superior a 100,000 unidades monetarias?


SELECT vendedor, YEAR(fechaMovimiento) AS Anio, SUM(total) AS Total_Ventas
FROM ve.documento
GROUP BY vendedor, YEAR(fechaMovimiento)
HAVING SUM(total) > 100000;
-------------------------------------------------------------------------

--10--Desglose mensual de ventas por vendedor:

  --¿Cuál es el desglose mensual de las ventas por vendedor en cada año, considerando la suma total de ventas para cada mes y año específico?


SELECT vendedor, MONTH(fechaMovimiento) AS Anio, SUM(total) AS Total_Ventas
FROM ve.documento
GROUP BY vendedor, MONTH(fechaMovimiento)
HAVING SUM(total) > 100000;
-------------------------------------------------------------------------

--11--Clientes que compraron más de 10 veces en un año:

  --¿Cuántos clientes compraron más de 10 veces en un año?


SELECT persona, YEAR(fechaMovimiento) AS Año, COUNT(*) AS Compras
FROM ve.documento
WHERE tipoMovimiento = 1
GROUP BY persona, YEAR(fechaMovimiento)
HAVING COUNT(*) > 10;



-------------------------------------------------------------------------

--12--Total acumulado de descuentos por vendedor:

  --¿Cuál es el total acumulado de descuentos aplicados por cada vendedor en la base de datos de ventas, considerando la suma de los descuentos descto01, descto02 y descto03, y mostrando solo aquellos vendedores cuyo total de descuentos acumulados supere los 5000?



SELECT vendedor, SUM(descto01 + descto02 + descto03) AS Descuentos_Acumulados
FROM ve.documento
GROUP BY vendedor
HAVING SUM(descto01 + descto02 + descto03) > 5000;


-------------------------------------------------------------------------

--13--Total anual de ventas por persona:

  --¿Cuál es el total anual de ventas realizadas por cada persona en la base de datos de ventas, considerando únicamente los movimientos de tipo venta (tipoMovimiento = 1), y mostrando solo aquellas personas cuyas ventas anuales superan los 10000?



SELECT persona, YEAR(fechaMovimiento) AS Año, SUM(total) AS Total_Anual
FROM ve.documento
WHERE tipoMovimiento = 1
GROUP BY persona, YEAR(fechaMovimiento)
HAVING SUM(total) > 10000;

-------------------------------------------------------------------------

--14--Recuento total de productos vendidos por vendedor:

  --¿Cuál es el recuento total de productos vendidos por cada vendedor en la base de datos de ventas?


SELECT
 d.vendedor,
 COUNT(dd.documentoDetalle) AS Total_Productos_Vendidos
FROM
 ve.documentoDetalle dd
JOIN
 ve.documento d ON dd.documento = d.documento
GROUP BY
 d.vendedor;
-------------------------------------------------------------------------

--15--Ventas mensuales desglosadas por tipo de pago:

  --¿Cuánto se vendió cada mes del año 2009, desglosado por tipo de pago?


SELECT
MONTH(d.fechaMovimiento) AS Mes,
p.formaPago,
SUM(d.total) AS Total_Ventas
FROM
ve.documento d
JOIN
pa.pago p ON d.vendedor = p.vendedor
WHERE
YEAR(d.fechaMovimiento) = 2009
GROUP BY
MONTH(d.fechaMovimiento),
p.formaPago;

-------------------------------------------------------------------------
-------------------------------------------------------------------------

--16--Total de ventas en el año 2007:

  --¿Cuál es el total de ventas realizadas en el año 2007?


DECLARE @fecha_inicio datetime = '2007-01-01';
DECLARE @fecha_fin datetime = '2007-12-31';
SELECT SUM(total) AS Total_Ventas
FROM [ve].[documento]
WHERE fechaMovimiento BETWEEN @fecha_inicio AND @fecha_fin;

-------------------------------------------------------------------------
-------------------------------------------------------------------------


--17--Personas sin entradas registradas en la tabla personaDestino en el año 2008:

  --¿Cuáles son las personas que no tienen una entrada registrada en la tabla personaDestino en el año 2008?


SELECT p.*
FROM [ma].[persona] p
LEFT JOIN [ma].[personaDestino] pd ON p.persona = pd.persona AND YEAR(pd.fecha) = 2008
WHERE pd.persona IS NULL;

-------------------------------------------------------------------------
-------------------------------------------------------------------------

--18--Promedio del monto total de transacciones de ventas en el año 2009:

  --¿Cuál es el promedio del monto total de todas las transacciones de ventas registradas en la base de datos en el año 2009, expresadas en moneda local (soles peruanos)?

DECLARE @fecha_inicio datetime = '2009-01-01';
DECLARE @fecha_fin datetime = '2009-12-31';
SELECT FORMAT(AVG(total), 'C', 'es-PE') AS [Promedio del Monto Total en Soles]
FROM [ve].[documento]
WHERE fechaMovimiento BETWEEN @fecha_inicio AND @fecha_fin;

------------------------------------------------------------------------
-------------------------------------------------------------------------

--19--Documentos de ventas con monto total superior al promedio en el año 2005:

  --Obtenga una lista de todos los documentos de ventas cuyo monto total supere el promedio del monto total de todos los documentos de ventas registrados en la base de datos en el año 2005.

DECLARE @fecha_inicio datetime = '2005-01-01';
DECLARE @fecha_fin datetime = '2005-12-31';
DECLARE @promedio_monto_total decimal(18, 2) = (
 SELECT AVG(total)
 FROM [ve].[documento]
 WHERE fechaMovimiento BETWEEN @fecha_inicio AND @fecha_fin
);
SELECT *
FROM [ve].[documento]
WHERE fechaMovimiento BETWEEN @fecha_inicio AND @fecha_fin
AND total > @promedio_monto_total;
-------------------------------------------------------------------------
-------------------------------------------------------------------------

--20--Documentos de ventas pagados con una forma de pago específica en el año 2006:

  --Listar los documentos de ventas que han sido pagados utilizando una forma de pago específica desde la tabla documentoPago en el año 2006.

DECLARE @fecha_inicio datetime = '2006-01-01';
DECLARE @fecha_fin datetime = '2006-12-31';
DECLARE @forma_pago int = 1; -- Forma de pago específica
SELECT d.*
FROM [ve].[documentoPago] dp
INNER JOIN [ve].[documento] d ON dp.documento = d.documento
INNER JOIN [pa].[pago] p ON dp.pago = p.pago
WHERE p.formaPago = @forma_pag;
 AND d.fechaMovimiento BETWEEN @fecha_inicio AND @fecha_fin;

-----------------------------------------------------------------------
-------------------------------------------------------------------------

--21--Detalles de documentos de ventas canjeados en el año 2007:

  --¿Cómo se distribuye el saldo total entre los diferentes almacenes, considerando la información de los saldos iniciales de inventario en la base de datos en el año 2007?


SELECT s.almacen,
 SUM(s.costoSoles) AS Saldo_Tota
FROM [ma].[saldosIniciales] s
INNER JOIN [ve].[documento] d ON s.almacen = d.almacen -- Asumiendo que 'almacen' es el campo que une las dos tablas
INNER JOIN [ve].[documentosCanjeados] dc ON d.documento = dc.documento01 OR d.documento = dc.documento02
WHERE
 YEAR(d.fechaMovimiento) = 2007
GROUP BY
 s.almacen;

-------------------------------------------------------------------------
-------------------------------------------------------------------------


--22--Saldo total distribuido por almacén en el año 2008:

  --Obtén una lista de todos los documentos de ventas cuyo monto total supere el promedio del monto total de todos los documentos de ventas registrados en la base de datos en el año 2008.

 -- Consulta para obtener el saldo total distribuido por almacén en el año 2008

SELECT almacen,
SUM(costoSoles) AS Saldo_Total
FROM [ma].[saldosIniciales]
WHERE
YEAR(fecha) = 2008
GROUP BY
almacen;

  -- Consulta para obtener una lista de documentos de ventas cuyo monto total supere el promedio del monto total de todos los documentos de ventas registrados en la base de datos en el año 2008

SELECT *
FROM [ve].[documento]
WHERE total > (SELECT AVG(total)
FROM [ve].[documento]
WHERE YEAR(fechaMovimiento) = 2008);

-------------------------------------------------------------------------
-------------------------------------------------------------------------

--23--Detalles de documentos de ventas por vendedor en el año 2009:

  --¿Cuáles son los detalles de los documentos de ventas asociados al vendedor con identificación número 3 en la base de datos en el año 2009, considerando la información detallada de cada documento en relación con sus elementos de venta?


SELECT d.*, dd.*
FROM [ve].[documento] d
INNER JOIN [ve].[documentoDetalle] dd ON d.documento = dd.documento
WHERE d.vendedor = 3
AND YEAR(d.fechaMovimiento) = 2009;

-------------------------------------------------------------------------
-------------------------------------------------------------------------
--24--Total de ventas por año y vendedor en el año 2008:

  --¿Cuál es el total de ventas por año y vendedor en la base de datos de ventas, considerando solo aquellos vendedores cuya suma total de ventas en el año 2008 sea superior a 100,000 unidades monetarias?

SELECT vendedor,
YEAR(fechaMovimiento) AS Año,
SUM(total) AS Total_Ventas
FROM ve.documento
WHERE YEAR(fechaMovimiento) = 2008
GROUP BY vendedor, YEAR(fechaMovimiento)
HAVING SUM(total) > 100000;

-------------------------------------------------------------------------
-------------------------------------------------------------------------
--25--Desglose mensual de ventas por vendedor en el año 2009:

  --¿Cuál es el desglose mensual de las ventas por vendedor en cada año, considerando la suma total de ventas para cada mes y año específico en el año 2009?


SELECT vendedor,
YEAR(fechaMovimiento) AS Año,
MONTH(fechaMovimiento) AS Mes,
SUM(total) AS Total_Ventas
FROM ve.documento
WHERE YEAR(fechaMovimiento) = 2009
GROUP BY vendedor, YEAR(fechaMovimiento), MONTH(fechaMovimiento);

-------------------------------------------------------------------------
-------------------------------------------------------------------------

--26--Clientes que compraron más de 10 veces en un año en el año 2005:
  --¿Cuántos clientes compraron más de 10 veces en un año en el año 2005?

SELECT persona,
YEAR(fechaMovimiento) AS Año,
COUNT(*) AS Compras
FROM ve.documento
WHERE tipoMovimiento = 1
AND YEAR(fechaMovimiento) = 2005
GROUP BY persona, YEAR(fechaMovimiento)
HAVING COUNT(*) > 10;

-------------------------------------------------------------------------
-------------------------------------------------------------------------

--27--Total acumulado de descuentos por vendedor en el año 2006:


  --¿Cuál es el total acumulado de descuentos aplicados por cada vendedor en la base de datos de ventas, considerando la suma de los descuentos descto01, descto02 y descto03, y mostrando solo aquellos vendedores cuyo total de descuentos acumulados supere los 5000 en el año 2005?

SELECT vendedor,
SUM(descto01 + descto02 + descto03) AS Descuentos_Acumulados
FROM ve.documento
WHERE YEAR(fechaMovimiento) = 2005
GROUP BY vendedor
HAVING 
SUM(descto01 + descto02 + descto03) > 5000;

-------------------------------------------------------------------------
-------------------------------------------------------------------------

--28--Total anual de ventas por persona en el año 2007:

  --¿Cuál es el total anual de ventas realizadas por cada persona en la base de datos de ventas, considerando únicamente los movimientos de tipo venta (tipoMovimiento = 1), y mostrando solo aquellas personas cuyas ventas anuales superen los 10000 en el año 2007?

SELECT persona,
YEAR(fechaMovimiento) AS Año,
SUM(total) AS Total_Anual
FROM ve.documento
WHERE
tipoMovimiento = 1
AND YEAR(fechaMovimiento) = 2007
GROUP BY
persona, YEAR(fechaMovimiento)
HAVING
SUM(total) > 10000;
-------------------------------------------------------------------------
-------------------------------------------------------------------------

--29--Recuento total de productos vendidos por vendedor en el año 2008:
  --¿Cuál es el recuento total de productos vendidos por cada vendedor en la base de datos de ventas en el año 200


SELECT
 d.vendedor,
 COUNT(dd.documentoDetalle) AS Total_Productos_Vendidos
FROM
 ve.documentoDetalle dd
JOIN
 ve.documento d ON dd.documento = d.documento
WHERE
 YEAR(d.fechaMovimiento) = 2008
GROUP BY
 d.vendedor;

-------------------------------------------------------------------------
------------------------------------------------------------------------

    --30--Ventas mensuales desglosadas por tipo de pago en el año 2009:
    --¿Cuánto se vendió cada mes del año 2009, desglosado por tipo de pago?


SELECT
MONTH(d.fechaMovimiento) AS Mes,
p.formaPago,
SUM(d.total) AS Total_Ventas
FROM ve.documento d
JOIN
pa.pago p ON d.vendedor = p.vendedor
WHERE
YEAR(d.fechaMovimiento) = 2009
GROUP BY
MONTH(d.fechaMovimiento),
p.formaPago;

-------------------------------------------------------------------------
-------------------------------------------------------------------------
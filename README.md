# Introduccion

El objetivo de esta practica es realizar el diseño de una base de datos (quevedofest) para la gestion de Eventos (festivales). Con ella se podrá controlar tanto los ingresos como los gastos generados por cada Evento. Cada Evento tendrá una persona responsable de su gestión y los ingresos/gastos se registrarán teniendo en cuenta el tipo de ingreso/gasto.

La base de datos deberá permitir conocer en todo momento tanto los ingresos como los gastos de cada evento. Además permitirá obtener los ingresos/gastos por cada tipo con el fin de conocer la procedencia de los ingresos (patrocinadores,entradas,retransmisiones, grabaciones, etc...) y de los gastos (Montaje escenarios, equipos sonido, iluminación, etc...).

Los datos registrados al permitirnos conocer los ingresos/gastos agrupados por tipo, nos permitiran, además de conocer el beneficio o perdida del evento, analizar que ingresos o gastos han sido diferentes a lo previsto. Esto permitirá tenerlo en cuenta en la organización de próximos eventos.

___

# Modelo Conceptual
## Especificaciones
La base de datos constara de las siguientes tablas:

Persona: Constara de un ID, un NIF, un nombre , un apellido y fecha de nacimiento.

Las personas que facturaran los gastos seran generalmente del departamento de Administracion.

Departamento: Constara de un ID y un nombre.

Los nombres de los departamentos seran: Administracion, Seguridad, Tecnico, Voluntariado y Asuntos legales que a su vez podra ser un tipo de gasto

Eventos: Constara de un ID, Evento, Descrpcion, Lugar de celebración, Fecha de celebración

Ingresos: Constara de un ID, una fecha de ingreso, una cantidad y un precio.

Gastos: : Constara de un ID, una fecha de gasto, una cantidad y un precio.

TipoIngreso: Constara de un ID, un tipo de ingreso, un nombre y una descripcion del ingreso.

Los tipos de ingreso seran: Entradas, Patrocinadores, Tv, Streaming, Radio, Grabaciones y Asuntos legales.

TipoGasto: Constara de un ID, un tipo de gasto, un nombre y una descripcion del gasto.

Los tipos de gastos seran de todo tipo dependiendo las necesidades del festival.

## Diagrama Entidad-Relación
---

![Diagrama_ER](img\QuevedoFest_ER.png)

# Modelo Logico
## Modelo Relacional

```txt

Departamento (Id(PK), Nombre)
Persona (Id(PK), NIF, Nombre, Apellido, FechaNac,Id_Departamentos(FK))
Eventos (Id(PK), Evento, Descripcion, Lugar, Fecha, Id_persona(FK))
Ingresos (Id(PK), Fecha, Cantidad, Precio, Id_Eventos(FK), Id_TipoIngreso(FK))
Gastos (Id(PK), Fecha, Cantidad, Precio, Id_Eventos(FK), Id_TipoGasto(FK))
TipoIngreso (Id(PK), Tipo, Nombre, Descripcion)
TipoGasto (Id(PK), Tipo, Nombre, Descripcion)

```
## Normalizacion

El modelo ya se encuentra nomalizado.

# Modelo Fisico
## Diagrama de base de datos (notación "Crow's feet" o IDEF1X)
---

![Diagrama_BD](img\DiagramaBaseDatos.PNG)

## Creación de tablas y otros objetos

```sql

CREATE DATABASE quevedofest;
\c quevedofest;

CREATE TABLE departamento (
id INT NOT NULL,
nombre VARCHAR(50),
CONSTRAINT departamento_id_pk PRIMARY KEY (id)
);

CREATE TABLE persona (
id SERIAL,
nif VARCHAR(9),
nombre VARCHAR(50),
apellido VARCHAR(50),
fechanac DATE,
id_departamentos INT,
CONSTRAINT persona_id_pk PRIMARY KEY (id),
CONSTRAINT persona_id_departamentos_fk FOREIGN KEY (id_departamentos) REFERENCES departamento(id) ON DELETE CASCADE ON UPDATE CASCADE
);

CREATE TABLE eventos (
id SERIAL,
evento VARCHAR(50),
descripcion VARCHAR(50),
lugar VARCHAR(50),
fecha DATE,
id_persona INT,
CONSTRAINT eventos_id_pk PRIMARY KEY (id),
CONSTRAINT eventos_id_persona_fk FOREIGN KEY (id_persona) REFERENCES persona(id) ON UPDATE CASCADE
);

CREATE TABLE tipoingreso (
id SERIAL,
tipo VARCHAR(30),
nombre VARCHAR(50), 
descripcion VARCHAR(200),
CONSTRAINT tipoingreso_id_pk PRIMARY KEY (id)
);

CREATE TABLE tipogasto (
id SERIAL,
tipo VARCHAR(30),
nombre VARCHAR(50), 
descripcion VARCHAR(200),
CONSTRAINT tipogasto_id_pk PRIMARY KEY (id)
);

CREATE TABLE ingresos (
id SERIAL,
fecha DATE,
cantidad INT,
precio FLOAT,
id_eventos INT,
id_tipoingreso INT,
CONSTRAINT ingresos_id_pk PRIMARY KEY (id),
CONSTRAINT ingresos_id_eventos_fk FOREIGN KEY (id_eventos) REFERENCES eventos (id) ON UPDATE CASCADE,
CONSTRAINT ingresos_id_tipoingreso_fk FOREIGN KEY (id_tipoingreso) REFERENCES tipoingreso(id) ON UPDATE CASCADE
);

CREATE TABLE gastos (
id SERIAL,
fecha DATE,
cantidad INT,
precio FLOAT,
id_eventos INT,
id_tipogasto INT,
CONSTRAINT gastos_id_pk PRIMARY KEY (id),
CONSTRAINT gastos_id_eventos_fk FOREIGN KEY (id_eventos) REFERENCES eventos (id) ON UPDATE CASCADE,
CONSTRAINT gastos_id_tipogasto_fk FOREIGN KEY (id_tipogasto) REFERENCES tipogasto(id) ON UPDATE CASCADE
);

```
## Carga de datos de prueba

```sql

INSERT INTO departamento VALUES (nextval('id_departamento'),'Administracion'),(nextval('id_departamento'),'Seguridad'),(nextval('id_departamento'),'Tecnico'),(nextval('id_departamento'),'Voluntariado'),(nextval('id_departamento'),'AsuntosLegales');

INSERT INTO persona VALUES 
(DEFAULT,'35498756f','Juan','Perez','1990-06-25',1),
(DEFAULT,'62154825h','Pedro','Garcia','1980-06-05',2),
(DEFAULT,'96312542h','Maria','Fernandez','1960-04-02',3),
(DEFAULT,'97254568y','Isabel','Sanchez','1990-03-06',4),
(DEFAULT,'93125428t','Carlos','Garcia','1960-06-06',5),
(DEFAULT,'93120614e','Marta','Martinez','1982-05-05',1),
(DEFAULT,'94562354t','Fernando','Gonzalez','1955-04-08',1),
(DEFAULT,'78912546t','Antonio','Alvarez','1986-08-02',2),
(DEFAULT,'91564328y','Lucia','Blanco','1972-06-02',3),
(DEFAULT,'12003520u','Oscar','Puente','1992-04-12',4);

INSERT INTO eventos VALUES 
(DEFAULT,'Festival1','Festival de musica Country','Madrid','2021-06-20',1),
(DEFAULT,'Festival2','Festival de musica Rock','Madrid','2021-07-20',6),
(DEFAULT,'Festival3','Festival de musica Pop','Madrid','2021-05-10',7),
(DEFAULT,'Festival4','Festival de musica Jazz','Madrid','2021-09-15',1);

INSERT INTO tipoingreso VALUES 
(DEFAULT,'Entradas','EntradaVip','Entrada con todos los accesos'),
(DEFAULT,'Entradas','EntradaPista','Entrada con acceso a pista'),
(DEFAULT,'Entradas','EntradaGrada','Entrada con acceso a grada con asiento'),
(DEFAULT,'Patrocinadores','Patrocinador','Ingresos de patrocinadores'),
(DEFAULT,'Retransmision','Television','Retransmision de concierto por television'),
(DEFAULT,'Retransmision','Streaming','Retransmision de concierto por internet'),
(DEFAULT,'Retransmision','Radio','Retransmision de concierto por radio'),
(DEFAULT,'Grabaciones','Grabacion','Grabacion de concierto');

INSERT INTO tipogasto VALUES 
(DEFAULT,'Personal','HorasTecnicoSonido','Coste por hora de tecnico de sonido'),
(DEFAULT,'Personal','HorasTecnicoIluminacion','Coste por hora de tecnico de Iluminacion'),
(DEFAULT,'Marketing','Publicidad','Gastos en publicidad'),
(DEFAULT,'Marketing','Relaciones Publicas','Gastos en personal de relaciones publicas'),
(DEFAULT,'Asuntos Legales','Asesoria','Gastos en asesoria'),
(DEFAULT,'Montaje','Escenarios','Gastos en empresa de montaje de escenarios'),
(DEFAULT,'Material','Sonido','Alquiler de equipos de sonido');

INSERT INTO ingresos VALUES 
(DEFAULT,'2021-05-20',10,120,1,1),
(DEFAULT,'2021-05-30',20,150,1,1),
(DEFAULT,'2021-04-12',100,60,1,2),
(DEFAULT,'2021-04-20',60,85,1,2),
(DEFAULT,'2021-05-10',50,40,1,3),
(DEFAULT,'2021-05-10',35,65,1,3),
(DEFAULT,'2021-05-02',1,2000,1,4),
(DEFAULT,'2021-06-20',1,20000,1,5),
(DEFAULT,'2021-06-20',1,6000,1,6),
(DEFAULT,'2021-06-20',1,500,1,7),
(DEFAULT,'2021-06-20',30,100,1,8),
(DEFAULT,'2021-06-20',15,110,2,1),
(DEFAULT,'2021-07-10',25,120,2,1),
(DEFAULT,'2021-05-12',140,50,2,2),
(DEFAULT,'2021-05-15',86,78,2,2),
(DEFAULT,'2021-04-10',45,35,2,3),
(DEFAULT,'2021-05-20',65,49,2,3),
(DEFAULT,'2021-06-02',1,2500,2,4),
(DEFAULT,'2021-07-20',1,30000,2,5),
(DEFAULT,'2021-07-20',1,7200,2,6),
(DEFAULT,'2021-07-20',1,600,2,7),
(DEFAULT,'2021-07-20',30,135,2,8),
(DEFAULT,'2021-03-20',25,135,3,1),
(DEFAULT,'2021-04-20',56,145,3,1),
(DEFAULT,'2021-02-12',168,55,3,2),
(DEFAULT,'2021-02-28',115,75,3,2),
(DEFAULT,'2021-03-10',75,35,3,3),
(DEFAULT,'2021-04-18',120,45,3,3),
(DEFAULT,'2021-04-10',1,2200,3,4),
(DEFAULT,'2021-05-10',1,15000,3,5),
(DEFAULT,'2021-05-10',1,2500,3,6),
(DEFAULT,'2021-05-10',1,750,3,7),
(DEFAULT,'2021-05-10',43,68,3,8),
(DEFAULT,'2021-05-20',67,85,4,1),
(DEFAULT,'2021-06-15',120,95,4,1),
(DEFAULT,'2021-07-12',200,30,4,2),
(DEFAULT,'2021-08-15',123,45,4,2),
(DEFAULT,'2021-08-15',120,25,4,3),
(DEFAULT,'2021-09-05',100,35,4,3),
(DEFAULT,'2021-08-15',1,3000,4,4),
(DEFAULT,'2021-09-15',1,15000,4,5),
(DEFAULT,'2021-09-15',1,4500,4,6),
(DEFAULT,'2021-09-15',1,1000,4,7),
(DEFAULT,'2021-09-15',15,75,4,8);

INSERT INTO gastos VALUES 
(DEFAULT,'2021-06-20',8,95,1,1),
(DEFAULT,'2021-06-19',8,85,1,1),
(DEFAULT,'2021-06-20',8,100,1,2),
(DEFAULT,'2021-06-19',8,100,1,2),
(DEFAULT,'2021-05-15',1,300,1,3),
(DEFAULT,'2021-06-10',1,250,1,3),
(DEFAULT,'2021-05-16',2,150,1,4),
(DEFAULT,'2021-05-25',3,160,1,4),
(DEFAULT,'2021-06-10',12,130,1,5),
(DEFAULT,'2021-06-18',10,100,1,5),
(DEFAULT,'2021-06-10',15,150,1,6),
(DEFAULT,'2021-06-19',12,120,1,6),
(DEFAULT,'2021-06-15',1,5000,1,7),
(DEFAULT,'2021-06-19',1,1500,1,7),
(DEFAULT,'2021-07-20',8,110,2,1),
(DEFAULT,'2021-07-20',7,85,2,1),
(DEFAULT,'2021-07-15',8,95,2,2),
(DEFAULT,'2021-07-20',9,75,2,2),
(DEFAULT,'2021-07-18',1,300,2,3),
(DEFAULT,'2021-07-15',1,500,2,4),
(DEFAULT,'2021-07-10',1,300,2,5),
(DEFAULT,'2021-07-19',16,120,2,6),
(DEFAULT,'2021-07-20',16,150,2,6),
(DEFAULT,'2021-07-19',1,3500,2,7),
(DEFAULT,'2021-07-20',9,110,3,1),
(DEFAULT,'2021-07-20',12,95,3,2),
(DEFAULT,'2021-07-18',1,350,3,3),
(DEFAULT,'2021-07-15',1,600,3,4),
(DEFAULT,'2021-07-10',1,250,3,5),
(DEFAULT,'2021-07-19',20,110,3,6),
(DEFAULT,'2021-07-20',25,75,3,6),
(DEFAULT,'2021-07-19',1,4500,3,7),
(DEFAULT,'2021-06-20',12,75,4,1),
(DEFAULT,'2021-06-19',14,90,4,1),
(DEFAULT,'2021-06-20',12,110,4,2),
(DEFAULT,'2021-06-19',14,75,4,2),
(DEFAULT,'2021-05-15',1,300,4,3),
(DEFAULT,'2021-05-16',2,150,4,4),
(DEFAULT,'2021-06-10',14,115,4,5),
(DEFAULT,'2021-06-19',12,125,4,6),
(DEFAULT,'2021-06-15',1,6000,4,7);

```
# Consultas de la base de datos

## Consultas más frecuentes
```sql
--Lista de departamentos

SELECT nombre FROM departamento;

--Lista de empleados ordenado por departamentos

SELECT p.nif as "NIF", p.nombre as "Nombre", p.apellido as "Apellidos", p.fechanac as "Fecha Nacimiento", d.nombre as "Departamento" 
from persona p 
left join departamento d on p.id_departamentos=d.id 
order by d.nombre;

--Lista de Eventos

SELECT e.evento, e.descripcion, e.lugar, e.fecha, p.nombre as "Responsable" FROM eventos e
left join persona p on e.id_persona = p.id
order by e.evento;

--Lista tipos de gastos

SELECT tipo, nombre, descripcion FROM tipogasto ORDER BY tipo;

--Lista tipos de ingreso

SELECT tipo, nombre, descripcion FROM tipoingreso ORDER BY tipo;

--Listado ingresos ordenado por tipo

SELECT e.evento as "Evento", e.descripcion as "Descripcion", ti.tipo as "Tipo Ingreso", ti.nombre as "Descripción Corta", i.fecha as "Fecha Ingreso", i.cantidad as "Cantidad", i.precio as "Precio", i.cantidad*i.precio as "Importe"  
FROM ingresos i
left join eventos e on i.id_eventos = e.id
left join tipoingreso ti on i.id_tipoingreso = ti.id 
order by ti.tipo;

--Listado ingresos ordenado por evento

SELECT e.evento as "Evento", e.descripcion as "Descripcion", ti.tipo as "Tipo Ingreso", ti.nombre as "Descripción Corta", i.fecha as "Fecha Ingreso", i.cantidad as "Cantidad", i.precio as "Precio", i.cantidad*i.precio as "Importe"  
FROM ingresos i
left join eventos e on i.id_eventos = e.id
left join tipoingreso ti on i.id_tipoingreso = ti.id 
order by e.evento;

--Listado ingresos de un evento ordenado por tipo 

SELECT e.evento as "Evento", e.descripcion as "Descripcion", ti.tipo as "Tipo Ingreso", ti.nombre as "Descripción Corta", i.fecha as "Fecha Ingreso", i.cantidad as "Cantidad", i.precio as "Precio", i.cantidad*i.precio as "Importe"  
FROM ingresos i
left join eventos e on i.id_eventos = e.id
left join tipoingreso ti on i.id_tipoingreso = ti.id 
where e.evento = 'Festival2'
order by ti.tipo;

--Listado de gastos ordenado por tipo

SELECT e.evento as "Evento", e.descripcion as "Descripcion",  tg.tipo as "Tipo Gasto", tg.nombre as "Descripción Corta", g.fecha as "Fecha Gasto", g.cantidad as "Cantidad", g.precio as "Precio", g.cantidad*g.precio as "Importe"
FROM gastos g
left join eventos e on g.id_eventos = e.id
left join tipogasto tg on g.id_tipogasto = tg.id 
order by tg.tipo;

--Listado de gastos ordenado por evento

SELECT e.evento as "Evento", e.descripcion as "Descripcion",  tg.tipo as "Tipo Gasto", tg.nombre as "Descripción Corta", g.fecha as "Fecha Gasto", g.cantidad as "Cantidad", g.precio as "Precio", g.cantidad*g.precio as "Importe"
FROM gastos g
left join eventos e on g.id_eventos = e.id
left join tipogasto tg on g.id_tipogasto = tg.id 
order by e.evento;

--Listado de gastos de un evento ordenado por tipo 

SELECT e.evento as "Evento", e.descripcion as "Descripcion",  tg.tipo as "Tipo Gasto", tg.nombre as "Descripción Corta", g.fecha as "Fecha Gasto", g.cantidad as "Cantidad", g.precio as "Precio", g.cantidad*g.precio as "Importe"
FROM gastos g
left join eventos e on g.id_eventos = e.id
left join tipogasto tg on g.id_tipogasto = tg.id 
where e.evento = 'Festival3'
order by tg.tipo;

```

## Consultas sencillas

```sql

--Lista Personas

SELECT * FROM persona;

--Lista Departamentos

SELECT * FROM departamento;

--Lista Eventos

SELECT * FROM eventos;

--Lista Ingresos

SELECT * FROM ingresos;

--Lista Tipos de Ingresos

SELECT * FROM tipoingreso;

--Lista Gastos

SELECT * FROM gastos;

--Lista Tipos de Gastos

SELECT * FROM tipogasto;

```
## Consultas de agregación y resumen

```sql

--Total Ingresos agrupado por tipo y nombre de un Festival( Festival1 ) y que el importe sea mayor que 0 (Para que salgan solo los grupos que tengan ingresos)

SELECT COUNT(i.id) as "Registros", ti.tipo as "Tipo Ingreso", ti.nombre as "Descripción Corta", SUM(i.cantidad) as "Unidades", SUM(i.cantidad*i.precio) as "Importe"
FROM ingresos i
left join eventos e on i.id_eventos = e.id
left join tipoingreso ti on i.id_tipoingreso = ti.id 
where e.evento ='Festival1'
group by ti.tipo, ti.nombre
having SUM(i.cantidad*i.precio)>0
order by ti.tipo; 

--Total gastos agrupado por tipo y nombre de un Festival( Festival1 ) y que el importe sea mayor que 0 (Para que salgan solo los grupos que tengan gastos)

SELECT COUNT(g.id) as "Registros", tg.tipo as "Tipo", tg.nombre as "Descripción Corta",SUM(g.cantidad) as "Unidades", SUM(g.cantidad*g.precio) as "Importe"
FROM gastos g
left join eventos e on g.id_eventos = e.id
left join tipogasto tg on g.id_tipogasto = tg.id 
where e.evento ='Festival1'
group by tg.tipo, tg.nombre
having SUM(g.cantidad*g.precio)>0
order by tg.tipo; 

```

## Consultas con subconsultas

```sql

--Consulta de ingresos de un evento ordenado por tipo

select e.evento as "Evento", ti.nombre as "Tipo Ingreso", i.fecha as "Fecha", i.cantidad as "Cantidad", i.precio as "Precio", i.cantidad*i.precio as "Importe" 
from ingresos i
left join eventos e on i.id_eventos = e.id
left join tipoingreso ti on i.id_tipoingreso=ti.id 
where id_eventos in ( select id from eventos where evento = 'Festival2' )
order by ti.tipo

--Consulta de gastos de dos eventos (Festival1 y festival3) ordenado por tipo

select e.evento as "Evento", tg.nombre as "Tipo Ingreso", g.fecha as "Fecha", g.cantidad as "Cantidad", g.precio as "Precio", g.cantidad*g.precio as "Importe" 
from gastos g
left join eventos e on g.id_eventos = e.id
left join tipogasto tg on g.id_tipogasto = tg.id 
where id_eventos in ( select id from eventos where evento = 'Festival1' OR evento = 'Festival3' )
order by tg.tipo

```
# Vistas, secuencias e índices

```sql

-- Lista de departamentos con su número de empleados

CREATE OR REPLACE VIEW numemple_x_depto AS
SELECT d.nombre, COUNT(e.nombre) "Nº Empleados"
FROM persona e
RIGHT JOIN departamento d ON (e.id_departamentos = d.id)
GROUP BY d.nombre;

-- Gastos de personal

CREATE OR REPLACE VIEW gastos_personal as 
select e.evento as "Evento", tg.nombre as "Tipo Gasto", g.fecha as "Fecha", g.cantidad as "Cantidad", g.precio as "Precio", g.cantidad*g.precio as "Importe"
from gastos g
left join eventos e on g.id_eventos = e.id
left join tipogasto tg on g.id_tipogasto=tg.id 
where g.id_tipogasto in (select id from tipogasto where tipo='Personal' )
order by e.evento;

-- Ingresos por entradas

CREATE OR REPLACE VIEW ingresos_entradas as
select e.evento as "Evento",ti.tipo as "Tipo", ti.nombre as "Descripción", i.fecha as "Fecha", i.cantidad as "Cantidad", i.precio as "Precio", i.cantidad*i.precio as "Importe"
from ingresos i
left join eventos e on i.id_eventos = e.id
left join tipoingreso ti on i.id_tipoingreso = ti.id 
where i.id_tipoingreso in (select id from tipoingreso where tipo='Entradas' )
order by e.evento

-- Secuencia id

CREATE SEQUENCE id_departamento
AS INT
INCREMENT 1
START 1;

-- Indices Sintaxis: CREATE [ UNIQUE ] INDEX nombre_indice ON nombre_tabla(columna, [...]) [ USING metodo ];


--Indice tabla ingresos (id_eventos, id_tipoingrsos)
CREATE INDEX ingresos_id_eventos_ix ON ingresos(id_eventos);
CREATE INDEX ingresos_id_tipoingreso_ix ON ingresos(id_tipoingreso);

--Indice tabla gastos (id_eventos, id_tipogasto)
CREATE INDEX gastos_id_eventos_ix ON gastos(id_eventos);
CREATE INDEX gastos_id_tipogasto_ix ON gastos(id_tipogasto);

--Indice tabla persona (nif) único
CREATE UNIQUE INDEX persona_nif_ix ON persona(nif);
--Indice tabla persona (id_departamentos)
CREATE INDEX persona_id_departamentos_ix ON persona(id_departamentos);

--Indice tabla eventos (id_persona)
CREATE INDEX eventos_id_persona_ix ON eventos(id_persona);

```

# Scripts en PL/pgSQL

## PROCEDIMIENTOS
___

```sql

-- Total ingresos por tipo

CREATE or REPLACE PROCEDURE ingresos (p_tipo varchar(30) )
language plpgsql
AS
$$
declare
	v_total_ingresos float;
	v_total_Cantidad integer := 0;
begin
	select SUM(cantidad), SUM(cantidad*precio) into v_total_Cantidad, v_total_ingresos
	from ingresos 
	where id_tipoingreso in ( select id from tipoingreso where tipo = p_tipo );
	raise notice 'Las Unidades por Ingresos de % es de: % que supone un Importe Total de %', p_tipo, v_total_Cantidad, v_total_ingresos;
end;
$$

--Total gastos por tipo

CREATE or REPLACE PROCEDURE gastos (p_tipo varchar(30) )
language plpgsql
AS
$$
declare
	v_total_gastos float;
	v_total_Cantidad integer := 0;
begin
	select SUM(cantidad), SUM(cantidad*precio) into v_total_Cantidad, v_total_gastos
	from gastos 
	where id_tipogasto in ( select id from tipogasto where tipo = p_tipo );
	raise notice 'Las Unidades por Gastos de % es de: % que supone un Importe Total de %', p_tipo, v_total_Cantidad, v_total_gastos;
end;
$$


--Total ingresos por evento

CREATE or REPLACE PROCEDURE ingresos_evento (p_evento varchar(30) )
language plpgsql
AS
$$
declare
	v_total_ingresos float;
begin
	select SUM(cantidad*precio) into v_total_ingresos
	from ingresos 
	where id_eventos in ( select id from eventos where evento = p_evento );
	raise notice 'El Total de Ingresos del Evento: [ % ] es de: % ', p_evento, v_total_ingresos;
end;
$$

--Total gastos por evento

CREATE or REPLACE PROCEDURE gastos_evento (p_evento varchar(30) )
language plpgsql
AS
$$
declare
	v_total_gastos float;
begin
	select SUM(cantidad*precio) into v_total_gastos
	from gastos 
	where id_eventos in ( select id from eventos where evento = p_evento );
	raise notice 'El Total de Gastos del Evento: [ % ] es de: %', p_evento, v_total_gastos;
end;
$$

--Ingresos y gastos totales por Evento

CREATE or REPLACE PROCEDURE ingresos_gastos( p_evento varchar(30) ) 
language plpgsql
AS
$$
declare
	v_total_ingresos float;
	v_total_gastos float;
begin
	select sum(cantidad*precio) into v_total_ingresos from ingresos
	where id_eventos in ( select id from eventos where evento = p_evento );
	select sum(cantidad*precio) into v_total_gastos from gastos
	where id_eventos in ( select id from eventos where evento = p_evento );
	raise notice 'Los Ingresos del Evento: [ % ] ascienden a: %', p_evento, v_total_ingresos;
	raise notice 'Los Gastos   del Evento  [ % ] ascienden a: %', p_evento, v_total_gastos;
	raise notice 'El Beneficio/Perdida asciende a: %', (v_total_ingresos-v_total_gastos);
end;
$$

--Ingresos y gastos de un evento agrupados por tipo

CREATE or REPLACE PROCEDURE igeventoportipo( p_evento varchar(30) ) 
language plpgsql
AS
$$
declare
	v_ingresos record;
	v_gastos record;
	v_total_ingresos integer := 0;
	v_total_gastos integer := 0;
begin
		raise notice 'Ingresos del Evento: % agrupados por Tipo.', p_evento;raise notice '----------------------------------------------------------';	
	for v_ingresos in select ti.tipo as "tipoingreso", sum(cantidad*precio) as "totalingreso" from ingresos i
							left join tipoingreso ti on i.id_tipoingreso = ti.id
							where i.id_eventos in ( select id from eventos where evento = p_evento )
							group by ti.tipo
	loop
		raise notice 'Tipo Ingreso: %, Importe: % EUR', v_ingresos.tipoingreso, v_ingresos.totalingreso;	
		v_total_ingresos := v_total_ingresos + v_ingresos.totalingreso;
	end loop;
		raise notice '----------------------------------------------------------';	 
		raise notice 'Total Ingresos...: %', v_total_ingresos;	
		raise notice '----------------------------------------------------------';	 
		raise notice 'Gastos del Evento: % agrupados por Tipo.', p_evento;raise notice '----------------------------------------------------------';	
	for v_gastos in select tg.tipo as "tipogasto", sum(cantidad*precio) as "totalgasto" from gastos g
						  left join tipogasto tg on g.id_tipogasto = tg.id
						  where g.id_eventos in ( select id from eventos where evento = p_evento )
						  group by tg.tipo
	loop
		raise notice 'Tipo Gastos: %, Importe: % EUR', v_gastos.tipogasto,v_gastos.totalgasto;	
		v_total_gastos := v_total_gastos + v_gastos.totalgasto;
	end loop;						
	    raise notice '----------------------------------------------------------';	
		raise notice 'Total Gastos.....: %', v_total_gastos;	
		raise notice '----------------------------------------------------------';	
		raise notice 'Beneficio/Perdida: %', v_total_ingresos-v_total_gastos;	
end;
$$

-- Cambiar el id de departamento a un empleado

CREATE or REPLACE PROCEDURE cambiar_departamento( p_persona_id int, p_depto_id int )
language plpgsql
as
$$
begin
	update persona
	set id_departamentos = p_depto_id
	where id = p_persona_id;
	commit;
end;
$$

```

## FUNCIONES
___

```sql

-- Obtener número de empleados de un Departamento

CREATE OR REPLACE FUNCTION num_empleados_depto( p_depto_id int )
returns int
language plpgsql
as
$$
declare
	v_num_personas integer;
begin
	select count(*) into v_num_personas
	from persona
	where id_departamentos = p_depto_id;
	return v_num_personas;
end;
$$

-- Obtener número de eventos gestionados por un empleados

CREATE OR REPLACE FUNCTION num_eventos_persona( p_persona_id int )
returns int
language plpgsql
as
$$
declare
	v_num_eventos integer;
begin
	select count(*) into v_num_eventos
	from eventos
	where id_persona = p_persona_id;
	return v_num_eventos;
end;
$$

-- Obtener número de Entradas vendidas en un evento

CREATE OR REPLACE FUNCTION num_entradas_evento( p_evento_id int  )
returns int
language plpgsql
as
$$
declare
	v_num_entradas integer;
begin
	select sum(cantidad) into v_num_entradas
	from ingresos
	where id_tipoingreso in ( select id from tipoingreso where tipo = 'Entradas' ) and id_eventos = p_evento_id;
	return v_num_entradas;
end;
$$

-- Obtener Total Ingresos de un evento por su nombre

CREATE OR REPLACE FUNCTION funingresos_evento( p_evento_nombre varchar  )
returns float
language plpgsql
as
$$
declare
	v_total_ingresos float;
begin
	select sum(cantidad*precio) into v_total_ingresos
	from ingresos
	where id_eventos in ( select id from eventos where evento = p_evento_nombre );
	return v_total_ingresos;
end;
$$

-- Obtener Total Gastos de un evento por su nombre

CREATE OR REPLACE FUNCTION fungastos_evento( p_evento_nombre varchar  )
returns float
language plpgsql
as
$$
declare
	v_total_gastos float;
begin
	select sum(cantidad*precio) into v_total_gastos
	from gastos
	where id_eventos in ( select id from eventos where evento = p_evento_nombre );
	return v_total_gastos;
end;
$$
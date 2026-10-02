README — Sistema de Gestión de Reservas y Tarificación "FlexSpace"

Descripción del Proyecto

FlexSpace es un sistema de consola desarrollado en C# (.NET) que permite a la empresa de co-working FlexSpace gestionar la reserva de puestos de trabajo, validar la disponibilidad en tiempo real y calcular el cobro final aplicando reglas dinámicas según el tipo de cliente, horario y sanciones previas.

Arquitectura (3 Capas)

El proyecto está organizado en una solución con tres proyectos independientes, cada uno con responsabilidades estrictas:

- Capa de Presentación (FlexSpace.UI — Consola): Menús interactivos, captura de datos, visualización de resultados. Queda totalmente prohibido llamar a la capa de datos, usar SQL o realizar cálculos de tarifas o validaciones de fechas.
- Capa de Negocio (FlexSpace.BLL — Biblioteca de Clases): Contiene la lógica de dominio, validaciones de disponibilidad, algoritmos de cálculo de precio y orquestación de reglas. Aplica principios de POO. No debe contener Console.WriteLine ni Console.ReadLine.

- Capa de Datos (FlexSpace.DAL — Biblioteca de Clases): Encargada exclusivamente del acceso a la base de datos (SQL Server / SQLite / PostgreSQL usando ADO.NET o Dapper). Ejecuta consultas, procedimientos o comandos SQL y retorna objetos de entidad / DTOs hacia la BLL. No conoce la existencia de la UI ni realiza validaciones de negocio.

Modelo de Dominio

Las entidades persistidas en base de datos son:

- Cliente: Id, Nombre, Email, TipoCliente (Estandar, VIP), SancionesActivas (entero).
- Puesto: Id, Codigo, TipoPuesto (EscritorioIndividual, SalaReuniones, CabinaPrivada), TarifaBasePorHora (decimal.

- Reserva: Id, ClienteId, PuestoId, FechaInicio (DateTime), FechaFin (DateTime), Estado (Confirmada, Cancelada, Finalizada), CostoTotal (decimal.

Reglas de Negocio Implementadas (BLL)

A. Validación de Disponibilidad

No se puede registrar una nueva reserva si el puesto seleccionado ya tiene otra reserva en estado Confirmada que se solape en el rango horario solicitado.

B. Cálculo Dinámico de la Tarifa (en orden)

El cálculo del monto final no es una simple multiplicación. La capa de negocio aplica las siguientes reglas en orden:

1. Subtotal Base: Horas reservadas × TarifaBasePorHora del puesto.
2. Recargo por Fin de Semana: Si la reserva incluye días sábado o domingo, se aplica un 15% de recargo sobre el subtotal.
3. Descuento por Volumen: Si la reserva dura 5 horas o más, se aplica un 10% de descuento sobre el total acumulado.
4. Beneficio VIP: Si el cliente es de tipo VIP, obtiene un 5% de descuento extrafinal.
5. Penalización por Sanciones: Si el cliente tiene SancionesActivas mayor a 0, pierde todos los descuentos previos y se le recarga un 20% adicional sobre la tarifa base.

C. Bloqueo de Cliente

Si un cliente acumula 3 o más SancionesActivas, la BLL debe rechazar cualquier intento de reserva arrojando la excepción ClienteSancionadoException.

Menú de Consola (UI)

El programa principal presenta las siguientes opciones:

1. Registrar Nueva Reserva: Solicita ClienteId, PuestoId, Fecha/Hora Inicio y Fecha/Hora Fin. Muestra el resumen del cálculo del precio antes de confirmar la persistencia.
2. Cancelar Reserva: Permite cancelar una reserva. Si se cancela con menos de 2 horas de anticipación a la fecha de inicio, la BLL debe incrementar en +1 las SancionesActivas del cliente en la BD.
3. Consultar Reservas Activas por Puesto: Muestra el listado de reservas futuras filtradas por el código del puesto.

4. Listar Clientes Sancionados: Muestra clientes que tienen penalizaciones vigentes.


Cómo Compilar y Ejecutar

1. Restaurar dependencias: dotnet restore
2. Compilar la solución: dotnet build
3. Ejecutar la consola: dotnet run --project FlexSpace.UI



Estado de la Entrega Parcial

Implementado:

- Estructura de solución en 3 capas con referencias estrictas
- Entidades de dominio (Cliente, Puesto, Reserva)
- Validación de disponibilidad (sin solapamiento)
- Cálculo dinámico de tarifa con las 5 reglas
- Bloqueo de cliente sancionado( excepción)
- Menú de consola con las 4 opciones
- Acceso a datos en DAL

Pendiente para la Entrega Final:

- ?


- Nombre: Mia Acosta
- Fecha de entrega parcial: 01/10/2026
- Materia / Profesor: POO / Obregón

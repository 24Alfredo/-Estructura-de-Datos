    --DESARROLLO DEL ENTREGABLE 


    -- Creando la tabla Departamento

CREATE TABLE Departamento (
 Dept_No INT PRIMARY KEY,
 DNombre VARCHAR(255),
 Loc VARCHAR(255)
);

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
    -- Creando la tabla Empleado

CREATE TABLE Empleado (
 Emp_No INT PRIMARY KEY,
 Apellido VARCHAR(255),
 Oficio VARCHAR(255),
 Dir VARCHAR(255),
 Fecha_Alt DATE,
 Salario DECIMAL(10,2),
 Comision DECIMAL(10,2),
 Dept_No INT,
 FOREIGN KEY (Dept_No) REFERENCES Departamento(Dept_No)
);

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
    -- Creando la tabla Enfermo

CREATE TABLE Enfermo (
 Id INT PRIMARY KEY,
 Apellido VARCHAR(255),
 Direccion VARCHAR(255),
 Fecha_Nac DATE,
 S VARCHAR(255),
 NSS VARCHAR(255)
);

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------

    -- Insertando datos en la tabla Departamento

INSERT INTO  clinicadb.Departamento (Dept_No, DNombre, Loc) VALUES
 (10, 'Contabilidad', 'Nueva York'),
 (20, 'Investigación', 'Dallas'),
 (30, 'Ventas', 'Chicago'),
 (40, 'Recursos Humanos', 'Boston');

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
    -- Insertando datos en la tabla Empleado

INSERT INTO clinicadb.Empleado (Emp_No, Apellido, Oficio, Dir, Fecha_Alt, Salario, Comision, Dept_No) VALUES
 (7839, 'King', 'Presidente', 'New York', '1981-11-17', 5000.00, NULL, 10),
 (7698, 'Blake', 'Gerente', 'New York', '1981-05-01', 2850.00, NULL, 30),
 (7782, 'Clark', 'Gerente', 'New York', '1981-06-09', 2450.00, NULL, 10),
 (7566, 'Jones', 'Gerente', 'New York', '1981-04-02', 2975.00, NULL, 20),
 (7654, 'Martin', 'Vendedor', 'New York', '1981-09-28', 1250.00, 1400.00, 30),
 (7499, 'Allen', 'Vendedor', 'New York', '1981-02-20', 1600.00, 300.00, 30),
 (7844, 'Turner', 'Vendedor', 'New York', '1981-09-08', 1500.00, 0.00, 30),
 (7900, 'James', 'Clero', 'New York', '1981-12-03', 950.00, NULL, 30),
 (7521, 'Ward', 'Analista', 'New York', '1981-02-22', 1250.00, 500.00, 30),
 (7902, 'Ford', 'Analista', 'New York', '1981-12-03', 3000.00, NULL, 20),
 (7369, 'Smith', 'Clero', 'New York', '1980-12-17', 800.00, NULL, 20),
 (7788, 'Scott', 'Analista', 'New York', '1987-07-13', 3000.00, NULL, 20);

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
    -- Insertando datos en la tabla Enfermo


INSERT INTO clinicadb.Enfermo (Inscripcion, Apellido, Direccion, Fecha_Nac, S, NSS) VALUES
(1, 'Lopez', 'Calle Principal 123', '1990-01-15', 'Masculino', '123456789'),
(4, 'Garcia', 'Avenida Siempreviva 456', '1985-07-20', 'Femenino', '987654321'),
(5, 'Rodriguez', 'Calle Falsa 789', '1978-10-05', 'Masculino', '123456789');


--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------


  ----------------------------------------------- CONSULTAS--------------------------------------------------------------

    --1-- Crear el procedimiento almacenado para insertar un nuevo departamento

DELIMITER //

CREATE PROCEDURE InsertarDepartamento (
    IN p_Dept_No INT,
    IN p_DNombre VARCHAR(255),
    IN p_Loc VARCHAR(255)
)
BEGIN
    INSERT INTO Departamento (Dept_No, DNombre, Loc) VALUES (p_Dept_No, p_DNombre, p_Loc);
END //

DELIMITER ;

    -- Llamar al procedimiento para insertar un nuevo departamento

CALL InsertarDepartamento(50, 'Marketing', 'San Francisco');

    -- Obtener empleados que se dieron de alta antes del año 2018 y que pertenecen al departamento 

SELECT * FROM Empleado WHERE Fecha_Alt < '2018-01-01' AND Dept_No = 30;

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------


    --2-- Crear el procedimiento almacenado para recuperar el promedio de edad por departamento

DELIMITER //

CREATE PROCEDURE PromedioEdadPorDepartamento (
    IN p_Dept_No INT
)
BEGIN
    SELECT AVG(TIMESTAMPDIFF(YEAR, Fecha_Nac, CURDATE())) AS PromedioEdad
    FROM Enfermo
    WHERE NSS IN (SELECT NSS FROM Empleado WHERE Dept_No = p_Dept_No);
END //

DELIMITER ;

    -- Llamar al procedimiento para obtener el promedio de edad de las personas en el departamento con Dept_No 20

CALL PromedioEdadPorDepartamento(20);

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------

    --3-- Crear el procedimiento almacenado para devolver el apellido, oficio y salario del empleado
DELIMITER //

CREATE PROCEDURE ObtenerInformacionEmpleado (
    IN p_Emp_No INT
)
BEGIN
    SELECT Apellido, Oficio, Salario
    FROM Empleado
    WHERE Emp_No = p_Emp_No;
END //

DELIMITER ;

    -- Llamar al procedimiento para obtener la información del empleado con Emp_No 7839

CALL ObtenerInformacionEmpleado(7839);

-------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------

    --4-- Crear el procedimiento almacenado para eliminar un empleado
DELIMITER //

CREATE PROCEDURE EliminarEmpleado (
    IN p_Apellido VARCHAR(255)
)
BEGIN
    DELETE FROM Empleado WHERE Apellido = p_Apellido;
END //

DELIMITER ;

    -- Llamar al procedimiento para eliminar al empleado con apellido 'Scott'
CALL EliminarEmpleado('Scott');

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
-----------------------------------------------------RESTRICCIONES-----------------------------------------------------

    -- 1  --Restricción para evitar que el nombre del departamento esté vacío:

ALTER TABLE Departamento
ADD CONSTRAINT CK_DNombre CHECK (DNombre <> '');
--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
    -- 2  --Restricción para evitar que el salario de un empleado sea negativo:

ALTER TABLE Empleado
ADD CONSTRAINT CK_Salario CHECK (Salario >= 0);

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
    -- 3  --Restricción para evitar que la fecha de alta de un empleado sea posterior a la fecha actual:

ALTER TABLE Empleado
ADD CONSTRAINT CK_Fecha_Alt CHECK (Fecha_Alt <= CURDATE());

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
    -- 4  --Restricción para asegurar que la comisión de un empleado no sea negativa:

ALTER TABLE Empleado
ADD CONSTRAINT CK_Comision CHECK (Comision >= 0);

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
    -- 5  --Restricción para evitar que el NSS en la tabla Enfermo sea único:

ALTER TABLE Enfermo
ADD CONSTRAINT UC_NSS UNIQUE (NSS);
--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------

---------------------------------------------------OPTIMIZACION DE CONSULTAS---------------------------------------------------

    --1--Crear índices para mejorar la velocidad de las consultas:

    -- Crear índice en el campo Dept_No de la tabla Empleado

CREATE INDEX idx_dept_no ON Empleado (Dept_No);

    -- Crear índice en el campo NSS de la tabla Enfermo

CREATE INDEX idx_nss ON Enfermo (NSS);

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
    --2--Asegurar que las claves foráneas están correctamente indexadas:

    -- Crear índice en la clave foránea Dept_No en la tabla Empleado

CREATE INDEX idx_fk_dept_no ON Empleado (Dept_No);

--------------------------------------------------------------------------------------------------------------------------------
--------------------------------------------------------------------------------------------------------------------------------
# BD07
Crea el tipo de objetos "MiembroEscolar" con los siguientes atributos:  
```
codigo INTEGER,
dni VARCHAR2(10),
nombre VARCHAR2(30),
apellidos VARCHAR2(30),
sexo VARCHAR2(1),
fecha_nac DATE
```
```SQL
create or replace TYPE MiembroEscolar AS OBJECT(
    codigo INTEGER,
    dni VARCHAR2(10),
    nombre VARCHAR2(30),
    apellidos VARCHAR2(30),
    sexo VARCHAR2(1),
    fecha_nac DATE
) NOT FINAL;
```
Crea, como tipo heredado de "MiembroEscolar", el tipo de objeto "Profesor" con los siguientes atributos:
```
especialidad VARCHAR2(20),
antiguedad INTEGER 
```
```SQL
create or replace TYPE Profesor UNDER MiembroEscolar(
    especialidad VARCHAR2(20),
    antiguedad INTEGER, 
    CONSTRUCTOR FUNCTION Profesor(codigo INTEGER, nombre VARCHAR2, apellido1 VARCHAR2, apellido2 VARCHAR2, especialidad VARCHAR2)
    RETURN SELF AS RESULT,
    MEMBER FUNCTION getNombreCompleto RETURN VARCHAR2
);
```
Crea el tipo de objeto "Cursos" con los siguientes atributos:
```SQL
codigo INTEGER,
nombre VARCHAR2(20),
refProfe REF Profesor,
max_Alumn INTEGER,
fecha_Inic DATE,
fecha_Fin DATE,
num_Horas INTEGER
```
```SQL
create or replace TYPE Cursos AS OBJECT (
    codigo INTEGER,
    nombre VARCHAR2(20),
    refProfe REF Profesor,
    max_Alumn INTEGER,
    fecha_Inic DATE,
    fecha_Fin DATE,
    num_Horas INTEGER
);
```
Crea, como tipo heredado de "MiembroEscolar", el tipo de objeto "Alumno" con los siguientes atributos:
```
cursoAlumno Cursos
```
```SQL
create or replace TYPE Alumno UNDER MiembroEscolar (
    cursoAlumno Cursos
);
```
Crea un método constructor para el tipo de objetos "Profesor", en el que se indiquen como parámetros el código, nombre, primer apellido, segundo apellido y especialidad. Este método debe asignar al atributo "apellidos" los datos de primer apellido y segundo apellido que se han pasado como parámetros, uniéndolos con un espacio entre ellos.
Crea un método "getNombreCompleto" para el tipo de objetos "Profesor" que permita obtener su nombre completo con el formato "apellidos nombre".
```SQL
create or replace TYPE Profesor UNDER MiembroEscolar(
    especialidad VARCHAR2(20),
    antiguedad INTEGER, 
    CONSTRUCTOR FUNCTION Profesor(codigo INTEGER, nombre VARCHAR2, apellido1 VARCHAR2, apellido2 VARCHAR2, especialidad VARCHAR2)
    RETURN SELF AS RESULT,
    MEMBER FUNCTION getNombreCompleto RETURN VARCHAR2
);
create or replace TYPE BODY Profesor AS
CONSTRUCTOR FUNCTION Profesor (codigo INTEGER, nombre VARCHAR2, apellido1 VARCHAR2, apellido2 VARCHAR2, especialidad VARCHAR2) RETURN SELF AS RESULT IS
    BEGIN
     SELF.codigo := codigo;
     SELF.nombre := nombre;
     SELF.apellidos := apellido1 || ' ' || apellido2;
     SELF.especialidad := especialidad;
    RETURN;
    end;
    MEMBER FUNCTION getNombreCompleto RETURN VARCHAR2 IS
        nombreCompleto VARCHAR2(100);
    BEGIN
        nombreCompleto := SELF.apellidos || ' ' || SELF.nombre;
        RETURN nombreCompleto;
    END;

END;
```
Crea un tabla "Profesorado" de objetos "Profesor". Inserta en dicha tabla dos objetos "Profesor". El primero de ellos con los datos: 
```
codigo: 2
dni: 51083099F
nombre: MARIA LUISA
apellidos: FABRE BERDUN
sexo: F
fecha_nac: 31/03/1975
especialidad: TECNOLOGIA
antiguedad: 4
```
El segundo objeto "Profesor" debes crearlo usando el método constructor que has realizado anteriormente. Debes usar los siguientes datos:
```
codigo: 3
nombre: JAVIER
apellidos: JIMENEZ HERNANDO
especialidad: LENGUA
```
```SQL
CREATE TABLE Profesorado OF Profesor (codigo PRIMARY KEY);
 
INSERT INTO Profesorado VALUES(2, '51083099F', 'MARIA LUISA', 'FABRE BERDUN', 'F', TO_DATE('31/03/1975', 'DD/MM/YYYY'), 'TECNOLOGIA', 4);
 
INSERT INTO Profesorado Values(Profesor(3, 'JAVIER', 'JIMENEZ', 'HERNANDO', 'LENGUA'));
```
Crea una colección VARRAY llamada "ListaCursos" en la que se puedan almacenar hasta 10 objetos "Cursos". Guarda en una instancia "listaCursos1" de dicha lista, los dos cursos siguientes:
El primer curso que debes almacenar en dicha lista debe tener los siguientes datos:
```
codigo: 1
nombre: Curso 1
refProfe: Referencia al profesor cuyo codigo es 3.
max_Alumn: 20
fecha_Inic: 1/6/2011
fecha_Fin: 30/6/2011
num_Horas: 30
```
El segundo curso que debes almacenar en dicha lista debe tener los siguientes datos:
```
codigo: 2
nombre: Curso 2
refProfe: Referencia al profesor cuyo DNI es 51083099F.
max_Alumn: 20
fecha_Inic: 1/6/2011
fecha_Fin: 30/6/2011
num_Horas: 30
```
Crea una tabla "Alumnado" de objetos "Alumno". Inserta en dicha tabla las siguientes filas:
```
codigo: 100
dni: 76401092Z
nombre: MANUEL
apellidos: SUAREZ IBAÑEZ
sexo: M
fecha_nac: 30/6/1990
cursoAlumno: objeto creado anteriormente para el primer curso
codigo: 102
dni: 6915588V
nombre: MILAGROSA
apellidos: DIAZ PEREZ
sexo: F
fecha_nac: 28/10/1984
cursoAlumno: objeto que se encuentre en la segunda posición de "listaCursos1" 
 (debe tomarse de la lista)
 ```
Obtener, de la tabla "Alumnado", el alumno que tiene el código 100, asignándoselo a una variable "unAlumno". Modifica el código del alumno guardado en esa variable "unAlumno" asignando el valor 101, y su curso debe ser el segundo que se había creado anteriormente. Inserta ese alumno en la tabla "Alumnado".
```SQL
CREATE Or Replace TYPE ListaCursos AS VARRAY(10) OF cursos;
 
DECLARE
	c1 Cursos;
	c2 Cursos;
    ref1 REF Profesor;
    ref2 REF Profesor;
    listaCursos1 ListaCursos;
    unAlumno Alumno;
BEGIN
    select REF(x) into ref1 FROM profesorado x where codigo = 3;
    select REF(x) into ref2 FROM profesorado x where dni like '51083099F';
	c1 := NEW Cursos(1, 'Curso 1', ref1, 20,TO_DATE('01/06/2015', 'DD/MM/YYYY'),TO_DATE('30/06/2015', 'DD/MM/YYYY'), 30);
	c2 := NEW Cursos(2, 'Curso 2', ref2, 20,TO_DATE('01/06/2015', 'DD/MM/YYYY'),TO_DATE('30/06/2015', 'DD/MM/YYYY'), 30);
    listaCursos1 := listaCursos(c1,c2);
    execute immediate 'create table Alumnado of Alumno (codigo PRIMARY KEY)';
    insert into Alumnado values(100, '76401092Z', 'MANUEL', 'SUAREZ IBAÑEZ', 'M', TO_DATE('30/6/1990', 'DD/MM/YYYY'), listaCursos1(1));
    select DEREF(REF(al)) into unAlumno from alumnado al where al.codigo= 100;
    unAlumno.codigo := 101;
    unAlumno.cursoalumno := listaCursos1(2);
    insert into Alumnado values(unAlumno);
END;
```

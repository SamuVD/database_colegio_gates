# database_colegio_gates

En este readme pondré el script directo y los datos de acceso a la base de datos en la nube.

DATOS DE ACCESO: 
HOST = boz8dsojxxvxgg2xtimt-mysql.services.clever-cloud.com
DATABASE NAME = boz8dsojxxvxgg2xtimt
USER NAME = ubvsljih9tmg8uhs
PASSWORD = HYNmYwDgIv05213rT8cY
PORT: 3306

SCRIPT SQL:

/*______________________________________CREACIÓN DE LAS TABLAS CON SUS ATRIBUTOS Y SUS RESTRICCIONES______________________________________*/

CREATE TABLE `roles` (
  `id_rol` INT,
  `rol` VARCHAR(100),
  PRIMARY KEY (`id_rol`),
  KEY `NN` (`rol`)
);

CREATE TABLE `cursos` (
  `id_curso` INT,
  `curso` VARCHAR(100),
  `jornada` VARCHAR(20),
  PRIMARY KEY (`id_curso`),
  KEY `NN` (`curso`, `jornada`)
);

CREATE TABLE `asignaturas` (
  `id_asignatura` INT,
  `asignatura` VARCHAR(100),
  PRIMARY KEY (`id_asignatura`),
  KEY `NN` (`asignatura`)
);

CREATE TABLE `tipos_de_documentos` (
  `id_tipo_de_documento` INT,
  `tipo` VARCHAR(60),
  PRIMARY KEY (`id_tipo_de_documento`),
  KEY `NN` (`tipo`)
);

CREATE TABLE `estudiantes` (
  `id_estudiante` INT,
  `nombre` VARCHAR(100),
  `apellido` VARCHAR(100),
  `correo_electronico` VARCHAR(255),
  `numero_de_documento` INT,
  `fecha_de_nacimiento` DATE,
  `id_rol` INT,
  `id_tipo_de_documento` INT,
  `id_curso` INT,
  PRIMARY KEY (`id_estudiante`),
  FOREIGN KEY (`id_tipo_de_documento`) REFERENCES `tipos_de_documentos`(`id_tipo_de_documento`),
  FOREIGN KEY (`id_curso`) REFERENCES `cursos`(`id_curso`),
  FOREIGN KEY (`id_rol`) REFERENCES `roles`(`id_rol`),
  KEY `NN` (`nombre`, `apellido`, `correo_electronico`, `numero_de_documento`, `fecha_de_nacimiento`)
);

CREATE TABLE `maestros` (
  `id_maestro` INT,
  `nombre` VARCHAR(100),
  `apellido` VARCHAR(100),
  `correo_electronico` VARCHAR(255),
  `numero_de_documento` INT,
  `fecha_de_nacimiento` DATE,
  `id_rol` INT,
  `id_tipo_de_documento` INT,
  PRIMARY KEY (`id_maestro`),
  FOREIGN KEY (`id_tipo_de_documento`) REFERENCES `tipos_de_documentos`(`id_tipo_de_documento`),
  FOREIGN KEY (`id_rol`) REFERENCES `roles`(`id_rol`),
  KEY `NN` (`nombre`, `apellido`, `correo_electronico`, `numero_de_documento`, `fecha_de_nacimiento`)
);

CREATE TABLE `cursos_asignaturas` (
  `id_curso_asignatura` INT,
  `id_curso` INT,
  `id_asignatura` INT,
  `id_maestro` INT,
  PRIMARY KEY (`id_curso_asignatura`),
  FOREIGN KEY (`id_asignatura`) REFERENCES `asignaturas`(`id_asignatura`),
  FOREIGN KEY (`id_maestro`) REFERENCES `maestros`(`id_maestro`),
  FOREIGN KEY (`id_curso`) REFERENCES `cursos`(`id_curso`)
);

/*_____________________________________________________INSERCIÓN DE LOS REGISTROS_____________________________________________________*/

INSERT INTO roles (rol) VALUES ('estudiante'), ('maestro');

INSERT INTO estudiantes (
nombre, apellido, correo_electronico,
numero_de_documento, fecha_de_nacimiento, id_rol, id_curso) VALUES
('samuel', 'villarreal duque', 'duque@gmail.com', '1354697826', '2000-02-25', 1, 5),
('maria', 'gonzalez', 'maria@gmail.com', '1236548920', '1998-05-10', 1, 4),
('ana', 'perez', 'ana@gmail.com', '1546320595', '2001-03-20', 1, 3),
('luis', 'martinez', 'luis@gmail.com', '1456300257', '2002-11-05', 1, 2),
('daniel', 'cano', 'dacardonac201@gmail.com', '1039447360', '2005-07-19', 1, 1);
UPDATE estudiantes SET id_curso = 3 WHERE id_estudiante = 5;

INSERT INTO maestros (
nombre, apellido, correo_electronico,
numero_de_documento, fecha_de_nacimiento, id_rol) VALUES
('samuel', 'escobar', 'escobar@gmail.com', '1456874596', '1995-02-25', 2),
('maria', 'orozco', 'orozco@gmail.com', '1796542035', '2000-05-10', 2),
('ana', 'ramirez', 'ramirez@gmail.com', '1520330156', '2002-03-20', 2),
('luis', 'arango', 'arango@gmail.com', '1554632057', '1994-11-05', 2),
('daniel', 'zapata', 'zapata@gmail.com', '1356226478', '1990-07-19', 2);

INSERT INTO cursos (curso, jornada) VALUES
('once', 'AM'),
('decimo', 'AM'),
('noveno', 'PM'),
('octavo', 'PM'),
('septimo', 'PM');

INSERT INTO asignaturas (asignatura) VALUES
('matematicas'),
('ingles'),
('ciencias naturales'),
('artistica'),
('lengua castellana');

INSERT INTO tipos_de_documentos (tipo) VALUES
('cedula de ciudadania'),
('tarjeta de identidad'),
('pasaporte'),
('cedula de extranjeria'),
('registro civil');

INSERT INTO cursos_asignaturas (id_curso, id_asignatura, id_maestro) VALUES 
(1, 1, 1),
(2, 2, 2),
(1, 3, 3),
(2, 4, 4),
(1, 5, 5);

/*____________________________________________________________CONSULTAS____________________________________________________________*/

-- Consulta para ver la lista de estudiantes que hay en diferentes cursos "x"

SELECT cursos.curso, estudiantes.id_estudiante as "estudiantes en el curso"
FROM estudiantes
INNER JOIN cursos ON estudiantes.id_estudiante = cursos.id_curso where estudiantes.id_curso = 1;

-- Consulta para ver la lista de estudiantes que ven la asignatura "x"

SELECT estudiantes.nombre, estudiantes.apellido, asignaturas.asignatura
FROM estudiantes
JOIN cursos_asignaturas ON estudiantes.id_curso = cursos_asignaturas.id_curso
JOIN asignaturas ON cursos_asignaturas.id_asignatura = asignaturas.id_asignatura
WHERE asignaturas.id_asignatura = '3';

-- Consulta para ver la lista de maestros que le dictan clases a un estudiante "x"

SELECT maestros.nombre AS maestro, maestros.apellido AS apellido, estudiantes.nombre AS estudiante, estudiantes.apellido AS apellido
FROM maestros
JOIN cursos_asignaturas ON maestros.id_maestro = cursos_asignaturas.id_maestro
JOIN estudiantes ON cursos_asignaturas.id_curso = estudiantes.id_curso
WHERE estudiantes.nombre = 'samuel' AND estudiantes.apellido = 'villarreal duque';

-- Consulta para ver la lista de cursos ordenados de manera descendente por la cantidad de estudiantes inscritos

SELECT cursos.curso, COUNT(estudiantes.id_estudiante) AS cantidad_de_estudiantes
FROM cursos
LEFT JOIN estudiantes ON cursos.id_curso = estudiantes.id_curso
GROUP BY cursos.curso
ORDER BY cantidad_de_estudiantes DESC;

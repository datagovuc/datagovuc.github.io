---
weight: 1
title: "Accesos a BD"
---





# 1. perfilamiento de accesos a en base de datos (Authorization)

##  1.1 Acceso para un grupo de AAD

```tsql
GRANT ALTER ON SCHEMA :: [web] TO [g_magnet];
GRANT SELECT ON SCHEMA :: [web] TO [g_magnet];
GRANT CONTROL ON SCHEMA :: [web] TO [g_magnet];
GRANT CREATE PROCEDURE to [g_magnet];
```

##  1.2 Acceso para un usuario de AAD

```tsql
GRANT ALTER ON SCHEMA :: [web] TO [usuario1@uc.cl];
GRANT SELECT ON SCHEMA :: [web] TO [usuario1@uc.cl];
GRANT CONTROL ON SCHEMA :: [web] TO [usuario1@uc.cl];
GRANT CREATE PROCEDURE to [usuario1@uc.cl];
GRANT CREATE VIEW TO [usuario1@uc.cl];
```

##  1.3 Acceso para un usuario con autenticación SQL-login




##  1.4 Para comprobar accesos

```tsql
EXECUTE AS USER = 'usuario1@uc.cl'
```
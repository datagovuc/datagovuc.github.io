---
weight: 1
title: "API SIPA"
---


## HOW-TO

La API se llama a través de un HTTP trigger desplegado en la siguiente ruta:

[https://apidwuc.azurewebsites.net/](https://apidwuc.azurewebsites.net/)

Y cuyos endpoints para las consultas de acádemicos y unidades académicas son los siguientes:

| Objeto de consulta | Endpoint | Descripción |
| :-- | :-- | :-- |
| Unidades académicas | [/uas](https://apidwuc.azurewebsites.net/uas) | Devuelve el total de las unidades académicas de la universidad. |
| Unidad académica | [/ua/<id unidad académica>](https://apidwuc.azurewebsites.net/ua/1) | Devuelve una unidad académica en según el ID de esta, donde `<id unidad académica>` es el número (ID) de la unidad académica. El link redirecciona a la unidad académica con **`<id unidad académica>` = 1** para ilustrar. |
| Académicos | [/academicos](https://apidwuc.azurewebsites.net/academicos) | Devuelve la totalidad de los académicos. |
| Académicos por código de persona | [/academicos_codper/<cod_per>](https://apidwuc.azurewebsites.net/academicos_codper/163754) | Devuelve un académico según su código de persona. El link redirecciona al académico con **`<cod_per>` = 163754** para ilustrar. |
| Académicos por usuario UC | [/academicos_usuario/<usuario_uc>](https://apidwuc.azurewebsites.net/academicos_usuario/xdarriet) | Devuelve un académico según su usuario UC. El link redirecciona al académico con **`<usuario_uc>` = xdarriet** para ilustrar. |

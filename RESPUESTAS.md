# Respuestas del parcial

1. ¿Qué error encontraste en la validación de `stock`?

El schema `GameCreate` tenía `Field(ge=0, le=50)`, por lo que aceptaba stock 0. Lo corregí a `ge=1, le=50` para cumplir la regla de 1 a 50. El error estaba en la capa Schema.

2. ¿Qué hacía que un juego pareciera creado pero no quedara persistido?

En `create_game` se hacía `session.add(game)` pero faltaba `session.commit()`. Sin commit, la transacción no se confirma y SQLite no guarda el registro. Además, `refresh` sin commit no puede cargar el `id` generado. Agregué el orden correcto: add, commit, refresh.

3. ¿Qué problema tenía el endpoint que lista juegos?

El router declaraba `response_model=GameResponse` (un solo objeto), pero el servicio devuelve una lista. Lo cambié a `response_model=list[GameResponse]`. El error estaba en la capa Router.

4. ¿Cómo solucionaste la consulta de un ID inexistente?

`get_game` devolvía `None` cuando `session.get` no encontraba el registro. Agregué una validación: si no existe, lanza `HTTPException(status_code=404, detail="Game not found")`. Se resolvió en la capa Service.

5. ¿Por qué DELETE podía responder correctamente y aun así no eliminar el registro de SQLite?

`session.delete(game)` solo marca el objeto para eliminarse dentro de la sesión; el cambio no se aplica a la base de datos hasta hacer `session.commit()`. Como faltaba el commit, la API respondía "Game deleted" pero el registro seguía en SQLite. Agregué `session.commit()` después de `session.delete(game)`.


# pipe

```kotlin
interface IHandler<C> {
    suspend fun match(context: C, env: Kodein?): Boolean = true
    suspend fun exec(context: C, env: Kodein?)
}

fun <C> pipe(vararg handlers: IHandler<C>): IHandler<C> {
    return pipe(
        { _: C, _: Kodein? -> true },
        *handlers
    )
}

fun <C> pipe(
    matcher: (context: C, env: Kodein?) -> Boolean,
    vararg handlers: IHandler<C>
): IHandler<C> {
    return object : IHandler<C> {
        override suspend fun match(context: C, env: Kodein?): Boolean {
            return matcher(context, env)
        }

        override suspend fun exec(context: C, env: Kodein?) {
            handlers.forEach { handler ->
                if (handler.match(context = context, env = env)) {
                    handler.exec(context = context, env = env)
                }
            }
        }
    }
}
```
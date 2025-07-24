# HTTP client, Async HTTP
- HTTP is an application-layer protocol sent over TCP connections
- The `socket` library is used to create raw TCP connections
- Sending raw HTTP means manually formatting request lines, headers, and parsing responses
- HTTP clients (like `requests`, `aiohttp`, `curl`) handle all the protocol complexity for you
- Async HTTP means making non-blocking HTTP requests using asynchronous programming
- In async code, functions only pause when you use `await` on an async operation
- If your next line needs data from a request, `await` makes the function pause at that line only
- While awaiting, other async tasks can continue running
- Async improves performance in programs that make many I/O-bound calls (e.g., many HTTP requests)
- If you forget to `await`, you get a coroutine object, not the actual result
- Use sync HTTP for simple tasks, async for high-concurrency I/O or event-driven programs


# Context and Image loading
- Context in Android represents the current state/environment of the app or a component
- It provides access to resources, system services, and app-level info
- There are different types: Application context (lives for entire app), Activity context (tied to one screen), Fragment context (scoped to fragment lifecycle)
- Glide needs context to know where it is in the app and how to access resources
- Passing an Activity or Fragment context lets Glide track lifecycle events (start, stop)
- This lifecycle tracking lets Glide pause, resume, or cancel image loading appropriately
- Passing application context means Glide cannot track UI lifecycle and might keep loading unnecessarily
- Without correct context, Glide can cause memory leaks or load images when UI is not visible
- Under the hood, Glide detects context type and binds requests to lifecycle if possible
- Always pass the most specific context available (Activity, Fragment, or View) for efficient loading
- Avoid passing application context to Glide unless lifecycle tracking is not needed


# `lateinit` and var declarations in Kotlin
- Kotlin enforces **null safety** strictly at compile time to prevent null pointer errors.
- In Kotlin, **non-nullable variables must be initialized when declared or in the constructor**.
- If you want to initialize a non-nullable property later, use `lateinit var` to tell Kotlin you'll initialize it before use.
- Kotlin **does not allow uninitialized non-nullable properties without `lateinit`**; the compiler will give an error.
- In Java, fields are automatically initialized with default values (`null` for objects, `0` for numbers, `false` for booleans).
- Java does **not enforce null safety** at compile time; null pointer exceptions can occur at runtime.
- Kotlin’s approach forces developers to be explicit about initialization and nullability, leading to safer code.
- `lateinit` in Kotlin helps when you can't initialize a property immediately but guarantee it will be initialized later.
- Kotlin’s strictness helps avoid many common bugs that happen in Java due to uninitialized or null fields.
# Internet permission before and networking
Must give the app permission to use internet before any http can be done

`<uses-permission android:name="android.permission.INTERNET" />`
inside the manifest tags (androidmanifest)

# How codepath's asynchttp library works
```kotlin
import com.codepath.asynchttpclient.AsyncHttpClient
import com.codepath.asynchttpclient.RequestParams
import com.codepath.asynchttpclient.callback.TextHttpResponseHandler
import okhttp3.Headers

val client = AsyncHttpClient()
val params = RequestParams()
params["limit"] = "5"
params["page"] = "0"
client["https://api.thecatapi.com/v1/images/search", params, object :
    TextHttpResponseHandler() {
    override fun onSuccess(statusCode: Int, headers: Headers, response: String) {
        // called when response HTTP status is "200 OK"
    }

    override fun onFailure(
        statusCode: Int,
        headers: Headers?,
        errorResponse: String,
        t: Throwable?
    ) {
        // called when response HTTP status is "4XX" (eg. 401, 403, 404)
    }
}]
```
### 1. **The `client` data structure**

- From your code snippet, `client` is likely an **instance of an HTTP client library** that performs network requests.
- The syntax `client[url, object : TextHttpResponseHandler() { ... }]` means:
    - You’re making an HTTP request to `url`.
    - The second argument is an **anonymous object** implementing the `TextHttpResponseHandler` interface (or extending the class).
    - This object defines callbacks for success (`onSuccess`) and failure (`onFailure`).

**In Android, libraries like `AsyncHttpClient` from loopj use this pattern**.

### 2. **What’s `object : TextHttpResponseHandler()`?**

- This is called an **anonymous object expression** in Kotlin.
- It creates an **anonymous subclass** of `TextHttpResponseHandler`.
- You **override** its methods (`onSuccess`, `onFailure`) right there without naming the class explicitly.
- This is Kotlin’s way to implement callback interfaces or abstract classes inline.

### 3. **Why use anonymous objects here?**
- It’s concise — no need to write a whole new named class for the response handler.
- You can directly define the behavior for success and failure **where you call the network request**.
- Keeps related logic in one place.

### 4. **How does it flow?**
- You call `client[url, handler]`.
- The HTTP client performs the request asynchronously.
- When the response arrives:
    - If successful, it calls `handler.onSuccess(...)`.
    - If failed, it calls `handler.onFailure(...)`.
- Your code inside those overrides defines what happens (parse response, update UI, show error, etc.).
# Glide
Glide is a fast and efficient open source media management and image loading framework for Android that wraps media decoding, memory and disk caching, and resource pooling into a simple and easy to use interface.

```kotlin
import okhttp3.Headers  
import com.bumptech.glide.Glide

/**
implementation ("com.github.bumptech.glide:glide:4.15.1")  
annotationProcessor ("com.github.bumptech.glide:compiler:4.15.1") in app/build.gradle first
**/
Glide.with(this)  
    .load(imgUrl)  
    .into(imgField)
```


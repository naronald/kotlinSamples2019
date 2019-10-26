# Kotlin samples

---

## AsyncTask

```kotlin
private inner class CADrawer : AsyncTask<ElemCA, Void, Bitmap>() {

    override fun doInBackground(vararg ca: ElemCA): Bitmap {
        ca[0].drawCA()
        return ca[0].getCA()
    }

    override fun onPostExecute(result: Bitmap) {
        imageView.setImageBitmap(result)
    }
}
```

@[1](takes a ElemCA object, return a Bitmap)
@[3-6](the steps to do in the background)
@[8-10](once the Bitmap is ready, update the UI)


---
## Coroutines

---
@title[Setting up coroutines]

```kotlin
class MainActivity : AppCompatActivity(), CoroutineScope {

    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Main + job

    private lateinit var job: Job
```
    
@[1](need to implement CoroutineScope)
@[3-4](set up local scope and the default dispatcher)
@[6](job allows for control such as cancelling)


---
@title[On click]

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {

    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    job = Job()

    button.setOnClickListener {
        val input = etRule.text.toString()
        val imm = getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
        imm.hideSoftInputFromWindow(it.windowToken, 0)

        launch {
            drawCA(input.toInt())
        }
    }
}
```
    
@[5](initialise job)
@[12-14](launch some code to be run; no return value apart from job)
@[14](after this code will continue to run on Main)



---
@title[Updated drawing]

```kotlin
suspend fun drawCA(rule: Int) {
    
    imageView.setImageBitmap(null)
    progressBar.visibility = View.VISIBLE
    val ca = ElemCA(2000, 5000)
    ca.setNumber(rule)

    val bm = withContext(Dispatchers.Default) { 
        ca.processCA() 
    }
    imageView.setImageBitmap(bm)

    progressBar.visibility = View.INVISIBLE
}
```
    
@[1](suspend indicates that function can wait for a step to complete)
@[8-10](run this on Default thread)
@[11-13](does not execute until bm returned)


---
@title[Finally]

```kotlin
override fun onDestroy() {
    job.cancel()
    super.onDestroy()
}
````

---
@title[Refactored processCA()]

```kotlin
fun processCA(): Bitmap {
    drawCA()
    return ca
}
````
@[3](returns a Bitmap instead of having separate draw and get calls)



### 1. language injections and triple-quoted strings

Java:

![Java example](https://i.ibb.co/B63BCHT/68747470733a2f2f692e6962622e636f2f42636d644d77762f53637265656e73686f742d323032312d30372d30392d61742d.png)

Kotlin:

![Kotlin example](https://i.ibb.co/prH8zpH/68747470733a2f2f692e6962622e636f2f5474624b3544362f53637265656e73686f742d323032312d30372d30392d61742d.png)


since Java 13 https://openjdk.java.net/jeps/355

### 2. string templates

Java:
```java
String s = "Some string with parameter: " + doSomething() + " .";
log.info("Some message with parameter: {} and yet another parameter: {}", param, param2);
```

Kotlin:
```kotlin
val s = "Some string with parameter: ${doSomething()} ."
log.info { "Some message with parameter: $param and yet another parameter: $param2" }
```

### 3. nullability

Java:
```java
String primaryPhone = null;
if (user != null && user.getPhone() != null && user.getPhone().getPrimary() != null) {
  primaryPhone = user.getPhone().getPrimary().getPhone();
}
```

Kotlin:
```kotlin
val primaryPhone = user?.phone?.primary?.phone
```

### 4. data classed

Java:
```java
@Data
public class AttributeResult {
  private String attributeId;
  private String value;
  private Source source;
}
```

Kotlin:
```kotlin
data class AttributeResult(
  private val attributeId: String,
  private val value: String,
  private val source: Source
)
```

### 5. copy in data classes

Java:
```java
@Data
public class User {
  private String name;
  private String lastname;
  private String phone;
  private Integer age;
};

var jack = new User("Jack", "Jones", "+7123456789", 1);
var olderJack = new User(jack.getName(), jack.getLastname(), jack.getPhone(), 2);
```

Kotlin:
```kotlin
data class User(
  val name: String,
  val lastname: String,
  val phone: String,
  val age: Int
)
val jack = User("Jack", "Jones", "+7123456789", 1)
val olderJack = jack.copy(age = 2)
```

### 6. single expression functions
Java:
```java
public void sayHello() {
  System.out.println("Hello");
}

public String getUuidAsString() {
  return UUID.randomUUID().toString();
}
```

Kotlin:
```kotlin
fun sayHello() = println("Hello")

fun getUuidAsString() = UUID.randomUUID().toString()
```

### 7. function naming
Java:
```java
@Test
public void shoudThrowNotFoundExceptionIfUserWithIdNotFoundInDatabase() {
  ...
}

or

@Test
public void shoud_throw_NotFoundException_if_user_with_id_not_found_in_database() {
  ...
}
```

Kotlin:
```kotlin
@Test
fun `shoud throw NotFoundException if user with id not found in database`() {
  ...
}
```

### 8. wrapper functions
Kotlin:
```kotlin
fun <T> runCatchingJrException(block: () -> T) = try {
    block()
} catch (ex: JRException) {
    throw JasperException(ex.safeMessage, ex)
}

fun convertCompiledBytesToJasperReport(compiledBytes: ByteArray): JasperReport = runCatchingJrException {
    JRLoader.loadObject(compiledBytes.inputStream()) as JasperReport
}
```

### 9. multi-value return
Java:
```java
@Data
class Student {
  private String name;
  private Integer age;
}

public Student studentsFun() {
  return new Student("John", 11);
}

var student = studentsFun();
var name = student.getName();
var age = student.getAge();

System.out.println(name);
System.out.println(age);
```

Kotlin:
```kotlin
data class Student(val name:String, val age:Int)

fun studentsFun() = Student("John", 11)

val (name,age) = studentsFun(stud)
println(name)
println(age)
```

### 10. smartcasts
Java:
```java
public void demo(Object x) {
  if (x instanceOf String) {
    System.out.println(((String) x).length());
  }
}
```

Kotlin:
```kotlin
fun demo(x: Any) {
  if (x is String) {
    print(x.length) // x is automatically cast to string
  }
}
```

### 11. extension functions

Kotlin:
```kotlin
fun BigDecimal.round() = this.setScale(2, RoundingMode.HALF_UP)

"10.123456".toBigDecimal().round()
```

### 12. inline functions with reified type
Java:
```java
public <T> T toObject(json: String, Class<T> clazz) {
  return objectMapper.readValue(json, clazz);
}

var userJson = " { \"name\": \"Alex\" } ";
toObject(userJson, User.class);
```

Kotlin:
```kotlin
inline fun <reified T> String.toObject(): T = objectMapper.readValue(this, T::class.java)

val userJson = """ { "name": "Alex" } """
val user = userJson.toObject<User>()
```

Java:
```kotlin
objectMapper.readValue(json, new TypeReference<List<User>>() {})
```

Kotlin:
```kotlin
inline fun <reified T> ObjectMapper.readValue(json: String): T = this.readValue(json, object : TypeReference<T>() {})

objectMapper.readValue<List<User>>(json)
```

### 13. mapping

Java:
```java
public FulfillmentTask convertToFulfillmentTask(FulfillmentTaskResponse response) {
  CreationInformation created = null;
  if (response.getCreated() != null) {
    created = toCreationInformation(response.getCreated());
  }
  FulfillmentTaskPointOfGiveAway pointOfGiveAway = null;
  if (response.getPointOfGiveAway() != null) {
    pointOfGiveAway = toFulfillmentTaskPointOfGiveAway(response.getPointOfGiveAway());
  }
  List<FulfillmentTaskLine> lines = null;
  if (response.getLines() != null) {
    lines = response.getLines().stream()
      .map(this::toFulfillmentTaskLine)
      .collect(Collectors.toList());
  }
  var fulfillmentTask = new FulfillmentTask(
    response.getTaskId(),
    response.getExtOrderId(),
    response.getTaskStatus(),
    response.getBuId(),
    response.getStoreId(),
    response.getPriority(),
    created,
    response.getCustomerId(),
    response.getDocumentType(),
    pointOfGiveAway,
    response.getDateOfGiveAway(),
    response.getTotalAmount(),
    response.getCurrency(),
    response.getComment(),
    lines
  );
  return fulfillmentTask;
}
```

Kotlin:
```kotlin
fun convertToFulfillmentTask(response: FulfillmentTaskResponse) = FulfillmentTask(
  taskId = response.taskId,
  extOrderId = response.extOrderId,
  taskStatus = response.taskStatus,
  buId = response.buId,
  storeId = response.storeId,
  priority = response.priority,
  created = response.created?.toCreationInformation(),
  customerId = response.customerId,
  documentType = response.documentType,
  pointOfGiveAway = response.pointOfGiveAway?.toFulfillmentTaskPointOfGiveAway(),
  dateOfGiveAway = response.dateOfGiveAway,
  totalAmount = response.totalAmount,
  currency = response.currency,
  comment = response.comment,
  lines = response.lines?.map { it.toFulfillmentTaskLine() }
)
```

### 14. sealed classes and interfaces

Kotlin:
```kotlin
sealed class Expr
data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()

fun eval(expr: Expr): Double = when (expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
}
```

### 15. default attributes and overriding
Java:
```java
public void foo(String baz) {
  foo("default", baz);
}

public void foo(String bar, String baz) {
  ...
}
```

Kotlin:
```kotlin
fun foo(bar: String = "default", baz: Int) {
  ...
}
```

### 16. interface delegation
```kotlin
interface Base {
    fun printMessage()
    fun printMessageLine()
}

class BaseImpl(val x: Int) : Base {
    override fun printMessage() { print(x) }
    override fun printMessageLine() { println(x) }
}

class Derived(b: Base) : Base by b {
    override fun printMessage() { print("abc") }
}
```

### 17. it keyword
Java:
```java
  ...
  .map(num -> num.toString())
  ...
```

Kotlin:
```kotlin
  ...
  .map{ it.toString() }
  ...
```

### 18. streams and collectors
Java:
```java
List.of(1, 2, 3, 4)
  .stream()
  .map(num -> num.toString())
  .collect(Collectors::toSet());
```

Kotlin:
```kotlin
listOf(1, 2, 3, 4)
  .map{ it.toString() }
  .toSet()
```

### 19. mutable and immutable collections
```java
List.of(1, 2, 3);
new ArrayList<>(List.of(1, 2, 3));

Set.of(1, 2, 3);
new HashSet<>(Set.of(1, 2, 3));

Map.of(1, "1");
new HashMap<>(Map.of(1, "1"));
```

```kotlin
listOf(1, 2, 3)
mutableListOf(1, 2, 3)

setOf(1, 2, 3)
mutableSetOf(1, 2, 3)

mapOf(1 to "1")
mutableMapOf(1 to "1")
```

### 20. mapOf
Java:
```java
static <K,​V> Map<K,​V> of​(K k1, V v1);
static <K,​V> Map<K,​V> of​(K k1, V v1, K k2, V v2);
...

Map<Integer, String> map = Map.of(
  101, "PP",
  102, "QQ",
  103, "RR"
);
```

Kotlin:
```kotlin
fun <K, V> mapOf(vararg pairs: Pair<K, V>): Map<K, V>

val map = mapOf(
  101 to "PP",
  102 to "QQ",
  103 to "RR"
)
```

### 21. when and pair
Kotlin:
```kotlin
val dbEntryExists: Boolean = groupingDb != null
val anyAuthorSaleInRequest: Boolean = request.authorSaleLines.isNotEmpty()

return when(dbEntryExists to anyAuthorSaleInRequest) {
    false to false -> request
    false to true -> {
        createNewDbEnry(request)
        request
    }
    true to false -> overwriteRequestLinesWithDbLines(groupingDb, request)
    true to true -> mergeRequestLinesWithDbLines(groupingDb, request)
    else -> error("Impossible case")
}
```

### 22. with apply also run let
// with

Java:
```java
var numbers = List.of("one", "two", "three");
System.out.println("'with' is called with argument " + numbers);
System.out.println("It contains " + numbers.length() + " elements");
```

Kotlin:
```kotlin
val numbers = listOf("one", "two", "three")
with(numbers) {
  println("'with' is called with argument $this")
  println("It contains $size elements")
}
```

// apply

Java:
```java
var adam = new Person("Adam");
adam.setAge(32);
adam.setCity("London");
```

Kotlin:
```kotlin
val adam = Person("Adam").apply {
  age = 32
  city = "London"
}
```

// also

Java:
```java
public List<String> foo() {
  var listOfString = bar();
  log.info("Result: {}", listOfString);
  return listOfString;
}
```

Kotlin:
```kotlin
fun foo() = bar().also {
  log.info("Result: $it")
}
```

// run

Java:
```java
public void mood() {
  var mood = "I am sad";
  var mood2 = "I am happy";
  System.out.println(mood2);
  System.out.println(mood);
}
```

Kotlin:
```kotlin
fun mood() {
  var mood = "I am sad"

  run {
    val mood = "I am happy"
    println(mood) // I am happy
  }
  println(mood)  // I am sad
}
```

// let

Java:
```java
String cat = null;
if (cat != null) {
  System.out.println(cat);
}
cat = "Barsik"
if (cat != null) {
  System.out.println(cat);
}
```

Kotlin:
```kotlin
var cat: String? = null
cat?.let{println(it)} // do not print
cat = "Barsik"
cat?.let{println(it)}
```

### 23. chunked
Java:
```java
Integer productsChunkSize = 2;
List<Intger> productIds = List.of(1, 2, 3, 4);
List<ProductAttribute> productAttributes = new ArrayList<>();
Set<String> currentProducts = new HashSet<>();

for (var productId : productIds) {
  currentProducts.add(productId);
  if (++counter == productsChunkSize) {
    productAttributes.putAll(productClient.getProducts(Set.copyOf(currentProducts)));
    currentProducts.clear();
    counter = 0;
  }
}
```

Kotlin:
```kotlin
val productsChunkSize = 2
val productIds = listOf(1, 2, 3, 4)
val productAttributes = productIds.chunked(productsChunkSize)
    .map { productClient.getProducts(it.toSet()) }
    .flatten()
```

### 24. Partition and destructive declaration
Kotlin:
```kotlin
data class Person(val name: String, val age: Int)

val list = listOf(Person("Tom", 18), Person("Andy", 32), Person("Sarah", 22))
val (young, old) = list.partition { it.age < 30 } // ([Tom - 18, Sarah - 22], [Andy - 32])
```

### 25. associate
Java:
```java
var byLastName = new HashMap<String, String>();
var names = List.of("Grace Hopper", "Jacob Bernoulli", "Johann Bernoulli");

var List<List<String> splitStrs = names
    .stream()
    .map(str -> str.split(" "))
    .collect(Collectors::toList());

splitStrs.stream().foreach(item -> byLastName.put(item[0], item[1]))

System.out.println(byLastName);
```

Kotlin:
```kotlin
val names = listOf("Grace Hopper", "Jacob Bernoulli", "Johann Bernoulli")

val byLastName = names
    .associate { 
        it.split(" ")
        .let { (firstName, lastName) -> lastName to firstName } 
    }

// Jacob Bernoulli does not occur in the map because only the last pair with the same key gets added
println(byLastName) // {Hopper=Grace, Bernoulli=Johann}
```

### 26. equality
Java:
```java
a.equals(b)
a == b
```

Kotlin:
```kotlin
a == b
a === b
```

### 27. Operator overloading
Java:
```java
BigDecimal.Ten.add(BigDecimal.Ten);
BigDecimal.Ten.multiply(BigDecimal.Ten);
```

Kotlin:
```kotlin
BigDecimal.Ten + BigDecimal.Ten
BigDecimal.Ten * BigDecimal.Ten
```

### 28. Coroutines

https://kotlinlang.org/docs/coroutines-overview.html

### 29. Compilation time
https://medium.com/keepsafe-engineering/kotlin-vs-java-compilation-speed-e6c174b39b5d

### 30. Load testing
https://confluence.lmru.tech/display/NDP/Repository+-+02.04.2020.+Test+from+350rps+to+1000rps+on+repository+x4+and+gateway+x4


---
tags:
  - software
summary: creational patterns, structural patterns, behavioral patterns
use_cases:
  - backend
  - frontend
  - architecture
---

# Design Patterns

Design Patterns are reusable solutions to common problems in software design.
They are not code, but templates for organizing code.

## Creational Patterns

Focus: How to create objects

### Singleton

Ensure a class has only one instance.

```java
class Singleton {
    private static final Singleton INSTANCE = new Singleton(); // single instance

    private Singleton() {
        // prevent external instantiation
    }

    public static Singleton getInstance() {
        // global access point
        return INSTANCE;
    }
}

// usage
class Client {
    public static void main(String[] args) {
        Singleton s1 = Singleton.getInstance();
        Singleton s2 = Singleton.getInstance();
        // s1 and s2 refer to the same instance
    }
}
```

### Factory Method

Create objects without exposing instantiation logic.

```java
interface Parser {
    void parse();
}

class JsonParser implements Parser {
    public void parse() {
        // parse JSON data
    }
}

class XmlParser implements Parser {
    public void parse() {
        // parse XML data
    }
}

class ParserFactory {
    public static Parser create(String fileName) {
        // decide concrete class by input parameter
        if (fileName.endsWith(".json")) return new JsonParser();
        if (fileName.endsWith(".xml")) return new XmlParser();
        throw new IllegalArgumentException("Unsupported file type");
    }
}

// usage
class Client {
    public static void main(String[] args) {
        Parser parser = ParserFactory.create("data.json");
        parser.parse();
    }
}
```

### Abstract Factory

Create families of related objects.

```java
interface Button {
    void click();
}

interface TextBox {
    void input();
}

class WindowsButton implements Button {
    public void click() {
        // Windows button logic
    }
}

class WindowsTextBox implements TextBox {
    public void input() {
        // Windows text box logic
    }
}

class MacButton implements Button {
    public void click() {
        // Mac button logic
    }
}

class MacTextBox implements TextBox {
    public void input() {
        // Mac text box logic
    }
}

interface GUIFactory {
    Button createButton();
    TextBox createTextBox();
}

class WindowsFactory implements GUIFactory {
    public Button createButton() {
        return new WindowsButton();
    }

    public TextBox createTextBox() {
        return new WindowsTextBox();
    }
}

class MacFactory implements GUIFactory {
    public Button createButton() {
        return new MacButton();
    }

    public TextBox createTextBox() {
        return new MacTextBox();
    }
}

// usage
class Client {
    public static void main(String[] args) {
        GUIFactory factory = new WindowsFactory(); // or new MacFactory
        Button button = factory.createButton();
        TextBox textBox = factory.createTextBox();
        button.click();
        textBox.input();
    }
}
```

### Builder

Construct complex objects step by step.

```java
class Request {
    String url;
    String host;
    Map<String, String> headers = new HashMap<>();
}

class RequestBuilder {
    private Request req = new Request();

    public RequestBuilder url(String url) {
        // step1: set url
        req.url = url;
        // derive host from url (dependency)
        req.host = parseHost(url);
        return this;
    }

    public RequestBuilder addHeader(String key, String value) {
        // step2: add headers (depends on url)
		if (req.url == null) {
			throw new IllegalStateException("set url first");
		}
        req.headers.put(key, value);
        return this;
    }

    public Request build() {
        // final validation
        if (req.url == null) {
            throw new IllegalStateException("url required");
        }
        return req;
    }

    private String parseHost(String url) {
        // parsing logic
        return url.split("/")[2];
    }
}

// usage
class Client {
    public static void main(String[] args) {
        Request r = new RequestBuilder()
			.url("http://example.com/api") // set url
			.addHeader("Auth", "token") // add headers (depends on url)
			.build();
    }
}
```

## Structural Patterns

Focus: How to compose classes/objects

### Adapter

Convert one interface into another.

```java
class OldAPI {
    void specificRequest() {
        // old logic
    }
}

interface NewAPI {
    void request();
}

class Adapter implements NewAPI {
    private OldAPI old = new OldAPI(); // hidden old system

    public void request() {
        // convert input if needed
        old.specificRequest(); // delegate to old API
        // convert output if needed
    }
}

// usage
class Client {
	public static void main(String[] args) {
		NewAPI api = new Adapter(); // client only knows NewAPI
		api.request(); // transparently uses OldAPI
	}
}
```

### Decorator

Add behavior dynamically.

```java
interface Coffee {
    int cost();
}

class BasicCoffee implements Coffee {
    public int cost() {
        // base price
        return 5;
    }
}

class MilkDecorator implements Coffee {
    private Coffee coffee;

    public MilkDecorator(Coffee coffee) {
        this.coffee = coffee;
    }

    public int cost() {
        // before enhancement: add milk cost
        int result = coffee.cost();
        result += 2; // milk price
        // after enhancement (if needed)
        return result;
    }
}

class SugarDecorator implements Coffee {
    private Coffee coffee;

    public SugarDecorator(Coffee coffee) {
        this.coffee = coffee;
    }

    public int cost() {
        // before enhancement: add sugar cost
        int result = coffee.cost();
        result += 1; // sugar price
        // after enhancement (if needed)
        return result;
    }
}

// usage
class Client {
	public static void main(String[] args) {
		Coffee c =
		    new SugarDecorator(
		        new MilkDecorator(
		            new BasicCoffee()
		        )
		    );
		// layered enhancement: Basic -> Milk -> Sugar
		int price = c.cost();
	}
}
```

### Proxy

Control access to an object.

```java
interface Service {
    void request();
}

class RealService implements Service {
    public void request() {
        // real business logic
    }
}

class ProxyService implements Service {
    private Service real = new RealService(); // hidden real object

    public void request() {
        // before control logic: permission / cache / transaction
        real.request();
        // after control logic: logging / metrics / cleanup
    }
}

// usage
class Client {
    public static void main(String[] args) {
        Service s = new ProxyService(); // client unaware of real object
        s.request();
    }
}
```

## Facade

Provide a simplified interface.

```java
class VideoDecoder {
    void decode(String file) {
        // decode video stream
    }
}

class AudioDecoder {
    void decode(String file) {
        // decode audio stream
    }
}

class Renderer {
    void render() {
        // render frames to screen
    }
}

class PlayerFacade {
    private VideoDecoder video = new VideoDecoder();
    private AudioDecoder audio = new AudioDecoder();
    private Renderer renderer = new Renderer();

    public void play(String file) {
        // step1: decode video
        video.decode(file);
        // step2: decode audio
        audio.decode(file);
        // step3: render output
        renderer.render();
    }
}

// usage
class Client {
    public static void main(String[] args) {
        PlayerFacade player = new PlayerFacade();
        player.play("movie.mp4"); // simple interface for complex workflow
    }
}
```
## Behavioral Patterns

Focus: Object interaction

### Strategy

Encapsulate interchangeable algorithms.

```java
interface PaymentStrategy {
    void pay(int amount);
}

class CreditCardPayment implements PaymentStrategy {
    public void pay(int amount) {
        // pay by credit card
    }
}

class PayPalPayment implements PaymentStrategy {
    public void pay(int amount) {
        // pay by PayPal
    }
}

class Checkout {
    private PaymentStrategy strategy;

    public Checkout(PaymentStrategy strategy) {
        this.strategy = strategy;
    }

    public void process(int amount) {
        // delegate to chosen payment strategy
        strategy.pay(amount);
    }
}

// usage
class Client {
    public static void main(String[] args) {
        Checkout c1 = new Checkout(new CreditCardPayment());
        c1.process(100); // use credit card
        Checkout c2 = new Checkout(new PayPalPayment());
        c2.process(200); // use PayPal
    }
}
```

### Observer

One-to-many dependency.

```java
import java.util.*;

interface Observer {
    void update(int temperature);
}

class PhoneDisplay implements Observer {
    public void update(int temperature) {
        // show temperature on phone
    }
}

class AirConditioner implements Observer {
    public void update(int temperature) {
        // adjust AC based on temperature
    }
}

class WeatherStation {
    private List<Observer> observers = new ArrayList<>();
    private int temperature;

    public void add(Observer o) {
        observers.add(o);
    }

    public void setTemperature(int t) {
        temperature = t;
        // notify all observers
        for (Observer o : observers) {
            o.update(temperature);
        }
    }
}

// usage
class Client {
    public static void main(String[] args) {
        WeatherStation station = new WeatherStation();
        station.add(new PhoneDisplay());
        station.add(new AirConditioner());
        station.setTemperature(30); // all observers react
    }
}
```

### Chain of Responsibility

Pass request along a chain.

```java
abstract class Handler {
    protected Handler next;

    public Handler setNext(Handler next) {
        this.next = next;
        return next;
    }

    public abstract void handle(Request request);
}

class Request {
    int amount;

    Request(int amount) {
        this.amount = amount;
    }
}

class Manager extends Handler {
    public void handle(Request request) {
        if (request.amount <= 1000) {
            // manager approves
            return;
        }

        if (next != null) next.handle(request);
    }
}

class Director extends Handler {
    public void handle(Request request) {
        if (request.amount <= 10000) {
            // director approves
            return;
        }

        if (next != null) next.handle(request);
    }
}

class CEO extends Handler {
    public void handle(Request request) {
        // CEO handles final approval
    }
}

// usage
class Client {
    public static void main(String[] args) {
        Handler manager = new Manager();
        Handler director = new Director();
        Handler ceo = new CEO();

        manager.setNext(director).setNext(ceo);

        manager.handle(new Request(500));
        manager.handle(new Request(5000));
        manager.handle(new Request(50000));
    }
}
```

### Template Method

Define algorithm skeleton.

```java
abstract class DataProcessor {
    public final void process() {
        readData();
        processData();
        saveData();
    }

    protected abstract void readData();

    protected abstract void processData();

    protected void saveData() {
        // default save logic
    }
}

class CSVProcessor extends DataProcessor {
    protected void readData() {
        // read CSV file
    }

    protected void processData() {
        // process CSV data
    }
}

class JSONProcessor extends DataProcessor {
    protected void readData() {
        // read JSON file
    }

    protected void processData() {
        // process JSON data
    }
}

// usage
class Client {
    public static void main(String[] args) {
        DataProcessor p1 = new CSVProcessor();
        p1.process();

        DataProcessor p2 = new JSONProcessor();
        p2.process();
    }
}
```
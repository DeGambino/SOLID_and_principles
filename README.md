# SOLID and Other Principles



<details>
  <summary>The Law of Demeter (LoD)</summary>
  
  ## Core Principles

1. **Limited Knowledge:** A unit (e.g., function, module, or class) should have limited knowledge about other units.
2. **Immediate Friends:** A unit should only interact with its immediate friends.
3. **No Strangers:** A unit should not communicate with strangers or unknown units.

## Concept Explanation

- **Unit Definition:** In this context, a unit can be a function, module, or class.
- **Talking to a Stranger:** This involves interfacing with or calling code from a unit that is not an immediate friend.

## Example

**Real-Life Analogy:**
- Consider a shopkeeper who directly accesses a customer’s wallet to take money without interacting with the customer. This is socially inappropriate and unrealistic.

**Improved Design:**
- Instead, the shopkeeper should request payment from the customer, who then manages their own wallet and handles the payment.

## Programming Implications

- **DOM Example:** If a function directly manipulates the Document Object Model (DOM), it violates LoD. Instead, create a separate abstraction to handle DOM interactions.
  
- **Improved Code Structure:** Develop generalized functions for common tasks to enhance maintainability and adaptability.

## Benefits of Applying LoD

- **Decoupling:** Reduces dependencies between different parts of the codebase.
- **Maintainability:** Simplifies modifications and extensions.
- **Flexibility:** Facilitates changes in implementation without affecting other code parts.

</details>

<details>
  <summary>SOLID Principles</summary>
  
SOLID is a set of five object-oriented programming (OOP) design principles that help in constructing modules or architectures. The acronym stands for:

1. **Single Responsibility Principle (SRP)**
2. **Open-Closed Principle (OCP)**
3. **Liskov Substitution Principle (LSP)**
4. **Interface Segregation Principle (ISP)**
5. **Dependency Inversion Principle (DIP)**

These principles guide the creation of clean and maintainable code. Understanding the core ideas behind SOLID can improve code quality, regardless of the programming paradigm used.

## 1. Single Responsibility Principle (SRP)

- **Definition:** A class or module should have one, and only one, reason to change. This means that each class should have a single responsibility or purpose.
- **Key Concept:** Cohesion. A class or function should be focused on a single task or responsibility.
- **Example:** Imagine you have a function that handles both rendering a list and logging details about the data. This violates SRP because it has two reasons to change (rendering and logging).

### Problematic Code:

```javascript
  function renderAndLogList(items) {
  items.forEach(item => {
    // Render the item
    console.log(`Rendering item: ${item}`);
    document.body.innerHTML += `<div>${item}</div>`;

    // Log the item
    console.log(`Logged: ${item}`);
  });
}

```


### Improved Code (SRP Applied):

```javascript
  function renderList(items) {
  items.forEach(item => {
    document.body.innerHTML += `<div>${item}</div>`;
  });
}

function logItems(items) {
  items.forEach(item => {
    console.log(`Logged: ${item}`);
  });
}

// Now we separate concerns
const items = ['apple', 'banana', 'orange'];
renderList(items);
logItems(items);

```
This way, changes to rendering or logging can be made independently.


## 2. Open-Closed Principle (OCP)

- **Definition:** Software entities should be open for extension but closed for modification. This means you should be able to add new functionality without altering existing code.
- **Key Concept:** Adaptability. You should be able to extend a class’s behavior without changing its source code.
- **Example:** Suppose you have a class for notifications that needs to be extended for new notification types. We violate OCP if we modify the existing code for each new type.

### Problematic Code:

```javascript
 class Notification {
  send(type, message) {
    if (type === 'email') {
      console.log(`Sending email: ${message}`);
    } else if (type === 'sms') {
      console.log(`Sending SMS: ${message}`);
    }
  }
}

```

### Improved Code (OCP Applied):

```javascript
 class Notification {
  send(notificationType) {
    notificationType.send();
  }
}

class EmailNotification {
  constructor(message) {
    this.message = message;
  }
  send() {
    console.log(`Sending email: ${this.message}`);
  }
}

class SMSNotification {
  constructor(message) {
    this.message = message;
  }
  send() {
    console.log(`Sending SMS: ${this.message}`);
  }
}

const email = new EmailNotification('Hello via Email');
const sms = new SMSNotification('Hello via SMS');
const notification = new Notification();

notification.send(email);
notification.send(sms);

```
Now, you can extend it with new notification types without modifying the `Notification` class.


## 3. Liskov Substitution Principle (LSP)

- **Definition:** Subtypes should be substitutable for their base types without altering the correctness of the program. In other words, a derived class should be able to replace a base class without affecting functionality.

- **Key Concept:** Substitutability. Derived classes should extend base classes without changing their expected behavior.

- **Example:** Imagine you have a `Bird` class with a `fly()` method that is used in many parts of your program. Now, you introduce a `Penguin` subclass, but penguins can't fly. When a `Penguin` is passed into a function expecting a `Bird` and that function calls `fly()`, the program breaks because `Penguin` does not behave like a `Bird`.

### Problematic Code:

```javascript
 class Bird {
  fly() {
    console.log('Flying...');
  }
}

class Penguin extends Bird {
  fly() {
    throw new Error('Penguins cannot fly!');
  }
}

function makeBirdFly(bird) {
  bird.fly();
}

const genericBird = new Bird();
const penguin = new Penguin();

makeBirdFly(genericBird); // Works fine
makeBirdFly(penguin);     // Throws error

```

**Why it's a problem:** The `Penguin` class violates LSP because it cannot substitute the `Bird` class. The client expects all birds to be able to fly, but `Penguin` throws an error.


### Improved Code (LSP Applied):

```javascript
  class Bird {
  move() {
    console.log('Moving...');
  }
}

class FlyingBird extends Bird {
  fly() {
    console.log('Flying...');
  }
}

class Penguin extends Bird {
  move() {
    console.log('Swimming...');
  }
}

function makeBirdMove(bird) {
  bird.move();
}

const genericBird = new FlyingBird();
const penguin = new Penguin();

makeBirdMove(genericBird); // Works fine: 'Flying...'
makeBirdMove(penguin);     // Works fine: 'Swimming...'

```

**Key Takeaway:** Now `Penguin` adheres to LSP because it can substitute `Bird` in a way that makes sense within the expected behavior.

## 4. Interface Segregation Principle (ISP)

- **Definition:** Clients should not be forced to depend on interfaces they do not use. This means creating focused and cohesive interfaces that are relevant to their clients.
- **Key Concept:** Cohesive Interfaces. Design interfaces to handle only related functions, avoiding large and complex interfaces.

- **Example:** Imagine you have an `Employee` interface that includes methods like `work()`, `eat()`, and `manage()`. Now, both `Manager` and `Intern` classes implement this interface. However, the `Intern` class doesn’t need the `manage()` method, and the `Manager` class doesn’t need the `eat()` method. This forces both classes to implement unnecessary methods they don’t need.

### Problematic Code:

```javascript
// Violates ISP because both Intern and Manager have to implement unnecessary methods
class Employee {
  work() {
    throw new Error("Method not implemented.");
  }

  eat() {
    throw new Error("Method not implemented.");
  }

  manage() {
    throw new Error("Method not implemented.");
  }
}

class Intern extends Employee {
  work() {
    console.log("Intern is working.");
  }

  // Intern does not need to implement manage() but is forced to
  manage() {
    throw new Error("Interns do not manage.");
  }

  eat() {
    console.log("Intern is eating.");
  }
}

class Manager extends Employee {
  work() {
    console.log("Manager is working.");
  }

  manage() {
    console.log("Manager is managing.");
  }

  // Manager does not need to implement eat() but is forced to
  eat() {
    throw new Error("Managers do not need to eat in this context.");
  }
}

```

**Why it's a problem:** Here, the `Intern` class is forced to implement the `manage()` method even though it doesn't need it, and the `Manager` class is forced to implement the `eat()` method unnecessarily. This violates ISP because both classes are dependent on methods they don't use.


### Improved Code (ISP Applied):

```javascript
  // Separate interfaces for different roles
class Worker {
  work() {
    throw new Error("Method not implemented.");
  }
}

class Eater {
  eat() {
    throw new Error("Method not implemented.");
  }
}

class ManagerRole {
  manage() {
    throw new Error("Method not implemented.");
  }
}

// Intern only implements work and eat methods
class Intern extends Worker {
  work() {
    console.log("Intern is working.");
  }
}

class Manager extends Worker {
  work() {
    console.log("Manager is working.");
  }

  manage() {
    console.log("Manager is managing.");
  }
}


```

Now, `Intern` and `Manager` only implement the methods they need. This follows the Interface Segregation Principle.


## 5. Dependency Inversion Principle (DIP)

- **Definition:** High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details. Details should depend on abstractions.
- **Key Concept:** Decoupling. High-level code should interact with abstractions rather than concrete implementations, which should be decoupled from high-level logic.

- **Example:** Imagine you have a `ReportGenerator` class that directly depends on a `PDFExporter` class to export reports in PDF format. If you want to support exporting reports in another format, like Excel, you would have to modify the `ReportGenerator` class to include this new functionality, tightly coupling it to multiple concrete classes.


### Problematic Code:

```javascript
// Violates DIP because ReportGenerator depends directly on a concrete implementation
class PDFExporter {
  export(content) {
    console.log("Exporting report as PDF:", content);
  }
}

class ReportGenerator {
  constructor() {
    this.exporter = new PDFExporter();
  }

  generateReport(content) {
    console.log("Generating report...");
    this.exporter.export(content); // Tightly coupled to PDFExporter
  }
}

// If we need Excel export, we have to modify ReportGenerator
class ExcelExporter {
  export(content) {
    console.log("Exporting report as Excel:", content);
  }
}

```

**Why it's a problem:** In this example, the `ReportGenerator` is tightly coupled to the `PDFExporter` class, violating DIP. If we want to support another export format like Excel, we would need to modify the `ReportGenerator` class directly, which makes the code harder to maintain and extend.


### Improved Code (DIP Applied):

```javascript
  // Abstraction for export functionality
class Exporter {
  export(content) {
    throw new Error("Method not implemented.");
  }
}

// Concrete implementations of the exporter interface
class PDFExporter extends Exporter {
  export(content) {
    console.log("Exporting report as PDF:", content);
  }
}

class ExcelExporter extends Exporter {
  export(content) {
    console.log("Exporting report as Excel:", content);
  }
}

// ReportGenerator now depends on an abstraction
class ReportGenerator {
  constructor(exporter) {
    this.exporter = exporter; // Dependency injection
  }

  generateReport(content) {
    console.log("Generating report...");
    this.exporter.export(content);
  }
}

// Usage example
const pdfExporter = new PDFExporter();
const reportGeneratorPDF = new ReportGenerator(pdfExporter);
reportGeneratorPDF.generateReport("Quarterly report content");

const excelExporter = new ExcelExporter();
const reportGeneratorExcel = new ReportGenerator(excelExporter);
reportGeneratorExcel.generateReport("Annual report content");


```

Now, `ReportGenerator` depends on an abstraction (`Exporter`), not a concrete class (`PDFExporter` or `ExcelExporter`). This follows the Dependency Inversion Principle (DIP), making it easier to swap out different export formats without modifying the core `ReportGenerator` logic.


</details>

<details>
  <summary>Abstraction Principle</summary>
  
**Definition:**

- **Implementation should be separate from interface.**

**Concept:**

- **Abstraction**: A simplified view of complex systems that hides underlying details.
- **Interface**: The simplified interaction point provided by the abstraction.

**Key Warnings:**

1. **Don't Repeat Yourself (DRY)**:
   - Avoid code duplication. If you find code repetition, it indicates that abstraction is missing or insufficient.

2. **You Aren't Gonna Need It (YAGNI)**:
   - Avoid over-abstraction. Create abstractions only when necessary to prevent adding unnecessary complexity.

**Balancing Act:**

- **Under-abstraction**: Results in duplicated code.
- **Over-abstraction**: Adds unnecessary complexity.


</details>

<details>
  <summary>Over-Abstraction</summary>
  
 **Definition:**

- **Over-abstraction** occurs when an abstraction becomes too complex or too simplified, making it harder to use effectively.

**Risks:**

- **Excess Complexity**: Removing too much complexity may strip away important features, rendering the abstraction less useful.
- **Unnecessary Complexity**: Adding extra features can complicate the interface and make it confusing.

**Examples:**

1. **Over-Complicated Abstraction**:
   - **Issue**: The interface is overly complex, with many features and details that are not needed for the basic requirements. This added complexity makes it difficult to use the abstraction effectively.

2. **Oversimplified Abstraction**:
   - **Issue**: The interface is too basic and lacks essential features, such as customization options or detailed controls. This simplicity limits its functionality and flexibility.

**Balancing Act:**

- The appropriate level of abstraction is context-dependent. What is considered over-abstraction in one scenario might be suitable in another. Striking the right balance is crucial to ensure that the abstraction simplifies complexity without introducing new challenges.

</details>

<details>
  <summary>Under-Abstraction</summary>
  
  **Definition:**

- **Under-abstraction** occurs when an abstraction fails to simplify enough, leaving too much complexity exposed to the user.

**Risks:**

- **Exposed Complexity**: Users must manage and interact with complex underlying details directly, making the abstraction less effective.
- **Redundancy**: Users may need to repeat information or manage platform-specific details that should be abstracted away.

**Examples:**

1. **Under-Abstracted Gallery Component**:
   - **Issue**: The component requires defining platform-specific code for both web and Android, exposing internal complexities rather than hiding them. Users must handle HTML for the web and Java code for Android directly.

2. **Repetitive Details**:
   - **Issue**: The abstraction forces users to repeatedly specify details like image sources and captions, which should be managed by the abstraction itself.

**Implications:**

- **DRY Principle**: Repeating information indicates that the abstraction is not sufficiently abstracting away complexity.
- **Leaky Abstractions**: Some abstractions may leak their internal complexities through their interfaces, making it evident that they are under-abstracted.

**Key Takeaway:**

- Effective abstractions should hide unnecessary complexity and provide a simplified interface. Being vigilant for areas where complexity is exposed or redundancy occurs can help in creating better abstractions.
</details>

<details>
  <summary>Balanced Abstraction</summary>
  
**Definition:**

- **Balanced abstraction** is an abstraction that strikes the right balance between simplifying complexity and maintaining essential functionalities. It avoids both over-abstraction (adding unnecessary complexity) and under-abstraction (exposing too much underlying complexity).

**Characteristics:**

- **Essential Features**: It addresses the core requirements of the problem domain effectively without introducing extraneous features or complications.
- **Simplified Interface**: It provides a user-friendly interface that hides the underlying complexities, making it easier to work with.
- **Avoids Extremes**: It avoids the pitfalls of both over-abstraction and under-abstraction by ensuring that the abstraction remains practical and functional.

**Example Scenario:**

Consider a gallery component designed to display images with captions. A balanced abstraction for this component would:

- Allow specifying the images to be displayed.
- Include captions for each image.
- Enable setting the dimensions of the images.

**Benefits of Balanced Abstraction:**

- **Clarity**: It simplifies the usage of the component without unnecessary complexity.
- **Functionality**: It provides all necessary features without exposing implementation details.
- **Usability**: It is user-friendly and effectively meets the needs of the users.

**Key Takeaway:**

- Achieving a balanced abstraction requires a deep understanding of the problem domain and the needs of the users. It involves creating an interface that is both straightforward and sufficiently powerful, without over-complicating or oversimplifying the solution.

</details>

<details>
  <summary>Functional Programming Principles</summary>
  
  ### Overview

Functional Programming (FP) emphasizes the use of pure functions and immutable data. Unlike Object-Oriented Programming (OOP), which relies on objects and mutable state, FP focuses on creating functions that do not alter state and always produce consistent results for given inputs.

### Key Concepts

- **Pure Functions**: Functions that, given the same inputs, always return the same output and do not cause side effects. They are predictable and simplify testing.

- **Immutability**: Data that cannot be changed once created. Instead of modifying existing data, FP creates new data structures, preserving the original state.

### Comparison with OOP

- **OOP**: Utilizes classes and methods that maintain internal state, making use of object-oriented constructs to manage data and behavior.

- **FP**: Utilizes functions that return new instances with updated state without altering existing data. This approach reduces side effects and enhances predictability.

### Benefits of Functional Programming

- **Predictability**: Pure functions provide consistent results for the same inputs, improving reliability.

- **Maintainability**: Immutability and pure functions reduce side effects, making code easier to understand and debug.

- **Testability**: Pure functions are easier to test due to their deterministic nature.

</details>

<details>
  <summary>Functional Purity</summary>
  
  ### Definition

**Functional purity** refers to functions that exhibit two key characteristics:
- **Dependence solely on input values**: The output of a pure function is determined only by its input parameters.
- **Absence of side effects**: A pure function does not alter any external state or cause changes outside its scope.

### Benefits

1. **Predictability**: Pure functions are easier to understand and reason about because they do not affect or rely on external states. This eliminates complexities associated with unintended changes in other parts of the system.
   
2. **Testability**: Since pure functions consistently produce the same result for the same set of inputs, they are straightforward to test. This consistency simplifies the process of verifying correctness.

### Key Concepts

- **Idempotence**: A function is idempotent if it yields the same result when applied multiple times with the same inputs. While idempotence is highly desirable, it does not always guarantee functional purity, as some idempotent functions may still have side effects.

### Comparison with Object-Oriented Programming (OOP)

In Object-Oriented Programming, methods often modify the internal state of objects and may produce different results even with the same inputs. This contrasts with functional purity, where functions should remain unaffected by and not alter the external environment.

### Practical Application

A pure function always returns the same output for the same inputs and doesn't cause side effects (like modifying global state).

### Example of Impure Function:

```javascript
  let count = 0;

function incrementCounter() {
  count += 1; // Side effect: modifying external state
  return count;
}

incrementCounter(); // count is now 1

```

### Pure Function (No Side Effects)::

```javascript
  function incrementCounter(count) {
  return count + 1; // No external state is modified
}

let count = 0;
const newCount = incrementCounter(count); // newCount is 1, count remains 0


```

### Using `.map()` for Functional Purity:

Functional methods like `.map()` create new arrays without mutating the original one, adhering to the principles of functional purity.

```javascript
  const numbers = [1, 2, 3];

const doubledNumbers = numbers.map(number => number * 2);

console.log(numbers); // [1, 2, 3] (original array unchanged)
console.log(doubledNumbers); // [2, 4, 6] (new array created)
```


</details>

<details>
  <summary>Immutability</summary>
  
 ### Definition

**Immutability** is the principle that once data is created, it cannot be changed. Instead of modifying existing data, new data structures should be created to reflect any changes.

### Key Concepts

- **Unchanging Data**: Data that remains constant after its creation ensures that its state is consistent and predictable. Changes should be made by creating new data rather than altering the original.

- **Enforcement**: In some programming languages, immutability can be enforced by language features that prevent modification of variables or objects. 

- **Reliability**: Immutable data structures prevent unexpected changes from other parts of the program, which is especially important in asynchronous or concurrent environments.

### Practical Application

In functional programming, data should not be mutated but rather copied and modified. Let's look at a case where mutating an object causes issues.

### Problematic Code (Mutating Data):

```javascript
  const user = { name: 'Alice', age: 25 };

function updateAge(user, newAge) {
  user.age = newAge; // Mutating the original object
  return user;
}

const updatedUser = updateAge(user, 26);
console.log(user.age); // 26 (original data is mutated)

```


### Improved Code:

```javascript
  const user = { name: 'Alice', age: 25 };

function updateAge(user, newAge) {
  return { ...user, age: newAge }; // Returning a new object without modifying the original
}

const updatedUser = updateAge(user, 26);
console.log(user.age); // 25 (original data is preserved)
console.log(updatedUser.age); // 26 (new data is created)

```

Immutability ensures that functions don’t accidentally alter shared state, making code more predictable.



### Benefits

1. **Predictability**: Immutable data structures are easier to reason about since their state does not change unexpectedly.
   
2. **Safety**: Helps prevent bugs related to unintended data changes and ensures consistency.

3. **Ease of Debugging**: Simplifies debugging by making it easier to trace data changes and understand state.

Immutability is a core concept in functional programming and can greatly improve code maintainability and reliability when used appropriately.

</details>



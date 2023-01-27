### Local Classes
- Local classes are classes that are defined in a block.
- You typically find local classes defined in the body of a method.

**Declaring Local Classes**
You can define a local class inside any block. For example, you can define a local class in a `method body`, a `for loop`, or an `if clause`.

**Accessing Members of an Enclosing Class**
A local class has access to the members of its enclosing class.
In addition, a local class has access to local variables. However, a local class can only access local variables that are declared final.


### Anonymous Classes

They enable us to declare and instantiate a class at the same time.Use them if you need to use a local class only once.
An anonymous class is an expression, which means that you define the class in another expression.

**Syntax of Anonymous Classes**
```
    HelloWorld frenchGreeting = new HelloWorld() {
        String name = "tout le monde";
        public void greet() {
            greetSomeone("tout le monde");
        }
        public void greetSomeone(String someone) {
            name = someone;
            System.out.println("Salut " + name);
        }
    };
```
The anonymous class expression consists of the following:
- The new operator
- The name of an interface to implement or a class to extend. In this example, the anonymous class is implementing the interface HelloWorld.
- Parentheses that contain the arguments to a constructor, just like a normal class instance creation expression. 
  Note: When you implement an interface, there is no constructor, so you use an empty pair of parentheses, as in this example. 
- A body, which is a class declaration body. More specifically, in the body, method declarations are allowed but statements are not.
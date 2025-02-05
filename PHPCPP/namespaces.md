# Namespaces

Although namespaces have a very rich implementation in PHP scripts, with special keyswords like 'use' and 'namespace' and special constants like `'__NAMESPACE__'`, they are internally very simple.

A namespace is nothing else than a class or function prefix. If you want your classes or functions to appear in a specific namespace, you simply have to add a prefix to the class or function name. The following code creates a function "myFunction" in the "myNamespace" namespace.

```cpp
    #include <phpcpp.h>

    // function that we're going to export
    void myFunction()
    {
    }

    extern "C" {
        PHPCPP_EXPORT void *get_module() {

            // create extension object
            static Php::Extension extension("my_extension", "1.0");

            // add the myFunction function to the extension, 
            // and put it in namespace "myNamespace"
            extension.add("myNamespace\\myFunction", myFunction);

            // return the extension
            return extension;
        }
    }
```

If you like, you can use the `Php::Namespace` utility class for this. This is a class with exactly the same signature as the `Php::Extension` class, and that you can use to register classes and functions too.

```cpp
    #include <phpcpp.h>

    // function that we're going to export
    void myFunction()
    {
    }

    extern "C" {
        PHPCPP_EXPORT void *get_module() {

            // create extension object
            static Php::Extension extension("my_extension", "1.0");

            // create a namespace
            Php::Namespace myNamespace("myNamespace");

            // add the myFunction function to the namespace
            myNamespace.add<myFunction>("myFunction");

            // @todo add more functions and classes to the namespace

            // create a nested namespace
            Php::Namespace nestedNamespace("nestedNamespace");

            // @todo add functions and classes to the nested namespace

            // add the nested namespace to the first namespace
            myNamespace.add(std::move(nestedNamespace));

            // add the namespace to the extension
            extension.add(std::move(myNamespace));

            // return the extension
            return extension;
        }
    }
```

The `Php::Namespace` class is just a container that automatically adds a prefix to all classes and functions that you add to it. Nesting namespaces is possible too, as you saw in the example.

In the example we used the `std::move()` function to move the nested namespace into the parent namespace, and to move the first namespace into the extension. Moving is more efficient that adding, although a regular extension.add(myNamespace) would have been valid too.

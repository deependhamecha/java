# Source File Declaration Rules <a name="sourcefile"></a>

- There can be only one public class per source code file.

- If there is a public class in a file, the name of the file must match the name of the public class. For example, a class declared as public class Dog {} must be in a source code file named Dog.java.

- If the class is part of a package, the package statement must be the first line in the source code file, before any iport statements that may be present.

- There's no way to declare multiple classes in a file and have them in different packages, or use different imports.

- A file can have more than one nonpublic class.

- Files with no public classes can have a name that does not match any of the classes in the file.
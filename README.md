# Serialization

## Introduction

For games, you will often find that in the settings page you can specify whether or not you want to hear thumping tunes while playing, as well as the ability to adjust the volume.

To preserve the program settings set by the user, we save these settings in a file, and this is what you will learn in this lesson.

## The shape of the object in memory

While the program is running, every object created in it is represented in memory as a large string of bytes that the operating system understands.
This string contains all of the object's information, such as:

- class derived from it.
- Each class inherits from it.
- Every interface applies it.
- Each function owns it.
- Each variable has it, along with its type, current value, etc.

When all commands in the program are executed or when you exit it, all data related to that program is erased from memory because it is no longer needed. That is, all objects, variables, and functions are erased, and thus any existing communication between the program and external objects such as a file, network, database, etc. stops.

## The concept of serialization and deserialization

- **Serialization** means saving the object's current state into a file.

  - When we say: "Save the state of the object", we mean to create an exact copy of the object in memory and place it in an external file.

- **Deserialization** means to return the state of an object contained in a file.

## Serialization steps in Java

To create an object from a specific class and save its state, follow these steps:

- The object whose state you want to save must be a derivative of a class that implements the **Serializable** interface.
- Create a .ser file using the **FileOutputStream** class.
- Prepare an object of the **ObjectOutputStream** class, which is used to write the object's state to a file.
- Copy the state of the in-memory object into this file by the **writeObject()** function.
- When the copying process is finished, we cut off all contact we had with this file.

## Keyword : transient

If you don't want everything related to the object to be copied to memory, you have to put the word **transient** in the definition of everything you don't want to be copied to the file, and then it will be ignored.

## Deserialization steps in Java

To retrieve the state of the object that was saved in a specific file, you must follow these steps:

- Create an empty object of the same type as the object whose state we want to retrieve from the file.
- Prepare an object of the **FileInputStream** class that is used to insert the data of a given file into memory.
- Prepare an object of the **ObjectInputStream** class to recreate the object in memory.
- Reading the state of the object by the **readObject()** function and storing it in the empty object that we created in the first step, here you will have to do Downcasting to convert the object type returned by the readObject() function to the real object type because it returns the object in memory as Object and not as his true kind.
- When the copying process is finished, we cut off all contact we had with this file.

## Example

Editor Class :

```
import java.io.Serializable;

public class Editor implements Serializable {

    public String language;
    public String encoding;
    public String fontSize;
    public String fontFamily;
    public boolean autoSave;
    public boolean autoComplete;
    public transient String direction;

}
```

Main class :

```
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.IOException;

public class Main {

    public static void main(String[] args) {

        Editor e = new Editor();


        if ( new File("./user-prefrences.ser").exists() )
        {
            try
            {
                FileInputStream fis = new FileInputStream("./user-prefrences.ser");

                ObjectInputStream ois = new ObjectInputStream(fis);

                e = (Editor) ois.readObject();

                fis.close();
                ois.close();

                System.out.println("Deserialized data has been created in the memory");
                System.out.println("Language:      " + e.language);
                System.out.println("Encoding:      " + e.encoding);
                System.out.println("Font size:     " + e.fontSize);
                System.out.println("Font family:   " + e.fontFamily);
                System.out.println("Auto save:     " + e.autoSave);
                System.out.println("Direction:     " + e.direction);
                System.out.println("Auto Complete: " + e.autoComplete);
                System.out.println();
            }
            catch(IOException | ClassNotFoundException ex)
            {
                System.out.println(ex.getMessage());
            }
        }




        try
        {
            e.language   = "arabic";
            e.encoding   = "utf-8";
            e.fontSize   = "12pt";
            e.fontFamily = "tahoma";
            e.autoSave   = true;
            e.direction  = "right to left";

            FileOutputStream fos = new FileOutputStream("./user-prefrences.ser");

            ObjectOutputStream oos = new ObjectOutputStream(fos);

            oos.writeObject(e);

            oos.close();
            fos.flush();
            fos.close();

            System.out.println("Serialized data has been saved in the project in a file called user-prefrences.ser");
        }
        catch(IOException ex)
        {
            System.out.println(ex.getMessage());
        }

    }

}
```

## Concept of serialVersionUID constant in java

Each class that implements a Serializable interface is given its own version number.
This number is stored in the serialVersionUID variable.

So each class that implements a Serializable interface has a variable named serialVersionUID even if it is not defined.

The version number ensures that the sender and receiver of the file on the network have the same class version of the object stored in the file.
If the version number of the sending class is different from the version number of the receiving class, an InvalidClassException is thrown.

Example : You are forced to define it as **static final long**

```
private static final long serialVersionUID = 1L;
```

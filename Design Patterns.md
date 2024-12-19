# Design Patterns

## Singleton

- Let you access your object from anywhere in your application
- Guarantee that only one instance of this class will be available at any point in time

But why?

- For some applications and contexts it is advantageous to ensure that a class only have a single instance at a time.

### Example

**Note:** Below is a siple example, there is some extension that can be added to the Singleton class to ensure that two threads are not able to access the instance at the same time.

```java
public class Singleton {
	
	private static Singleton inst;
	private String data;

	private Singleton(String data) {
		this.data = data;
	}

	public static Singleton getInstance(String data) {
		Singleton result = inst;

		/* Ensures only one instance is created */
		if (result == null) {
			inst = result = new Singleton(data);
		}

		return result;
	}

}
```

### Overview

- The singleton pattern must only be used when a class must only have a single instance available.
- disables all means of creating objects of a class except for the special static creation method.
- returns the existing instance if it has already been created

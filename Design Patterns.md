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

## Strategy

Suppose we have this *PaymentService* class where the amount we need to pay is provided to us. And all we need to do is implement the *processOrder()* method.

```java
public class PaymentService {
	private int cost;
	private boolean includeDelivery;

	public void processOrder() {
		/* Pop-up to collect card details */
		CreditCard card = new CreditCard("cardNumber", "expiryDate", "cvv");

		/* Validate Card */
		System.out.println("Paying " + getTotal() + " using Credit Card");
		card.setAmount(card.getAmount - getTotal());

	}

	private int getTotal() {
		return includeDelivery ? cost + 10 : cost;
	}
}
```
### **<span style="color: red">But what if we tried to add more payment options?</span>**

For example, lets say that we wanted to add ApplePay along side using a credit card as payment. We would then have to use a switch or if statment to check the payment method and switch between options. This is not the most efficient way of handling this in the long run.

> ***<span style="color: blue">Def:</span>* Open/Closed Principle**  
> code th the Open/Closed Principle states that an encapsulating entity (class, function, etc..) is open for extension and closed for modifiction. We can see that in the above class if we wanted to add additional payment methods this would violate the Open/Closed Principle because every time we need to extend the *PaymentService* class's functionality we have to modidy the code heavily violating the principle.

Additionally, we can also note that the above class violates the *Single Responsibility Principle*, which basically means that the class is taking on to many responsibilites that are above it's requirements.

To Handle these issues we need a sort of *Strategy* if you will. Moreover, we need to place each payment method in it's own class.

### Review

- Behavioral design pattern
- defines a family of algorithms, puts each of them in a seperate class, and makes their objects interchangeable. 

### Fixes

```java
public interface PaymentStrategy {
	void collectPaymentDetails();
	boolean validatePaymentDetails();
	void pay(int amount):
}
```

```java
public class PaymentByCreditCard implements PaymentStrategy {
	private CreditCard card;

	@Override
	public void collectPaymentDetails() {
		CreditCard card = new CreditCard("cardNumber", "expiryDate", "cvv");
	}

	@Override
	public boolean validatePaymentDetails() {
		/* Validate */
		return false;
	}

	@Override
	public void pay(int amount) {
		System.out.println("Paying " + getTotal() + " using Credit Card");
		card.setAmount(card.getAmount - getTotal());
	}
}
```

```java
public class PaymentByApplePay  {
	private String email;
	private String passwd;

	@Override
	public void collectPaymentDetails() {
		email = "...";
		passwd = "...";
	}

	@Override
	public boolean validatePaymentDetails() {
		/* Validate */
		return false;
	}

	public void pay(int amount) {
		System.out.println("Paying " + amount + " using ApplyPay");
	}
}
```

Now we can fix our *PaymentService* class:

```java
public class PaymentService {
	private int cost;
	private boolean includeDelivery;

	private PaymentStrategy strategy;

	public void processOrder() {
		strategy.collectPaymentDetails();
		if (strategy.validatePaymentDetails()) {
			strategy.pay(getTotal());
		}
	}

	private int getTotal() {
		return includeDelivery ? cost + 10 : cost;
	}
}
```

**Note:** This class has no visibility on how the payment is being conducted as it is making use of the strategy interface.

Here is what this would look like for a client:
```java
public static void main(String[] args) {
	PaymentService payment = new PaymentService();
	payment.setStrategy(new PaymentByCreditCard());
	payment.processOrder();
}
```

